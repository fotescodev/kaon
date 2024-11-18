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

---

## CLI Commands

## abandontransaction

Mark in-wallet transaction <txid> as abandoned. This will mark this transaction and all its in-wallet descendants as abandoned which will allow for their inputs to be respent. It can be used to replace "stuck" or evicted transactions. It only works on transactions which are not included in a block and are not currently in the mempool. It has no effect on transactions which are already conflicted or abandoned.

### Synopsis

```bash
abandontransaction <txid>
```

### Description

Marks a specific transaction and its descendants as abandoned within the wallet. This allows the inputs of the abandoned transactions to be reused in new transactions. This command is useful for dealing with transactions that are "stuck" or have been evicted from the mempool. It's important to note that this command only affects transactions that haven't been confirmed in a block and are not currently in the mempool. It won't have any effect on transactions that are already marked as conflicted or abandoned.

### Arguments

| Name | Type   | Required | Description                           |
| ---- | ------ | -------- | ------------------------------------- |
| txid | string | Yes      | The ID of the transaction to abandon. |

### Flags

None

### Input

The transaction ID ( `txid` ) as a string.

### Output

None

### Examples

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

### Error Handling

- If the transaction ID is invalid or doesn't exist in the wallet, an error message will be returned.
- If the transaction is already confirmed in a block or is currently in the mempool, the command will have no effect and may return an error or a warning.

### Notes

Use this command with caution. Once a transaction is abandoned, its outputs become spendable again, so ensure you understand the implications before using this command.

---

## addmultisigaddress

Add a nrequired-to-sign multisignature address to the wallet. Each key is a Kaon address or hex-encoded public key. If 'account' is specified (DEPRECATED), assign address to that account.

### Synopsis

```bash
addmultisigaddress <nrequired> <keysobject> (<account>)
```

### Description

Adds a multisignature address to the wallet. This address requires `nrequired` signatures from the provided list of keys to authorize spending. Each key can be a Kaon address or a hex-encoded public key. The optional `account` parameter is deprecated and should not be used.

### Arguments

