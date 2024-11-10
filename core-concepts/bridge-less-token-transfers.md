# Bridge-less Token Transfers

In Kaon, we have designed a system that facilitates secure and efficient cross-chain transfers without the need for intermediary bridges. This process is managed by a specialized listeners and a set of clearly defined steps.

### **The Listeners**

<figure><img src="https://kaon.gitbook.io/~gitbook/image?url=https%3A%2F%2Fgithub.com%2Fakropolisio%2Farch-in-diagrams%2Fraw%2Fmain%2Fassets%2Fbridge-less.jpg&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=613dd977&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* **Monitoring:** The listener continuously monitors incoming messages and transactions.
* **Expected Parameters:** It holds the expected parameters of incoming transactions from the Bitcoin Network or other chains.
* **Lifespan and Gas:** The listener has a defined gas amount that determines its lifespan, which can be refilled as necessary.
* **Triggering:** When triggered, the listener creates a new UTXO transaction in Kaon using parameters from both its creation and the incoming transaction, utilizing its stored gas as the transaction fee.

### **Locks and Vouchers**

<figure><img src="https://kaon.gitbook.io/~gitbook/image?url=https%3A%2F%2Fgithub.com%2Fakropolisio%2Farch-in-diagrams%2Fraw%2Fmain%2Fassets%2Flocks-and-vouchers.jpg&#x26;width=300&#x26;dpr=4&#x26;quality=100&#x26;sign=c455a8da&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* **Collator Mempool:** The new EVM transaction is added to the EVM chain part’s mempool by the collator nodes.
* **Triggering the Listener:** The listener is triggered based on parameter recognition and verification.
* **Token Lock Handling:** If the transaction involves a token lock (indicating liquidity entering the chain from outside), a voucher (NFT) is created to identify the exact lock.
* **Token Unlock Handling:** If the transaction involves token unlocking, the corresponding voucher is detected and destroyed.
* **Transaction Composition and Signing:** The transaction is composed and signed using the Threshold Signature Scheme (TSS).
* **ERC Token Exchange:** The voucher is subsequently exchanged for the corresponding ERC tokens.
* **Transaction Acceptance:** The final step ensures the acceptance and validation of the transaction, completing the cross-chain transfer process.

### **Contract-to-Contract Call**

<figure><img src="https://kaon.gitbook.io/~gitbook/image?url=https%3A%2F%2Fgithub.com%2Fakropolisio%2Farch-in-diagrams%2Fraw%2Fmain%2Fassets%2Fcontract-to-contract.jpg&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=f2839bb9&#x26;sv=1" alt=""><figcaption></figcaption></figure>

* Specific message type proxies a call from one smart contract to another smart contract.
* The process works as a chain of calls. A smart contract call is relayed through a transaction to our router (a proxying smart contract that serves as an execution table of tables).
* The initial smart contract call is sent to the router. This router serves as an intermediary that ensures the call is directed to the correct destination.
* Our collator node picks up the transaction from the router. The collator then routes the transaction to the appropriate destination, ensuring the integrity and security of the call.
* The chain of calls is executed in a manner similar to how an ordinary smart contract would function. Each call in the chain is processed sequentially, ensuring that all conditions and parameters are met.
