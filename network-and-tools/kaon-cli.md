---
description: >-
  The Kaon Command Line Interface (CLI) provides developers with direct access
  to the Kaon blockchain's functionality. This reference guide covers essential
  commands, usage, and practical applications.
---

# Kaon CLI

## Getting Started

#### Installation & Setup

The `kaon-cli` tool is included with the Kaon node software. Before using these commands, ensure:

1. Your Kaon node is fully synced
2. You have proper permissions to execute CLI commands
3. Your wallet is properly configured and encrypted

#### Basic Syntax

All commands follow this general pattern:

```bash
kaon-cli <command> [parameters]
```

For help with any command:

```bash
kaon-cli help <command>
```

***

### Command Name: abandontransaction

### Synopsis:

`kaon-cli abandontransaction "txid"`

### Description:

Marks an in-wallet transaction specified by its `txid` as abandoned. This action allows the inputs of the abandoned transaction and its descendants to be respent. It's particularly useful for dealing with "stuck" or evicted transactions. The command only affects transactions not yet included in a block and not currently in the mempool. It has no effect on transactions that are already conflicted or abandoned.

### Arguments:

* **Name:** txid
* **Type:** string
* **Description:** The ID of the transaction to be abandoned.
* **Required/Optional:** Required

### Input:

The command accepts the transaction ID as a string argument.

### Output:

The command doesn't return a specific result upon successful execution.

### Examples:

**CLI Usage:**

