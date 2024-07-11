# Rewards Server Message Partial

This top level message key is added by the server as part of any synchronization process for every epoch boundary transition.
In respect to the Cardano blockchain, there are two kinds of rewards:

## 1. Staking Rewards

Staking rewards originate from two sources:

### Reserve/ Monetary expansion

The reserve is a fixed amount of ADA set aside at the blockchain's creation that is gradually distributed over time every epoch.

### Transaction Fees

A portion of fees is collected from network transactions.

> [!NOTE]
> More details can be found [here](https://docs.cardano.org/about-cardano/explore-more/monetary-policy/#monetary-policy).

## 2. Voting Rewards

Voting rewards for participating in Catalyst are funded by the treasury which receives a percentage of transaction fees (defined by a protocol parameter).
The treasury is primarily used for funding development and improvements to the Cardano ecosystem but has also distributed rewards through community voting in the past.

## Withdrawals

By default, any accrued rewards are a credit in a special rewards account associated to the respective wallet until withdrawn. This rewards account uses a balance model. A wallet's balance already includes rewards as soon as they are granted. In order for a wallet to claim/ spend aggregated rewards, the user must initiate a withdrawal transaction, which moves the rewards from the rewards account to the wallet, creating new UTxOs.

> [!NOTE]
> Withdrawals are a specific field within a transaction, and they are serialized as part of the transaction's CBOR representation. This allows wallets to derive their UTxO set as well as available reward balance.

## Schema

To enable wallets to distinguish between both kind of rewards, a reward message contains its source type (delegation/ voting).

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "rewards": {
      "type": "object",
      "properties": {
        "epoch": {
          "type": "integer",
          "minimum": 0
        },
        "delegation": {
          "type": "object",
          "properties": {
            "poolId": {
              "type": "string",
              "pattern": "^pool[a-zA-Z0-9]{54}$"
            },
            "lovelace": {
              "type": "integer",
              "minimum": 0
            }
          },
          "required": ["poolId", "lovelace"]
        },
        "voting": {
          "type": "object",
          "properties": {
            "lovelace": {
              "type": "integer",
              "minimum": 0
            }
          },
          "required": ["lovelace"]
        }
      },
      "required": ["epoch"],
      "anyOf": [
        { "required": ["delegation"] },
        { "required": ["voting"] }
      ]
    }
  },
  "required": ["rewards"]
}
```

## Example

```json
{
  "rewards": {
    "epoch": 223,
    "delegation": {
      "poolId": "pool13gdtqme63jprkug3j4wzslhmu0yk4kdx323rtxpjuz7rqv3yyes",
      "lovelace": 1234567
    },
    "voting": {
      "lovelace": 111222
    }
  }
}
```
