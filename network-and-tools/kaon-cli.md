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

## CLI Commands

### `abandontransaction`

Mark in-wallet transaction \<txid> as abandoned. This will mark this transaction and all its in-wallet descendants as abandoned which will allow for their inputs to be respent. It can be used to replace "stuck" or evicted transactions. It only works on transactions which are not included in a block and are not currently in the mempool. It has no effect on transactions which are already conflicted or abandoned.

#### Synopsis

```
abandontransaction <txid>
```

#### Description

Marks a specific transaction and its descendants as abandoned within the wallet. This allows the inputs of the abandoned transactions to be reused in new transactions. This command is useful for dealing with transactions that are "stuck" or have been evicted from the mempool. It's important to note that this command only affects transactions that haven't been confirmed in a block and are not currently in the mempool. It won't have any effect on transactions that are already marked as conflicted or abandoned.

#### Arguments

| Name | Type   | Required | Description                           |
| ---- | ------ | -------- | ------------------------------------- |
| txid | string | Yes      | The ID of the transaction to abandon. |

#### Flags

None

#### Input

The transaction ID (`txid`) as a string.

#### Output

None

#### Examples

CLI Usage:

```bash
abandontransaction "1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d"
```

JSON-RPC Call:

```json
{
  "method": "abandontransaction",
  "params": ["1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d"]
}
```

#### Error Handling

* If the transaction ID is invalid or doesn't exist in the wallet, an error message will be returned.
* If the transaction is already confirmed in a block or is currently in the mempool, the command will have no effect and may return an error or a warning.

#### Notes

Use this command with caution. Once a transaction is abandoned, its outputs become spendable again, so ensure you understand the implications before using this command.

***

### addmultisigaddress

Add a nrequired-to-sign multisignature address to the wallet. Each key is a Kaon address or hex-encoded public key. If 'account' is specified (DEPRECATED), assign address to that account.

#### Synopsis

```bash
addmultisigaddress <nrequired> <keysobject> (<account>)
```

#### Description

Adds a multisignature address to the wallet. This address requires `nrequired` signatures from the provided list of keys to authorize spending. Each key can be a Kaon address or a hex-encoded public key. The optional `account` parameter is deprecated and should not be used.

#### Arguments

