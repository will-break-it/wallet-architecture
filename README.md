# Wallet API

The primary goal of a well-designed API for a multi-chain digital wallet is to provide the data required to construct transactions and to allow deriving the current state of the wallet while the set of subscribed blockchains is continuously extended. This includes aggregating transactions and on-chain events to present users their transaction history, current balance, as well as chain-specific features such as stake delegation or staking rewards.

The proposed API design offers different endpoints to retrieve the same data in order to support a wide range of edge clients,
in particular clients with an intermittent connection or that are bandwidth-constrained.

The API was desgined with an overarching abstraction in mind to focus on the fundamental value that wallets provide:

> The ability to transact

## What does a wallet need to construct transactions?

In general, to be able to construct transactions the following data is required:

- any transaction related to a client's wallet (incoming/ receiving & outgoing/ spending)
- staking rewards _if applicable_
- network, era or epoch specific data, like:
  - protocol parameters - tx fee calculation (Cardano)
  - current fee rate, satoshis per byte, sats/byte (Bitcoin)
  - gas limit, max fee per gas, max priority fee per gas, nonce (Ethereum)
- the current tip/ block height for validity intervals of transactions

## API Design

We divide the wallet-optimized API into a [push-based, event-driven API](./docs/01-Stream-api.md) and a request/ response API.
