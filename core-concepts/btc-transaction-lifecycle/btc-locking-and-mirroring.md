# BTC Locking and Mirroring

In Kaon, we anticipate that users may want to utilize BTC in scenarios that are only implementable using Solidity Smart Contracts. To facilitate this, our chain supports a comprehensive BTC locking and mirroring process.

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

The user makes a view call to the Kaon contract (at address 0x0…1, where addresses 0x0…1 to 0x0…1000 are reserved). It sends a lock script of P2PSH type for users to use it to lock BTC in the Bitcoin Network.

### **Message Transactions**

* **Output New Lock (P2PSH):** The initial transaction output with a new P2PSH lock script.
* **Output Updated Lock by Kaon in BTC:** Subsequent transactions update the lock script managed by Kaon in the Bitcoin network.
* **Output BRC/ERC New Record in Kaon:** A new record is created in Kaon’s transaction history, involving an Inventory container to spread information throughout the chain.

### **Voucher Creation**

* A voucher (similar to an NFT) is issued, identifying the exact lock and linking it to the user’s transaction.
* The user’s voucher tokens are managed and utilized as part of the locking and mirroring process.

### **Mirroring process**

* The user makes a view call to the reserved Kaon contract address.
* The user initiates a request to lock BTC using a specialized P2PSH script.
* Initial and subsequent transactions manage the lock script on the Bitcoin network.
* A voucher is issued to represent the locked BTC.