| Name       | Type    | Required | Description                                                      |
| ---------- | ------- | -------- | ---------------------------------------------------------------- |
| nrequired  | numeric | Yes      | The number of required signatures.                               |
| keysobject | string  | Yes      | A JSON array of Kaon addresses or hex-encoded public keys.       |
| account    | string  | No       | **DEPRECATED.** An account to assign the address to (don't use). |

#### Flags

None

#### Input

* `nrequired`: An integer representing the number of required signatures.
* `keysobject`: A JSON string representing an array of Kaon addresses or hex-encoded public keys.
* `account`: (Optional, deprecated) A string representing the account name.

#### Output

A Kaon address associated with the provided keys.

#### Examples

CLI Usage:

```bash
addmultisigaddress 2 "[\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\",\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\"]"
```

JSON-RPC Call:

```json
{
  "method": "addmultisigaddress",
  "params": [2, "[\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\",\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\"]"]
}
```

#### Error Handling

* If `nrequired` is invalid (e.g., less than 1 or greater than the number of keys), an error is returned.
* If the `keysobject` is not a valid JSON array or contains invalid Kaon addresses or public keys, an error is returned.

#### Notes

Multisignature addresses enhance security by requiring multiple parties to authorize transactions.

***

### addnode

Attempts add or remove a node from the addnode list. Or try a connection to a node once.

#### Synopsis

```bash
addnode <node> <add|remove|onetry>
```

#### Description

Manages the list of nodes that the Kaon client attempts to connect to. You can use this command to add a new node, remove an existing node, or attempt a one-time connection to a specific node.

#### Arguments

| Name    | Type   | Required | Description                                                     |
| ------- | ------ | -------- | --------------------------------------------------------------- |
| node    | string | Yes      | The IP address and port of the node (e.g., "192.168.0.6:9871"). |
| command | string | Yes      | The action to perform: "add", "remove", or "onetry".            |

#### Flags

None

#### Input

* `node`: The IP address and port of the node as a string.
* `command`: The command to execute ("add", "remove", or "onetry") as a string.

#### Output

None

#### Examples

CLI Usage:

```bash
addnode "192.168.0.6:9871" "onetry"
```

JSON-RPC Call:

```json
{
  "method": "addnode",
  "params": ["192.168.0.6:9871", "onetry"]
}
```

#### Error Handling

* If the `node` format is invalid, an error will be returned.
* If the `command` is not one of "add", "remove", or "onetry", an error will be returned.
* If attempting to add a node that is already in the addnode list, or remove a node that is not in the list, an error or warning may be returned.

#### Notes

The `addnode` command affects the client's connection attempts but doesn't guarantee a successful connection. See `getpeerinfo` for a list of currently connected peers. Related commands: `getpeerinfo`, `disconnectnode`.

***

### autocombinerewards

Wallet will automatically monitor for any coins with value below the threshold amount, and combine them if they reside with the same Kaon address. When autocombinerewards runs it will create a transaction, and therefore will be subject to transaction fees.

#### Synopsis

```bash
autocombinerewards <enable> (<threshold>)
```

#### Description

Enables or disables the automatic combining of small coin outputs within the wallet. If enabled, the wallet will regularly check for outputs below the specified `threshold` and combine them into a single larger output. This helps to reduce the number of UTXOs in the wallet and can improve transaction efficiency. Note that combining outputs involves creating a new transaction, which incurs transaction fees.

#### Arguments

| Name      | Type    | Required | Description                                                  |
| --------- | ------- | -------- | ------------------------------------------------------------ |
| enable    | boolean | Yes      | Enable (true) or disable (false) automatic combining.        |
| threshold | numeric | No       | The minimum output value below which coins will be combined. |

#### Flags

None

#### Input

* `enable`: A boolean value (`true` or `false`).
* `threshold`: (Optional) A numeric value representing the threshold. Defaults to 0.

#### Output

None

#### Examples

CLI Usage:

```bash
autocombinerewards true 500
```

JSON-RPC Call:

```json
{
  "method": "autocombinerewards",
  "params": [true, 500]
}
```

#### Error Handling

* If `enable` is not a valid boolean, an error will be returned.
* If `threshold` is not a valid number, an error will be returned.

#### Notes

Transaction fees are deducted from the combined output. Combining outputs can help improve wallet performance, especially if you have many small UTXOs.

***

### backupwallet

Safely copies wallet.dat to destination, which can be a directory or a path with filename.

#### Synopsis

```bash
backupwallet <destination>
```

#### Description

Creates a backup of the wallet data file (`wallet.dat`). This backup can be used to restore the wallet in case of data loss or corruption. The `destination` can be either a directory or a full file path.

#### Arguments

| Name        | Type   | Required | Description                                       |
| ----------- | ------ | -------- | ------------------------------------------------- |
| destination | string | Yes      | The directory or file path for the wallet backup. |

#### Flags

None

#### Input

* `destination`: A string representing the backup destination.

#### Output

None

#### Examples

CLI Usage:

```bash
backupwallet "backup.dat"
```

JSON-RPC Call:

```json
{
  "method": "backupwallet",
  "params": ["backup.dat"]
}
```

#### Error Handling

* If the destination directory doesn't exist or is not writable, an error will be returned.
* If the wallet file cannot be read, an error will be returned.

#### Notes

Regularly backing up your wallet is essential for protecting your funds. Store backups securely and in multiple locations. Related commands: `dumpwallet`, `importwallet`

***

### bip38decrypt

Decrypts and then imports password protected private key.

#### Synopsis

```bash
bip38decrypt <kaonaddress> <passphrase>
```

#### Description

Decrypts a BIP38-encrypted private key and imports it into the wallet. BIP38 encryption provides a secure way to store private keys by encrypting them with a passphrase.

#### Arguments

| Name        | Type   | Required | Description                        |
| ----------- | ------ | -------- | ---------------------------------- |
| kaonaddress | string | Yes      | The encrypted private key.         |
| passphrase  | string | Yes      | The passphrase to decrypt the key. |

#### Flags

None

#### Input

* `kaonaddress`: The encrypted BIP38 private key as a string.
* `passphrase`: The passphrase used to encrypt the private key as a string.

#### Output

The decrypted private key.

#### Examples

CLI Usage:

```bash
bip38decrypt "6PRVWUbkzzsbcVac2qwfssoUJAN1Xhrg6bNk8J7Nzm5H7kxEbn2Nh2ZoGg" "mypassphrase"
```

JSON-RPC Call:

```json
{
  "method": "bip38decrypt",
  "params": ["6PRVWUbkzzsbcVac2qwfssoUJAN1Xhrg6bNk8J7Nzm5H7kxEbn2Nh2ZoGg", "mypassphrase"]
}
```

#### Error Handling

* If the encrypted key is invalid or not a valid BIP38-encrypted key, an error message will be returned.
* If the passphrase is incorrect, decryption will fail, and an error will be returned.

#### Notes

After decrypting and importing the key, you can use it like any other private key in your wallet. Be sure to keep the decrypted private key secure. Related commands: `bip38encrypt`, `importprivkey`, `dumpprivkey`

***

### bip38encrypt

Encrypts a private key corresponding to 'kaonaddress'.

#### Synopsis

```bash
bip38encrypt <kaonaddress> <passphrase>
```

#### Description

Encrypts a private key corresponding to a specific Kaon address using BIP38 encryption. This allows you to securely store your private keys by protecting them with a passphrase. This command only works if you already hold the private key for the specified address in your wallet.

#### Arguments

| Name        | Type   | Required | Description                               |
| ----------- | ------ | -------- | ----------------------------------------- |
| kaonaddress | string | Yes      | The Kaon address associated with the key. |
| passphrase  | string | Yes      | The passphrase to encrypt the key with.   |

#### Flags

None

#### Input

* `kaonaddress`: The Kaon address for the private key to encrypt.
* `passphrase`: The passphrase to use for encryption. Valid special chars: !#$%&'()\*+,-./:;<=>?\`{|}\~

#### Output

The BIP38 encrypted private key.

#### Examples

CLI Usage:

```bash
bip38encrypt "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" "mypassphrase"
```

JSON-RPC Call:

```json
{
  "method": "bip38encrypt",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6", "mypassphrase"]
}

```

#### Error Handling

* If the address is invalid or doesn't exist in the wallet, an error is returned.
* If the wallet doesn't hold the private key for the specified address, an error is returned.

#### Notes

Store the encrypted private key securely. You will need the passphrase to decrypt and use the key later. Related commands: `bip38decrypt`, `importprivkey`, `dumpprivkey`

***

### callcontract

Call contract methods offline.

#### Synopsis

```bash
callcontract <address> <data> (<senderAddress> <gasLimit> <amount>)
```

#### Description

Calls a contract method offline, allowing you to test and debug contract interactions without broadcasting a transaction to the network. This command provides detailed information about the execution result and transaction receipt, including gas usage, returned data, and any exceptions thrown.

#### Arguments

| Name          | Type    | Required | Description                                           |
| ------------- | ------- | -------- | ----------------------------------------------------- |
| address       | string  | Yes      | The contract address (hexadecimal).                   |
| data          | string  | Yes      | The data hex string to pass to the contract method.   |
| senderAddress | string  | No       | The sender address string.                            |
| gasLimit      | numeric | No       | The gas limit for executing the contract.             |
| amount        | numeric | No       | UNUSED The amount in KAON to send. eg 0.1, default: 0 |

#### Flags

None

#### Input

* `address`: The contract address as a hexadecimal string.
* `data`: The data to send to the contract method as a hexadecimal string.
* `senderAddress`: (Optional) The sender's Kaon address.
* `gasLimit`: (Optional) The gas limit for the contract execution.
* `amount`: (Optional, UNUSED) The amount of KAON to send (default: 0).

#### Output

A JSON object containing the execution result and transaction receipt.

```json
{
  "address": "contract address",
  "executionResult": {
    "gasUsed": n,
    "excepted": "exception",
    "newAddress": "contract address",
    "output": "data",
    "codeDeposit": n,
    "gasRefunded": n,
    "depositSize": n,
    "gasForDeposit": n
  },
  "transactionReceipt": {
    "stateRoot": "hash",
    "gasUsed": n,
    "bloom": "bloom",
    "log": [
      {
        "address": "address",
        "topics": [
          "topic"
        ],
        "data": "data"
      }
    ]
  }
}
```

#### Examples

CLI Usage:

```bash
callcontract eb23c0b3e6042821da281a2e2364feb22dd543e3 06fdde03
```

JSON-RPC Call:

```json
{
  "method": "callcontract",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3", "06fdde03"]
}
```

#### Error Handling

* If the contract address is invalid, an error is returned.
* If the data hex string is invalid, an error is returned.
* If the gas limit is insufficient, an error is returned.
* If the contract execution throws an exception, the `excepted` field in the output will contain the exception message.

#### Notes

This command is helpful for testing contract functionality before deploying it or sending transactions on the mainnet. It simulates a transaction without actually broadcasting it.

***

### checkbudgets

Initiates a budget check cycle manually.

#### Synopsis

```bash
checkbudgets
```

#### Description

Manually triggers a budget check cycle. This cycle evaluates budget proposals and determines which proposals will be funded in the next payment cycle.

#### Arguments

None

#### Flags

None

#### Input

None

#### Output

None

#### Examples

CLI Usage:

```bash
checkbudgets
```

JSON-RPC Call:

```json
{
  "method": "checkbudgets",
  "params": []
}
```

#### Error Handling

Any errors encountered during the budget check cycle will be logged.

#### Notes

Manually checking budgets can be useful for testing or debugging the budget system. Related commands: `getbudgetinfo`, `getbudgetprojection`, `getbudgetvotes`, `preparebudget`, `submitbudget`

***

### clearbanned

Clear all banned IPs.

#### Synopsis

```bash
clearbanned
```

#### Description

Removes all IP addresses and subnets from the ban list. Banned IPs are prevented from connecting to your Kaon node.

#### Arguments

None

#### Flags

None

#### Input

None

#### Output

None

#### Examples

CLI Usage:

```bash
clearbanned
```

JSON-RPC Call:

```json
{
  "method": "clearbanned",
  "params": []
}
```

#### Error Handling

Any errors encountered while clearing the ban list will be logged.

#### Notes

Use with caution, as clearing the ban list might allow previously banned nodes to reconnect. Related commands: `setban`, `listbanned`

***

### createcontract

Create a contract with bytecode.

#### Synopsis

```bash
createcontract <bytecode> (<gasLimit> <gasPrice> <senderaddress> <broadcast> <changeToSender>)
```

#### Description

Creates a new smart contract on the Kaon blockchain using the provided bytecode. This command allows you to deploy your own smart contracts and interact with them. You can specify gas limit, gas price, sender address, whether to broadcast the transaction, and whether to return the change to the sender.

#### Arguments

| Name           | Type    | Required | Description                                                                     |
| -------------- | ------- | -------- | ------------------------------------------------------------------------------- |
| bytecode       | string  | Yes      | The contract bytecode (hexadecimal string).                                     |
| gasLimit       | numeric | No       | The gas limit for contract creation (default: DEFAULT\_GAS\_LIMIT\_OP\_CREATE). |
| gasPrice       | numeric | No       | The gas price in KAON per gas unit.                                             |
| senderaddress  | string  | No       | The Kaon address used to create the contract.                                   |
| broadcast      | boolean | No       | Whether to broadcast the transaction (default: true).                           |
| changeToSender | boolean | No       | Return change to the sender (default: true).                                    |

#### Flags

None

#### Input

* `bytecode`: Contract bytecode as a hexadecimal string.
* `gasLimit`: (Optional) Gas limit as a number. Defaults to DEFAULT\_GAS\_LIMIT\_OP\_CREATE.
* `gasPrice`: (Optional) Gas price in KAON per gas unit.
* `senderaddress`: (Optional) Sender's Kaon address.
* `broadcast`: (Optional) Boolean indicating whether to broadcast the transaction. Defaults to true.
* `changeToSender`: (Optional) Boolean indicating whether to return change to the sender. Defaults to true.

#### Output

A JSON array containing the transaction details:

```json
[
  {
    "txid" : "string",         // The transaction id.
    "sender" : "string",      // CURRENCY_UNIT address of the sender.
    "hash160" : "string",    // ripemd-160 hash of the sender.
    "address" : "string"     // Expected contract address.
  }
]
```

#### Examples

CLI Usage:

```bash
createcontract "60606040525b33600060006101000a81548173ffffffffffffffffffffffffffffffffffffffff02191690836c010000000000000000000000009081020402179055506103786001600050819055505b600c80605b6000396000f360606040526008565b600256"
```

```bash
createcontract "60606040525b33600060006101000a81548173ffffffffffffffffffffffffffffffffffffffff02191690836c010000000000000000000000009081020402179055506103786001600050819055505b600c80605b6000396000f360606040526008565b600256" 6000000 40 "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" true
```

JSON-RPC Call:

```json
{
  "method": "createcontract",
  "params": [
      "60606040525b33600060006101000a81548173ffffffffffffffffffffffffffffffffffffffff02191690836c010000000000000000000000009081020402179055506103786001600050819055505b600c80605b6000396000f360606040526008565b600256",
      6000000,
      40,
      "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
      true
 ]
}

```

#### Error Handling

* If the bytecode is invalid, an error is returned.
* If the gas limit is insufficient, an error is returned.
* If the sender address is invalid or doesn't have enough balance, an error is returned.
* If broadcasting the transaction fails, an error is returned.

#### Notes

Contract creation consumes KAON for gas. Ensure that the sender address has sufficient balance and the gas limit and price are appropriately set. Relatd commands: `sendtocontract`, `callcontract`, `listcontracts`, `getaccountinfo`, `getstorage`

***

### createmasternodebroadcast

Creates a masternode broadcast message for one or all masternodes configured in masternode.conf.

#### Synopsis

```bash
createmasternodebroadcast <command> (<alias>)
```

#### Description

Generates a masternode broadcast message, which is necessary for announcing a masternode to the network. You can create a broadcast for a single masternode using its alias or for all masternodes defined in your `masternode.conf` file.

#### Arguments

| Name    | Type   | Required                    | Description                                                               |
| ------- | ------ | --------------------------- | ------------------------------------------------------------------------- |
| command | string | Yes                         | The command to execute: "alias" for a single masternode or "all" for all. |
| alias   | string | Yes (if command is "alias") | The alias of the masternode (defined in masternode.conf).                 |

#### Flags

None

#### Input

* `command`: A string, either "alias" or "all".
* `alias`: (Optional, required if command is "alias") A string representing the masternode alias.

#### Output

* **If `command` is "all":** A JSON object with an overall status message and an array of broadcast objects, each containing details about the broadcast for a specific masternode.

```json
{
  "overall": "xxx",
  "detail": [
    {
      "alias": "xxx",
      "success": true|false,
      "hex": "xxx",          // If success is true.
      "error_message": "xxx"   // If success is false.
    }
    // ...
  ]
}
```

* **If `command` is "alias":** A JSON object containing the broadcast details for the specified masternode.

```json
{
  "alias": "xxx",
  "success": true|false,
  "hex": "xxx",          // If success is true.
  "error_message": "xxx"   // If success is false.
}

```

#### Examples

CLI Usage:

```bash
createmasternodebroadcast alias mymn1
```

JSON-RPC Call:

```json
{
  "method": "createmasternodebroadcast",
  "params": ["alias", "mymn1"]
}
```

#### Error Handling

* If the command is not "alias" or "all", an error is returned.
* If "alias" is specified but the alias doesn't exist in the configuration, an error is returned.
* If any error occurs during the broadcast creation for a specific masternode, the "success" field for that masternode will be false, and an "error\_message" will be provided.

#### Notes

This command doesn't start the masternode; it only creates the broadcast message. You'll need to use other commands like `startmasternode` to actually start the masternode. Related commands: `startmasternode`, `decodemasternodebroadcast`, `relaymasternodebroadcast`, `listmasternodes`

***

### createmasternodekey

Create a new masternode private key.

#### Synopsis

```bash
createmasternodekey
```

#### Description

Generates a new private key specifically for use with a masternode. This key is crucial for running a masternode and should be kept securely.

#### Arguments

None

#### Flags

None

#### Input

None

#### Output

The newly generated masternode private key.

#### Examples

CLI Usage:

```bash
createmasternodekey
```

JSON-RPC Call:

```json
{
  "method": "createmasternodekey",
  "params": []
}
```

#### Error Handling

If the key generation process encounters any errors, an error message will be returned.

#### Notes

Store this private key securely, as losing it can result in the loss of your masternode collateral. This key is different from your regular wallet private keys and shouldn't be used for other purposes. Related commands: `startmasternode`, `listmasternodes`

***

### createmasternodev2

Attempts to start one or more masternode(s). 1) send a tx with 10k to that address. 2) get the rate output. 3) use those values on the masternode.conf.

