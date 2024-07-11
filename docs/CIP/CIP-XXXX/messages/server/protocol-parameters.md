# Protocol Parameter Server Message Partial

This top level message key is added by the server as part of any synchronization process for every epoch boundary transition.

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
    "minFeeA": {
      "type": "integer"
    },
    "minFeeB": {
      "type": "integer"
    },
    "maxBlockSize": {
      "type": "integer"
    },
    "maxTxSize": {
      "type": "integer"
    },
    "maxBlockHeaderSize": {
      "type": "integer"
    },
    "keyDeposit": {
      "type": "string"
    },
    "poolDeposit": {
      "type": "string"
    },
    "eMax": {
      "type": "integer"
    },
    "nOpt": {
      "type": "integer"
    },
    "a0": {
      "type": "number"
    },
    "rho": {
      "type": "number"
    },
    "tau": {
      "type": "number"
    },
    "decentralisationParam": {
      "type": "number"
    },
    "extraEntropy": {
      "type": ["null", "string"]
    },
    "protocolMajorVer": {
      "type": "integer"
    },
    "protocolMinorVer": {
      "type": "integer"
    },
    "minUtxo": {
      "type": "string"
    },
    "minPoolCost": {
      "type": "string"
    },
    "nonce": {
      "type": "string"
    },
    "costModels": {
      "type": "object",
      "properties": {
        "plutusV1": {
          "type": "object",
          "additionalProperties": {
            "type": "integer"
          }
        },
        "plutusV2": {
          "type": "object",
          "additionalProperties": {
            "type": "integer"
          }
        }
      }
    },
    "priceMem": {
      "type": "number"
    },
    "priceStep": {
      "type": "number"
    },
    "maxTxExMem": {
      "type": "string"
    },
    "maxTxExSteps": {
      "type": "string"
    },
    "maxBlockExMem": {
      "type": "string"
    },
    "maxBlockExSteps": {
      "type": "string"
    },
    "maxValSize": {
      "type": "string"
    },
    "collateralPercent": {
      "type": "integer"
    },
    "maxCollateralInputs": {
      "type": "integer"
    },
    "coinsPerUtxoSize": {
      "type": "string"
    }
  },
  "required": [
    "epoch",
    "minFeeA",
    "minFeeB",
    "maxBlockSize",
    "maxTxSize",
    "maxBlockHeaderSize",
    "keyDeposit",
    "poolDeposit",
    "eMax",
    "nOpt",
    "a0",
    "rho",
    "tau",
    "decentralisationParam",
    "extraEntropy",
    "protocolMajorVer",
    "protocolMinorVer",
    "minUtxo",
    "minPoolCost",
    "nonce",
    "costModels",
    "priceMem",
    "priceStep",
    "maxTxExMem",
    "maxTxExSteps",
    "maxBlockExMem",
    "maxBlockExSteps",
    "maxValSize",
    "collateralPercent",
    "maxCollateralInputs",
    "coinsPerUtxoSize"
  ]
}
```

## Example

```json
{
  "protocolParameters": {
    "epoch": 225,
    "minFeeA": 44,
    "minFeeB": 155381,
    "maxBlockSize": 65536,
    "maxTxSize": 16384,
    "maxBlockHeaderSize": 1100,
    "keyDeposit": "2000000",
    "poolDeposit": "500000000",
    "eMax": 18,
    "nOpt": 150,
    "a0": 0.3,
    "rho": 0.003,
    "tau": 0.2,
    "decentralisationParam": 0.5,
    "extraEntropy": null,
    "protocolMajorVer": 2,
    "protocolMinorVer": 0,
    "minUtxo": "1000000",
    "minPoolCost": "340000000",
    "nonce": "1a3be38bcbb7911969283716ad7aa550250226b76a61fc51cc9a9a35d9276d81",
    "costModels": {
      "plutusV1": {
        "addIntegerCpuArgumentsIntercept": 197209,
        "addIntegerCpuArgumentsSlope": 0
      },
      "plutusV2": {
        "addIntegerCpuArgumentsIntercept": 197209,
        "addIntegerCpuArgumentsSlope": 0
      }
    },
    "priceMem": 0.0577,
    "priceStep": 0.0000721,
    "maxTxExMem": "10000000",
    "maxTxExSteps": "10000000000",
    "maxBlockExMem": "50000000",
    "maxBlockExSteps": "40000000000",
    "maxValSize": "5000",
    "collateralPercent": 150,
    "maxCollateralInputs": 3,
    "coinsPerUtxoSize": "34482"
  }
}
```
