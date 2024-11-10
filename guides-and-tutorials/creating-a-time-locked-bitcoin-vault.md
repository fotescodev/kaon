# Creating a Time-Locked Bitcoin Vault

## Guide **Objective**

A developer wants to:

* **Lock BTC** on the Bitcoin network.
* **Mirror** the locked BTC as **mirrorBTC** on the Kaon network.
* **Create a smart contract** that enforces a **time-locked covenant** using mirrorBTC.
* **Use a voucher** to represent the mirroring event and track the locked BTC.
* **Redeem** the mirrorBTC back to BTC through a **peg-out** process after the time-lock expires.

***

## **Workflow Steps**

#### **Set Up the Development Environment**

**Install and Configure Bitcoin Core**

*   **Install Bitcoin Core**

    Download and install Bitcoin Core from the official website: [bitcoin.org](https://bitcoin.org/en/download)
*   **Configure Bitcoin Core**

    Edit the `bitcoin.conf` file to enable RPC and set appropriate settings.

    ```ini
    server=1
    rpcuser=yourusername
    rpcpassword=yourpassword
    txindex=1
    ```

**Install and Configure Kaon Node**

*   **Clone the Kaon Repository**

    ```bash
    git clone https://github.com/kaon-blockchain/kaon.git
    ```
*   **Build Kaon from Source**

    Follow the instructions in the `README.md` to build Kaon.
*   **Run Kaon Node**

    ```bash
    src/kaond -daemon
    ```
*   **Configure Kaon Node**

    Edit the `kaon.conf` file with necessary settings.

    ```ini
    server=1
    rpcuser=yourusername
    rpcpassword=yourpassword
    ```

**Set Up MetaMask with Kaon Testnet**

*   **Install MetaMask**

    Download from [metamask.io](https://metamask.io/).
* **Add Kaon Testnet Network**
  * **Network Name**: Kaon Testnet
  * **New RPC URL**: `https://testnet.kaon.one`
  * **Chain ID**: `11989`
  * **Currency Symbol**: `KAON`
  * **Block Explorer URL**: `https://explorer.testnet.kaon.one`

***

#### **Perform a Peg-In (Mirror BTC to mirrorBTC)**

**Request a Peg-In Address**

Use the Kaon CLI or API to request a peg-in address.

```bash
kaon-cli getpeginaddress
```

_Example Output_:

```json
{
  "address": "bc1qxyz...",
  "redeem_script": "512102..."
}
```

**Send BTC to the Peg-In Address**

Use your Bitcoin wallet or `bitcoin-cli` to send BTC to the provided address.

```bash
bitcoin-cli sendtoaddress "bc1qxyz..." 0.001
```

**Wait for Transaction Confirmation**

Wait for at least 6 confirmations on the Bitcoin network to ensure the transaction is securely recorded.

**Kaon Network Detection**

Kaon's sMPC participants monitor the Bitcoin network. Once they detect and verify your transaction:

* **mirrorBTC is minted** on the Kaon network.
* **Voucher NFT is issued** representing the peg-in event.

**Verify Receipt of mirrorBTC**

Check your MetaMask wallet on the Kaon Testnet to confirm the mirrorBTC balance.

***

#### **Create a Smart Contract with a Time-Locked Covenant**

**Write the Solidity Smart Contract**

Create a new file `TimeLockedVault.sol` in Remix IDE.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IERC20 {
    function transfer(address recipient, uint256 amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
}

contract TimeLockedVault {
    IERC20 public mirrorBTC;
    address public owner;
    uint256 public releaseTime;

    constructor(IERC20 _mirrorBTC, uint256 _releaseTime) {
        require(_releaseTime > block.timestamp, "Release time must be in the future");
        mirrorBTC = _mirrorBTC;
        owner = msg.sender;
        releaseTime = _releaseTime;
    }

    function deposit(uint256 amount) public {
        require(msg.sender == owner, "Only owner can deposit");
        require(mirrorBTC.transferFrom(msg.sender, address(this), amount), "Transfer failed");
    }

    function withdraw() public {
        require(msg.sender == owner, "Only owner can withdraw");
        require(block.timestamp >= releaseTime, "Current time is before release time");
        uint256 amount = mirrorBTC.balanceOf(address(this));
        require(mirrorBTC.transfer(owner, amount), "Transfer failed");
    }
}
```

**Compile the Contract**

* Set the compiler version to `0.8.0` or compatible.
* Ensure there are no compilation errors.

**Deploy the Contract**

* **Environment**: Injected Web3 (MetaMask).
* **Contract**: `TimeLockedVault`.
* **Constructor Parameters**:
  * **mirrorBTC Address**: Input the mirrorBTC token contract address.
  * **releaseTime**: Unix timestamp of when the funds can be withdrawn (e.g., `block.timestamp + 86400` for 24 hours later).
* **Deploy**: Confirm the transaction in MetaMask.

**Add mirrorBTC Token to MetaMask**

* **Token Contract Address**: Add the mirrorBTC token address to MetaMask to see your balance.

***

#### **Interact with the Smart Contract**

**Deposit mirrorBTC into the Contract**

Before depositing, you need to approve the contract to spend your mirrorBTC.

*   **Approve Transfer**:

    ```javascript
    mirrorBTC.approve(contractAddress, amount);
    ```
*   **Execute Deposit**:

    Call the `deposit` function from the deployed contract in Remix:

    * **Amount**: The amount of mirrorBTC you want to lock.
    * **Confirm**: Approve the transaction in MetaMask.

**Verify the Deposit**

* Call the `mirrorBTCBalance` function to ensure the funds are deposited.
* Alternatively, check the contract's balance in MetaMask or via the Kaon block explorer.

**Attempt Early Withdrawal (Should Fail)**

* Try calling the `withdraw` function before `releaseTime`.
* The transaction should revert with the error: `"Current time is before release time"`.

**Wait Until Release Time**

* Wait until the current block timestamp is equal to or greater than `releaseTime`.
* You can simulate time passage on a local blockchain, but on the testnet, you need to wait in real-time.

**Withdraw mirrorBTC after Time-Lock Expires**

* Call the `withdraw` function from the contract.
* Confirm the transaction in MetaMask.
* The mirrorBTC is transferred back to your wallet.

***

#### **Redeem the Voucher and Perform a Peg-Out (mirrorBTC to BTC)**

**Initiate Peg-Out Request**

Send your mirrorBTC to the designated peg-out address or smart contract provided by Kaon.

*   **Peg-Out Contract Address**: Obtain from Kaon's documentation or via:

    ```bash
    kaon-cli getpegoutaddress
    ```
*   **Transfer mirrorBTC**:

    In MetaMask or via a script:

    ```javascript
    mirrorBTC.transfer(pegOutContractAddress, amount);
    ```

**Burn mirrorBTC and Prepare BTC Transaction**

* The peg-out contract burns the mirrorBTC to prevent double-spending.
* The sMPC group prepares a Bitcoin transaction to send BTC back to your Bitcoin address.

**Provide Your Bitcoin Address**

* If required, provide your Bitcoin address to the peg-out contract during the transaction or via an interface.

**sMPC Group Signs the Transaction**

* The transaction is securely signed using Threshold Signature Scheme (TSS) by the sMPC participants.

**Broadcast the Transaction to the Bitcoin Network**

* The signed transaction is broadcasted to the Bitcoin network by the validators.

**Wait for Confirmation**

* Monitor the transaction on a Bitcoin block explorer.
* Wait for at least 6 confirmations to ensure the transaction is secure.

**Receive BTC**

* The BTC is deposited into your provided Bitcoin address.

**Voucher Redemption**

* The voucher NFT associated with the mirrorBTC is marked as redeemed.
* You can verify this by checking the voucher's status in an NFT explorer or your wallet.

***

#### **Verify the Entire Process**

**Check Transaction Histories**

* **Bitcoin Network**:
  * Use a Bitcoin block explorer like [blockchain.com](https://www.blockchain.com/explorer) or [blockstream.info](https://blockstream.info/) to view your BTC transactions.
* **Kaon Network**:
  * Use the Kaon block explorer at [explorer.testnet.kaon.one](https://explorer.testnet.kaon.one) to view your mirrorBTC transactions.

**Verify Voucher Status**

* **View Voucher Details**:
  * Use an NFT-compatible wallet or an explorer that supports Kaon NFTs.
  * Check the metadata to confirm the voucher is marked as redeemed.

***

## **Detailed Explanations and Code Snippets**

### **Understanding mirrorBTC**

#### **What is mirrorBTC?**

mirrorBTC is a token on the Kaon network that represents BTC locked on the Bitcoin network. It allows users to leverage their BTC within the Kaon ecosystem, enabling interactions with EVM-compatible smart contracts and decentralized applications.

#### **Why use mirrorBTC?**

* **Liquidity**: Utilize BTC liquidity in DeFi applications on Kaon.
* **Interoperability**: Interact with Ethereum-based tools while holding BTC value.
* **Security**: Benefit from Kaon's security mechanisms without relying on centralized bridges.

## **Working with Covenants**

### **What is a Covenant?**

A covenant imposes conditions or restrictions on how and when funds can be spent in the future, enhancing security and enabling complex financial instruments.

#### **Implementing a Time-Locked Covenant**

In the `TimeLockedVault` contract, the covenant is enforced through a time lock:

```solidity
require(block.timestamp >= releaseTime, "Current time is before release time");
```

This line ensures that the `withdraw` function can only be executed after the specified `releaseTime`.

### **Utilizing Vouchers**

#### **What is a Voucher?**

A voucher in Kaon is an NFT that represents a specific peg-in event. It tracks the locked BTC and provides proof of ownership and status of the mirrored assets.

#### **Accessing Voucher Details**

* **Via Wallet**: Use an NFT-compatible wallet that supports the Kaon network to view your vouchers.
* **Metadata**: Vouchers contain metadata such as `tokenId`, `amount`, `status`, and `related transactions`.

**Example Voucher Metadata**

```json
{
  "tokenId": "1",
  "amount": "0.001 BTC",
  "pegInTransactionId": "txid1234...",
  "status": "Redeemed"
}
```

### **Handling Approvals and Transfers in Smart Contracts**

#### **Approving Token Transfers**

Before a smart contract can transfer tokens on your behalf, you must grant it permission:

```javascript
await mirrorBTC.approve(contractAddress, amount);
```

#### **Transferring Tokens within Contracts**

Use the `transferFrom` method to move tokens from the user's address to the contract:

```solidity
require(mirrorBTC.transferFrom(msg.sender, address(this), amount), "Transfer failed");
```

### **Interacting with Kaon CLI**

#### **Getting a Peg-In Address**

```bash
kaon-cli getpeginaddress
```

#### **Getting a Peg-Out Address**

```bash
kaon-cli getpegoutaddress
```

**Checking mirrorBTC Balance**

```bash
kaon-cli getbalance "<your Kaon address>"
```

***

### **Additional Tips**

#### **Test on Testnet First**

Always perform operations on the Kaon Testnet to familiarize yourself with the processes and avoid potential loss of funds.

#### **Secure Your Private Keys**

* Use secure wallets and consider hardware wallets for storing private keys.
* Never share your private keys or seed phrases.

#### **Stay Updated**

* Regularly check Kaon's official channels for updates:
  * **Website**: [kaon.one](https://kaon.one)
  * **Documentation**: [docs.kaon.one](https://docs.kaon.one)
  * **GitHub**: [github.com/kaon-blockchain](https://github.com/kaon-blockchain)

#### **Use Appropriate Gas Fees**

Ensure that you set sufficient gas limits and gas prices for transactions on the Kaon network to avoid failures or delays.

#### **Monitor Transaction Status**

* **Kaon Transactions**: Use the Kaon block explorer to monitor the status of your mirrorBTC and smart contract transactions.
* **Bitcoin Transactions**: Use a Bitcoin block explorer to track your BTC transactions during peg-in and peg-out.

***

## **Conclusion**

By completing this use case, you have:

* Successfully **mirrored BTC to mirrorBTC** through a peg-in process.
* Created and interacted with a **time-locked vault** smart contract utilizing covenants.
* Managed and redeemed a **voucher** representing your mirrored assets.
* Performed a **peg-out** to convert mirrorBTC back to BTC on the Bitcoin network.

This practical example demonstrates how developers can leverage Kaon's unique features to build secure, decentralized financial applications that bridge the Bitcoin and Ethereum ecosystems without relying on centralized intermediaries.

***

## **References**

* **Kaon GitHub Repository**: [https://github.com/kaon-blockchain/kaon](https://github.com/kaon-blockchain/kaon)
* **Kaon Documentation**: [https://docs.kaon.one](https://docs.kaon.one)
* **Remix IDE**: [https://remix.ethereum.org](https://remix.ethereum.org)
* **Solidity Documentation**: [https://docs.soliditylang.org](https://docs.soliditylang.org)
* **MetaMask**: [https://metamask.io](https://metamask.io)
* **Bitcoin Core**: [https://bitcoin.org/en/download](https://bitcoin.org/en/download)
