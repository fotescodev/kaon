# Network and Tools

### EVM Smart Contracts <a href="#evm-smart-contracts" id="evm-smart-contracts"></a>

Kaon's node EVM offers comprehensive support without zero-knowledge (ZK) functionalities, adhering to adjusted gas price policies. The gas limit is managed by validator consensus, ensuring fair and efficient network operation.

### Bitcoin Sigscript <a href="#bitcoin-sigscript" id="bitcoin-sigscript"></a>

Kaon is part of the Bitcoin family, fully supporting Bitcoin sigscript for its native transactions. It incorporates a few additional OP\_CODEs for internal use while strictly following the Bitcoin GitHub repository. Kaon does not include unconfirmed OP\_CODEs from pull requests and discussions, with rare exceptions for essential updates.

### BitVM Support <a href="#bitvm-support" id="bitvm-support"></a>

Kaon supports [Tree++](https://bitvm.org/treeplusplus.html) and will continue to follow its updates. This ensures that Kaon remains compatible with advanced scripting capabilities and the evolving BitVM ecosystem.

### Bitcoin Rust Support <a href="#bitcoin-rust-support" id="bitcoin-rust-support"></a>

Kaon has forked and adapted the bitcoin-rust library, making core parameters current and functional. The adapted version is shared for community use and further development.

### Bitcoin RPC / Ordinals API <a href="#bitcoin-rpc-ordinals-api" id="bitcoin-rpc-ordinals-api"></a>

Kaon provides `kaon-cli` and `kaond` RPC interfaces based on the Bitcoin 1.26 RPC standard, with additional methods to support enhanced functionalities. A demonstration API for Ordinals and BRC20 is also available, showcasing the integration and usage of these features within the Kaon network.

### Ethereum Gate API <a href="#ethereum-gate-api" id="ethereum-gate-api"></a>

Users have the capability to interact with the Ethereum network via the Ethereum Gate API, facilitating seamless cross-chain communication and asset management.

\
Technical Architecture

* System design and component interaction. Include an architectural diagram here.
* Diagrams illustrating key processes (mBTC locking, withdrawals, etc.).
* Consensus mechanisms (dPoS, BFT, BFT).

#### Development Tools

* KAON CLI and SDKs.
* Testing framework and deployment process.

#### Network Parameters

Chain configuration and security parameters.
