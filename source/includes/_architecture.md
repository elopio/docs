Architecture
============
<aside class="notice">The Architecture section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur is designed to work with a locally-running Ethereum node; however, if one is not available, Augur can be configured to use a hosted Ethereum node instead. A hosted Ethereum node is one that is hosted on a public server by the Augur development team. The sections below explain a bit about each setup and provide a diagram of what's going on under the hood.

Local Ethereum Node
----------
Running a local Ethereum node, such as [Geth](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum), while using Augur provides the best performance and overall experience. This is because Augur sends RPC requests to a hosted Ethereum node takes more time than sending them to a local node. To use a local Ethereum node with Augur, either download and run the Augur app locally, or simply visit [https://app.augur.net](https://app.augur.net), which is configured to automatically talk to a local node, if present.

<img src="images/architecture_local.svg" onerror="this.src='images/architecture_local.png'">

<aside class="notice">To use sendTransaction RPC commands (or any other command that requires Ether or is actually sent to the Ethereum network) the Ethereum node will need to unlocked. The easiest way to do this using Geth is to start it using the <code>--unlock</code> option:

<code class="block">geth --unlock 0 --testnet --rpc --ws --rpccorsdomain "http://localhost:8080" --wsorigins "http://localhost:8080"</code>

The <code>--ws</code> flag allows WebSocket connections, which are recommended for speed.  The <code>--rpccorsdomain</code> and <code>--wsorigins</code> flags specify what domains are allowed to send RPC requests to your Geth node. Here <code>http://localhost:8080</code> is whitelisted; this is the default address where the <a href="https://github.com/AugurProject/augur">Augur client</a> is served, if you build Augur locally. To use your local Ethereum node with a remote (hosted) Augur client, you will need to set <code>--rpccorsdomain</code> and/or <code>--wsorigins</code> to your host's address; for example, if the remote host is at <code>http://app.augur.net</code>, then you would use <code>http://app.augur.net</code> for <code>--rpccorsdomain</code> and <code>--wsorigins</code>.</aside>

**Note to Geth users:** Since Geth's RPC server uses regular (unencrypted) HTTP, the Augur app must also use regular HTTP to communicate with a local Geth node.

Hosted Ethereum Node
-----------
Since many users may not wish to go to the trouble of running a full Ethereum node solely to use Augur, the Augur development team maintains a public Ethereum node on the Rinkeby test network (while in beta) at `rinkeby.ethereum.nodes.augur.net`, which is used automatically by [https://app.augur.net](https://app.augur.net) (and by [local.augur.net](http://local.augur.net) if a local Ethereum node is not detected).

<img src="images/architecture_hosted.svg" onerror="this.src='images/architecture_hosted.png'">
