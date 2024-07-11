# Wallet API

The primary goal of a well-designed API for a multi-chain digital wallet is to provide the data required to construct transactions and to allow deriving the current state of the wallet while the set of subscribed blockchains is continuously extended. This includes aggregating transactions and on-chain events to present users their transaction history, current balance, as well as chain-specific features such as stake delegation or staking rewards.

The proposed API design offers different endpoints to retrieve the same data in order to support a wide range of edge clients,
in particular clients with an intermittent connection or that are bandwidth-constrained.

The API was desgined with an overarching abstraction in mind to focus on the fundamental value that wallets provide:

> The ability to transact

## Problem Desription [CPS](./docs/CPS/CPS-XXXX/)

## Improvement Proposal [CIP](./docs/CIP/CIP-XXXX/)
