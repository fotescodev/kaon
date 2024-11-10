# Metamask and other Offchain EVM Wallets Support



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

When a user sends a request from MetaMask or another EVM wallet, it undergoes a transformation process to ensure compatibility with the Kaon ecosystem:

* **Eth RPC Gate:** Transforms EVM wallet requests into Bitcoin RPC 1.26 type requests and sends them to the Kaon Node. It may also compose additional information as needed. Also it provides transaction information in Ethereum format, ensuring compatibility and ease of use for MetaMask and other EVM wallets.
* **WebSockets Subscriptions:** Provides WebSockets subscriptions for new events by wrapping ZMQ events from the Kaon node.

**Support and Processing**

* **Account Balance and Transaction Indexing:** Each Kaon Masternode has the Account Balance Index and Transaction Index turned on to support balance requests and transaction composition based on RLP (Recursive Length Prefix) data.
* **RLP Processing Module:** For RLP-type requests (sendrawtransaction calls such as sending money, deploying contracts, and executing contract methods), Kaon:
  1. Decodes the input.
  2. Validates the input and its parameters.
  3. Composes a UTXO transaction based on the input.
  4. Signs the transaction using Cascade Signature with the input RLP transaction as its basis.
* **Denomination Handling:** Despite being a Bitcoin fork, Kaon supports a denomination with 18 zeros for the native token to align with EVM standards.
* **Complete EVM Integration:** Kaon includes a complete EVM as part of its core.
* **Unified Gas Fees:** Kaon composes gas fees and network bitcoin-type fees (based on priorities) to present a single gas fee to the user, making it hidden from the user.
* **Gas Price and Transaction Estimation:** Methods for current gas price and gas transaction estimation work seamlessly to support EVM wallets.
