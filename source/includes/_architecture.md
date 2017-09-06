Architecture
============
<aside class="notice">The Architecture section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur is designed to work with a locally running Ethereum Node. If a local Ethereum Node isn't available then Augur will default to using one of the hosted Augur Nodes. A hosted Augur Node is simply an Ethereum Node being run by the Augur Team and hosted on a public server. The sections below explain a bit about each setup (local or hosted) and provide a diagram of what is going on under the hood.

Local node
----------
If you are running a [local Ethereum node](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum), sending RPC requests to your local node is the best (fastest) way to use Augur. To do this, you can either download and run the Augur app locally, or simply visit [https://app.augur.net](https://app.augur.net), which is configured to automatically talk to your local node, if present.

<img src="images/architecture_local.svg" onerror="this.src='images/architecture_local.png'">

<aside class="notice">To use sendTransaction/sendRawTransaction RPC commands (i.e., anything that requires Ether / is actually sent to the network) you will need to unlock your Ethereum node. If you are running geth, the easiest way to do this is to start it with the <code>--unlock</code> option:

<code class="block">geth --unlock 0 --testnet --rpc --ws --rpccorsdomain "http://localhost:8080" --wsorigins "http://localhost:8080"</code>

The <code>--ws</code> flag allows websocket connections, which are recommended for speed.  The <code>--rpccorsdomain</code> and <code>--wsorigins</code> flags specify what domains are allowed to send RPC requests to your geth node. Here <code>http://localhost:8080</code> is whitelisted; this is the default address where the <a href="https://github.com/AugurProject/augur">Augur client</a> is served, if you build Augur locally. To use your local Ethereum node with a remote (hosted) Augur client, you will need to set <code>--rpccorsdomain</code> and/or <code>--wsorigins</code> to your host's address; for example, if the remote host is at <code>http://app.augur.net</code>, then you would use <code>http://app.augur.net</code> for <code>--rpccorsdomain</code> and <code>--wsorigins</code>.</aside>

**Note to geth users:** Geth's RPC server uses regular (unencrypted) HTTP and because of this, in order for the Augur app to communicate with your local geth node, it must be served over HTTP.

Hosted node
-----------
Understandably, many users will not want to go to the trouble of running a full Ethereum node solely for the purpose of using Augur. Augur maintains a public (testnet during the beta) Ethereum node at [https://eth9000.augur.net](https://eth9000.augur.net), which is used automatically by [https://app.augur.net](https://app.augur.net) (and by [local.augur.net](http://local.augur.net) if a local Ethereum node is not detected).

<img src="images/architecture_hosted.svg" onerror="this.src='images/architecture_hosted.png'">
