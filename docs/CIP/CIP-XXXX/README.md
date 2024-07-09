---
CIP: 1
Title: Event-driven, wallet-optimized API
Status: Proposed
Category: Wallets
Authors:
  - William Wolff <william.wolff@iohk.io>
  - Hızır Sefa İrken <sefa.irken@iohk.io>
  - Rhys Bartels-Waller <rhys.bartelswaller@iohk.io>
  - Martynas Kazlauskas <martynas.kaslauskas@iohk.io>
  - Daniele Ricci <daniele.ricci@iohk.io>
Implementors: N/A
Solution-To: CPS-????
Discussions:
Created: 2024-07-10
License: CC-BY-4.0
---

## Abstract

We propose a multi-chain, event-driven, push-based API optimized for wallet applications, leveraging the principle that a wallet's state should be a pure function of the blockchain state. This approach eliminates polling strategies and aggregating views, enabling real-time synchronization with the blockchain as new blocks are appended. Our solution addresses key challenges in current wallet implementations, including rollback handling, address discovery and session management for serving stateful clients.

## Motivation: why is this CIP necessary?

The CIP provides a partial solution to the problems described in [CPS-????](../CPS/CPS-XXXX/README.md). In particular, we strive to define a standardized approach serving data to edge clients in an unopinionated, close to blockchain native format using a push-based protocol.

## Specification

### Introduction

#### What does a wallet need to construct transactions?

- past transactions (receiving & spending) to derive balance, UTxO set, delegation and governance state
- reward account balance (staking, voting)
- network, era or epoch specific data, like:
  - protocol parameters (tx fees, plutus cost models, ...)
  - genesis (security param, slot length)
- the current tip/ block height for validity intervals of transactions as well as sync progress

#### How do we serve this data?

Through a push-based API, wallets are provided with a continuous stream of **relevant** on-chain events, enabling them to derive their current wallet state efficiently. This API supports various blockchain models, including UTXO-based chains and account-based chains. Additionally, clients receive secondary data relevant to the blockchain network they are subscribed to, ensuring comprehensive and up-to-date information.

#### Protocol

We use a bidirectional protocol and encode all messages as JSON objects, while transactions contained in messages are preserved in the chain's native encoding standard (cbor). Messages adhere to a specific structure, ensuring consistency and facilitating efficient communication within our event-driven protocol. As we strive to support multiple blockchains, a server-sent message will always reference the originating chain it relates to.

### Message Types & Structure

We distinguish between client-sent and server-sent messages.

#### Client

Client-sent messages are **typed** messages, such as:

```json
{
  "type": "<unique message type>"
  // ...
}
```

This allows the server to quickly identify the client's intent. More details on what typed messages exist will be covered in [Client Typed Messages](#client-typed-messages) section.

#### Server

Instead of typed messages, server-sent messages vary in structure depending on the underlying blockchain. Our design aims to be chain-agnostic and serve data from different blockchains in future. Therefore, we rely on **untyped** messages that are enriched and aggregated into compound messages. We call each top level key a server message partial.

This leads to a structure that has many top level keys of which, not every key may be present for every message, for example:

```json
{
  "transactions": [
    /* ... */
  ],
  "resolvedInputs": [
    /* ... */
  ],
  "rewards": {
    /* ... */
  },
  "tip": {
    /* ... */
  }
  // ...
}
```

> [!IMPORTANT]
>
> New message partials can be added at any time, which allows for flexible extension of the API.
> Clients are expected to check for message key presence and ignore those they do not support.

#### Errors

Any cases of server-side failure trigger an error message that is append as separate `error` message partial:

```json
{
  "error": {
    "type": "error type",
    "message": "error message"
  }
  /* ... */
}
```

### Authentication

A newly connected client authenticates as part of the [`subscribe`](./messages/client/subscribe.md) message that is sent to the server to synchronize a specific wallet account up to the chain's current tip. This message must include a **signature** that verifies the ownership of provided extended public key (xpub key).

Extended public keys (xpub keys) serve a dual purpose in our design: **authentication** and **efficient transaction querying**. Here's how it works:

#### Account Authentication

Clients connect and authenticate via subscribe messages by providing:

- a digital signature
- a public key whose Blake2b-256 hash corresponds to one of their provided credentials: payment and stake key hash(es) or script hash (es) in case of shared wallets

This ensures that only authorized clients can access their account data.

The `signature` field is generated by signing with the private payment/ stake key a SHA256 HMAC hashed string. The prehash string is constructed by concatenating the subscribed blockchain + timestamp.

The server verifies the signature, timestamp and whether the Blake2b-256 hash of public key matches one of the credentials before serving any data to the client. If any part of the `subscribe` message is invalid, the server sends an error and closes the connection.

#### Transaction Querying

The credentials also enable efficient querying of client-relevant data:

1. **Indexing Strategy**

   We index transactions by both payment and stake credential. This dual-indexing approach allows for comprehensive transaction tracking.

2. **BIP32 Wallet Query Process**

   The server queries our index using the corresponding key hashes per client to serve only relevant transactions to scubribed clients.

### Ordering of Events

Our API needs a reliable measurement of time to construct a stream of ordered, client-relevant, on-chain events that connected clients can easily consume. Time is crucial for clients for various reasons, including:

- monitoring the progress of synchronization up to the chain's tip
- defining transaction validity intervals

>
> - supports operations that need to occur within specific timeframes
> - helps manage network congestion by allowing expired transactions to be discarded
> - protects against replay attacks by ensuring transactions can't be resubmitted after expiration

