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

### Core Functionality

#### Blockchain Interaction

**Querying Block Information**

The `getblock` command retrieves detailed block data:

```bash
kaon-cli getblock <blockhash> [verbosity]
```

**Parameters:**

* `blockhash`: Block identifier (hex string)
* `verbosity`: Detail level (0=hex, 1=decoded, 2=decoded with tx data)

**Example - Retrieving Block Details:**

```bash
# Get full block information
BLOCK_HASH="00000000000fd08c2fb661d2fcb0d49abb3a91e5f27082ce64feed3b4dede2e2"
kaon-cli getblock $BLOCK_HASH 2

# Get only block header info
kaon-cli getblock $BLOCK_HASH 1
```

**Response Structure:**

```json
{
  "hash": "blockhash",
  "confirmations": n,
  "size": n,
  "height": n,
  "version": n,
  "merkleroot": "hash",
  "tx": [
    {
      "txid": "hash",
      "version": n,
      "time": n,
      "vout": []
    }
  ],
  "time": timestamp,
  "mediantime": timestamp,
  "nonce": n,
  "bits": "hex",
  "difficulty": n
}
```

### Smart Contract Operations

**Contract Deployment**

The `createcontract` command deploys EVM-compatible smart contracts:

```bash
kaon-cli createcontract <bytecode> [gaslimit] [gasprice] [sender] [broadcast] [changetosender]
```

**Key Parameters:**

* `bytecode`: Contract bytecode (hex string)
* `gaslimit`: Maximum gas units (default: 2500000)
* `gasprice`: Price per gas unit in KAON (minimum: 0.0000004)

**Example - ERC-20 Token Deployment:**

```bash
#!/bin/bash

# Contract deployment with verification
deploy_token_contract() {
    local bytecode=$1
    local sender=$2
    local gas_limit=2500000
    local gas_price=0.0000004

    echo "Validating deployment parameters..."
    
    # Verify sender balance
    local balance=$(kaon-cli getbalance)
    if (( $(echo "$balance < 1" | bc -l) )); then
        echo "Error: Insufficient balance for deployment"
        return 1
    }

    echo "Deploying contract..."
    local result=$(kaon-cli createcontract "$bytecode" \
        $gas_limit \
        $gas_price \
        "$sender" \
        true \
        true)

    # Extract contract address
    local contract_address=$(echo "$result" | jq -r '.address')
    
    echo "Verifying deployment..."
    sleep 30
    
    # Verify contract exists
    local code=$(kaon-cli getaccountinfo "$contract_address")
    if [[ -z "$code" ]]; then
        echo "Error: Contract deployment failed"
        return 1
    }

    echo "Contract deployed successfully at: $contract_address"
    return 0
}
```

**Contract Interaction**

The `callcontract` command enables interaction with deployed contracts:

```bash
kaon-cli callcontract <address> <data> [sender] [gaslimit]
```

**Example - Token Contract Interaction:**

```bash
#!/bin/bash

# Contract interaction utilities
get_token_info() {
    local contract=$1
    
    # Function signatures
    local NAME="06fdde03"        # name()
    local SYMBOL="95d89b41"      # symbol()
    local DECIMALS="313ce567"    # decimals()
    local TOTAL_SUPPLY="18160ddd"  # totalSupply()
    
    # Get basic token information
    local name=$(kaon-cli callcontract "$contract" "$NAME" | jq -r '.executionResult.output')
    local symbol=$(kaon-cli callcontract "$contract" "$SYMBOL" | jq -r '.executionResult.output')
    local decimals=$(kaon-cli callcontract "$contract" "$DECIMALS" | jq -r '.executionResult.output')
    local supply=$(kaon-cli callcontract "$contract" "$TOTAL_SUPPLY" | jq -r '.executionResult.output')

    # Convert hex outputs to readable format
    name=$(echo "$name" | xxd -r -p | tr -d '\0')
    symbol=$(echo "$symbol" | xxd -r -p | tr -d '\0')
    decimals=$((16#${decimals:64}))
    supply=$((16#${supply:64}))

    cat << EOF
Token Information:
-----------------
Name: $name
Symbol: $symbol
Decimals: $decimals
Total Supply: $supply
EOF
}
```

### Transaction Management

**Creating and Sending Transactions**

The `sendtoaddress` command facilitates KAON transfers with built-in confirmation monitoring:

```bash
#!/bin/bash

send_with_confirmation() {
    local address=$1
    local amount=$2
    local required_confs=${3:-6}
    
    # Validate address
    local valid=$(kaon-cli validateaddress "$address" | jq -r '.isvalid')
    if [[ "$valid" != "true" ]]; then
        echo "Error: Invalid address"
        return 1
    }
    
    # Check balance
    local balance=$(kaon-cli getbalance)
    if (( $(echo "$balance < $amount" | bc -l) )); then
        echo "Error: Insufficient balance"
        return 1
    }
    
    echo "Sending $amount KAON to $address..."
    
    # Send transaction
    local txid=$(kaon-cli sendtoaddress "$address" "$amount")
    if [[ $? -ne 0 ]]; then
        echo "Error: Transaction failed"
        return 1
    }
    
    echo "Transaction sent: $txid"
    echo "Waiting for confirmations..."
    
    # Monitor confirmations
    while true; do
        local confs=$(kaon-cli gettransaction "$txid" | jq -r '.confirmations')
        if [[ $confs -ge $required_confs ]]; then
            echo "Transaction confirmed with $confs confirmations"
            break
        fi
        echo "Current confirmations: $confs/$required_confs"
        sleep 30
    done
    
    return 0
}
```

