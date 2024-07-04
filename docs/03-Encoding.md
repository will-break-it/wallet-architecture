# Encoding Standards

Our proposed communication protocol **adopts blockchain-native encoding standards** to ensure compatibility and efficiency. This approach leverages existing conventions familiar to clients while minimizing conversion overhead. The protocol incorporates the following encoding standards:

## Transaction Hash Encoding

Transaction hashes are typically encoded using **hexadecimal representation**. This standard provides a compact and human-readable format for unique transaction identifiers.

## Address Encoding

Address encoding varies depending on the blockchain network:
  - Bitcoin: Uses Base58Check encoding for legacy addresses and `Bech32` for SegWit addresses.
  - Ethereum: Employs hexadecimal encoding with a `0x` prefix.
  - Cardano (and others): use custom address formats (e.g., `Bech32` variants).


## Native Asset Policy Encoding

For blockchain networks supporting native assets (e.g., Cardano), policy identifiers are typically encoded in hexadecimal format.

