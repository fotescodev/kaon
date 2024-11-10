# Restore Mirror BTC From Wrap Process

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

1. The user initiates the process by sending the ERC20 tokens (which represent the wrapped Mirrored BTC) to a designated smart contract on the Kaon chain.
2. The smart contract recognizes the transaction and identifies the corresponding voucher created during the initial wrapping process. This voucher contains the necessary details to facilitate the restoration.
3. The voucher undergoes a validation process to ensure its authenticity and to confirm that it matches the ERC20 tokens being unwrapped.
4. Upon successful validation, the voucher is destroyed. This step is crucial to prevent any double-spending or reuse of the voucher.
5. The details from the destroyed voucher are used to trigger the creation of Mirrored BTC tokens back on the Kaon network.
6. The Kaon network mints new Mirrored BTC tokens equivalent to the amount specified in the destroyed voucher. This step ensures that the user regains their original token value in the form of Mirrored BTC.
7. The newly minted Mirrored BTC tokens are sent to the user’s wallet within the Kaon network, completing the restoration process.

It is possible to do the reverse without using Voucher and using only tokens. But this way Kaon pool will assign Bitcoins randomly and only from those who are received from unrecognized sources.
