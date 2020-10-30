<h1 align="center">
    Hschainjs - hschain JavaScript Library 
</h1>

*:star: Developed / Developing by ver*

A JavasSript Open Source Library for [hschain Network](https://testnet.hschain.io/)

This library supports cosmos address generation and verification. It enables you to create an offline signature functions of different types of transaction messages. It will eventually support all the other blockchains that are based on Tendermint in the future.

## Installation

In order to fully use this library, you need to run a local or remote full node and set up its rest server, which acts as an intermediary between the front-end and the full-node

### NPM

```bash
npm install hschainjs
```

### Browser Distribution

HSCHAINJS supports browserify.

## Import 

#### NodeJS

```js
const hschainjs = require("hschainjs");
```

#### ES6 module
```js
import hschainjs from "hschainjs";
```

#### CDN

- You can find hschainjs jsDelivr and use the global hschainjs variable.
- You can see example file at [/example/browser-example.html](https://github.com/cosmostation/hschainjs/tree/master/example/browser-example.html)

```js
<script src="https://cdn.jsdelivr.net/npm/hschainjs@1.0.0/dist/hschain.min.js"></script>
```

## Usage
- hschainjs: Generate Cosmos address from mnemonic  
```js
const hschainjs = require("hschainjs");

const chainId = "hst01";
const hschain = hschainjs.network(lcdUrl, chainId);

const mnemonic = "..."
hschain.setPath("m/44'/118'/0'/0/0");
const address = hschain.getAddress(mnemonic);
const ecpairPriv = hschain.getECPairPriv(mnemonic);
```

Generate ECPairPriv value that is needed for signing signatures
```js
const ecpairPriv = hschain.getECPairPriv(mnemonic);
```

Transfer ATOM to designated address. 
* Make sure to input proper type, account number, and sequence of the cosmos account to generate StdSignMsg. You can get those account information on blockchain 
* Above 0.5.0 version, Hschainjs follows the exact same json format as Cosmos SDK defines.
```js
hschain.getAccounts(address).then(data => {
	let stdSignMsg = hschain.newStdMsg({
		msgs: [
			{
				type: "hschain-sdk/MsgSend",
				value: {
					amount: [
						{
							amount: String(100000),
							denom: "uhst"
						}
					],
					from_address: address,
					to_address: "hsc18vhdczjut44gpsy804crfhnd5nq003nz0nf20v"
				}
			}
		],
		chain_id: chainId,
		fee: { amount: [ { amount: String(5000), denom: "uhst" } ], gas: String(200000) },
		memo: "",
		account_number: String(data.result.value.account_number),
		sequence: String(data.result.value.sequence)
	});

	...
})
```

Sign transaction by using stdSignMsg and broadcast by using [/txs]https://testnet.hschain.io/txs) REST API
```js
const signedTx = hschain.sign(stdSignMsg, ecpairPriv);
hschain.broadcast(signedTx).then(response => console.log(response));
```