We borrow the term `point` from the Cardano blockchain to introduce a server-sent message partial. For instance, if a new transaction is deemed relevant for a connected client, the server enriches the message by adding the current "`point` in on-chain time" of the respective chain. For Cardano, this typically looks as follows:

#### Cardano Point

```json
{
  "point": {
    "slot": 127838345,
    "hash": "9f06ab6ecce25041b3a55db6c1abe225a65d46f7ff9237a8287a78925b86d10e"
  }
  /* ... */
}
```

> [!NOTE]
> Clients may safely assume that any server-sent message, aside from error messages contain `point` partial.

### Synchronization

We refer to synchronization as the process that a wallet undertakes to catch up to the current chain's tip in order to derive its latest state and proceed to transact. The aforedescribed `point` represents the main anchor for wallets, to be able to:

- resume the sync process since their last known point
- keep track of their current position in the blockchain
- track and handle chain reorganizations (rollbacks)

The following sequence diagram shows, how a client connects providing an array of `point`s and extended public key. The client provides more than those two bits which is described in the [`subscribe`](./messages/client/subscribe.md) message.
Subsequently, the server tries to find an intersection given the list of `point`s.

For an identified intersection, the server computes and publishes an ordered stream of events.
This event stream provides a chronological record of on-chain events and affectively state changes for the wallet to derive its latest state.

> [!NOTE]
> The server sources client-relevant data through credential-dependent, point-specific database queries, though the implementation details are beyond this CIP's scope.

![Sequence Diagram Synchronization](./images/02-Synchronization-Sequence.svg)

#### Resumability

One of the traditional operational challenges of serving stateful clients is session management during times of scaling. In our protocol, the server can disconnect clients at any time while clients can resume synchronization from their last known `point`s after reconnecting. To minimize reconnection attempts, clients should provide a list of `point`s based on their most recent state, including some that may have been affected by rollbacks. This approach reduces the likelihood of the server failing to find a valid intersection, which would otherwise result in dropped connections and additional round trips.

> [!NOTE]
> More details are described in the [`subscribe`](./messages/client/subscribe.md) message.

#### Rollback Handling

In a [CAP](https://en.wikipedia.org/wiki/CAP_theorem) system like Cardano, which balances global consistency with availability, there is a challenge in dealing with rollback events that impact transaction finality. Regardless of whether one uses their own full node or a server provider for transaction submission - if such events are not properly handled with the help of monitoring the rolling window of [k blocks](https://plutus-apps.readthedocs.io/en/latest/plutus/explanations/rollback.html) and respective resubmission, there is no guarantee that the transaction is finalized, eg. becomes part of the immutable pefix of the Cardano blockchain.

Since our wallet-optimized protocol defines that server-sent messages include the `point` message partial, the client can check if its last `point` is greater than any subsequent message `point` from the server, without the need to introduce a `rollback` specific message partial. This assumes that the client keeps a local copy of the volatile segment of the blockchain defined by the security parameter.

#### Connection Management

Clients are expected to reconnect on error, when providing invalid `point`s for which a server cannot find an intersection.
Clients may send multiple [`subscribe`](./messages/client/subscribe.md) messages for different accounts, but subsequently take responsibility to coordinate server-sent events by time to their possibly differently synchronized accounts. When receiving a message that references a `point` that the first account has already seen, the client only applies it for its second account for example.

### Versioning

We propose to version message partials individually. In our protocol, the client sends its list of supported versions as part of the initial [`subscribe`](./messages/client/subscribe.md) message. Upon receiving this information, the server selects the highest mutually supported version and associates it with the client.
This method requires the server to implement version-based message construction, allowing it to tailor its responses to each client's supported version.

### Encoding

Our proposed communication protocol **adopts blockchain-native encoding standards** to ensure compatibility and efficiency. This approach leverages existing conventions familiar to clients while minimizing conversion overhead on the server-side. (see [CPS - Fragile API](../../CPS/CPS-XXXX/README.md#fragile-api))

#### Address Encoding

Address encoding varies depending on the blockchain network. In Cardano the protocol servers bech32 encoded addresses.

### Extensions

Clients can turn on certain `extensions` by setting a respective flags in their subscription [`config](./messages/client/subscribe.md#subscribe-example) which tells the server to for example:

- to enrich transaction output asset values by their respective off-chain metadata such as decimal offsets
- to resolve transaction inputs (transaction output references) as transaction outputs

## Rationale: how does this CIP achieve its goals?

A primary consideration for the protocol design was to minimize message round trips between client and server as well as the amount of work to be done by the server for each client. Thus, the API's authentication does not require an initial message exchange but rather defines a standard verifable method for the server to either drop a client early and proceed to publish its relevant data.

### Client Typed Messages

- [`heartbeat`](./messages/client/heartbeat.md): Connection management/ keep alive

> [!NOTE]
> Since the API supports extensions, transport-specific messages can be implemented such as this.

- [`subscribe`](./messages/client/subscribe.md): Define topics of interest & authentication

### Server Message Partials

- [`welcome`](./messages/server/welcome.md): Service details, sent once immediately upon establishing a connection
- [`genesis`](./messages/server/genesis.md): If applicable for the subscribed blockchain, it serves static data used as part of the network bootstrap
- [`transaction`](./messages/server/transaction.md): A new transaction
- [`tip`](./messages/server/tip.md): A new block was appended
- [`protocol parameters`](./messages/server/protocol-parameters.md): Epoch based message for updated protocol parameters
- [`era summary`](./messages/server/era-summary.md): Era based message for updated slot length

## Path to Active

### Acceptance Criteria

[!TODO]

### Implementation Plan

[!TODO]

## Copyright

This CIP is licensed under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/legalcode).