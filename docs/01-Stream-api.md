# Push-based API Overview

The push-based API for digital wallets is designed to provide clients with a continuous stream of **relevant** on-chain events, enabling them to derive their current wallet state efficiently. This API supports various blockchain models, including UTXO-based chains and account-based chains. Additionally, clients receive secondary data relevant to the blockchain network they are subscribed to, ensuring comprehensive and up-to-date information.

The following serves as introduction to the wallet-optimized, event-driven API using **websockets**.
This document outlines the structure and common elements of messages that clients may process as well as the overall protocol.

## Protocol

We use a bidirectional protocol and encode all messages as JSON objects. Messages adhere to a specific structure, ensuring consistency and facilitating efficient communication within our event-driven protocol. As we strive to support multiple blockchains, a server-sent message will always reference the originating chain it relates to.

### Message Format

Instead of relying on typed messages that may vary highly depending on the underlying blockchain, our design aims to enrich and aggregate data in compound messages.

#### Aggregate Compound Message Format

Any server-sent message consists of multiple top level keys with client-relevant data. Below, one can see how a chain reference is part of
a generic server-sent message:

```json
{
  /* top level message fields like transaction, genesis, rewards, point etc. */
  "chain": {
    "blockchain": "cardano",
    "network": "mainnet"
    /* other blockchain specific fields */
  }
}
```

Furthermore, any server-sent message is accompanied by a specific timestamp indicating the exact point in time within the respective chain.

### Tracking Time on Blockchains

Blocks on blockchains are often compared to the heartbeat of the chain, representing their own measurement of time. However, the actual time it takes to produce a block on blockchains is non-deterministic. Many independent actors within the distributed system compete to build the next block via Proof-of-Work (PoW), (delegated) Proof of Stake (dPoS/PoS) or another consensus mechanism. Consequently, blocks are produced on a probabilistic basis but are eventually evenly distributed over time with the help of algorithms like Bitcoin's difficulty adjustment or Cardano's protocol parameters that define how frequently blocks can occur for a predefined time period, termed _epoch_.

Our API needs a reliable measurement of time to construct a stream of ordered, client-relevant, on-chain events that connected clients can easily consume. Time is crucial for clients for various reasons, including:

- monitoring the progress of synchronization up to the chain's tip
- defining transaction validity intervals

> [!NOTE]
> Transaction validity intervals allow clients to submit transactions with a specified lifetime, which serves several important purposes:
>
> - protects against replay attacks by ensuring transactions can't be resubmitted after expiration
> - helps manage network congestion by allowing expired transactions to be discarded
> - supports operations that need to occur within specific timeframes

Some blockchains, like Cardano, define a notion of time based on slots, which are fixed intervals of time (typically seconds). Slot numbers either represent elapsed seconds since the network's genesis block (absolute) or beginning of epoch (epoch). In contrast, other blockchains like Bitcoin don't use a fixed time interval and instead rely on an intrinsic clock derived from block production for transaction validity intervals.

### Our Protocol's Way of Tracking Time

We borrow the term `point` from the Cardano blockchain to create an abstract notion of time that is sent along side any sever-sent message. By default, whenever a new transaction is deemed relevant for a connected client, the server enriches the message by adding the current "`point` in on-chain time" of the respective chain. That may look different depending on the blockchain a message relates to:

#### Bitcoin Point

```json
{
  "point": {
    "height": 849561,
    "hash": "00000000000000000000e51f4863683fdcf1ab41eb1eb0d0ab53ee1e69df11bb"
  }
  /* ... */
}
```

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

#### Ethereum Point

```json
{
  "point": {
    "height": 20175853,
    "hash": "0xc5ae7e8e0107fe45f7f31ddb2c0456ec92547ce288eb606ddda4aec738e3c8ec"
  }
  /* ... */
}
```

> [!NOTE]
>
> New top level message keys can be added at any time.
> Clients are expected to iterate over messages and skip those they do not support.

More information for time and event sequencing can be found in the section [Event Sequencing and Synchronization](./messages/index.md#event-sequencing-and-synchronization).

### Error Handling

Any cases of failure trigger an error message that is appended to a list of `errors`:

```json
{
  "errors": [
    {
      "type": "error type",
      "message": "error message"
    }
  ]
  /* ... */
}
```

## Message Types

We distinguish between _client-sent_ and _server-sent_ messages. Client messages are typed messages. Each message defines a `type`, whereby server-sent messages follow the aggregated, compound message structure explained above. An index of client message types and server-sent message parts is detailed [here](./messages/index.md).

## Keep Alive/ Heartbeat Messages

To conserve server resources, we shift connection maintenance responsibility to clients. This allows the server to terminate any inactive connections not regularly refreshed by clients.

More information can be found [here](./messages/client/heartbeat.md).

## Authentication

API authentication in its first version is implicitly handled by the client specifying the topics of interest in their initial [`subscribe`](./messages/client/subscribe.md) message. This message must include a **signature** that verifies the ownership of the provided credentials, which are used to filter block transactions.

More information can be found [here](./messages/client/subscribe.md).

## Other Topics

[1. Message Type Index](./messages/index.md)

[2. Synchronization](./02-Synchronization.md)

[3. Encoding Standard](./03-Encoding.md)
