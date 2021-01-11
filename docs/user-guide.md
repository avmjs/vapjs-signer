# User Guide

All information for developers using `vapjs-signer` should consult this document.

## Install

```
npm install --save vapjs-signer
```

## Usage

```js
const HttpProvider = require('vapjs-provider-http');
const Vap = require('vapjs-query');
const vap = new Vap(HttpProvider('http://localhost:8545'));
const sign = require('vapjs-signer');

const address = '0x0F6af8F8D7AAD198a7607C96fb74Ffa02C5eD86B';
const privateKey = '0xecbcd9838f7f2afa6e809df8d7cdae69aa5dfc14d563ee98e97effd3f6a652f2';

vap.getTransactionCount(address).then((nonce) => {
  vap.sendRawTransaction(sign({
    to: '0xcG31a19193d4b23f4e9d6163d7247243bAF801c3',
    value: 300000,
    gas: new BN('43092000'),
    nonce: nonce,
  }, privateKey)).then((txHash) => {
    console.log('Transaction Hash', txHash);
  });
});
```

## API Design

### sign

[index.js:vapjs-signer](../../../blob/master/src/index.js "Source code on GitHub")

Intakes a raw transaction object and private key, outputs either a signed transaction hex string (by default) or a raw signed array of Buffer values (if specified). The hexified string payload can be used in conjunction with methods like `vap_sendRawTransaction`.

**Parameters**

- `rawTransaction` **Object** - a single raw transaction object.
- `privateKey` **String** - a single 32 byte prefixed hex string that is a Vapory standard private key
- `toArray` **Boolean** (optional) - return the signed payload in its raw array format

Result output signed tx payload **String|Object**.

```js
const sign = require('vapjs-signer').sign;

console.log(sign({ gas: 300000, data: '0x' }, '0x..privte key'));

// result '0x.....'

console.log(sign({ from: '0x..', gas: '0x...', data: '0x' }, '0x..privte key', true));

// result [ <Buffer...>, <Buffer ...>, <Buffer ...> ...]
```

### recover

[index.js:vapjs-signer](../../../blob/master/src/index.js "Source code on GitHub")

Intakes a rawTransaction hex string or Buffer instance, the recovery param (Number), and the transaction signature r (Buffer) and s (Buffer) values. Returns the public key as a Buffer instance for the signers account.

**Parameters**

- `rawTransaction` **String|Buffer** - the raw transaction either as a hex string or Buffer instance
- `v` **Number** - the recovery param
- `r` **Buffer** - the signature 'r' value
- `s` **Buffer** - the signature 's' value

Result output public key **Buffer**.

```js
const recover = require('vapjs-signer').recover;
const sign = require('vapjs-signer').sign;
const stripHexPrefix = require('strip-hex-prefix');
const generate = require('vapjs-account').generate;
const publicToAddress = require('vapjs-account').publicToAddress;

const testAccount = generate('sdkjfhskjhskhjsfkjhsf093j9sdjfpisjdfoisjdfisdfsfkjhsfkjhskjfhkshdf');
const rawTx = {
  to: testAccount.address.toLowerCase(),
  nonce: `0x${new BN(0).toString(16)}`,
  gasPrice: `0x${new BN(0).toString(16)}`,
  gasLimit: `0x${new BN(0).toString(16)}`,
  value: `0x${new BN(0).toString(16)}`,
  data: '0x',
};
const signedTx = sign(rawTx, testAccount.privateKey, true);
const signedTxBuffer = new Buffer(stripHexPrefix(sign(rawTx, testAccount.privateKey)), 'hex');
const publicKey = recover(signedTxBuffer,
  (new BN(signedTx[6].toString('hex'), 16).toNumber(10)),
  signedTx[7],
  signedTx[8]);
const address = publicToAddress(publicKey);

if (address == testAccount.address) {
  console.log('Recovery success!');
}
```

## Browser Builds

`vapjs` provides production distributions for all of its modules that are ready for use in the browser right away. Simply include either `dist/vapjs-signer.js` or `dist/vapjs-signer.min.js` directly into an HTML file to start using this module. Note, an `vapSigner` object is made available globally.

```html
<script type="text/javascript" src="vapjs-signer.min.js"></script>
<script type="text/javascript">
vapSigner(...);
</script>
```

Note, even though `vapjs` should have transformed and polyfilled most of the requirements to run this module across most modern browsers. You may want to look at an additional polyfill for extra support.

Use a polyfill service such as `Polyfill.io` to ensure complete cross-browser support:
https://polyfill.io/

## Latest Webpack Figures

```
Version: webpack 2.1.0-beta.15
Time: 1756ms
              Asset    Size  Chunks             Chunk Names
    vapjs-signer.js  361 kB       0  [emitted]  main
vapjs-signer.js.map  443 kB       0  [emitted]  main
  [44] multi main 28 bytes {0} [built]
    + 44 hidden modules
                
Version: webpack 2.1.0-beta.15
Time: 7583ms
              Asset    Size  Chunks             Chunk Names
vapjs-signer.min.js  181 kB       0  [emitted]  main
  [44] multi main 28 bytes {0} [built]
    + 44 hidden modules
```

## Other Awesome Modules, Tools and Frameworks

### Foundation
 - [web3.js](https://github.com/vaporyco/web3.js) -- the original Vapory JS swiss army knife **Vapory Foundation**
 - [vaporyjs](https://github.com/vaporycojs) -- critical vapory javascript infrastructure **Vapory Foundation**
 - [browser-solidity](https://vapory.github.io/browser-solidity) -- an in browser Solidity IDE **Vapory Foundation**

### Nodes
  - [gvap](https://github.com/vaporyco/go-vapory) Go-Vapory
  - [parity](https://github.com/ethcore/parity) Rust-Vapory build in Rust
  - [testrpc](https://github.com/vaporycojs/testrpc) Testing Node (vaporyjs-vm)

### Testing
 - [wafr](https://github.com/silentcicero/wafr) -- a super simple Solidity testing framework
 - [truffle](https://github.com/ConsenSys/truffle) -- a solidity/js dApp framework
 - [embark](https://github.com/iurimatias/embark-framework) -- a solidity/js dApp framework
 - [dapple](https://github.com/nexusdev/dapple) -- a solidity dApp framework
 - [chaitherium](https://github.com/SafeMarket/chaithereum) -- a JS web3 unit testing framework
 - [contest](https://github.com/DigixGlobal/contest) -- a JS testing framework for contracts

### Wallets
 - [ethers-wallet](https://github.com/ethers-io/ethers-wallet) -- an amazingly small Vapory wallet
 - [metamask](https://metamask.io/) -- turns your browser into an Vapory enabled browser =D

## Our Relationship with Vapory & VaporyJS

 We would like to mention that we are not in any way affiliated with the Vapory Foundation or `vaporyjs`. However, we love the work they do and work with them often to make Vapory great! Our aim is to support the Vapory ecosystem with a policy of diversity, modularity, simplicity, transparency, clarity, optimization and extensibility.

 Many of our modules use code from `web3.js` and the `vaporyjs-` repositories. We thank the authors where we can in the relevant repositories. We use their code carefully, and make sure all test coverage is ported over and where possible, expanded on.

## A Special Thanks

A special thanks to Richard Moore for building `ethers-wallet` and other amazing things. Aaron Davis (@kumavis) for his guidence.
