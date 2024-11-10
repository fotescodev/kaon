# Mirrored BTC Transfer To EVM Chain

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

1. The transfer process is initiated by the user.
2. Upon recognition of the transfer, the Mirrored BTC is moved to the network’s storage. This storage is part of the cross-chain pool that manages the creation of new vouchers for cross-chain transactions.
3. The cross-chain pool storage creates new vouchers to represent the Mirrored BTC tokens.
4. The special BRC/ERC Mirrored BTC tokens are transformed into simple ERC20 tokens because other chains do not support the Bitcoin UTXO paradigm. Maintaining the UTXO model on these chains is impossible.
5. New ERC20 tokens are issued on the target EVM chain, representing the transformed Mirrored BTC. These tokens adhere to the ERC20 standard, ensuring compatibility with the EVM chain’s infrastructure.

> Although the UTXO model is not supported on EVM chains, the transformation process can be reversed using Voucher produced by this transformation.

