---
hidden: true
---

# Technical Approach \[DRAFT]

## Key Terms

Before diving into the technical architecture of Kaon, it is essential to understand a few terms that will frequently appear:

* **$KAON**: The native coin on the Kaon blockchain.
* **Mirrored BTC**: A digital twin of Bitcoin (BTC) sent to the Kaon blockchain. For more details, refer to the "BTC Locking and Mirroring Process" section.
* **Wrapped BTC**: Minted by destroying Mirrored BTC on Kaon, this is an ERC20 token. While the ability to trace the exact BTC in the process is lost, the token can be transferred to other chains. Note that tracing from the Mirrored BTC to the original BTC in the Bitcoin Network is not possible with ERC20 tokens.
* **Cross-chain Validators (Collators)**: These are established Bitcoin miners, node holders, validators, or node holders of any other integrated chain. They run an embedded node from the target chain within the Kaon node. Randomly selected validators participate in the Secure Multi-Party Computation (sMPC) key generation ceremony per epoch for all the signatures used within the chain to generate token locks.
* **Voucher**: A system record that identifies exact mirroring events, represented as a Non-Fungible Token (NFT). It is a part of the Mirrored BTC.
* **Inventory**: A special type of transport container commonly used in classic blockchains to transfer data about blocks and transactions from peer to peer.

## Diagrammes: Key Processes

The following diagrams for each process provide a visual representation of Kaon’s data flow. These visual aids are designed to enhance the understanding of Kaon’s technical architecture by simplifying complex concepts and illustrating how it works at its core.

***

### Preparation for Launch

#### At Launch:

**Validator Nodes:**

* Kaon begins with **25 validator nodes**.
* At least **10 of these nodes must be Collator Nodes** with Bitcoin integration to work with the Bitcoin Network.

**Master Node:**

* Each node must stake KAON tokens and create a collateral transaction to become a Master Node.
* The required amount of tokens to be staked is dependent on the chain height (the number of confirmed blocks in the chain).

**Validator:**

* Entities that stake and lease KAON tokens, optionally running an embedded hot node of Bitcoin or any other integrated chain along with the Kaon node.
* Validators have the ability to produce new blocks, receive rewards, and collect gas fees from transactions added to a block.
* At the start of the Epoch, they must apply to become Validators and pass a public vote.
* The Master Node must have a leased amount to its address using the special core functionality called the Leasing Manager.

**Collator Node (Not Active):**

* To interact with Bitcoin, at least **10 nodes** need to embed the latest release of Bitcoin Core into the Kaon Node and perform a standard Bitcoin Node setup and sync.

**Cross-chain Validator or Collator Node:**

* After embedding Bitcoin Core, these nodes must be added to the Store Groups of BFT to participate in sMPC (Secure Multi-Party Computation) rounds.
* To do this, they must stake an additional amount of KAON tokens in the BFT consensus to become a Collator Node or a Cross-chain Validator.
* The exact amount to be staked is governed by the chain consensus, similar to how gas limits and gas prices are managed by consensus.

***

### Kaon Chain Consensus (dPoS)

Kaon Chain employs a Delegated Proof of Stake (dPoS) consensus mechanism, a Bitcoin-family consensus model based on stakes and delegates. This forms a two-level network comprising Master Nodes, Validators, and other nodes. Our implementation extends the traditional model to manage whitelists for BFT consensus.

#### Epochs and Super-Cycles:

* An **Epoch** consists of **80 blocks** (this number is subject to change).
* Kaon uses Epochs and super-cycles to structure its blockchain operations.

#### Master Nodes and Validator Candidates:

* Master Nodes can apply to become Validator Candidates starting from block **0** of the Epoch up to block **69**.
* From block **70** to block **80**, current Validators vote for Validator Candidates for the next round of validators.
* A Validator cannot apply to be a Validator Candidate and cannot be voted for during the Public Vote step.

#### Validator Rotation:

* At the end of each Epoch, there is a rotation of validators.
* Old Validators are removed from the Validators Pool.
* New Validators are randomly selected from the top-voted candidates of the Voting step.

#### Reward Distribution:

* The consensus mechanism is responsible for distributing rewards.
* The reward for a block consists of two parts:
  * A **base part**, which depends on the block height (Kaon has a modified halving algorithm).
  * The cumulative spent **gas** of all transactions included in the block.

#### Recipients of Rewards:

* The Validator who proposed the accepted block.
* Master Nodes that voted by placing their stakes on the block.
* **30%** of the reward is allocated to the **Treasury**.

***

### Kaon Cross-Chain Consensus BFT

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

#### Managing Lock Script

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