| Name       | Type    | Required | Description                                                      |
| ---------- | ------- | -------- | ---------------------------------------------------------------- |
| nrequired  | numeric | Yes      | The number of required signatures.                               |
| keysobject | string  | Yes      | A JSON array of Kaon addresses or hex-encoded public keys.       |
| account    | string  | No       | **DEPRECATED.** An account to assign the address to (don't use). |

### Flags

None

### Input

- `nrequired`: An integer representing the number of required signatures.
- `keysobject`: A JSON string representing an array of Kaon addresses or hex-encoded public keys.
- `account`: (Optional, deprecated) A string representing the account name.

### Output

A Kaon address associated with the provided keys.

### Examples

CLI Usage:

```bash
addmultisigaddress 2 "[\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\",\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\"]"
```

JSON-RPC Call:

```json
{
  "method": "addmultisigaddress",
  "params": [
    2,
    "[\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\",\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\"]"
  ]
}
```

### Error Handling

- If `nrequired` is invalid (e.g., less than 1 or greater than the number of keys), an error is returned.
- If the `keysobject` is not a valid JSON array or contains invalid Kaon addresses or public keys, an error is returned.

### Notes

Multisignature addresses enhance security by requiring multiple parties to authorize transactions.

---

## addnode

Attempts add or remove a node from the addnode list. Or try a connection to a node once.

### Synopsis

```bash
addnode <node> <add|remove|onetry>
```

### Description

Manages the list of nodes that the Kaon client attempts to connect to. You can use this command to add a new node, remove an existing node, or attempt a one-time connection to a specific node.

### Arguments

| Name    | Type   | Required | Description                                                     |
| ------- | ------ | -------- | --------------------------------------------------------------- |
| node    | string | Yes      | The IP address and port of the node (e.g., "192.168.0.6:9871"). |
| command | string | Yes      | The action to perform: "add", "remove", or "onetry".            |

### Flags

None

### Input

- `node`: The IP address and port of the node as a string.
- `command`: The command to execute ("add", "remove", or "onetry") as a string.

### Output

None

### Examples

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

### Error Handling

- If the `node` format is invalid, an error will be returned.
- If the `command` is not one of "add", "remove", or "onetry", an error will be returned.
- If attempting to add a node that is already in the addnode list, or remove a node that is not in the list, an error or warning may be returned.

### Notes

The `addnode` command affects the client's connection attempts but doesn't guarantee a successful connection. See `getpeerinfo` for a list of currently connected peers. Related commands: [ `getpeerinfo` ](#getpeerinfo), [ `disconnectnode` ](#disconnectnode).

---

## autocombinerewards

Wallet will automatically monitor for any coins with value below the threshold amount, and combine them if they reside with the same Kaon address. When autocombinerewards runs it will create a transaction, and therefore will be subject to transaction fees.

### Synopsis

```bash
autocombinerewards <enable> (<threshold>)
```

### Description

Enables or disables the automatic combining of small coin outputs within the wallet. If enabled, the wallet will regularly check for outputs below the specified `threshold` and combine them into a single larger output. This helps to reduce the number of UTXOs in the wallet and can improve transaction efficiency. Note that combining outputs involves creating a new transaction, which incurs transaction fees.

### Arguments

| Name      | Type    | Required | Description                                                  |
| --------- | ------- | -------- | ------------------------------------------------------------ |
| enable    | boolean | Yes      | Enable (true) or disable (false) automatic combining.        |
| threshold | numeric | No       | The minimum output value below which coins will be combined. |

### Flags

None

### Input

- `enable`: A boolean value (`true` or `false`).
- `threshold`: (Optional) A numeric value representing the threshold. Defaults to 0.

### Output

None

### Examples

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

### Error Handling

- If `enable` is not a valid boolean, an error will be returned.
- If `threshold` is not a valid number, an error will be returned.

### Notes

Transaction fees are deducted from the combined output. Combining outputs can help improve wallet performance, especially if you have many small UTXOs.

---

## backupwallet

Safely copies wallet.dat to destination, which can be a directory or a path with filename.

### Synopsis

```bash
backupwallet <destination>
```

### Description

Creates a backup of the wallet data file ( `wallet.dat` ). This backup can be used to restore the wallet in case of data loss or corruption. The `destination` can be either a directory or a full file path.

### Arguments

| Name        | Type   | Required | Description                                       |
| ----------- | ------ | -------- | ------------------------------------------------- |
| destination | string | Yes      | The directory or file path for the wallet backup. |

### Flags

None

### Input

- `destination`: A string representing the backup destination.

### Output

None

### Examples

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

### Error Handling

- If the destination directory doesn't exist or is not writable, an error will be returned.
- If the wallet file cannot be read, an error will be returned.

### Notes

Regularly backing up your wallet is essential for protecting your funds. Store backups securely and in multiple locations. Related commands: [ `dumpwallet` ](#dumpwallet), [ `importwallet` ](#importwallet)

---

## bip38decrypt

Decrypts and then imports password protected private key.

### Synopsis

```bash
bip38decrypt <kaonaddress> <passphrase>
```

### Description

Decrypts a BIP38-encrypted private key and imports it into the wallet. BIP38 encryption provides a secure way to store private keys by encrypting them with a passphrase.

### Arguments

| Name        | Type   | Required | Description                        |
| ----------- | ------ | -------- | ---------------------------------- |
| kaonaddress | string | Yes      | The encrypted private key.         |
| passphrase  | string | Yes      | The passphrase to decrypt the key. |

### Flags

None

### Input

- `kaonaddress`: The encrypted BIP38 private key as a string.
- `passphrase`: The passphrase used to encrypt the private key as a string.

### Output

The decrypted private key.

### Examples

CLI Usage:

```bash
bip38decrypt "6PRVWUbkzzsbcVac2qwfssoUJAN1Xhrg6bNk8J7Nzm5H7kxEbn2Nh2ZoGg" "mypassphrase"
```

JSON-RPC Call:

```json
{
  "method": "bip38decrypt",
  "params": [
    "6PRVWUbkzzsbcVac2qwfssoUJAN1Xhrg6bNk8J7Nzm5H7kxEbn2Nh2ZoGg",
    "mypassphrase"
  ]
}
```

### Error Handling

- If the encrypted key is invalid or not a valid BIP38-encrypted key, an error message will be returned.
- If the passphrase is incorrect, decryption will fail, and an error will be returned.

### Notes

After decrypting and importing the key, you can use it like any other private key in your wallet. Be sure to keep the decrypted private key secure. Related commands: [ `bip38encrypt` ](#bip38encrypt), [ `importprivkey` ](#importprivkey), [ `dumpprivkey` ](#dumpprivkey)

---

## bip38encrypt

Encrypts a private key corresponding to 'kaonaddress'.

### Synopsis

```bash
bip38encrypt <kaonaddress> <passphrase>
```

### Description

Encrypts a private key corresponding to a specific Kaon address using BIP38 encryption. This allows you to securely store your private keys by protecting them with a passphrase. This command only works if you already hold the private key for the specified address in your wallet.

### Arguments

| Name        | Type   | Required | Description                               |
| ----------- | ------ | -------- | ----------------------------------------- |
| kaonaddress | string | Yes      | The Kaon address associated with the key. |
| passphrase  | string | Yes      | The passphrase to encrypt the key with.   |

### Flags

None

### Input

- `kaonaddress`: The Kaon address for the private key to encrypt.
- `passphrase`: The passphrase to use for encryption. Valid special chars: !#$%&'()\*+, -./:; <=>?`{|}~

### Output

The BIP38 encrypted private key.

### Examples

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

### Error Handling

- If the address is invalid or doesn't exist in the wallet, an error is returned.
- If the wallet doesn't hold the private key for the specified address, an error is returned.

### Notes

Store the encrypted private key securely. You will need the passphrase to decrypt and use the key later. Related commands: [ `bip38decrypt` ](#bip38decrypt), [ `importprivkey` ](#importprivkey), [ `dumpprivkey` ](#dumpprivkey)

---

## callcontract

Call contract methods offline.

### Synopsis

```bash
callcontract <address> <data> (<senderAddress> <gasLimit> <amount>)
```

### Description

Calls a contract method offline, allowing you to test and debug contract interactions without broadcasting a transaction to the network. This command provides detailed information about the execution result and transaction receipt, including gas usage, returned data, and any exceptions thrown.

### Arguments

| Name          | Type    | Required | Description                                           |
| ------------- | ------- | -------- | ----------------------------------------------------- |
| address       | string  | Yes      | The contract address (hexadecimal).                   |
| data          | string  | Yes      | The data hex string to pass to the contract method.   |
| senderAddress | string  | No       | The sender address string.                            |
| gasLimit      | numeric | No       | The gas limit for executing the contract.             |
| amount        | numeric | No       | UNUSED The amount in KAON to send. eg 0.1, default: 0 |

### Flags

None

### Input

- `address`: The contract address as a hexadecimal string.
- `data`: The data to send to the contract method as a hexadecimal string.
- `senderAddress`: (Optional) The sender's Kaon address.
- `gasLimit`: (Optional) The gas limit for the contract execution.
- `amount`: (Optional, UNUSED) The amount of KAON to send (default: 0).

### Output

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

### Examples

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

### Error Handling

- If the contract address is invalid, an error is returned.
- If the data hex string is invalid, an error is returned.
- If the gas limit is insufficient, an error is returned.
- If the contract execution throws an exception, the `excepted` field in the output will contain the exception message.

### Notes

This command is helpful for testing contract functionality before deploying it or sending transactions on the mainnet. It simulates a transaction without actually broadcasting it.

---

## checkbudgets

Initiates a budget check cycle manually.

### Synopsis

```bash
checkbudgets
```

### Description

Manually triggers a budget check cycle. This cycle evaluates budget proposals and determines which proposals will be funded in the next payment cycle.

### Arguments

None

### Flags

None

### Input

None

### Output

None

### Examples

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

### Error Handling

Any errors encountered during the budget check cycle will be logged.

### Notes

Manually checking budgets can be useful for testing or debugging the budget system. Related commands: [ `getbudgetinfo` ](#getbudgetinfo), [ `getbudgetprojection` ](#getbudgetprojection), [ `getbudgetvotes` ](#getbudgetvotes), [ `preparebudget` ](#preparebudget), [ `submitbudget` ](#submitbudget)

---

## clearbanned

Clear all banned IPs.

### Synopsis

```bash
clearbanned
```

### Description

Removes all IP addresses and subnets from the ban list. Banned IPs are prevented from connecting to your Kaon node.

### Arguments

None

### Flags

None

### Input

None

### Output

None

### Examples

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

### Error Handling

Any errors encountered while clearing the ban list will be logged.

### Notes

Use with caution, as clearing the ban list might allow previously banned nodes to reconnect. Related commands: [ `setban` ](#setban), [ `listbanned` ](#listbanned)

---

## createcontract

Create a contract with bytecode.

### Synopsis

```bash
createcontract <bytecode> (<gasLimit> <gasPrice> <senderaddress> <broadcast> <changeToSender>)
```

### Description

Creates a new smart contract on the Kaon blockchain using the provided bytecode. This command allows you to deploy your own smart contracts and interact with them. You can specify gas limit, gas price, sender address, whether to broadcast the transaction, and whether to return the change to the sender.

### Arguments

| Name           | Type    | Required | Description                                                                 |
| -------------- | ------- | -------- | --------------------------------------------------------------------------- |
| bytecode       | string  | Yes      | The contract bytecode (hexadecimal string).                                 |
| gasLimit       | numeric | No       | The gas limit for contract creation (default: DEFAULT_GAS_LIMIT_OP_CREATE). |
| gasPrice       | numeric | No       | The gas price in KAON per gas unit.                                         |
| senderaddress  | string  | No       | The Kaon address used to create the contract.                               |
| broadcast      | boolean | No       | Whether to broadcast the transaction (default: true).                       |
| changeToSender | boolean | No       | Return change to the sender (default: true).                                |

### Flags

None

### Input

- `bytecode`: Contract bytecode as a hexadecimal string.
- `gasLimit`: (Optional) Gas limit as a number. Defaults to DEFAULT_GAS_LIMIT_OP_CREATE.
- `gasPrice`: (Optional) Gas price in KAON per gas unit.
- `senderaddress`: (Optional) Sender's Kaon address.
- `broadcast`: (Optional) Boolean indicating whether to broadcast the transaction. Defaults to true.
- `changeToSender`: (Optional) Boolean indicating whether to return change to the sender. Defaults to true.

### Output

A JSON array containing the transaction details:

```json
[
  {
    "txid": "string", // The transaction id.
    "sender": "string", // CURRENCY_UNIT address of the sender.
    "hash160": "string", // ripemd-160 hash of the sender.
    "address": "string" // Expected contract address.
  }
]
```

### Examples

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

### Error Handling

- If the bytecode is invalid, an error is returned.
- If the gas limit is insufficient, an error is returned.
- If the sender address is invalid or doesn't have enough balance, an error is returned.
- If broadcasting the transaction fails, an error is returned.

### Notes

Contract creation consumes KAON for gas. Ensure that the sender address has sufficient balance and the gas limit and price are appropriately set. Relatd commands: [ `sendtocontract` ](#sendtocontract), [ `callcontract` ](#callcontract), [ `listcontracts` ](#listcontracts), [ `getaccountinfo` ](#getaccountinfo), [ `getstorage` ](#getstorage)

---

## createmasternodebroadcast

Creates a masternode broadcast message for one or all masternodes configured in masternode.conf.

### Synopsis

```bash
createmasternodebroadcast <command> (<alias>)
```

### Description

Generates a masternode broadcast message, which is necessary for announcing a masternode to the network. You can create a broadcast for a single masternode using its alias or for all masternodes defined in your `masternode.conf` file.

### Arguments

| Name    | Type   | Required                    | Description                                                               |
| ------- | ------ | --------------------------- | ------------------------------------------------------------------------- |
| command | string | Yes                         | The command to execute: "alias" for a single masternode or "all" for all. |
| alias   | string | Yes (if command is "alias") | The alias of the masternode (defined in masternode.conf).                 |

### Flags

None

### Input

- `command`: A string, either "alias" or "all".
- `alias`: (Optional, required if command is "alias") A string representing the masternode alias.

### Output

- **If `command` is "all":**
  A JSON object with an overall status message and an array of broadcast objects, each containing details about the broadcast for a specific masternode.

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

- **If `command` is "alias":**
  A JSON object containing the broadcast details for the specified masternode.

```json
{
  "alias": "xxx",
  "success": true|false,
  "hex": "xxx",          // If success is true.
  "error_message": "xxx"   // If success is false.
}

```

### Examples

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

### Error Handling

- If the command is not "alias" or "all", an error is returned.
- If "alias" is specified but the alias doesn't exist in the configuration, an error is returned.
- If any error occurs during the broadcast creation for a specific masternode, the "success" field for that masternode will be false, and an "error_message" will be provided.

### Notes

This command doesn't start the masternode; it only creates the broadcast message. You'll need to use other commands like `startmasternode` to actually start the masternode. Related commands: [ `startmasternode` ](#startmasternode), [ `decodemasternodebroadcast` ](#decodemasternodebroadcast), [ `relaymasternodebroadcast` ](#relaymasternodebroadcast), [ `listmasternodes` ](#listmasternodes)

---

## createmasternodekey

Create a new masternode private key.

### Synopsis

```bash
createmasternodekey
```

### Description

Generates a new private key specifically for use with a masternode. This key is crucial for running a masternode and should be kept securely.

### Arguments

None

### Flags

None

### Input

None

### Output

The newly generated masternode private key.

### Examples

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

### Error Handling

If the key generation process encounters any errors, an error message will be returned.

### Notes

Store this private key securely, as losing it can result in the loss of your masternode collateral. This key is different from your regular wallet private keys and shouldn't be used for other purposes. Related commands: [ `startmasternode` ](#startmasternode), [ `listmasternodes` ](#listmasternodes)

---

## createmasternodev2

Attempts to start one or more masternode(s). 1) send a tx with 10k to that address. 2) get the rate output. 3) use those values on the masternode.conf.

### Synopsis

```bash
createmasternodev2 <alias> <127.0.0.1:9871> <privkey>
```

### Description

This command attempts to start a masternode by sending a transaction with the specified amount (10, 000 KAON) to the specified address. It then retrieves the transaction output and uses those values to configure the masternode in the `masternode.conf` file.

### Arguments

| Name    | Type   | Required | Description                            |
| ------- | ------ | -------- | -------------------------------------- |
| privkey | string | Yes      | The masternode's private key.          |
| alias   | string | Yes      | The alias for the masternode.          |
| address | string | Yes      | IP address and port of the masternode. |

### Flags

None

### Input

- `privkey`: The private key of the masternode.
- `alias`: The alias or label for the masternode.
- `address`: The IP address and port of the masternode.

### Output

A JSON object indicating the status of the operation.

```json
{
  "success": true|false, // Status of the operation
  "result": "string"    // Masternode creation status message
}
```

### Examples

CLI Usage:

```bash
createmasternodev2 "mymn1" "127.0.0.1:9871" "93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg"
```

JSON-RPC Call (example - structure depends on the actual output):

```json
{
  "method": "createmasternodev2",
  "params": [
    "mymn1",
    "127.0.0.1:9871",
    "93HaYBVUCYjEMeeH1Y4sBGLALQZE1Yc1K64xiqgX37tGBDQL8Xg"
  ]
}
```

### Error Handling

- If any of the arguments are invalid or missing, an error message will be returned.
- If the transaction fails to send or the output cannot be retrieved, an error message will be returned.
- If the `masternode.conf` file cannot be updated, an error message will be returned.

### Notes

This command automates the process of setting up a masternode, but it's important to understand the underlying steps involved. Ensure you have the correct collateral amount (10, 000 KAON) and that the specified address is controlled by your wallet. Related commands: [ `startmasternode` ](#startmasternode), [ `listmasternodes` ](#listmasternodes)

---

## createmultisig

Creates a multi-signature address with n signature of m keys required.
It returns a json object with the address and redeemScript.

### Synopsis

```bash
createmultisig <nrequired> <keys>
```

### Description

Creates a multisignature address that requires a specific number of signatures ( `nrequired` ) to authorize spending. The address is generated from a list of provided public keys or Kaon addresses ( `keys` ). The command returns the newly created multisignature address and its corresponding redeem script.

### Arguments

| Name      | Type    | Required | Description                                                |
| --------- | ------- | -------- | ---------------------------------------------------------- |
| nrequired | numeric | Yes      | The number of required signatures.                         |
| keys      | string  | Yes      | A JSON array of Kaon addresses or hex-encoded public keys. |

### Flags

None

### Input

- `nrequired`: An integer representing the minimum number of signatures required.
- `keys`: A JSON string representing an array of Kaon addresses or hex-encoded public keys.

### Output

A JSON object containing the multisignature address and redeem script.

```json
{
  "address": "multisigaddress", // The new multisig address
  "redeemScript": "script" // The hex-encoded redemption script
}
```

### Examples

CLI Usage:

```bash
createmultisig 2 "[\"16sSauSf5pF2UkUwvKGq4qjNRzBZYqgEL5\",\"171sgjn4YtPu27adkKGrdDwzRTxnRkBfKV\"]"
```

JSON-RPC Call:

```json
{
  "method": "createmultisig",
  "params": [
    2,
    "[\"16sSauSf5pF2UkUwvKGq4qjNRzBZYqgEL5\",\"171sgjn4YtPu27adkKGrdDwzRTxnRkBfKV\"]"
  ]
}
```

### Error Handling

- If the `nrequired` parameter is invalid (e.g., less than 1 or greater than the number of keys), an error is returned.
- If any of the keys in the `keys` array are invalid, an error is returned.

### Notes

Multisignature addresses enhance security by requiring multiple parties to authorize transactions. The redeem script is essential for spending funds from this address. Related commands: [ `addmultisigaddress` ](#addmultisigaddress)

---

## createwallet

Creates and loads a new wallet. Note that this will shutdown the server.

### Synopsis

```bash
createwallet (<wallet_name> <disable_private_keys> <blank> <passphrase> <avoid_reuse> <descriptors> <load_on_startup> <external_signer>)
```

### Description

Creates a new Kaon wallet. This action will shut down the current Kaon server. The parameters `wallet_name` , `disable_private_keys` , `blank` , `avoid_reuse` , `descriptors` , `load_on_startup` and `external_signer` are currently unused. You can specify a passphrase to encrypt the new wallet.

### Arguments

| Name                 | Type    | Required | Description                                                                                     |
| -------------------- | ------- | -------- | ----------------------------------------------------------------------------------------------- |
| wallet_name          | string  | No       | UNUSED                                                                                          |
| disable_private_keys | boolean | No       | UNUSED                                                                                          |
| blank                | boolean | No       | UNUSED                                                                                          |
| passphrase           | string  | No       | The passphrase to encrypt the wallet with. It must be at least 1 character, but should be long. |
| avoid_reuse          | boolean | No       | UNUSED                                                                                          |
| descriptors          | boolean | No       | UNUSED                                                                                          |
| load_on_startup      | boolean | No       | UNUSED                                                                                          |
| external_signer      | boolean | No       | UNUSED                                                                                          |

### Flags

None

### Input

- `passphrase`: (Optional) The passphrase to encrypt the new wallet.

### Output

None

### Examples

CLI Usage:

```bash
createwallet "mywallet" false false "mystrongpassphrase" false false false false
```

JSON-RPC Call:

```json
{
  "method": "createwallet",
  "params": [
    "mywallet",
    false,
    false,
    "mystrongpassphrase",
    false,
    false,
    false,
    false
  ]
}
```

### Error Handling

If the wallet creation fails (e.g., due to insufficient disk space or an invalid passphrase), an error message will be displayed, and the server will not shut down.

### Notes

After creating a new wallet, you'll need to restart the Kaon server. If you provided a passphrase, you'll need to unlock the wallet using the `walletpassphrase` command before performing any transactions. Related commands: [ `encryptwallet` ](#encryptwallet), [ `walletpassphrase` ](#walletpassphrase), [ `walletlock` ](#walletlock)

---

## decodemasternodebroadcast

Command to decode masternode broadcast messages.

### Synopsis

```bash
decodemasternodebroadcast <hexstring>
```

### Description

Decodes a hex-encoded masternode broadcast message. This command is useful for inspecting the contents of a broadcast message, such as the masternode's IP address, public keys, and signature.

### Arguments

| Name      | Type   | Required | Description                                   |
| --------- | ------ | -------- | --------------------------------------------- |
| hexstring | string | Yes      | The hex-encoded masternode broadcast message. |

### Flags

None

### Input

- `hexstring`: The hexadecimal representation of the masternode broadcast message.

### Output

A JSON object containing the decoded information from the broadcast message.

```json
{
  "vin": "xxxx",
  "addr": "xxxx",
  "pubkeycollateral": "xxxx",
  "pubkeymasternode": "xxxx",
  "vchsig": "xxxx",
  "sigtime": "nnn",
  "sigvalid": "xxx",
  "protocolversion": "nnn",
  "nlastdsq": "nnn",
  "nMessVersion": "nnn",
  "lastping": {
    "vin": "xxxx",
    "blockhash": "xxxx",
    "sigtime": "nnn",
    "sigvalid": "xxx",
    "vchsig": "xxxx",
    "nMessVersion": "nnn"
  }
}
```

### Examples

CLI Usage:

```bash
decodemasternodebroadcast "hexstring"
```

JSON-RPC Call:

```json
{
  "method": "decodemasternodebroadcast",
  "params": ["hexstring"]
}
```

### Error Handling

If the input `hexstring` is not a valid masternode broadcast message, an error will be returned.

### Notes

The decoded information helps verify the authenticity and validity of the broadcast message. Related commands: [ `createmasternodebroadcast` ](#createmasternodebroadcast), [ `relaymasternodebroadcast` ](#relaymasternodebroadcast)

---

## decoderawtransaction

Return a JSON object representing the serialized, hex-encoded transaction.

### Synopsis

```bash
decoderawtransaction <hex> <iswitness>
```

### Description

Decodes a raw transaction given its hexadecimal representation. This command provides a structured JSON representation of the transaction, including details about its inputs, outputs, and other relevant information. The `iswitness` parameter is unused because Kaon always uses SegWit.

### Arguments

| Name      | Type    | Required | Description                       |
| --------- | ------- | -------- | --------------------------------- |
| hex       | string  | Yes      | The transaction hex string.       |
| iswitness | boolean | Yes      | UNUSED (Kaon always uses SegWit). |

### Flags

None

### Input

- `hex`: The raw transaction as a hexadecimal string.
- `iswitness`: A boolean value (always treat as true for Kaon).

### Output

A JSON object representing the decoded raw transaction. See the original documentation for the detailed structure of this object. It includes details such as transaction ID, size, version, inputs, outputs, and involved addresses.

### Examples

CLI Usage:

```bash
decoderawtransaction "hexstring" true
```

JSON-RPC Call:

```json
{
  "method": "decoderawtransaction",
  "params": ["hexstring", true]
}
```

### Error Handling

If the input `hex` string is not a valid transaction, an error will be returned.

### Notes

This command is helpful for understanding the structure and contents of a raw transaction. Related commands: [ `createrawtransaction` ](#createrawtransaction), [ `signrawtransactionwithkey` ](#signrawtransactionwithkey), [ `sendrawtransaction` ](#sendrawtransaction)

---

## decodescript

Decode a hex-encoded script.

### Synopsis

```bash
decodescript <hex>
```

### Description

Decodes a hex-encoded script and returns information about its structure and function. This command is helpful for understanding the details of a script, such as the type of script, the required signatures, and involved addresses.

### Arguments

| Name | Type   | Required | Description             |
| ---- | ------ | -------- | ----------------------- |
| hex  | string | Yes      | The hex-encoded script. |

### Flags

None

### Input

- `hex`: A hexadecimal string representing the script.

### Output

A JSON object with the decoded script information, including the assembly code, type, required signatures, and associated addresses.

```json
{
  "asm": "asm",          // Disassembled script
  "hex": "hex",          // Hex-encoded script
  "type": "type",        // Script type (e.g., pubkeyhash)
  "reqSigs": n,          // Required signatures
  "addresses": [         // Related addresses
    "address"             // ...
  ],
  "p2sh": "address"     // P2SH address (if applicable)
}
```

### Examples

CLI Usage:

```bash
decodescript "hexstring"
```

JSON-RPC Call:

```json
{
  "method": "decodescript",
  "params": ["hexstring"]
}
```

### Error Handling

If the input `hex` is not a valid script, an error message will be returned.

### Notes

This command is valuable for analyzing the details of scripts used in transactions.

---

## delegatestake

Delegate an amount to a given address for cold staking. The amount is a real and is rounded to the nearest 0.00000001.

### Synopsis

```bash
delegatestake <stakingaddress> <amount> (<owneraddress> <fExternalOwner> <fUseDelegated> <fForceNotEnabled>)
```

### Description

Delegates a specified amount of KAON to a staking address for cold staking. Cold staking allows you to earn staking rewards without keeping your coins online. The optional `owneraddress` specifies the address that can spend the staking rewards. If not provided, a new address is generated. `fExternalOwner` allows you to use an `owneraddress` even if it's not in your wallet. `fUseDelegated` allows the use of already delegated inputs if needed, and `fForceNotEnabled` forces the creation even if cold staking is disabled (for testing).

### Arguments

| Name             | Type    | Required | Description                                                                                   |
| ---------------- | ------- | -------- | --------------------------------------------------------------------------------------------- |
| stakingaddress   | string  | Yes      | The Kaon staking address to delegate to.                                                      |
| amount           | numeric | Yes      | The amount to delegate.                                                                       |
| owneraddress     | string  | No       | The Kaon address that can spend the staking rewards.                                          |
| fExternalOwner   | boolean | No       | Use the provided `owneraddress` even if it's not in your wallet (default: false).             |
| fUseDelegated    | boolean | No       | Include already delegated inputs if needed (default: false).                                  |
| fForceNotEnabled | boolean | No       | Force stake delegation even if it's disabled (default: false, use for testing purposes only). |

### Flags

None

### Input

See Arguments description. Amounts are in KAON.

### Output

A JSON object containing the delegation details.

```json
{
  "owner_address": "xxx", // Delegator's address
  "staker_address": "xxx", // Staker's address
  "txid": "xxx" // Transaction ID
}
```

### Examples

CLI Usage:

```bash
delegatestake "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 100
```

```bash
delegatestake "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 1000 "DMJRSsuU9zfyrvxVaAEFQqK4MxZg34fk"
```

JSON-RPC Call:

```json
{
  "method": "delegatestake",
  "params": [
    "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6",
    1000,
    "DMJRSsuU9zfyrvxVaAEFQqK4MxZg34fk"
  ]
}
```

### Error Handling

- If the `stakingaddress` or `owneraddress` are invalid, an error is returned.
- If the amount is insufficient or exceeds the available balance, an error is returned.
- If cold staking is disabled and `fForceNotEnabled` is not set, an error is returned.

### Notes

Cold staking allows for offline staking, enhancing security. The delegator retains ownership of the delegated funds. Related commands: [ `rawdelegatestake` ](#rawdelegatestake), [ `getcoldstakingbalance` ](#getcoldstakingbalance), [ `listcoldutxos` ](#listcoldutxos)

---

## delegatoradd

Add the provided address <addr> into the allowed delegators AddressBook. This enables the staking of coins delegated to this wallet, owned by <addr>.

### Synopsis

```bash
delegatoradd <addr> (<label>)
```

### Description

Adds a delegator address to the whitelist, allowing the wallet to stake coins delegated from that address. This is necessary for cold staking scenarios where the owner of the funds delegates staking rights to another address. An optional label can be added for better organization.

### Arguments

| Name  | Type   | Required | Description                                     |
| ----- | ------ | -------- | ----------------------------------------------- |
| addr  | string | Yes      | The Kaon address to whitelist.                  |
| label | string | No       | A label for the whitelisted address (optional). |

### Flags

None

### Input

- `addr`: The delegator's Kaon address as a string.
- `label`: (Optional) A descriptive label for the address.

### Output

- `true` if the address was successfully added.
- `false` otherwise.

### Examples

CLI Usage:

```bash
delegatoradd DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6
```

```bash
delegatoradd DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6 "myPaperWallet"
```

JSON-RPC Call:

```json
{
  "method": "delegatoradd",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6", "myPaperWallet"]
}
```

### Error Handling

- If the provided address is invalid, an error is returned.
- If the address is already whitelisted, an error or warning may be returned.

### Notes

Adding a delegator to the whitelist enables cold staking for their delegated funds. Related commands: [ `delegatorremove` ](#delegatorremove), [ `listdelegators` ](#listdelegators), [ `delegatestake` ](#delegatestake)

---

## delegatorremove

Updates the provided address <addr> from the allowed delegators keystore to a "delegable" status.
This disables the staking of coins delegated to this wallet, owned by <addr>.

### Synopsis

```bash
delegatorremove <addr>
```

### Description

Removes a delegator address from the whitelist, preventing the wallet from staking coins delegated from that address. This is the reverse of `delegatoradd` .

### Arguments

| Name | Type   | Required | Description                           |
| ---- | ------ | -------- | ------------------------------------- |
| addr | string | Yes      | The address to remove from whitelist. |

### Flags

None

### Input

- `addr`: The delegator's Kaon address to remove.

### Output

- `true` if the address was successfully removed.
- `false` otherwise.

### Examples

CLI Usage:

```bash
delegatorremove DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6
```

JSON-RPC Call:

```json
{
  "method": "delegatorremove",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6"]
}
```

### Error Handling

- If the provided address is invalid, an error will be returned.
- If the address is not currently whitelisted, a warning or error may be returned.

### Notes

Removing a delegator disables cold staking for their delegated UTXOs held by this wallet. Related commands: [ `delegatoradd` ](#delegatoradd), [ `listdelegators` ](#listdelegators), [ `delegatestake` ](#delegatestake)

---

## disconnectnode

Immediately disconnects from the specified node.

### Synopsis

```bash
disconnectnode <node>
```

### Description

Disconnects from a specific peer node. This is useful for troubleshooting network issues or manually managing connections to other nodes on the network.

### Arguments

| Name | Type   | Required | Description                     |
| ---- | ------ | -------- | ------------------------------- |
| node | string | Yes      | The node's IP address and port. |

### Flags

None

### Input

The IP address and port of the node to disconnect from (e.g., "192.168.0.6:8333").

### Output

None

### Examples

CLI Usage:

```bash
disconnectnode "192.168.0.6:8333"
```

JSON-RPC Call:

```json
{
  "method": "disconnectnode",
  "params": ["192.168.0.6:8333"]
}
```

### Error Handling

- If the specified node is not connected, an error or warning may be returned.
- If the node address is invalid, an error will be returned.

### Notes

Disconnecting a node may temporarily affect network connectivity and synchronization. Related commands: [ `addnode` ](#addnode), [ `getpeerinfo` ](#getpeerinfo)

---

## dumpprivkey

Reveals the private key corresponding to 'kaonaddress'. Then the importprivkey can be used with this output.

### Synopsis

```bash
dumpprivkey <kaonaddress>
```

### Description

Retrieves and displays the private key associated with a specified Kaon address. This command is extremely sensitive as it exposes the private key, which controls access to the funds associated with that address. Use with extreme caution and only when absolutely necessary. It's essential to ensure no unauthorized individuals or processes can access the output of this command.

### Arguments

| Name        | Type   | Required | Description                                     |
| ----------- | ------ | -------- | ----------------------------------------------- |
| kaonaddress | string | Yes      | The Kaon address for which to retrieve the key. |

### Flags

None

### Input

The Kaon address.

### Output

The private key corresponding to the specified Kaon address.

### Examples

CLI Usage:

```bash
dumpprivkey "myaddress"
```

JSON-RPC Call:

```json
{
  "method": "dumpprivkey",
  "params": ["myaddress"]
}
```

### Error Handling

- If the provided address is not valid or not found in the wallet, an error is returned.
- If the wallet is encrypted and not unlocked, an error is returned.

### Notes

**Security Warning:** Handle the output of this command with utmost care. Anyone with access to the private key has control over the funds associated with the corresponding Kaon address. Related commands: [ `importprivkey` ](#importprivkey), [ `bip38encrypt` ](#bip38encrypt)

---

## dumpwallet

Dumps all wallet keys in a human-readable format.

### Synopsis

```bash
dumpwallet <filename>
```

### Description

Exports all private keys from the wallet to a file in a human-readable format. This command is highly sensitive and should be used with extreme caution. The exported file contains all the information needed to control the funds associated with the wallet, so it's crucial to store it securely and protect it from unauthorized access.

### Arguments

| Name     | Type   | Required | Description                  |
| -------- | ------ | -------- | ---------------------------- |
| filename | string | Yes      | The name of the output file. |

### Flags

None

### Input

- `filename`: The name of the file where the wallet data will be written.

### Output

None (The output is written to the specified file.)

### Examples

CLI Usage:

```bash
dumpwallet "wallet_backup.txt"
```

JSON-RPC Call:

```json
{
  "method": "dumpwallet",
  "params": ["wallet_backup.txt"]
}
```

### Error Handling

- If the file cannot be created or written to (e.g., due to permissions issues or disk space), an error is returned.
- If the wallet is encrypted and not unlocked, an error is returned.

### Notes

**Security Warning:** The file generated by this command contains highly sensitive information. Securely store the backup and protect it from unauthorized access. Never share this file with anyone. Related commands: [ `importwallet` ](#importwallet), [ `backupwallet` ](#backupwallet)

---

## encryptwallet

Encrypts the wallet with 'passphrase'. This is for first time encryption.
After this, any calls that interact with private keys such as sending or signing
will require the passphrase to be set prior the making these calls.
Use the walletpassphrase call for this, and then walletlock call.
If the wallet is already encrypted, use the walletpassphrasechange call.
Note that this will shutdown the server.

### Synopsis

```bash
encryptwallet <passphrase>
```

### Description

Encrypts the wallet using the provided passphrase. This is a crucial security measure to protect your funds. Once encrypted, any command that requires access to private keys (such as sending transactions or signing messages) will require you to unlock the wallet first using the `walletpassphrase` command. This command will shut down the server after encrypting the wallet.

### Arguments

| Name       | Type   | Required | Description                                  |
| ---------- | ------ | -------- | -------------------------------------------- |
| passphrase | string | Yes      | The passphrase to use for wallet encryption. |

### Flags

None

### Input

- `passphrase`: The encryption passphrase as a string. Must be at least one character long, but stronger, longer passphrases are highly recommended.

### Output

None

### Examples

CLI Usage:

```bash
encryptwallet "mystrongpassphrase"
```

JSON-RPC Call:

```json
{
  "method": "encryptwallet",
  "params": ["mystrongpassphrase"]
}
```

### Error Handling

- If the wallet is already encrypted, an error is returned.
- If the passphrase is invalid or empty, an error is returned.

### Notes

After encrypting the wallet, you'll need to restart the Kaon server. Remember your passphrase, as losing it means losing access to your funds. Related commands: [ `walletpassphrase` ](#walletpassphrase), [ `walletlock` ](#walletlock), [ `walletpassphrasechange` ](#walletpassphrasechange), [ `createwallet` ](#createwallet)

---

## estimatefee

Estimates the approximate fee per kilobyte needed for a transaction to begin confirmation within nblocks blocks.

### Synopsis

```bash
estimatefee <nblocks>
```

### Description

Estimates the transaction fee per kilobyte required for a transaction to be confirmed within a specified number of blocks ( `nblocks` ). This helps you determine an appropriate fee to include with your transactions to ensure timely confirmation.

### Arguments

| Name    | Type    | Required | Description                                                   |
| ------- | ------- | -------- | ------------------------------------------------------------- |
| nblocks | numeric | Yes      | The number of blocks within which to confirm the transaction. |

### Flags

None

### Input

- `nblocks`: A number of blocks for fee estimation.

### Output

The estimated fee per kilobyte, or -1.0 if there is insufficient data to make an estimate.

### Examples

CLI Usage:

```bash
estimatefee 6
```

JSON-RPC Call:

```json
{
  "method": "estimatefee",
  "params": [6]
}
```

### Error Handling

If the `nblocks` parameter is invalid, an error may be returned.

### Notes

The estimate is based on historical transaction data and network conditions, so it's not guaranteed to be accurate. A value of -1.0 indicates that the node hasn't observed enough transactions to provide a reliable estimate.

---

## estimatepriority

Estimates the approximate priority a zero-fee transaction needs to begin confirmation within nblocks blocks.

### Synopsis

```bash
estimatepriority <nblocks>
```

### Description

Estimates the priority required for a zero-fee transaction to be confirmed within a given number of blocks ( `nblocks` ). Transaction priority is calculated based on factors like coin age and transaction size. This command is generally less relevant with modern fee estimation mechanisms.

### Arguments

| Name    | Type    | Required | Description                                            |
| ------- | ------- | -------- | ------------------------------------------------------ |
| nblocks | numeric | Yes      | The number of blocks for confirmation time estimation. |

### Flags

None

### Input

- `nblocks`: The target number of blocks for confirmation.

### Output

The estimated priority value. Returns -1.0 if insufficient data prevents estimation.

### Examples

CLI Usage:

```bash
estimatepriority 6
```

JSON-RPC Call:

```json
{
  "method": "estimatepriority",
  "params": [6]
}
```

### Error Handling

If the input `nblocks` value is invalid, an error may be returned.

### Notes

The estimate is based on recent transactions and may not be accurate. The returned value of -1.0 signifies insufficient data for a reliable estimate. Zero-fee transactions are generally discouraged on the Kaon network as they are unlikely to be confirmed in a timely manner.

---

## fromhexaddress

Converts a raw hex address to a base58 pubkeyhash address.

### Synopsis

```bash
fromhexaddress <hexaddress>
```

### Description

Converts a hexadecimal representation of a Kaon address to its base58 encoded format. This is useful when dealing with raw addresses in scripts or smart contracts and needing their standard, user-friendly representation.

### Arguments

| Name       | Type   | Required | Description                  |
| ---------- | ------ | -------- | ---------------------------- |
| hexaddress | string | Yes      | The raw hexadecimal address. |

### Flags

None

### Input

- `hexaddress`: The hexadecimal address string.

### Output

The base58 encoded pubkeyhash address.

### Examples

CLI Usage:

```bash
fromhexaddress "0014751e76e8199196d454941c45d1b3a323f142f3"
```

JSON-RPC Call:

```json
{
  "method": "fromhexaddress",
  "params": ["0014751e76e8199196d454941c45d1b3a323f142f3"]
}
```

### Error Handling

- If the input is not a valid hexadecimal string or represents an invalid Kaon address, an error will be returned.

### Notes

This function is the reverse of `gethexaddress` . Related commands: [ `gethexaddress` ](#gethexaddress), [ `validateaddress` ](#validateaddress)

---

## gasprice

Returns the current gas price.

### Synopsis

```bash
gasprice
```

### Description

Retrieves the current price of gas in KAON. Gas is required to execute smart contracts on the Kaon network. The gas price determines the cost of each unit of gas used during contract execution.

### Arguments

None

### Flags

None

### Input

None

### Output

The current gas price as a numeric value.

### Examples

CLI Usage:

```bash
gasprice
```

JSON-RPC Call:

```json
{
  "method": "gasprice",
  "params": []
}
```

### Error Handling

Any errors encountered while retrieving the gas price will be logged. However, it's rare for this command to return an error.

### Notes

The gas price can fluctuate based on network demand. Related commands: [ `createcontract` ](#createcontract), [ `sendtocontract` ](#sendtocontract)

---

## generate

Mine blocks immediately (before the RPC call returns). Note: this function can only be used on the regtest network.

### Synopsis

```bash
generate <nGenerate>
```

### Description

Generates (mines) a specified number of blocks on the regtest network. This command is intended for testing and development purposes only and should not be used on mainnet or testnet.

### Arguments

| Name      | Type    | Required | Description                   |
| --------- | ------- | -------- | ----------------------------- |
| nGenerate | numeric | Yes      | The number of blocks to mine. |

### Flags

None

### Input

- `nGenerate`: An integer specifying the number of blocks to generate.

### Output

An array of hexadecimal block hashes representing the generated blocks.

### Examples

CLI Usage:

```bash
generate 11
```

JSON-RPC Call:

```json
{
  "method": "generate",
  "params": [11]
}
```

### Error Handling

- This command will return an error if used on mainnet or testnet.
- If the requested number of blocks cannot be generated, an error will be returned.

### Notes

This command is exclusively for testing and development on the regtest network. Using it on mainnet or testnet will have no effect and might return an error.

---

## generatetoaddress

Mine blocks immediately to a specified address (before the RPC call returns).

### Synopsis

```bash
generatetoaddress <nblocks> <address> (<maxtries>)
```

### Description

Mines a specified number of blocks and sends the mining rewards to a given address. This command is primarily for testing and development purposes. The `address` parameter is unused, and the `maxtries` parameter is also unused.

### Arguments

| Name     | Type    | Required | Description                                       |
| -------- | ------- | -------- | ------------------------------------------------- |
| nblocks  | numeric | Yes      | The number of blocks to generate.                 |
| address  | string  | Yes      | UNUSED. The address to send rewards to (ignored). |
| maxtries | numeric | No       | UNUSED. (Ignored).                                |

### Flags

None

### Input

- `nblocks`: The number of blocks to generate.
- `address`: UNUSED - Provide a valid Kaon address (but it's ignored).
- `maxtries`: UNUSED

### Output

An array of string hashes of the generated blocks.

### Examples

CLI Usage:

```bash
generatetoaddress 11 "myaddress"
```

JSON-RPC Call:

```json
{
  "method": "generatetoaddress",
  "params": [11, "myaddress"]
}
```

### Error Handling

- If the `nblocks` parameter is invalid, an error will be returned.

### Notes

While you can specify an address, the mining rewards will not be sent to that address (this parameter is ignored). This command is primarily intended for testing and development. Related commands: [ `getnewaddress` ](#getnewaddress), [ `generate` ](#generate)

---

## getaccount

DEPRECATED. Returns the account associated with the given address.

### Synopsis

```bash
getaccount <kaonaddress>
```

### Description

**DEPRECATED.** This command is deprecated and should not be used. It was previously used to retrieve the account name associated with a specific Kaon address. Account functionality is generally deprecated in newer versions of Kaon.

### Arguments

| Name        | Type   | Required | Description                  |
| ----------- | ------ | -------- | ---------------------------- |
| kaonaddress | string | Yes      | The Kaon address for lookup. |

### Flags

None

### Input

- `kaonaddress`: The Kaon address as a string.

### Output

The account name associated with the address (if any), or an error if the address is invalid or not associated with an account.

### Examples

CLI Usage:

```bash
getaccount "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6"
```

JSON-RPC Call:

```json
{
  "method": "getaccount",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6"]
}
```

### Error Handling

If the input address is invalid, doesn't exist, or no account is associated with it, an error message is returned.

### Notes

This command is deprecated and shouldn't be used in new code. Avoid using accounts in your Kaon applications.

---

## getaccountaddress

DEPRECATED. Returns the current Kaon address for receiving payments to this account.

### Synopsis

```bash
getaccountaddress <account>
```

### Description

**DEPRECATED.** This command is deprecated and should not be used. In older versions of Kaon, this command returned a Kaon address associated with a specific account. If the account didn't exist, it would be created along with a new address. Account functionality is deprecated in newer versions of Kaon.

### Arguments

| Name    | Type   | Required | Description                                          |
| ------- | ------ | -------- | ---------------------------------------------------- |
| account | string | Yes      | The account name. Can be "" for the default account. |

### Flags

None

### Input

The account name. Can be an empty string "" for the default account.

### Output

The Kaon address associated with the specified account.

### Examples

CLI Usage:

```bash
getaccountaddress "myaccount"
```

JSON-RPC Call:

```json
{
  "method": "getaccountaddress",
  "params": ["myaccount"]
}
```

### Error Handling

Errors can occur if the account name is invalid.

### Notes

This command is deprecated and should not be used. Avoid using account-related functionalities in newer Kaon applications. Consider using `getnewaddress` instead to generate fresh addresses. Related commands: [ `getnewaddress` ](#getnewaddress), [ `setaccount` ](#setaccount), [ `getaccount` ](#getaccount), [ `getaddressesbyaccount` ](#getaddressesbyaccount)

---

## getaccountinfo

Get contract details including balance, storage data and code.

### Synopsis

```bash
getaccountinfo <address>
```

### Description

Retrieves detailed information about a smart contract account, including its balance, storage data, and bytecode. This command provides insights into the state and functionality of a deployed smart contract.

### Arguments

| Name    | Type   | Required | Description           |
| ------- | ------ | -------- | --------------------- |
| address | string | Yes      | The contract address. |

### Flags

None

### Input

- `address`: The address of the smart contract.

### Output

A JSON object containing contract details.

```json
{
  "address": "contract address",  // Address of the contract
  "balance": n,                   // Balance of the contract
  "storage": {...},                 // Storage data of the contract
  "code": "bytecode"              // Bytecode of the contract
}
```

### Examples

CLI Usage:

```bash
getaccountinfo eb23c0b3e6042821da281a2e2364feb22dd543e3
```

JSON-RPC Call:

```json
{
  "method": "getaccountinfo",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3"]
}
```

### Error Handling

- If the provided contract address is invalid, or if the contract doesn't exist, an error will be returned.

### Notes

This command is helpful for inspecting the current state of a smart contract. Related commands: [ `createcontract` ](#createcontract), [ `sendtocontract` ](#sendtocontract), [ `callcontract` ](#callcontract), [ `listcontracts` ](#listcontracts)

---

## getaddednodeinfo

Returns information about the given added node, or all added nodes (note that onetry addnodes are not listed here). If dns is false, only a list of added nodes will be provided, otherwise connected information will also be available.

### Synopsis

```bash
getaddednodeinfo <dns> (<node>)
```

### Description

Returns information about nodes added using the `addnode` command. The `dns` parameter controls whether to resolve the node's hostname. If `dns` is true, the command will attempt to resolve the hostname and provide connection information. If `dns` is false, only the added node IP addresses/hostnames will be listed. The optional `node` parameter allows retrieval of information for a specific node. If omitted, information for all added nodes is returned.

### Arguments

| Name | Type    | Required | Description                                                                                 |
| ---- | ------- | -------- | ------------------------------------------------------------------------------------------- |
| dns  | boolean | Yes      | Whether to look up the DNS info for the node.                                               |
| node | string  | No       | The node to get information about (optional). If omitted, returns data for all added nodes. |

### Flags

None

### Input

- `dns`: Boolean value (true/false).
- `node`: (Optional) IP address or hostname of the node.

### Output

A JSON array containing information about each added node.

```json
[
  {
    "addednode": "192.168.0.201", // Node IP or hostname
    "connected": true|false,      // Connection status
    "addresses": [             // Connection details (if connected)
      {
        "address": "192.168.0.201:9871", // Node address and port
        "connected": "outbound"         // Connection direction
      },
      // ...
    ]
  },
  // ... more nodes
]
```

### Examples

CLI Usage:

```bash
getaddednodeinfo true
```

```bash
getaddednodeinfo true "192.168.0.201"
```

JSON-RPC Call:

```json
{
  "method": "getaddednodeinfo",
  "params": [true, "192.168.0.201"]
}
```

### Error Handling

- If an invalid `node` parameter is provided, an error message will be returned.

### Notes

The information provided includes the added node, connection status, and addresses if a connection exists. Related commands: [ `addnode` ](#addnode), [ `getpeerinfo` ](#getpeerinfo)

---

## getaddressbalance

Returns the balance for an address(es) (requires addressindex to be enabled).

### Synopsis

```bash
getaddressbalance <argument>
```

### Description

Returns the balance for one or more addresses. Requires the `addressindex` to be enabled. This command provides the balance, total received, and immature balance for the given addresses.

### Arguments

| Name     | Type   | Required | Description                                                |
| -------- | ------ | -------- | ---------------------------------------------------------- |
| argument | object | Yes      | A JSON object with an "addresses" array of Kaon addresses. |

### Flags

None

### Input

A JSON object with the following format:

```json
{
  "addresses": ["address1", "address2", ...] // Array of Kaon addresses
}
```

### Output

A JSON object with the balances for the given addresses.

```json
{
  "balance": "xxx", // Current balance in satoshis
  "received": "xxx", // Total received in satoshis
  "immature": "xxx" // Immature balance in satoshis
}
```

### Examples

CLI Usage:

```bash
getaddressbalance '{"addresses": ["UD1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XX"]}'
```

JSON-RPC Call:

```json
{
  "method": "getaddressbalance",
  "params": [{ "addresses": ["UD1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XX"] }]
}
```

### Error Handling

- If the addressindex is not enabled, the command returns an error.
- If any provided address is invalid, the command might return an error or exclude the invalid address from the results.

### Notes

The balance information is specific to the provided addresses. Ensure that the `addressindex` is enabled to use this command. Related commands: [ `getaddressutxos` ](#getaddressutxos), [ `getreceivedbyaddress` ](#getreceivedbyaddress)

---

## getaddressesbyaccount

DEPRECATED. Returns the list of addresses for the given account.

### Synopsis

```bash
getaddressesbyaccount <account>
```

### Description

**DEPRECATED.** Returns a list of addresses associated with a given account. Account functionality is deprecated and should not be used in new code. This command is provided for backward compatibility.

### Arguments

| Name    | Type   | Required | Description       |
| ------- | ------ | -------- | ----------------- |
| account | string | Yes      | The account name. |

### Flags

None

### Input

- `account`: The name of the account.

### Output

A JSON array of Kaon addresses associated with the account.

### Examples

CLI Usage:

```bash
getaddressesbyaccount "tabby"
```

JSON-RPC Call:

```json
{
  "method": "getaddressesbyaccount",
  "params": ["tabby"]
}
```

### Error Handling

If the specified account doesn't exist or is invalid, an error message will be returned.

### Notes

This command is deprecated. Avoid using accounts in your Kaon applications. For retrieving addresses associated with your wallet, consider using other methods like `listreceivedbyaddress` with a minimum confirmations value of 0. Related commands: [ `getaccount` ](#getaccount), [ `getaccountaddress` ](#getaccountaddress), [ `setaccount` ](#setaccount)

---

## getaddressutxos

Returns all unspent outputs for an address (requires addressindex to be enabled).

### Synopsis

```bash
getaddressutxos <argument>
```

### Description

Returns all unspent transaction outputs (UTXOs) associated with a given set of Kaon addresses. This command requires the `addressindex` to be enabled in the Kaon Core configuration. It can optionally include chain information in the output.

### Arguments

| Name     | Type   | Required | Description                                                   |
| -------- | ------ | -------- | ------------------------------------------------------------- |
| argument | object | Yes      | A JSON object specifying the addresses and chain info option. |

### Flags

None

### Input

A JSON object in the following format:

```json
{
  "addresses": ["address1", "address2", ...],  // Array of Kaon addresses.
  "chainInfo": true|false                     // Include chain information (optional, defaults to false).
}
```

### Output

A JSON object listing the UTXOs and optionally the current chain tip hash and height.

```json
{
  "addresses": [
    {
      "address": "xxx",        // Address base58check encoded
      "txid": "0x",           // Transaction ID
      "height": n,            // Block height
      "outputIndex": n,       // Output index
      "script": "0x",           // Script hex encoded
      "satoshis": n,          // Number of satoshis
      "isStake": true|false    // Is a coinstake output
    },
    // ... more UTXOs
  ],
  "hash": "0x",             //  (Only if chainInfo is true) Tip block hash.
  "height": n              //  (Only if chainInfo is true) Tip block height.
}
```

### Examples

CLI Usage:

```bash
getaddressutxos '{"addresses": ["QD1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XX"]}'
```

```bash
getaddressutxos '{"addresses": ["QD1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XX"], "chainInfo": true}'
```

JSON-RPC Call:

```json
{
  "method": "getaddressutxos",
  "params": [
    { "addresses": ["QD1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XX"], "chainInfo": true }
  ]
}
```

### Error Handling

- Returns an error if addressindex is not enabled.
- If any of the provided addresses are invalid, those addresses might be ignored, or an error might be returned.

### Notes

This command requires the addressindex to be enabled. This might increase the size of the Kaon Core data directory significantly. The returned UTXO information can be used for constructing transactions. Related commands: [ `getaddressbalance` ](#getaddressbalance), [ `listunspent` ](#listunspent)

---

## getbalance

If account is not specified, returns the server's total available balance.
If account is specified (DEPRECATED), returns the balance in the account.
Note that the account "" is not the same as leaving the parameter out.
The server total may be different to the balance in the default "" account.

### Synopsis

```bash
getbalance (<account> <minconf> <includeWatchonly> <includeDelegated>)
```

### Description

Returns the wallet's available balance. The `account` parameter is deprecated and should not be used. You can optionally specify the minimum number of confirmations ( `minconf` ) for transactions to be included in the balance calculation, whether to include watch-only addresses ( `includeWatchonly` ), and whether to include delegated balances ( `includeDelegated` ).

### Arguments

| Name             | Type    | Required | Description                                                 |
| ---------------- | ------- | -------- | ----------------------------------------------------------- |
| account          | string  | No       | **DEPRECATED.** The account name (use "\*").                |
| minconf          | numeric | No       | The minimum number of confirmations (default: 1).           |
| includeWatchonly | boolean | No       | Include watch-only addresses (default: false).              |
| includeDelegated | boolean | No       | Include balances delegated to cold stakers (default: true). |

### Flags

None

### Input

- `account`: **DEPRECATED.** Use "\*".
- `minconf`: Minimum confirmations to include in the balance. Defaults to 1.
- `includeWatchonly`: Include watch-only addresses in the balance calculation. Defaults to false.
- `includeDelegated`: Include delegated stakes in the balance calculation. Defaults to true.

### Output

The total available balance in KAON.

### Examples

CLI Usage:

```bash
getbalance
```

```bash
getbalance "*" 6
```

JSON-RPC Call:

```json
{
  "method": "getbalance",
  "params": ["*", 6]
}
```

### Error Handling

Errors might occur if the provided parameters are invalid (e.g., a negative `minconf` ).

### Notes

The balance reflects the amount available for spending, considering the specified parameters. Related commands: [ `getunconfirmedbalance` ](#getunconfirmedbalance), [ `getwalletinfo` ](#getwalletinfo), [ `listunspent` ](#listunspent)

---

## getbestblockhash

Returns the hash of the best (tip) block in the longest block chain.

### Synopsis

```bash
getbestblockhash
```

### Description

Returns the hash of the current blockchain tip, which represents the most recently added block to the longest valid chain. This is a fundamental command for blockchain interactions, often used to identify the current state of the chain.

### Arguments

None

### Flags

None

### Input

None

### Output

The block hash (hexadecimal string).

### Examples

CLI Usage:

```bash
getbestblockhash
```

JSON-RPC Call:

```json
{
  "method": "getbestblockhash",
  "params": []
}
```

### Error Handling

This command rarely returns errors. Errors might occur in exceptional circumstances, such as if the blockchain is not yet initialized.

### Notes

The returned hash is a unique identifier for the current block at the tip of the blockchain. Related commands: [ `getblock` ](#getblock), [ `getblockcount` ](#getblockcount), [ `getblockchaininfo` ](#getblockchaininfo)

---

## getblock

If verbose is false, returns a string that is serialized, hex-encoded data for block 'hash'.
If verbose is true, returns an Object with information about block <hash>.

### Synopsis

```bash
getblock "<hash>" (<verbose>)
```

### Description

Retrieves information about a specific block identified by its hash. If `verbose` is true (default), the command returns a JSON object with detailed block information. If `verbose` is false, it returns the serialized, hex-encoded block data.

### Arguments

| Name    | Type    | Required | Description                                                                       |
| ------- | ------- | -------- | --------------------------------------------------------------------------------- |
| hash    | string  | Yes      | The hash of the block.                                                            |
| verbose | boolean | No       | Whether to return detailed block information (true, default) or hex data (false). |

### Flags

None

### Input

- `hash`: The block hash as a hexadecimal string.
- `verbose`: Optional boolean, defaults to true.

### Output

- If `verbose` is true: A JSON object with detailed information about the block, including its hash, confirmations, size, height, version, transactions, time, difficulty, previous and next block hashes, and Proof of Stake data.
- If `verbose` is false: A hexadecimal string representing the serialized block data.

### Examples

CLI Usage:

```bash
getblock "00000000000fd08c2fb661d2fcb0d49abb3a91e5f27082ce64feed3b4dede2e2"
```

JSON-RPC Call:

```json
{
  "method": "getblock",
  "params": [
    "00000000000fd08c2fb661d2fcb0d49abb3a91e5f27082ce64feed3b4dede2e2",
    true
  ] // verbose=true is optional (and is default)
}
```

### Error Handling

- If the provided hash is not a valid block hash, or if the block is not found, an error will be returned.

### Notes

This command provides detailed information about blocks, including transaction details, timestamps, and difficulty. Related commands: [ `getbestblockhash` ](#getbestblockhash), [ `getblockhash` ](#getblockhash), [ `getblockheader` ](#getblockheader), [ `getblockcount` ](#getblockcount)

---

## getblockchaininfo

Returns an object containing various state info regarding block chain processing.

### Synopsis

```bash
getblockchaininfo
```

### Description

Returns an object containing various details about the state of the blockchain. This includes the current chain, number of blocks and headers, best block hash, difficulty, verification progress, chainwork, and soft fork status.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with details about the blockchain state. See the original documentation for the complete structure of this JSON object. Key fields include: `chain` (current network name), `blocks` (number of processed blocks), `headers` (number of validated headers), `bestblockhash` , `difficulty` , `verificationprogress` , `chainwork` and `softforks` .

### Examples

CLI Usage:

```bash
getblockchaininfo
```

JSON-RPC Call:

```json
{
  "method": "getblockchaininfo",
  "params": []
}
```

### Error Handling

This command rarely produces errors. If the node is in an unexpected state, an error might be returned.

### Notes

This command provides a comprehensive overview of the current blockchain status and progress. Related commands: [ `getblockcount` ](#getblockcount), [ `getbestblockhash` ](#getbestblockhash), [ `getchaintips` ](#getchaintips)

---

## getblockcount

Returns the number of blocks in the longest block chain.

### Synopsis

```bash
getblockcount
```

### Description

Returns the current height of the blockchain, which represents the number of blocks in the longest valid chain. This is a simple but essential command for tracking the blockchain's progress.

### Arguments

None

### Flags

None

### Input

None

### Output

The current block count as an integer.

### Examples

CLI Usage:

```bash
getblockcount
```

JSON-RPC Call:

```json
{
  "method": "getblockcount",
  "params": []
}
```

### Error Handling

This command rarely returns errors. If the blockchain is not yet initialized, an error might occur.

### Notes

The block count represents the height or length of the blockchain. Related commands: [ `getbestblockhash` ](#getbestblockhash), [ `getblock` ](#getblock), [ `getblockchaininfo` ](#getblockchaininfo)

---

## getblockhash

Returns hash of block in best-block-chain at index provided.

### Synopsis

```bash
getblockhash <height>
```

### Description

Returns the hash of the block at the specified height in the longest blockchain. This command allows retrieving the block hash for any given block height.

### Arguments

| Name   | Type    | Required | Description       |
| ------ | ------- | -------- | ----------------- |
| height | numeric | Yes      | The block height. |

### Flags

None

### Input

- `height`: An integer value representing the block height or index.

### Output

The hexadecimal block hash for the specified height.

### Examples

CLI Usage:

```bash
getblockhash 1000
```

JSON-RPC Call:

```json
{
  "method": "getblockhash",
  "params": [1000]
}
```

### Error Handling

- If the specified height is out of range (e.g., negative or greater than the current block height), an error will be returned.

### Notes

Block height is zero-indexed, meaning the first block (genesis block) has a height of 0. Related commands: [ `getblock` ](#getblock), [ `getbestblockhash` ](#getbestblockhash), [ `getblockheader` ](#getblockheader)

---

## getblockheader

If verbose is false, returns a string that is serialized, hex-encoded data for block 'hash' header.
If verbose is true, returns an Object with information about block <hash> header.

### Synopsis

```bash
getblockheader "<hash>" (<verbose>)
```

### Description

Retrieves information about a specific block header, identified by its hash. If `verbose` is true (the default), the command returns a JSON object containing detailed header information. If `verbose` is false, it returns the serialized, hexadecimal representation of the block header.

### Arguments

| Name    | Type    | Required | Description                                                                        |
| ------- | ------- | -------- | ---------------------------------------------------------------------------------- |
| hash    | string  | Yes      | The hash of the block header.                                                      |
| verbose | boolean | No       | Whether to return detailed header information (true, default) or hex data (false). |

### Flags

None

### Input

- `hash`: The block hash.
- `verbose`: (Optional) A boolean indicating whether to return verbose output or the hex-encoded header. Defaults to true.

### Output

- If verbose is true: A JSON object with information about the block header, including version, previous block hash, Merkle root, timestamp, median time, nonce, and bits.
- If verbose is false: A string containing the serialized, hex-encoded block header data.

### Examples

CLI Usage:

```bash
getblockheader "00000000000fd08c2fb661d2fcb0d49abb3a91e5f27082ce64feed3b4dede2e2"
```

JSON-RPC Call:

```json
{
  "method": "getblockheader",
  "params": [
    "00000000000fd08c2fb661d2fcb0d49abb3a91e5f27082ce64feed3b4dede2e2",
    true
  ] // true (verbose) is optional here
}
```

### Error Handling

- If an invalid block hash is provided or the block is not found, an error message will be returned.

### Notes

The block header contains crucial information about the block, including its link to the previous block and a summary of the transactions it contains (Merkle root). Related commands: [ `getblock` ](#getblock), [ `getblockhash` ](#getblockhash), [ `getblockcount` ](#getblockcount)

---

## getblockindexstats

Returns aggregated BlockIndex data for blocks [height, height+1, height+2, ..., height+range-1].

### Synopsis

```bash
getblockindexstats <height> <range> (<fFeeOnly>)
```

### Description

Returns aggregated data from the block index for a range of blocks. This command is useful for analyzing blockchain statistics over a specific period. It can return details such as transaction counts, sizes, fees, and optionally mint/spend counts for different denominations.

### Arguments

| Name     | Type    | Required | Description                                                      |
| -------- | ------- | -------- | ---------------------------------------------------------------- |
| height   | numeric | Yes      | The starting block height.                                       |
| range    | numeric | Yes      | The number of blocks to include in the range.                    |
| fFeeOnly | boolean | No       | If true, return only fee-related information (default is false). |

### Flags

None

### Input

- `height`: The starting block height.
- `range`: The number of blocks to analyze.
- `fFeeOnly`: (Optional) Boolean, defaults to false. When true, only returns fee-related information

### Output

A JSON object with aggregated block index data. See original documentation for the detailed structure of this object. Key fields include block height range, transaction counts, transaction sizes, total fees, average fee per KB, and if `fFeeOnly` is false, mint and spend counts per denomination.

### Examples

CLI Usage:

```bash
getblockindexstats 1200000 1000
```

JSON-RPC Call:

```json
{
  "method": "getblockindexstats",
  "params": [1200000, 1000]
}
```

### Error Handling

- If an invalid height or range is provided, an error message is returned.

### Notes

The returned data is an aggregation of information from the specified block range. The `fFeeOnly` flag can be used to retrieve only fee-related statistics.

---

## getblocktemplate

If the request parameters include a 'mode' key, that is used to explicitly select between the default 'template' request or a 'proposal'.
It returns data needed to construct a block to work on.
See https://en.bitcoin.it/wiki/BIP_0022 for full specification.

### Synopsis

```bash
getblocktemplate (<jsonrequestobject>)
```

### Description

Returns data necessary for constructing a new block. This command is used by miners to create valid blocks. The optional `jsonrequestobject` allows customizing the template request, for instance, setting the mode to 'template' or 'proposal'. For the complete BIP_0022 specification, refer to: https://en.bitcoin.it/wiki/BIP_0022.

### Arguments

| Name              | Type   | Required | Description                                                             |
| ----------------- | ------ | -------- | ----------------------------------------------------------------------- |
| jsonrequestobject | string | No       | A JSON object to customize the template request (see below for format). |

### Flags

None

### Input

- `jsonrequestobject`: (Optional) A JSON string with the following structure:

```json
{
  "mode": "template"|"proposal", // The mode (optional, defaults to "template")
  "capabilities": ["support", ... ] // Client-side supported features (optional)
}
```

### Output

A JSON object containing the block template. See the original documentation and the BIP_0022 specification for the complete structure of this object. It contains data such as block version, previous block hash, transactions to include, coinbase information, target hash, timestamp, difficulty, and more.

### Examples

CLI Usage:

```bash
getblocktemplate
```

JSON-RPC Call:

```json
{
  "method": "getblocktemplate",
  "params": []
}
```

### Error Handling

If the request for a block template fails, an error message is returned. This may happen if the node is not fully synchronized or if the `jsonrequestobject` is malformed.

### Notes

This is a critical command for miners and is involved in the process of creating new blocks. Refer to the BIP_0022 specification for detailed information.

---

## getbudgetinfo

Show current masternode budgets.

### Synopsis

```bash
getbudgetinfo (<proposal>)
```

### Description

Retrieves information about the current masternode budgets. Provides details about budget proposals, including their name, URL, payment details, vote counts, and validity status. You can optionally specify a `proposal` name to retrieve information about a specific proposal.

### Arguments

| Name     | Type   | Required | Description                                 |
| -------- | ------ | -------- | ------------------------------------------- |
| proposal | string | No       | The name of a specific proposal (optional). |

### Flags

None

### Input

- `proposal`: (Optional) The name of the proposal you want information about.

### Output

If a proposal name is provided, it returns the budget information for that proposal as a JSON object. If no proposal name is provided, it returns an array of JSON objects, one for each current budget proposal. Refer to the original documentation for the detailed structure of the output. Key information includes the proposal name, URL, hash, payment address, vote counts (yeas, nays, abstains), total payment amount, and validity status.

### Examples

CLI Usage:

```bash
getbudgetinfo
```

```bash
getbudgetinfo "myproposal"
```

JSON-RPC Call:

```json
{
  "method": "getbudgetinfo",
  "params": ["myproposal"] // "myproposal" is optional
}
```

### Error Handling

- If an invalid proposal name is given, an error message will be returned.

### Notes

This command is used to review the status and details of masternode budget proposals. Related commands: [ `getbudgetprojection` ](#getbudgetprojection), [ `getbudgetvotes` ](#getbudgetvotes), [ `preparebudget` ](#preparebudget), [ `submitbudget` ](#submitbudget), [ `mnbudgetvote` ](#mnbudgetvote), [ `mnfinalbudget` ](#mnfinalbudget)

---

## getbudgetprojection

Show the projection of which proposals will be paid the next cycle.

### Synopsis

```bash
getbudgetprojection
```

### Description

Shows which budget proposals are projected to be paid in the next budget cycle. This command helps understand how the available budget is allocated across different proposals.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array of objects. Each object represents a budget proposal and includes details such as its name, URL, payment information, vote counts, total payment, and whether it's projected to be paid in the next cycle. See the original documentation for the full structure of the returned JSON. Important fields include `Name` , `URL` , `Hash` , `PaymentAddress` , `Yeas` , `Nays` , `Abstains` , `TotalPayment` , `MonthlyPayment` , `IsEstablished` , `Alloted` , `TotalBudgetAlloted` .

### Examples

CLI Usage:

```bash
getbudgetprojection
```

JSON-RPC Call:

```json
{
  "method": "getbudgetprojection",
  "params": []
}
```

### Error Handling

Errors might be returned under exceptional circumstances, for example, if the node can't determine the next budget cycle or if there's an issue with the budget data.

### Notes

The projection is based on the current status of proposals and available funds. Related commands: [ `getbudgetinfo` ](#getbudgetinfo), [ `getbudgetvotes` ](#getbudgetvotes), [ `checkbudgets` ](#checkbudgets)

---

## getbudgetvotes

Print vote information for a budget proposal.

### Synopsis

```bash
getbudgetvotes <proposal-name>
```

### Description

Returns the vote information for a specific budget proposal. This allows reviewing how masternodes have voted on a particular proposal.

### Arguments

| Name          | Type   | Required | Description           |
| ------------- | ------ | -------- | --------------------- |
| proposal-name | string | Yes      | Name of the proposal. |

### Flags

None

### Input

- `proposal-name`: The name of the budget proposal.

### Output

A JSON array. Each element in the array is a JSON object representing a vote and includes the masternode ID, vote hash, the vote cast ("YES" or "NO"), the timestamp of the vote, and the vote's validity status.

### Examples

CLI Usage:

```bash
getbudgetvotes "test-proposal"
```

JSON-RPC Call:

```json
{
  "method": "getbudgetvotes",
  "params": ["test-proposal"]
}
```

### Error Handling

- If an invalid proposal name is provided or the proposal is not found, an error message will be returned.

### Notes

This command helps understand the voting breakdown for a budget proposal. Related commands: [ `getbudgetinfo` ](#getbudgetinfo), [ `getbudgetprojection` ](#getbudgetprojection), [ `mnbudgetvote` ](#mnbudgetvote)

---

## getchaintips

Return information about all known tips in the block tree, including the main chain as well as orphaned branches.

### Synopsis

```bash
getchaintips
```

### Description

Provides information about all known chain tips in the block tree. This includes the main chain and any orphaned or forked branches. This command is useful for analyzing the blockchain's structure and identifying any potential forks or inconsistencies.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array. Each element in the array represents a chain tip and includes information such as:

- `height`: The block height of the tip.
- `hash`: The block hash of the tip.
- `branchlen`: The length of the branch (0 for the main chain, >0 for forks).
- `status`: The status of the chain ("active", "valid-fork", "valid-headers", "headers-only", "invalid"). For detailed explanation of these statuses, see the original documentation.

### Examples

CLI Usage:

```bash
getchaintips
```

JSON-RPC Call:

```json
{
  "method": "getchaintips",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under normal circumstances.

### Notes

This command provides a way to explore the block tree and identify forks or inconsistencies in the blockchain. Related commands: [ `getblockchaininfo` ](#getblockchaininfo), [ `getblock` ](#getblock)

---

## getcoldstakingbalance

If account is not specified, returns the server's total available cold balance.
If account is specified (DEPRECATED), returns the cold balance in the account.
Note that the account "" is not the same as leaving the parameter out.
The server total may be different to the balance in the default "" account.

### Synopsis

```bash
getcoldstakingbalance (<account>)
```

### Description

Returns the wallet's total balance that is currently available for cold staking. This includes the balance held in P2CS (Pay-to-Cold-Staking) scripts. The `account` parameter is deprecated and should not be used. If no account is specified, the total cold staking balance for the whole wallet is returned (use "\*" for "all" if you use the account parameter).

### Arguments

| Name    | Type   | Required | Description                                           |
| ------- | ------ | -------- | ----------------------------------------------------- |
| account | string | No       | **DEPRECATED**. The account name. Use "\*" for "all". |

### Flags

None

### Input

- `account`: **DEPRECATED.** Use "\*".

### Output

The total cold staking balance available in the wallet, as a numeric value in KAON.

### Examples

CLI Usage:

```bash
getcoldstakingbalance
```

JSON-RPC Call:

```json
{
  "method": "getcoldstakingbalance",
  "params": [] // or ["*"]
}
```

### Error Handling

If any error occurs while retrieving the balance, an error message will be returned.

### Notes

This command provides a summary of the balance available for cold staking in the wallet. Relatd commands: [ `delegatestake` ](#delegatestake), [ `listcoldutxos` ](#listcoldutxos), [ `getdelegatedbalance` ](#getdelegatedbalance), [ `getbalance` ](#getbalance), [ `getwalletinfo` ](#getwalletinfo)

---

## getconnectioncount

Returns the number of connections to other nodes.

### Synopsis

```bash
getconnectioncount
```

### Description

Returns the number of active connections your Kaon node has to other peers in the network. This is useful for monitoring network connectivity and health.

### Arguments

None

### Flags

None

### Input

None

### Output

The number of connections as an integer.

### Examples

CLI Usage:

```bash
getconnectioncount
```

JSON-RPC Call:

```json
{
  "method": "getconnectioncount",
  "params": []
}
```

### Error Handling

This command is unlikely to produce errors under normal conditions.

### Notes

The connection count reflects the number of active peer connections at the moment the command is executed. Related commands: [ `getpeerinfo` ](#getpeerinfo), [ `addnode` ](#addnode), [ `disconnectnode` ](#disconnectnode)

---

## getdelegatedbalance

If account is not specified, returns the server's total available delegated balance (sum of all utxos delegated
to a cold staking address to stake on behalf of addresses of this wallet).
If account is specified (DEPRECATED), returns the cold balance in the account.
Note that the account "" is not the same as leaving the parameter out.
The server total may be different to the balance in the default "" account.

### Synopsis

```bash
getdelegatedbalance (<account>)
```

### Description

Returns the total balance delegated to cold staking addresses associated with the wallet. This includes the sum of all UTXOs delegated to a cold staking address that belongs to this wallet. The `account` parameter is deprecated and should not be used. If no account is specified, returns the total delegated balance for the wallet (use "\*" if using the account parameter).

### Arguments

| Name    | Type   | Required | Description               |
| ------- | ------ | -------- | ------------------------- |
| account | string | No       | **DEPRECATED.** Use "\*". |

### Flags

None

### Input

- `account`: **DEPRECATED**. Use "\*".

### Output

The total delegated balance in KAON.

### Examples

CLI Usage:

```bash
getdelegatedbalance
```

JSON-RPC Call:

```json
{
  "method": "getdelegatedbalance",
  "params": [] // or ["*"]
}
```

### Error Handling

Any errors encountered during balance retrieval result in an error message.

### Notes

This command summarizes the total balance delegated by the wallet for cold staking. Related commands: [ `delegatestake` ](#delegatestake), [ `listcoldutxos` ](#listcoldutxos), [ `getcoldstakingbalance` ](#getcoldstakingbalance), [ `getbalance` ](#getbalance), [ `getwalletinfo` ](#getwalletinfo)

---

## getdifficulty

Returns the proof-of-work difficulty as a multiple of the minimum difficulty.

### Synopsis

```bash
getdifficulty
```

### Description

Returns the current proof-of-work difficulty. This value indicates how difficult it is to mine a new block on the Kaon network, expressed as a multiple of the minimum difficulty.

### Arguments

None

### Flags

None

### Input

None

### Output

The current proof-of-work difficulty as a floating-point number.

### Examples

CLI Usage:

```bash
getdifficulty
```

JSON-RPC Call:

```json
{
  "method": "getdifficulty",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under normal circumstances.

### Notes

The difficulty adjusts dynamically based on network hashrate to maintain a consistent block time. Related commands: [ `getblockchaininfo` ](#getblockchaininfo), [ `getblock` ](#getblock), [ `getmininginfo` ](#getmininginfo) (if available in Kaon)

---

## getfeeinfo

Returns details of transaction fees over the last n blocks.

### Synopsis

```bash
getfeeinfo <blocks>
```

### Description

Returns statistical information about transaction fees over a specified number of blocks. This data includes the total number of transactions, the total size of transactions in bytes, the total fees collected, the average fee per kilobyte, and the recommended fee per kilobyte for high-priority transactions.

### Arguments

| Name   | Type | Required | Description                      |
| ------ | ---- | -------- | -------------------------------- |
| blocks | int  | Yes      | The number of blocks to analyze. |

### Flags

None

### Input

- `blocks`: An integer specifying the number of recent blocks to consider for the fee statistics.

### Output

A JSON object containing fee information.

```json
{
  "txcount": xxxxx,                // Transaction count
  "txbytes": xxxxx,                // Total transaction size in bytes
  "ttlfee": xxxxx,                 // Total fees collected
  "feeperkb": xxxxx,               // Average fee per kilobyte
  "rec_highpriorityfee_perkb": xxxxx // Recommended high-priority fee per kilobyte
}

```

### Examples

CLI Usage:

```bash
getfeeinfo 5
```

JSON-RPC Call:

```json
{
  "method": "getfeeinfo",
  "params": [5]
}
```

### Error Handling

If an invalid number of `blocks` is provided, an error message might be returned.

### Notes

This command provides insight into recent transaction fee trends and helps determine an appropriate fee for new transactions. Related commands: [ `estimatefee` ](#estimatefee), [ `settxfee` ](#settxfee)

---

## getgenerate

Return if the server is set to generate coins or not. The default is false. It is set with the command line argument -gen (or kaon.conf setting gen). It can also be set with the setgenerate call.

### Synopsis

```bash
getgenerate
```

### Description

Indicates whether the Kaon Core node is currently generating (mining) blocks. This setting can be configured using the `-gen` command-line argument, the `gen` setting in the `kaon.conf` file, or the `setgenerate` command.

### Arguments

None

### Flags

None

### Input

None

### Output

- `true`: If block generation is enabled.
- `false`: If block generation is disabled.

### Examples

CLI Usage:

```bash
getgenerate
```

JSON-RPC Call:

```json
{
  "method": "getgenerate",
  "params": []
}
```

### Error Handling

Unlikely to return errors under normal conditions.

### Notes

Block generation is typically disabled on nodes that are not intended for mining. Related commands: [ `setgenerate` ](#setgenerate), [ `generate` ](#generate), [ `generatetoaddress` ](#generatetoaddress)

---

## gethashespersec

Returns a recent hashes per second performance measurement while generating.
See the getgenerate and setgenerate calls to turn generation on and off.

### Synopsis

```bash
gethashespersec
```

### Description

Returns the recent hashing performance of the node in hashes per second. This command only returns a meaningful value if the node is actively generating (mining) blocks. If generation is off, it returns 0.

### Arguments

None

### Flags

None

### Input

None

### Output

The current hashrate in hashes per second, or 0 if generation is not active.

### Examples

CLI Usage:

```bash
gethashespersec
```

JSON-RPC Call:

```json
{
  "method": "gethashespersec",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under typical operation.

### Notes

The reported hashrate represents a recent measurement of mining performance. Related commands: [ `getgenerate` ](#getgenerate), [ `setgenerate` ](#setgenerate)

---

## gethexaddress

Converts a base58 pubkeyhash address to a hex address for use in smart contracts.

### Synopsis

```bash
gethexaddress <address>
```

### Description

Converts a base58 encoded Kaon address to its hexadecimal representation. This is useful for interacting with smart contracts, which often require addresses in hexadecimal format.

### Arguments

| Name    | Type   | Required | Description                      |
| ------- | ------ | -------- | -------------------------------- |
| address | string | Yes      | The base58 encoded Kaon address. |

### Flags

None

### Input

- `address`: A base58 encoded Kaon address string.

### Output

The hexadecimal representation of the Kaon address suitable for use in smart contracts.

### Examples

CLI Usage:

```bash
gethexaddress "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6"
```

JSON-RPC Call:

```json
{
  "method": "gethexaddress",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6"]
}
```

### Error Handling

If the input is not a valid base58 encoded address, an error message is returned.

### Notes

This conversion is crucial for using Kaon addresses in smart contracts. This is the reverse operation of `fromhexaddress` . Related commands: [ `fromhexaddress` ](#fromhexaddress), [ `validateaddress` ](#validateaddress)

---

## getinfo

Returns an object containing various state info.

### Synopsis

```bash
getinfo
```

### Description

Returns an object containing various state information about the Kaon Core node and wallet. This includes version information, wallet balance, staking status, block count, network connections, and other relevant details.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object containing general information about the Kaon Core node and the wallet's status. Refer to the original documentation for a comprehensive list of fields in this JSON object. Important fields include: `version` , `protocolversion` , `walletversion` , `balance` , `staking status` , `blocks` , `timeoffset` , `connections` , `proxy` , `difficulty` , `testnet` , `moneysupply` , `keypoololdest` , `keypoolsize` , `unlocked_until` , `paytxfee` , `relayfee` , `errors` .

### Examples

CLI Usage:

```bash
getinfo
```

JSON-RPC Call:

```json
{
  "method": "getinfo",
  "params": []
}
```

### Error Handling

This command rarely returns errors. Errors could occur under unusual circumstances, such as if the node or wallet is in an unexpected state.

### Notes

Provides a comprehensive overview of the current status of the node and wallet. This command is helpful for monitoring and debugging. Related commands: [ `getblockchaininfo` ](#getblockchaininfo), [ `getnetworkinfo` ](#getnetworkinfo), [ `getwalletinfo` ](#getwalletinfo)

---

## getmasternodecount

Get masternode count values.

### Synopsis

```bash
getmasternodecount
```

### Description

Provides counts related to masternodes, including the total number of masternodes, stable masternodes, obfuscation-compatible masternodes, enabled masternodes, and masternodes currently in the queue.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object providing masternode counts.

```json
{
  "total": n,        // Total masternodes
  "stable": n,       // Stable masternodes
  "obfcompat": n,    // Obfuscation-compatible masternodes
  "enabled": n,      // Enabled masternodes
  "inqueue": n       // Masternodes in queue
}
```

### Examples

CLI Usage:

```bash
getmasternodecount
```

JSON-RPC Call:

```json
{
  "method": "getmasternodecount",
  "params": []
}
```

### Error Handling

Unlikely to return errors under normal conditions.

### Notes

Use this command to get an overview of the masternode network. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodestatus` ](#getmasternodestatus), [ `masternodecurrent` ](#masternodecurrent)

---

## getmasternodeoutputs

Print all masternode transaction outputs.

### Synopsis

```bash
getmasternodeoutputs
```

### Description

Returns a list of all masternode transaction outputs. This information identifies the transactions and specific outputs that hold the collateral for masternodes.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array. Each element represents a masternode output and contains:

- `txhash`: The transaction hash.
- `outputidx`: The output index within the transaction.

### Examples

CLI Usage:

```bash
getmasternodeoutputs
```

JSON-RPC Call:

```json
{
  "method": "getmasternodeoutputs",
  "params": []
}
```

### Error Handling

This command might return an error in situations where the masternode information is not available.

### Notes

This command is helpful for identifying the UTXOs that serve as collateral for masternodes. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodestatus` ](#getmasternodestatus)

---

## getmasternodescores

Print list of winning masternode by score.

### Synopsis

```bash
getmasternodescores (<blocks>)
```

### Description

Returns a list of winning masternodes ranked by score, for a specified number of blocks. The score is determined by a combination of factors, and this information is relevant to masternode selection and reward distribution.

### Arguments

| Name   | Type    | Required | Description                                       |
| ------ | ------- | -------- | ------------------------------------------------- |
| blocks | numeric | No       | The number of blocks to consider (default is 10). |

### Flags

None

### Input

- `blocks`: (Optional) Number of recent blocks to consider for scores. Defaults to 10.

### Output

A JSON object where keys represent block heights, and values are the corresponding masternode hashes.

### Examples

CLI Usage:

```bash
getmasternodescores
```

JSON-RPC Call:

```json
{
  "method": "getmasternodescores",
  "params": [] // or [<number of blocks>]
}
```

### Error Handling

If any errors occur while retrieving masternode scores or an invalid number of `blocks` is specified, an error message will be returned.

### Notes

This command provides information relevant to masternode rewards and ranking. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodewinners` ](#getmasternodewinners)

---

## getmasternodestatus

Print masternode status.

### Synopsis

```bash
getmasternodestatus
```

### Description

Returns the status information for your masternode. This provides details like the collateral transaction, network address, payment address, and the current status of the masternode.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with masternode status details.

```json
{
  "txhash": "xxxx",      // Collateral transaction hash
  "outputidx": n,        // Collateral transaction output index
  "netaddr": "xxxx",     // Masternode network address
  "addr": "xxxx",        // Kaon address for payments
  "status": "xxxx",      // Masternode status (e.g., ENABLED)
  "message": "xxxx"      // Status message (if any)
}

```

### Examples

CLI Usage:

```bash
getmasternodestatus
```

JSON-RPC Call:

```json
{
  "method": "getmasternodestatus",
  "params": []
}
```

### Error Handling

If there is an issue retrieving the masternode status (e.g. if the masternode is not running or configured correctly), an error message will be returned.

### Notes

This command provides the operational status of your masternode. Related commands: [ `listmasternodes` ](#listmasternodes), [ `startmasternode` ](#startmasternode)

---

## getmasternodewinners

Print the masternode winners for the last n blocks.

### Synopsis

```bash
getmasternodewinners (<blocks> "<filter>")
```

### Description

Returns the winning masternodes for a specified number of recent blocks. This information is related to masternode rewards and block validation. You can optionally provide a filter to restrict the results to masternodes with addresses matching the filter string.

### Arguments

| Name   | Type    | Required | Description                                                      |
| ------ | ------- | -------- | ---------------------------------------------------------------- |
| blocks | numeric | No       | The number of previous blocks to show winners for (default: 10). |
| filter | string  | No       | A filter string to match masternode addresses (optional).        |

### Flags

None

### Input

- `blocks`: (Optional) The number of recent blocks to consider. Default is 10.
- `filter`: (Optional) A string to filter masternode addresses by.

### Output

The format of the output depends on whether there is a single winner per block or multiple winners. If only a single winner per block is found:

```json
[
  {
    "nHeight": n,         // Block height
    "winner": {
      "address": "xxxx",  // Kaon masternode address
      "nVotes": n        // Number of votes
    }
  },
  // ... more winners
]
```

If multiple winners are present:

```json
[
  {
    "nHeight": n,       // Block height
    "winner": [         // Array of winners at this height
      {
        "address": "xxxx",  // Kaon masternode address
        "nVotes": n        // Number of votes
      },
       // ... more winners at this height
    ]
  },
  // ... more blocks with winners
]

```

### Examples

CLI Usage:

```bash
getmasternodewinners
```

```bash
getmasternodewinners 20 "DMJRSsuU"
```

JSON-RPC Call:

```json
{
  "method": "getmasternodewinners",
  "params": [20, "DMJRSsuU"] // Parameters are optional
}
```

### Error Handling

If an invalid number of `blocks` or an invalid filter string is provided, an error message will be returned.

### Notes

This command shows which masternodes have won rewards for recent blocks. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodescores` ](#getmasternodescores)

---

## getmempoolinfo

Returns details on the active state of the TX memory pool.

### Synopsis

```bash
getmempoolinfo
```

### Description

Returns information about the current state of the transaction memory pool (mempool). This includes the current number of transactions in the mempool and their total size in bytes. The mempool holds transactions that have been broadcast to the network but not yet included in a block.

### Arguments

None

### Flags

None

### Input

None

### Output

Returns a JSON object containing mempool details:

```json
{
  "size": xxxxx,  // Number of transactions in the mempool
  "bytes": xxxxx // Total size of transactions in bytes
}
```

### Examples

CLI Usage:

```bash
getmempoolinfo
```

JSON-RPC Call:

```json
{
  "method": "getmempoolinfo",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under normal operation.

### Notes

The mempool information provides insights into the current transaction activity and network congestion. Related commands: [ `getrawmempool` ](#getrawmempool)

---

## getmintsinblocks

Returns the number of mints of a certain denomination occurred in blocks [height, height+1, height+2, ..., height+range-1].

### Synopsis

```bash
getmintsinblocks <height> <range> <coinDenomination>
```

### Description

Returns the number of mints of a specific coin denomination within a given range of blocks. This command is helpful for analyzing the minting patterns of different denominations.

### Arguments

| Name             | Type    | Required | Description                                   |
| ---------------- | ------- | -------- | --------------------------------------------- |
| height           | numeric | Yes      | The starting block height for the range.      |
| range            | numeric | Yes      | The number of blocks to include in the range. |
| coinDenomination | numeric | Yes      | The coin denomination to count mints for.     |

### Flags

None

### Input

- `height`: The starting block height (inclusive).
- `range`: The number of blocks to analyze.
- `coinDenomination`: The denomination to count mints for (e.g., 1, 5, 10, etc.).

### Output

A JSON object providing information about the starting and ending block of the range, as well as the number of mints for the specified denomination.

```json
{
  "Starting block": "x", // Starting block height
  "Ending block": "x", // Ending block height
  "Number of d-denom mints": "x" // Mint count for the denomination
}
```

### Examples

CLI Usage:

```bash
getmintsinblocks 1200000 1000 5
```

JSON-RPC Call:

```json
{
  "method": "getmintsinblocks",
  "params": [1200000, 1000, 5]
}
```

### Error Handling

- If the provided height or range is invalid (e.g., negative or exceeding the current blockchain height), an error message will be returned.
- If the specified coin denomination is invalid, an error message might be returned.

### Notes

The command returns the total mint count for the specified denomination within the given range of blocks. Related commands: [ `getblockindexstats` ](#getblockindexstats)

---

## getnettotals

Returns information about network traffic, including bytes in, bytes out, and current time.

### Synopsis

```bash
getnettotals
```

### Description

Returns statistics about network traffic, including the total number of bytes received and sent by the Kaon Core node, along with the current time in milliseconds.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with network traffic data.

```json
{
  "totalbytesrecv": n,   // Total bytes received
  "totalbytessent": n,   // Total bytes sent
  "timemillis": t        // Current time in milliseconds
}

```

### Examples

CLI Usage:

```bash
getnettotals
```

JSON-RPC Call:

```json
{
  "method": "getnettotals",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under normal circumstances.

### Notes

This command provides a snapshot of network traffic activity. Related commands: [ `getnetworkinfo` ](#getnetworkinfo), [ `getpeerinfo` ](#getpeerinfo)

---

## getnetworkhashps

Returns the estimated network hashes per second based on the last n blocks.
Pass in [blocks] to override # of blocks, -1 specifies since last difficulty change.
Pass in [height] to estimate the network speed at the time when a certain block was found.

### Synopsis

```bash
getnetworkhashps (<blocks> <height>)
```

### Description

Estimates the network hashrate (hashing power) based on recent blocks. The `blocks` parameter allows you to specify the number of blocks to consider for the calculation (default is 120). A value of -1 indicates using all blocks since the last difficulty change. The `height` parameter allows estimating the hashrate at a specific block height. If omitted, the current height is used.

### Arguments

| Name   | Type    | Required | Description                                                                                                      |
| ------ | ------- | -------- | ---------------------------------------------------------------------------------------------------------------- |
| blocks | numeric | No       | The number of blocks to use for the estimate (default: 120, -1 for blocks since last difficulty adjustment).     |
| height | numeric | No       | The block height at which to estimate the network speed (optional). If omitted, defaults to -1 (current height). |

### Flags

None

### Input

- `blocks`: (Optional) The number of blocks to use in the calculation. Defaults to 120, or -1 for blocks since the last difficulty change.
- `height`: (Optional) The block height to estimate hashrate at. Defaults to -1 (the current tip).

### Output

The estimated network hashrate in hashes per second.

### Examples

CLI Usage:

```bash
getnetworkhashps
```

JSON-RPC Call:

```json
{
  "method": "getnetworkhashps",
  "params": [] // or [<blocks>, <height>]
}
```

### Error Handling

If an invalid number of `blocks` or `height` is provided, an error message might be returned.

### Notes

The estimated network hashrate provides a measure of the overall computational power dedicated to mining on the Kaon network. Related commands: [ `getdifficulty` ](#getdifficulty), [ `getblockchaininfo` ](#getblockchaininfo)

---

## getnetworkinfo

Returns an object containing various state info regarding P2P networking.

### Synopsis

```bash
getnetworkinfo
```

### Description

Provides information about the peer-to-peer (P2P) networking aspects of the Kaon Core node. This includes details like node version, network protocol version, local services, connected peers, network connections, relay fee, and local addresses.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with details about the network. Refer to the original documentation for a full description of fields included in this JSON object. Key information includes: `version` , `subversion` , `protocolversion` , `localservices` , `timeoffset` , `connections` , `networks` (an array with details about each connected network), `relayfee` , and `localaddresses` .

### Examples

CLI Usage:

```bash
getnetworkinfo
```

JSON-RPC Call:

```json
{
  "method": "getnetworkinfo",
  "params": []
}
```

### Error Handling

Unlikely to produce errors in normal operation.

### Notes

Provides a comprehensive overview of the P2P networking status of the Kaon Core node. Related commands: [ `getpeerinfo` ](#getpeerinfo), [ `addnode` ](#addnode), [ `getconnectioncount` ](#getconnectioncount)

---

## getnewaddress

Returns a new Kaon address for receiving payments.
If 'label' is specified, it is added to the address book
so payments received with the address will be associated with 'label'.

### Synopsis

```bash
getnewaddress (<"label"> <"address_type">)
```

### Description

Generates a new Kaon address that can be used to receive payments. You can optionally provide a label to associate with the address. The address type can also be specified (legacy, p2sh-segwit, bech32, or bech32m).

### Arguments

| Name         | Type   | Required | Description                                                                                                   |
| ------------ | ------ | -------- | ------------------------------------------------------------------------------------------------------------- |
| label        | string | No       | A label to associate with the new address (optional).                                                         |
| address_type | string | No       | The address type to generate ("legacy", "p2sh-segwit", "bech32", or "bech32m", optional, defaults to legacy). |

### Flags

None

### Input

- `label`: (Optional) A string to label the address.
- `address_type`: (Optional) The type of address to generate.

### Output

The newly generated Kaon address.

### Examples

CLI Usage:

```bash
getnewaddress
```

```bash
getnewaddress "donation_address" "bech32"
```

JSON-RPC Call:

```json
{
  "method": "getnewaddress",
  "params": ["donation_address", "bech32"] // Parameters are optional
}
```

### Error Handling

Errors might occur if an invalid address type is specified.

### Notes

It's good practice to generate a new address for each transaction to enhance privacy. Related commands: [ `validateaddress` ](#validateaddress), [ `listreceivedbyaddress` ](#listreceivedbyaddress)

---

## getnewleasingaddress

Returns a new Kaon leasing address for receiving leases.

### Synopsis

```bash
getnewleasingaddress
```

### Description

Generates a new Kaon leasing address. Leasing allows users to lease their KAON for mining to other users.

### Arguments

None

### Flags

None

### Input

None

### Output

The newly generated Kaon leasing address.

### Examples

CLI Usage:

```bash
getnewleasingaddress
```

JSON-RPC Call:

```json
{
  "method": "getnewleasingaddress",
  "params": []
}
```

### Error Handling

Errors might be returned if address generation fails.

### Notes

Leasing addresses are specifically designed for leasing KAON for mining purposes. Related command: [ `leasetoaddress` ](#leasetoaddress)

---

## getnewstakingaddress

Returns a new Kaon cold staking address for receiving delegated cold stakes.

### Synopsis

```bash
getnewstakingaddress (<"account">)
```

### Description

Generates a new Kaon address for cold staking. Cold staking allows users to delegate their staking power without keeping their wallets online. The `account` parameter is deprecated and should not be used.

### Arguments

| Name    | Type   | Required | Description              |
| ------- | ------ | -------- | ------------------------ |
| account | string | No       | **DEPRECATED.** Ignored. |

### Flags

None

### Input

- `account`: **DEPRECATED**. Ignored.

### Output

The newly generated Kaon staking address.

### Examples

CLI Usage:

```bash
getnewstakingaddress
```

JSON-RPC Call:

```json
{
  "method": "getnewstakingaddress",
  "params": []
}
```

### Error Handling

Errors might be returned if address generation fails.

### Notes

Staking addresses are used for cold staking, where staking rights are delegated to another online wallet. Related command: [ `delegatestake` ](#delegatestake)

---

## getnextsuperblock

Print the next super block height.

### Synopsis

```bash
getnextsuperblock
```

### Description

Returns the height (block number) of the next superblock. Superblocks are special blocks at which certain events occur, such as budget payments or other governance-related actions.

### Arguments

None

### Flags

None

### Input

None

### Output

The block height of the next superblock.

### Examples

CLI Usage:

```bash
getnextsuperblock
```

JSON-RPC Call:

```json
{
  "method": "getnextsuperblock",
  "params": []
}
```

### Error Handling

Errors might occur if the node is unable to determine the next superblock height (e.g., if the blockchain is not synchronized).

### Notes

Superblocks play a crucial role in Kaon's governance and budget mechanisms.

---

## getpeerinfo

Returns data about each connected network node as a json array of objects.

### Synopsis

```bash
getpeerinfo
```

### Description

Returns information about each connected peer in the Kaon P2P network. This information includes details such as the peer's IP address and port, connection status, version, and other network statistics. This command is essential for monitoring network connections and troubleshooting network issues.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array of objects. Each object represents a connected peer and includes a variety of details about the connection. Refer to the original documentation for a comprehensive list of the fields in these peer objects. Key fields include `id` , `addr` , `services` , `lastsend` , `lastrecv` , `bytessent` , `bytesrecv` , `conntime` , `pingtime` , `version` , `subver` , `inbound` , `startingheight` , and `banscore` .

### Examples

CLI Usage:

```bash
getpeerinfo
```

JSON-RPC Call:

```json
{
  "method": "getpeerinfo",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under normal circumstances. Errors may occur if the node has problems accessing peer information.

### Notes

Provides real-time information about the peers connected to your node, useful for network diagnostics. Related commands: [ `addnode` ](#addnode), [ `disconnectnode` ](#disconnectnode), [ `getconnectioncount` ](#getconnectioncount)

---

## getpoolinfo

Returns anonymous pool-related information.

### Synopsis

```bash
getpoolinfo
```

### Description

Returns information about the anonymous transaction pool. This pool is used for privacy-enhancing features. This includes the address of the current masternode serving the pool, the state of the pool, the number of entries in the pool, and the number of accepted entries.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with details about the anonymous pool.

```json
{
  "current": "addr",  // Current masternode address
  "state": xxxx,        // Pool state
  "entries": xxxx,      // Number of entries
  "accepted": xxxx     // Number of accepted entries
}
```

### Examples

CLI Usage:

```bash
getpoolinfo
```

JSON-RPC Call:

```json
{
  "method": "getpoolinfo",
  "params": []
}
```

### Error Handling

If the pool information is not available or if an error occurs, an error message is returned.

### Notes

Provides insight into the current status and activity of the anonymous transaction pool.

---

## getpubkey

Returns public key for argument "secret".

### Synopsis

```bash
getpubkey "<secret>"
```

### Description

Returns the public key corresponding to a given private key ("secret").

### Arguments

| Name   | Type   | Required | Description                   |
| ------ | ------ | -------- | ----------------------------- |
| secret | string | Yes      | The private key (WIF format). |

### Flags

None

### Input

- `secret`: The private key (Wallet Import Format) to derive the public key from.

### Output

The public key as a hexadecimal string.

### Examples

CLI Usage:

```bash
getpubkey "cXY2zR59bo1oqiNr49B73n8GJ8dYSF8dd"
```

JSON-RPC Call:

```json
{
  "method": "getpubkey",
  "params": ["cXY2zR59bo1oqiNr49B73n8GJ8dYSF8dd"]
}
```

### Error Handling

Returns an error if the provided secret is invalid or if the public key cannot be derived.

---

## getrawchangeaddress

Returns a new Kaon address, for receiving change.
This is for use with raw transactions, NOT normal use.

### Synopsis

```bash
getrawchangeaddress
```

### Description

Generates a new address that can be used as a change address for raw transactions. Change addresses are used to receive the remaining funds from a transaction after accounting for the amount being sent and the transaction fee.

### Arguments

None

### Flags

None

### Input

None

### Output

A new Kaon change address.

### Examples

CLI Usage:

```bash
getrawchangeaddress
```

JSON-RPC Call:

```json
{
  "method": "getrawchangeaddress",
  "params": []
}
```

### Error Handling

Errors may be returned if a new change address cannot be generated (e.g. due to wallet issues).

### Notes

This command is intended for creating raw transactions, where you have manual control over inputs, outputs, and change addresses. Related commands: [ `createrawtransaction` ](#createrawtransaction), [ `decoderawtransaction` ](#decoderawtransaction), [ `signrawtransactionwithkey` ](#signrawtransactionwithkey)

---

## getrawmempool

Returns all transaction ids in memory pool as a json array of string transaction ids.

### Synopsis

```bash
getrawmempool (<verbose>)
```

### Description

Returns information about the transactions currently residing in the memory pool (mempool). The `verbose` parameter controls the level of detail in the output. If `verbose` is false (default), the command returns a JSON array of transaction IDs. If `verbose` is true, the command returns a JSON object with more detailed information about each transaction.

### Arguments

| Name    | Type    | Required | Description                                              |
| ------- | ------- | -------- | -------------------------------------------------------- |
| verbose | boolean | No       | Whether to return detailed information (default: false). |

### Flags

None

### Input

- `verbose`: (Optional) A boolean to indicate whether detailed information is required. Default is false.

### Output

- If `verbose` is false: A JSON array of transaction IDs (hexadecimal strings) representing the transactions in the mempool.
- If `verbose` is true: A JSON object where keys are transaction IDs, and values are objects containing details about each transaction (size, fee, time, height, dependencies, etc.).

### Examples

CLI Usage:

```bash
getrawmempool
```

```bash
getrawmempool true
```

JSON-RPC Call:

```json
{
  "method": "getrawmempool",
  "params": [true]
}
```

### Error Handling

This command is unlikely to produce errors under normal conditions.

### Notes

This command provides a way to inspect the current set of unconfirmed transactions in the mempool. Related commands: [ `getmempoolinfo` ](#getmempoolinfo), [ `gettransaction` ](#gettransaction)

---

## getrawtransaction

NOTE: By default this function only works sometimes. This is when the tx is in the mempool
or there is an unspent output in the utxo for this transaction. To make it always work,
you need to maintain a transaction index, using the -txindex command line option.
Return the raw transaction data.
If verbose is 'true', returns an Object with information about 'txid'.
If verbose is 'false' or omitted, returns a string that is serialized, hex-encoded data for 'txid'.

### Synopsis

```bash
getrawtransaction "<txid>" (<verbose> "<blockhash>")
```

### Description

Returns the raw transaction data for a given transaction ID ( `txid` ). By default, it returns the hexadecimal representation of the raw transaction. If `verbose` is set to true, it returns a JSON object with detailed transaction information. The optional `blockhash` parameter specifies the block in which to search for the transaction.

### Arguments

| Name      | Type    | Required | Description                                                                                                                       |
| --------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------------------- |
| txid      | string  | Yes      | The transaction ID.                                                                                                               |
| verbose   | boolean | No       | If true, return detailed transaction information in JSON format (default: false).                                                 |
| blockhash | string  | No       | The hash of the block in which to look for the transaction (optional). Use this if the transaction might be in an orphaned block. |

### Flags

None

### Input

- `txid`: The transaction ID as a hexadecimal string.
- `verbose`: (Optional) A boolean indicating whether to return detailed transaction information. Defaults to false.
- `blockhash`: (Optional) The block hash to search within.

### Output

- If `verbose` is false (default): The hexadecimal representation of the raw transaction.
- If `verbose` is true: A JSON object containing detailed transaction information. Consult the original documentation for the complete structure of this object. Includes details such as transaction ID, size, version, inputs, outputs, block hash, confirmations, and timestamps.

### Examples

CLI Usage:

```bash
getrawtransaction "mytxid"
```

```bash
getrawtransaction "mytxid" true
```

```bash
getrawtransaction "mytxid" false "myblockhash"
```

```bash
getrawtransaction "mytxid" true "myblockhash"
```

JSON-RPC Call:

```json
{
  "method": "getrawtransaction",
  "params": ["mytxid", true, "myblockhash"] // verbose and blockhash are optional
}
```

### Error Handling

- An error message will be returned if the transaction is not found or if the provided `txid` or `blockhash` is invalid.
- By default, this command might not return the transaction data if the transaction is not in the mempool and the `-txindex` command-line option is not enabled.

### Notes

This command retrieves the raw transaction data, which can be used for various purposes, including transaction analysis and signing. The `-txindex` option is required to efficiently retrieve transactions that are not in the mempool. Related commands: [ `decoderawtransaction` ](#decoderawtransaction), [ `sendrawtransaction` ](#sendrawtransaction)

---

## getreceivedbyaccount

DEPRECATED. Returns the total amount received by addresses with <account> in transactions with at least [minconf] confirmations.

### Synopsis

```bash
getreceivedbyaccount "<account>" (<minconf>)
```

### Description

**DEPRECATED.** This command is deprecated and should not be used. It previously returned the total amount received by addresses associated with a specified account. Account functionality is deprecated in newer versions of Kaon.

### Arguments

| Name    | Type    | Required | Description                                           |
| ------- | ------- | -------- | ----------------------------------------------------- |
| account | string  | Yes      | The account name (can be "" for the default account). |
| minconf | numeric | No       | Minimum number of confirmations (default: 1).         |

### Flags

None

### Input

- `account`: The name of the account. The default account can be specified as "".
- `minconf`: (Optional) The minimum number of confirmations for transactions to be included. Defaults to 1.

### Output

The total amount received by the specified account in KAON.

### Examples

CLI Usage:

```bash
getreceivedbyaccount ""
```

```bash
getreceivedbyaccount "tabby" 6
```

JSON-RPC Call:

```json
{
  "method": "getreceivedbyaccount",
  "params": ["tabby", 6] // minconf is optional
}
```

### Error Handling

Errors can occur if the provided account name is invalid or if another error arises during balance retrieval.

### Notes

This command is deprecated. Avoid using accounts in Kaon applications. Use [ `getreceivedbyaddress` ](#getreceivedbyaddress) instead. Related commands: [ `listreceivedbyaccount` ](#listreceivedbyaccount), [ `listreceivedbyaddress` ](#listreceivedbyaddress)

---

## getreceivedbyaddress

Returns the total amount received by the given kaonaddress in transactions with at least minconf confirmations.

### Synopsis

```bash
getreceivedbyaddress "<kaonaddress>" (<minconf>)
```

### Description

Returns the total amount received by a specific Kaon address, considering transactions with a minimum number of confirmations ( `minconf` ). This command is used to check the balance of an address, taking confirmation requirements into account.

### Arguments

| Name        | Type    | Required | Description                           |
| ----------- | ------- | -------- | ------------------------------------- |
| kaonaddress | string  | Yes      | The Kaon address.                     |
| minconf     | numeric | No       | Minimum confirmations (default is 1). |

### Flags

None

### Input

- `kaonaddress`: The Kaon address to check.
- `minconf`: (Optional) The minimum number of confirmations required for transactions to be included in the calculation. The default is 1.

### Output

The total amount (in KAON) received by the specified address.

### Examples

CLI Usage:

```bash
getreceivedbyaddress "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6"
```

```bash
getreceivedbyaddress "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" 0  // Include unconfirmed transactions
```

```bash
getreceivedbyaddress "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" 6  // For higher confirmation security
```

JSON-RPC Call:

```json
{
  "method": "getreceivedbyaddress",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6", 6] // minconf is optional
}
```

### Error Handling

- If the specified address is invalid, an error will be returned.
- Errors might occur in cases where the balance information cannot be retrieved.

### Notes

This command calculates the total received amount at a given address based on the minimum confirmations specified. Related commands: [ `listreceivedbyaddress` ](#listreceivedbyaddress), [ `getaddressbalance` ](#getaddressbalance)

---

## getstakesplitthreshold

Returns the threshold for stake splitting.

### Synopsis

```bash
getstakesplitthreshold
```

### Description

Retrieves the current threshold for stake splitting. Stake splitting involves dividing larger staking outputs (UTXOs) into smaller ones to potentially improve staking efficiency and reduce the chance of orphaned blocks.

### Arguments

None

### Flags

None

### Input

None

### Output

The stake splitting threshold (integer value).

### Examples

CLI Usage:

```bash
getstakesplitthreshold
```

JSON-RPC Call:

```json
{
  "method": "getstakesplitthreshold",
  "params": []
}
```

### Error Handling

Unlikely to return errors under normal operation.

### Notes

Stake splitting can be beneficial for optimizing staking rewards. Related command: [ `setstakesplitthreshold` ](#setstakesplitthreshold)

---

## getstakinginfo

Returns an object containing staking-related information.

### Synopsis

```bash
getstakinginfo
```

### Description

Returns various information about the staking status of the wallet and the network. This includes whether staking is enabled, whether the wallet is currently staking, any staking errors, and other relevant details.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object containing staking details:

```json
{
  "enabled": true|false,           // Staking enabled status
  "staking": true|false,           // Current staking status
  "errors": "xxxx",                // Staking errors (if any)
  "pooledtx": n,                   // Number of pooled transactions
  "difficulty": n,                 // Current staking difficulty
  "search-interval": n,            // Staker search interval
  "weight": n,                     // Staker weight
  "netstakeweight": n,             // Network stake weight
  "expectedtime": n                // Expected time to earn reward
}

```

### Examples

CLI Usage:

```bash
getstakinginfo
```

JSON-RPC Call:

```json
{
  "method": "getstakinginfo",
  "params": []
}
```

### Error Handling

Errors are unlikely under typical operation but can occur due to unexpected wallet or node conditions.

### Notes

Use this command to monitor staking activity and diagnose any staking-related problems. Related command: [ `getstakingstatus` ](#getstakingstatus)

---

## getstakingstatus

Returns an object containing various staking information.

### Synopsis

```bash
getstakingstatus
```

### Description

Returns an object containing comprehensive information related to staking. This command provides details about the staking status, both from the wallet's perspective and the network's perspective, including synchronization status, wallet lock status, connection status and other relevant details that influence staking activity.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with detailed staking status information. Refer to the original documentation for a full explanation of the fields in this object. Key information includes `staking_status` , `staking_enabled` , `tiptime` , `haveconnections` , `mnsync` , `walletunlocked` , `stakeablecoins` , `hashLastStakeAttempt` , `heightLastStakeAttempt` , and `timeLastStakeAttempt` .

### Examples

CLI Usage:

```bash
getstakingstatus
```

JSON-RPC Call:

```json
{
  "method": "getstakingstatus",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors during normal operation. Errors can occur under unusual node or wallet conditions.

### Notes

Provides a comprehensive view of factors affecting staking, useful for diagnosing staking-related issues. Related commands: [ `getstakinginfo` ](#getstakinginfo), [ `listunspent` ](#listunspent)

---

## getstorage

Get contract storage data.

### Synopsis

```bash
getstorage <address> <blocknum> <index>
```

### Description

Retrieves the storage data of a contract at a specific block height and storage index. This command provides access to the persistent data stored within a smart contract.

### Arguments

| Name     | Type    | Required | Description                                     |
| -------- | ------- | -------- | ----------------------------------------------- |
| address  | string  | Yes      | The contract address.                           |
| blocknum | numeric | Yes      | The block number to retrieve storage data from. |
| index    | numeric | Yes      | The storage index.                              |

### Flags

None

### Input

- `address`: The address of the smart contract as a hexadecimal string.
- `blocknum`: The block number as an integer.
- `index`: The index of the storage slot within the contract as an integer.

### Output

The requested storage data at the given block number and storage index. The format of the output depends on the data type stored in the contract's storage.

### Examples

CLI Usage:

```bash
getstorage eb23c0b3e6042821da281a2e2364feb22dd543e3 1000 0
```

JSON-RPC Call:

```json
{
  "method": "getstorage",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3", 1000, 0]
}
```

### Error Handling

- If the contract address is invalid, the block number is out of range, or the storage index is invalid, an error message is returned.

### Notes

Understanding the contract's storage layout is essential to interpret the returned data correctly. Related commands: [ `getaccountinfo` ](#getaccountinfo), [ `listcontracts` ](#listcontracts)

---

## gettransaction

Get detailed information about in-wallet transaction <txid>.

### Synopsis

```bash
gettransaction "<txid>" (<include_watchonly>)
```

### Description

Retrieves detailed information about a specific transaction identified by its ID ( `txid` ). This includes information like amount, confirmations, block details, timestamps, and details about inputs and outputs. The optional `include_watchonly` parameter determines whether to include watch-only addresses in the balance calculations and details.

### Arguments

| Name              | Type    | Required | Description                                               |
| ----------------- | ------- | -------- | --------------------------------------------------------- |
| txid              | string  | Yes      | The transaction ID.                                       |
| include_watchonly | boolean | No       | Include watch-only addresses in details (default: false). |

### Flags

None

### Input

- `txid`: The hexadecimal string representing the transaction ID.
- `include_watchonly`: (Optional) A boolean indicating whether to include watch-only addresses. Defaults to false.

### Output

A JSON object containing comprehensive transaction details. See the original documentation for the complete structure of this JSON object. Important fields include `amount` , `fee` , `confirmations` , `blockhash` , `blockindex` , `txid` , `time` , `timereceived` , `details` (with information about inputs and outputs), and `hex` (the raw transaction data).

### Examples

CLI Usage:

```bash
gettransaction "1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d"
```

```bash
gettransaction "1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d" true
```

JSON-RPC Call:

```json
{
  "method": "gettransaction",
  "params": [
    "1075db55d416d3ca199f55b6084e2115b9345e16c5cf302fc80e9d5fbf5d48d",
    true
  ] // include_watchonly is optional
}
```

### Error Handling

- Returns an error if the given `txid` is invalid or if the transaction is not found.

### Notes

Provides detailed information about a specific transaction, useful for tracking transactions and investigating wallet activity. Related commands: [ `listtransactions` ](#listtransactions), [ `getrawtransaction` ](#getrawtransaction)

---

## gettransactionreceipt

Get the transaction receipt.

### Synopsis

```bash
gettransactionreceipt "<hash>"
```

### Description

Retrieves the transaction receipt for a given transaction hash. The transaction receipt provides details about the execution of a transaction, including gas used, logs emitted by the transaction, and the contract address if the transaction created a new contract.

### Arguments

| Name | Type   | Required | Description           |
| ---- | ------ | -------- | --------------------- |
| hash | string | Yes      | The transaction hash. |

### Flags

None

### Input

- `hash`: The transaction hash as a hexadecimal string.

### Output

A JSON object containing the transaction receipt. Refer to the original documentation for the complete structure. Key fields include `blockHash` , `blockNumber` , `transactionHash` , `transactionIndex` , `from` , `to` , `cumulativeGasUsed` , `gasUsed` , `contractAddress` , `excepted` (for exceptions), `bloom` , and `log` (an array of log entries).

### Examples

CLI Usage:

```bash
gettransactionreceipt "3b04bc73afbbcf02cfef2ca1127b60fb0baf5f8946a42df67f1659671a2ec53c"
```

JSON-RPC Call:

```json
{
  "method": "gettransactionreceipt",
  "params": ["3b04bc73afbbcf02cfef2ca1127b60fb0baf5f8946a42df67f1659671a2ec53c"]
}
```

### Error Handling

- If an invalid transaction hash is provided, or if the transaction receipt is not found, an error is returned.

### Notes

Transaction receipts provide valuable information about the execution of a transaction, especially for smart contracts. Related commands: [ `gettransaction` ](#gettransaction), [ `callcontract` ](#callcontract), [ `sendtocontract` ](#sendtocontract)

---

## gettxidbyethhash

Check for the txId if it is in a chain already. And returns Kaon tx hash if it is there.

### Synopsis

```bash
gettxidbyethhash "<ethTxId>"
```

### Description

Checks if a transaction with a given Ethereum transaction ID ( `ethTxId` ) exists on the Kaon blockchain and, if found, returns the corresponding Kaon transaction hash.

### Arguments

| Name    | Type   | Required | Description                  |
| ------- | ------ | -------- | ---------------------------- |
| ethTxId | string | Yes      | The Ethereum transaction ID. |

### Flags

None

### Input

- `ethTxId`: The Ethereum transaction ID.

### Output

The Kaon transaction hash (hexadecimal string) or an error if not found.

### Examples

CLI Usage:

```bash
gettxidbyethhash "0x123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
```

JSON-RPC Call:

```json
{
  "method": "gettxidbyethhash",
  "params": [
    "0x123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef"
  ]
}
```

### Error Handling

Returns an error if the provided Ethereum transaction ID is not found on the Kaon blockchain.

### Notes

Used to check if an Ethereum transaction has been processed by Kaon and obtain its corresponding Kaon transaction ID.

---

## gettxout

Returns details about an unspent transaction output.

### Synopsis

```bash
gettxout "<txid>" <n> (<includemempool>)
```

### Description

Returns information about a specific unspent transaction output (UTXO). A UTXO is an output from a previous transaction that hasn't yet been spent as an input in a new transaction. This command provides details about the UTXO's value, scriptPubKey (which determines the conditions for spending), and confirmation status. The optional `includemempool` parameter specifies whether to also search for the UTXO in the mempool (transactions not yet included in a block).

### Arguments

| Name           | Type    | Required | Description                                                |
| -------------- | ------- | -------- | ---------------------------------------------------------- |
| txid           | string  | Yes      | The transaction ID containing the UTXO.                    |
| n              | numeric | Yes      | The vout (output index) within the transaction.            |
| includemempool | boolean | No       | Whether to include mempool in the search. Default is true. |

### Flags

None

### Input

- `txid`: The ID of the transaction containing the output.
- `n`: The index of the output within the transaction (vout).
- `includemempool`: (Optional) A boolean indicating whether to include the mempool in the UTXO search. Defaults to true.

### Output

A JSON object containing details about the UTXO. If the UTXO is spent or doesn't exist, it returns null. See the original documentation for the complete structure of this JSON object. Key details include: `bestblock` (block hash where the UTXO was created), `confirmations` , `value` , `scriptPubKey` (containing script and address information), `version` , and `coinbase` (whether the output is a coinbase transaction output).

### Examples

CLI Usage:

```bash
gettxout "mytxid" 1
```

JSON-RPC Call:

```json
{
  "method": "gettxout",
  "params": ["mytxid", 1, true] // includemempool is optional (and defaults to true)
}
```

### Error Handling

- Returns an error if the provided `txid` is invalid.
- If the specified UTXO does not exist (either spent or never existed), it returns null.

### Notes

UTXOs are fundamental components of transactions. This command provides information about a specific UTXO. Related commands: [ `listunspent` ](#listunspent), [ `getrawtransaction` ](#getrawtransaction)

---

## gettxoutsetinfo

Returns statistics about the unspent transaction output set. Note this call may take some time.

### Synopsis

```bash
gettxoutsetinfo
```

### Description

Returns statistics about the Unspent Transaction Output (UTXO) set. This includes information about the current block height, the hash of the best block, the number of transactions and UTXOs, the serialized size and hash of the UTXO set, and the total amount of KAON in the UTXO set. This command can take some time to execute as it needs to scan the entire UTXO set.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object containing UTXO set statistics.

```json
{
  "height":n,             // Current block height
  "bestblock": "hex",      // Best block hash
  "transactions": n,       // Number of transactions
  "txouts": n,             // Number of transaction outputs (UTXOs)
  "bytes_serialized": n,   // Serialized size of the UTXO set
  "hash_serialized": "hash",// Serialized hash of the UTXO set
  "total_amount": x.xxx     // Total amount in UTXOs
}

```

### Examples

CLI Usage:

```bash
gettxoutsetinfo
```

JSON-RPC Call:

```json
{
  "method": "gettxoutsetinfo",
  "params": []
}
```

### Error Handling

This command is generally robust, but errors might be returned if the UTXO set data cannot be accessed or processed correctly.

### Notes

The information provided gives a high-level view of the UTXO set. Related command: [ `gettxout` ](#gettxout)

---

## getunconfirmedbalance

Returns the server's total unconfirmed balance.

### Synopsis

```bash
getunconfirmedbalance
```

### Description

Returns the total unconfirmed balance in the wallet. This balance includes transactions that have been received but are not yet confirmed in a block.

### Arguments

None

### Flags

None

### Input

None

### Output

The total unconfirmed balance in KAON.

### Examples

CLI Usage:

```bash
getunconfirmedbalance
```

JSON-RPC Call:

```json
{
  "method": "getunconfirmedbalance",
  "params": []
}
```

### Error Handling

Errors are unlikely under normal circumstances, but may occur due to unexpected wallet conditions.

### Notes

Unconfirmed transactions are not yet fully secure and might be reversed if they are not included in a block. Related commands: [ `getbalance` ](#getbalance), [ `getwalletinfo` ](#getwalletinfo), [ `listunspent` ](#listunspent)

---

## getwalletinfo

Returns an object containing various wallet state info.

### Synopsis

```bash
getwalletinfo
```

### Description

Returns an object containing various information about the wallet's state, including balance details, transaction count, keypool size, and other relevant information.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object with wallet information. See the original documentation for the full structure of the JSON object. Important fields include `walletversion` , `balance` , `delegated_balance` , `cold_staking_balance` , `unconfirmed_balance` , `immature_balance` , `txcount` , `keypoololdest` , `keypoolsize` , `unlocked_until` , and `paytxfee` .

### Examples

CLI Usage:

```bash
getwalletinfo
```

JSON-RPC Call:

```json
{
  "method": "getwalletinfo",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors under normal operation. Errors might occur due to unexpected wallet conditions.

### Notes

This command provides a summary of the wallet's current state and configuration. Related commands: [ `getbalance` ](#getbalance), [ `getunconfirmedbalance` ](#getunconfirmedbalance), [ `listunspent` ](#listunspent)

---

## help

List all commands, or get help for a specified command.

### Synopsis

```bash
help (<"command">)
```

### Description

Provides help information about available commands or details about a specific command. If no command is specified, lists all available commands.

### Arguments

| Name    | Type   | Required | Description                            |
| ------- | ------ | -------- | -------------------------------------- |
| command | string | No       | The command to get help on (optional). |

### Flags

None

### Input

- `command`: (Optional) The name of the command for which help is needed.

### Output

The help text, either listing all commands or providing details about a specific command.

### Examples

CLI Usage:

```bash
help
```

```bash
help getbalance
```

JSON-RPC Call:

```json
{
  "method": "help",
  "params": ["getbalance"] // "getbalance" is optional
}
```

### Error Handling

If an invalid command is specified, an error message will be returned.

### Notes

The help command is your primary source of information within the CLI.

---

## importaddress

Adds an address or scriptse (in hex) that can be watched as if it were in your wallet but cannot be used to spend.

### Synopsis

```bash
importaddress "<address>" (<"label"> <rescan>)
```

### Description

Imports a Kaon address or script (in hexadecimal format) into the wallet as a watch-only address. Watch-only addresses allow you to track transactions and balances associated with the address without having the private key necessary to spend from it. An optional label can be associated with the imported address. The optional `rescan` parameter determines whether the wallet should rescan the blockchain for transactions involving this address. Rescanning can be time-consuming.

### Arguments

| Name    | Type    | Required | Description                                                                                 |
| ------- | ------- | -------- | ------------------------------------------------------------------------------------------- |
| address | string  | Yes      | The Kaon address or hex-encoded script to import.                                           |
| label   | string  | No       | An optional label for the address (default is "").                                          |
| rescan  | boolean | No       | Rescan the wallet for transactions (default is true, potentially time-consuming operation). |

### Flags

None

### Input

- `address`: The Kaon address or script (hexadecimal) you want to import.
- `label`: (Optional) A label to identify the imported address. Defaults to an empty string.
- `rescan`: (Optional) Whether to rescan the blockchain for transactions related to the imported address. Defaults to true.

### Output

None

### Examples

CLI Usage:

```bash
importaddress "myaddress"
```

```bash
importaddress "myaddress" "testing" false
```

JSON-RPC Call:

```json
{
  "method": "importaddress",
  "params": ["myaddress", "testing", false] // label and rescan are optional
}
```

### Error Handling

- If the provided `address` is invalid, an error message will be returned.
- Errors might occur during the rescan process if there are issues with the blockchain data.

### Notes

Importing watch-only addresses allows you to monitor addresses without having spending control. The `rescan` operation can be time-consuming, especially for addresses with a long transaction history. Related commands: [ `importprivkey` ](#importprivkey), [ `importwallet` ](#importwallet)

---

## importprivkey

Adds a private key (as returned by dumpprivkey) to your wallet.

### Synopsis

```bash
importprivkey "<kaonprivkey>" (<"label"> <rescan> <fStakingAddress>)
```

### Description

Imports a private key into your wallet. This allows the wallet to manage the funds associated with that private key. The key can be provided in Wallet Import Format (WIF), as returned by the `dumpprivkey` command. An optional label can be assigned to the imported key. The `rescan` parameter determines whether to rescan the blockchain for transactions related to this key. Rescanning can be a lengthy process. The `fStakingAddress` parameter should be set to true if the key corresponds to a staking address used for cold staking.

### Arguments

| Name            | Type    | Required | Description                                                                         |
| --------------- | ------- | -------- | ----------------------------------------------------------------------------------- |
| kaonprivkey     | string  | Yes      | The private key in WIF format.                                                      |
| label           | string  | No       | An optional label for the private key.                                              |
| rescan          | boolean | No       | Rescan the blockchain for transactions (default: true, this can be time-consuming). |
| fStakingAddress | boolean | No       | Set to true if the key is for a staking address (default: false).                   |

### Flags

None

### Input

- `kaonprivkey`: The private key to import.
- `label`: (Optional) A descriptive label for the key, making it easier to manage within the wallet.
- `rescan`: (Optional) Whether to rescan the blockchain for past transactions. Defaults to true, which can take time. If set to false, only new transactions involving this key will be detected.
- `fStakingAddress`: (Optional) A boolean indicating whether the imported key is for a staking address (cold staking). Defaults to false.

### Output

None

### Examples

CLI Usage:

```bash
importprivkey "mykey"
```

```bash
importprivkey "mykey" "testing" false true
```

JSON-RPC Call:

```json
{
  "method": "importprivkey",
  "params": ["mykey", "testing", false, true] // Label, rescan, and fStakingAddress are optional
}
```

### Error Handling

- If an invalid private key is provided, or if other errors occur during import, an error message will be returned.
- If the `rescan` option is enabled and problems occur during blockchain rescanning, errors might be displayed.

### Notes

Importing a private key adds it to your wallet, granting control over the corresponding funds. Be extremely cautious with private keys, as anyone with access can spend the associated funds. Related commands: [ `dumpprivkey` ](#dumpprivkey), [ `importwallet` ](#importwallet), [ `bip38decrypt` ](#bip38decrypt)

---

## importwallet

Imports keys from a wallet dump file (see dumpwallet).

### Synopsis

```bash
importwallet "<filename>" (<full-rescan>)
```

### Description

Imports keys from a wallet dump file (created using `dumpwallet` ). This allows restoring or merging a previously exported wallet into the current wallet. The optional `full-rescan` parameter controls whether to perform a full rescan of the blockchain after import. This rescan is time-consuming, but essential to detect all transactions involving the imported keys.

### Arguments

| Name        | Type    | Required | Description                                                                               |
| ----------- | ------- | -------- | ----------------------------------------------------------------------------------------- |
| filename    | string  | Yes      | Path to the wallet dump file.                                                             |
| full-rescan | boolean | No       | Whether to rescan the blockchain after import (default: true, but can be time-consuming). |

### Flags

None

### Input

- `filename`: The path to the wallet dump file to be imported.
- `full-rescan`: (Optional) Whether to perform a full blockchain rescan after importing the keys. Defaults to true. Setting it to false will speed up the import process but might require a manual rescan later.

### Output

None

### Examples

CLI Usage:

```bash
importwallet "test"
```

```bash
importwallet "test" false
```

JSON-RPC Call:

```json
{
  "method": "importwallet",
  "params": ["test", false] //  full-rescan is optional
}
```

### Error Handling

- If an invalid wallet dump file is provided, if the file cannot be read, or if other errors occur during import, an error message will be returned.
- Errors might be displayed if a full rescan is performed and there are issues with the blockchain data.

### Notes

This command allows recovering or merging wallets from exported data. Ensure the wallet dump file comes from a trusted source and handle it securely. Related commands: [ `dumpwallet` ](#dumpwallet), [ `importprivkey` ](#importprivkey)

---

## initmasternode

Initialize masternode on demand if it's not already initialized.

### Synopsis

```bash
initmasternode "<masternodePrivKey>" "<masternodeAddr>"
```

### Description

Initializes a masternode if it hasn't been initialized already. This prepares the masternode for operation. This command is typically used during the initial setup of a masternode.

### Arguments

| Name              | Type   | Required | Description                                |
| ----------------- | ------ | -------- | ------------------------------------------ |
| masternodePrivKey | string | Yes      | The masternode's private key.              |
| masternodeAddr    | string | Yes      | The IP address and port of the masternode. |

### Flags

None

### Input

- `masternodePrivKey`: The private key associated with the masternode. This key is essential for masternode operation and should be kept highly secure.
- `masternodeAddr`: The IP address and port on which the masternode will listen for connections. This is how other nodes in the network will communicate with your masternode.

### Output

"success" if initialization was successful, or an error message if it failed.

### Examples

CLI Usage:

```bash
initmasternode "9247iC59poZmqBYt9iDh9wDam6v9S1rW5XekjLGyPnDhrDkP4AK" "187.24.32.124:51473"
```

JSON-RPC Call:

```json
{
  "method": "initmasternode",
  "params": [
    "9247iC59poZmqBYt9iDh9wDam6v9S1rW5XekjLGyPnDhrDkP4AK",
    "187.24.32.124:51473"
  ]
}
```

### Error Handling

Returns an error message if initialization fails. This can be due to an invalid private key, incorrect IP address/port, or other configuration issues.

### Notes

This command only initializes the masternode. You need to further configure and start it using related commands. Related commands: [ `startmasternode` ](#startmasternode), [ `createmasternodekey` ](#createmasternodekey), [ `listmasternodes` ](#listmasternodes)

---

## invalidateblock

Permanently marks a block as invalid, as if it violated a consensus rule.

### Synopsis

```bash
invalidateblock "<hash>"
```

### Description

Marks a specified block as invalid. This action is usually performed if a block is found to violate consensus rules. The blockchain will be reorganized to exclude the invalidated block and any blocks built on top of it.

### Arguments

| Name | Type   | Required | Description     |
| ---- | ------ | -------- | --------------- |
| hash | string | Yes      | The block hash. |

### Flags

None

### Input

- `hash`: The hash of the block to invalidate.

### Output

None

### Examples

CLI Usage:

```bash
invalidateblock "blockhash"
```

JSON-RPC Call:

```json
{
  "method": "invalidateblock",
  "params": ["blockhash"]
}
```

### Error Handling

If the provided block hash is invalid or if the block cannot be invalidated (for example, if it's part of the current main chain), an error message will be returned.

### Notes

Use this command cautiously as it directly affects the blockchain's integrity. It's essential to understand the implications before invalidating a block. Related commands: [ `reconsiderblock` ](#reconsiderblock)

---

## keypoolrefill

Fills the keypool.

### Synopsis

```bash
keypoolrefill (<newsize>)
```

### Description

Refills the keypool, which is a reserve of pre-generated addresses used by the wallet. This ensures the wallet always has addresses available for receiving payments without needing to generate them on demand. You can specify the desired `newsize` of the keypool; otherwise, it defaults to 100.

### Arguments

| Name    | Type    | Required | Description                                        |
| ------- | ------- | -------- | -------------------------------------------------- |
| newsize | numeric | No       | The desired size for the keypool (default is 100). |

### Flags

None

### Input

- `newsize`: (Optional) The new keypool size.

### Output

None

### Examples

CLI Usage:

```bash
keypoolrefill
```

JSON-RPC Call:

```json
{
  "method": "keypoolrefill",
  "params": [200] // newsize is optional (default is 100)
}
```

### Error Handling

Unlikely to return errors under normal operation. If the keypool cannot be refilled due to wallet or system constraints, an error message will be returned.

### Notes

Keypool management helps ensure smooth operation when receiving payments.

---

## leasetoaddress

Lease an amount to a given address for mining. The amount is a real and is rounded to the nearest 0.00000001.

### Synopsis

```bash
leasetoaddress "<leasingaddress>" <amount> (<timestamp> "<owneraddress>" <fExternalOwner> <fForceNotEnabled>)
```

### Description

Leases a specified amount of KAON to a given `leasingaddress` for mining. The `amount` is leased for a specified duration. The `timestamp` argument sets an optional lock time for the leased UTXOs. The `owneraddress` parameter lets you specify an address that controls the leased funds. If `owneraddress` isn't provided, a new address is generated in the wallet. `fExternalOwner` lets you specify an external `owneraddress` that's not managed by the current wallet. `fForceNotEnabled` lets you bypass the usual SPORK check that controls the leasing feature. This last option is mainly intended for testing.

### Arguments

| Name             | Type    | Required | Description                                                                                                                                                                             |
| ---------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| leasingaddress   | string  | Yes      | The Kaon leasing address.                                                                                                                                                               |
| amount           | numeric | Yes      | The amount to lease in KAON.                                                                                                                                                            |
| timestamp        | numeric | No       | Optional lock time until the leased UTXO is locked.                                                                                                                                     |
| owneraddress     | string  | No       | The Kaon address that can spend the lease rewards. If not provided or empty, a new wallet address is generated.                                                                         |
| fExternalOwner   | boolean | No       | Use the provided `owneraddress` even if it is not present in this wallet (default: false). WARNING: Only the owner of the `owneraddress` private key will be able to spend these coins. |
| fForceNotEnabled | boolean | No       | Bypass SPORK 1017 check (default: false, for testing purposes).                                                                                                                         |

### Flags

None

### Input

- `leasingaddress`: The receiving address for the lease.
- `amount`: The quantity of KAON to lease.
- `timestamp`: (Optional) The lock time for the leased UTXO (Unix timestamp format).
- `owneraddress`: (Optional) The Kaon address authorized to spend the lease rewards. If omitted, a new address will be generated.
- `fExternalOwner`: (Optional) Boolean flag indicating if the owner address is external to this wallet. Defaults to false. Use with caution.
- `fForceNotEnabled`: (Optional) Boolean flag to bypass SPORK 1017 check, intended for testing. Defaults to false.

### Output

A JSON object including lease details:

```json
{
  "owner_address": "xxx", // Owner's (leasee's) address
  "leaser_address": "xxx", // Miner's (leaser's) address
  "txid": "xxx" // Transaction ID
}
```

### Examples

CLI Usage:

```bash
leasetoaddress "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 100
```

```bash
leasetoaddress "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 100 1649328238
```

```bash
leasetoaddress "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 1000 "DMJRSsuU9zfyrvxVaAEFQqK4MxZg34fk"
```

JSON-RPC Call:

```json
{
  "method": "leasetoaddress",
  "params": [
    "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6",
    1000,
    1678886400,
    "DMJRSsuU9zfyrvxVaAEFQqK4MxZg34fk",
    false,
    false
  ] // timestamp, owneraddress, fExternalOwner and fForceNotEnabled are optional
}
```

### Error Handling

- Returns an error if any address is invalid or if the amount is insufficient.
- Returns an error if the SPORK 1017 is not active and `fForceNotEnabled` is not true.

### Notes

This command facilitates leasing KAON for mining, offering flexibility in managing ownership and enabling participation in mining without direct mining operations. Related commands: [ `rawleasetoaddress` ](#rawleasetoaddress), [ `listleasingaddresses` ](#listleasingaddresses), [ `listleasingutxos` ](#listleasingutxos)

---

## listaccounts

DEPRECATED. Returns Object that has account names as keys, account balances as values.

### Synopsis

```bash
listaccounts (<minconf> <includeWatchonly>)
```

### Description

**DEPRECATED.** This command is deprecated and should not be used. It previously returned a JSON object listing all accounts in the wallet along with their balances. Account functionality is deprecated.

### Arguments

| Name             | Type    | Required | Description                                                  |
| ---------------- | ------- | -------- | ------------------------------------------------------------ |
| minconf          | numeric | No       | The minimum number of confirmations to include (default: 1). |
| includeWatchonly | boolean | No       | Include watch-only addresses (default: false).               |

### Flags

None

### Input

- `minconf`: (Optional) Minimum number of confirmations for a transaction to be included in the balance calculation. Default is 1.
- `includeWatchonly`: (Optional) Include the balance of watch-only addresses. Default is false.

### Output

A JSON object where keys are account names, and values are their balances. Returns an empty object if no accounts are found.

### Examples

CLI Usage:

```bash
listaccounts
```

```bash
listaccounts 6 true
```

JSON-RPC Call:

```json
{
  "method": "listaccounts",
  "params": [6, true] // minconf and includeWatchonly are optional
}
```

### Error Handling

Errors may occur if invalid parameters are provided.

### Notes

Avoid using this command and accounts in your Kaon applications. Use [ `listreceivedbyaddress` ](#listreceivedbyaddress) instead. Related commands: [ `getaccount` ](#getaccount), [ `getaccountaddress` ](#getaccountaddress), [ `setaccount` ](#setaccount)

---

## listaddressgroupings

Lists groups of addresses which have had their common ownership
made public by common use as inputs or as the resulting change
in past transactions.

### Synopsis

```bash
listaddressgroupings
```

### Description

Lists groups of addresses that have been identified as belonging to the same entity based on their transaction history. This analysis is done by looking at addresses that commonly appear together as inputs or outputs in transactions, suggesting shared ownership.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array of address groupings. Each grouping is an array of arrays, where each inner array contains an address, amount, and optionally an account name (deprecated). The inner array structure is: `["address", amount, "account"]` .

### Examples

CLI Usage:

```bash
listaddressgroupings
```

JSON-RPC Call:

```json
{
  "method": "listaddressgroupings",
  "params": []
}
```

### Error Handling

Unlikely to return errors under normal circumstances.

### Notes

This command helps in identifying address clusters that might belong to the same user or entity.

---

## listbanned

List all banned IPs/Subnets.

### Synopsis

```bash
listbanned
```

### Description

Returns a list of all IP addresses and subnets that have been banned by the Kaon Core node. Banned IPs are prevented from connecting to your node.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array of banned IP addresses/subnets. Each element is a JSON object containing:

- `address`: The banned IP address or subnet.
- `banned_until`: Timestamp (Unix time) when the ban expires.
- `ban_created`: Timestamp when the ban was created.
- `ban_reason`: The reason for the ban.

### Examples

CLI Usage:

```bash
listbanned
```

JSON-RPC Call:

```json
{
  "method": "listbanned",
  "params": []
}
```

### Error Handling

Unlikely to return errors under normal operation. Errors might be returned under unusual node conditions.

### Notes

Useful for reviewing and managing banned IPs. Related commands: [ `setban` ](#setban), [ `clearbanned` ](#clearbanned)

---

## listcoldutxos

List P2CS unspent outputs received by this wallet as cold-staker.

### Synopsis

```bash
listcoldutxos (<nonWhitelistedOnly>)
```

### Description

Lists all Pay-to-Cold-Staking (P2CS) Unspent Transaction Outputs (UTXOs) that the wallet has received as a cold staker. Cold staking allows you to delegate staking without keeping your coins online, and P2CS scripts manage the delegation process. The `nonWhitelistedOnly` parameter filters the listed UTXOs to show only those from delegators who are not in the whitelist.

### Arguments

| Name               | Type    | Required | Description                                                                             |
| ------------------ | ------- | -------- | --------------------------------------------------------------------------------------- |
| nonWhitelistedOnly | boolean | No       | Show only UTXOs from non-whitelisted delegators (default: false, shows all P2CS UTXOs). |

### Flags

None

### Input

- `nonWhitelistedOnly`: (Optional) Boolean, defaults to false. If true, it excludes whitelisted delegators.

### Output

A JSON array, with each element representing a P2CS UTXO.

```json
[
  {
    "txid": "true",           // Transaction ID of the UTXO
    "txidn": "accountname",   // Output number (vout) of the UTXO
    "amount": x.xxx,          // Amount of the UTXO
    "confirmations": n,       // Number of confirmations
    "cold-staker": "address", // Cold staker address
    "coin-owner": "address",  // Coin owner address
    "whitelisted": "true|false" // If the delegator is whitelisted
  },
  // ... more UTXOs
]
```

### Examples

CLI Usage:

```bash
listcoldutxos
```

```bash
listcoldutxos true // Show only from non-whitelisted addresses
```

### Error Handling

Errors might occur if there's a problem accessing wallet data.

### Notes

This command gives an overview of P2CS UTXOs held by the wallet as a cold staker. Related commands: [ `delegatestake` ](#delegatestake), [ `getcoldstakingbalance` ](#getcoldstakingbalance)

---

## listcontracts

Get the contracts list.

### Synopsis

```bash
listcontracts
```

### Description

Lists all known contracts on the Kaon blockchain. This provides a way to view deployed smart contracts.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object where keys are contract addresses and values are their corresponding bytecodes. An empty JSON object `{}` is returned if no contracts are found.

### Examples

CLI Usage:

```bash
listcontracts
```

JSON-RPC Call:

```json
{
  "method": "listcontracts",
  "params": []
}
```

### Error Handling

Errors might occur in situations where accessing contract data fails.

### Notes

Provides a list of deployed contracts. Related commands: [ `createcontract` ](#createcontract), [ `getaccountinfo` ](#getaccountinfo)

---

## listdelegators

Shows the list of allowed delegator addresses for cold staking.

### Synopsis

```bash
listdelegators (<fBlacklist>)
```

### Description

Lists delegator addresses that are allowed for cold staking on this wallet. You can optionally filter for addresses that have been removed from the whitelist (blacklist).

### Arguments

| Name       | Type    | Required | Description                                                                                         |
| ---------- | ------- | -------- | --------------------------------------------------------------------------------------------------- |
| fBlacklist | boolean | No       | Show addresses removed from the delegators whitelist (default: false, shows the current whitelist). |

### Flags

None

### Input

- `fBlacklist`: (Optional) If set to true, shows the removed delegators (blacklist). If omitted or set to false (default), shows the current allowed delegators.

### Output

Returns a JSON array of objects, each representing a delegator. Each object contains:

- `label`: The label associated with the address (if any).
- `address`: The Kaon address of the delegator.

### Examples

CLI Usage:

```bash
listdelegators
```

JSON-RPC Call:

```json
{
  "method": "listdelegators",
  "params": [true] // fBlacklist is optional, defaults to false
}
```

### Error Handling

Errors can occur if there's a problem accessing wallet data.

### Notes

This command helps manage cold staking permissions by listing whitelisted or blacklisted delegators. Related commands: [ `delegatoradd` ](#delegatoradd), [ `delegatorremove` ](#delegatorremove)

---

## listleasingaddresses

Shows the list of leasing addresses for this wallet.

### Synopsis

```bash
listleasingaddresses
```

### Description

Lists all leasing addresses associated with this wallet. Leasing addresses are used to receive leased funds for mining.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array. Each element in the array is a JSON object representing a leasing address and contains:

- `label`: The label associated with the address (or an empty string).
- `address`: The Kaon leasing address.

### Examples

CLI Usage:

```bash
listleasingaddresses
```

JSON-RPC Call:

```json
{
  "method": "listleasingaddresses",
  "params": []
}
```

### Error Handling

Unlikely to return errors under normal wallet operations.

### Notes

This command helps users manage and view their leasing addresses. Related commands: [ `getnewleasingaddress` ](#getnewleasingaddress), [ `leasetoaddress` ](#leasetoaddress)

---

## listleasingutxos

List P2L unspent outputs received by this wallet as leaser.

### Synopsis

```bash
listleasingutxos (<nonWhitelistedOnly>)
```

### Description

Lists all Pay-to-Leasing (P2L) UTXOs received by this wallet. P2L UTXOs represent funds that have been leased to this wallet for mining. The optional parameter `nonWhitelistedOnly` , when true, filters the results to show only those UTXOs originating from leasees who are not in the whitelist.

### Arguments

| Name               | Type    | Required | Description                                                                                    |
| ------------------ | ------- | -------- | ---------------------------------------------------------------------------------------------- |
| nonWhitelistedOnly | boolean | No       | Show only UTXOs from leasees who are not whitelisted. Defaults to false (shows all P2L UTXOs). |

### Flags

None

### Input

- `nonWhitelistedOnly`: (Optional) If true, shows UTXOs only from non-whitelisted leasees. Default is false.

### Output

A JSON array. Each element in the array is a JSON object that represents a P2L UTXO and includes details like the transaction ID ( `txid` ), output index ( `txidn` ), amount, confirmations, coin leaser's address, coin owner's address, and whether the coin owner is whitelisted.

### Examples

CLI Usage:

```bash
listleasingutxos
```

```bash
listleasingutxos true
```

JSON-RPC Call:

```json
{
  "method": "listleasingutxos",
  "params": [true] // nonWhitelistedOnly is optional
}
```

### Error Handling

Errors can occur if there are issues with wallet data or processing.

### Notes

This command provides an overview of the P2L UTXOs in the wallet. Related commands: [ `leasetoaddress` ](#leasetoaddress), [ `listleasingaddresses` ](#listleasingaddresses)

---

## listlockunspent

Returns list of temporarily unspendable outputs.
See the lockunspent call to lock and unlock transactions for spending.

### Synopsis

```bash
listlockunspent
```

### Description

Returns a list of transaction outputs that have been temporarily locked using the `lockunspent` command. Locked outputs will not be automatically selected as inputs when creating new transactions.

### Arguments

None

### Flags

None

### Input

None

### Output

Returns a JSON array of locked outputs. Each object in the array represents a locked UTXO and contains the transaction ID ( `txid` ) and output index ( `vout` ).

### Examples

CLI Usage:

```bash
listlockunspent
```

JSON-RPC Call:

```json
{
  "method": "listlockunspent",
  "params": []
}
```

### Error Handling

Unlikely to return errors during normal operation. Errors might occur due to unexpected wallet conditions.

### Notes

This command is used to review currently locked transaction outputs. Related commands: [ `lockunspent` ](#lockunspent), [ `listunspent` ](#listunspent)

---

## listmasternodeconf

Print masternode.conf in JSON format.

### Synopsis

```bash
listmasternodeconf (<"filter">)
```

### Description

Returns the contents of the `masternode.conf` file in JSON format. This configuration file contains details about your masternodes. You can optionally provide a `filter` string for partial matching by alias, address, transaction hash, or status.

### Arguments

| Name   | Type   | Required | Description                                     |
| ------ | ------ | -------- | ----------------------------------------------- |
| filter | string | No       | Text for filtering masternodes (partial match). |

### Flags

None

### Input

- `filter`: (Optional) Filter text for searching masternodes based on partial matches of their alias, address, txHash, or status.

### Output

A JSON array. Each element represents a masternode configuration entry from the file and is a JSON object containing fields like alias, address, privateKey, txHash, outputIndex, and status.

### Examples

CLI Usage:

```bash
listmasternodeconf
```

JSON-RPC Call:

```json
{
  "method": "listmasternodeconf",
  "params": ["mymn"] // filter is optional
}
```

### Error Handling

If the `masternode.conf` file is missing, unreadable, or if there's an error parsing the content, an error message is returned.

### Notes

This command provides a structured way to review masternode configurations.

---

## listmasternodes

Get a ranked list of masternodes.

### Synopsis

```bash
listmasternodes (<"filter">)
```

### Description

Returns a list of masternodes, optionally filtered by a search string. This list includes details about each masternode, such as their rank, collateral transaction, status, address, protocol version, last seen time, active time, and last paid time. You can provide a filter to search for masternodes based on their transaction hash, status, or address.

### Arguments

| Name   | Type   | Required | Description                                                         |
| ------ | ------ | -------- | ------------------------------------------------------------------- |
| filter | string | No       | Filter string (optional). Partial match by txhash, status, or addr. |

### Flags

None

### Input

- `filter`: (Optional) A string to filter results. Performs a partial match on txhash, status, or address fields.

### Output

A JSON array of masternode objects. Each object provides details about a masternode:

```json
[
  {
    "rank": n,           // Masternode rank
    "txhash": "hash",    // Collateral transaction hash
    "outidx": n,         // Collateral output index
    "pubkey": "key",     // Masternode public key
    "status": s,         // Masternode status (e.g. ENABLED)
    "addr": "addr",      // Masternode Kaon address
    "version": v,        // Protocol version
    "lastseen": ttt,       // Last seen timestamp
    "activetime": ttt,     // Active time
    "lastpaid": ttt,       // Last paid timestamp
  },
  // ... more masternodes
]
```

### Examples

CLI Usage:

```bash
listmasternodes
```

JSON-RPC Call:

```json
{
  "method": "listmasternodes",
  "params": ["127.0.0."] // filter is optional
}
```

### Error Handling

Errors might be returned if there is a problem accessing masternode information.

### Notes

Provides a comprehensive list of masternodes, useful for monitoring the masternode network. Related commands: [ `getmasternodecount` ](#getmasternodecount), [ `getmasternodestatus` ](#getmasternodestatus), [ `startmasternode` ](#startmasternode)

---

## listreceivedbyaccount

DEPRECATED. List balances by account.

### Synopsis

```bash
listreceivedbyaccount (<minconf> <includeempty> <includeWatchonly>)
```

### Description

**DEPRECATED.** This command is deprecated and should not be used. It was previously used to list the total amount received by each account in the wallet. Account functionality has been deprecated.

### Arguments

| Name             | Type    | Required | Description                                                                    |
| ---------------- | ------- | -------- | ------------------------------------------------------------------------------ |
| minconf          | numeric | No       | The minimum number of confirmations before payments are included (default: 1). |
| includeempty     | boolean | No       | Include accounts that haven't received any payments (default: false).          |
| includeWatchonly | boolean | No       | Include watch-only addresses (default: false).                                 |

### Flags

None

### Input

- `minconf`: (Optional) Minimum number of confirmations for transactions to be considered. Defaults to 1.
- `includeempty`: (Optional) Whether to include accounts with zero balance. Defaults to false.
- `includeWatchonly`: (Optional) Whether to include watch-only addresses. Defaults to false.

### Output

A JSON array of objects, where each object represents an account and its balance. If no accounts meet the specified criteria, it returns an empty array.

### Examples

CLI Usage:

```bash
listreceivedbyaccount
```

```bash
listreceivedbyaccount 6 true
```

JSON-RPC Call:

```json
{
  "method": "listreceivedbyaccount",
  "params": [6, true, true] // minconf, includeempty, and includeWatchonly are optional
}
```

### Error Handling

If the provided parameters are invalid, an error message may be returned.

### Notes

This command is deprecated. Use `listreceivedbyaddress` instead. Related commands: [ `getreceivedbyaccount` ](#getreceivedbyaccount), [ `listreceivedbyaddress` ](#listreceivedbyaddress)

---

## listreceivedbyaddress

List balances by receiving address.

### Synopsis

```bash
listreceivedbyaddress (<minconf> <includeempty> <includeWatchonly>)
```

### Description

Lists the total amount received by each address in the wallet. Optional parameters allow you to filter by minimum confirmations, include empty addresses, and include watch-only addresses.

### Arguments

| Name             | Type    | Required | Description                                                                    |
| ---------------- | ------- | -------- | ------------------------------------------------------------------------------ |
| minconf          | numeric | No       | The minimum number of confirmations before payments are included (default: 1). |
| includeempty     | boolean | No       | Include addresses that haven't received any payments (default: false).         |
| includeWatchonly | boolean | No       | Include watch-only addresses (default: false).                                 |

### Flags

None

### Input

- `minconf`: (Optional) The minimum number of confirmations required for a transaction to be included in the calculation. Default is 1.
- `includeempty`: (Optional) Whether to include addresses with a zero balance. Default is false.
- `includeWatchonly`: (Optional) Whether to include watch-only addresses in the listing. Default is false.

### Output

A JSON array of objects. Each object represents an address and contains information like the address, account (deprecated), amount received, confirmations, and whether it involves a watch-only address.

### Examples

CLI Usage:

```bash
listreceivedbyaddress
```

```bash
listreceivedbyaddress 6 true true
```

JSON-RPC Call:

```json
{
  "method": "listreceivedbyaddress",
  "params": [6, true, true] // minconf, includeempty, and includeWatchonly are optional
}
```

### Error Handling

May return an error if the provided parameters are invalid.

### Notes

Useful for viewing and analyzing incoming payments to the wallet. Related commands: [ `getreceivedbyaddress` ](#getreceivedbyaddress), [ `listreceivedbyaccount` ](#listreceivedbyaccount) (deprecated), [ `listunspent` ](#listunspent)

---

## listsinceblock

Get all transactions in blocks since block [blockhash], or all transactions if omitted.

### Synopsis

```bash
listsinceblock (<"blockhash"> <target-confirmations> <includeWatchonly>)
```

### Description

Returns all transactions in blocks since a specified block hash, or all transactions if no block hash is provided. This command is helpful for retrieving a history of transactions within a certain timeframe or since a particular point in the blockchain.

### Arguments

| Name                 | Type    | Required | Description                                                                                 |
| -------------------- | ------- | -------- | ------------------------------------------------------------------------------------------- |
| blockhash            | string  | No       | The block hash to list transactions since (optional, if omitted, returns all transactions). |
| target-confirmations | numeric | No       | The minimum number of confirmations required.                                               |
| includeWatchonly     | boolean | No       | Include transactions to watch-only addresses (default: false).                              |

### Flags

None

### Input

- `blockhash`: (Optional) The hash of the block to start listing transactions from. If omitted, it lists all transactions.
- `target-confirmations`: (Optional) The minimum number of confirmations required for transactions to be included. Default is 1.
- `includeWatchonly`: (Optional) Whether to include transactions involving watch-only addresses. Defaults to false.

### Output

A JSON object with transaction details and the last block hash.

```json

{
  "transactions": [
    {
      "account": "accountname",         // DEPRECATED
      "address": "kaonaddress",          // Receiving/sending address
      "category": "send|receive",       // Transaction category
      "amount": x.xxx,                  // Amount
      "vout": n,                         // Output index
      "fee": x.xxx,                      // Fee (if applicable)
      "confirmations": n,                // Number of confirmations
      "blockhash": "hashvalue",        // Block hash
      "blockindex": n,                   // Block index
      "blocktime": xxx,                  // Block time
      "txid": "transactionid",           // Transaction ID
      "time": xxx,                       // Transaction time
      "timereceived": xxx,               // Time received
      "comment": "...",                 // Comment (if any)
      "to": "...",                      // "To" comment (if any)
    },
    // ... more transactions
  ],
  "lastblock": "lastblockhash"         // Hash of the last block processed
}

```

### Examples

CLI Usage:

```bash
listsinceblock
```

```bash
listsinceblock "000000000000000bacf66f7497b7dc45ef753ee9a7d38571037cdb1a57f663ad" 6
```

JSON-RPC Call:

```json
{
  "method": "listsinceblock",
  "params": [
    "00000000000fd08c2fb661d2fcb0d49abb3a91e5f27082ce64feed3b4dede2e2",
    6,
    true
  ] // blockhash, target-confirmations, and includeWatchonly are optional
}
```

### Error Handling

If the given block hash is invalid, an error message will be returned.

### Notes

Useful for obtaining a filtered transaction history. Related commands: [ `listtransactions` ](#listtransactions), [ `gettransaction` ](#gettransaction)

---

## liststakingaddresses

Shows the list of staking addresses for this wallet.

### Synopsis

```bash
liststakingaddresses
```

### Description

Lists all the staking addresses in the current wallet. Staking addresses are used for staking KAON to earn rewards.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON array. Each element is a JSON object representing a staking address and includes the following:

- `label`: A user-defined label associated with the address, if any. Otherwise, an empty string.
- `address`: The KAON staking address.

### Examples

CLI Usage:

```bash
liststakingaddresses
```

JSON-RPC Call:

```json
{
  "method": "liststakingaddresses",
  "params": []
}
```

### Error Handling

This command might return an error if there are issues accessing or processing the wallet's addresses.

### Notes

This command helps users manage and keep track of their staking addresses. Related commands: [ `getnewstakingaddress` ](#getnewstakingaddress), [ `validateaddress` ](#validateaddress)

---

## listtransactions

Returns up to 'count' most recent transactions skipping the first 'from' transactions for account 'account'.

### Synopsis

```bash
listtransactions (<"account"> <count> <from> <includeWatchonly> <includeDelegated> <includeCold>)
```

### Description

Lists wallet transactions, optionally filtered by account, count, starting point, and inclusion of watch-only and delegated transactions. The `account` parameter is deprecated and should be set to "\*". You can specify the number of transactions to return ( `count` ), the number of transactions to skip ( `from` ), whether to include watch-only addresses ( `includeWatchonly` ), delegated transactions ( `includeDelegated` ), and cold staking transactions ( `includeCold` ).

### Arguments

| Name             | Type    | Required | Description                                                           |
| ---------------- | ------- | -------- | --------------------------------------------------------------------- |
| account          | string  | No       | **DEPRECATED.** The account name. Should be "\*".                     |
| count            | numeric | No       | The number of transactions to return (default: 10).                   |
| from             | numeric | No       | The number of transactions to skip (default: 0).                      |
| includeWatchonly | boolean | No       | Include transactions involving watch-only addresses (default: false). |
| includeDelegated | boolean | No       | Include delegated transactions (default: true).                       |
| includeCold      | boolean | No       | Include cold staking transactions (default: true).                    |

### Flags

None

### Input

- `account`: **DEPRECATED.** Use "\*".
- `count`: (Optional) Number of transactions to return. Defaults to 10.
- `from`: (Optional) Number of transactions to skip. Useful for pagination. Defaults to 0.
- `includeWatchonly`: (Optional) Whether to include watch-only transactions. Defaults to false.
- `includeDelegated`: (Optional) Whether to include delegated transactions. Defaults to true.
- `includeCold`: (Optional) Whether to include cold staking transactions. Defaults to true.

### Output

A JSON array of transaction objects. Each transaction object includes details such as account (deprecated), address, category ("send", "receive", or "move"), amount, fee, confirmations, block hash, transaction ID, time, and other relevant fields. Consult the original documentation for the complete transaction object structure.

### Examples

CLI Usage:

```bash
listtransactions
```

```bash
listtransactions "*" 20 100  false true false
```

JSON-RPC Call:

```json
{
  "method": "listtransactions",
  "params": ["*", 20, 100, false, true, false] // all parameters except account "*" are optional
}
```

### Error Handling

If invalid parameters are given (e.g. a negative count or from), an error message is returned.

### Notes

Provides a list of wallet transactions with flexible filtering options. Related commands: [ `gettransaction` ](#gettransaction), [ `listsinceblock` ](#listsinceblock)

---

## listunspent

Returns array of unspent transaction outputs
with between minconf and maxconf (inclusive) confirmations.
Optionally filter to only include txouts paid to specified addresses.
Results are an array of Objects, each of which has:
{txid, vout, scriptPubKey, amount, confirmations, spendable}.

### Synopsis

```bash
listunspent (<minconf> <maxconf> <["address",...]> <watchonlyconfig>)
```

### Description

Returns a list of unspent transaction outputs (UTXOs) in the wallet, filtered by minimum and maximum confirmations, addresses, and watch-only settings. This command is crucial for constructing new transactions, as it identifies available funds that can be used as inputs.

### Arguments

| Name            | Type    | Required | Description                                                                                                                          |
| --------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| minconf         | numeric | No       | The minimum number of confirmations required (default: 1).                                                                           |
| maxconf         | numeric | No       | The maximum number of confirmations allowed (default: 9999999).                                                                      |
| addresses       | string  | No       | A JSON array of addresses to filter by (optional). If provided, only outputs sent to these addresses are returned.                   |
| watchonlyconfig | numeric | No       | Configuration for handling watch-only addresses: 1 = list regular UTXOs, 2 = list only watch-only UTXOs, 3 = list both (default: 1). |

### Flags

None

### Input

- `minconf`: (Optional) Minimum confirmations required for a UTXO to be included in the result. Defaults to 1.
- `maxconf`: (Optional) Maximum confirmations allowed for a UTXO. Defaults to 9999999 (effectively including all confirmed transactions).
- `addresses`: (Optional) A JSON formatted array of Kaon addresses. If supplied, only outputs sent to those addresses are listed.
- `watchonlyconfig`: (Optional) Determines handling of watch-only addresses. 1 for regular UTXOs, 2 for watch-only UTXOs, 3 for both. Default is 1.

### Output

A JSON array of UTXO objects. Each object contains:

```json
[
  {
    "txid": "txid",             // Transaction ID
    "vout": n,                  // Output index
    "address": "address",       // Address associated with the output
    "account": "account",       // DEPRECATED. The associated account
    "scriptPubKey": "key",      // ScriptPubKey (locking script)
    "redeemScript": "key",      // Redeem script (for P2SH)
    "amount": x.xxx,             // Amount of KAON in the UTXO
    "confirmations": n,         // Number of confirmations
    "spendable": true|false     // Whether the UTXO is spendable by this wallet
  },
  // ... more UTXOs
]
```

### Examples

CLI Usage:

```bash
listunspent
```

```bash
listunspent 6 9999999 "[\"1PGFqEzfmQch1gKD3ra4k18PNj3tTUUSqg\",\"1LtvqCaApEdUGFkpKMM4MstjcaL4dKg8SP\"]"
```

JSON-RPC Call:

```json
{
  "method": "listunspent",
  "params": [6, 9999999, ["address1", "address2"], 3] // all parameters are optional
}
```

### Error Handling

Errors can occur if provided parameters are invalid (such as negative minconf/maxconf).

### Notes

This is an important command used when constructing transactions. It identifies available funds for use as inputs. The `watchonlyconfig` parameter allows including or excluding watch-only addresses from the output. Related commands: [ `gettxout` ](#gettxout), [ `lockunspent` ](#lockunspent), [ `listlockunspent` ](#listlockunspent)

---

## loadwallet

Reloads a wallet from a wallet file from data dir.
Note that all wallet command-line options used when starting kaond will be
applied to the new wallet (eg -rescan, etc).

### Synopsis

```bash
loadwallet "<filename>" (<load_on_startup>)
```

### Description

Loads a wallet from a specified file. This allows switching between different wallets or restoring a previously saved wallet. Command-line options passed when launching `kaond` will also be applied to the loaded wallet (e.g., `-rescan` ).

### Arguments

| Name            | Type   | Required | Description                                                                                                                  |
| --------------- | ------ | -------- | ---------------------------------------------------------------------------------------------------------------------------- |
| filename        | string | Yes      | The filename of the wallet (including the .dat extension, or directory name).                                                |
| load_on_startup | string | No       | Controls whether the wallet is loaded on startup. "true" to add to startup list, "false" to remove, null to leave unchanged. |

### Flags

None

### Input

- `filename`: The wallet filename or directory.
- `load_on_startup`: (Optional) A string: "true" (add to startup), "false" (remove from startup), or null (no change to startup settings).

### Output

```json
{
  "name": "name", // Wallet name if loaded successfully
  "warning": "warning" // Warning message if loaded with issues
}
```

### Examples

CLI Usage:

```bash
loadwallet "test.dat"
```

JSON-RPC Call:

```json
{
  "method": "loadwallet",
  "params": ["test.dat", "true"] // "load_on_startup" is optional
}
```

### Error Handling

Returns an error if the specified wallet file does not exist, is invalid, or cannot be loaded. The "warning" field in the output might contain information about issues encountered during loading, even if the wallet is loaded successfully.

### Notes

Loading a wallet makes it the active wallet for subsequent commands. Any command-line arguments applied when starting the Kaon daemon will also affect the loaded wallet.

---

## lockunspent

Updates list of temporarily unspendable outputs.
Temporarily lock (unlock=false) or unlock (unlock=true) specified transaction outputs.
A locked transaction output will not be chosen by automatic coin selection, when spending KAONs.
Locks are stored in memory only. Nodes start with zero locked outputs, and the locked output list
is always cleared (by virtue of process exit) when a node stops or fails.
Also see the listunspent call.

### Synopsis

```bash
lockunspent <unlock> <"transactions">
```

### Description

Locks or unlocks specified transaction outputs (UTXOs). Locked UTXOs are excluded from automatic coin selection when creating new transactions. This allows reserving specific UTXOs for particular purposes. Locks are stored in memory and are reset when the node restarts.

### Arguments

| Name         | Type    | Required | Description                                                          |
| ------------ | ------- | -------- | -------------------------------------------------------------------- |
| unlock       | boolean | Yes      | Whether to unlock (true) or lock (false) the specified transactions. |
| transactions | string  | Yes      | A JSON array of UTXO objects to lock or unlock.                      |

### Flags

None

### Input

- `unlock`: Boolean value: true to unlock, false to lock.
- `transactions`: A JSON array of objects. Each object should contain the `txid` (transaction ID) and `vout` (output index) of the UTXO to lock or unlock.
  Example: `"[{\"txid\":\"mytxid\",\"vout\":0},{\"txid\":\"anothertxid\",\"vout\":1}]"`

### Output

- `true`: If the command was successful.
- `false`: If the command failed.

### Examples

CLI Usage:

```bash
lockunspent false "[{\"txid\":\"a08e6907dbbd3d809776dbfc5d82e371b764ed838b5655e72f463568df1aadf0\",\"vout\":1}]"
```

```bash
lockunspent true "[{\"txid\":\"a08e6907dbbd3d809776dbfc5d82e371b764ed838b5655e72f463568df1aadf0\",\"vout\":1}]"
```

JSON-RPC Call:

```json
{
  "method": "lockunspent",
  "params": [
    false,
    [
      {
        "txid": "a08e6907dbbd3d809776dbfc5d82e371b764ed838b5655e72f463568df1aadf0",
        "vout": 1
      }
    ]
  ]
}
```

### Error Handling

Returns an error if the `transactions` array contains invalid transaction IDs or output indices, or if other errors occur related to locking/unlocking outputs.

### Notes

Locking UTXOs prevents them from being used as inputs in automatically constructed transactions. Remember that locks are memory-resident and are cleared on node restart. Related commands: [ `listlockunspent` ](#listlockunspent), [ `listunspent` ](#listunspent)

---

## masternode connect

Attempts to connect to specified masternode address.

### Synopsis

```bash
masternodeconnect "<address>"
```

### Description

Attempts a direct connection to a specific masternode. This command is helpful for troubleshooting network connectivity issues or for forcing a connection to a particular masternode.

### Arguments

| Name    | Type   | Required | Description             |
| ------- | ------ | -------- | ----------------------- |
| address | string | Yes      | The masternode address. |

### Flags

None

### Input

- `address`: IP address or hostname of the masternode, including the port number.

### Output

The output can vary depending on the implementation but typically indicates success or failure in establishing a connection. It might also display an error message if the connection attempt fails.

### Examples

CLI Usage:

```bash
masternodeconnect "192.168.0.6:9872"
```

JSON-RPC Call:

```json
{
  "method": "masternodeconnect",
  "params": ["192.168.0.6:9871"]
}
```

### Error Handling

- Returns an error if the provided address is invalid or unreachable.
- Returns an error if a connection cannot be established.

### Notes

Attempts a P2P connection to a masternode. This doesn't affect masternode status or configuration. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodestatus` ](#getmasternodestatus)

---

## masternodecurrent

Get current masternode winner.

### Synopsis

```bash
masternodecurrent
```

### Description

Returns details about the current masternode winner. The "winner" refers to the masternode selected to create the next block, if the network operates with a masternode-based consensus mechanism.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object containing information about the currently selected masternode:

```json
{
  "protocol": xxxx,       // Protocol version
  "txhash": "xxxx",       // Collateral transaction hash
  "pubkey": "xxxx",       // Masternode public key
  "lastseen": xxx,        // Last seen timestamp
  "activeseconds": xxx   // Active time in seconds
}
```

### Examples

CLI Usage:

```bash
masternodecurrent
```

JSON-RPC Call:

```json
{
  "method": "masternodecurrent",
  "params": []
}
```

### Error Handling

If information about the current masternode winner isn't available, this command might return an error.

### Notes

Use this command to get information about the active masternode that is currently or was recently responsible for block creation. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodewinners` ](#getmasternodewinners)

---

## masternodedebug

Print masternode status.

### Synopsis

```bash
masternodedebug
```

### Description

Prints debugging information about the local masternode status and configuration. This command is helpful for troubleshooting issues related to masternode setup and operation.

### Arguments

None

### Flags

None

### Input

None

### Output

A string containing the masternode status message. This message provides insights into the masternode's current state and any potential problems.

### Examples

CLI Usage:

```bash
masternodedebug
```

JSON-RPC Call:

```json
{
  "method": "masternodedebug",
  "params": []
}
```

### Error Handling

Errors are unlikely under normal circumstances. However, if unexpected conditions arise related to the masternode configuration or operation, an error message may be displayed.

### Notes

This command is primarily intended for diagnostic purposes. Related commands: [ `getmasternodestatus` ](#getmasternodestatus), [ `listmasternodes` ](#listmasternodes), [ `startmasternode` ](#startmasternode)

---

## mnbudgetrawvote

Compile and relay a proposal vote with provided external signature instead of signing vote internally.

### Synopsis

```bash
mnbudgetrawvote "<masternode-tx-hash>" <masternode-tx-index> "<proposal-hash>" <yes|no> <time> "<vote-sig>"
```

### Description

Allows voting on a budget proposal using an externally created signature. This command is used when a masternode owner wants to vote on a proposal without directly using the masternode's private key on the node where the wallet is located. This enhances security by allowing offline signing of the vote.

### Arguments

| Name                | Type    | Required | Description                                                 |
| ------------------- | ------- | -------- | ----------------------------------------------------------- |
| masternode-tx-hash  | string  | Yes      | The transaction hash of the masternode's collateral output. |
| masternode-tx-index | numeric | Yes      | The output index of the masternode's collateral output.     |
| proposal-hash       | string  | Yes      | The hash of the budget proposal.                            |
| yes\|no             | boolean | Yes      | The vote: "yes" to approve, "no" to reject.                 |
| time                | numeric | Yes      | The current time in seconds since epoch.                    |
| vote-sig            | string  | Yes      | The external signature for the vote.                        |

### Flags

None

### Input

- `masternode-tx-hash`: The transaction ID of the masternode's collateral.
- `masternode-tx-index`: The output index of the masternode's collateral.
- `proposal-hash`: The hash of the proposal being voted on.
- `yes|no`: The vote itself ("yes" or "no").
- `time`: The current Unix timestamp.
- `vote-sig`: The externally generated signature for the vote.

### Output

The vote status ("success" or an error message).

### Examples

CLI Usage:

```bash
mnbudgetrawvote "masternodetxhash" 0 "proposalhash" "yes" 1678886400 "votesig"
```

JSON-RPC Call:

```json
{
  "method": "mnbudgetrawvote",
  "params": [
    "masternodetxhash",
    0,
    "proposalhash",
    "yes",
    1678886400,
    "votesig"
  ]
}
```

### Error Handling

If any of the parameters are invalid, or if there is a problem relaying the vote, an error message will be returned.

### Notes

This command allows secure, offline signing of budget proposal votes. Related commands: [ `mnbudgetvote` ](#mnbudgetvote), [ `mnfinalbudget` ](#mnfinalbudget), [ `getbudgetinfo` ](#getbudgetinfo)

---

## mnbudgetvote

Vote on a budget proposal.

### Synopsis

```bash
mnbudgetvote "<local|many|alias>" "<votehash>" "<yes|no>" (<"alias">)
```

### Description

Votes on a masternode budget proposal. The "mode" parameter specifies how the vote is cast:

- `local`: Votes directly from the local masternode.
- `many`: Votes from multiple masternodes controlled by the wallet (requires a masternode controller setup).
- `alias`: Votes from a single masternode specified by its alias (also requires a masternode controller).

### Arguments

| Name     | Type   | Required                 | Description                                                             |
| -------- | ------ | ------------------------ | ----------------------------------------------------------------------- |
| mode     | string | Yes                      | Voting mode: "local", "many", or "alias".                               |
| votehash | string | Yes                      | The hash of the proposal to vote on.                                    |
| votecast | string | Yes                      | The vote: "yes" or "no".                                                |
| alias    | string | Yes (if mode is "alias") | The alias of the masternode to vote from (required if mode is "alias"). |

### Flags

None

### Input

- `mode`: The voting mode ("local", "many", or "alias").
- `votehash`: The hash of the budget proposal.
- `votecast`: The vote ("yes" or "no").
- `alias`: (Optional, required for "alias" mode) The alias of the masternode.

### Output

A JSON object indicating the overall voting status and details for each masternode involved.

```json
{
  "overall": "xxxx", // Overall status message
  "detail": [
    {
      "node": "xxxx", // Node name (local or alias)
      "result": "xxxx", // "success" or "failed"
      "error": "xxxx" // Error message (if any)
    }
    // ... more details for other masternodes (if applicable)
  ]
}
```

### Examples

CLI Usage:

```bash
mnbudgetvote "local" "ed2f83cedee59a91406f5f47ec4d60bf5a7f9ee6293913c82976bd2d3a658041" "yes"
```

JSON-RPC Call:

```json
{
  "method": "mnbudgetvote",
  "params": [
    "local",
    "ed2f83cedee59a91406f5f47ec4d60bf5a7f9ee6293913c82976bd2d3a658041",
    "yes"
  ]
}
```

### Error Handling

If any of the parameters are invalid, if the vote cannot be cast, or if another error arises, an error message is returned, usually within the `detail` array for the specific node where it occurred.

### Notes

Used to cast votes on masternode budget proposals. Ensure the `votehash` corresponds to a valid proposal. The "many" and "alias" modes require additional setup for controlling multiple masternodes. Related commands: [ `preparebudget` ](#preparebudget), [ `submitbudget` ](#submitbudget), [ `getbudgetinfo` ](#getbudgetinfo), [ `getbudgetvotes` ](#getbudgetvotes), [ `mnfinalbudget` ](#mnfinalbudget), [ `mnbudgetrawvote` ](#mnbudgetrawvote)

---

## mnfinalbudget

Vote or show current budgets.

### Synopsis

```bash
mnfinalbudget "<command>" ... (<"passphrase">)
```

### Description

Manages finalization of budget proposals. This command includes several subcommands:

- `vote-many`: Vote on a finalized budget from multiple masternodes.
- `vote`: Vote on a finalized budget from a single masternode.
- `show`: Display current finalized budgets.
- `getvotes`: Retrieve vote information for finalized budgets.

### Arguments

| Name       | Type   | Required | Description                                                                                            |
| ---------- | ------ | -------- | ------------------------------------------------------------------------------------------------------ |
| command    | string | Yes      | The command: "vote-many", "vote", "show", or "getvotes".                                               |
| ...        |        |          | Additional parameters depending on the chosen command (see further documentation for each subcommand). |
| passphrase | string | No       | Wallet passphrase if the wallet is encrypted.                                                          |

### Flags

None

### Input

The input depends on the specific subcommand used. See below and refer to further documentation for details on each subcommand's parameters.

### Output

The output varies depending on the chosen command. Consult the original documentation for the specific output structure for each subcommand.

### Examples

The example usage is dependent on the chosen subcommand. See documentation on `mnfinalbudget <command>` for details about each subcommand.

CLI Usage:

```bash
mnfinalbudget show
```

```bash
mnfinalbudget getvotes "proposal_hash"
```

JSON-RPC Call:

```json
{
  "method": "mnfinalbudget",
  "params": ["show"] // Subcommand parameters are required based on the subcommand
}
```

### Error Handling

Returns an error if the command or subcommand is invalid, parameters are missing or incorrect, or another error occurs during budget finalization.

### Notes

`mnfinalbudget` manages the final budget voting and review process for masternodes. Related commands: [ `preparebudget` ](#preparebudget), [ `submitbudget` ](#submitbudget), [ `mnbudgetvote` ](#mnbudgetvote), [ `getbudgetinfo` ](#getbudgetinfo)

---

## mnping

Send masternode ping. Only for remote masternodes on Regtest.

### Synopsis

```bash
mnping
```

### Description

Sends a ping message from your masternode. This command is primarily used for testing purposes on the Regtest network, typically for remote masternodes.

### Arguments

None

### Flags

None

### Input

None

### Output

A JSON object indicating whether the ping was sent successfully.

```json
{
  "sent": "YES"|"NO" // "YES" if successful, "NO" if not
}

```

### Examples

CLI Usage:

```bash
mnping
```

JSON-RPC Call:

```json
{
  "method": "mnping",
  "params": []
}
```

### Error Handling

If there's an issue sending the ping (e.g. the masternode is not running or not configured correctly), the "sent" field will be "NO".

### Notes

Used for testing masternode functionality on Regtest. Related commands: [ `startmasternode` ](#startmasternode), [ `getmasternodestatus` ](#getmasternodestatus), [ `listmasternodes` ](#listmasternodes)

---

## mnregvalidator

Apply to register a validator.

### Synopsis

```bash
mnregvalidator "<account-name>"
```

### Description

Applies to register a validator node on the Kaon network.

### Arguments

| Name         | Type   | Required | Description             |
| ------------ | ------ | -------- | ----------------------- |
| account-name | string | Yes      | The masternode account. |

### Flags

None

### Input

- `account-name`: The name of the masternode account.

### Output

A JSON array where each element is a JSON object representing a registered validator with their registration hash.

### Examples

CLI Usage:

```bash
mnregvalidator "MN1"
```

JSON-RPC Call:

```json
{
  "method": "mnregvalidator",
  "params": ["MN1"]
}
```

### Error Handling

Errors can occur if the registration attempt fails due to incorrect parameters, insufficient funds, or network issues.

### Notes

This applies to register a validator node. Related commands: [ `mnvotevalidator` ](#mnvotevalidator), [ `mnregvalidatorlist` ](#mnregvalidatorlist)

---

## mnsync

Returns the sync status or resets sync.

### Synopsis

```bash
mnsync "<status|reset>"
```

### Description

Used to check or control the masternode synchronization (mnsync) status. This synchronization process involves updating the local node's list of masternodes, their statuses, and related information.

### Arguments

| Name | Type   | Required | Description                                              |
| ---- | ------ | -------- | -------------------------------------------------------- |
| mode | string | Yes      | The command: "status" to check status, "reset" to reset. |

### Flags

None

### Input

- `mode`: Either "status" or "reset".

### Output

- If mode is "status": Returns a JSON object containing various details about the synchronization status, including flags for blockchain sync, masternode list sync, masternode winner sync, budget sync, and timestamps of the last successful updates. See original documentation for full details of the returned JSON object.
- If mode is "reset": Returns the string "status": "success" if the reset was successful.

### Examples

CLI Usage:

```bash
mnsync "status"
```

```bash
mnsync "reset"
```

JSON-RPC Call:

```json
{
  "method": "mnsync",
  "params": ["status"]
}
```

### Error Handling

If an invalid mode is provided or if an issue occurs during status retrieval or reset, an error message might be returned.

### Notes

The masternode sync process is essential for the proper functioning of masternode features. This command helps check and control the sync. Related commands: [ `listmasternodes` ](#listmasternodes), [ `getmasternodecount` ](#getmasternodecount)

---

## mnvotevalidator

Voting for validators from registered list.
List of registered validators you can see by calling mnregvalidatorlist.

### Synopsis

```bash
mnvotevalidator "<valvote>"
```

### Description

Allows voting for validators from a list of registered validators. This is part of Kaon's governance mechanism where masternode owners can vote for validators to participate in block production and network consensus.

### Arguments

| Name    | Type   | Required | Description                                                                                            |
| ------- | ------ | -------- | ------------------------------------------------------------------------------------------------------ |
| valvote | string | Yes      | A JSON array of objects. Each object contains the validator's public key and the vote ("yes" or "no"). |

### Flags

None

### Input

- `valvote`: A JSON string representing an array of vote objects. Each vote object must contain "pubkey" (the validator's public key in hexadecimal format) and "vote" ("yes" or "no"). Example: `"[{\"pubkey\":\"pubkeyhex1\",\"vote\":\"yes\"},{\"pubkey\":\"pubkeyhex2\",\"vote\":\"no\"}]"`

### Output

The hash of the vote transaction.

### Examples

CLI Usage:

```bash
mnvotevalidator "[{\"pubkey\":\"a08e6907dbbd3d809776dbfc5d82e371b764ed838b5655e72f463568df1aadf0\",\"vote\":\"yes\"}]"
```

JSON-RPC Call:

```json
{
  "method": "mnvotevalidator",
  "params": [
    "[{\"pubkey\":\"a08e6907dbbd3d809776dbfc5d82e371b764ed838b5655e72f463568df1aadf0\",\"vote\":\"yes\"}]"
  ]
}
```

### Error Handling

- Returns an error if any of the provided public keys are invalid, if the votes are not correctly formatted, or if the vote transaction cannot be created or sent.

### Notes

Allows for voting for registered validators. Related commands: [ `mnregvalidator` ](#mnregvalidator), [ `mnregvalidatorlist` ](#mnregvalidatorlist)

---

## move

DEPRECATED. Move a specified amount from one account in your wallet to another.

### Synopsis

```bash
move "<fromaccount>" "<toaccount>" <amount> (<minconf> "<comment">)
```

### Description

**DEPRECATED.** This command is deprecated and should not be used. It was previously used to move funds between accounts within the wallet. Account functionality has been deprecated.

### Arguments

| Name        | Type    | Required | Description                                                             |
| ----------- | ------- | -------- | ----------------------------------------------------------------------- |
| fromaccount | string  | Yes      | The source account name (can be "" for default).                        |
| toaccount   | string  | Yes      | The destination account name (can be "" for default).                   |
| amount      | numeric | Yes      | The amount to move.                                                     |
| minconf     | numeric | No       | Minimum number of confirmations for the funds to be moved (default: 1). |
| comment     | string  | No       | A comment to store with the transaction (optional).                     |

### Flags

None

### Input

- `fromaccount`: The source account (can be "" for default).
- `toaccount`: The destination account (can be "" for default).
- `amount`: The quantity of KAON to transfer.
- `minconf`: (Optional) Minimum confirmations for inputs. Default is 1.
- `comment`: (Optional) A comment to store with the transaction (wallet only).

### Output

- `true` if the funds were successfully moved.
- `false` otherwise.

### Examples

CLI Usage:

```bash
move "" "tabby" 0.01
```

```bash
move "timotei" "akiko" 0.01 1 "happy birthday!"
```

JSON-RPC Call:

```json
{
  "method": "move",
  "params": ["timotei", "akiko", 0.01, 1, "happy birthday!"] // minconf and comment are optional
}
```

### Error Handling

If any account name is invalid, the amount is negative, insufficient funds are available, or any other error arises, an error message will be returned.

### Notes

This command is deprecated. Account-based operations are discouraged. Use [ `sendtoaddress` ](#sendtoaddress) to send coins directly between addresses.

---

## multisend

Multisend commands.

### Synopsis

```bash
multisend <command>
```

### Description

Provides a set of commands related to the MultiSend feature, which allows automatic distribution of staking or masternode rewards to multiple addresses.

### Arguments

| Name    | Type   | Required | Description                          |
| ------- | ------ | -------- | ------------------------------------ |
| command | string | Yes      | The multisend subcommand to execute. |

### Flags

None

### Input

- `command`: One of the MultiSend subcommands (see below).

### Output

The output depends on the specific subcommand. See the descriptions of individual MultiSend subcommands below.

### Multisend Subcommands

- `print`: Displays the current MultiSend vector (list of configured payout addresses and percentages).
- `clear`: Clears the current MultiSend vector.
- `activatestake` or `enablestake`: Activates the MultiSend vector for stake rewards.
- `activatemasternode` or `enablemasternode`: Activates MultiSend for masternode rewards.
- `disable` or `deactivate`: Disables MultiSend.
- `delete <Address #>`: Deletes an address from the MultiSend vector by its index number.
- `disable <address>`: Prevents a specific address from receiving MultiSend payouts.
- `enableall`: Enables all addresses to receive MultiSend payouts.

### Examples

CLI Usage:

```bash
multisend print
```

```bash
multisend activatestake
```

```bash
multisend delete 1
```

```bash
multisend <kaonaddress> <percent> // Adds an address to the MultiSend vector. <percent> must be an integer from 1 to 100.
```

JSON-RPC Call:
(Not directly available as a single JSON-RPC call. It requires interacting with the underlying functionality based on the specific subcommand. See individual subcommand documentation).

### Error Handling

The error handling is specific to each subcommand. If the provided command or parameters are invalid, an error message will be returned.

### Notes

Multisend allows automated distribution of staking and masternode rewards to multiple addresses, which is useful for sharing rewards or automatically reinvesting them.

---

## ping

Requests that a ping be sent to all other nodes, to measure ping time.
Results provided in getpeerinfo, pingtime and pingwait fields are decimal seconds.
Ping command is handled in queue with all other commands, so it measures processing backlog, not just network ping.

### Synopsis

```bash
ping
```

### Description

Sends a ping message to all connected peers. This command is used to measure the latency (ping time) to other nodes in the Kaon network.

### Arguments

None

### Flags

None

### Input

None

### Output

None. (Ping times are returned by the `getpeerinfo` command.)

### Examples

CLI Usage:

```bash
ping
```

JSON-RPC Call:

```json
{
  "method": "ping",
  "params": []
}
```

### Error Handling

This command rarely returns errors.

### Notes

This command measures network latency and processing time. Actual ping time values can be retrieved using the `getpeerinfo` command. Related commands: [ `getpeerinfo` ](#getpeerinfo)

---

## preparebudget

Prepare proposal for network by signing and creating tx.

### Synopsis

```bash
preparebudget "<proposal-name>" "<url>" <payment-count> <block-start> "<kaon-address>" <monthly-payment>
```

### Description

Prepares a budget proposal to be submitted to the network. This command involves signing and creating the proposal transaction, which needs to be submitted in a later step.

### Arguments

| Name            | Type    | Required | Description                                          |
| --------------- | ------- | -------- | ---------------------------------------------------- |
| proposal-name   | string  | Yes      | The name of the budget proposal (max 20 characters). |
| url             | string  | Yes      | The URL with proposal details (max 64 characters).   |
| payment-count   | numeric | Yes      | The number of monthly payments.                      |
| block-start     | numeric | Yes      | The starting superblock height for payments.         |
| kaon-address    | string  | Yes      | The Kaon address to receive payments.                |
| monthly-payment | numeric | Yes      | The monthly payment amount in KAON.                  |

### Flags

None

### Input

- `proposal-name`: The name for your proposal. Must be 20 characters or less.
- `url`: URL providing details about the proposal. Must be 64 characters or less.
- `payment-count`: Total number of monthly payments to be made if the proposal is accepted.
- `block-start`: Starting superblock height for the payments. This indicates when the payments should begin if the proposal is approved.
- `kaon-address`: Kaon address to receive the budget payments.
- `monthly-payment`: Amount to be paid monthly in KAON.

### Output

The proposal's fee tx hash (hexadecimal string) if successful, or an error message if the preparation fails.

### Examples

CLI Usage:

```bash
preparebudget "test-proposal" "https://forum.kaon.one/t/test-proposal" 2 820800 "D9oc6C3dttUbv8zd7zGNq1qKBGf4ZQ1XEE" 500
```

JSON-RPC Call:

```json
{
  "method": "preparebudget",
  "params": [
    "test-proposal",
    "https://forum.kaon.one/t/test-proposal",
    2,
    820800,
    "D9oc6C3dttUbv8zd7zGNq1qKBGf4ZQ1XEE",
    500
  ]
}
```

### Error Handling

Returns an error if any of the provided parameters are invalid, if the proposal cannot be prepared, or if there are insufficient funds to cover the proposal fee.

### Notes

This command prepares a budget proposal, but the proposal still needs to be submitted to the network using the `submitbudget` command. Related commands: [ `submitbudget` ](#submitbudget), [ `getbudgetinfo` ](#getbudgetinfo), [ `mnbudgetvote` ](#mnbudgetvote)

---

## prioritisetransaction

Accepts the transaction into mined blocks at a higher (or lower) priority.

### Synopsis

```bash
prioritisetransaction "<txid>" <priority delta> <fee delta>
```

### Description

Allows you to influence the priority of a transaction in the memory pool. By adjusting the priority and fee delta, you can make a transaction more or less likely to be included in the next mined block.

### Arguments

| Name           | Type    | Required | Description                                                                                                                                                                                    |
| -------------- | ------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| txid           | string  | Yes      | The transaction ID.                                                                                                                                                                            |
| priority delta | numeric | Yes      | The adjustment to the transaction's priority. A higher value increases priority.                                                                                                               |
| fee delta      | numeric | Yes      | The adjustment to the transaction fee (in satoshis). This does not actually add or subtract from the fee, but influences the transaction selection process as if the fee were higher or lower. |

### Flags

None

### Input

- `txid`: The transaction ID.
- `priority delta`: The priority adjustment value. Higher values increase the transaction's priority.
- `fee delta`: The fee adjustment value (in satoshis). This doesn't change the actual fee paid but simulates a higher or lower fee for prioritization purposes.

### Output

Returns `true` if successful.

### Examples

CLI Usage:

```bash
prioritisetransaction "txid" 0.0 10000
```

JSON-RPC Call:

```json
{
  "method": "prioritisetransaction",
  "params": ["txid", 0.0, 10000]
}
```

### Error Handling

Returns an error if the transaction ID is invalid, the deltas are invalid, or another issue prevents prioritization.

### Notes

Use this command to fine-tune the confirmation speed of your transactions. However, miners ultimately decide which transactions to include in blocks.

---

## rawdelegatestake

Delegate an amount to a given address for cold staking. The amount is a real and is rounded to the nearest 0.00000001.
Delegate transaction is returned as json object.

### Synopsis

```bash
rawdelegatestake "<stakingaddress>" <amount> (<"owneraddress"> <fExternalOwner> <fUseDelegated>)
```

### Description

Creates a raw, unsigned transaction for delegating a stake to a cold staking address. Unlike the `delegatestake` command, which handles the entire delegation process, this command generates the raw transaction without signing or broadcasting it. This is useful for offline signing scenarios or for constructing custom transactions.

### Arguments

| Name           | Type    | Required | Description                                                                                                 |
| -------------- | ------- | -------- | ----------------------------------------------------------------------------------------------------------- |
| stakingaddress | string  | Yes      | The cold staking address.                                                                                   |
| amount         | numeric | Yes      | The amount to delegate in KAON.                                                                             |
| owneraddress   | string  | No       | The Kaon address that can spend the staking rewards.                                                        |
| fExternalOwner | boolean | No       | Use the specified `owneraddress` even if it isn't present in the wallet (default: false, use with caution). |
| fUseDelegated  | boolean | No       | Include already delegated UTXOs as inputs if needed (default: false).                                       |

### Flags

None

### Input

- `stakingaddress`: The Kaon address to delegate the stake to. This should be a cold staking address.
- `amount`: The amount to delegate.
- `owneraddress`: (Optional) The Kaon address that will control the staking rewards. If not provided, a new address is generated.
- `fExternalOwner`: (Optional) If true, allows using an `owneraddress` not managed by this wallet (default: false). WARNING: Exercise caution when setting to true.
- `fUseDelegated`: (Optional) Include already delegated inputs. Default is false.

### Output

A JSON object representing the raw, unsigned delegation transaction. This includes standard transaction details like `txid` , `vin` , `vout` , and `hex` . See original documentation or [ `createrawtransaction` ](#createrawtransaction) command for the structure.

### Examples

CLI Usage:

```bash
rawdelegatestake "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 100
```

JSON-RPC Call:

```json
{
  "method": "rawdelegatestake",
  "params": [
    "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6",
    100,
    "owner_address",
    false,
    true
  ] // owneraddress, fExternalOwner, fUseDelegated are optional
}
```

### Error Handling

If the staking address, owner address (if provided), or amount is invalid, an error is returned. An error can also be returned if the wallet doesn't have enough balance for the delegation, or if other issues occur during transaction creation.

### Notes

This command generates a raw, unsigned transaction that requires further processing (signing and broadcasting) before the delegation is complete. Related commands: [ `delegatestake` ](#delegatestake), [ `signrawtransactionwithwallet` ](#signrawtransactionwithwallet), [ `sendrawtransaction` ](#sendrawtransaction), [ `createrawtransaction` ](#createrawtransaction), [ `decoderawtransaction` ](#decoderawtransaction)

---

## rawleasetoaddress

Lease an amount to a given address for mining. The amount is a real and is rounded to the nearest 0.00000001.

### Synopsis

```bash
rawleasetoaddress "<leasingaddress>" <amount> (<"owneraddress"> <fExternalOwner>)
```

### Description

Creates an unsigned raw transaction for leasing KAON to a given address for mining. Unlike `leasetoaddress` , it does not sign or broadcast the transaction. Designed for offline signing or custom transaction construction.

### Arguments

| Name           | Type    | Required | Description                                                                                                           |
| -------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| leasingaddress | string  | Yes      | The Kaon address to lease to (miner's address).                                                                       |
| amount         | numeric | Yes      | The amount to lease in KAON.                                                                                          |
| owneraddress   | string  | No       | The Kaon address that can spend the lease rewards. If not provided, a new address is generated within the wallet.     |
| fExternalOwner | boolean | No       | Allows the use of an external `owneraddress` not managed by this wallet. Default is false. WARNING: Use with caution. |

### Flags

None

### Input

- `leasingaddress`: The miner's Kaon address for receiving the leased funds.
- `amount`: The amount of KAON to lease.
- `owneraddress`: (Optional) The address that will control the leased funds and receive the rewards. If not provided, a new address will be generated within your wallet.
- `fExternalOwner`: (Optional) A boolean flag. If set to true, allows the `owneraddress` to be one not controlled by this wallet. Default is false. WARNING: Use with extreme caution as it grants spending authority to an external address.

### Output

A JSON object representing the unsigned raw leasing transaction. This includes standard transaction details like transaction ID, version, size, inputs, outputs, and the hexadecimal representation of the transaction. Refer to the [ `createrawtransaction` ](#createrawtransaction) command output details for more information.

### Examples

CLI Usage:

```bash
rawleasetoaddress "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 100
```

JSON-RPC Call:

```json
{
  "method": "rawleasetoaddress",
  "params": ["S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6", 100, "owner_address", false] // owneraddress and fExternalOwner are optional
}
```

### Error Handling

- If any address is invalid, if the amount is insufficient, or any other error occurs during transaction creation, an error message is returned.
- If the SPORK 1017 controlling the leasing feature is not active, this command will result in an error unless a special flag for bypassing the check (for testing) is used.

### Notes

This command generates the raw, unsigned transaction, which must be signed and broadcasted to complete the leasing process. Related commands: [ `leasetoaddress` ](#leasetoaddress), [ `signrawtransactionwithwallet` ](#signrawtransactionwithwallet), [ `sendrawtransaction` ](#sendrawtransaction), [ `createrawtransaction` ](#createrawtransaction), [ `decoderawtransaction` ](#decoderawtransaction)

---

## reconsiderblock

Removes invalidity status of a block and its descendants, reconsider them for activation.
This can be used to undo the effects of invalidateblock.

### Synopsis

```bash
reconsiderblock "<hash>"
```

### Description

Reverses the invalidation of a specific block and any blocks built on top of it (its descendants), allowing them to be reconsidered for inclusion in the main chain. This command effectively undoes the action of the `invalidateblock` command.

### Arguments

| Name | Type   | Required | Description            |
| ---- | ------ | -------- | ---------------------- |
| hash | string | Yes      | The hash of the block. |

### Flags

None

### Input

- `hash`: The hash of the block to be reconsidered.

### Output

None

### Examples

CLI Usage:

```bash
reconsiderblock "blockhash"
```

JSON-RPC Call:

```json
{
  "method": "reconsiderblock",
  "params": ["blockhash"]
}
```

### Error Handling

Returns an error if the block hash is invalid or if the block cannot be reconsidered (e.g. if it's already part of the main chain or still violates consensus rules).

### Notes

Use this command with care as it modifies the blockchain's structure. Related commands: [ `invalidateblock` ](#invalidateblock)

---

## relaymasternodebroadcast

Command to relay masternode broadcast messages.

### Synopsis

```bash
relaymasternodebroadcast "<hexstring>"
```

### Description

Relays a masternode broadcast message to the network. This is typically used after a masternode broadcast has been created (using `createmasternodebroadcast` ) and potentially signed offline. Relaying the message announces the masternode to other nodes in the network.

### Arguments

| Name      | Type   | Required | Description                        |
| --------- | ------ | -------- | ---------------------------------- |
| hexstring | string | Yes      | The hex-encoded broadcast message. |

### Flags

None

### Input

- `hexstring`: The hexadecimal representation of the masternode broadcast message. This is the message you want to distribute across the network.

### Output

The result of the relay operation. This may vary depending on the implementation but generally indicates success or failure. It might return a transaction ID or an error message if the relay fails.

### Examples

CLI Usage:

```bash
relaymasternodebroadcast "hexstring"
```

JSON-RPC Call:

```json
{
  "method": "relaymasternodebroadcast",
  "params": ["hexstring"]
}
```

### Error Handling

Returns an error if the provided `hexstring` is not a valid masternode broadcast message or if the relay operation fails (e.g. due to network issues).

### Notes

Relaying the masternode broadcast is a crucial step in announcing the masternode to the network. Make sure the broadcast message is correctly formed and signed before relaying it. Related commands: [ `createmasternodebroadcast` ](#createmasternodebroadcast), [ `decodemasternodebroadcast` ](#decodemasternodebroadcast)

---

## reprocess

Reprocess blocks immediately (before the RPC call returns). Note: this function can only be used on the regtest network.

### Synopsis

```bash
reprocess <numblocks>
```

### Description

Reprocesses a specified number of blocks. This command is primarily for testing and debugging on the regtest network. It forces the node to re-validate and re-process the specified blocks, which can be useful for simulating scenarios or analyzing behavior.

### Arguments

| Name      | Type    | Required | Description                    |
| --------- | ------- | -------- | ------------------------------ |
| numblocks | numeric | Yes      | Number of blocks to reprocess. |

### Flags

None

### Input

- `numblocks`: The number of blocks to reprocess. Should be a positive integer.

### Output

The specific output may vary depending on the version or implementation but typically includes logging information about the reprocessing status of the blocks.

### Examples

CLI Usage:

```bash
reprocess 11
```

JSON-RPC Call:

```json
{
  "method": "reprocess",
  "params": [11]
}
```

### Error Handling

If the command is used outside of regtest or if an invalid number of blocks is specified, it will return an error. Errors might also occur during the reprocessing itself.

### Notes

This is intended for testing and development on the regtest network only. Running it on mainnet or testnet is unlikely to have any effect and might cause errors.

---

## reservebalance

Show or set the reserve amount not participating in network protection. If no parameters provided current setting is printed.

### Synopsis

```bash
reservebalance (<reserve> <amount>)
```

### Description

Controls the reserve balance, which is the amount of KAON in your wallet that is excluded from staking and masternode operations. This command allows you to set the reserve amount or view the current reserve setting. If no parameters are provided, it displays the current reserve status and amount.

### Arguments

| Name    | Type    | Required | Description                               |
| ------- | ------- | -------- | ----------------------------------------- |
| reserve | boolean | No       | True to enable reserve, false to disable. |
| amount  | numeric | No       | The amount to reserve in KAON.            |

### Flags

None

### Input

- `reserve`: (Optional) A boolean indicating whether to enable (true) or disable (false) the reserve.
- `amount`: (Optional) The amount of KAON to set as the reserve.

### Output

If no parameters are provided, a JSON object with the current reserve balance status and amount is returned. When setting the reserve, there is typically no output upon success.

```json
{
  "reserve": true|false,  // Reserve status (enabled/disabled)
  "amount": x.xxxx         // Reserved amount
}

```

### Examples

CLI Usage:

```bash
reservebalance true 5000
```

```bash
reservebalance      // Returns current settings
```

JSON-RPC Call:

```json
{
  "method": "reservebalance",
  "params": [true, 5000] // Parameters are optional
}
```

### Error Handling

- If invalid parameters (e.g., a negative amount) are provided, an error is returned.

### Notes

The reserve balance allows you to exclude a certain amount of KAON from staking and masternode activities.

---

## searchlogs

Search logs. Note: requires -logevents to be enabled.

### Synopsis

```bash
searchlogs <fromblock> <toblock> <addressfilter> <topicfilter> <minconf>
```

### Description

Searches for log entries within a specified block range, filtered by address and topic. This command requires the `-logevents` option to be enabled. It allows you to retrieve specific events emitted by smart contracts during transaction execution.

### Arguments

| Name          | Type   | Required | Description                                                                   |
| ------------- | ------ | -------- | ----------------------------------------------------------------------------- |
| fromblock     | int    | Yes      | The starting block number.                                                    |
| toblock       | int    | Yes      | The ending block number.                                                      |
| addressfilter | object | Yes      | A JSON object specifying address filters (see Input section for details).     |
| topicfilter   | object | Yes      | A JSON object defining topic filters.                                         |
| minconf       | int    | Yes      | The minimum number of confirmations for a block to be included in the search. |

### Flags

None

### Input

- `fromblock`, `toblock`: Integers specifying the block range (inclusive) for the search. Use "latest" for the latest block.
- `addressfilter`: A JSON object with an "addresses" array: `{"addresses": ["address1", "address2", ... ]}`. Filters results to include logs only from the specified addresses.
- `topicfilter`: A JSON object with a "topics" array: `{"topics": ["topic1", "topic2", ... ]}`. Filters for log entries that match at least one of the specified topics. Use `null` to skip a topic in a specific position.
- `minconf`: The minimum number of confirmations before a log is included.

### Output

A JSON array of log entries. Each log entry object provides information like the block hash, block number, transaction details, contract address, topics, and data associated with the log entry. See original documentation for full structure of a log entry object.

### Examples

CLI Usage:

```bash
searchlogs 0 100 '{"addresses": ["12ae42729af478ca92c8c66773a3e32115717be4"]}' '{"topics": [null,"b436c2bf863ccd7b8f63171201efd4792066b4ce8e543dde9c3e9e9ab98e216c"]}'
```

JSON-RPC Call:

```json
{
  "method": "searchlogs",
  "params": [
    0,
    100,
    { "addresses": ["12ae42729af478ca92c8c66773a3e32115717be4"] },
    {
      "topics": [
        null,
        "b436c2bf863ccd7b8f63171201efd4792066b4ce8e543dde9c3e9e9ab98e216c"
      ]
    },
    0
  ]
}
```

### Error Handling

Returns an error if `-logevents` is not enabled, an invalid block range, invalid filter format or other errors occur related to log access.

### Notes

Used to query for specific contract events. Ensure `-logevents` is enabled. Related commands: [ `waitforlogs` ](#waitforlogs)

---

## sendfrom

DEPRECATED (use sendtoaddress). Send an amount from an account to a Kaon address.
The amount is a real and is rounded to the nearest 0.00000001.

### Synopsis

```bash
sendfrom "<fromaccount>" "<tokaonaddress>" <amount> (<minconf> "<comment>" "<comment-to>" <includeDelegated>)
```

### Description

**DEPRECATED.** Use `sendtoaddress` instead. Sends KAON from a specified account to a Kaon address. Account functionality is deprecated.

### Arguments

| Name             | Type    | Required | Description                                                            |
| ---------------- | ------- | -------- | ---------------------------------------------------------------------- |
| fromaccount      | string  | Yes      | The name of the account to send from (use "" for default account).     |
| tokaonaddress    | string  | Yes      | The destination Kaon address.                                          |
| amount           | numeric | Yes      | The amount to send in KAON.                                            |
| minconf          | numeric | No       | The minimum confirmations required for inputs (default: 1).            |
| comment          | string  | No       | A comment for the transaction (appears in your wallet only, optional). |
| comment-to       | string  | No       | A comment about the recipient (appears in your wallet only, optional). |
| includeDelegated | boolean | No       | Whether to include delegated UTXOs in coin selection (default: false). |

### Flags

None

### Input

- `fromaccount`: **DEPRECATED** - Source account name or "" for default account.
- `tokaonaddress`: The recipient's address.
- `amount`: The number of KAON to send.
- `minconf`: (Optional) Minimum confirmations for inputs. Defaults to 1.
- `comment`: (Optional) A transaction comment for your wallet.
- `comment-to`: (Optional) A comment about the recipient for your wallet.
- `includeDelegated`: (Optional) Boolean indicating whether to include delegated KAON. Defaults to false.

### Output

The transaction ID (txid).

### Examples

CLI Usage:

```bash
sendfrom "" "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" 0.01
```

JSON-RPC Call:

```json
{
  "method": "sendfrom",
  "params": [
    "",
    "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6",
    0.01,
    6,
    "donation",
    "sean's outpost",
    false
  ] //  minconf, comment, comment-to, and includeDelegated are optional
}
```

### Error Handling

- Returns an error if the input parameters are invalid, if the sending address does not exist or doesn't have enough balance, or if there are other issues preventing the transaction.

### Notes

Avoid using `sendfrom` . It is deprecated. Use `sendtoaddress` instead, which sends KAON directly between addresses and doesn't rely on the deprecated account system. Related commands: [ `sendtoaddress` ](#sendtoaddress), [ `sendmany` ](#sendmany)

---

## sendmany

Send multiple times. Amounts are double-precision floating point numbers.

### Synopsis

```bash
sendmany "<fromaccount>" "<amounts>" (<minconf> "<comment>" <includeDelegated>)

```

### Description

Sends multiple amounts to multiple Kaon addresses in a single transaction. The amounts are specified as a JSON object where keys are Kaon addresses, and values are the amounts to send.

### Arguments

| Name             | Type    | Required | Description                                                                                             |
| ---------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------- |
| fromaccount      | string  | Yes      | **DEPRECATED.** Source account (should be "" for default account).                                      |
| amounts          | string  | Yes      | A JSON object with destination addresses and amounts: `{"address1": amount1, "address2": amount2, ...}` |
| minconf          | numeric | No       | Minimum number of confirmations required for inputs (default: 1).                                       |
| comment          | string  | No       | An optional comment to store with the transaction (only stored locally in your wallet).                 |
| includeDelegated | boolean | No       | Whether to include outputs delegated for cold staking in coin selection (default: false).               |

### Flags

None

### Input

- `fromaccount`: **DEPRECATED**. Should be an empty string "".
- `amounts`: A JSON formatted string. Keys are Kaon addresses, values are amounts to be sent to each corresponding address. Example: `"\{\"address1\":0.1,\"address2\":0.2\}"`
- `minconf`: (Optional) Minimum number of confirmations required for inputs. Defaults to 1.
- `comment`: (Optional) A comment for the transaction, visible only in your wallet.
- `includeDelegated`: (Optional) Boolean indicating if delegated coins should be used. Defaults to false.

### Output

The transaction ID (txid) of the combined transaction.

### Examples

CLI Usage:

```bash
sendmany "" "{\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\":0.01,\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\":0.02}"
```

JSON-RPC Call:

```json
{
  "method": "sendmany",
  "params": [
    "",
    "{\"DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6\":0.01,\"DAD3Y6ivr8nPQLT1NEPX84DxGCw9jz9Jvg\":0.02}",
    6,
    "testing",
    false
  ] // fromaccount should be "", minconf, comment and includeDelegated are optional
}
```

### Error Handling

- If the fromaccount doesn't exist or has insufficient balance (after considering minconf), an error is returned.
- If any address in "amounts" is invalid, or if any amount is negative or too small, an error is returned.
- Other errors might arise due to issues creating or broadcasting the transaction.

### Notes

Use `sendmany` to send funds to multiple addresses in one transaction, saving transaction fees compared to sending multiple individual transactions. Avoid using accounts; set `fromaccount` to "" which represents the default account. Related commands: [ `sendtoaddress` ](#sendtoaddress), [ `sendfrom` ](#sendfrom) (deprecated), [ `createrawtransaction` ](#createrawtransaction)

---

## sendrawtransaction

Submits raw transaction (serialized, hex-encoded) to local node and network.
NOTE: This method most commonly used by Metamask, TrustWallet. Also see createrawtransaction and signrawtransaction calls.

### Synopsis

```bash
sendrawtransaction "<hexstring>" (<allowhighfees> <swiftx>)
```

### Description

Submits a raw transaction (provided in hexadecimal format) to the network. This command is often used by external wallets or applications (like Metamask or TrustWallet) that construct transactions outside of Kaon Core.

### Arguments

| Name          | Type    | Required | Description                                                                             |
| ------------- | ------- | -------- | --------------------------------------------------------------------------------------- |
| hexstring     | string  | Yes      | The raw transaction data as a hexadecimal string.                                       |
| allowhighfees | boolean | No       | Allow high transaction fees (default: false).                                           |
| swiftx        | boolean | No       | Use SwiftX for faster transaction processing (default: false). If Kaon supports SwiftX. |

### Flags

None

### Input

- `hexstring`: The raw transaction in hexadecimal format.
- `allowhighfees`: (Optional) Whether to allow high fees. Setting to true can bypass fee checks but might result in expensive transactions. Defaults to false.
- `swiftx`: (Optional) If Kaon has SwiftX, enables faster transaction sending. Defaults to false.

### Output

The transaction hash (txid) of the submitted transaction if successful.

### Examples

CLI Usage:

```bash
sendrawtransaction "signedhex"
```

JSON-RPC Call:

```json
{
  "method": "sendrawtransaction",
  "params": ["signedhex", false, false] // allowhighfees and swiftx are optional
}
```

### Error Handling

- If the `hexstring` is not a valid transaction, or if it fails validation, an error will be returned.
- An error will occur if the transaction cannot be broadcasted to the network.

### Notes

Make sure the raw transaction is properly signed and valid before submitting it using this command. Related commands: [ `createrawtransaction` ](#createrawtransaction), [ `signrawtransactionwithkey` ](#signrawtransactionwithkey), [ `decoderawtransaction` ](#decoderawtransaction)

---

## sendtoaddress

Send an amount to a given address. The amount is a real and is rounded to the nearest 0.00000001.

### Synopsis

```bash
sendtoaddress "<kaonaddress>" <amount> (<"comment"> <"comment-to">)
```

### Description

Sends a specified amount of KAON to a given Kaon address. You can include optional comments to describe the transaction (these comments are stored locally within your wallet and are not part of the transaction on the blockchain).

### Arguments

| Name        | Type    | Required | Description                                                              |
| ----------- | ------- | -------- | ------------------------------------------------------------------------ |
| kaonaddress | string  | Yes      | The Kaon address to send to.                                             |
| amount      | numeric | Yes      | The amount to send (in KAON).                                            |
| comment     | string  | No       | A comment for the transaction, stored locally in your wallet (optional). |
| comment-to  | string  | No       | A comment about the recipient, stored locally in your wallet (optional). |

### Flags

None

### Input

- `kaonaddress`: The receiving Kaon address.
- `amount`: The quantity of KAON to send.
- `comment`: (Optional) A comment to describe the transaction (visible in your wallet only).
- `comment-to`: (Optional) A comment about the recipient (visible only in your wallet).

### Output

The transaction ID (txid) of the sent transaction.

### Examples

CLI Usage:

```bash
sendtoaddress "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" 0.1
```

JSON-RPC Call:

```json
{
  "method": "sendtoaddress",
  "params": [
    "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6",
    0.1,
    "donation",
    "sean's outpost"
  ] // comment and comment-to are optional
}
```

### Error Handling

Returns an error if the given Kaon address is invalid, the amount is invalid, the wallet has insufficient funds, or another problem prevents sending the transaction.

### Notes

This is the standard command to send KAON to a specific address. Related commands: [ `sendfrom` ](#sendfrom) (deprecated), [ `sendmany` ](#sendmany)

---

## sendtoaddressix

Send an amount to a given address. The amount is a real and is rounded to the nearest 0.00000001.

### Synopsis

```bash
sendtoaddressix "<kaonaddress>" <amount> (<"comment"> <"comment-to">)
```

### Description

Sends a specified amount of KAON to a given address. This command functions similarly to `sendtoaddress` , but it might have slightly different behavior or added features depending on the specific implementation (the 'ix' suffix might refer to an internal or extended version of `sendtoaddress` ). You can also add optional comments to the transaction, just like with `sendtoaddress` .

### Arguments

| Name        | Type    | Required | Description                                                              |
| ----------- | ------- | -------- | ------------------------------------------------------------------------ |
| kaonaddress | string  | Yes      | The recipient's address.                                                 |
| amount      | numeric | Yes      | The amount to send in KAON.                                              |
| comment     | string  | No       | A comment to describe the transaction, stored in your wallet (optional). |
| comment-to  | string  | No       | A comment about the recipient, stored in your wallet (optional).         |

### Flags

None

### Input

- `kaonaddress`: The recipient's address.
- `amount`: The amount to send.
- `comment`: (Optional) A comment describing the transaction for your wallet.
- `comment-to`: (Optional) A comment about the recipient, also stored in your wallet.

### Output

The transaction ID of the transaction.

### Examples

CLI Usage:

```bash
sendtoaddressix "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" 0.1
```

JSON-RPC Call:

```json
{
  "method": "sendtoaddressix",
  "params": [
    "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6",
    0.1,
    "donation",
    "sean's outpost"
  ] // comment and comment-to are optional
}
```

### Error Handling

Returns an error if the address, amount, or comments are invalid, or if any other condition prevents sending the transaction.

### Notes

Refer to Kaon's documentation for the specific differences between `sendtoaddressix` and `sendtoaddress` . Related commands: [ `sendtoaddress` ](#sendtoaddress), [ `sendmany` ](#sendmany)

---

## sendtocontract

Send funds and data to a contract.

### Synopsis

```bash
sendtocontract "<contractaddress>" "<datahex>" (<amount> <gasLimit> <gasPrice> "<senderaddress>" <broadcast> <changeToSender>)
```

### Description

Sends funds and data to a specified smart contract. This command allows interacting with deployed smart contracts by calling its functions and transferring KAON.

### Arguments

| Name            | Type    | Required | Description                                                   |
| --------------- | ------- | -------- | ------------------------------------------------------------- |
| contractaddress | string  | Yes      | The contract's address.                                       |
| datahex         | string  | Yes      | The data to send (hex-encoded).                               |
| amount          | numeric | No       | The amount of KAON to send (default: 0).                      |
| gasLimit        | numeric | No       | Gas limit for execution (default: DEFAULT_GAS_LIMIT_OP_SEND). |
| gasPrice        | numeric | No       | Gas price in KAON (default: network gas price).               |
| senderaddress   | string  | No       | The sending address.                                          |
| broadcast       | boolean | No       | Whether to broadcast the transaction (default: true).         |
| changeToSender  | boolean | No       | Whether to send change back to the sender (default: true).    |

### Flags

None

### Input

- `contractaddress`: The target contract address (hexadecimal format).
- `datahex`: Data to be sent to the contract (hexadecimal encoded). This often represents the function signature and arguments to be called on the contract.
- `amount`: (Optional) The amount of KAON to send along with the data. Default is 0.
- `gasLimit`: (Optional) The gas limit for executing the contract code. Defaults to a predefined constant (DEFAULT_GAS_LIMIT_OP_SEND).
- `gasPrice`: (Optional) The price of gas in KAON. Defaults to the network's current gas price.
- `senderaddress`: (Optional) The KAON address sending the transaction. If omitted, the default sending address of the wallet will be used.
- `broadcast`: (Optional) A boolean indicating whether to broadcast the transaction to the network. Defaults to true. If set to false, the transaction is created but not broadcasted.
- `changeToSender`: (Optional) A boolean indicating whether to send any change back to the `senderaddress`. Defaults to true.

### Output

A JSON array with the transaction details.

```json
[
  {
    "txid": "transactionid", // The transaction ID
    "sender": "address", // The sender's KAON address
    "hash160": "hash" // ripemd-160 hash of the sender
  }
]
```

### Examples

CLI Usage:

```bash
sendtocontract "c6ca2697719d00446d4ea51f6fac8fd1e9310214" "54f6127f"
```

JSON-RPC Call:

```json
{
  "method": "sendtocontract",
  "params": [
    "c6ca2697719d00446d4ea51f6fac8fd1e9310214",
    "54f6127f",
    12.0015,
    6000000,
    0.6,
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
    true,
    true
  ] // amount, gasLimit, gasPrice, senderaddress, broadcast, changeToSender are optional
}
```

### Error Handling

- Returns an error if the contract address, data, or sender address is invalid. Returns an error if the wallet lacks sufficient funds, if the gas limit is too low, or if other problems occur with the transaction creation or broadcast.

### Notes

Interacting with smart contracts often requires specific data encoding. Ensure the `datahex` parameter is correctly formatted for the target contract function. Related commands: [ `callcontract` ](#callcontract), [ `createcontract` ](#createcontract), [ `listcontracts` ](#listcontracts)

---

## setaccount

DEPRECATED. Sets the account associated with the given address.

### Synopsis

```bash
setaccount "<kaonaddress>" "<account>"
```

### Description

**DEPRECATED.** Associates a Kaon address with a specific account. This functionality is deprecated and should not be used in new code. Accounts were previously used to organize addresses within a wallet, but this feature is generally no longer recommended.

### Arguments

| Name        | Type   | Required | Description                         |
| ----------- | ------ | -------- | ----------------------------------- |
| kaonaddress | string | Yes      | The Kaon address to assign.         |
| account     | string | Yes      | The account name to associate with. |

### Flags

None

### Input

- `kaonaddress`: The Kaon address.
- `account`: The account name to associate the address with.

### Output

None

### Examples

CLI Usage:

```bash
setaccount "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" "tabby"
```

JSON-RPC Call:

```json
{
  "method": "setaccount",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6", "tabby"]
}
```

### Error Handling

- An error will be returned if the given Kaon address or account name is invalid.

### Notes

This command is deprecated. Avoid using accounts when working with Kaon. Related commands: [ `getaccount` ](#getaccount), [ `getaccountaddress` ](#getaccountaddress), [ `listaccounts` ](#listaccounts) (deprecated)

---

## setban

Attempts add or remove a IP/Subnet from the banned list.

### Synopsis

```bash
setban "<ip(/netmask)>" "<add|remove>" (<bantime> <absolute>)
```

### Description

Adds or removes an IP address or subnet from the ban list. Banning an IP prevents it from connecting to your Kaon node.

### Arguments

| Name         | Type    | Required | Description                                                                                                                            |
| ------------ | ------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| ip(/netmask) | string  | Yes      | The IP address or subnet to ban or unban. A netmask (e.g., /24) can be used to ban a range of IPs. Default is /32 (single IP).         |
| command      | string  | Yes      | The action: "add" to ban or "remove" to unban.                                                                                         |
| bantime      | numeric | No       | The duration of the ban in seconds. If 0 or omitted, uses the default ban time (24 hours, configurable with `-bantime` startup arg).   |
| absolute     | boolean | No       | If true, `bantime` is an absolute timestamp (seconds since epoch); otherwise, `bantime` is a relative time from now. Default is false. |

### Flags

None

### Input

- `ip(/netmask)`: IP address or subnet. For example: "192.168.1.10" or "192.168.1.0/24".
- `command`: Either "add" (to ban) or "remove" (to unban).
- `bantime`: (Optional) Duration of the ban in seconds. If omitted or 0, the default ban time is used.
- `absolute`: (Optional) If true, interprets `bantime` as an absolute Unix timestamp. Defaults to false.

### Output

None

### Examples

CLI Usage:

```bash
setban "192.168.0.6" "add" 86400  // Ban a single IP for 24 hours
```

```bash
setban "192.168.0.0/24" "add"    // Ban a subnet
```

```bash
setban "192.168.0.6" "remove"    // Unban an IP
```

JSON-RPC Call:

```json
{
  "method": "setban",
  "params": ["192.168.0.6", "add", 86400, false] // bantime and absolute are optional
}
```

### Error Handling

If any of the provided parameters are invalid (e.g., incorrect IP format, unknown command), an error will be returned.

### Notes

Use this command to manage the list of banned IP addresses and subnets, enhancing the security of your node. Related commands: [ `listbanned` ](#listbanned), [ `clearbanned` ](#clearbanned)

---

## setgenerate

Set 'generate' true or false to turn generation on or off.
Generation is limited to 'genproclimit' processors, -1 is unlimited.
See the getgenerate call for the current setting.

### Synopsis

```bash
setgenerate <generate> (<genproclimit>)
```

### Description

Enables or disables block generation (mining) on the node and, optionally, sets a limit on processor usage for mining.

### Arguments

| Name         | Type    | Required | Description                                                                               |
| ------------ | ------- | -------- | ----------------------------------------------------------------------------------------- |
| generate     | boolean | Yes      | True to enable generation (mining), false to disable.                                     |
| genproclimit | numeric | No       | The number of processors to use for mining (optional). -1 means unlimited. Default is -1. |

### Flags

None

### Input

- `generate`: A boolean value – true turns on block generation, false turns it off.
- `genproclimit`: (Optional) Limits the number of processors dedicated to mining. -1 means no limit (use all available processors).

### Output

None

### Examples

CLI Usage:

```bash
setgenerate true 1      // Enable mining, use one processor
```

```bash
setgenerate false        // Disable mining
```

JSON-RPC Call:

```json
{
  "method": "setgenerate",
  "params": [true, 1] // genproclimit is optional and defaults to -1
}
```

### Error Handling

- If the `generate` parameter is not a valid boolean, an error is returned.
- If the `genproclimit` value is invalid (e.g. less than -1), an error will be returned.

### Notes

This command affects whether the node will attempt to mine new blocks. Resource usage can be controlled with the `genproclimit` parameter. Related commands: [ `getgenerate` ](#getgenerate), [ `gethashespersec` ](#gethashespersec)

---

## setstakesplitthreshold

This will set the output size of your stakes to never be below this number.

### Synopsis

```bash
setstakesplitthreshold <value>
```

### Description

Sets the threshold for stake splitting. If a staking output is larger than this threshold, it will be split into smaller outputs when staking. This helps optimize staking efficiency.

### Arguments

| Name  | Type    | Required | Description                                               |
| ----- | ------- | -------- | --------------------------------------------------------- |
| value | numeric | Yes      | The new stake splitting threshold (between 1 and 999999). |

### Flags

None

### Input

- `value`: An integer representing the new stake splitting threshold. Must be between 1 and 999999.

### Output

```json
{
  "threshold": n,        // New threshold
  "saved": true|false    // Whether the value was saved successfully
}
```

### Examples

CLI Usage:

```bash
setstakesplitthreshold 5000
```

JSON-RPC Call:

```json
{
  "method": "setstakesplitthreshold",
  "params": [5000]
}
```

### Error Handling

Returns an error if the provided value is outside the allowed range. May also return an error if there are problems saving the new threshold to the wallet.

### Notes

Adjusting the stake splitting threshold may improve staking efficiency. Related commands: [ `getstakesplitthreshold` ](#getstakesplitthreshold)

---

## settxfee

Set the transaction fee per kB.

### Synopsis

```bash
settxfee <amount>
```

### Description

Sets the transaction fee per kilobyte. This fee is included with transactions to incentivize miners to include them in blocks. Higher fees generally lead to faster confirmations.

### Arguments

| Name   | Type    | Required | Description                         |
| ------ | ------- | -------- | ----------------------------------- |
| amount | numeric | Yes      | The transaction fee per kB in KAON. |

### Flags

None

### Input

- `amount`: A numeric value representing the fee per kilobyte. It should be a positive number.

### Output

- `true` if successful.
- `false` otherwise.

### Examples

CLI Usage:

```bash
settxfee 0.00001
```

JSON-RPC Call:

```json
{
  "method": "settxfee",
  "params": [0.00001]
}
```

### Error Handling

- Returns an error if the specified fee is invalid (e.g., negative or too small).

### Notes

The transaction fee you set affects the confirmation time of transactions. Higher fees generally lead to faster confirmation. Related commands: [ `estimatefee` ](#estimatefee), [ `getfeeinfo` ](#getfeeinfo)

---

## signmessage

Sign a message with the private key of an address.

### Synopsis

```bash
signmessage "<kaonaddress>" "<message>"
```

### Description

Signs a message using the private key associated with a specific Kaon address. This proves that you control the private key for that address without revealing the key itself. The resulting signature can be verified using the `verifymessage` command.

### Arguments

| Name        | Type   | Required | Description                    |
| ----------- | ------ | -------- | ------------------------------ |
| kaonaddress | string | Yes      | The Kaon address to sign with. |
| message     | string | Yes      | The message to sign.           |

### Flags

None

### Input

- `kaonaddress`: The Kaon address for which the corresponding private key will be used to sign the message.
- `message`: The message string you wish to sign.

### Output

The signature of the message encoded in base64.

### Examples

CLI Usage:

```bash
signmessage "DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6" "my message"
```

JSON-RPC Call:

```json
{
  "method": "signmessage",
  "params": ["DMJRSsuU9zfyrvxVaAEFQqK4MxZg6vgeS6", "my message"]
}
```

### Error Handling

- Returns an error if the address is invalid, not found in the wallet, or if you don't possess the private key for that address.
- If the wallet is locked, you'll need to unlock it before signing using `walletpassphrase`.

### Notes

Ensure the wallet is unlocked using `walletpassphrase` before signing if it's encrypted. The base64 encoded signature can be shared and verified by others using `verifymessage` . Related commands: [ `verifymessage` ](#verifymessage), [ `walletpassphrase` ](#walletpassphrase)

---

## signrawtransactionwithkey

Sign inputs for raw transaction (serialized, hex-encoded).
The second optional argument (may be null) is an array of previous transaction outputs that
this transaction depends on but may not yet be in the block chain.
The third optional argument (may be null) is an array of base58-encoded private
keys that, if given, will be the only keys used to sign the transaction.
Requires wallet passphrase to be set with walletpassphrase call if wallet is encrypted.

### Synopsis

```bash
signrawtransactionwithkey "<hexstring>" (["prevtxs", ...] ["privatekeys", ...] <sighashtype>)
```

### Description

Signs a raw transaction using provided private keys. This allows signing transactions offline or using keys not managed by the wallet. The `hexstring` parameter is the raw transaction in hexadecimal format. You can optionally provide `prevtxs` (previous transaction outputs) and `privatekeys` .

### Arguments

| Name        | Type   | Required | Description                                                                                                                                         |
| ----------- | ------ | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| hexstring   | string | Yes      | The raw transaction in hexadecimal string format.                                                                                                   |
| prevtxs     | array  | No       | An array of previous transaction outputs used as inputs for the current transaction (optional).                                                     |
| privatekeys | array  | No       | An array of base58-encoded private keys used to sign the transaction (optional).                                                                    |
| sighashtype | string | No       | The signature hash type (optional, default is ALL). Allowed values: ALL, NONE, SINGLE, ALL\|ANYONECANPAY, NONE\|ANYONECANPAY, SINGLE\|ANYONECANPAY. |

### Flags

None

### Input

- `hexstring`: The raw transaction in hexadecimal format.
- `prevtxs`: (Optional) A JSON array of previous transaction outputs (`prevtxs`). Each `prevtx` object needs to contain information about the transaction (`txid`, `vout`, `scriptPubKey`, and optionally `redeemScript` if a P2SH script is used).
- `privatekeys`: (Optional) A JSON array of WIF-encoded private keys that will be used for signing the inputs. If specified, only these keys are used; otherwise, the wallet attempts to sign using keys it manages.
- `sighashtype`: (Optional) The signature hash type. See the argument description for valid values.

### Output

A JSON object representing the signed raw transaction, its completeness status, and any errors encountered during signing.

```json
{
  "hex": "value",             // Hex-encoded signed transaction
  "complete": true|false,     // Indicates if the transaction is fully signed
  "errors": [                 // Array of signing errors, if any
    {
      "txid": "hash",         // Txid of input with error
      "vout": n,              // Vout of input with error
      "scriptSig": "hex",     // Hex of the scriptSig
      "sequence": n,          // Input sequence number
      "error": "text"         // Error message
    },
    // ...
  ]
}
```

### Examples

CLI Usage:

```bash
signrawtransactionwithkey "myhex" "[{\"txid\":\"id\",\"vout\":0,\"scriptPubKey\":\"hex\",\"redeemScript\":\"hex\"}]" "[\"key1\",\"key2\"]" "ALL"
```

JSON-RPC Call:

```json
{
  "method": "signrawtransactionwithkey",
  "params": [
    "myhex",
    [{ "txid": "id", "vout": 0, "scriptPubKey": "hex", "redeemScript": "hex" }],
    ["key1", "key2"],
    "ALL"
  ] // prevtxs, privatekeys, and sighashtype are optional
}
```

### Error Handling

- Returns an error if the raw transaction is invalid, any of the private keys are invalid, or if any other issues occur during the signing process.
- If the wallet is encrypted, ensure that it's unlocked using walletpassphrase before using this command.

### Notes

Facilitates signing raw transactions offline, offering increased security and flexibility. Related commands: [ `createrawtransaction` ](#createrawtransaction), [ `decoderawtransaction` ](#decoderawtransaction), [ `sendrawtransaction` ](#sendrawtransaction)

---

## signrawtransactionwithwallet

Sign inputs for raw transaction (serialized, hex-encoded).
The second optional argument (may be null) is an array of previous transaction outputs that
this transaction depends on but may not yet be in the block chain.
Requires wallet passphrase to be set with walletpassphrase call if wallet is encrypted.

### Synopsis

```bash
signrawtransactionwithwallet "<hexstring>" ([{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex","witnessScript":"hex","amount":amount},...] <sighashtype> )
```

### Description

This command signs a raw transaction using keys stored in the wallet. This is useful for completing partially signed transactions or for signing transactions where you control the private keys through your wallet.

### Arguments

| Name        | Type   | Required | Description                                                                                                                                                 |
| ----------- | ------ | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hexstring   | string | Yes      | The hexadecimal representation of the raw transaction.                                                                                                      |
| prevtxs     | array  | No       | A JSON array of previous transaction outputs (UTXOs) that are being spent by this transaction. These are needed for SegWit transactions.                    |
| sighashtype | string | No       | The signature hash type (default: DEFAULT). Valid options are: DEFAULT, ALL, NONE, SINGLE, ALL\|ANYONECANPAY, NONE\|ANYONECANPAY, and SINGLE\|ANYONECANPAY. |

### Flags

None

### Input

- `hexstring`: The hexadecimal representation of the raw, unsigned transaction. Use `createrawtransaction` to create such an unsigned transaction.
- `prevtxs`: (Optional) A JSON array. Each element represents a UTXO being spent and _must_ contain `txid`, `vout`, `scriptPubKey`, `amount`. It can optionally contain `redeemScript` for P2SH or `witnessScript` for P2WSH transactions. Example `"[{\"txid\":\"id\",\"vout\":0,\"scriptPubKey\":\"hex\",\"redeemScript\":\"hex\",\"amount\":amount}]"`
- `sighashtype`: (Optional) The signature hash type to use. The default setting is usually fine. Other options allow different parts of the transaction to be signed.

### Output

A JSON object containing the signed transaction and related data.

```json
{
  "hex": "0x...",               // The signed raw transaction in hexadecimal format.
  "complete": true|false,        // Indicates whether all inputs are signed.
  "errors": [                    // An array of error objects, if any signing errors occurred.
    {
      "txid": "0x...",          // The transaction ID of the input that had a signing error.
      "vout": n,               // The output index (vout) of the input with the error.
      "scriptSig": "0x...",     // The scriptSig of the input with the error.
      "amount": n,              // The amount of the input.
      "error": "error message" // A descriptive error message.
    }
    // ...
  ]
}

```

### Examples

CLI Usage:

```bash
signrawtransactionwithwallet "myhex" "[{\"txid\":\"mytxid\",\"vout\":0,\"scriptPubKey\":\"hex\",\"redeemScript\":\"hex\",\"amount\":0.1}]" "ALL"
```

JSON-RPC Call:

```json
{
  "method": "signrawtransactionwithwallet",
  "params": [
    "myhex",
    [
      {
        "txid": "mytxid",
        "vout": 0,
        "scriptPubKey": "hex",
        "redeemScript": "hex",
        "amount": 0.1
      }
    ],
    "ALL"
  ] // prevtxs and sighashtype are optional. If prevtxs is provided, amount *must* be provided as well.
}
```

### Error Handling

- An error is returned if the `hexstring` is invalid, the wallet is locked (and the required passphrase is not provided), or if another error occurs during signing.
- If any of the optional parameters are malformed, an error will likely be returned as well.

### Notes

Used for signing transactions with the wallet's keys. Ensure the wallet is unlocked before using this command if it is encrypted. Related commands: [ `createrawtransaction` ](#createrawtransaction), [ `sendrawtransaction` ](#sendrawtransaction), [ `signrawtransactionwithkey` ](#signrawtransactionwithkey)

---

## spork

Return spork values or their active state.

### Synopsis

```bash
spork "<name>" (<value>)
```

### Description

Used to view or change "spork" values. Sporks are special configuration parameters that can be changed dynamically by authorized nodes on the Kaon network. They are used to activate or deactivate features without requiring a full network restart or upgrade. To change a spork value, you need to be a designated spork signer.

### Arguments

| Name  | Type    | Required    | Description                                                                                         |
| ----- | ------- | ----------- | --------------------------------------------------------------------------------------------------- |
| name  | string  | Yes         | The spork's name, or "show" to display all spork values, or "active" to display their active state. |
| value | numeric | Conditional | The new value for the spork (required when setting a spork's value). Must be numeric.               |

### Flags

None

### Input

- `name`: Spork name (or "show"/"active").
- `value`: (Optional) New numeric value for the spork if changing its setting. Required when changing a spork.

### Output

- If name is "show": Returns a JSON object where keys are spork names, and values are their numeric values.
- If name is "active": Returns a JSON object where keys are spork names, and values are booleans indicating if the spork is active.
- If setting a spork value: Returns a status message ("success" or "failure").

### Examples

CLI Usage:

```bash
spork show
```

```bash
spork active
```

```bash
spork SPORK_2_INSTANTSEND_ENABLED 1
```

JSON-RPC Call:

```json
{
  "method": "spork",
  "params": ["show"] // Or ["active"] or ["spork_name", spork_value]
}
```

### Error Handling

- Returns an error if an invalid spork name is provided.
- Returns an error if you try to set a spork value without the necessary authorization.

### Notes

Sporks are configuration parameters that can be adjusted dynamically. Only authorized nodes (spork signers) can change their values. The "show" and "active" options provide insights into the current spork settings.

---

## startmasternode

Attempts to start one or more masternode(s).

### Synopsis

```bash
startmasternode "<local|all|many|missing|disabled|alias>" (<lockwallet> "<alias"> <reload_conf>)
```

### Description

Starts one or more masternodes. Masternodes contribute to network consensus and receive rewards.

### Arguments

| Name        | Type    | Required    | Description                                                                                                                                                                       |
| ----------- | ------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| set         | string  | Yes         | Specifies which masternodes to start: "local" (local masternode), "all", "many", "missing", "disabled", or "alias" (specific masternode by alias).                                |
| lockwallet  | boolean | Conditional | If true, the wallet will be locked after the command completes. This adds a layer of security when starting masternodes remotely. Default might vary depending on the 'set' used. |
| alias       | string  | Conditional | The alias of the masternode to start (required only when set is "alias").                                                                                                         |
| reload_conf | boolean | Conditional | Whether to reload the `masternode.conf` configuration file (optional, default is probably false). Only used when set is "alias".                                                  |

### Flags

None

### Input

- `set`: The set of masternodes to start ("local", "all", "many", "missing", "disabled", "alias").
- `lockwallet`: (Mostly optional, defaults vary) Lock wallet after completion. Recommendation: Lock your wallet.
- `alias`: (Required for "alias" set) The alias of the masternode.
- `reload_conf`: (Optional, defaults to false, applies only to "alias") Whether to reload the config file after starting.

### Output

- If `set` is "local": Returns a string indicating the masternode status.
- If any other `set` is used: Returns a JSON object with an overall status message and detailed information about each masternode.

### Examples

CLI Usage:

```bash
startmasternode "alias" true "my_mn"
```

JSON-RPC Call:

```json
{
  "method": "startmasternode",
  "params": ["alias", true, "my_mn", false] // lockwallet, alias, and reload_conf are optional or conditional based on 'set'. Consider providing 'lockwallet' as true.
}
```

### Error Handling

Returns an error if the command or parameters are invalid, or if a problem arises during the masternode startup process. The error message typically provides specific details about the issue.

### Notes

This command attempts to start masternodes according to the specified settings. Make sure that your masternodes are correctly configured in the `masternode.conf` file. Related commands: [ `createmasternodekey` ](#createmasternodekey), [ `createmasternodebroadcast` ](#createmasternodebroadcast), [ `listmasternodes` ](#listmasternodes), [ `getmasternodestatus` ](#getmasternodestatus)

---

## stop

Stop Kaon server.

### Synopsis

```bash
stop
```

### Description

Stops the Kaon daemon. This shuts down the Kaon Core node and any associated processes.

### Arguments

None

### Flags

None

### Input

None

### Output

None (The daemon shuts down).

### Examples

CLI Usage:

```bash
stop
```

JSON-RPC Call:

```json
{
  "method": "stop",
  "params": []
}
```

### Error Handling

This command is unlikely to return errors, but errors might occur if the daemon cannot be stopped gracefully or due to unexpected system issues.

### Notes

This command terminates the Kaon daemon. You will need to restart the daemon using the `kaond` command to run the Kaon Core node again.

---

## submitblock

Attempts to submit new block to network.
The 'jsonparametersobject' parameter is currently ignored.
See https://en.bitcoin.it/wiki/BIP_0022 for full specification.

### Synopsis

```bash
submitblock "<hexdata>" (<"jsonparametersobject">)
```

### Description

Submits a newly mined block to the Kaon network. The block is submitted in its raw hexadecimal format. The optional `jsonparametersobject` parameter is currently ignored by the Kaon Core node.

### Arguments

| Name                 | Type   | Required | Description                                                       |
| -------------------- | ------ | -------- | ----------------------------------------------------------------- |
| hexdata              | string | Yes      | The block data encoded as a hexadecimal string.                   |
| jsonparametersobject | string | No       | A JSON object containing optional parameters (currently ignored). |

### Flags

None

### Input

- `hexdata`: A hexadecimal string representing the raw block data.
- `jsonparametersobject`: (Optional) A JSON string containing potentially additional parameters for block submission, but currently ignored by the Kaon implementation. See the BIP_0022 specification for details on possible future use.

### Output

The result of the submission attempt. If successful, the output might indicate acceptance of the block or provide a relevant message. If unsuccessful, an error message is returned, which often contains specific details about the reason for rejection.

### Examples

CLI Usage:

```bash
submitblock "mydata"
```

JSON-RPC Call:

```json
{
  "method": "submitblock",
  "params": ["mydata", "{\"workid\":\"myworkid\"}"] // jsonparametersobject is optional
}
```

### Error Handling

Returns an error if the submitted block data is invalid, does not meet consensus rules, or if another error occurs during the submission process. Consult the error message for specifics about the rejection reason.

### Notes

This command is crucial for miners to submit newly mined blocks to the network. Ensure that the submitted block adheres to Kaon's consensus rules. Related commands: [ `getblocktemplate` ](#getblocktemplate)

---

## submitbudget

Submit proposal to the network.

### Synopsis

```bash
submitbudget "<proposal-name>" "<url>" <payment-count> <block-start> "<kaon-address>" <monthly-payment> "<fee-tx>"
```

### Description

Submits a budget proposal to the Kaon network. This follows the `preparebudget` command, which prepares the proposal. The proposal needs to be prepared first, which generates the necessary transaction that this command submits.

### Arguments

| Name            | Type    | Required | Description                                                               |
| --------------- | ------- | -------- | ------------------------------------------------------------------------- |
| proposal-name   | string  | Yes      | The proposal name (20 characters max).                                    |
| url             | string  | Yes      | URL of the proposal details (64 characters max).                          |
| payment-count   | numeric | Yes      | Number of payment months.                                                 |
| block-start     | numeric | Yes      | Starting superblock height.                                               |
| kaon-address    | string  | Yes      | Payment receiving Kaon address.                                           |
| monthly-payment | numeric | Yes      | Monthly payment amount in KAON.                                           |
| fee-tx          | string  | Yes      | The transaction hash of the proposal fee (generated by `preparebudget` ). |

### Flags

None

### Input

- `proposal-name`: The name of your proposal. Must not exceed 20 characters.
- `url`: The URL containing your detailed proposal information. Limited to 64 characters.
- `payment-count`: The total number of monthly payments you are requesting for your proposal.
- `block-start`: The superblock height at which funding should start if the proposal is approved.
- `kaon-address`: Your Kaon address to receive the payments.
- `monthly-payment`: The amount you are requesting per month in KAON.
- `fee-tx`: The transaction hash representing the paid proposal fee. Obtain this using the `preparebudget` command.

### Output

The proposal hash if successful, or an error message if submission fails.

### Examples

CLI Usage:

```bash
submitbudget "test-proposal" "https://forum.kaon.one/t/test-proposal" 2 820800 "D9oc6C3dttUbv8zd7zGNq1qKBGf4ZQ1XEE" 500 "d0398d7e40b9598def1a23e64feb112821c0b3e6042dd5f8a281da2cfef2ca1"
```

JSON-RPC Call:

```json
{
  "method": "submitbudget",
  "params": [
    "test-proposal",
    "https://forum.kaon.one/t/test-proposal",
    2,
    820800,
    "D9oc6C3dttUbv8zd7zGNq1qKBGf4ZQ1XEE",
    500,
    "d0398d7e40b9598def1a23e64feb112821c0b3e6042dd5f8a281da2cfef2ca1"
  ]
}
```

### Error Handling

- An error is returned if any of the input parameters are invalid or if there are any issues during the submission process (e.g., network errors or proposal rejection by the network).

### Notes

Submitting the proposal initiates the voting process by masternodes. Related commands: [ `preparebudget` ](#preparebudget), [ `mnbudgetvote` ](#mnbudgetvote), [ `getbudgetinfo` ](#getbudgetinfo)

---

## urc20allowance

Returns remaining tokens allowed to spend for an address.

### Synopsis

```bash
urc20allowance "<contractaddress>" "<addressfrom>" "<addressto>"
```

### Description

Returns the amount of URC20 tokens that a specific address ( `addressfrom` ) is allowed to spend on behalf of another address ( `addressto` ). This is part of the URC20 token standard, which allows for delegated token spending.

### Arguments

| Name            | Type   | Required | Description                                 |
| --------------- | ------ | -------- | ------------------------------------------- |
| contractaddress | string | Yes      | The address of the URC20 token contract.    |
| addressfrom     | string | Yes      | The address of the owner of the tokens.     |
| addressto       | string | Yes      | The address authorized to spend the tokens. |

### Flags

None

### Input

- `contractaddress`: The address (hexadecimal format) of the URC20 token contract.
- `addressfrom`: The address of the token owner who granted the allowance.
- `addressto`: The address that is allowed to spend the tokens.

### Output

A JSON object containing the remaining allowance.

```json
{
  "allowance": "xxxxx" // Remaining allowance
}
```

### Examples

CLI Usage:

```bash
urc20allowance "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX" "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd"
```

JSON-RPC Call:

```json
{
  "method": "urc20allowance",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX",
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd"
  ]
}
```

### Error Handling

If any address is invalid, or the contract address is not a URC20 contract, an error message will be returned.

### Notes

URC20 is a standard for tokens on the Kaon blockchain, just like ERC20 is for Ethereum. Related commands: [ `urc20approve` ](#urc20approve), [ `urc20transferfrom` ](#urc20transferfrom)

---

## urc20approve

Owner approves an address to spend some amount of tokens.

### Synopsis

```bash
urc20approve "<contractaddress>" "<owneraddress>" "<spenderaddress>" "<amount>" (<gaslimit> <gasprice> <checkoutputs>)
```

### Description

Allows a URC20 token owner to authorize another address (the "spender") to spend a specified amount of tokens on their behalf. This command interacts with the `approve` function of a URC20 token contract.

### Arguments

| Name            | Type    | Required | Description                                                                                                                                                                                                       |
| --------------- | ------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| contractaddress | string  | Yes      | The address of the URC20 token contract.                                                                                                                                                                          |
| owneraddress    | string  | Yes      | The KAON address of the token owner who is granting the approval.                                                                                                                                                 |
| spenderaddress  | string  | Yes      | The KAON address of the spender who is being authorized to spend tokens.                                                                                                                                          |
| amount          | string  | Yes      | The amount of tokens to approve.                                                                                                                                                                                  |
| gaslimit        | numeric | No       | The gas limit for the transaction (default: 21000, max: 9500000000000).                                                                                                                                           |
| gasprice        | numeric | No       | The gas price per unit (default: 0.20, min: 0.60). For KAON.                                                                                                                                                      |
| checkoutputs    | boolean | No       | Whether to check outputs before sending (default: probably false). Performing checks adds extra security, making sure the transaction sends the amount intended. But requires external resources to be available. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract.
- `owneraddress`: The address of the account that owns the tokens and is giving approval.
- `spenderaddress`: The address that's being granted permission to spend tokens.
- `amount`: The number of tokens to approve for spending by the `spenderaddress`.
- `gaslimit`: (Optional) The maximum amount of gas to use for this transaction.
- `gasprice`: (Optional) The gas price per unit in KAON. Affects transaction costs.
- `checkoutputs`: (Optional) If enabled, performs checks before sending the transaction. Consult Kaon documentation for specifics on these checks and potential requirements.

### Output

A JSON object indicating either the PSBT or the transaction ID.

```json
{
  "psbt": "xxxx", // Base64-encoded unsigned PSBT (if private keys are disabled)
  "txid": "0x" // Transaction ID (if private keys are enabled)
}
```

### Examples

CLI Usage:

```bash
urc20approve "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX" "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" 0.1
```

JSON-RPC Call:

```json
{
  "method": "urc20approve",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX",
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
    0.1,
    21000,
    0.6,
    true
  ] // gaslimit, gasprice, and checkoutputs are optional
}
```

### Error Handling

If any address is invalid, if the specified amount exceeds the owner's balance, or any other problems occur during transaction creation, an error message will be returned.

### Notes

This command allows delegated spending of URC20 tokens. The `spenderaddress` can then use the `urc20transferfrom` command to spend tokens up to the approved amount. Related commands: [ `urc20allowance` ](#urc20allowance), [ `urc20transferfrom` ](#urc20transferfrom)

---

## urc20balanceof

Returns the token balance for address.

### Synopsis

```bash
urc20balanceof "<contractaddress>" "<address>"
```

### Description

Returns the balance of URC20 tokens held by a specified address. This command queries the token contract for the token balance.

### Arguments

| Name            | Type   | Required | Description                                            |
| --------------- | ------ | -------- | ------------------------------------------------------ |
| contractaddress | string | Yes      | The address of the URC20 token contract (hexadecimal). |
| address         | string | Yes      | The KAON address to check the balance of.              |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract.
- `address`: The Kaon address for which you want to check the token balance.

### Output

A JSON object containing the token balance.

```json
{
  "balance": "xxxxx" // The token balance
}
```

### Examples

CLI Usage:

```bash
urc20balanceof "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX"
```

JSON-RPC Call:

```json
{
  "method": "urc20balanceof",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX"
  ]
}
```

### Error Handling

If the `address` or `contractaddress` are invalid, an error will be returned. If the specified contract address doesn't correspond to a URC20 contract, this command may return an error or unexpected results.

### Notes

This is a standard function of the URC20 token interface. Use this command to check URC20 token balances.

---

## urc20burn

Send token amount from selected address to a given address.

### Synopsis

```bash
urc20burn "<contractaddress>" "<owneraddress>" "<amount>" (<gaslimit> <gasprice> <checkoutputs>)
```

### Description

Burns (destroys) a specified amount of URC20 tokens held by a specific address. Burning tokens permanently removes them from circulation.

### Arguments

| Name            | Type    | Required | Description                                                                                                                                                                                         |
| --------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| contractaddress | string  | Yes      | The address of the URC20 token contract (hexadecimal).                                                                                                                                              |
| owneraddress    | string  | Yes      | The Kaon address that owns the tokens to be burned.                                                                                                                                                 |
| amount          | string  | Yes      | The amount of tokens to burn.                                                                                                                                                                       |
| gaslimit        | numeric | No       | The gas limit for the transaction (default: 21000, max: 9500000000000).                                                                                                                             |
| gasprice        | numeric | No       | The gas price per unit (default: 0.20, min: 0.60).                                                                                                                                                  |
| checkoutputs    | boolean | No       | Whether to check the transaction outputs before sending. Performing checks before executing the actual transaction enhances security but might require additional system resources to be available. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 contract.
- `owneraddress`: The address holding the tokens to be burned.
- `amount`: The amount of tokens to be burned.
- `gaslimit`: (Optional) The gas limit for the transaction. Defaults to 21000, with a maximum of 9500000000000.
- `gasprice`: (Optional) Gas price per unit. Defaults to 0.20.
- `checkoutputs`: (Optional) If true, checks transaction outputs before execution. Enhances security, but might need extra resources.

### Output

A JSON object indicating the transaction status:

```json
{
  "psbt": "xxxx", // Base64-encoded unsigned PSBT (if private keys are disabled)
  "txid": "0x" // Transaction ID (if private keys are enabled)
}
```

### Examples

CLI Usage:

```bash
urc20burn "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" 0.1
```

JSON-RPC Call:

```json
{
  "method": "urc20burn",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
    0.1,
    21000,
    0.6,
    true
  ] // gaslimit, gasprice, and checkoutputs are optional
}
```

### Error Handling

Returns an error if any of the addresses or the amount are invalid, if the owner's balance is insufficient, or if there's an issue with transaction creation or execution.

### Notes

Burning tokens reduces their total supply. The `checkoutputs` parameter enhances security by verifying outputs before transaction broadcast, but might demand extra resources. Relatd commands: [ `urc20burnfrom` ](#urc20burnfrom), [ `urc20totalsupply` ](#urc20totalsupply)

---

## urc20burnfrom

Send token amount from selected address to a given address.

### Synopsis

```bash
urc20burnfrom "<contractaddress>" "<owneraddress>" "<spenderaddress>"  "<amount>" (<gaslimit> <gasprice> <checkoutputs>)

```

### Description

Burns a specified quantity of URC20 tokens from an `owneraddress` on behalf of a designated `spenderaddress` . The `spender` must have prior approval via `urc20approve` to burn tokens on the owner's behalf. This command effectively destroys the specified amount of tokens, removing them from circulation.

### Arguments

| Name            | Type    | Required | Description                                                                                                                                                                                         |
| --------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| contractaddress | string  | Yes      | The address of the URC20 token contract (hexadecimal).                                                                                                                                              |
| owneraddress    | string  | Yes      | The Kaon address of the owner of the tokens.                                                                                                                                                        |
| spenderaddress  | string  | Yes      | The Kaon address allowed to burn the tokens (must have prior approval).                                                                                                                             |
| amount          | string  | Yes      | The amount of tokens to burn.                                                                                                                                                                       |
| gaslimit        | numeric | No       | The gas limit for the transaction (default: 21000, max: 9500000000000).                                                                                                                             |
| gasprice        | numeric | No       | The gas price per unit (default: 0.20, min: 0.60).                                                                                                                                                  |
| checkoutputs    | boolean | No       | Whether to check the transaction outputs before sending. Performing checks before executing the actual transaction enhances security but might require additional system resources to be available. |

### Flags

None

### Input

- `contractaddress`: The URC20 token contract address.
- `owneraddress`: The address owning the tokens.
- `spenderaddress`: The address authorized to burn the tokens (needs prior approval from `owneraddress` via `urc20approve`).
- `amount`: The quantity of tokens to burn.
- `gaslimit`: (Optional) Transaction gas limit. Defaults to 21000.
- `gasprice`: (Optional) Gas price in KAON. Defaults to 0.20.
- `checkoutputs`: (Optional) Whether to pre-check outputs before sending. Improves security, might require extra resources. Defaults to probably false.

### Output

```json
{
  "psbt": "xxxx", // Base64-encoded unsigned PSBT (if private keys are disabled)
  "txid": "0x" // Transaction ID (if private keys are enabled)
}
```

### Examples

CLI Usage:

```bash
urc20burnfrom "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX" "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" 0.1
```

JSON-RPC Call:

```json
{
  "method": "urc20burnfrom",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX",
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
    0.1,
    21000,
    0.6,
    true
  ] // gaslimit, gasprice, and checkoutputs are optional
}
```

### Error Handling

- Returns an error if any address is invalid, the contract is not URC20, the spender lacks sufficient allowance, the owner's balance is too low, or other transaction-related errors occur.

### Notes

Burning tokens permanently removes them, decreasing the total supply. `checkoutputs` adds security but might increase resource demands. `spenderaddress` needs prior approval through `urc20approve` . Related commands: [ `urc20burn` ](#urc20burn), [ `urc20approve` ](#urc20approve), [ `urc20allowance` ](#urc20allowance)

---

## urc20decimals

Returns the number of decimals of the token.

### Synopsis

```bash
urc20decimals "<contractaddress>"
```

### Description

Returns the number of decimal places used by a given URC20 token. This parameter is essential for interpreting the token's value correctly.

### Arguments

| Name            | Type   | Required | Description                              |
| --------------- | ------ | -------- | ---------------------------------------- |
| contractaddress | string | Yes      | The address of the URC20 token contract. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract in hexadecimal format.

### Output

```json
{
  "totalSupply": n      // The number of decimal places
}
```

### Examples

CLI Usage:

```bash
urc20decimals "eb23c0b3e6042821da281a2e2364feb22dd543e3"
```

JSON-RPC Call:

```json
{
  "method": "urc20decimals",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3"]
}
```

### Error Handling

If the provided contract address doesn't correspond to a valid URC20 token contract, or if there's an issue accessing contract data, an error message is returned.

### Notes

Understanding the number of decimals is crucial for proper interpretation of token amounts.

---

## urc20listtransactions

Returns transactions history for a specific address.

### Synopsis

```bash
urc20listtransactions "<contractaddress>" "<address>" (<fromblock> <minconf>)
```

### Description

Returns a list of URC20 token transactions associated with a specified address. This provides a transaction history for URC20 tokens.

### Arguments

| Name            | Type    | Required | Description                                                          |
| --------------- | ------- | -------- | -------------------------------------------------------------------- |
| contractaddress | string  | Yes      | The address of the URC20 token contract (hexadecimal).               |
| address         | string  | Yes      | The Kaon address for which you want transaction history.             |
| fromblock       | numeric | No       | The earliest block to include in the search (default is 0).          |
| minconf         | numeric | No       | Minimum number of confirmations for the transactions (default is 0). |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract.
- `address`: The Kaon address to retrieve transactions for.
- `fromblock`: (Optional) The starting block height for retrieving transactions. Default is 0 (from the genesis block).
- `minconf`: (Optional) The minimum number of confirmations required for transactions to be included. Default is 0, which also includes unconfirmed transactions.

### Output

A JSON array of URC20 token transaction objects. Each object includes:

```json
[
 {
    "receiver": "address",      // Receiver's Kaon address
    "sender": "address",        // Sender's address
    "amount": "amount",        // Amount transferred
    "confirmations": n,          // Number of confirmations
    "blockHash": "hash",        // Block hash
    "blockNumber": n,            // Block number
    "blocktime": timestamp,     // Block timestamp
    "transactionHash": "hash"   // Transaction hash
  },
  // ... more transactions
]
```

### Examples

CLI Usage:

```bash
urc20listtransactions "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX"
```

JSON-RPC Call:

```json
{
  "method": "urc20listtransactions",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX",
    0,
    6
  ] // fromblock and minconf are optional
}
```

### Error Handling

Returns an error if any address is invalid, the specified contract does not follow the URC20 standard, or another problem arises during data retrieval.

### Notes

Lists transactions based on the provided address and contract, filtered by `fromblock` and `minconf` . Related commands: [ `urc20transfer` ](#urc20transfer), [ `urc20transferfrom` ](#urc20transferfrom)

---

## urc20name

Returns the name of the token.

### Synopsis

```bash
urc20name "<contractaddress>"
```

### Description

Returns the name of a URC20 token, as defined in the token contract.

### Arguments

| Name            | Type   | Required | Description                              |
| --------------- | ------ | -------- | ---------------------------------------- |
| contractaddress | string | Yes      | The address of the URC20 token contract. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 contract in hexadecimal format.

### Output

```json
{
  "name": "tokenname" // The name of the token
}
```

### Examples

CLI Usage:

```bash
urc20name "eb23c0b3e6042821da281a2e2364feb22dd543e3"
```

JSON-RPC Call:

```json
{
  "method": "urc20name",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3"]
}
```

### Error Handling

Returns an error if the provided contract address is invalid or doesn't adhere to the URC20 token standard.

### Notes

This command queries the token contract directly to obtain its name. Part of the URC20 token standard. Related command: [ `urc20symbol` ](#urc20symbol)

---

## urc20symbol

Returns the symbol of the token.

### Synopsis

```bash
urc20symbol "<contractaddress>"
```

### Description

Returns the symbol of a URC20 token (e.g., "KAON", "USDT", etc.), which is defined within the token's smart contract. The symbol is a short, usually alphanumeric, representation of the token.

### Arguments

| Name            | Type   | Required | Description                          |
| --------------- | ------ | -------- | ------------------------------------ |
| contractaddress | string | Yes      | Address of the URC20 token contract. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract (hexadecimal format).

### Output

A JSON object with the token symbol.

```json
{
  "symbol": "xxxxx" // The token's symbol
}
```

### Examples

CLI Usage:

```bash
urc20symbol "eb23c0b3e6042821da281a2e2364feb22dd543e3"
```

JSON-RPC Call:

```json
{
  "method": "urc20symbol",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3"]
}
```

### Error Handling

Returns an error if the input contract address is invalid or if it does not represent a valid URC20 token contract.

### Notes

Provides the token symbol by querying the specified contract. Conforms to the URC20 token standard. Related command: [ `urc20name` ](#urc20name)

---

## urc20totalsupply

Returns the total supply of the token.

### Synopsis

```bash
urc20totalsupply "<contractaddress>"
```

### Description

Returns the total supply of a URC20 token. This command queries the specified token contract to get the total number of tokens that have been issued.

### Arguments

| Name            | Type   | Required | Description                          |
| --------------- | ------ | -------- | ------------------------------------ |
| contractaddress | string | Yes      | Address of the URC20 token contract. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract, in hexadecimal format.

### Output

```json
{
  "totalSupply": "xxxxx" // Total token supply
}
```

### Examples

CLI Usage:

```bash
urc20totalsupply "eb23c0b3e6042821da281a2e2364feb22dd543e3"
```

JSON-RPC Call:

```json
{
  "method": "urc20totalsupply",
  "params": ["eb23c0b3e6042821da281a2e2364feb22dd543e3"]
}
```

### Error Handling

Returns an error if the provided contract address is not a valid URC20 contract or if there are problems accessing data from the contract.

### Notes

This is part of the URC20 token standard. Provides the total token supply from the specified contract.

---

## urc20transfer

Send token amount to a given address.

### Synopsis

```bash
urc20transfer "<contractaddress>" "<senderaddress>" "<toaddress>" "<amount>" (<gaslimit> <gasprice> <checkoutputs>)
```

### Description

Transfers a specified amount of URC20 tokens to another address. This command interacts with a URC20 token contract to execute the token transfer.

### Arguments

| Name            | Type    | Required | Description                                                                                                                                                                                         |
| --------------- | ------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| contractaddress | string  | Yes      | The address of the URC20 token contract.                                                                                                                                                            |
| senderaddress   | string  | Yes      | The address sending the tokens.                                                                                                                                                                     |
| toaddress       | string  | Yes      | The address to receive the tokens.                                                                                                                                                                  |
| amount          | string  | Yes      | Amount of tokens to send.                                                                                                                                                                           |
| gaslimit        | numeric | No       | The gas limit for the transaction (default: 21000, max: 9500000000000).                                                                                                                             |
| gasprice        | numeric | No       | The gas price per unit (default: 0.20, min: 0.60).                                                                                                                                                  |
| checkoutputs    | boolean | No       | Whether to check the transaction outputs before sending. Performing checks before executing the actual transaction enhances security but might require additional system resources to be available. |

### Flags

None

### Input

- `contractaddress`: The address of the URC20 token contract (hexadecimal).
- `senderaddress`: The sending Kaon address. Must hold a balance of the specified URC20 token.
- `toaddress`: The receiving Kaon address.
- `amount`: The amount of URC20 tokens to send.
- `gaslimit`: (Optional) Gas limit for the transaction (defaults to 21000).
- `gasprice`: (Optional) Gas price per unit.
- `checkoutputs`: (Optional) Perform extra checks on transaction outputs before sending.

### Output

```json
{
  "psbt": "xxxx", // Base64-encoded unsigned PSBT (if private keys are disabled)
  "txid": "0x" // Transaction ID (if private keys are enabled)
}
```

### Examples

CLI Usage:

```bash
urc20transfer "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX" "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" 0.1
```

JSON-RPC Call:

```json
{
  "method": "urc20transfer",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX",
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
    0.1,
    21000,
    0.6,
    true
  ] // gaslimit, gasprice, checkoutputs are optional
}
```

### Error Handling

- Returns an error if addresses are invalid, if the contract is not a valid URC20 contract, if the sender doesn't have enough tokens, or if other problems occur during transaction processing.

### Notes

Standard URC20 token transfer function. `checkoutputs` improves security but needs more resources. Related commands: [ `urc20transferfrom` ](#urc20transferfrom), [ `urc20approve` ](#urc20approve), [ `urc20balanceof` ](#urc20balanceof)

---

## urc20transferfrom

Send token amount from selected address to a given address.

### Synopsis

```bash
urc20transferfrom "<contractaddress>" "<fromaddress>" "<senderaddress>" "<toaddress>" "<amount>" (<gaslimit> <gasprice> <checkoutputs>)
```

### Description

Transfers URC20 tokens from one address to another, using a delegated allowance. This command interacts with the `transferFrom` function of a URC20 token contract. The `senderaddress` must have been previously approved to spend tokens from the `fromaddress` using `urc20approve` .

### Arguments

| Name            | Type    | Required | Description                                                                                                                                                               |
| --------------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| contractaddress | string  | Yes      | The address of the URC20 token contract (hexadecimal).                                                                                                                    |
| fromaddress     | string  | Yes      | The address that holds the tokens to be transferred.                                                                                                                      |
| senderaddress   | string  | Yes      | The address authorized to transfer tokens from `fromaddress` (must have a sufficient allowance).                                                                          |
| toaddress       | string  | Yes      | The recipient's address.                                                                                                                                                  |
| amount          | string  | Yes      | The amount of tokens to transfer.                                                                                                                                         |
| gaslimit        | numeric | No       | The gas limit for the transaction (default: 21000, max: 9500000000000).                                                                                                   |
| gasprice        | numeric | No       | The gas price per unit (default: 0.20, min: 0.60).                                                                                                                        |
| checkoutputs    | boolean | No       | Whether to check outputs before sending. This enhances security but may require extra resources to be available, as it might involve accessing external services or APIs. |

### Flags

None

### Input

- `contractaddress`: The URC20 token contract's address.
- `fromaddress`: The address from which the tokens will be taken.
- `senderaddress`: The address authorized to spend tokens from the `fromaddress` (must have allowance).
- `toaddress`: The recipient address.
- `amount`: The token amount to transfer.
- `gaslimit`: (Optional) Transaction gas limit. Defaults to 21000.
- `gasprice`: (Optional) Gas price per unit, affecting transaction costs.
- `checkoutputs`: (Optional) Enables output checks for extra security before sending, but might require more resources. Defaults to probably false.

### Output

```json
{
  "psbt": "xxxx", // Base64-encoded unsigned PSBT (if private keys are disabled)
  "txid": "0x" // Transaction ID (if private keys are enabled)
}
```

### Examples

CLI Usage:

```bash
urc20transferfrom "eb23c0b3e6042821da281a2e2364feb22dd543e3" "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX" "QhZThdumK8EFRX8MziWzvjCdiQWRt7Mxdz" 0.1
```

JSON-RPC Call:

```json
{
  "method": "urc20transferfrom",
  "params": [
    "eb23c0b3e6042821da281a2e2364feb22dd543e3",
    "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd",
    "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX",
    "QhZThdumK8EFRX8MziWzvjCdiQWRt7Mxdz",
    0.1,
    21000,
    0.6,
    true
  ] // gaslimit, gasprice, and checkoutputs are optional
}
```

### Error Handling

- Returns an error if any address is invalid, the contract is not URC20, the sender lacks sufficient allowance, the `fromaddress` has insufficient balance, or if any other issues occur during transaction processing.

### Notes

Allows transferring URC20 tokens on another address's behalf, if prior approval ( `urc20approve` ) was given. `checkoutputs` increases security but may require more system resources. Related commands: [ `urc20transfer` ](#urc20transfer), [ `urc20approve` ](#urc20approve), [ `urc20allowance` ](#urc20allowance)

---

## validateaddress

Return information about the given Kaon address.

### Synopsis

```bash
validateaddress "<kaonaddress>"
```

### Description

Returns detailed information about a specified Kaon address, including its validity, associated scriptPubKey, whether it belongs to the wallet, if it's a staking address, and other relevant properties.

### Arguments

| Name        | Type   | Required | Description       |
| ----------- | ------ | -------- | ----------------- |
| kaonaddress | string | Yes      | The Kaon address. |

### Flags

None

### Input

- `kaonaddress`: The Kaon address to validate.

### Output

A JSON object with details about the address:

```json
{
  "isvalid": true|false,       // Validity of the address
  "address": "kaonaddress",   // Kaon address
  "scriptPubKey": "hex",     // Corresponding scriptPubKey
  "ismine": true|false,        // Whether the address belongs to the wallet
  "isstaking": true|false,     // Whether it's a staking address
  "iswatchonly": true|false,   // Whether it's a watch-only address
  "isscript": true|false,      // Whether it's a script address
  "hex": "hex",              // Redeemscript (for P2SH)
  "pubkey": "publickeyhex",  // Public key (if available)
  "iscompressed": true|false,  // Whether the public key is compressed
  "account": "account"       // DEPRECATED. Associated account
}

```

If the address is invalid, only the `isvalid` field is returned with a value of `false` .

### Examples

CLI Usage:

```bash
validateaddress "1PSSGeFHDnKNxiEyFrD1wcEaHr9hrQDDWc"
```

JSON-RPC Call:

```json
{
  "method": "validateaddress",
  "params": ["1PSSGeFHDnKNxiEyFrD1wcEaHr9hrQDDWc"]
}
```

### Error Handling

If an invalid address is provided, the only returned field will be `isvalid: false` .

### Notes

Useful for checking if an address is valid, belongs to the wallet, and if it's suitable for staking. Related commands: [ `getnewaddress` ](#getnewaddress), [ `importaddress` ](#importaddress)

---

## verifychain

Verifies blockchain database.

### Synopsis

```bash
verifychain (<checklevel> <numblocks>)
```

### Description

Performs consistency checks on the blockchain database. This command helps ensure data integrity and can be used to detect potential corruption issues. It verifies the chain by checking block headers, and prev/next block pointers. The optional `checklevel` parameter controls the depth of the verification process. Higher `checklevel` values mean more thorough checks. The `numblocks` parameter indicates how many blocks to verify, starting from the highest block. 0 means all blocks. Default is 288.

### Arguments

| Name       | Type    | Required | Description                                                                                                  |
| ---------- | ------- | -------- | ------------------------------------------------------------------------------------------------------------ |
| checklevel | numeric | No       | How thorough the block verification is (0-4, default: 3).                                                    |
| numblocks  | numeric | No       | The number of blocks to check, starting from the highest block (default: 288, set to 0 to check all blocks). |

### Flags

None

### Input

- `checklevel`: (Optional) The level of detail for the chain verification. 0 is the least thorough (fastest), and 4 is the most thorough (slowest). Defaults to 3.
- `numblocks`: (Optional) How many blocks to check, starting from the chain tip. Set to 0 to check all blocks in the chain. Defaults to 288.

### Output

- `true`: If the blockchain passes verification.
- `false`: If verification fails (indicating potential corruption).

### Examples

CLI Usage:

```bash
verifychain
```

JSON-RPC Call:

```json
{
  "method": "verifychain",
  "params": [4, 0] // checklevel and numblocks are optional
}
```

### Error Handling

This command may return errors related to problems encountered during verification.

### Notes

This command may take some time, particularly when verifying a large number of blocks. Regularly verifying the blockchain database can help maintain its integrity and detect issues early.

---

## verifymessage

Verify a signed message

### Synopsis

```bash
verifymessage "<kaonaddress>" "<signature>" "<message>"
```

### Description

Verifies a signed message. This command checks if a message was signed by the holder of the private key corresponding to the provided Kaon address. This is useful for confirming the authenticity of signed messages without revealing the private key.

### Arguments

| Name        | Type   | Required | Description                                            |
| ----------- | ------ | -------- | ------------------------------------------------------ |
| kaonaddress | string | Yes      | The Kaon address that allegedly signed the message.    |
| signature   | string | Yes      | The base64 encoded signature generated by signmessage. |
| message     | string | Yes      | The original message that was supposedly signed.       |

### Flags

None

### Input

- `kaonaddress`: The Kaon address that's claimed to have signed the message.
- `signature`: The base64 encoded message signature.
- `message`: The original message content.

### Output

- `true`: If the signature is valid for the given message and address.
- `false`: If the signature is not valid.

### Examples

CLI Usage:

```bash
verifymessage "1D1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XZ" "signature" "my message"
```

JSON-RPC Call:

```json
{
  "method": "verifymessage",
  "params": ["1D1ZrZNe3JUo7ZycKEYQQiQAWd9y54F4XZ", "signature", "my message"]
}
```

### Error Handling

Returns `false` if the signature is not valid or if there are any errors during the verification process.

### Notes

This command is useful for independently verifying the authenticity and integrity of signed messages. Related commands: [ `signmessage` ](#signmessage)

---

## waitforlogs

Waits for a new logs and return matching log entries. When the call returns, it also specifies the next block number to start waiting for new logs.
By calling waitforlogs repeatedly using the returned `nextBlock` number, a client can receive a stream of up-to-date log entires.
This call is different from the similarly named `searchlogs` . This call returns individual matching log entries, `searchlogs` returns a transaction receipt if one of the log entries of that transaction matches the filter conditions. Requires -logevents to be enabled.

### Synopsis

```bash
waitforlogs (<fromblock> <toblock> <filter> <minconf>)
```

### Description

Waits for new log entries that match the given filter criteria and returns them. This command is especially helpful for applications that need real-time updates about specific contract events. It differs from `searchlogs` by providing a continuous stream of individual log entries rather than just a transaction receipt. Requires the `-logevents` configuration option to be enabled.

### Arguments

| Name      | Type   | Required | Description                                                                                           |
| --------- | ------ | -------- | ----------------------------------------------------------------------------------------------------- |
| fromblock | int    | No       | The starting block number. Defaults to the latest block.                                              |
| toblock   | int    | No       | The ending block number. Defaults to -1 (the latest block).                                           |
| filter    | object | No       | A JSON object with filters for addresses and topics (see `searchlogs` and Input section for details). |
| minconf   | int    | No       | Minimum confirmations for blocks to be included. Defaults to 0 (includes unconfirmed logs).           |

### Flags

None

### Input

- `fromblock`: (Optional) Start block for log search. Defaults to "latest".
- `toblock`: (Optional) End block for the search. Defaults to -1 (latest block).
- `filter`: (Optional) A JSON object to filter results. Structure: `{"addresses": ["address1", ...], "topics": ["topic1", ... ]}`. Filters can be combined.
- `minconf`: (Optional) Minimum confirmations before a log is returned. Default is 0, which includes logs from unconfirmed transactions as well.

### Output

```json
{
  "entries": [                   // Array of matching log entries
    {
      "blockHash": "0x",           // Block hash
      "blockNumber": n,           // Block number
      "transactionHash": "0x",     // Transaction hash
      "transactionIndex": n,       // Transaction index within block
      "from": "address",          // Sender address
      "to": "address",            // Receiver address
      "cumulativeGasUsed": n,      // Total gas used
      "gasUsed": n,                // Gas used by this log entry
      "contractAddress": "0x",     // Contract address
      "excepted": "reason",       // Exception reason, if any
      "topics": ["0x", ...],      // Array of log topics
      "data": "0x"                 // Log data
    },
    // ...
  ],
  "count": n,                     // Number of log entries returned
  "nextBlock": n                  // Next block to start searching from
}
```

### Examples

CLI Usage:

```bash
waitforlogs  // Waits for new logs starting from the latest block
```

```bash
waitforlogs null null '{ "addresses": [ "12ae42729af478ca92c8c66773a3e32115717be4" ], "topics": [ "b436c2bf863ccd7b8f63171201efd4792066b4ce8e543dde9c3e9e9ab98e216c"] }' 0   // Waits for new logs matching filter and minconf
```

JSON-RPC Call:

```json
{
  "method": "waitforlogs",
  "params": [
    null,
    null,
    {
      "addresses": ["12ae42729af478ca92c8c66773a3e32115717be4"],
      "topics": [
        "b436c2bf863ccd7b8f63171201efd4792066b4ce8e543dde9c3e9e9ab98e216c"
      ]
    },
    0
  ] // fromblock, toblock, filter, minconf are optional
}
```

### Error Handling

Returns an error if `-logevents` is not enabled, the filter format is invalid, or other errors relating to log access occur.

### Notes

This command is useful for streaming log entries in real-time. The `nextBlock` field is crucial for retrieving subsequent logs without duplicates. Related commands: [ `searchlogs` ](#searchlogs)
