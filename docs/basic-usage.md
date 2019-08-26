# Basic Usage

## Running a node

To get started, you must download the binary file from [this link](https://github.com/codechain-io/codechain/releases).

In order to run CodeChain, run

```
codechain
```

You can create a block by sending a transaction through [JSON-RPC](https://github.com/CodeChain-io/codechain/blob/master/spec/JSON-RPC.md). In order to utilize JSON-RPC, you can use Curl or [JavaScript SDK](https://api.codechain.io/).

## Connecting to the network

You can participate in the Corgi or Main network.

You could get information about Corgi at this [link](https://corgi.codechain.io/). In order to participate in the Corgi network, you should use the command below:

```
codechain --chain corgi --no-miner --bootstrap-addresses "35.200.30.13:3485" "35.196.235.61:3485" "35.247.47.240:3485" "35.240.69.133:3485" "34.87.107.91:3485"
```

In order to participate in the Main network, you should use the command below:

```
codechain --no-miner --bootstrap-addresses "13.115.159.65:3485" "18.205.137.116:3485" "13.52.129.93:3485" "18.194.21.237:3485" "13.124.155.240:3485"
```

## Checking if CodeChain is configured properly

JSON-RPC is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over HTTP, or in many various message passing environments. It uses JSON (RFC 4627) as data format.

### Using Curl

First, check whether CodeChain's RPC port is listening for RPC connections. By default it should be PORT 8080.

In order to check whether CodeChain is configured properly or not, send a ping to check whether CodeChain's RPC server is actually responding. To do this, do the following:

```
curl \
    -H 'Content-Type: application/json' \
    -d '{"jsonrpc": "2.0", "method": "ping", "params": [], "id": null}' \
    localhost:8080
```

You should get the following response, or something similar:

```
{"jsonrpc":"2.0","result":"pong","id":null}
```

### Using JavaScript SDK

In order to use this method, first install the sdk by running the following:

```
npm install codechain-sdk
```

```
yarn add codechain-sdk
```

Then, make sure that your CodeChain RPC server is listening. In the examples, we assume it is localhost:8080

If you run the following code, your should receive a ping response:

```
// ping.js (javascript)
var SDK = require("codechain-sdk");

var sdk = new SDK({ server: "http://localhost:8080" });

sdk.rpc.node.ping().then(function (response) {
    console.log("Ping response:", response);
}).catch(console.error);
```

If you want to run the above example in the command line, first install `nvm` by running the following:

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

Then run the following:

```
node -e 'var SDK = require("codechain-sdk"); var sdk = new SDK({ server: "http://localhost:8080" });sdk.rpc.node.ping().then(function (response) {console.log("Ping response:", response); }).catch(console.error);'
```

You should receive the following response:

```
Ping response: pong
```