***

### Oracle-less Cross-chain Message Processing Process

The relay process in Kaon's cross-chain messaging does not utilize oracles, as the responsible nodes are legitimate parts of another chain. These nodes bear the responsibility for the process, and they manage fees and rewards within the dPoS and BFT consensus.

* **Threshold Signatures**: This process leverages threshold signatures from the current round, where consensus is achieved when the transaction is signed by the required number of actors.
* **Store Groups**: The process includes Store Groups with Cross-chain Validators (Collator Nodes), formed based on a whitelist provided by dPoS Epoch Validators. Each round, a slot leader is selected.
* **Transaction Maturity**: For a transaction to be processed across chains, it must reach maturity as defined by the connected network’s parameters, which ensures the transaction is irreversible.
* **Transaction Rejection**: If a transaction is rejected at any stage before reaching maturity, it is returned to the sender.

#### Slot Leader Selection:

1. **DbInit**: Ensures all necessary data structures and storage are prepared before any consensus-related activities begin.
2. **initSma**:
   * **Random Genesis Generation**: Utilizes a deterministic approach by hashing a predefined input to establish the initial set of values used in leader selection.
   * **Epoch Leader Retrieval**: Fetches the leaders of the genesis epoch from a precompiled smart contract, which holds the whitelisted addresses allowed to participate in the initial consensus rounds.
   * **Public Key Mapping**: Maps public keys to their respective leaders based on the retrieved data, setting up the necessary infrastructure for PKI operations within the consensus mechanism.
   * **Commitment Generation**: Each slot leader generates a commitment based on their public key and a shifted version of it (using a predetermined alpha value). These commitments serve as proofs of their participation and integrity in the consensus process.
   * **Transaction Broadcasting**: Commitments are then wrapped into transactions and broadcasted to the network.

***

### Bridge-less ERC Cross-chain Transfers Process

In Kaon, we have designed a system that facilitates secure and efficient cross-chain transfers without the need for intermediary bridges. This process is managed by specialized listeners and a set of clearly defined steps.

#### The Listeners

* **Monitoring**: The listener continuously monitors incoming messages and transactions.
* **Expected Parameters**: It holds the expected parameters of incoming transactions from the Bitcoin Network or other chains.
* **Lifespan and Gas**: The listener has a defined gas amount that determines its lifespan, which can be refilled as necessary.
* **Triggering**: When triggered, the listener creates a new UTXO transaction in Kaon using parameters from both its creation and the incoming transaction, utilizing its stored gas as the transaction fee.

#### Locks and Vouchers

* **Collator Mempool**: The new EVM transaction is added to the EVM chain part’s mempool by the collator nodes.
* **Triggering the Listener**: The listener is triggered based on parameter recognition and verification.
* **Token Lock Handling**:
  * If the transaction involves a token lock (indicating liquidity entering the chain from outside), a voucher (NFT) is created to identify the exact lock.
* **Token Unlock Handling**:
  * If the transaction involves token unlocking, the corresponding voucher is detected and destroyed.
* **Transaction Composition and Signing**: The transaction is composed and signed using the Threshold Signature Scheme (TSS).
* **ERC Token Exchange**: The voucher is subsequently exchanged for the corresponding ERC tokens.
* **Transaction Acceptance**: The final step ensures the acceptance and validation of the transaction, completing the cross-chain transfer process.

***

### Contract-to-Contract Call

* **Proxy Message Type**: Specific message type proxies a call from one smart contract to another smart contract.
* **Chain of Calls**:
  * A smart contract call is relayed through a transaction to our router (a proxying smart contract that serves as an execution table of tables).
  * The initial smart contract call is sent to the router, which serves as an intermediary ensuring the call is directed to the correct destination.
* **Collator Node Routing**:
  * Our collator node picks up the transaction from the router.
  * The collator then routes the transaction to the appropriate destination, ensuring the integrity and security of the call.
* **Execution**:
  * The chain of calls is executed similarly to how an ordinary smart contract would function.
  * Each call in the chain is processed sequentially, ensuring that all conditions and parameters are met.

***

### BTC Locking and Mirroring Process

In Kaon, we anticipate that users may want to utilize BTC in scenarios that are only implementable using Solidity Smart Contracts. To facilitate this, our chain supports a comprehensive BTC locking and mirroring process.

1. **User Interaction**:
   * The user makes a view call to the Kaon contract (at address `0x0…1`, where addresses `0x0…1` to `0x0…1000` are reserved). It sends a lock script of P2PSH type for users to use to lock BTC in the Bitcoin Network.
