# Protocol Parameter Server Message Partial

This top level message key is added by the server as part of any synchronization process for every epoch boundary transition. 
Protocol parameters sent as a raw cbor hex.

> [!NOTE]
> It is specific to the Cardano blockchain.

## Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "epoch": {
      "type": "integer"
    },
    "parameters": {
      "type": "string"
    }
  },
  "required": [
    "epoch",
    "parameters"
  ]
}
```

## Example

```json
{
  "protocolParameters": {
    "epoch": 225,
    "parameters": "cbor hex"
  }
}
```
