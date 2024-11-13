# Oracle-less Cross-chain Message

The relay process in Kaon's cross-chain messaging does not utilize oracles, as the responsible nodes are legitimate parts of another chain. These nodes bear the responsibility for the process, and they manage fees and rewards within the dPoS and BFT consensus.

* **Threshold Signatures**: This process leverages threshold signatures from the current round, where consensus is achieved when the transaction is signed by the required number of actors.
* **Store Groups**: The process includes Store Groups with Cross-chain Validators (Collator Nodes), formed based on a whitelist provided by dPoS Epoch Validators. Each round, a slot leader is selected.
* **Transaction Maturity**: For a transaction to be processed across chains, it must reach maturity as defined by the connected network’s parameters, which ensures the transaction is irreversible.
* **Transaction Rejection**: If a transaction is rejected at any stage before reaching maturity, it is returned to the sender.

### Slot Leader Selection:

1. **`DbInit`**: Ensures all necessary data structures and storage are prepared before any consensus-related activities begin.
2. **`initSma`**:
   * **Random Genesis Generation**: Utilizes a deterministic approach by hashing a predefined input to establish the initial set of values used in leader selection.
   * **Epoch Leader Retrieval**: Fetches the leaders of the genesis epoch from a precompiled smart contract, which holds the whitelisted addresses allowed to participate in the initial consensus rounds.
   * **Public Key Mapping**: Maps public keys to their respective leaders based on the retrieved data, setting up the necessary infrastructure for PKI operations within the consensus mechanism.
   * **Commitment Generation**: Each slot leader generates a commitment based on their public key and a shifted version of it (using a predetermined alpha value). These commitments serve as proofs of their participation and integrity in the consensus process.
   * **Transaction Broadcasting**: Commitments are then wrapped into transactions and broadcasted to the network.