2. **Message Transactions**:
   * **Output New Lock (P2PSH)**: The initial transaction output with a new P2PSH lock script.
   * **Output Updated Lock by Kaon in BTC**: Subsequent transactions update the lock script managed by Kaon in the Bitcoin network.
   * **Output BRC/ERC New Record in Kaon**: A new record is created in Kaon’s transaction history, involving an Inventory container to spread information throughout the chain.
3. **Voucher Creation**:
   * A voucher (similar to an NFT) is issued, identifying the exact lock and linking it to the user’s transaction.
   * The user’s voucher tokens are managed and utilized as part of the locking and mirroring process.

#### Mirroring Process:

* **Initiation**:
  * The user makes a view call to the reserved Kaon contract address.
  * The user initiates a request to lock BTC using a specialized P2PSH script.
* **Transaction Management**:
  * Initial and subsequent transactions manage the lock script on the Bitcoin network.
* **Voucher Issuance**:
  * A voucher is issued to represent the locked BTC.

#### BTC Withdrawal Process

1. **User Action**:
   * The user sends tokens to the contract where they initially received the P2PSH lock.
2. **Verification**:
   * Ensure the sender is the native BTC sender.
   * If using a MetaMask address, specify the receiver; otherwise, the transaction will be rejected.
3. **Transaction Composition**:
   * A P2PKH output is created.
   * The BTC is sent back to the user through a TSS signed transaction, completing the withdrawal process.
4. **Voucher Handling**:
   * The contract verifies the voucher and then destroys it to prevent reuse.

***

### Mirrored BTC and EVM Interactions Process

It is crucial to understand the unique properties of Mirrored BTC (mBTC). It is basically a dual-record token, where the BRC standard token part keeps transaction records for specific tokens, while ERC metadata facilitates interaction with EVM smart contracts.

1. **User Transaction**:
   * The user sends Mirrored BTC to a designated smart contract.
2. **BRC20 Verification**:
   * The transaction first enters the BRC20 section, where the Mirrored BTC verifies its data and balance.
3. **Transition to EVM State**:
   * After verification, the transaction transitions to the EVM state.
4. **Metadata Injection**:
   * Predefined ERC token metadata is injected at this stage.
5. **EVM Execution**:
   * The EVM state executes its standard methods, which are masked calls to the BRC/EVM processor that actually handles the responses.
   * This forms EVM state updates, including updates to the EVM store and the transactions Merkle Tree.
6. **State Updates and Mempool Addition**:
   * After verification by the BRC/ERC processor, the state updates produce UTXO sub-transactions, which are submitted to the mempool (they are appended to the end of the block during its composition).
7. **Network Propagation**:
   * Once the block is verified and accepted locally, it is spread across the entire blockchain network using an inventory system.

***

### MetaMask and Other Off-chain EVM Wallets Support Process

When a user sends a request from MetaMask or another EVM wallet, it undergoes a transformation process to ensure compatibility with the Kaon ecosystem:

1. **Eth RPC Gate**:
   * Transforms EVM wallet requests into Bitcoin RPC 1.26 type requests and sends them to the Kaon Node.
   * May compose additional information as needed.
   * Provides transaction information in Ethereum format, ensuring compatibility and ease of use for MetaMask and other EVM wallets.
2. **WebSockets Subscriptions**:
   * Provides WebSockets subscriptions for new events by wrapping ZMQ events from the Kaon node.

#### Support and Processing:

* **Account Balance and Transaction Indexing**:
  * Each Kaon Masternode has the Account Balance Index and Transaction Index turned on to support balance requests and transaction composition based on RLP (Recursive Length Prefix) data.
* **RLP Processing Module**:
  * For RLP-type requests (`sendrawtransaction` calls such as sending money, deploying contracts, and executing contract methods), Kaon:
    * Decodes the input.
    * Validates the input and its parameters.
    * Composes a UTXO transaction based on the input.
    * Signs the transaction using Cascade Signature with the input RLP transaction as its basis.
* **Denomination Handling**:
  * Despite being a Bitcoin fork, Kaon supports a denomination with **18 zeros** for the native token to align with EVM standards.
* **Complete EVM Integration**:
  * Kaon includes a complete EVM as part of its core.
* **Unified Gas Fees**:
  * Kaon composes gas fees and network Bitcoin-type fees (based on priorities) to present a single gas fee to the user, making it hidden from the user.
* **Gas Price and Transaction Estimation**:
  * Methods for current gas price and gas transaction estimation work seamlessly to support EVM wallets.

***

### Mirrored BTC Transfer to EVM Chain Process

1. **User Initiation**:
   * The transfer process is initiated by the user.
