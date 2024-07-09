# Era Summary Server Message Partial

This top level message key is added by the server as part of any synchronization process for every era transition.
It has similarities with hardfork events on other blockchains but has been represented by its own message partial.

The primary reason a wallet needs to be aware of era transitions in Cardano is due to potential network changes. Specifically, since the beginning of Cardano's Shelley era, the Ouroboros consensus protocol was introduced, which defined a slot length—a specific duration of time during which a block can be produced by a leader (stake pool). Initially, this slot length was set to one second but may change in the future. Therefore, depending on the slot length, the conversion of a specific [`point`](../../index.md#event-sequencing-and-synchronization) in on-chain time may vary.

In order to show and submit transaction times correctly, wallets need to know each era's slot length.

> [!NOTE]
> More information can be found in the article [Time handling on Cardano](https://iohk.io/en/blog/posts/2022/12/07/time-handling-on-cardano-part-1-about-ouroboros-and-the-importance-of-determinism/?__cf_chl_tk=Kddmdg0tEnYePJV5tfNsI_zyrJpcZm67K5yVHb96OGI-1720453490-0.0.1.1-4734) and [Cardano Foundation Documentation](https://docs.cardano.org/about-cardano/evolution/eras-and-phases/).

## Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "parameters": {
      "type": "object",
      "properties": {
        "epochLength": {
          "type": "integer"
        },
        "slotLength": {
          "type": "integer",
          "description": "Milliseconds"
        }
      },
      "required": ["epochLength", "slotLength"]
    },
    "start": {
      "type": "object",
      "properties": {
        "slot": {
          "type": "integer"
        },
        "time": {
          "type": "string",
          "format": "date-time"
        }
      },
      "required": ["slot", "time"]
    }
  },
  "required": ["parameters", "start"]
}
```

## Example

```json
{
  "parameters": {
    "epochLength": 432000,
    "slotLength": 1,
    "safeZone": 129600
  },
  "start": {
    "epoch": 74,
    "slot": 1598400,
    "time": "2020-09-11T08:36:51.000Z"
  },
  "end": {
    "epoch": 102,
    "slot": 13694400,
    "time": "2020-12-30T05:04:51.000Z"
  }
}
```
