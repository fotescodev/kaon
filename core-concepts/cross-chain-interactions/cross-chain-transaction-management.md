# Cross-Chain Transaction Management

#### Oracle-less Cross-chain Message Processing Process <a href="#oracle-less-cross-chain-message-processing-process" id="oracle-less-cross-chain-message-processing-process"></a>

The relay process in Kaon's cross-chain messaging does not utilize oracles, as the responsible nodes are legitimate parts of another chain. These nodes bear the responsibility for the process, and they manage fees and rewards within the dPoS and Galaxy consensus.\


{% hint style="info" %}
Needs Diagram
{% endhint %}



This process leverages threshold signatures from the current round, where consensus is achieved when the transaction is signed by the required number of actors.

* The process includes Store Groups with Cross-chain Validators (Collator Nodes), formed based on a whitelist provided by dPoS Epoch Validators. Each round, a slot leader is selected.
* For a transaction to be processed across chains, it must reach maturity as defined by the connected network’s parameters, which ensures the transaction is irreversible.
* If a transaction is rejected at any stage before reaching maturity, it is returned to the sender.

1. **Slot Leader Selection:**
   * (`DbInit`) Ensures all necessary data structures and storage are prepared before any consensus-related activities begin.
   * (`initSma`) The core function is multi-step process involves :
   * **Random Genesis Generation:**
     * Utilizes a deterministic approach by hashing a predefined input to establish the initial set of values used in leader selection.
   * **Epoch Leader Retrieval:**
     * Fetches the leaders of the genesis epoch from a precompiled smart contract, which holds the whitelisted addresses allowed to participate in the initial consensus rounds.
   * **Public Key Mapping:**
     * Maps public keys to their respective leaders based on the retrieved data, setting up the necessary infrastructure for public key infrastructure (PKI) operations within the consensus mechanism.
2. **Commitment Generation:**
   * Each slot leader generates a commitment based on their public key and a shifted version of it (using a predetermined alpha value). These commitments serve as proofs of their participation and integrity in the consensus process.
3. **Transaction Broadcasting:**
   * Commitments are then wrapped into transactions and broadcasted to the network. This involves additional activities to form a secure message for broadcast.
