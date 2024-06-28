# Server Tip Message

## Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "type": {
      "type": "string",
      "const": "tip"
    },
    "data": {
      "type": "object",
      "properties": {
        "point": {
          "type": "object",
          "oneOf": [
            {
              "type": "object",
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
              "type": "object",
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
      },
      "required": ["point"],
      "additionalProperties": false
    }
  },
  "required": ["type", "data"],
  "additionalProperties": false
}
```

### Message Example

#### Cardano

```json
{
  "type": "tip",
  "data": {
    "point": {
      "slot": 127838345,
      "hash": "9f06ab6ecce25041b3a55db6c1abe225a65d46f7ff9237a8287a78925b86d10e"
    }
  }
}
```

### Bitcoin

```json
{
  "type": "tip",
  "data": {
    "point": {
      "height": 849561,
      "hash": "00000000000000000000e51f4863683fdcf1ab41eb1eb0d0ab53ee1e69df11bb"
    }
  }
}
```