#### Synopsis

```bash
createmasternodev2 <alias> <127.0.0.1:9871> <privkey>
```

#### Description

This command attempts to start a masternode by sending a transaction with the specified amount (10,000 KAON) to the specified address. It then retrieves the transaction output and uses those values to configure the masternode in the `masternode.conf` file.

#### Arguments

| Name    | Type   | Required | Description                            |
| ------- | ------ | -------- | -------------------------------------- |
| privkey | string | Yes      | The masternode's private key.          |
| alias   | string | Yes      | The alias for the masternode.          |
| address | string | Yes      | IP address and port of the masternode. |

#### Flags

None

#### Input

* `privkey`: The private key of the masternode.
* `alias`: The alias or label for the masternode.
* `address`: The IP address and port of the masternode.

#### Output

A JSON object indicating the status of the operation.

```json
{
  "success": true|false, // Status of the operation
  "result": "string"    // Masternode creation status message
}
```

#### Examples

CLI Usage:

```bash
createmasternodev2 "mymn1" "127.0.0.1:9871" "93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg"
```

JSON-RPC Call (example - structure depends on the actual output):

```json
{
  "method": "createmasternodev2",
  "params": ["mymn1", "127.0.0.1:9871", "93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg"]
}
```

#### Error Handling

* If any of the arguments are invalid or missing, an error message will be returned.
* If the transaction fails to send or the output cannot be retrieved, an error message will be returned.
* If the `masternode.conf` file cannot be updated, an error message will be returned.

