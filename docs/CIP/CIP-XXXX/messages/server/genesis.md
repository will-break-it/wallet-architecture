# Genesis Server Message Partial

This top level message key is added by the server as part of any synchronization process that starts from the genesis [`point`](../index.md#event-sequencing-and-synchronization), also sometimes referred to `origin`.

Depending on the blockchain that a client subscribes to, this message partial may not exist.

## Schema

```json
{
  "genesis": {
    /* chain specific genesis data */
  }
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
    "activeSlotsCoefficient": {
      "type": "number",
      "format": "float"
    },
    "updateQuorum": {
      "type": "integer"
    },
    "maxLovelaceSupply": {
      "type": "string"
    },
    "networkMagic": {
      "type": "integer"
    },
    "epochLength": {
      "type": "integer"
    },
    "systemStart": {
      "type": "integer"
    },
    "slotsPerKesPeriod": {
      "type": "integer"
    },
    "slotLength": {
      "type": "integer"
    },
    "maxKesEvolutions": {
      "type": "integer"
    },
    "securityParam": {
      "type": "integer"
    }
  },
  "required": [
    "activeSlotsCoefficient",
    "updateQuorum",
    "maxLovelaceSupply",
    "networkMagic",
    "epochLength",
    "systemStart",
    "slotsPerKesPeriod",
    "slotLength",
    "maxKesEvolutions",
    "securityParam"
  ]
}
```

An example type can be found in the [cardano-js-sdk](https://github.com/input-output-hk/cardano-js-sdk/blob/master/packages/core/src/Cardano/types/Genesis.ts#L6).
