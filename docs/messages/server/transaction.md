# Server Transaction Message

The server sends this message to a client when it identifies a transaction as relevant. This enables the client to construct the transaction history, derive its balance, and, if applicable, determine its UTxO set.

> ℹ️ **Remark**<br />
> Transaction hashes, output addresses, and policy IDs are base64-encoded to optimize for space efficiency, as base64 encoding increases data size by approximately 33-37% compared to the original binary data. Base64 is more efficient in terms of encoding density.

## TODO: To be defined further

## Schema

```json
{
  "$schema": "https://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "type": {
      "type": "string",
      "enum": ["transaction"]
    },
    "point": {
      "type": "object",
      "properties": {
        "hash": {
          "type": "string",
          "enum": ["transaction"]
        },
        "slot": {
          "type": "integer"
        }
      },
      "required": ["hash", "slot"]
    },
    "chain": {
      "type": "object",
      "properties": {
        "blockchain": {
          "type": "string"
        },
        "network": {
          "type": "string"
        }
      },
      "required": ["blockchain", "network"]
    },
    "record": {
      "type": "object",
      "properties": {
        "auxiliary": {
          "type": "object"
        },
        "collateral": {
          "type": "object"
        },
        "fee": {
          "type": "string"
        },
        "hash": {
          "type": "string"
        },
        "inputs": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "outputIndex": {
                "type": "integer"
              },
              "txHash": {
                "type": "string"
              }
            },
            "required": ["outputIndex", "txHash"]
          }
        },
        "outputs": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "address": {
                "type": "string"
              },
              "assets": {
                "type": "array",
                "items": {
                  "type": "object",
                  "properties": {
                    "assets": {
                      "type": "array",
                      "items": {
                        "type": "object",
                        "properties": {
                          "name": {
                            "type": "string"
                          },
                          "outputCoin": {
                            "type": "string"
                          }
                        },
                        "required": ["name", "outputCoin"]
                      }
                    },
                    "policyId": {
                      "type": "string"
                    }
                  },
                  "required": ["assets", "policyId"]
                }
              },
              "coin": {
                "type": "string"
              }
            },
            "required": ["address", "assets", "coin"]
          }
        },
        "successful": {
          "type": "boolean"
        },
        "validity": {
          "type": "object",
          "properties": {
            "ttl": {
              "type": "string"
            }
          },
          "required": ["ttl"]
        },
        "witnesses": {
          "type": "object"
        }
      },
      "required": [
        "auxiliary",
        "collateral",
        "fee",
        "hash",
        "inputs",
        "outputs",
        "successful",
        "validity",
        "witnesses"
      ]
    }
  },
  "required": ["type", "point", "chain", "record"]
}
```

## Example Message

### Cardano

```json
{
  "type": "transaction",
  "point": {
    "hash": "498726fe257056c4ec4b2f0172ea8bd2bad2a0d68f191b68b9972effec094dd0",
    "slot": 126222397
  },
  "chain": {
    "blockchain": "cardano",
    "network": "mainnet"
  },
  "record": {
    "auxiliary": {},
    "collateral": {},
    "fee": "183101",
    "hash": "tdggcAoPAQPM7VaYPDjKPTa+ofSR0TZ+qG1phD7lvWo=",
    "inputs": [
      {
        "outputIndex": 1,
        "txHash": "tuav++KdQSQZ4qWizHBn5MwzuThFEzf8BXPMhGRsBDA="
      }
    ],
    "outputs": [
      {
        "address": "AVpMGFoKZym+QhZohbB0nbwOp+31nG2REV87Oo5q8DPtb81XYKLg1DvTSA/YULrqN4OZKKlEn9f6",
        "assets": [
          {
            "assets": [{ "name": "S3ViZUNvaW4=", "outputCoin": "65000000" }],
            "policyId": "omAiCWxqgFKYfau/qUhJq3iGzwu3hABE4BfVvg=="
          }
        ],
        "coin": "1189560"
      },
      {
        "address": "gtgYWEKDWBz6e1xwAg3XvI9vJNUYDsa53gIoQ8VKIbCfDuUVoQFYHlgcvqt7XGtF71qxaHKoNyn9MgJoxs2GV0rdR88OSAAaigLYDg==",
        "assets": [
          {
            "assets": [
              { "name": "d29ybGRtb2JpbGV0b2tlbg==", "outputCoin": "1000000000" }
            ],
            "policyId": "HX8zvSPYXhol2H2G+sTxmcMZei96/rZioPNOHg=="
          },
          {
            "assets": [{ "name": "U05FSw==", "outputCoin": "1093896" }],
            "policyId": "J5yQnzSOUz2lgIiY+H+aFLssPfu6zM1jHZJ6Pw=="
          },
          {
            "assets": [{ "name": "S3ViZUNvaW4=", "outputCoin": "34080000000" }],
            "policyId": "omAiCWxqgFKYfau/qUhJq3iGzwu3hABE4BfVvg=="
          },
          {
            "assets": [{ "name": "TUVMRA==", "outputCoin": "335000000000" }],
            "policyId": "opRFc+mdLtMFW4COqiZPC/EZ4B/GsYhjBnxj5A=="
          }
        ],
        "coin": "21511490678"
      }
    ],
    "successful": true,
    "validity": { "ttl": "126229595" },
    "witnesses": {}
  }
}
```
