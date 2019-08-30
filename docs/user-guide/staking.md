# Staking

## Delegating/Revoking Stake

If you have CCS, you can delegate CCS anytime to any account. The validators are elected in the order of the amount of delegated CCS. For the details, see [this link](https://medium.com/codechain/codechain-staking-guide-eecbc3fff041).

## Prepare for Delegation

### Install Stakeholder CLI

Use the command below to install stakeholder CLI:

```
yarn global add codechain-stakeholder-cli
```

or

```
npm install -g codechain-stakeholder-cli
```

If the installation was successful, the result of `ccstake --version` will be `2.0.0`.

## How to Delegate CCS

Run the command below:

```
ccstake delegate \
  --rpc-server "http://127.0.0.1:8080" \
  --keys-path ./keystore.db
  --account <Account holding CCS> \
  --delegatee <Account to delegate> \
  --quantity <CCS quantity> \
  --fee 100
```

- `--rpc-server`: Since you need to send a transaction to delegate CCS to someone else, you need a CodeChain node that accepts and broadcasts your transaction to the CodeChain network. We recommend you run your own CodeChain node. If you are running a CodeChain node in the same machine with the default RPC port, you can use the address "http://127.0.0.1:8080" in the argument. If you are not running a CodeChain node, you can use a public RPC node, but public RPC nodes are not always available. The main network's public RPC address is "https://rpc.codechain.io". The Corgi network's public RPC address is "https://corgi-rpc.codechain.io".
- `--keys-path`: To delegate CCS to an account, you should verify that you are the owner of the CCS. CodeChain uses a key file to save your private key. Please specify the key file that has an account that could unlock your account.
- `--account`: An account that has CCS tokens.
- `--delegatee`: An account to be delegated.
- `--quantity`: Delegation quantity.
- `--fee`: Transaction fee.

## How to Revoke Delegation

You can revoke delegated CCS anytime with the Revoke transaction. An account that is already active as a validator will be a validator until the end of the term, even if the delegation is revoked.

To revoke a delegation, run the command below:

```
ccstake revoke \
  --rpc-server "http://127.0.0.1:8080" \
  --keys-path ./keystore.db
  --account <Account delegated CCS> \
  --delegatee <Account is delegated CCS> \
  --quantity <CCS quantity> \
  --fee 100
```

- `--rpc-server`: Please refer to the explanation above.
- `--keys-path`: Please refer to the explanation above.
- `--account`: The account that has CCS and delegated CCS to the delegatee.
- `--delegatee`: The account that is delegated CCS.
- `--quantity`: Revoke quantity.
- `--fee`: Transaction fee.

## Change Common Params

CCS holders could change CommonParams if more than half of CCS holders agreed. There are two roles in the process. One is _Initiator_ and the other one is _signers_. The initiator creates candidate params and collects signatures from _signers_. Signers verify candidate params and send a signature if they agree on it.

### Initiator Instruction

Visit [CodeChain Change Common Params page](https://codechain-io.github.io/codechain-change-common-params/#/) and change params to the desired value. You can load the current parameters from CodeChain. Due to the browsers' security policy, you can use an RPC server that supports https, or a local CodeChain node(if you are using Chrome).

Share the generated URL that is presented in the "Share" box with _signers_.

When _signers_ send their signatures, insert the signatures in the "Collect signature" box.

When enough signatures are collected, click the "CreateTransaction" button to create encoded transaction information.

Run the following script to send the encoded transaction information:

```
ccstake change-params \
  --rpc-server "http://127.0.0.1:8080" \
  --keys-path ./keystore.db \
  --account <Account to pay fee> \
  --fee 1000 \
  --transaction f8e081ff80f8552082040082010082020082626364822710830f4240830186a0822710830186a080821388821388830186a064830186a0830186a0830186a0830186a064834000008240003c81f002031e04830186a08203e8820100b8417659b87bdb10e3fc95b377df0931196d2993a32d767ffd0593e9dcae42ffadc36f8831b021073583398cdf210cee4ad304b754fa69585b352c549232770302ac01b841ab7f0da6964cb544e6ff4ec8bcb41ae4ce6767bf8dd80ccb1f14f7eb16a1d9d6737bf7ef2122f88e23e53fd96172c88d0833d58b05783b74ae7da4447c680b2400
```

- `--rpc-server`: Please refer to the explanation above.
- `--keys-path`: Please refer to the explanation above.
- `--account`: The account that sends a transaction to the network, and pays a fee
- `--fee`: Transaction fee.
- `--transaction`: A created transaction itself.

### Signers Instruction

If the initiator gives you a ChangeParams link, check the params carefully. If you think that the values are correct, sign the encoded params in the bottom of the page using all of your accounts that have CCS.

```
ccstake sign --message f85a81ff80f8552082040082010082020082626364822710830f4240830186a0822710830186a080821388821388830186a064830186a0830186a0830186a0830186a064834000008240003c81f002031e04830186a08203e8820100 \
 --account cccq8ah0efv5ckpx6wy5mwva2aklzwsdw027sqfksrr
 --keys-path ./keystore.db
```

- `--keys-path`: Please refer to the explanation above.
- `--message`: The encoded params data that is printed in the ChangeParams page.
- `--account`: The account that is used to sign Params. The account should have CCS.

If successful, a signature will be printed in the console. Pass the signature to the initiator.