## Advanced Topics

### Error Handling

Implement robust error handling in your scripts:

```bash
#!/bin/bash

# General error handler
handle_error() {
    local exit_code=$1
    local error_msg=$2
    
    echo "Error ($exit_code): $error_msg" >&2
    logger -t "kaon-cli" "Error: $error_msg"
    
    return $exit_code
}

# RPC error checker
check_rpc_error() {
    local result=$1
    
    if echo "$result" | jq -e '.error' >/dev/null; then
        local error_msg=$(echo "$result" | jq -r '.error.message')
        handle_error 1 "RPC Error: $error_msg"
        return 1
    }
    
    return 0
}
```

#### Batch Operations

For multiple operations, use batch processing:

```bash
#!/bin/bash

# Batch transaction processor
process_transactions() {
    local addresses=("$@")
    local results=()
    
    for address in "${addresses[@]}"; do
        echo "Processing transaction to $address..."
        
        local result=$(kaon-cli sendtoaddress "$address" 1.0)
        if [[ $? -eq 0 ]]; then
            results+=("Success: $result")
        else
            results+=("Failed: $address")
        fi
    done
    
    # Report results
    printf '%s\n' "${results[@]}"
}
```



### Leasing Commands

**leasetoaddress**

Lease an amount to an address for mining.

```bash
kaon-cli leasetoaddress "leasingaddress" amount [timestamp] ["owneraddress"] [fExternalOwner] [fForceNotEnabled]
```

**Arguments:**

* `leasingaddress` (string, required) - Target leasing address
* `amount` (numeric, required) - Amount to lease
* `timestamp` (numeric, optional) - Lock time for leased UTXO
* `owneraddress` (string, optional) - Address for reward spending
* `fExternalOwner` (boolean, optional) - Use external owner address

**Example:**

```bash
kaon-cli leasetoaddress "S1t2a3kab9c8c71VA78xxxy4MxZg6vgeS6" 1000 1649328238
```

#### listleasingaddresses

List all leasing addresses for the wallet.

```bash
kaon-cli listleasingaddresses
```

#### listleasingutxos

List unspent leasing outputs.

```bash
kaon-cli listleasingutxos [nonWhitelistedOnly]
```

### URC20 Token Commands

#### Token Information Commands

**urc20name**

Get token name.

```bash
kaon-cli urc20name "contractaddress"
```

**urc20symbol**

Get token symbol.

```bash
kaon-cli urc20symbol "contractaddress"
```

**urc20totalsupply**

Get total token supply.

```bash
kaon-cli urc20totalsupply "contractaddress"
```

**urc20decimals**

Get token decimals.

```bash
kaon-cli urc20decimals "contractaddress"
```

#### Token Transaction Commands

**urc20transfer**

Transfer tokens.

```bash
kaon-cli urc20transfer "contractaddress" "owneraddress" "spenderaddress" "amount" [gaslimit] [gasprice] [checkoutputs]
```

**Arguments:**

* `contractaddress` (string, required) - Token contract address
* `owneraddress` (string, required) - Token owner's address
* `spenderaddress` (string, required) - Recipient's address
* `amount` (string, required) - Amount to transfer
* `gaslimit` (numeric, optional) - Default: 21000
* `gasprice` (numeric, optional) - Default: 0.20
* `checkoutputs` (boolean, optional) - Verify outputs

**Example:**

```bash
kaon-cli urc20transfer "eb23c0b3e6042821da281a2e2364feb22dd543e3" \
                       "QX1GkJdye9WoUnrE2v6ZQhQ72EUVDtGXQX" \
                       "QM72Sfpbz1BPpXFHz9m3CdqATR44Jvaydd" \
                       0.1 21000 0.60 true
```

**urc20approve**

Approve token spending.

```bash
kaon-cli urc20approve "contractaddress" "owneraddress" "spenderaddress" "amount" [gaslimit] [gasprice] [checkoutputs]
```

**urc20transferfrom**

Transfer tokens from another address.

```bash
kaon-cli urc20transferfrom "contractaddress" "owneraddress" "spenderaddress" "receiveraddress" "amount" [gaslimit] [gasprice] [checkoutputs]
```

#### Token Query Commands

**urc20balanceof**

Get token balance.

```bash
kaon-cli urc20balanceof "contractaddress" "address"
```

**urc20allowance**

Check spending allowance.

```bash
kaon-cli urc20allowance "contractaddress" "owneraddress" "spenderaddress"
```

**urc20listtransactions**

Get transaction history.

```bash
kaon-cli urc20listtransactions "contractaddress" "address" [fromblock] [minconf]
```

### Event Logging Commands

#### waitforlogs

Wait for and retrieve matching log entries.

```bash
kaon-cli waitforlogs fromblock toblock addressfilter topicfilter minconf
```

**Arguments:**

* `fromblock` (int) - Starting block number
* `toblock` (int) - Ending block number
* `addressfilter` (object) - Contract address filters
* `topicfilter` (object) - Log topic filters
* `minconf` (int) - Minimum confirmations

**Example:**

```bash
kaon-cli waitforlogs 600 700 \
  '{"addresses": ["12ae42729af478ca92c8c66773a3e32115717be4"]}' \
  '{"topics": ["b436c2bf863ccd7b8f63171201efd4792066b4ce8e543dde9c3e9e9ab98e216c"]}'
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
