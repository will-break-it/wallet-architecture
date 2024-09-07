# Introduction: Push-based Blockchain API

In the evolving landscape of multi-chain ecosystems, we propose a streamlined Push-based Blockchain API designed to efficiently serve the fundamental needs of wallets and dApps. This API focuses on a single, crucial function:
  
> delivering relevant transactions to clients in real-time originating from multiple chains.

The simplicity of this approach lies in its power. By providing only the pertinent transaction data, the API enables clients to derive their own state efficiently. This method supports a wide range of functionalities without the need for complex, costly, pre-calculated data from the server side.

For instance, clients may calculate native asset price ratios by analyzing the transactions of a smart contract that tracks an asset pair, or determine current wallet balances by processing the sequence of relevant, historical transactions.

Our API design includes multiple endpoints for transaction retrieval (push & pull), catering to various client scenarios - from high-performance applications to those operating under bandwidth constraints or intermittent connectivity. This flexibility ensures broad compatibility while maintaining the API's streamlined focus.

The core principle driving this API's design is rooted in the most fundamental aspect of blockchain interaction:

> The ability to transact

By concentrating solely on delivering relevant transaction data, the API empowers clients to construct transactions and derive state information autonomously, fostering a more decentralized and efficient multi-chain ecosystem.

For a detailed technical specification and implementation details, please refer to the full:

[Cardano Improvement Proposal (CIP)](./docs/CIP/CIP-XXXX/)

or the more detailed:

 [Cardano Problem Statement (CPS)](./docs/CPS/CPS-XXXX/)
