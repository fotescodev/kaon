---
hidden: true
---

# Key Innovations

Kaon introduces several groundbreaking changes that differentiate this system apart from other existing blockchain platforms:

## mirrorBTC

The **mirrorBTC** feature ensures that mirrored token interactions are genuinely connected to the original BTC, addressing trust and security issues associated with wrapped BTC. Built using a BRC20-like approach with a basis transaction, it includes predefined metadata and handlers. This design enables the Ethereum Virtual Machine (EVM) to interact with these tokens just like it does with smart contracts.

## UTXO-EVM Combination

Kaon allows seamless interaction between Unspent Transaction Output (UTXO) and EVM models. This combination enables inscriptions, ordinals, BRC20 tokens, and runes to interact directly from smart contracts, effectively bridging the gap between UTXO and EVM paradigms.

## RLP Transaction Support

By supporting Recursive Length Prefix (RLP) transactions, Kaon enables seamless integration with MetaMask and other Ethereum-based tools. This feature, based on the Onion (Cascade) signature concept, is unique among UTXO chains and enhances interoperability with existing Ethereum infrastructure.

## 1e-18 Denomination

Kaon adopts a **1e-18 denomination**, offering gas fees that are ten times less expensive than traditional models and providing complete EVM support. This surpasses the conventional 1e-8 BTC denomination. All compression and encoding algorithms are updated accordingly to reduce excessive zeros in the database, optimizing storage efficiency.

## Full RPC Ethereum Support (Geth Client Type)

Kaon parses UTXO transactions as standard Ethereum transactions, ensuring compatibility with Ethereum tools like Blockscout. This full RPC Ethereum support facilitates integration with existing Ethereum-based applications and services.

## Cross-Contracts

Kaon facilitates smart contract interactions across different chains, simplifying the creation of cross-chain NFTs, stablecoins, and decentralized applications (dApps). This cross-contract functionality streamlines the development process for multi-chain solutions and enhances the versatility of dApps built on Kaon.

## Observers

Introducing a subscription model for dApps, Kaon allows the back-spreading of specific events from other chains. This enhances user experience and enables more complex interactions, providing developers with tools to create dynamic and responsive applications.

## Chain-Controlled Pools

Kaon ensures that liquidity pools for swaps are managed by the chain itself, enhancing security and trust. This approach maintains the non-custodial nature of pools in Bitcoin-family chains, providing a secure environment for decentralized finance (DeFi) activities.

## Node Merging

By combining nodes from different chains to operate as a single entity (or binary), Kaon eliminates the need for oracles in cross-chain interactions. This innovation simplifies network architecture, reduces potential points of failure, and enhances overall system efficiency.

## Unified Private Keys

Kaon allows users to sign transactions on both chains with the same wallet by leveraging account abstraction with BTC. This unified private key system simplifies the user experience and enhances security by reducing the number of keys that users need to manage.