2. **Mirrored BTC Movement**:
   * Upon recognition of the transfer, the Mirrored BTC is moved to the network’s storage.
   * This storage is part of the cross-chain pool that manages the creation of new vouchers for cross-chain transactions.
3. **Voucher Creation**:
   * The cross-chain pool storage creates new vouchers to represent the Mirrored BTC tokens.
4. **Token Transformation**:
   * The special BRC/ERC Mirrored BTC tokens are transformed into simple ERC20 tokens because other chains do not support the Bitcoin UTXO paradigm.
   * Maintaining the UTXO model on these chains is impossible.
5. **ERC20 Token Issuance**:
   * New ERC20 tokens are issued on the target EVM chain, representing the transformed Mirrored BTC.
   * These tokens adhere to the ERC20 standard, ensuring compatibility with the EVM chain’s infrastructure.
6. **Reversibility**:
   * Although the UTXO model is not supported on EVM chains, the transformation process can be reversed using the voucher produced by this transformation.

***

### Restore Mirrored BTC from Wrap Process

1. **User Action**:
   * The user initiates the process by sending the ERC20 tokens (which represent the wrapped Mirrored BTC) to a designated smart contract on the Kaon chain.
2. **Voucher Identification**:
   * The smart contract recognizes the transaction and identifies the corresponding voucher created during the initial wrapping process.
3. **Voucher Validation**:
   * The voucher undergoes a validation process to ensure its authenticity and to confirm that it matches the ERC20 tokens being unwrapped.
4. **Voucher Destruction**:
   * Upon successful validation, the voucher is destroyed to prevent any double-spending or reuse.
5. **Minting Mirrored BTC**:
   * The details from the destroyed voucher are used to trigger the creation of Mirrored BTC tokens back on the Kaon network.
   * The Kaon network mints new Mirrored BTC tokens equivalent to the amount specified in the destroyed voucher.
6. **Completion**:
   * The newly minted Mirrored BTC tokens are sent to the user’s wallet within the Kaon network, completing the restoration process.

#### Alternative Method:

* It is possible to reverse the process without using a voucher and using only tokens.
* In this method, the Kaon pool will assign Bitcoins randomly and only from those received from unrecognized sources.

***

### Gasless Operating Process

1. **Transaction Initiation**:
   * The user initiates a transaction to transfer some tokens without holding the native gas token for transaction fees.
2. **Fee Conversion**:
   * A portion of the transaction fee is paid using the tokens being transferred.
   * Some tokens are converted to Kaon tokens.
3. **Universal Fee Management**:
   * This gasless mechanism acts as a universal means for transaction fee management, making it possible to conduct transactions across different chains without needing the native gas token.
   * This can later be adapted to use a stablecoin for even more stability and predictability.
4. **Slippage Handling**:
   * The system accounts for slippage up to three times (Slippage X3) to ensure the transaction’s success.
5. **Cross-Network Functionality**:
   * The gasless mechanism is designed to work across various blockchain networks, with Observers being introduced.

***

### Operator Set Changes Process

1. **Decentralization**:
   * The validator set is managed in a decentralized manner, eliminating the need for additional actions when validators change.
2. **Open Participation**:
   * Any eligible participant can join the validators' pool without restrictions, ensuring a diverse and resilient validator set.
3. **Cold Staking**:
   * Participants can opt for cold staking, which allows them to stake tokens without having them online.
4. **Dynamic Validation**:
   * The validator set can dynamically adjust to changes in validator participation, such as new validators joining or existing validators leaving.
5. **BFT Consensus Role**:
   * The BFT consensus mechanism works in tandem with the dPoS framework to validate transactions and produce blocks.

***

### Slashing Incidence Process

#### Incident Detection and Reporting:

1. **Block Not Accepted**:
   * When a block proposed by a validator is not accepted, it triggers a report node mechanism.
2. **Validator Reporting**:
   * Validators report the invalid block (`inv`) or invalid transactions (`inv`) associated with the block.
3. **Consensus Mechanism**:
   * The consensus mechanism processes the report and, if validated, the incident is escalated.

#### Increasing Distrust Level:

1. **Consensus Decision**:
   * The consensus mechanism evaluates the report.
   * If the block or transaction is rejected, the distrust level of the involved validator is increased.
2. **Malicious Behavior Reporting**:
   * Validators can report any malicious actions observed from another validator.
3. **Uptime Monitoring**:
   * Validators continuously monitor each other’s uptime.
   * Lack of uptime or unavailability is also reported.

#### Validator Response and Exclusion:

1. **Ping Mechanism**:
   * Validators and Master Nodes periodically ping each other to check for responsiveness.
