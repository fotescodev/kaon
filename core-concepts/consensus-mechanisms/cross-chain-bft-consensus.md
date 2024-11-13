# Cross-Chain BFT Consensus

BFT is a cross-chain consensus mechanism based on Store Groups, Shamir polynomials, and the composition of one-time TSS (Threshold Signature Scheme) signatures per each round. It is utilized by Kaon in a cross-chain context and is managed by the chain’s dPoS.

1. **User Interaction**:
   * The user calls a view function `no` on the Kaon contract to obtain an address for the P2SH (Pay-to-Script-Hash) lock script on the Bitcoin Network from the current round of sMPC.
   * This contract also provides the current fee for transfer-type transactions.
2. **Locking BTC**:
   * The user locks BTC using the retrieved address.
   * The Bitcoin node processes the signature of the output from the UTXO (Unspent Transaction Output) transaction sent by the user.
3. **Collator Nodes Processing**:
   * Collator Nodes, part of the Bitcoin Network, will also process this signature.
   * If they identify a valid signature, the Transaction Handler on the Kaon side of the node will be triggered.
4. **Fee Deduction and Distribution**:
   * The fee for the transfer will be deducted from the base transaction value.
   * If the fee is insufficient, the transaction will be ignored until the user sends an additional amount of BTC.
   * The fee will be distributed among the validators who processed the current sMPC round.
5. **Updating Lock Manager**:
   * The received output will be added to the Lock Manager, which will update its lock script according to sMPC round updates.
6. **Minting Mirrored BTC**:
   * Based on the transaction sender, Mirrored BTC (with the fee deducted) will be produced on the Kaon Network, with the sender as the owner.
   * A special token voucher, similar to an NFT, will be issued to the sender as an identifier of the token.

### Managing Lock Script

* At the end of the sMPC round, the Lock Manager may produce an updated P2SH lock script for some of the locked outputs.

#### Reversing the Process (Returning BTC to the Bitcoin Network):

1. **User Initiation**:
   * The user sends their Mirrored BTCs to the contract.
2. **Validation**:
   * Recognition and validation processes are initiated.
   * If successful, the voucher and Mirrored BTC will be destroyed.
3. **BTC Transaction Composition**:
   * On the Bitcoin node side, a P2PKH (Pay-to-PubKey-Hash) type transaction will be composed and signed, with the user as the receiver.
4. **BTC Retrieval**:
   * The user can retrieve their locked BTC, completing the reversal process.
