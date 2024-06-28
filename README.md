# Wallet API

The primary goals of a well-designed API for a multi-chain digital wallet are to provide the data required to construct transactions and to compute the current state of the wallet. This includes aggregating transactions and on-chain events to present wallet history, current balance, and chain-specific features such as stake delegation and rewards.

### What does a wallet need to construct transactions?

In general, to be able to construct transactions the following data is required:

- transactions (incoming/ receiving & outgoing/ spending)
- staking rewards *if applicable*
- network/ era specific data, like:
    - protocol parameters (Cardano)
    - current fee rate, satoshis per byte, sats/byte (Bitcoin)
    - gas limit, max fee per gas, max priority fee per gas, nonce (Ethereum)
- the current tip/ block height for validity intervals of transactions

## API Design

We divide the wallet-optimized API into a [push-based, event-driven API](./docs/01-Stream-api.md) and a request/ response API.