```bash
kaon-cli abandontransaction "1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "abandontransaction", "params": ["1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors might occur if the provided `txid` is invalid, the transaction is already included in a block, or the transaction is currently in the mempool. Specific error messages are not detailed in the provided reference.

### Notes:

This command modifies the wallet's internal state. Ensure the provided `txid` is correct before executing the command.

***

### Command Name: addmultisigaddress

### Synopsis:

`kaon-cli addmultisigaddress nrequired ["key",...] ("account")`

### Description:

Adds a multisignature address to the wallet, requiring `nrequired` signatures out of the provided keys. Each key can be a Kaon address or a hex-encoded public key. The `account` parameter is deprecated and should not be used.

### Arguments:

* **Name:** nrequired
* **Type:** numeric
* **Description:** The number of required signatures.
* **Required/Optional:** Required
* **Name:** keysobject
* **Type:** string (JSON array)
* **Description:** A JSON array of Kaon addresses or hex-encoded public keys.
* **Required/Optional:** Required
* **Name:** account
* **Type:** string
* **Description:** Deprecated. Do not use.
* **Required/Optional:** Optional

### Input:

The command accepts the number of required signatures, a JSON array of keys, and an optional (deprecated) account name.

### Output:

Returns the generated Kaon multisignature address as a string.

### Examples:

**CLI Usage:**

```bash
kaon-cli addmultisigaddress 2 "[\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\",\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\"]"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "addmultisigaddress", "params": [2, "[\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\",\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\"]"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors might occur if the provided keys are invalid, `nrequired` is out of range, or there's an issue with wallet access. Specific error messages are not detailed in the provided reference.

### Notes:

This command modifies the wallet's state by adding a new multisignature address.

***

### Command Name: addnode

### Synopsis:

`kaon-cli addnode "node" "add|remove|onetry"`

### Description:

Manages the list of nodes known to the client. It allows adding a new node, removing an existing node, or attempting a one-time connection.

### Arguments:

* **Name:** node
* **Type:** string
* **Description:** The IP address and port of the node (e.g., "192.168.0.6:9871").
* **Required/Optional:** Required
* **Name:** command
* **Type:** string
* **Description:** 'add' to add the node permanently, 'remove' to remove it, or 'onetry' for a single connection attempt.
* **Required/Optional:** Required

### Input:

The command accepts the node's address and the desired action as string arguments.

### Output:

The command doesn't return a specific result upon successful execution. The output of `getpeerinfo` can be used to check the node list.

### Examples:

**CLI Usage:**

```bash
kaon-cli addnode "192.168.0.6:9871" "onetry"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "addnode", "params": ["192.168.0.6:9871", "onetry"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors may occur if the provided node address is invalid or if the command is unable to connect to the node. Specific error messages are not detailed in the provided reference.

### Notes:

Nodes added with `onetry` are not persistent and won’t appear in the list of added nodes retrieved via `getaddednodeinfo`. `getpeerinfo` can be used to see currently connected peers.

***

### Command Name: autocombinerewards

### Synopsis:

`kaon-cli autocombinerewards enable (threshold)`

### Description:

Enables or disables the automatic combining of rewards below a specified threshold. This helps manage UTXOs (Unspent Transaction Outputs) by consolidating smaller reward amounts into larger ones. Because this involves creating a transaction on the blockchain, network transaction fees will apply.

### Arguments:

* **Name:** enable
* **Type:** boolean
* **Description:** `true` to enable automatic reward combining, `false` to disable.
* **Required/Optional:** Required
* **Name:** threshold
* **Type:** numeric
* **Description:** The threshold amount below which rewards will be combined.
* **Required/Optional:** Optional (default: 0)

### Input:

The command accepts a boolean value to enable/disable the feature and an optional numeric threshold.

### Output:

The command doesn't provide a specific output upon successful execution.

### Examples:

**CLI Usage:**

```bash
kaon-cli autocombinerewards true 500
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "autocombinerewards", "params": [true, 500] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors might occur if the provided threshold is invalid or the wallet cannot create the necessary transactions. Specific error messages are not provided in the reference.

### Notes:

The default threshold of 0 means that all eligible rewards will be combined. Setting a higher threshold will only combine rewards below that value. Be mindful of transaction fees when setting the threshold.

***

### Command Name: backupwallet

### Synopsis:

`kaon-cli backupwallet "destination"`

### Description:

Creates a backup copy of the wallet data file (`wallet.dat`) to the specified destination. This destination can be a file path or a directory.

### Arguments:

* **Name:** destination
* **Type:** string
* **Description:** The path to the backup file or directory.
* **Required/Optional:** Required

### Input:

The command takes the backup destination as a string argument.

### Output:

The command doesn't produce a specific output on success.

### Examples:

**CLI Usage:**

```bash
kaon-cli backupwallet "backup.dat"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "backupwallet", "params": ["backup.dat"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors may arise if the destination is inaccessible or if there's an issue reading the wallet data. The reference does not provide detailed error messages.

### Notes:

Ensure the backup destination is secure and accessible when needed to restore the wallet. It's highly recommended to regularly back up your wallet.

***

### Command Name: bip38decrypt

### Synopsis:

`kaon-cli bip38decrypt "kaonaddress" "passphrase"`

### Description:

Decrypts a BIP38-encrypted private key and imports it into the wallet. BIP38 is a standard for encrypting private keys with a passphrase, adding a layer of security.

### Arguments:

* **Name:** encryptedkey
* **Type:** string
* **Description:** The BIP38-encrypted private key.
* **Required/Optional:** Required
* **Name:** passphrase
* **Type:** string
* **Description:** The passphrase used to encrypt the private key.
* **Required/Optional:** Required

### Input:

The command requires the encrypted private key and the associated passphrase.

### Output:

Returns the decrypted private key as a string.

### Examples:

**CLI Usage:**

```bash
kaon-cli bip38decrypt "encryptedkey" "mypassphrase"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "bip38decrypt", "params": ["encryptedkey", "mypassphrase"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors can occur if the provided key is not a valid BIP38-encrypted key, the passphrase is incorrect, or if there are issues importing the decrypted key into the wallet. Specific error messages are not detailed.

### Notes:

The decrypted private key is sensitive information. Handle it with care and avoid storing it insecurely.

***

### Command Name: bip38encrypt

### Synopsis:

`kaon-cli bip38encrypt "kaonaddress" "passphrase"`

### Description:

Encrypts a private key corresponding to the provided Kaon address using BIP38 encryption. The wallet must already contain the private key for the specified address. Valid passphrase special characters: !#$%&'()\*+,-./:;<=>?\`{|}\~

### Arguments:

* **Name:** kaonaddress
* **Type:** string
* **Description:** The Kaon address for which the corresponding private key will be encrypted.
* **Required/Optional:** Required
* **Name:** passphrase
* **Type:** string
* **Description:** The passphrase used to encrypt the private key.
* **Required/Optional:** Required

### Input:

The command accepts the Kaon address and the encryption passphrase as string arguments.

### Output:

Returns the BIP38-encrypted private key as a string.

### Examples:

**CLI Usage:**

```bash
kaon-cli bip38encrypt "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" "mypasphrase"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "bip38encrypt", "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6", "mypasphrase"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors can occur if the address is invalid, the wallet doesn't contain the private key for the address, or if the encryption process fails. Detailed error messages are not provided.

### Notes:

Store the encrypted key and passphrase securely. Losing either will result in the loss of access to the associated funds. BIP38 encryption significantly enhances the security of private keys.

***

### Command Name: callcontract

### Synopsis:

`kaon-cli callcontract <address> <data> (<senderAddress> <gasLimit> <amount>)`

### Description:

Calls a contract method offline, allowing you to test contract interactions without broadcasting a transaction to the network. Provides execution details and a simulated transaction receipt. Note that the "amount" parameter is unused according to the provided documentation.

### Arguments:

* **Name:** address
* **Type:** string (hex)
* **Description:** The contract address.
* **Required/Optional:** Required
* **Name:** data
* **Type:** string (hex)
* **Description:** The data to be sent to the contract method as a hex string.
* **Required/Optional:** Required
* **Name:** senderAddress
* **Type:** string
* **Description:** The sender address.
* **Required/Optional:** Optional, named
* **Name:** gasLimit
* **Type:** numeric
* **Description:** The gas limit for executing the contract.
* **Required/Optional:** Optional, named
* **Name:** amount
* **Type:** numeric
* **Description:** Unused. Do not use.
* **Required/Optional:** Optional, named

### Input:

Accepts the contract address, the data (hex string), the sender address, the gas limit, and an unused amount parameter.

### Output:

Returns a JSON object containing:

* `address`: The contract address.
* `executionResult`: An object detailing the execution results including gas used, exceptions, new contract address (if created), output data, and deposit-related information.
* `transactionReceipt`: A simulated transaction receipt including state root hash, gas used, bloom filter, and logs generated by the contract call.

### Examples:

**CLI Usage:**

```bash
kaon-cli callcontract eb23c0b3e6042821da281a2e2364feb22dd543e3 06fdde03
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "callcontract", "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3", "06fdde03"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors may occur if the contract address is invalid, the data is malformed, the gas limit is insufficient, or if there are other issues during the contract call. The provided reference lacks specific error message details.

### Notes:

The command executes the contract call offline, so the results are not reflected on the blockchain. The "amount" parameter, while present, is explicitly marked as unused in the provided documentation.

***

### Command Name: checkbudgets

### Synopsis:

`kaon-cli checkbudgets`

### Description:

Manually initiates a budget check cycle. This allows for on-demand verification and processing of budget proposals and payments.

### Input:

The command does not take any arguments.

### Output:

The command doesn't have a specified output upon successful completion.

### Examples:

**CLI Usage:**

```bash
kaon-cli checkbudgets
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "checkbudgets", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors might be related to the budget processing cycle. The provided reference does not offer specifics on error messages.

### Notes:

This command can be useful for debugging budget-related issues or for forcing budget updates.

***

### Command Name: clearbanned

### Synopsis:

`kaon-cli clearbanned`

### Description:

Clears all banned IP addresses from the node's ban list. This allows previously banned clients to reconnect.

### Input:

This command does not require any arguments.

### Output:

The command does not produce a specific output on successful completion.

### Examples:

**CLI Usage:**

```bash
kaon-cli clearbanned
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "clearbanned", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Potential errors might involve permission issues or problems accessing the ban list. Error details are not provided.

### Notes:

Use with caution as it removes all bans, potentially allowing malicious actors to reconnect. `listbanned` can be used to view the current ban list before clearing it.

***

### Command Name: createcontract

### Synopsis:

`kaon-cli createcontract <bytecode> (<gasLimit> <gasPrice> <senderaddress> <broadcast> <changeToSender>)`

### Description:

Creates a new smart contract on the Kaon blockchain using the provided bytecode. Offers options for specifying gas limit, gas price, sender address, broadcast behavior, and change handling.

### Arguments:

* **Name:** bytecode
* **Type:** string (hex)
* **Description:** The compiled bytecode of the contract.
* **Required/Optional:** Required
* **Name:** gasLimit
* **Type:** numeric (Amount)
* **Description:** The maximum amount of gas to use for contract deployment (default: `DEFAULT_GAS_LIMIT_OP_CREATE`).
* **Required/Optional:** Optional
* **Name:** gasPrice
* **Type:** numeric (Amount)
* **Description:** The gas price in KAON per gas unit.
* **Required/Optional:** Optional
* **Name:** senderaddress
* **Type:** string (hex)
* **Description:** The Kaon address that will deploy the contract.
* **Required/Optional:** Optional
* **Name:** broadcast
* **Type:** boolean
* **Description:** Whether to broadcast the contract creation transaction (default: true).
* **Required/Optional:** Optional
* **Name:** changeToSender
* **Type:** boolean
* **Description:** Whether to return any remaining KAON (change) to the sender address (default: true).
* **Required/Optional:** Optional

### Input:

The command requires the contract bytecode and optionally accepts gas limit, gas price, sender address, broadcast flag, and change-to-sender flag.

### Output:

Returns a JSON array containing an object with the following:

* `txid`: The transaction ID of the contract creation transaction.
* `sender`: The sender's KAON address.
* `hash160`: The RIPEMD-160 hash of the sender's address.
* `address`: The expected address of the deployed contract.

### Examples:

**CLI Usage:**

```bash
kaon-cli createcontract "60606040525b33600060006101000a81548173ffffffffffffffffffffffffffffffffffffffff02191690836c010000000000000000000000009081020402179055506103786001600050819055505b600c80605b6000396000f360606040526008565b600256"
kaon-cli createcontract "60606040525b33600060006101000a81548173ffffffffffffffffffffffffffffffffffffffff02191690836c010000000000000000000000009081020402179055506103786001600050819055505b600c80605b6000396000f360606040526008565b600256" 6000000 40 "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" true true
```

### Error Handling:

Errors may occur if the bytecode is invalid, gas limits are exceeded, the sender has insufficient funds, or if broadcasting the transaction fails. Error messages are not detailed.

### Notes:

Contract creation consumes gas. The `gasLimit` and `gasPrice` arguments help manage the cost of deployment. If `broadcast` is false, the transaction is not sent to the network, allowing for offline testing. The `changeToSender` flag affects how any remaining KAON from the transaction is handled.

***

### Command Name: createmasternodebroadcast

### Synopsis:

`kaon-cli createmasternodebroadcast "command" ("alias")`

### Description:

Creates and returns a masternode broadcast message. This message is necessary for announcing a masternode to the network and enabling its participation in the consensus process. It can be created for a specific masternode identified by its alias, or for all configured masternodes.

### Arguments:

* **Name:** command
* **Type:** string
* **Description:** "alias" for a single masternode, or "all" for all masternodes defined in `masternode.conf`.
* **Required/Optional:** Required
* **Name:** alias
* **Type:** string
* **Description:** The alias of the masternode (required when `command` is "alias"). The alias should correspond to an entry in the `masternode.conf` file.
* **Required/Optional:** Required if command is "alias"

### Input:

The command takes the desired command ("alias" or "all") and, if applicable, the masternode alias.

### Output:

* **If command is "all":** Returns a JSON object with an "overall" status message and a "detail" array containing individual broadcast results for each masternode. Each element in the "detail" array includes the alias, success status, hex-encoded broadcast message (if successful), and any error messages.
* **If command is "alias":** Returns a JSON object with the alias, success status, hex-encoded broadcast message (if successful), and any error messages.

### Examples:

**CLI Usage:**

```bash
kaon-cli createmasternodebroadcast alias mymn1
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "createmasternodebroadcast", "params": ["alias", "mymn1"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors can occur if the specified alias is invalid, the masternode configuration is incorrect, or if there are network issues. Detailed error descriptions are returned within the result JSON.

### Notes:

The generated broadcast message needs to be relayed to the network using the `relaymasternodebroadcast` command (or equivalent methods) for the masternode to be recognized. The `masternode.conf` file contains the necessary configuration details for each masternode.

***

### Command Name: createmasternodekey

### Synopsis:

`kaon-cli createmasternodekey`

### Description:

Generates a new private key specifically for use with a masternode. This key is essential for masternode operation and should be kept securely.

### Input:

This command doesn't require any arguments.

### Output:

Returns the newly generated masternode private key as a string.

### Examples:

**CLI Usage:**

```bash
kaon-cli createmasternodekey
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "createmasternodekey", "params": [] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors might be related to key generation or wallet access. Specific errors are not described in the given reference.

### Notes:

Securely store the generated private key. Loss of this key can lead to loss of control over the associated masternode and its collateral.

***

### Command Name: createmasternodev2

### Synopsis:

`kaon-cli createmasternodev2 "alias" "127.0.0.1:9871"`

### Description:

Attempts to set up and start a masternode using provided arguments. It initiates the process by sending a transaction with the required collateral (10,000 KAON) to the specified address, then retrieves the transaction output and prompts for inclusion in `masternode.conf`.

### Arguments:

* **Name:** privkey
* **Type:** string
* **Description:** The masternode's private key.
* **Required/Optional:** Required
* **Name:** alias
* **Type:** string
* **Description:** The label or alias for the masternode. It will be used as an identifier in the `masternode.conf` file.
* **Required/Optional:** Required
* **Name:** address
* **Type:** string
* **Description:** The IP address and port of the masternode.
* **Required/Optional:** Required

### Input:

The command takes the masternode's private key, alias, and IP:Port as string arguments.

### Output:

Returns a JSON object containing:

* `success`: A boolean indicating whether the masternode setup process succeeded.
* `result`: A message describing the status of the masternode creation.

### Examples:

The documentation does not provide any examples. Please ask the developers for this information.

### Error Handling:

Errors can occur if the provided information is invalid, there are insufficient funds for the collateral transaction, or there are issues communicating with the network. Specific error messages are not provided in the original reference.

### Notes:

This command automates part of the masternode setup. After running this, the output data must be manually added to `masternode.conf` for the masternode to function.

***

### Command Name: createmultisig

### Synopsis:

`kaon-cli createmultisig nrequired ["key",...]`

### Description:

Creates a multi-signature address requiring `nrequired` signatures out of the provided list of keys. This address can be used to receive funds that can only be spent with the required number of signatures.

### Arguments:

* **Name:** nrequired
* **Type:** numeric
* **Description:** The minimum number of signatures required to spend funds sent to this address.
* **Required/Optional:** Required
* **Name:** keys
* **Type:** string (JSON array)
* **Description:** A JSON array of Kaon addresses or hex-encoded public keys. These keys represent the participants authorized to sign transactions.
* **Required/Optional:** Required

### Input:

The command takes the number of required signatures and a JSON array of keys as arguments.

### Output:

Returns a JSON object containing:

* `address`: The newly generated multi-signature address.
* `redeemScript`: The hex-encoded redemption script that defines the spending conditions for this multi-signature address.

### Examples:

**CLI Usage:**

```bash
kaon-cli createmultisig 2 "[\"16sSauSf5pF2UkUwvKGq4qjNRzBZYqgEL5\",\"171sgjn4YtPu27adkKGrdDwzRTxnRkBfKV\"]"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "createmultisig", "params": [2, "[\"16sSauSf5pF2UkUwvKGq4qjNRzBZYqgEL5\",\"171sgjn4YtPu27adkKGrdDwzRTxnRkBfKV\"]"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors may occur if the provided keys are invalid, the required number of signatures is out of range, or there's an issue generating the multi-signature address. Specific errors and their corresponding messages are not detailed in the provided reference.

### Notes:

The `redeemScript` is crucial for spending funds sent to this address. Keep it secure as it contains the logic that defines the multi-signature conditions.

***

### Command Name: createrawtransaction

### Synopsis:

`kaon-cli createrawtransaction [{"txid":"id","vout":n},...] {"address":amount,...} (locktime)`

### Description:

Creates a raw, unsigned transaction. This command constructs the basic structure of a transaction based on specified inputs (UTXOs) and outputs (addresses and amounts), without signing it or broadcasting it to the network.

### Arguments:

* **Name:** transactions
* **Type:** string (JSON array)
* **Description:** A JSON array of objects, each representing a transaction input (UTXO). Each UTXO object must contain the `txid` and `vout` fields and optionally a `sequence` field.
* **Required/Optional:** Required
* **Name:** addresses
* **Type:** string (JSON object)
* **Description:** A JSON object specifying the transaction outputs. Keys are Kaon addresses, and values are the corresponding amounts.
* **Required/Optional:** Required
* **Name:** locktime
* **Type:** numeric
* **Description:** Sets the `locktime` field of the transaction. A non-zero value prevents the transaction from being included in a block until the specified block height or time (depending on transaction version). Default is 0.
* **Required/Optional:** Optional (default: 0)

### Input:

The command takes two JSON strings as arguments: one for inputs (UTXOs) and another for outputs (addresses/amounts). It also optionally accepts a numeric `locktime`.

### Output:

Returns the hex-encoded raw transaction as a string.

### Examples:

**CLI Usage:**

```bash
kaon-cli createrawtransaction "[{\"txid\":\"myid\",\"vout\":0}]" "{\"address\":0.01}"
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc": "1.0", "id":"curltest", "method": "createrawtransaction", "params": ["[{\"txid\":\"myid\",\"vout\":0}]", "{\"address\":0.01}"] }' -H 'content-type: text/plain;' http://127.0.0.1:51473/
```

### Error Handling:

Errors might occur if the input JSON is malformed, the referenced UTXOs are invalid or spent, or the output addresses are incorrect. Specific error information is not available in the provided documentation.

### Notes:

The created transaction is unsigned and needs to be signed using `signrawtransactionwithkey` or `signrawtransactionwithwallet` before being broadcasted to the network with `sendrawtransaction`. Setting a non-zero `locktime` also locktime-activates inputs.

***

### Command Name: createwallet

### Synopsis:

`kaon-cli createwallet <passphrase>`

### Description:

Creates and loads a new wallet. This shuts down the server. All other arguments specified in the documentation are marked as UNUSED.

### Arguments:

* **Name:** passphrase
* **Type:** string
* **Description:** The passphrase to encrypt the new wallet with.
* **Required/Optional:** Required

### Input:

The command requires a passphrase as a string argument. All other arguments originally listed are unused.

### Output:

The command doesn't return a specific value upon success. The server will shut down after creating the wallet.

### Examples:

The original reference does not provide any examples, although the description of the `encryptwallet` command contains some `createwallet` context:

```
> kaon-cli createwallet "my pass phrase"
```

### Error Handling:

Errors can occur if there are issues creating or loading the wallet, or problems with the server shutdown process. Detailed error information is not provided.

### Notes:

This command shuts down the server. After creating the wallet, you need to restart the server to use it. Ensure you have a secure passphrase.

***

### Command Name: decodemasternodebroadcast

### Synopsis:

`kaon-cli decodemasternodebroadcast "hexstring"`

### Description:

Decodes a hex-encoded masternode broadcast message (MNB), revealing the information contained within. Useful for verifying the contents of an MNB and for debugging masternode-related issues.

### Arguments:

* **Name:** hexstring
* **Type:** string
* **Description:** The hex-encoded masternode broadcast message.
* **Required/Optional:** Required

### Input:

Accepts the hex-encoded MNB string as an argument.

### Output:

Returns a JSON object containing the decoded information from the MNB:

* `vin`: The unspent transaction output (UTXO) used as collateral for the masternode.
* `addr`: The IP address of the masternode.
* `pubkeycollateral`: The public key of the collateral address.
* `pubkeymasternode`: The masternode's public key.
* `vchsig`: The Base64-encoded signature of the MNB.
* `sigtime`: The timestamp of the signature.
* `sigvalid`: A boolean ("true"/"false") indicating if the signature is valid.
* `protocolversion`: The masternode's protocol version.
* `nlastdsq`: Deprecated field related to mixing.
* `nMessVersion`: The MNB message version number.
* `lastping`: A JSON object containing information about the masternode's last ping, including the signing UTXO, block hash, signature time, validity, signature, and message version.

### Examples:

**CLI Usage:**

```bash
kaon-cli decodemasternodebroadcast <hexstring>
```

**JSON-RPC Call:**

```bash
curl --user myusername --data-binary '{"jsonrpc
```

## Additional Resources

* [Kaon Documentation](https://docs.kaon.one)
* [Developer Portal](https://dev.kaon.one)
* [API Reference](https://api.kaon.one)
* [GitHub Repository](https://github.com/kaon-network)

## Contributing

For contributing to this documentation or reporting issues:

1. Submit issues on GitHub
2. Follow our contribution guidelines
3. Join our developer community
