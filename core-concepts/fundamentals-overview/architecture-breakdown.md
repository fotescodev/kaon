# Architecture View

```markup
%% Set default font style to Arial for compatibility
%% Use font-size and color to ensure text displays correctly in PowerPoint

flowchart RL
 subgraph s3[" "]
    direction TB
        BTC["Bitcoin"]
        CL["Consensus<br /> Layer"]
 end
 style BTC fill:transparent,stroke:#F7931A,font-family:Arial,font-size:14px
 style CL fill:transparent,stroke:#000000,font-family:Arial,font-size:14px
 style s3 fill:transparent,stroke-width:0px,font-family:Arial

 subgraph s4[" "]
    direction TB
         NI["Node Interface"]
 end
 style NI fill:none,stroke:#000000,font-family:Arial,font-size:14px
 style s4 fill:none,stroke-width:0px,font-family:Arial

 subgraph s1["BFT Consensus:"]
    direction TB
        n4["Crosschain Mempool"]
        s2
        n1["Verifies data accuracy<br> and integrity"]
  end
 style s1 fill:none,stroke:#000000,font-family:Arial,font-size:14px
 style n4 fill:none,stroke:#000000,font-family:Arial,font-size:14px
 style s2 fill:none,stroke:#000000,font-family:Arial,font-size:14px
 style n1 fill:none,stroke:#000000,font-family:Arial,font-size:14px

 subgraph s2["sMPC Groups / TSS Providers"]
    direction TB
        n3["Witness Group"]
        n2["Validator Group"]
 end
 style s2 fill:none,stroke:#000000,font-family:Arial,font-size:14px
 style n3 fill:none,stroke:#000000,font-family:Arial,font-size:14px
 style n2 fill:none,stroke:#000000,font-family:Arial,font-size:14px

    t1["*mirrored transactions can be mirrored again"]
    t2["Validator group: First required signature to send transaction to Kaon or to Bitcoin Network"]
    t3["Witness Group: Second required signature, approve locked BTC to be sent or mark it as corrupt"]
    t4["Transactions are processed in a similar manner as on Bitcoin"]
    
    s3 ~~~ s4 ~~~ s1
    n2 ~~~ n3
    n1 ~~~ s2
    CL -- Kaon Transaction --> NI
    BTC -- Kaon Transaction --> NI
    NI -- Mirrored Transaction --> CL
    NI -- Mirrored Transaction --> BTC
    CL -- Rotate and determine new signers for new epoch --> s1
    NI -- Kaon Transaction --> s1
    s1 -- Aggregates and broadcasts<br /> Mirrored Transactions to Kaon<br /> or Bitcoin --> NI
    n4 -- Pulls in transaction<br /> to be mirrored* --> s2
    s2 -- Pushes partially<br /> signed mirrored<br /> transaction --> n4
    s1 ~~~ t1
    CL ~~~ t2
    CL ~~~ t3
    CL ~~~ t4

    style n1 stroke-width:0px,fill:transparent,font-family:Arial
    style s3 stroke-width:0px,fill:transparent,font-family:Arial

    t1:::Ash
    t2:::Ash
    t3:::Ash
    t4:::Ash
    classDef Ash stroke-width:1px, stroke-dasharray:none, stroke:#999999, fill:#EEEEEE, color:#000000, font-family:Arial, font-size:14px
```

The architecture is designed to facilitate secure and deterministic cross-chain operations between the Bitcoin network and Kaon's Consensus Layer. At its core is the **Consensus Layer**, an L1 system providing Byzantine BFT by randomly selecting and rotating validators through epochs. Each **Epoch** determines a new set of validators, ensuring a dynamic yet deterministic participant pool.

Validators are organized into **sMPC Groups** (secure Multi-Party Computation groups), which are coordinated sets of nodes responsible for collectively producing threshold signatures and managing cross-chain operations. These groups have specific roles:

* **Validator Groups**: Maintain locks over received BTC and can send it, but only after approval from the corresponding Witness Group.
* **Witness Groups**: Approve the release of locked BTC or mark it as corrupt.

Individual nodes within these groups are called **sMPC Participants**. They hold key shares, participate in collective signing processes, and monitor network transactions for their assigned group.

The **Node Interface** serves as an intermediary, wrapping original nodes to trigger specific logic upon receiving transactions and assisting in composing Mirrored Transactions. These transactions are pivotal for the **Peg In** and **Peg Out** processes, effectively mirroring assets between the Bitcoin Network and Kaon's Consensus Layer.

To ensure deterministic transaction relaying and maintain process integrity, the **Cross Chain Mempool** leverages UTXO features. It acts as a conduit between sMPC Groups, Node Interfaces, and both networks, facilitating the broadcasting and detection of transactions, and acts as a State manager.

To maintain continuity across epochs, the system employs a process called **Sweeping**, which delegates BTC locks from the Validator and Witness Groups of the previous epoch to the sMPC Groups of the current epoch.

Epoch management is a collaborative effort between the Consensus Layer and the BFT Consensus, determining validator selection for each new epoch. In instances of malicious activity, the Consensus Layer has the authority to enforce slashing, penalizing validators by confiscating their stakes to uphold network integrity.

The interactions between components are as follows:

* The **Node Interface** provides to the **Cross Chain Mempool** utility to detect transactions' state.
* **sMPC Participants** broadcast mirrored transactions through the **Node Interface**.
* The **Node Interface** processes confirmed transactions with specific taproot signature with message "KAON" on its first leaf and interacts through the **Cross Chain Mempool** with sMPC Groups to ensure the correctness of the process state.
* The **Cross Chain Mempool** interacts with sMPC Groups to receive prepared and signed mirrored transactions for broadcasting.
* **sMPC Groups** represents collaboration of its sMPC Participants to produce invoice addresses for peg-in transactions and sign mirrored transactions using PBST standard.
* The **Consensus Layer** works with the BFT Consensus mechanism to switch epochs, form new participant lists, resolve incidents, and apply judgments via slashing.

Overall, the architecture is designed to handle cross-chain operations securely, transparently and uncontrollable. Through the coordinated efforts of sMPC Groups, deterministic transaction management, and a resilient consensus mechanism, the Kaon Network ensures efficient, continuous and secure interactions with the Bitcoin Network.
