# Genesis Server Message Partial

This top level message key is added by the server as part of any synchronization process that starts from the genesis [`point`](../index.md#event-sequencing-and-synchronization), also sometimes referred to `origin`.

Depending on the blockchain that a client subscribes to, this message partial may not exist.

## Schema

```json
{
  "genesis": { /* chain specific genesis data */ },
  /// ...
}
```

### Cardano

Cardano's genesis information looks as follows:

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "systemStart": {
      "type": "string",
      "format": "date-time",
      "description": "The system start date and time"
    },
    "networkMagic": {
      "type": "integer",
      "description": "The network magic number"
    },
    "networkId": {
      "type": "string",
      "enum": ["mainnet", "testnet"],
      "description": "The network identifier"
    },
    "activeSlotsCoefficient": {
      "type": "number",
      "minimum": 0,
      "maximum": 1,
      "description": "The active slots coefficient"
    },
    "securityParameter": {
      "type": "integer",
      "minimum": 0,
      "description": "The security parameter (k)"
    },
    "epochLength": {
      "type": "integer",
      "minimum": 1,
      "description": "The number of slots in an epoch"
    },
    "slotsPerKesPeriod": {
      "type": "integer",
      "minimum": 1,
      "description": "The number of slots in a KES period"
    },
    "maxKesEvolutions": {
      "type": "integer",
      "minimum": 0,
      "description": "The maximum number of KES evolutions"
    },
    "slotLength": {
      "type": "integer",
      "minimum": 1,
      "description": "The length of a slot in seconds"
    },
    "updateQuorum": {
      "type": "integer",
    },
    "maxLovelaceSupply": {
      "type": "integer",
      "minimum": 0,
      "description": "The maximum Lovelace supply"
    }
  },
  "required": [
    "systemStart",
    "networkMagic",
    "networkId",
    "activeSlotsCoefficient",
    "securityParameter",
    "epochLength",
    "slotsPerKesPeriod",
    "maxKesEvolutions",
    "slotLength",
    "updateQuorum",
    "maxLovelaceSupply"
  ],
  "additionalProperties": false
}
```

An example type can be found in the [cardano-js-sdk](https://github.com/input-output-hk/cardano-js-sdk/blob/master/packages/core/src/Cardano/types/Genesis.ts#L6).
