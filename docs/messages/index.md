# Message Types

This index categorizes supported message types in our event-driven communication protocol, distinguishing between client-sent and server-sent messages. It serves as the foundation for understanding the protocol's structure and functionality.

> [!NOTE]
> We use the <https://json-schema.org/draft-07/schema#> schema to define messages.

## Client Messages

- [`heartbeat`](./client/heartbeat.md): Connection management/ keep alive
- [`subscribe`](./client/subscribe.md): Define topics of interest & authentication
- [`unsubscribe`](./client/unsubscribe.md): Graceful disconnect from server

## Server Messages

Server-sent messages are sent either **periodically** or **by trigger** due to newly available, relevant data for a specific client.

### Message Partials

Different blockchains have different **periodic** events that are not related to specific transactions, but are required for either transaction construction or necessary to derive the correct wallet state. These events get represented by additional top level message keys (message partial) correlated to a chain [`point`](#event-sequencing-and-synchronization).

We differentiate between [Ledger](#ledger-events) and [Network](#network-events) events.

#### Ledger events

Ledger events are specific occurrences that directly affect the state of a blockchain's ledger. These events typically involve changes to account balances, transaction histories, and other financial records.

##### Examples

- new transactions
- smart contract interactions
- token transfers
- stake delegation changes

#### Network events

Network events are occurrences that impact the overall operation and configuration of the blockchain network but do not directly alter the ledger's state. These events often involve changes to the network's consensus mechanism, protocol parameters, or infrastructure.

##### Examples

- Difficulty Adjustments (Bitcoin): Changes to the mining difficulty to maintain a consistent block production rate.
- Halving Events (Bitcoin): Reductions in the block reward given to miners, which occur approximately every four years.
- Epoch Transitions (Cardano): Periodic changes in the network's epoch, which can involve updates to protocol parameters and staking rewards.
- Era/ Network Transitions (Cardano): Cardano has transitioned through different [eras](https://roadmap.cardano.org/en/) which add/ remove or change certain network properties
- Network Upgrades (Ethereum): Implementation of new protocol features or improvements, such as the transition to Proof-of-Stake or gas price adjustments.
- Slot Leader Schedule Updates (Solana): Changes to the schedule of validators responsible for producing blocks.

## Server Message Partials

- [`genesis`](./server/genesis.md): If applicable for the subscribed blockchain, it serves static data used as part of the network bootstrap
- [`transaction`](./server/transaction.md): A new transaction
- [`tip`](./server/tip.md): A new block was appended

### Cardano

- [`protocol parameters`](./server/cardano/protocol-parameters.md): Epoch based message for updated protocol parameters
- [`era summary`](./server/cardano/era-summary.md): Era based message for updated slot length

## Multichain Support

Each server-sent message incorporates a unique chain-specific identifier. This identifier serves to precisely indicate the blockchain and network to which the message pertains.

```json
"chain": {
  "blockchain": "cardano",
  "network": "mainnet",
  /* ... */
}
```

> [!NOTE]
> The fields of `chain` may slightly vary because some networks require additional metadata.
> For example, Ethereum-compatible chains, the `chain_id` field is particularly important as it corresponds to the [EIP-155](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-155.md) chain ID.<br />

A client may define multiple [`subscription`](./messages/client/subscribe.md) objects (topics of interest) to receive events from different blockchains.

## Event Sequencing and Synchronization

As described on a high-level before, each event server-sent message includes a `point` object. This object is crucial for tracking a client's progress in synchronizing with the current tip of a **specific** blockchain.
This type may vary depending on the blockchain, the following shows a few examples:

## Schema

```json
"point": {
  "type": "object",
  "oneOf": [
    {
      "properties": {
        "height": {
          "type": "integer"
        },
        "hash": {
          "type": "string"
        }
      },
      "required": ["height", "hash"],
      "additionalProperties": false
    },
    {
      "properties": {
        "slot": {
          "type": "integer"
        },
        "hash": {
          "type": "string"
        }
      },
      "required": ["slot", "hash"],
      "additionalProperties": false
    }
  ]
}
```

#### Cardano Point

```json
"point": {
  "slot": 127838345, // absolute slot number
  "hash": "9f06ab6ecce25041b3a55db6c1abe225a65d46f7ff9237a8287a78925b86d10e" // block hash
}
```

#### Bitcoin Point

```json
"point": {
  "height": 849561, // block height
  "hash": "00000000000000000000e51f4863683fdcf1ab41eb1eb0d0ab53ee1e69df11bb" // block hash
}
```

#### Ethereum Point

```json
"point": {
  "height": 20175853, // block height
  "hash": "0xc5ae7e8e0107fe45f7f31ddb2c0456ec92547ce288eb606ddda4aec738e3c8ec" // block hash
}
```

### `Point` Uses

The `point` object serves several important functions:

1. **Synchronization**:

   It allows clients to keep track of their current position in the blockchain, enabling them to request only new events since their last known point. More details can be found in [02-Synchronization](../02-Synchronization.md).

2. **Consistency**:

   In case of chain reorganizations, clients can use the blockchain-specific point to detect and handle any changes in the blockchain history by handling `rollback` events properly.

3. **Resumability**:

   If a client disconnects and reconnects, it can provide its last known point to resume event processing from where it left off for a particular blockchain.
