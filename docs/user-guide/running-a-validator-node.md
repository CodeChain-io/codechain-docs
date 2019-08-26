# Running a Validator Node

## What is the validator node?

The CodeChain mainnet uses the Tendermint consensus algorithm to create blocks. The nodes that participate in the consensus are known as _validator nodes_.

## Recommended hardware requirements

| Resource          | Requirement |
| ----------------- | ----------- |
| CPU               | 8+ cores    |
| RAM               | 16 GiB+     |
| SSD               | 1 TiB+      |
| GPU               | X           |
| Network bandwidth | 1 Gbps+     |

## Prepare the CodeChain binary

### Download binary

You can download [the latest binary](https://github.com/CodeChain-io/codechain/releases) from GitHub. It's recommended to use the latest binary. Although it is recommended for you to use the latest binary, you MUST at least use a binary of the same major version as the latest version.

The pre-release binary may be compatible with the latest, but it's not recommended, as it is for the Corgi testnet.

### Build from source

If you want to build the binary from source, use the version tag instead of the master branch. The master branch is used for development. It contains many experimental features and is not compatible with the mainnet.

## Prepare an account

An account should have enough delegation of stake tokens to be a validator in CodeChain.

### Create an account

Create an account using `codechain-keystore-cli`.

First install `codechain-keystore-cli` using the command below:

```
yarn global add codechain-keystore-cli
```

or

```
npm install --global codechain-keystore-cli
```

Create an account using the command below:

```
cckey create -t platform
```

A JSON formatted key file is printed in your shell. Save the JSON text to a file and import it to CodeChain:

```
codechain account import <exported CodeChain JSON key file path>
```

You can check imported accounts by running `codechain account list`.

In addition, you should create a password file to use an account in the CodeChain program. Please create a `password.json` file and specify the password that you typed in the import command.

Here is a sample of password.json:

```
[
 {
   "address": "cccqygfj5kdqecsgdcf422nld58vexkjup9xgf6036e",
   "password": "jmm5m31z4xl6ge46kr50v9"
 }
]
```

## Run CodeChain

Please run CodeChain using the `password-path`, `engine-signer`, and `bootstrap-addresses` parameters.

- `password-path`: The file path of the password.json file that you created.
- `engine-signer`: The validator account's address.
- `bootstrap-addresses`: The IP addresses of the nodes in the network.

```
codechain \
  --password-path password.json \
  --engine-signer cccqygfj5kdqecsgdcf422nld58vexkjup9xgf6036e \
  --bootstrap-addresses "18.194.21.237:3485" "13.52.129.93:3485" "18.205.137.116:3485"
```

The addresses ("18.194.21.237:3485", "13.52.129.93:3485", and "18.205.137.116:3485") above are CodeChain mainnet's bootstrap nodes.

You can check whether peers are connected or not using the command below:

```
curl \
    -H 'Content-Type: application/json' \
    -d '{"jsonrpc": "2.0", "method": "net_getEstablishedPeers", "params": [], "id": 3}' \
    localhost:8080
```

Here is a sample output:

```
{
  "jsonrpc":"2.0",
  "result": ["1.2.3.4:3485", "1.2.3.5:3485"],
  "id":3
}
```

There will be more than just the address in the `result` field.

In addition, you can check your CodeChain instance's best block as follows:

```
curl \
    -H 'Content-Type: application/json' \
    -d '{"jsonrpc": "2.0", "method": "chain_getBestBlockId", "params": [], "id": null}' \
    localhost:8080
```

Here is a sample output:

```
{
  "jsonrpc":"2.0",
  "result":{
    "hash":"0x7f7104b580f9418d444560009e5a92a4573d42d2c51cd0c6045afdc761826249",
    "number":1
  },
  "id":null
}
```

If the sync process is working well, you can see the number field change each time you request it. When the sync is complete, the number field will be the same as the latest block in the block explorer.

NOTE:. It's important that you don't run different nodes with the same engine signer. Two nodes would sign different proposals and your account will be punished for double voting.

## Nominate yourself for the election

You need to stand for the election to become one of the validators. The nomination expires in 24 hours, so you have to send a transaction again every 24 hours. You are a candidate for 24 hours after you have nominated yourself. You can be elected as a validator in the election of this period.

You have to deposit at least 10,000,000 CCC to become a validator. The election occurs every hour and is based on the delegation you have received and the deposit you have submitted. You can see the detailed process of the election on the specification and this blog article.

Install `codechain-stakeholder-cli` to self-nominate using the command below:

```
yarn global add codechain-stakeholder-cli@alpha
```

or

```
npm install --global codechain-stakeholder-cli
```

Send the self-nominate transaction using the command below:

```
ccstake self-nominate --rpc-server "http://127.0.0.1:8080" --account "<your platform address>" --deposit 10000000 --metadata "<information for stakeholders>" --fee 1000
```

You can add metadata like a promotional phrase or short URLs to a self-nomination transaction that's up to 128 bytes of data.

## Auto re-nomination

The scripts below help you to nominate yourself automatically.

Clone the `cron-jobs` repository using the command below:

```
git clone https://github.com/CodeChain-io/cron-jobs.git
```

Move to the `auto-self-nominate` directory using the command below:

```
cd cron-jobs/auto-self-nominate
```

Build the `auto-self-nominate` using the commands below:

```
yarn install
yarn build
```

Run the auto-self-nominate with parameters using the command below:

```
RPC_URL="http://127.0.0.1:8080" \
NETWORK_ID=cc \
ACCOUNT_ADDRESS=<your platform address> \
PASSPHRASE=<you set when creating the account> \
METADATA=<nomination metadata> \
TARGET_DEPOSIT=<target deposit CCC> \
node build/index.js
```

## Promote yourself

You not only need a deposit, but also the delegation of the stakeholders. Thus, you need to promote yourself to become a validator.

You can describe yourself on the nomination transaction in 128 bytes(If the dynamic validator is not enabled yet, the size limit is 0. Only empty string is available). However, this is not long enough to describe yourself, so we recommend you to write a URL that publicises your information and the signature that was signed with your key.

## How to upgrade the node

It's obvious you don't need to run the nodes if you are not a validator, but; you can upgrade your nodes while you are not elected.

Sometimes you must upgrade the CodeChain binary ASAP for reasons such as a security bug in the previous version. In these cases, the CodeChain development team will let you know if you keep in touch with the team. You should upgrade your machine immediately even if you are a validator.

If you want to upgrade your hardware or move your machine that runs the CodeChain node, you must prepare your new machine to be synchronized. While synchronizing, you should not use the engine signer, as it can make double votes.

```
codechain --no-miner --bootstrap-addresses <address of your old machine>
```
