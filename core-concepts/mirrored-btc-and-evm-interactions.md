# Mirrored BTC and EVM Interactions

It is crucial to understand the unique properties of Mirrored BTC (mBTC), it is basically dual-record token, where BRC standard token part keep transaction records for specific tokens, while ERC metadata facilitates interaction with EVM smart contracts.\


<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

1. The user sends Mirrored BTC to a designated smart contract.
2. The transaction first enters the BRC20 section, where the Mirrored BTC verifies its data and balance.
3. After verification, the transaction transitions to the EVM state.
4. Predefined ERC token metadata is injected at this stage.
5. The EVM state executes its standard methods, which are masked calls to the BRC/EVM processor that actually handles the responses.
6. This forms EVM state updates, including updates to the EVM store and the transactions Merkle Tree.
7. After verification by the BRC/ERC processor, the state updates produce UTXO sub-transactions, which are submitted to the mempool (essentially, they are appended to the end of the block during its composition).
8. Once the block is verified and accepted locally, it is spread across the entire blockchain network using an inventory system.