#### Notes

This command automates the process of setting up a masternode, but it's important to understand the underlying steps involved. Ensure you have the correct collateral amount (10,000 KAON) and that the specified address is controlled by your wallet. Related commands: `startmasternode`, `listmasternodes`

***

### createmultisig

Creates a multi-signature address with n signature of m keys required. It returns a json object with the address and redeemScript.

#### Synopsis

```bash
createmultisig <nrequired> <keys>
```

#### Description

Creates a multisignature address that requires a specific number of signatures (`nrequired`) to authorize spending. The address is generated from a list of provided public keys or Kaon addresses (`keys`). The command returns the newly created multisignature address and its corresponding redeem script.

#### Arguments

| Name      | Type    | Required | Description                                                |
| --------- | ------- | -------- | ---------------------------------------------------------- |
| nrequired | numeric | Yes      | The number of required signatures.                         |
| keys      | string  | Yes      | A JSON array of Kaon addresses or hex-encoded public keys. |

#### Flags

None

#### Input

* `nrequired`: An integer representing the minimum number of signatures required.
* `keys`: A JSON string representing an array of Kaon addresses or hex-encoded public keys.

#### Output

A JSON object containing the multisignature address and redeem script.

```json
{
  "address": "multisigaddress", // The new multisig address
  "redeemScript": "script"       // The hex-encoded redemption script
}

```

