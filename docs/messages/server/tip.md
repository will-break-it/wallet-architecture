# Server Tip Message Partial

This message partial is added to all server-sent messages aside from error messages to simplify:

- tracking synchronization progress on the client-side
- defining transaction validity time intervals

## Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "tip": {
      "type": "object",
      "properties": {
        "point": {
          "type": "object",
          "properties": {
            "slot": {
              "type": "integer"
            },
            "height": {
              "type": "integer"
            },
            "hash": {
              "type": "string"
            }
          },
          "required": ["hash"],
        }
      },
      "required": ["point"],
    }
  },
  "required": ["tip"],
}
```

### Message Example

#### Cardano

```json
{
  "tip": {
    "point": {
      "slot": 127838345,
      "hash": "9f06ab6ecce25041b3a55db6c1abe225a65d46f7ff9237a8287a78925b86d10e"
    }
  }
  /// ...
}
```

### Bitcoin

```json
{
  "tip": {
    "point": {
      "height": 849561,
      "hash": "00000000000000000000e51f4863683fdcf1ab41eb1eb0d0ab53ee1e69df11bb"
    }
  }
  ///...
}
```
