# Welcome Message Partial

This message is sent to all clients as the first message immediately upon establishing a connection.
It contains essential details about the server and API, helping clients understand the environment they are interacting
with.

The message can contain:

- A list of supported blockchains, networks and extensions
- Versioning information

## Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Schema for Welcome message partial",
  "type": "object",
  "properties": {
    "welcome": {
      "type": "object",
      "properties": {
        "blockchains": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "name": {
                "type": "string"
              },
              "network": {
                "type": "string"
              },
              "extensions": {
                "type": "array",
                "items": {
                  "type": "object",
                  "properties": {
                    "name": {
                      "type": "string"
                    },
                    "config": {}, // using 'false' will switch off the extension
                    "default": {},
                    "switchable": {
                      "type": "boolean"
                    },
                    "versions": {
                      "type": "array",
                      "items": {
                        "type": "string"
                      }
                    }
                  },
                  "required": [
                    "name",
                    "config",
                    "default",
                    "switchable",
                    "versions"
                  ]
                }
              },
              "version": {
                "type": "string"
              }
            },
            "required": [
              "name",
              "network",
              "extensions",
              "version"
            ]
          }
        }
      },
      "required": [
        "blockchains"
      ]
    }
  },
  "required": [
    "welcome"
  ]
}
```

### Message Example

This message will contain details for all the served blockchains and networks in the API.

```json
{
  "welcome": {
    "blockchains": [
      {
        "name": "cardano",
        "network": "mainnet",
        "extensions": [
          {
            "name": "assetMetadata",
            "config": false,
            "switchable": true,
            "versions": [
              "1.0",
              "1.1"
            ]
          },
          {
            "name": "CIP-XXXX",
            "config": {
              "timeout": 10
            },
            "switchable": false,
            "versions": [
              "1.0"
            ]
          }
        ],
        "uri": "wss://www.example.com/socketserver",
        "version": "0.1"
      }
    ]
  }
}
```