#### Examples

CLI Usage:

```bash
createmultisig 2 "[\"16sSauSf5pF2UkUwvKGq4qjNRzBZYqgEL5\",\"171sgjn4YtPu27adkKGrdDwzRTxnRkBfKV\"]"
```

JSON-RPC Call:

```json
{
  "method": "createmultisig",
  "params": [2, "[\"16sSauSf5pF2UkUwvKGq4qjNRzBZYqgEL5\",\"171sgjn4YtPu27adkKGrdDwzRTxnRkBfKV\"]"]
}
```

#### Error Handling

* If the `nrequired` parameter is invalid (e.g., less than 1 or greater than the number of keys), an error is returned.
* If any of the keys in the `keys` array are invalid, an error is returned.

#### Notes

Multisignature addresses enhance security by requiring multiple parties to authorize transactions. The redeem script is essential for spending funds from this address. Related commands: `addmultisigaddress`

***

### createwallet

Creates and loads a new wallet. Note that this will shutdown the server.

#### Synopsis

```bash
createwallet (<wallet_name> <disable_private_keys> <blank> <passphrase> <avoid_reuse> <descriptors> <load_on_startup> <external_signer>)
```

#### Description

Creates a new Kaon wallet. This action will shut down the current Kaon server. The parameters `wallet_name`, `disable_private_keys`, `blank`, `avoid_reuse`, `descriptors`, `load_on_startup` and `external_signer` are currently unused. You can specify a passphrase to encrypt the new wallet.

#### Arguments

| Name                   | Type    | Required | Description                                                                                     |
| ---------------------- | ------- | -------- | ----------------------------------------------------------------------------------------------- |
| wallet\_name           | string  | No       | UNUSED                                                                                          |
| disable\_private\_keys | boolean | No       | UNUSED                                                                                          |
| blank                  | boolean | No       | UNUSED                                                                                          |
| passphrase             | string  | No       | The passphrase to encrypt the wallet with. It must be at least 1 character, but should be long. |
| avoid\_reuse           | boolean | No       | UNUSED                                                                                          |
| descriptors            | boolean |          |                                                                                                 |





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
