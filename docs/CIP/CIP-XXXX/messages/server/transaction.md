# Server Transaction Message Partial

The server broadcasts new, relevant transaction data to connected clients using chain-specific encoding protocols. This approach closely mimics the native blockchain synchronization process, ensuring compatibility and efficiency. By adhering to each blockchain's native encoding, the system maintains consistency with existing communication protocols.

## Transaction Processing and Enrichment/ Hydration

As new transactions are processed, filtered, and deemed relevant for a particular connected client, the server publishes a new message containing the transaction(s). In many blockchain systems, on-chain transactions often contain references to other transactions or metadata hashes, necessitating additional context.

## Hydration Process

The term "hydrated" refers to the process of enriching a message with additional data. This enrichment simplifies the client's processing and reduces the need for further requests to derive its wallet state. The hydration process can involve:

- Resolving transaction inputs (transaction output references)
- Adding asset metadata from off-chain data sources
- Incorporating other off-chain metadata, such as:
- Certificate information
- Stake pool metadata
- Smart contract data
- ...

The hydration process is achieved by adding top level keys to the message that follow a specific structure correlating to transactions that were passed as part of the message.

### Resolved transaction inputs

A transaction input of a UTxO blockchain also referred to as transaction output reference usually follows a structure like this:

```json
{
  "outputIndex": "number",
  "txHash": "string"
}
```

This hydrated structure would add a `resolvedInputs` key to the overall transaction message and nest the resolved inputs corresponding to their
input index of the respective transaction:

```json
{
  "transactions": [
    /* transaction cbor hex 1 */,
    /* transaction cbor hex 2 */,
    /* ... */
  ],
  "resolvedInputs": [
    [
      /* transaction 1 resolved input 1 cbor hex */,
      /* transaction 1 resolved input 2 cbor hex */
    ],
    [
      /* transaction 2 resolved input 1 cbor hex */
    ]
  ],
  "point": {
    /* chain height/ slot, block hash */
  },
  "chain": {
    /* blockchain name, network */
  },
  "tip": {
    /* current chain tip */
  }
  // ...
}
```

## Cardano

In the Cardano ecosystem, the native encoding is CBOR which follows the [CIP-0116](https://github.com/klntsky/CIPs/blob/klntsky/json-spec-cip/CIP-0116/README.md) standard. Therefore, transactions are passed as hexadecimal blob representing cbor.
