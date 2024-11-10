# Hello KAON!

Let's start with creating and deploying your first token on Kaon!

## Deploy an ERC20 Token

* Visit [tokengen.testnet.kaon.one](https://tokengen.testnet.kaon.one)
* Connect your MetaMask wallet (ensure you have some KAON tokens for gas)
* Configure your token parameters:
  * Token Name
  * Token Symbol
  * Initial Supply
  * Decimal Places
  * Additional Parameters (optional)
* Deploy your token
* Wait for deployment confirmation

## Verify your token deployment

Add the token to MetaMask using the contract address

* Send a small amount to another address
* View the transaction in the block explorer
* Verify the token contract details and total supply

## Create a Uniswap Pair (Optional)&#x20;

Explore DeFi functionality by creating a new trading pair on Kaon's version of Uniswap.

{% stepper %}
{% step %}
**Get WETH**

Send KAON tokens to the WETH contract: `0x11e465D214d00A37A11C67796b7fC4DD15dbCCB9` \
\
Exchange rate: 1 KAON = 1 WETH \
\
The contract will automatically return WETH to your address
{% endstep %}

{% step %}
**Create Liquidity**

* Go to uniswap.testnet.kaon.one/swap&#x20;
* Select "Pool" and "Create a pair"&#x20;
* Choose WETH and your new token Set initial liquidity amounts&#x20;
* Approve token usage and confirm pool creation
{% endstep %}

{% step %}
**Start Trading**

* Return to the swap interface. Your new trading pair should now be available
* Test swaps in both directions
{% endstep %}
{% endstepper %}

