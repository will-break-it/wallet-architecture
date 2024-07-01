# Message Types

This index categorizes supported message types in our event-driven communication protocol, distinguishing between client-sent and server-sent messages. It serves as the foundation for understanding the protocol's structure and functionality.

> ℹ️ **Remark**<br />
> We use the https://json-schema.org/draft-07/schema# schema to define messages.

# Client Messages

- [`heartbeat`](./client/heartbeat.md): Connection management/ keep alive
- [`subscribe`](./client/subscribe.md): Define topics of interest & authentication
- [`unsubscribe`](./client/unsubscribe.md): Graceful disconnect from server

# Server Messages

Server-sent messages are sent either **periodically** or **by trigger** due to newly available, relevant data for a specific client.
Any server-sent message is enriched with any data that can be tied to the current chain's point in time. This may include:

  - protocol parameters have changed:<br />
  → message is enriched by adding a unique top level key referencing the the current epoch's protocol parameters
  - a new epoch started:<br />
  → message is enriched with wallet's staking rewards
  - a new era started:<br />
  → message is enriched with era summary
  - ...

## List of Server Message Partials

- [`transaction`](./server/transaction.md): A new transaction
- [`tip`](./server/tip.md): A new block was appended

## Multichain Support

Each server-sent message incorporates a unique chain-specific identifier. This identifier serves to precisely indicate the blockchain and network to which the message pertains.

```json
"chain": {
  "blockchain": "cardano",
  "network": "mainnet",
  /* ... */
}
```

> ℹ️ **Remark**<br />
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