2. **No Reaction**:
   * If a validator does not respond or is unavailable, this is reported.
3. **Exclusion Decision**:
   * Based on the reports, the consensus mechanism may decide to exclude the unresponsive validator from the current round.

#### Reaching Distrust Maximum:

1. **Distrust Level Monitoring**:
   * The distrust level of each validator is continuously monitored.
2. **Slashing Decision**:
   * If a validator’s distrust level reaches the maximum threshold, a slashing decision is made through the consensus mechanism.

#### Executing Slashing:

1. **Consensus Approval**:
   * The consensus mechanism must approve the slashing decision.
   * This involves validating the accumulated reports and ensuring that due process has been followed.
2. **Validator Slashing**:
   * Once approved, the validator is slashed. This may involve:
     * **Token Penalties**: The validator’s staked tokens are partially or fully burnt.
     * **Exclusion**: The validator is excluded from the network.
       * Every member of the chain won’t accept any information from the slashed validator.
       * The validator’s address won’t be accepted anymore in a transaction validation process.

***

## Development

### EVM Smart Contracts

* Kaon's node EVM offers comprehensive support without zero-knowledge (ZK) functionalities, adhering to adjusted gas price policies.
* The gas limit is managed by validator consensus, ensuring fair and efficient network operation.

### Bitcoin SigScript

* Kaon is part of the Bitcoin family, fully supporting Bitcoin SigScript for its native transactions.
* It incorporates a few additional `OP_CODEs` for internal use while strictly following the Bitcoin GitHub repository.
* Kaon does not include unconfirmed `OP_CODEs` from pull requests and discussions, with rare exceptions for essential updates.

### BitVM Support

* Kaon supports **Tree++** and will continue to follow its updates.
* This ensures that Kaon remains compatible with advanced scripting capabilities and the evolving BitVM ecosystem.

### Bitcoin Rust Support

* Kaon has forked and adapted the **bitcoin-rust** library, making core parameters current and functional.
* The adapted version is shared for community use and further development.

### Bitcoin RPC / Ordinals API

* Kaon provides **kaon-cli** and **kaond** RPC interfaces based on the Bitcoin 1.26 RPC standard, with additional methods to support enhanced functionalities.
* A demonstration API for **Ordinals** and **BRC20** is also available, showcasing the integration and usage of these features within the Kaon network.

### Ethereum Gate API

* Users have the capability to interact with the Ethereum network via the Ethereum Gate API, facilitating seamless cross-chain communication and asset management.

***

## Key Innovations

Kaon introduces several groundbreaking changes that set this system apart from other existing blockchain platforms:

1. **mirrorBTC**:
   * Ensures mirrored token interactions are genuinely connected to the original BTC, addressing trust and security issues associated with wrapped BTC.
   * Built using a BRC20-alike approach with a basis transaction, and has predefined metadata and handlers enabling EVM to interact with them just like it does with smart contracts.
2. **UTXO-EVM Combination**:
   * Allows seamless interaction with inscriptions, ordinals, BRC20, and runes from smart contracts, bridging the gap between UTXO and EVM models.
3. **RLP Transaction Support**:
   * Enables seamless integration with MetaMask and other Ethereum-based tools, a unique feature among UTXO chains, based on the Onion (Cascade) signature concept.
4. **1e-18 Denomination**:
   * Offers 10 times less expensive gas fees and complete EVM support, surpassing the traditional 1e-8 BTC denomination.
   * All compression and encoding algorithms are updated accordingly to reduce excessive zeros in a database as much as possible.
5. **RPC Ethereum Full Support (geth client type)**:
   * UTXO transactions are parsed as standard Ethereum transactions, ensuring compatibility with Ethereum tools like Blockscout.
6. **Cross-Contracts**:
   * Facilitates smart contract interactions across different chains, simplifying the creation of cross-chain NFTs, stablecoins, and dApps.
7. **Observers**:
   * Introduces a subscription model for dApps to back-spread specific events from other chains, enhancing user experience and possible interaction complexity.
8. **Control over Pools by the Chain**:
   * Ensures liquidity pools for swaps are managed by the chain, enhancing security and trust.
   * Ensures the non-custodial nature of the pool in Bitcoin-family chains.
9. **Merge of Nodes**:
   * Combines nodes from different chains to operate as a single entity (or binary), eliminating the need for oracles in cross-chain interactions.
10. **Same Private Keys as in BTC**:
    * Allows users to sign transactions in both chains with the same wallet, leveraging account abstraction with BTC.

These innovations collectively position Kaon as a versatile and forward-thinking L1 capable of creating entirely new financial strategies.
