## Web3.js Interface 

Interact with nodes through web3 objects provided by web3.js. On the underlying implementation, it communicates with the local node through RPC calls. web3.js can connect to any PlatON node that exposes the RPC interface.

### Usage

First, make sure the nodeJS environment is successfully installed locally. `WEB3.JS` uses the [lerna](https://github.com/lerna/lerna) management tool to optimize the workflow of the multi-package code base hosted on `git\ npm`, so you make sure before installing The lerna package has been installed globally. If not, execute the command `npm i lerna -g` for global installation. 

Then you can integrate client-sdk-js into the project project through package management tools such as npm or yarn, the steps are as follows:

- npm: `npm i PlatONnetwork/client-sdk-js`
- yarn: `yarn add PlatONnetwork/client-sdk-js`

Create a web3 instance and set up a provider. You can refer to the following code:

```js
// in node.js
var Web3 = require('web3');

var web3 = new Web3('http://127.0.0.1:6789');
console.log(web3);
> {
    platon: ... ,
    utils: ...,
    ppos: ...,
    ...
}
```

After successful introduction, you can use the relevant API of web3.


### API Reference

#### web3.version

`web3.version`: Contains the current package version of the web3.js library.

Method:

```js
Web3.version
web3.version
```

Returns:

`String`: Current version number.

Example:

```js
web3.version;
> "0.8.0"
```

***

#### web3.modules

`web3.modules` Will return an object with the classes of all major sub modules, to be able to instantiate them manually.

Method:

```js
Web3.modules
web3.modules
```

Returns:

`Object`: A list of module constructors:

*  `Platon` - Function: The PlatON module for interacting with the PlatON network see web3.platon for more.
*  `Net` - Function: The Net module for interacting with network properties see web3.platon.net for more.
*  `Personal` - Function: The Personal module for interacting with the PlatON accounts see web3.platon.personal for more.

Example:

```js
web3.modules
> {
    Platon: Platon function(provider),
    Net: Net function(provider),
    Personal: Personal function(provider)
}
```

***

#### web3.setProvider

`web3.setProvider()` Will change the provider for its module.

Method:

```
web3.setProvider(myProvider)
web3.platon.setProvider(myProvider)
...
```

Notes: When called on the umbrella package web3 it will also set the provider for all sub modules web3.platon, web3.shh, etc EXCEPT web3.bzz which needs a separate provider at all times.

Parameter:

`Object` - myProvider: a valid provider.

Returns:

`Boolean`

Example:

```js
var Web3 = require('web3');
var web3 = new Web3('http://localhost:8545');
// or
var web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));

// change provider
web3.setProvider('ws://localhost:8546');
// or
web3.setProvider(new Web3.providers.WebsocketProvider('ws://localhost:8546'));
```

***

#### web3.providers

Contains the current available providers.

Method:

```
web3.providers
web3.platon.providers
...
```

Returns:

`Object`， with the following providers:

*  Object - HttpProvider: The HTTP provider is deprecated, as it won’t work for subscriptions.
*  Object - WebsocketProvider: The Websocket provider is the standard for usage in legacy browsers.

Example:

```js
var Web3 = require('web3');
// // use the given Provider, e.g in Mist, or instantiate a new websocket provider.
var web3 = new Web3(Web3.givenProvider || 'ws://remotenode.com:8546');
// or
var web3 = new Web3(Web3.givenProvider || new Web3.providers.WebsocketProvider('ws://remotenode.com:8546'));
```

***

#### web3.givenProvider

When using web3.js in an Ethereum compatible browser, it will set with the current native provider by that browser. Will return the given provider by the (browser) environment, otherwise null.

Method:

```
web3.givenProvider
web3.platon.givenProvider
...
```

Returns:

`Object`:  The given provider set or null;

***

#### web3.currentProvider

`web3.currentProvider` Will return the current provider, otherwise null.

Method:

```
web3.currentProvider
web3.platon.currentProvider
...
```

Returns:

`Object`： The current provider set or null;

***

#### web3.BatchRequest

`web3.BatchRequest` Class to create and execute batch requests.

Method:

```
new web3.BatchRequest()
new web3.platon.BatchRequest()
```

Parameter:

none


Returns:

Object: With the following methods:

*  `add(request)`: To add a request object to the batch call.
*  `execute()`: Will execute the batch request.

Example:

```js
var contract = new web3.platon.Contract(abi, address);

var batch = new web3.BatchRequest();
batch.add(web3.platon.getBalance.request('0x0000000000000000000000000000000000000000', 'latest', callback));
batch.add(contract.methods.balance(address).call.request({from: '0x0000000000000000000000000000000000000000'}, callback2));
batch.execute();
```

***

#### web3.platon.defaultAccount

`web3.platon.defaultAccount` This default address is used as the default "from" property, if no "from" property is specified in for the following methods:

*  web3.platon.sendTransaction()
*  web3.platon.call()
*  new web3.platon.Contract() -> myContract.methods.myMethod().call()
*  new web3.platon.Contract() -> myContract.methods.myMethod().send()

Method:

```
web3.platon.defaultAccount
```

Property：

`String` - 20 Bytes: 20 Bytes: Any PlatON address. You should have the private key for that address in your node or keystore. (Default is undefined)

Example:

```js
web3.platon.defaultAccount;
> undefined

// set the default account
web3.platon.defaultAccount = '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe';
```

***

#### web3.platon.defaultBlock

`web3.platon.defaultBlock` The default block is used for certain methods. 

*  web3.platon.getBalance()
*  web3.platon.getCode()
*  web3.platon.getTransactionCount()
*  web3.platon.getStorageAt()
*  web3.platon.call()
*  new web3.platon.Contract() -> myContract.methods.myMethod().call()

You can override it by passing in the defaultBlock as last parameter. 

Method:

```
web3.platon.defaultBlock
```

Property:

Default block parameters can be one of the following:

*  Number: A block number
*  "genesis" - String: The genesis block
*  "latest" - String: The latest block (current head of the blockchain)
*  "pending" - String: The currently mined block (including pending transactions)

Default is "latest"

Example:

```js
web3.platon.defaultBlock;
> "latest"

// set the default block
web3.platon.defaultBlock = 231;
```

***

#### web3.platon.getProtocolVersion

Returns the ethereum protocol version of the node.

Method:

```
web3.platon.getProtocolVersion([callback])
```

Returns:

Promise returns String: the protocol version.

Example:

```js
web3.platon.getProtocolVersion().then(console.log);
> "63"
```

***

#### web3.platon.isSyncing

`web3.platon.isSyncing()` Checks if the node is currently syncing and returns either a syncing object, or false.

Method:

```
web3.platon.isSyncing([callback])
```

Returns:

Promise returns Object|Boolean - A sync object when the node is currently syncing or false:

*  startingBlock - Number: The block number where the sync started.
*  currentBlock - Number: The block number where at which block the node currently synced to already.
*  highestBlock - Number: The estimated block number to sync to.
*  knownStates - Number: The estimated states to download.
*  pulledStates - Number: The already downloaded states.

Example:

```js
web3.platon.isSyncing().then(console.log);
> {
    startingBlock: 100,
    currentBlock: 312,
    highestBlock: 512,
    knownStates: 234566,
    pulledStates: 123455
}
```

***

#### web3.platon.getGasPrice

`web3.platon.getGasPrice()` Returns the current gas price oracle. The gas price is determined by the last few blocks median gas price.

Method:

```
web3.platon.getGasPrice([callback])
```

Returns:

Promise returns String - Number string of the current gas price in von.

Example:

```js
web3.platon.getGasPrice().then(console.log);
> "20000000000"
```

***

#### web3.platon.getAccounts

`web3.platon.getAccounts()` Returns a list of accounts the node controls.

Method:

```
web3.platon.getAccounts([callback])
```

Returns:

Promise returns Array - An array of addresses controlled by node.

Example:

```js
web3.platon.getAccounts().then(console.log);
> ["0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe", "0xDCc6960376d6C6dEa93647383FfB245CfCed97Cf"]
```

***

#### web3.platon.getBlockNumber

`web3.platon.getBlockNumber()` Returns the current block number.

Method:

```
web3.platon.getBlockNumber([callback])
```

Returns:

Promise returns Number - The number of the most recent block.

Example:

```js
web3.platon.getBlockNumber().then(console.log);
> 2744
```

***

#### web3.platon.getBalance

`web3.platon.getBalance()` Get the balance of an address at a given block.

Method:

```
web3.platon.getBalance(address [, defaultBlock] [, callback])
```

Parameter:

*  `address`：String - The address to get the balance of.
*  `defaultBlock`：Number|String -  (optional) If you pass this parameter it will not use the default block set with web3.platon.defaultBlock. Pre-defined block numbers as "latest", "earliest", "pending", and "genesis" can also be used.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns String - The current balance for the given address in von.

Example:

```js
web3.platon.getBalance("0x407d73d8a49eeb85d32cf465507dd71d507100c1")
.then(console.log);
> "1000000000000"
```

***

#### web3.platon.getStorageAt

`web3.platon.getStorageAt()` Get the storage at a specific position of an address.

Method:

```
web3.platon.getStorageAt(address, position [, defaultBlock] [, callback])
```

Parameter:

*  `address`：String - The address to get the storage from.
*  `position`：Number - The index position of the storage.
*  `defaultBlock`：Number|String -  (optional) If you pass this parameter it will not use the default block set with web3.platon.defaultBlock. Pre-defined block numbers as "latest", "earliest", "pending", and "genesis" can also be used.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns String - The value in storage at the given position.

Example:

```js
web3.platon.getStorageAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0)
.then(console.log);
> "0x033456732123ffff2342342dd12342434324234234fd234fd23fd4f23d4234"
```

***

#### web3.platon.getCode

`web3.platon.getCode()` Get the code at a specific address.

Method:

```
web3.platon.getCode(address [, defaultBlock] [, callback])
```

Parameter:

*  `address`：String - The address to get the code from.
*  `defaultBlock`：Number|String - (optional) If you pass this parameter it will not use the default block set with web3.platon.defaultBlock. Pre-defined block numbers as "latest", "earliest", "pending", and "genesis" can also be used.
*  `callback`：Function -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns String - The data at given address address.

Example:

```js
web3.platon.getCode("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8")
.then(console.log);
> "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

***

#### web3.platon.getBlock

`web3.platon.getBlock()` Returns a block matching the block number or block hash.

Method:

```
web3.platon.getBlock(blockHashOrBlockNumber [, returnTransactionObjects] [, callback])
```

Parameter:

*  `blockHashOrBlockNumber`：String|Number - The block number or block hash. Or the string "genesis", "latest", "earliest", or "pending" as in the default block parameter.
*  `returnTransactionObjects`：Boolean -   (optional, default false) If specified true, the returned block will contain all transactions as objects. By default it is false so, there is no need to explictly specify false. And, if false it will only contains the transaction hashes.
*  `callback`：Function -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns Object - The block object:

* `number` - `Number`: The block number. null when its pending block.
* `hash` 32 Bytes - `String`: Hash of the block. null when its pending block.
* `parentHash` 32 Bytes - `String`: Hash of the parent block.
* `nonce` 8 Bytes - `String`: Hash of the generated proof-of-work. null when its pending block.
* `sha3Uncles` 32 Bytes - `String`: SHA3 of the uncles data in the block.
* `logsBloom` 256 Bytes - `String`: The bloom filter for the logs of the block. null when its pending block.
* `transactionsRoot` 32 Bytes - `String`: The root of the transaction trie of the block
* `stateRoot` 32 Bytes - `String`: The root of the final state trie of the block.
* `miner` - `String`: The address of the beneficiary to whom the mining rewards were given.
* `difficulty` - `String`: Integer of the difficulty for this block.
* `totalDifficulty` - `String`: Integer of the total difficulty of the chain until this block.
* `extraData` - `String`: The “extra data” field of this block.
* `size` - `Number`: Integer the size of this block in bytes.
* `gasLimit` - `Number`: The maximum gas allowed in this block.
* `gasUsed` - `Number`: The total used gas by all transactions in this block.
* `timestamp` - `Number`: The unix timestamp for when the block was collated.
* `transactions` - `Array`: Array of transaction objects, or 32 Bytes transaction hashes depending on the returnTransactionObjects parameter.
* `uncles` - `Array`: Array of uncle hashes.

Example:

```js
web3.platon.getBlock(3150)
.then(console.log);

> {
    "number": 3,
    "hash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
    "parentHash": "0x2302e1c0b972d00932deb5dab9eb2982f570597d9d42504c05d9c2147eaf9c88",
    "nonce": "0xfb6e1a62d119228b",
    "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "transactionsRoot": "0x3a1b03875115b79539e5bd33fb00d8f7b7cd61929d5a3c574f507b8acf415bee",
    "stateRoot": "0xf1133199d44695dfa8fd1bcfe424d82854b5cebef75bddd7e40ea94cda515bcb",
    "miner": "0x8888f1f195afa192cfee860698584c030f4c9db1",
    "difficulty": '21345678965432',
    "totalDifficulty": '324567845321',
    "size": 616,
    "extraData": "0x",
    "gasLimit": 3141592,
    "gasUsed": 21662,
    "timestamp": 1429287689,
    "transactions": [
        "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b"
    ],
    "uncles": []
}
```

***

#### web3.platon.getBlockTransactionCount

`web3.platon.getBlockTransactionCount()` Returns the number of transaction in a given block.

Method:

```
web3.platon.getBlockTransactionCount(blockHashOrBlockNumber [, callback])
```

Parameter:

*  `blockHashOrBlockNumber`：String|Number - The block number or hash. Or the string "genesis", "latest", "earliest", or "pending" as in the default block parameter.
*  `callback`：Function -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`Promise` returns `Number` - The number of transactions in the given block.

Example:

```js
web3.platon.getBlockTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1")
.then(console.log);
> 1
```

***

#### web3.platon.getTransaction

`web3.platon.getTransaction()` Returns a transaction matching the given transaction hash.

Method:

```
web3.platon.getTransaction(transactionHash [, callback])
```

Parameter:

*  `transactionHash`：`String` - The transaction hash.
*  `callback`：`Function` -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`Promise` returns `Object` - A transaction object its hash `transactionHash`:

* `hash` 32 Bytes - `String:` Hash of the transaction.
* `nonce` - `Number`: The number of transactions made by the sender prior to this one.
* `blockHash` 32 Bytes - `String`: Hash of the block where this transaction was in. null when its pending.
* `blockNumber` - `Number`: Block number where this transaction was in. null when its pending.
* `transactionIndex` - `Number`: Integer of the transactions index position in the block. null when its pending.
* `from` - `String`: Address of the sender.
* `to` - `String`: Address of the receiver. null when its a contract creation transaction.
* `value` - `String`: Value transferred in von.
* `gasPrice` - `String`: Gas price provided by the sender in von.
* `gas` - `Number`: Gas provided by the sender.
* `input` - `String`: The data sent along with the transaction.

Example:

```js
web3.platon.getTransaction('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b§234')
.then(console.log);
> {
    "hash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
    "nonce": 2,
    "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
    "blockNumber": 3,
    "transactionIndex": 0,
    "from": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
    "to": "0x6295ee1b4f6dd65047762f924ecd367c17eabf8f",
    "value": '123450000000000000',
    "gas": 314159,
    "gasPrice": '2000000000000',
    "input": "0x57cb2fc4"
}
```

***

#### web3.platon.getTransactionFromBlock

`web3.platon.getTransactionFromBlock()` Returns a transaction based on a block hash or number and the transactions index position.

Method:

```
getTransactionFromBlock(hashStringOrNumber, indexNumber [, callback])
```

Parameter:

*  `hashStringOrNumber`：String - A block number or hash. Or the string "genesis", "latest", "earliest", or "pending" as in the default block parameter.
*  `indexNumber`：Number - The transactions index position.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`Promise` returns `Object` - A transaction object, see web3.eth.getTransaction:

Example:

```js
var transaction = web3.platon.getTransactionFromBlock('0x4534534534', 2)
.then(console.log);
> // see web3.platon.getTransaction
```

***

#### web3.platon.getTransactionReceipt

`web3.platon.getTransactionReceipt()` Returns the receipt of a transaction by transaction hash.

Notes: The receipt is not available for pending transactions and returns null.

Method:

```
web3.platon.getTransactionReceipt(hash [, callback])
```

Parameter:

*  `hash`：String - The transaction hash.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`Promise` returns `Object` - A transaction receipt object, or null when no receipt was found:

* `status` - `Boolean`: TRUE if the transaction was successful, FALSE, if the EVM reverted the transaction.
* `blockHash` 32 Bytes - `String`: Hash of the block where this transaction was in.
* `blockNumber` - `Number`: Block number where this transaction was in.
* `transactionHash` 32 Bytes - `String`: Hash of the transaction.
* `transactionIndex`- `Number`: Integer of the transactions index position in the block.
* `from` - `String`: Address of the sender.
* `to` - `String`: Address of the receiver. null when its a contract creation transaction.
* `contractAddress` - `String`: The contract address created, if the transaction was a contract creation, otherwise null.
* `cumulativeGasUsed` - `Number`: The total amount of gas used when this transaction was executed in the block.
* `gasUsed` - `Number`: The amount of gas used by this specific transaction alone.
* `logs` - `Array`: Array of log objects, which this transaction generated.


Example:

```js
var receipt = web3.platon.getTransactionReceipt('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b')
.then(console.log);
> {
  "status": true,
  "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
  "transactionIndex": 0,
  "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
  "blockNumber": 3,
  "contractAddress": "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
  "cumulativeGasUsed": 314159,
  "gasUsed": 30234,
  "logs": [{
         // logs as returned by getPastLogs, etc.
     }, ...]
}
```

***

#### web3.platon.getTransactionCount

`web3.platon.getTransactionCount()` Get the numbers of transactions sent from this address.

Method:

```
web3.platon.getTransactionCount(address [, defaultBlock] [, callback])
```

Parameter:

*  `address`：String - The address to get the numbers of transactions from.
*  `defaultBlock`：Number|String -  (optional) If you pass this parameter it will not use the default block set with web3.platon.defaultBlock. Pre-defined block numbers as "latest", "earliest", "pending", and "genesis" can also be used.
*  `callback`：Function -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`Promise` returns `Number` - The number of transactions sent from the given address.

Example:

```js
web3.platon.getTransactionCount("0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe")
.then(console.log);
> 1
```

***

#### web3.platon.sendTransaction

`web3.platon.sendTransaction()` Sends a transaction to the network.

Method:

```
web3.platon.sendTransaction(transactionObject [, callback])
```

Parameter:

*  `transactionObject`：`Object` - The transaction object to send:
	* `from` - `String|Number`: The address for the sending account. Uses the web3.platon.defaultAccount property, if not specified. Or an address or index of a local wallet in web3.platon.accounts.wallet.
	* `to` - `String`: (optional) The destination address of the message, left undefined for a contract-creation transaction.
	* `value` - `Number|String|BN|BigNumber`: (optional) The value transferred for the transaction in wei, also the endowment if it’s a contract-creation transaction.
	* `gas` - `Number`: (optional, default: To-Be-Determined) The amount of gas to use for the transaction (unused gas is refunded).
	* `gasPrice` - `Number|String|BN|BigNumber`: (optional) The price of gas for this transaction in wei, defaults to web3.platon.gasPrice.
	* `data` - `String`: (optional) Either a ABI byte string containing the data of the function call on a contract, or in the case of a contract-creation transaction the initialisation code.
	* `nonce` - `Number`: (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
*  callback - Function: (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`web3.platon.sendTransaction()` The callback will return the 32 bytes transaction hash.

PromiEvent:  A promise combined event emitter. Will be resolved when the transaction receipt is available. Additionally the following events are available:

*  "transactionHash" returns String: Is fired right after the transaction is sent and a transaction hash is available.
*  "receipt"  returns `Object`: Is fired when the transaction receipt is available.
*  "confirmation" returns Number, Object: Is fired for every confirmation up to the 12th confirmation. Receives the confirmation number as the first and the receipt as the second argument. Fired from confirmation 0 on, which is the block where its minded.
*  "error" returns Error and Object|undefined: Is fired if an error occurs during sending. If the transaction was rejected by the network with a receipt, the second parameter will be the receipt.

Example:

```js
// compiled solidity source code using https://remix.ethereum.org
var code = "603d80600c6000396000f3007c01000000000000000000000000000000000000000000000000000000006000350463c6888fa18114602d57005b6007600435028060005260206000f3";

// using the callback
web3.platon.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    data: code // deploying a contracrt
}, function(error, hash){
    ...
});

// using the promise
web3.platon.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    to: '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe',
    value: '1000000000000000'
})
.then(function(receipt){
    ...
});


// using the event emitter
web3.platon.sendTransaction({
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    to: '0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe',
    value: '1000000000000000'
})
.on('transactionHash', function(hash){
    ...
})
.on('receipt', function(receipt){
    ...
})
.on('confirmation', function(confirmationNumber, receipt){ ... })
.on('error', console.error); // If a out of gas error, the second parameter is the receipt.
```

***

#### web3.platon.sendSignedTransaction

`web3.platon.sendSignedTransaction()` Sends an already signed transaction, generated for example using web3.platon.accounts.signTransaction.

Method:

```
web3.platon.sendSignedTransaction(signedTransactionData [, callback])
```

Parameter:

*  `signedTransactionData`：String - Signed transaction data in HEX format.
*  `callback`：Function -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

PromiEvent:  A promise combined event emitter. Will be resolved when the transaction receipt is available.

Please see the return values for web3.platon.sendTransaction for details.

Example:

```js
var Tx = require('ethereumjs-tx');
var privateKey = new Buffer('e331b6d69882b4cb4ea581d88e0b604039a3de5967688d3dcffdd2270c0fd109', 'hex')

var rawTx = {
  nonce: '0x00',
  gasPrice: '0x09184e72a000',
  gasLimit: '0x2710',
  to: '0x0000000000000000000000000000000000000000',
  value: '0x00',
  data: '0x7f7465737432000000000000000000000000000000000000000000000000000000600057'
}

var tx = new Tx(rawTx);
tx.sign(privateKey);

var serializedTx = tx.serialize();

// console.log(serializedTx.toString('hex'));
// 0xf889808609184e72a00082271094000000000000000000000000000000000000000080a47f74657374320000000000000000000000000000000000000000000000000000006000571ca08a8bbf888cfa37bbf0bb965423625641fc956967b81d12e23709cead01446075a01ce999b56a8a88504be365442ea61239198e23d1fce7d00fcfc5cd3b44b7215f

web3.platon.sendSignedTransaction('0x' + serializedTx.toString('hex'))
.on('receipt', console.log);

> // see platon.getTransactionReceipt() for details
```

***

#### web3.platon.sign

`web3.platon.sign()` Signs data using a specific account. This account needs to be unlocked.

Method:

```
web3.platon.sign(dataToSign, address [, callback])
```

Parameter:

*  `dataToSign`：String - Data to sign. If String it will be converted using web3.utils.utf8ToHex.
*  `address`：String|Number - Address to sign data with. Or an address or index of a local wallet in web3.platon.accounts.wallet.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns String - The signature.

Example:

```js
web3.platon.sign("Hello world", "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe")
.then(console.log);
> "0x30755ed65396facf86c53e6217c52b4daebe72aa4941d89635409de4c9c7f9466d4e9aaec7977f05e923889b33c0d0dd27d7226b6e6f56ce737465c5cfd04be400"

// the below is the same
web3.platon.sign(web3.utils.utf8ToHex("Hello world"), "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe")
.then(console.log);
> "0x30755ed65396facf86c53e6217c52b4daebe72aa4941d89635409de4c9c7f9466d4e9aaec7977f05e923889b33c0d0dd27d7226b6e6f56ce737465c5cfd04be400"
```

***

#### web3.platon.signTransaction

`web3.platon.signTransaction()` Signs a transaction. This account needs to be unlocked.

Method:

```
web3.platon.signTransaction(transactionObject, address [, callback])
```

Parameter:

*  `transactionObject`：`Object` - The transaction data to sign web3.eth.sendTransaction() for more.
*  `address`：`String` - Address to sign transaction with.
*  `callback`：`Function` - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns Object - The RLP encoded transaction. The raw property can be used to send the transaction using web3.platon.sendSignedTransaction.

Example:

```js
web3.platon.signTransaction({
    from: "0xEB014f8c8B418Db6b45774c326A0E64C78914dC0",
    gasPrice: "20000000000",
    gas: "21000",
    to: '0x3535353535353535353535353535353535353535',
    value: "1000000000000000000",
    data: ""
}).then(console.log);
> {
    raw: '0xf86c808504a817c800825208943535353535353535353535353535353535353535880de0b6b3a76400008025a04f4c17305743700648bc4f6cd3038ec6f6af0df73e31757007b7f59df7bee88da07e1941b264348e80c78c4027afc65a87b0a5e43e86742b8ca0823584c6788fd0',
    tx: {
        nonce: '0x0',
        gasPrice: '0x4a817c800',
        gas: '0x5208',
        to: '0x3535353535353535353535353535353535353535',
        value: '0xde0b6b3a7640000',
        input: '0x',
        v: '0x25',
        r: '0x4f4c17305743700648bc4f6cd3038ec6f6af0df73e31757007b7f59df7bee88d',
        s: '0x7e1941b264348e80c78c4027afc65a87b0a5e43e86742b8ca0823584c6788fd0',
        hash: '0xda3be87732110de6c1354c83770aae630ede9ac308d9f7b399ecfba23d923384'
    }
}
```

***

#### web3.platon.estimateGas

`web3.platon.estimateGas()` Executes a message call or transaction and returns the amount of the gas used.

Method:

```
web3.platon.estimateGas(callObject [, callback])
```

Parameter:

*  `callObject`：Object - A transaction object see web3.platon.sendTransaction, with the difference that for calls the from property is optional as well.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

`Promise` returns `Number` - the used gas for the simulated call/transaction.

Example:

```js
web3.platon.estimateGas({
    to: "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
})
.then(console.log);
> "0x0000000000000000000000000000000000000000000000000000000000000015"
```

***

#### web3.platon.getPastLogs

`web3.platon.getPastLogs()` Gets past logs, matching the given options.

Method:

```
web3.platon.getPastLogs(options [, callback])
```

Parameter:

*  `options`：Object - The filter options as follows:
   *  fromBlock - Number|String: The number of the earliest block ("latest" may be given to mean the most recent and "pending" currently mining, block). By default "latest".
   *  toBlock - Number|String: The number of the latest block ("latest" may be given to mean the most recent and "pending" currently mining, block). By default "latest".
   *  address - String|Array: An address or a list of addresses to only get logs from particular account(s).
   *  topics - Array: An array of values which must each appear in the log entries. The order is important, if you want to leave topics out use null, e.g. [null, '0x12...']. You can also pass an array for each topic with options for that topic e.g. [null, ['option1', 'option2']]

Returns:

`Promise` returns `Array` - Array of log objects.

The structure of the returned event `Object` in the `Array` looks as follows:

* `address` - `String`: From which this event originated from.
* `data` - `String`: The data containing non-indexed log parameter.
* `topics` - `Array`: An array with max 4 32 Byte topics, topic 1-3 contains indexed parameters of the log.
* `logIndex` - `Number`: Integer of the event index position in the block.
* `transactionIndex` - `Number`: Integer of the transaction’s index position, the event was created in.
* `transactionHash` 32 Bytes - `String`: Hash of the transaction this event was created in.
* `blockHash` 32 Bytes - `String`: Hash of the block where this event was created in. null when its still pending.
* `blockNumber` - `Number`: The block number where this log was created in. null when still pending.

Example:

```js
web3.platon.getPastLogs({
    address: "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
    topics: ["0x033456732123ffff2342342dd12342434324234234fd234fd23fd4f23d4234"]
})
.then(console.log);

> [{
    data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
},{...}]
```

***

#### web3.platon.subscribe

The web3.platon.subscribe function lets you subscribe to specific events in the blockchain.

Method:

```
web3.platon.subscribe(type [, options] [, callback]);
```

Parameter:

*  `type`：String - The subscription, you want to subscribe to.
*  `options`：Mixed - (optional) Optional additional parameters, depending on the subscription type.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second. Will be called for each incoming subscription, and the subscription itself as 3 parameter.

Returns:

EventEmitter -  A Subscription instance:

* `subscription.id`: The subscription id, used to identify and unsubscribing the subscription.
* `subscription.subscribe([callback])`: Can be used to re-subscribe with the same parameters.
* `subscription.unsubscribe([callback])`: Unsubscribes the subscription and returns TRUE in the callback if successfull.
* `subscription.arguments`: The subscription arguments, used when re-subscribing.
* `on("data")` returns Object: 	Fires on each incoming log with the log object as argument.
* `on("changed")` returns Object: Fires on each log which was removed from the blockchain. The log will have the additional property `"removed: true"`.
* `on("error")` returns Object: Fires when an error in the subscription occurs.
* `on("connected")` returns String: Fires once after the subscription successfully connected. Returns the subscription id.

Notification returns:

Mixed -  depends on the subscription, see the different subscriptions for more.

Example:

```js
var subscription = web3.platon.subscribe('logs', {
    address: '0x123456..',
    topics: ['0x12345...']
}, function(error, result){
    if (!error)
        console.log(log);
});

// unsubscribes the subscription
subscription.unsubscribe(function(error, success){
    if(success)
        console.log('Successfully unsubscribed!');
});
```

***

#### web3.platon.clearSubscriptions

`web3.platon.clearSubscriptions()` Resets subscriptions. This will not reset subscriptions from other packages like web3-shh, as they use their own requestManager.

Method:

```
web3.platon.clearSubscriptions(flag)
```

Parameters:

*  `flag`：Boolean -  值为true则表示保持同步订阅

Returns:

`Boolean`： If true it keeps the "syncing" subscription.

Example:

```js
web3.platon.subscribe('logs', {} ,function(){ ... });

...

web3.platon.clearSubscriptions();
```

***

#### web3.platon.subscribe("pendingTransactions")

`pendingTransactions` Subscribes to incoming pending transactions.

Method:

```
web3.platon.subscribe('pendingTransactions' [, callback]);
```

Parameter:

*  `type`：String - "pendingTransactions"，the type of the subscription.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second. Will be called for each incoming subscription.

Returns:

`EventEmitter`: An subscription instance as an event emitter with the following events:

*  "data" returns `String`: Fires on each incoming pending transaction and returns the transaction hash.
*  "error" returns `Object`: Fires when an error in the subscription occurs.

Notification returns:

*  Object|Null -  First parameter is an error object if the subscription failed.
*  Object - Second parameter is the transaction hash.

Example:

```js
var subscription = web3.platon.subscribe('pendingTransactions', function(error, result){
    if (!error)
        console.log(result);
})
.on("data", function(transaction){
    console.log(transaction);
});

// unsubscribes the subscription
subscription.unsubscribe(function(error, success){
    if(success)
        console.log('Successfully unsubscribed!');
});
```

***

#### web3.platon.subscribe('newBlockHeaders')

`newBlockHeaders` Subscribes to incoming block headers. This can be used as timer to check for changes on the blockchain.

Method:

```
web3.platon.subscribe('newBlockHeaders' [, callback]);
```

Parameter:

*  `type`：`String` - `"newBlockHeaders"`, the type of the subscription.
*  `callback`：`Function` -  (optional) Optional callback, returns an error object as first parameter and the result as second. Will be called for each incoming subscription.

Returns:

`EventEmitter`: An subscription instance as an event emitter with the following events:

*  `"data"`  returns `Object`: Fires on each incoming block header.
*  `"error"` returns `Object`: Fires when an error in the subscription occurs.

The structure of a returned block header is as follows:

* `number` - `Number`: The block number. null when its pending block.
* `hash` 32 Bytes - `String`: Hash of the block. null when its pending block.
* `parentHash` 32 Bytes - `String`: Hash of the parent block.
* `nonce` 8 Bytes - `String`: Hash of the generated proof-of-work. null when its pending block.
* `sha3Uncles` 32 Bytes - `String`: SHA3 of the uncles data in the block.
* `logsBloom` 256 Bytes - `String`: The bloom filter for the logs of the block. null when its pending block.
* `transactionsRoot` 32 Bytes - `String`: The root of the transaction trie of the block
* `stateRoot` 32 Bytes - `String`: The root of the final state trie of the block.
* `receiptsRoot` 32 Bytes - `String`: The root of the receipts.
* `miner` - `String`: The address of the beneficiary to whom the mining rewards were given.
* `extraData` - `String`: The “extra data” field of this block.
* `gasLimit` - `Number`: The maximum gas allowed in this block.
* `gasUsed` - `Number`: The total used gas by all transactions in this block.
* `timestamp` - `Number`: The unix timestamp for when the block was collated.


Notification returns:

* `Object|Null` - First parameter is an error object if the subscription failed.
* `Object` - The block header object like above.

Example:

```js
var subscription = web3.platon.subscribe('newBlockHeaders', function(error, result){
    if (error)
        console.log(error);
})
.on("data", function(blockHeader){
});

// unsubscribes the subscription
subscription.unsubscribe(function(error, success){
    if(success)
        console.log('Successfully unsubscribed!');
});
```

***

#### web3.platon.subscribe('syncing')

`syncing` Subscribe to syncing events. This will return an object when the node is syncing and when its finished syncing will return FALSE.

Method:

```
web3.platon.subscribe('syncing' [, callback]);
```

Parameter:

*  `type`：String - `"syncing"`, the type of the subscription.
*  `callback`：`Function` -  (optional) Optional callback, returns an error object as first parameter and the result as second. Will be called for each incoming subscription.

Returns:

`EventEmitter`: An subscription instance as an event emitter with the following events:

* `"data"`  returns `Object`: Fires on each incoming sync object as argument.
* `"changed"` returns `Object`: Fires when the synchronisation is started with true and when finished with false.
* `"error"` returns `Object`: Fires when an error in the subscription occurs.

For the structure of a returned event Object see `web3.platon.isSyncing` return values.

Notification returns:

* `Object|Null` - First parameter is an error object if the subscription failed.
* `Object|Boolean` - The syncing object, when started it will return true once or when finished it will return false once.

Example:

```js
var subscription = web3.platon.subscribe('syncing', function(error, sync){
    if (!error)
        console.log(sync);
})
.on("data", function(sync){
    // show some syncing stats
})
.on("changed", function(isSyncing){
    if(isSyncing) {
        // stop app operation
    } else {
        // regain app operation
    }
});

// unsubscribes the subscription
subscription.unsubscribe(function(error, success){
    if(success)
        console.log('Successfully unsubscribed!');
});
```

***

#### web3.platon.subscribe('logs')

Subscribes to incoming logs, filtered by the given options. If a valid numerical fromBlock options property is set, Web3 will retrieve logs beginning from this point, backfilling the response as necessary.

Method:

```
web3.platon.subscribe('logs', options [, callback]);
```

Parameter:

*  `"logs"` ：`String`, the type of the subscription.
*  `options`：`Object` - The subscription options:
   * `fromBlock` - `Number`: The number of the earliest block. By default null.
   * `address` - `String|Array`: An address or a list of addresses to only get logs from particular account(s).
   * `topics` - `Array`:  An array of values which must each appear in the log entries. The order is important, if you want to leave topics out use null, e.g. [null, '0x00...']. You can also pass another array for each topic with options for that topic e.g. [null, ['option1', 'option2']]
*  `callback` - Function:  An array of values which must each appear in the log entries. The order is important, if you want to leave topics out use null, e.g. [null, '0x00...']. You can also pass another array for each topic with options for that topic e.g. [null, ['option1', 'option2']].

Returns:

EventEmitter: An subscription instance as an event emitter with the following events:

* `"data"` returns `Object`: Fires on each incoming log with the log object as argument.
* `"changed"` 返回 `Object`: returns Object: Fires on each log which was removed from the blockchain. The log will have the additional property "removed: true".
* `"error"`  returns `Object`: Fires when an error in the subscription occurs.

For the structure of a returned event Object see web3.platon.getPastEvents return values.

Notification returns:

* `Object|Null` - First parameter is an error object if the subscription failed.
* `Object` - The log object like in web3.platon.getPastEvents return values.

Example:

```js
var subscription = web3.platon.subscribe('logs', {
    address: '0x123456..',
    topics: ['0x12345...']
}, function(error, result){
    if (!error)
        console.log(result);
})
.on("data", function(log){
    console.log(log);
})
.on("changed", function(log){
});

// unsubscribes the subscription
subscription.unsubscribe(function(error, success){
    if(success)
        console.log('Successfully unsubscribed!');
});
```

***

#### web3.platon.Contract

The `web3.platon.Contract` object makes it easy to interact with smart contracts on the PlatON blockchain. When you create a new contract object you give it the json interface of the respective smart contract and web3 will auto convert all calls into low level ABI calls over RPC for you.

This allows you to interact with smart contracts as if they were JavaScript objects.

To use it standalone:

```
new web3.platon.Contract(jsonInterface[, address][, options])
```

Parameter:

* `jsonInterface` - `Object`: The json interface for the contract to instantiate
* `address` - `String`: (optional): The address of the smart contract to call.
* `options` - `Object` : (optional): The options of the contract. Some are used as fallbacks for calls and transactions:
   * `from` - `String`: The address transactions should be made from.
   * `gasPrice` - `String`: The gas price in von to use for transactions.
   * `gas` - `Number`: The maximum gas provided for a transaction (gas limit).
   * `data` - `String`: The byte code of the contract. Used when the contract gets deployed.

Returns:

`Object`: The contract instance with all its methods and events.

Example:

```js
var myContract = new web3.platon.Contract([...], '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe', {
    from: '0x1234567890123456789012345678901234567891', // default from address
    gasPrice: '20000000000' // default gas price in wei, 20 gwei in this case
});
```

***

#### options

The options object for the contract instance. from, gas and gasPrice are used as fallback values when sending transactions.

Method:

```
myContract.options
```

`options` options:

* `address` - `String`: The address where the contract is deployed. 
* `jsonInterface` - `Array`: The json interface of the contract.
* `data` - `String`: The byte code of the contract.
* `from` - `String`: The address transactions should be made from.
* `gasPrice` - `String`: The gas price in von to use for transactions.
* `gas` - `Number`: The maximum gas provided for a transaction (gas limit).

Example:

```js
myContract.options;
> {
    address: '0x1234567890123456789012345678901234567891',
    jsonInterface: [...],
    from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe',
    gasPrice: '10000000000000',
    gas: 1000000
}

myContract.options.from = '0x1234567890123456789012345678901234567891'; // default from address
myContract.options.gasPrice = '20000000000000'; // default gas price in wei
myContract.options.gas = 5000000; // provide as fallback always 5M gas
```

***

#### options.address - contract address

The address used for this contract instance. All transactions generated by web3.js from this contract will contain this address as the “to”. The address will be stored in lowercase.

Method:

```
myContract.options.address
```

Property:

`address` - `String|null`:  The address for this contract, or null if it’s not yet set.

Example:

```js
myContract.options.address;
> '0xde0b295669a9fd93d5f28d9ec85e40f4cb697bae'

// set a new address
myContract.options.address = '0x1234FFDD...';
```

***

#### options.jsonInterface

`jsonInterface` The json interface object derived from the ABI of this contract.

Method:

```
myContract.options.jsonInterface
```

Property:

`jsonInterface` - `Array`: The json interface for this contract. Re-setting this will regenerate the methods and events of the contract instance.

Example:

```js
myContract.options.jsonInterface;
> [{
    "type":"function",
    "name":"foo",
    "inputs": [{"name":"a","type":"uint256"}],
    "outputs": [{"name":"b","type":"address"}]
},{
    "type":"event",
    "name":"Event"
    "inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"bytes32","indexed":false}],
}]

// set a new interface
myContract.options.jsonInterface = [...];
```

***

#### deploy

Call this function to deploy the contract to the blockchain. After successful deployment the promise will resolve with a new contract instance.

Method:

```
myContract.deploy(options)
```

Parameter:

`options` - Object: The options used for deployment.

* `data` - `String`: The byte code of the contract.
* `arguments` - `Array`: (optional): The arguments which get passed to the constructor on deployment.

Returns:

`Object`: The transaction object:

*  `arguments`: `Array` - The arguments passed to the method before. They can be changed.
*  `send`: `Function` - Will deploy the contract. The promise will resolve with the new contract instance, instead of the receipt!
*  `estimateGas`: `Function` - Will estimate the gas used for deploying.
*  `encodeABI`: `Function` - Encodes the ABI of the deployment, which is contract data + constructor parameters

Example:

```js
myContract.deploy({
    data: '0x12345...',
    arguments: [123, 'My String']
})
.send({
    from: '0x1234567890123456789012345678901234567891',
    gas: 1500000,
    gasPrice: '30000000000000'
}, function(error, transactionHash){ ... })
.on('error', function(error){ ... })
.on('transactionHash', function(transactionHash){ ... })
.on('receipt', function(receipt){
   console.log(receipt.contractAddress) // contains the new contract address
})
.on('confirmation', function(confirmationNumber, receipt){ ... })
.then(function(newContractInstance){
    console.log(newContractInstance.options.address) // instance with the new contract address
});


// When the data is already set as an option to the contract itself
myContract.options.data = '0x12345...';

myContract.deploy({
    arguments: [123, 'My String']
})
.send({
    from: '0x1234567890123456789012345678901234567891',
    gas: 1500000,
    gasPrice: '30000000000000'
})
.then(function(newContractInstance){
    console.log(newContractInstance.options.address) // instance with the new contract address
});


// Simply encoding
myContract.deploy({
    data: '0x12345...',
    arguments: [123, 'My String']
})
.encodeABI();
> '0x12345...0000012345678765432'


// Gas estimation
myContract.deploy({
    data: '0x12345...',
    arguments: [123, 'My String']
})
.estimateGas(function(err, gas){
    console.log(gas);
});
```

***

#### methods

Creates a transaction object for that method, which then can be called, send, estimated.

Method:

```
myContract.methods.myMethod([param1[, param2[, ...]]])
```

The methods of this smart contract are available through:

* The name: myContract.methods.myMethod(123)
* The name with parameters: myContract.methods['myMethod(uint256)'](123)
* The signature: myContract.methods['0x58cf5f10'](123)

This allows calling functions with same name but different parameters from the JavaScript contract object.

Parameter:

Parameters of any method depend on the smart contracts methods, defined in the JSON interface.

Returns:

Object: The transaction object:

*  `arguments`: Array - The arguments passed to the method before. They can be changed.
*  `call`: Function -  Will call the “constant” method and execute its smart contract method in the EVM without sending a transaction (Can’t alter the smart contract state).
*  `send`: Function - Will send a transaction to the smart contract and execute its method (Can alter the smart contract state).
*  `estimateGas`: Function - Will estimate the gas used when the method would be executed on chain.
*  `encodeABI`: Function -  Encodes the ABI for this method. This can be send using a transaction, call the method or passing into another smart contracts method as argument.

Example:

```js
// calling a method
myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, result){
    ...
});

// or sending and using a promise
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(receipt){
    // receipt can also be a new contract instance, when coming from a "contract.deploy({...}).send()"
});

// or sending and using the events
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.on('transactionHash', function(hash){
    ...
})
.on('receipt', function(receipt){
    ...
})
.on('confirmation', function(confirmationNumber, receipt){
    ...
})
.on('error', console.error);
```

***

#### methods.myMethod.call

Will call a “constant” method and execute its smart contract method in the EVM without sending any transaction. Note calling can not alter the smart contract state.

Method:

```
myContract.methods.myMethod([param1[, param2[, ...]]]).call(options[, callback])
```

Parameter:

*  `options` - Object :  (optional): The options used for calling.
   *  `from` - String (optional): The address the call “transaction” should be made from.
   *  gasPrice - String (optional): The gas price in wei to use for this call “transaction”.
   *  gas - Number (optional): The maximum gas provided for this call “transaction” (gas limit).
*  `callback` - Function : (optional): This callback will be fired with the result of the smart contract method execution as the second argument, or with an error object as the first argument.

Returns:

Promise returns Mixed: The return value(s) of the smart contract method. If it returns a single value, it’s returned as is. If it has multiple return values they are returned as an object with properties and indices:
Example:

```js
// using the callback
myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, result){
    ...
});

// using the promise
myContract.methods.myMethod(123).call({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(result){
    ...
});
```

MULTI-ARGUMENT RETURN:

```
// Solidity
contract MyContract {
    function myFunction() returns(uint256 myNumber, string myString) {
        return (23456, "Hello!%");
    }
}
```


```
// web3.js
var MyContract = new web3.platon.contract(abi, address);
MyContract.methods.myFunction().call()
.then(console.log);
> Result {
    myNumber: '23456',
    myString: 'Hello!%',
    0: '23456', // these are here as fallbacks if the name is not know or given
    1: 'Hello!%'
}
```

SINGLE-ARGUMENT RETURN:

```
// Solidity
contract MyContract {
    function myFunction() returns(string myString) {
        return "Hello!%";
    }
}
```

Then in web3.js, the `call` method will also return a single value:

```
// web3.js
var MyContract = new web3.platon.contract(abi, address);
MyContract.methods.myFunction().call()
.then(console.log);
> "Hello!%"
```

***

#### send - methods.myMethod.send

Will send a transaction to the smart contract and execute its method. Note this can alter the smart contract state.

Method:

```
myContract.methods.myMethod([param1[, param2[, ...]]]).send(options[, callback])
```

Parameter:

* `options` - `Object`: The options used for sending.
   * `from` - `String`: The address the transaction should be sent from.
   * `gasPrice` - `String`:  (optional): The gas price in von to use for this transaction.
   * `gas` - `Number`: (optional): The maximum gas provided for this transaction (gas limit).
   * `value` - `Number|String|BN|BigNumber`: (optional): The value transferred for the transaction in von.
* `callback` - `Function`: (optional): This callback will be fired first with the “transactionHash”, or with an error object as the first argument.

Returns:

The callback will return the 32 bytes transaction hash.

PromiEvent: A promise combined event emitter. Will be resolved when the transaction receipt is available, OR if this send() is called from a someContract.deploy(), then the promise will resolve with the new contract instance. Additionally the following events are available:

*  "transactionHash" returns `String`: is fired right after the transaction is sent and a transaction hash is available.
*  "receipt" returns `Object`: is fired when the transaction receipt is available. Receipts from contracts will have no logs property, but instead an events property with event names as keys and events as properties. See getPastEvents return values for details about the returned event object.
*  "confirmation" returns `Number`, Object: is fired for every confirmation up to the 24th confirmation. Receives the confirmation number as the first and the receipt as the second argument. Fired from confirmation 1 on, which is the block where it’s minded.
*  "error" returns `Error and Object|undefined`: Is fired if an error occurs during sending. If the transaction was rejected by the network with a receipt, the second parameter will be the receipt.

Example:

```js
// using the callback
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'}, function(error, transactionHash){
    ...
});

// using the promise
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(receipt){
    // receipt can also be a new contract instance, when coming from a "contract.deploy({...}).send()"
});


// using the event emitter
myContract.methods.myMethod(123).send({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.on('transactionHash', function(hash){
    ...
})
.on('confirmation', function(confirmationNumber, receipt){
    ...
})
.on('receipt', function(receipt){
    // receipt example
    console.log(receipt);
    > {
        "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
        "transactionIndex": 0,
        "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
        "blockNumber": 3,
        "contractAddress": "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe",
        "cumulativeGasUsed": 314159,
        "gasUsed": 30234,
        "events": {
            "MyEvent": {
                returnValues: {
                    myIndexedParam: 20,
                    myOtherIndexedParam: '0x123456789...',
                    myNonIndexParam: 'My String'
                },
                raw: {
                    data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
                    topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
                },
                event: 'MyEvent',
                signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
                logIndex: 0,
                transactionIndex: 0,
                transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
                blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
                blockNumber: 1234,
                address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
            },
            "MyOtherEvent": {
                ...
            },
            "MyMultipleEvent":[{...}, {...}] // If there are multiple of the same event, they will be in an array
        }
    }
})
.on('error', console.error); // If there's an out of gas error the second parameter is the receipt.
```

***

#### estimateGas - methods.myMethod.estimateGas

Will call estimate the gas a method execution will take when executed in the EVM without. The estimation can differ from the actual gas used when later sending a transaction, as the state of the smart contract can be different at that time.

Method:

```
myContract.methods.myMethod([param1[, param2[, ...]]]).estimateGas(options[, callback])
```

Parameter:

*  `options` - `Object`: (optional): The options used for calling.
   - `from` - `String`: (optional): The address the call “transaction” should be made from.
   - `gas` - `Number` : (optional): The maximum gas provided for this call “transaction” (gas limit). Setting a specific value helps to detect out of gas errors. If all gas is used it will return the same number.
   - `value` - `Number|String|BN|BigNumber`:  (optional): The value transferred for the call “transaction” in von.
   
* `callback` - `Function` : (optional): This callback will be fired with the result of the gas estimation as the second argument, or with an error object as the first argument.

Returns:

Promise returns Number: The gas amount estimated.

Example:

```js
// using the callback
myContract.methods.myMethod(123).estimateGas({gas: 5000000}, function(error, gasAmount){
    if(gasAmount == 5000000)
        console.log('Method ran out of gas');
});

// using the promise
myContract.methods.myMethod(123).estimateGas({from: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'})
.then(function(gasAmount){
    ...
})
.catch(function(error){
    ...
});
```

***

#### encodeABI - methods.myMethod.encodeABI

Encodes the ABI for this method. This can be used to send a transaction, call a method, or pass it into another smart contracts method as arguments.

Method:

```
myContract.methods.myMethod([param1[, param2[, ...]]]).encodeABI()
```

Parameter:

none

Returns:

`String`: The encoded ABI byte code to send via a transaction or call.

Example:

```js
myContract.methods.myMethod(123).encodeABI();
> '0x58cf5f1000000000000000000000000000000000000000000000000000000000000007B'
```

***

#### events

Subscribe to an event.

Method:

```
myContract.events.MyEvent([options][, callback])
```

Parameter:

* `options` - `Object` (optional): The options used for deployment：
   - `filter` - `Object` (optional): Let you filter events by indexed parameters, e.g. {filter: {myNumber: [12,13]}} means all events where “myNumber” is 12 or 13.
   - `fromBlock` - `Number` (optional): The block number (greater than or equal to) from which to get events on. Pre-defined block numbers as "latest", "earlist", "pending", and "genesis" can also be used.
   - `topics` - `Array` (optional): This allows to manually set the topics for the event filter. If given the filter property and event signature, (topic[0]) will not be set automatically.

* `callback` - `Function` (optional): This callback will be fired for each event as the second argument, or an error as the first argument.

Returns:

EventEmitter: The event emitter has the following events:

* `"data"` returns `Object`: Fires on each incoming event with the event object as argument.
* `"changed"` returns `Object`: Fires on each event which was removed from the blockchain. The event will have the additional property "removed: true".
* `"error"` returns `Object`: Fires when an error in the subscription occours.

The structure of the returned event `Object` looks as follows:

* `event` - `String`: The event name.
* `signature` - `String|Null`: The event signature, null if it’s an anonymous event.
* `address` - `String`: Address this event originated from.
* `returnValues` - `Object`: The return values coming from the event, e.g. {myVar: 1, myVar2: '0x234...'}.
* `logIndex` - `Number`: Integer of the event index position in the block.
* `transactionIndex` - `Number`: Integer of the transaction’s index position the event was created in.
* `transactionHash` 32 Bytes - `String` Hash of the transaction this event was created in.
* `blockHash` 32 Bytes - `String`: Hash of the block this event was created in. null when it’s still pending.
* `blockNumber` - `Number`: The block number this log was created in. null when still pending.
* `raw.data` - `String`: The data containing non-indexed log parameter.
* `raw.topics` - `Array`: An array with max 4 32 Byte topics, topic 1-3 contains indexed parameters of the event.

Example:

```js
myContract.events.MyEvent({
    filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'}, // Using an array means OR: e.g. 20 or 23
    fromBlock: 0
}, function(error, event){ console.log(event); })
.on('data', function(event){
    console.log(event); // same results as the optional callback above
})
.on('changed', function(event){
    // remove event from local database
})
.on('error', console.error);

// event output example
> {
    returnValues: {
        myIndexedParam: 20,
        myOtherIndexedParam: '0x123456789...',
        myNonIndexParam: 'My String'
    },
    raw: {
        data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
        topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    },
    event: 'MyEvent',
    signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
}
```

***

#### events.allEvents

Method:

```
myContract.events.allEvents([options][, callback])
```

Same as `events` but receives all events from this smart contract. Optionally the filter property can filter those events.

***

#### getPastEvents

Gets past events for this contract.

Method:

```
myContract.getPastEvents(event[, options][, callback])
```

Parameter:

* `event` - `String`: The name of the event in the contract, or "allEvents" to get all events.
* `options` - `Object` (optional): The options used for deployment.
	- `filter` - `Object` (optional): Lets you filter events by indexed parameters, e.g. {filter: {myNumber: [12,13]}} means all events where “myNumber” is 12 or 13.
	- `fromBlock` - `Number|String|BN|BigNumber` (optional): The block number (greater than or equal to) from which to get events on. Pre-defined block numbers as "latest", "earlist", "pending", and "genesis" can also be used.
	- `toBlock` - `Number|String|BN|BigNumber` (optional): The block number (less than or equal to) to get events up to (Defaults to "latest"). Pre-defined block numbers as "latest", "earlist", "pending", and "genesis" can also be used.
	- `topics` - `Array` (optional): This allows manually setting the topics for the event filter. If given the filter property and event signature, (topic[0]) will not be set automatically.
* `callback` - `Function` (optional): This callback will be fired with an array of event logs as the second argument, or an error as the first argument.

Returns:

Promise returns Array: An array with the past event Objects, matching the given event name and filter.

Example:

```js
myContract.getPastEvents('MyEvent', {
    filter: {myIndexedParam: [20,23], myOtherIndexedParam: '0x123456789...'}, // Using an array means OR: e.g. 20 or 23
    fromBlock: 0,
    toBlock: 'latest'
}, function(error, events){ console.log(events); })
.then(function(events){
    console.log(events) // same results as the optional callback above
});

> [{
    returnValues: {
        myIndexedParam: 20,
        myOtherIndexedParam: '0x123456789...',
        myNonIndexParam: 'My String'
    },
    raw: {
        data: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
        topics: ['0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7', '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385']
    },
    event: 'MyEvent',
    signature: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    logIndex: 0,
    transactionIndex: 0,
    transactionHash: '0x7f9fade1c0d57a7af66ab4ead79fade1c0d57a7af66ab4ead7c2c2eb7b11a91385',
    blockHash: '0xfd43ade1c09fade1c0d57a7af66ab4ead7c2c2eb7b11a91ffdd57a7af66ab4ead7',
    blockNumber: 1234,
    address: '0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe'
},{
    ...
}]
```

***


#### web3.platon.personal

The web3-platon-personal package allows you to interact with the PlatON node’s accounts.

Notes: Many of these functions send sensitive information, like password. Never call these functions over a unsecured Websocket or HTTP provider, as your password will be sent in plain text!

Usage: 

```
var Personal = require('web3.platon-personal');

// "Personal.providers.givenProvider" will be set if in an PlatON supported browser.
var personal = new Personal(Personal.givenProvider || 'ws://some.local-or-remote.node:8546');


// or using the web3 umbrella package

var Web3 = require('web3');
var web3 = new Web3(Web3.givenProvider || 'ws://some.local-or-remote.node:8546');

// -> web3.platon.personal
```

***

#### web3.platon.personal.newAccount

Creates a new account.

Notes: Never call this function over a unsecured Websocket or HTTP provider, as your password will be send in plain text!

Method:

```
web3.platon.personal.newAccount(password, [callback])
```

Parameter:

password - String: The password to encrypt this account with.

Returns:

Promise returns String: The address of the newly created account.

Example:

```js
web3.platon.personal.newAccount('!@superpassword')
.then(console.log);
> '0x1234567891011121314151617181920212223456'
```

***

#### web3.platon.personal.sign

The sign method calculates an Ethereum specific signature with:

Notes: Sending your account password over an unsecured HTTP RPC connection is highly unsecure.

Method:

```
web3.platon.personal.sign(dataToSign, address, password [, callback])
```

Parameter:

*  `dataToSign`：String - Data to sign. If String it will be converted using web3.utils.utf8ToHex.
*  `address`：String - Address to sign data with.
*  `password`：String - The password of the account to sign data with.
*  `callback`：Function -  (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns String - The signature.

Example:

```js
web3.platon.personal.sign("Hello world", "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe", "test password!")
.then(console.log);
> "0x30755ed65396facf86c53e6217c52b4daebe72aa4941d89635409de4c9c7f9466d4e9aaec7977f05e923889b33c0d0dd27d7226b6e6f56ce737465c5cfd04be400"

// the below is the same
web3.platon.personal.sign(web3.utils.utf8ToHex("Hello world"), "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe", "test password!")
.then(console.log);
> "0x30755ed65396facf86c53e6217c52b4daebe72aa4941d89635409de4c9c7f9466d4e9aaec7977f05e923889b33c0d0dd27d7226b6e6f56ce737465c5cfd04be400"
```

***

#### web3.platon.personal.ecRecover

Recovers the account that signed the data.

Method:

```
web3.platon.personal.ecRecover(dataThatWasSigned, signature [, callback])
```

Parameter:

* `dataThatWasSigned`：`String` -  Data that was signed. If String it will be converted using web3.utils.utf8ToHex.
* `signature`：`String` - The signature.
* `Function` - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns String - The account.

Example:

```js
web3.platon.personal.ecRecover("Hello world", "0x30755ed65396facf86c53e6217c52b4daebe72aa4941d89635409de4c9c7f9466d4e9aaec7977f05e923889b33c0d0dd27d7226b6e6f56ce737465c5cfd04be400").then(console.log);
> "0x11f4d0A3c12e86B4b5F39B213F7E19D048276DAe"
```

***

#### web3.platon.personal.signTransaction

Signs a transaction. This account needs to be unlocked.

Notes: Sending your account password over an unsecured HTTP RPC connection is highly unsecure.

Method:

```
web3.platon.personal.signTransaction(transaction, password [, callback])
```

Parameter:

*  `transaction`：Object - The transaction data to sign web3.platon.sendTransaction() for more.
*  `password`：String - The password of the from account, to sign the transaction with.
*  `callback`：Function - (optional) Optional callback, returns an error object as first parameter and the result as second.

Returns:

Promise returns Object - The RLP encoded transaction. The raw property can be used to send the transaction using web3.platon.sendSignedTransaction.

Example:

```js
web3.platon.signTransaction({
    from: "0xEB014f8c8B418Db6b45774c326A0E64C78914dC0",
    gasPrice: "20000000000",
    gas: "21000",
    to: '0x3535353535353535353535353535353535353535',
    value: "1000000000000000000",
    data: ""
}, 'MyPassword!').then(console.log);
> {
    raw: '0xf86c808504a817c800825208943535353535353535353535353535353535353535880de0b6b3a76400008025a04f4c17305743700648bc4f6cd3038ec6f6af0df73e31757007b7f59df7bee88da07e1941b264348e80c78c4027afc65a87b0a5e43e86742b8ca0823584c6788fd0',
    tx: {
        nonce: '0x0',
        gasPrice: '0x4a817c800',
        gas: '0x5208',
        to: '0x3535353535353535353535353535353535353535',
        value: '0xde0b6b3a7640000',
        input: '0x',
        v: '0x25',
        r: '0x4f4c17305743700648bc4f6cd3038ec6f6af0df73e31757007b7f59df7bee88d',
        s: '0x7e1941b264348e80c78c4027afc65a87b0a5e43e86742b8ca0823584c6788fd0',
        hash: '0xda3be87732110de6c1354c83770aae630ede9ac308d9f7b399ecfba23d923384'
    }
}
```

***

#### web3.platon.abi

The web3.platon.abi functions let you de- and encode parameters to ABI (Application Binary Interface) for function calls to the EVM (Ethereum Virtual Machine).

Function List:

*  web3.platon.abi.encodeFunctionSignature
*  web3.platon.abi.encodeEventSignature
*  web3.platon.abi.encodeParameter
*  web3.platon.abi.encodeParameters
*  web3.platon.abi.encodeFunctionCall
*  web3.platon.abi.decodeParameter
*  web3.platon.abi.decodeParameters
*  web3.platon.abi.decodeLog

***

#### web3.platon.abi.encodeFunctionSignature

Encodes the function name to its ABI signature, which are the first 4 bytes of the sha3 hash of the function name including types.

Method:

```
web3.platon.abi.encodeFunctionSignature(functionName);
```

Parameter:

`functionName` - `String|Object`: The function name to encode. or the JSON interface object of the function. If string it has to be in the form function(type,type,...), e.g: myFunction(uint256,uint32[],bytes10,bytes)

Returns:

`String` - The ABI signature of the function.

Example:

```js
// From a JSON interface object
web3.platon.abi.encodeFunctionSignature({
    name: 'myMethod',
    type: 'function',
    inputs: [{
        type: 'uint256',
        name: 'myNumber'
    },{
        type: 'string',
        name: 'myString'
    }]
})
> 0x24ee0097

// Or string
web3.platon.abi.encodeFunctionSignature('myMethod(uint256,string)')
> '0x24ee0097'
```

***

#### web3.platon.abi.encodeEventSignature

Encodes the event name to its ABI signature, which are the sha3 hash of the event name including input types.

Method:

```
web3.platon.abi.encodeEventSignature(eventName);
```

Parameter:

`eventName` - `String|Object`: The event name to encode. or the JSON interface object of the event. If string it has to be in the form event(type,type,...), e.g: myEvent(uint256,uint32[],bytes10,bytes)

Returns:

`String` - The ABI signature of the event.

Example:

```js
// use params of string.
web3.platon.abi.encodeEventSignature('myEvent(uint256,bytes32)')
> 0xf2eeb729e636a8cb783be044acf6b7b1e2c5863735b60d6daae84c366ee87d97

// or from a json interface object
web3.platon.abi.encodeEventSignature({
    name: 'myEvent',
    type: 'event',
    inputs: [{
        type: 'uint256',
        name: 'myNumber'
    },{
        type: 'bytes32',
        name: 'myBytes'
    }]
})
> 0xf2eeb729e636a8cb783be044acf6b7b1e2c5863735b60d6daae84c366ee87d97
```

***

#### web3.platon.abi.encodeFunctionCall

Encodes a function call using its JSON interface object and given paramaters.

Method:

```
web3.platon.abi.encodeFunctionCall(jsonInterface, parameters);
```

Parameter:

* `jsonInterface` - Object: The JSON interface object of a function.
* `parameters` - Array: The parameters to encode.

Returns:

*  `String` - The ABI encoded function call. Means function signature + parameters.

Example:

```js
web3.platon.abi.encodeFunctionCall({
    name: 'myMethod',
    type: 'function',
    inputs: [{
        type: 'uint256',
        name: 'myNumber'
    },{
        type: 'string',
        name: 'myString'
    }]
}, ['2345675643', 'Hello!%']);
> "0x24ee0097000000000000000000000000000000000000000000000000000000008bd02b7b0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000748656c6c6f212500000000000000000000000000000000000000000000000000"
```

***

#### web3.platon.abi.decodeParameter

Decodes an ABI encoded parameter to its JavaScript type.

Method:

```
web3.platon.abi.decodeParameter(type, hexString);
```

Parameter:

*  `type` - `String`: The type of the parameter, see the solidity documentation for a list of types.
*  `hexString` - `String`: The ABI byte code to decode.

Returns:

`Mixed` - The decoded parameter.

Example:

```js
web3.platon.abi.decodeParameter('uint256', '0x0000000000000000000000000000000000000000000000000000000000000010');
> "16"

web3.platon.abi.decodeParameter('string', '0x0000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000848656c6c6f212521000000000000000000000000000000000000000000000000');
> "Hello!%!"
```

***

#### web3.platon.abi.decodeParameters

Decodes ABI encoded parameters to its JavaScript types.

Method:

```
web3.platon.abi.decodeParameters(typesArray, hexString);
```

Parameter:

* `typesArray` - `Array|Object`: An array with types or a JSON interface outputs array. 
* `hexString` - `String`: The ABI byte code to decode.

Returns:

`Object` - The result object containing the decoded parameters.

Example:

```js
web3.platon.abi.decodeParameters(['string', 'uint256'], '0x000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000ea000000000000000000000000000000000000000000000000000000000000000848656c6c6f212521000000000000000000000000000000000000000000000000');
> Result { '0': 'Hello!%!', '1': '234' }

web3.platon.abi.decodeParameters([{
    type: 'string',
    name: 'myString'
},{
    type: 'uint256',
    name: 'myNumber'
}], '0x000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000ea000000000000000000000000000000000000000000000000000000000000000848656c6c6f212521000000000000000000000000000000000000000000000000');
> Result {
    '0': 'Hello!%!',
    '1': '234',
    myString: 'Hello!%!',
    myNumber: '234'
}
```

***

#### web3.platon.abi.decodeLog

Decodes ABI encoded log data and indexed topic data.

Method:

```
web3.platon.abi.decodeLog(inputs, hexString, topics);
```

Parameter:

*  inputs - Object: A JSON interface inputs array. See the solidity documentation for a list of types.
*  hexString - String: The ABI byte code in the data field of a log.
*  topics - Array: An array with the index parameter topics of the log, without the topic[0] if its a non-anonymous event, otherwise with topic[0].

Returns:

`Object` - The result object containing the decoded parameters.

Example:

```js
web3.platon.abi.decodeLog([{
    type: 'string',
    name: 'myString'
},{
    type: 'uint256',
    name: 'myNumber',
    indexed: true
},{
    type: 'uint8',
    name: 'mySmallNumber',
    indexed: true
}],
'0x0000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000748656c6c6f252100000000000000000000000000000000000000000000000000',
['0x000000000000000000000000000000000000000000000000000000000000f310', '0x0000000000000000000000000000000000000000000000000000000000000010']);
> Result {
    '0': 'Hello%!',
    '1': '62224',
    '2': '16',
    myString: 'Hello%!',
    myNumber: '62224',
    mySmallNumber: '16'
}
```

***

#### web3.utils

This package provides utility functions for PlatON dapps and other web3.js packages

Method:

```js
Web3.utils
web3.utils
```

***

#### web3.utils.randomHex

The randomHex library to generate cryptographically strong pseudo-random HEX strings from a given byte size.

Method:

```
web3.utils.randomHex(size)
```

Parameter:

* `size` - `Number`: The byte size for the HEX string, e.g. 32 will result in a 32 bytes HEX string with 64 characters preficed with “0x”.

Returns:

`String`: The generated random HEX string.

Example:

```js
web3.utils.randomHex(32)
> "0xa5b9d60f32436310afebcfda832817a68921beb782fabf7915cc0460b443116a"

web3.utils.randomHex(4)
> "0x6892ffc6"

web3.utils.randomHex(2)
> "0x99d6"

web3.utils.randomHex(1)
> "0x9a"

web3.utils.randomHex(0)
> "0x"
```

***

#### web3.utils._

The underscore library for many convenience JavaScript functions.

See the [underscore API](http://underscorejs.org/) reference for details.

Method:

```
web3.utils._
```

Example:

```js
var _ = web3.utils._;

_.union([1,2],[3]);
> [1,2,3]

_.each({my: 'object'}, function(value, key){ ... })
```

***

#### web3.utils.BN

The [BN.js](https://github.com/indutny/bn.js/) library for calculating with big numbers in JavaScript. See the [BN.js](https://github.com/indutny/bn.js/) documentation for details.

Notes: For safe conversion of many types, incl [BigNumber.js](http://mikemcl.github.io/bignumber.js/) use [utils.toBN](https://web3js.readthedocs.io/en/v1.2.6/web3-utils.html#utils-tobn)

Method:

```
web3.utils.BN(mixed)
```

Parameter:

*  `mixed` - `String|Number`: A number, number string or HEX string to convert to a BN object.

Returns:

`Object`: The [BN.js](https://github.com/indutny/bn.js/) instance.

Example:

```
var BN = web3.utils.BN;

new BN(1234).toString();
> "1234"

new BN('1234').add(new BN('1')).toString();
> "1235"

new BN('0xea').toString();
> "234"
```

***

#### web3.utils.isBN

`web3.utils.isBN()` Checks if a given value is a [BN.js](https://github.com/indutny/bn.js/) instance.

Method:

```
web3.utils.isBN(bn)
```

Parameter:

`bn` - `Object`: An [BN.js](https://github.com/indutny/bn.js/) instance.

Returns:

`Boolean`

Example:

```
var number = new BN(10);

web3.utils.isBN(number);
> true
```

***

#### web3.utils.isBigNumber

Checks if a given value is a [BigNumber.js](http://mikemcl.github.io/bignumber.js/) instance.

Method:

```
web3.utils.isBigNumber(bignumber)
```

Parameter:

`bignumber` - `Object`: A [BigNumber.js](http://mikemcl.github.io/bignumber.js/) instance.

Returns:

`Boolean`

Example:

```
var number = new BigNumber(10);

web3.utils.isBigNumber(number);
> true
```

***

#### web3.utils.sha3

Will calculate the sha3 of the input.

Notes: To mimic the sha3 behaviour of solidity use [soliditySha3](https://web3js.readthedocs.io/en/v1.2.6/web3-utils.html#utils-soliditysha3).

Method:

```
web3.utils.sha3(string)
web3.utils.keccak256(string) // ALIAS
```

Parameter:

`string` - String: A string to hash.

Returns:

`String`:  the result hash.

Example:

```
web3.utils.sha3('234'); // taken as string
> "0xc1912fee45d61c87cc5ea59dae311904cd86b84fee17cc96966216f811ce6a79"

web3.utils.sha3(new BN('234')); 
> "0xbc36789e7a1e281436464229828f817d6612f7b477d66591ff96a9e064bcc98a"

web3.utils.sha3(234); 
> null // can't calculate the has of a number

web3.utils.sha3(0xea); // same as above, just the HEX representation of the number
> null

web3.utils.sha3('0xea'); // will be converted to a byte array first, and then hashed
> "0x2f20677459120677484f7104c76deb6846a2c071f9b3152c103bb12cd54d1a4a"
```

***

#### web3.utils.soliditySha3

Will calculate the sha3 of given input parameters in the same way solidity would. This means arguments will be ABI converted and tightly packed before being hashed.

Method:

```
web3.utils.soliditySha3(param1 [, param2, ...])
```

Parameter:

`paramX` - `Mixed`: Any type, or an object with `{type: 'uint', value: '123456'}` or `{t: 'bytes', v: '0xfff456'}`. Basic types are autodetected as follows:

* `String` non numerical UTF-8 string is interpreted as `string`.
* `String|Number|BN|HEX` positive number is interpreted as `uint256`.
* `String|Number|BN` negative number is interpreted as `int256`.
* `Boolean` as `bool`.
* `String` HEX string with leading 0x is interpreted as `bytes`.
* `HEX` HEX number representation is interpreted as `uint256`.

Returns:

`String`: the result hash.

Example:

```js
web3.utils.soliditySha3('234564535', '0xfff23243', true, -10);
// auto detects:        uint256,      bytes,     bool,   int256
> "0x3e27a893dc40ef8a7f0841d96639de2f58a132be5ae466d40087a2cfa83b7179"


web3.utils.soliditySha3('Hello!%'); // auto detects: string
> "0x661136a4267dba9ccdf6bfddb7c00e714de936674c4bdb065a531cf1cb15c7fc"


web3.utils.soliditySha3('234'); // auto detects: uint256
> "0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

web3.utils.soliditySha3(0xea); // same as above
> "0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

web3.utils.soliditySha3(new BN('234')); // same as above
> "0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

web3.utils.soliditySha3({type: 'uint256', value: '234'})); // same as above
> "0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"

web3.utils.soliditySha3({t: 'uint', v: new BN('234')})); // same as above
> "0x61c831beab28d67d1bb40b5ae1a11e2757fa842f031a2d0bc94a7867bc5d26c2"


web3.utils.soliditySha3('0x407D73d8a49eeb85D32Cf465507dd71d507100c1');
> "0x4e8ebbefa452077428f93c9520d3edd60594ff452a29ac7d2ccc11d47f3ab95b"

web3.utils.soliditySha3({t: 'bytes', v: '0x407D73d8a49eeb85D32Cf465507dd71d507100c1'});
> "0x4e8ebbefa452077428f93c9520d3edd60594ff452a29ac7d2ccc11d47f3ab95b" // same result as above


web3.utils.soliditySha3({t: 'address', v: '0x407D73d8a49eeb85D32Cf465507dd71d507100c1'});
> "0x4e8ebbefa452077428f93c9520d3edd60594ff452a29ac7d2ccc11d47f3ab95b" //same as above, but will do a checksum check, if its multi case


web3.utils.soliditySha3({t: 'bytes32', v: '0x407D73d8a49eeb85D32Cf465507dd71d507100c1'});
> "0x3c69a194aaf415ba5d6afca734660d0a3d45acdc05d54cd1ca89a8988e7625b4" // different result as above


web3.utils.soliditySha3({t: 'string', v: 'Hello!%'}, {t: 'int8', v:-23}, {t: 'address', v: '0x85F43D8a49eeB85d32Cf465507DD71d507100C1d'});
> "0xa13b31627c1ed7aaded5aecec71baf02fe123797fffd45e662eac8e06fbe4955"
```

***

#### web3.utils.isHex

Checks if a given string is a HEX string.

Method:

```
web3.utils.isHex(hex)
```

Parameter:

`hex` - `String|HEX`: The given HEX string.

Returns:

`Boolean`：Returns true if the argument is a hexadecimal string, otherwise returns false.

Example:

```js
web3.utils.isHex('0xc1912');
> true

web3.utils.isHex(0xc1912);
> true

web3.utils.isHex('c1912');
> true

web3.utils.isHex(345);
> true  // this is tricky, as 345 can be a a HEX representation or a number, be careful when not having a 0x in front!

web3.utils.isHex('0xZ1912');
> false

web3.utils.isHex('Hello');
> false
```

***

#### web3.utils.isHexStrict

Checks if a given string is a HEX string. Difference to `web3.utils.isHex()` is that it expects HEX to be prefixed with `0x`.

Method:

```
web3.utils.isHexStrict(hex)
```

Parameter:

`hex` - `String|HEX`: The given HEX string.

Returns:

`Boolean`

Example:

```js
web3.utils.isHexStrict('0xc1912');
> true

web3.utils.isHexStrict(0xc1912);
> false

web3.utils.isHexStrict('c1912');
> false

web3.utils.isHexStrict(345);
> false 

web3.utils.isHexStrict('0xZ1912');
> false

web3.utils.isHex('Hello');
> false
```

***

#### web3.utils.isAddress

Checks if a given string is a valid PlatON address. It will also check the checksum, if the address has upper and lowercase letters.

Method:

```
web3.utils.isAddress(address)
```

Parameter:

`address` - `String`: An address string.

Returns:

`Boolean`

Example:

```js
web3.utils.isAddress('0xc1912fee45d61c87cc5ea59dae31190fffff232d');
> true

web3.utils.isAddress('c1912fee45d61c87cc5ea59dae31190fffff232d');
> true

web3.utils.isAddress('0XC1912FEE45D61C87CC5EA59DAE31190FFFFF232D');
> true // as all is uppercase, no checksum will be checked

web3.utils.isAddress('0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
> true

web3.utils.isAddress('0xC1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
> false // wrong checksum
```

***

#### web3.utils.toChecksumAddress

Will convert an upper or lowercase Ethereum address to a checksum address.

Method:

```
web3.utils.toChecksumAddress(address)
```

Parameter:

`address` - `String`: An address string.

Returns:

`String`: The checksum address.

Example:

```js
web3.utils.toChecksumAddress('0xc1912fee45d61c87cc5ea59dae31190fffff2323');
> "0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d"

web3.utils.toChecksumAddress('0XC1912FEE45D61C87CC5EA59DAE31190FFFFF232D');
> "0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d" // same as above
```

***

#### web3.utils.checkAddressChecksum

Checks the checksum of a given address. Will also return false on non-checksum addresses.

Method:

```
web3.utils.checkAddressChecksum(address)
```

Parameter:

`address` - `String`: An address string.

Returns:

`Boolean`: `true` when the checksum of the address is valid, `false` if its not a checksum address, or the checksum is invalid.

Example:

```js
web3.utils.checkAddressChecksum('0xc1912fEE45d61C87Cc5EA59DaE31190FFFFf232d');
> true
```

***

#### web3.utils.toHex

Will auto convert any given value to HEX. Number strings will interpreted as numbers. Text strings will be interpreted as UTF-8 strings.

Method:

```
web3.utils.toHex(mixed)
```

Parameter:

`mixed` - `String|Number|BN|BigNumber`: The input to convert to HEX.

Returns:

String: The resulting HEX string.

Example:

```js
web3.utils.toHex('234');
> "0xea"

web3.utils.toHex(234);
> "0xea"

web3.utils.toHex(new BN('234'));
> "0xea"

web3.utils.toHex(new BigNumber('234'));
> "0xea"

web3.utils.toHex('I have 100€');
> "0x49206861766520313030e282ac"
```

***

#### web3.utils.hexToNumberString

Returns the number representation of a given HEX value as a string.

Method:

```
web3.utils.hexToNumberString(hex)
```

Parameter:

`hexString` - `String|HEX`: A string to hash.

Returns:

`String`: The number as a string.

Example:

```js
web3.utils.hexToNumberString('0xea');
> "234"
```

***

#### web3.utils.hexToNumber

Returns the number representation of a given HEX value.

This is not useful for big numbers, rather use [utils.toBN](https://web3js.readthedocs.io/en/v1.2.6/web3-utils.html#utils-tobn) instead.

Method:

```
web3.utils.hexToNumber(hex)
web3.utils.toDecimal(hex) // ALIAS, deprecated
```

Parameter:

`hexString` - `String|HEX`: A string to hash.

Returns:

`Number`

Example:

```js
web3.utils.hexToNumber('0xea');
> 234
```

***

#### web3.utils.numberToHex

Returns the HEX representation of a given number value.

Method:

```
web3.utils.numberToHex(number)
web3.utils.fromDecimal(number) // ALIAS, deprecated
```

Parameter:

`number` - `String|Number|BN|BigNumber`: A number as string or number.

Returns:

`String`: The HEX value of the given number.

Example:

```js
web3.utils.numberToHex('234');
> '0xea'
```

***

#### web3.utils.hexToUtf8

Returns the UTF-8 string representation of a given HEX value.

Method:

```
web3.utils.hexToUtf8(hex)
web3.utils.hexToString(hex) // ALIAS
web3.utils.toUtf8(hex) // ALIAS, deprecated
```

Parameter:

`hex` - `String`: A HEX string to convert to a UTF-8 string.

Returns:

`String`: The UTF-8 string.

Example:

```js
web3.utils.hexToUtf8('0x49206861766520313030e282ac');
> "I have 100€"
```

***

#### web3.utils.hexToAscii

Returns the ASCII string representation of a given HEX value.

Method:

```
web3.utils.hexToAscii(hex)
web3.utils.toAscii(hex) // ALIAS, deprecated
```

Parameter:

`hex` - `String`:  A HEX string to convert to a ASCII string.

Returns:

`String`: The ASCII string.

Example:

```js
web3.utils.hexToAscii('0x4920686176652031303021');
> "I have 100!"
```

***

#### web3.utils.utf8ToHex

Returns the HEX representation of a given UTF-8 string.

Method:

```
web3.utils.utf8ToHex(string)
web3.utils.stringToHex(string) // ALIAS
web3.utils.fromUtf8(string) // ALIAS, deprecated
```

Parameter:

`string` - `String`: A UTF-8 string to convert to a HEX string.

Returns:

`String`: The HEX string.

Example:

```js
web3.utils.utf8ToHex('I have 100€');
> "0x49206861766520313030e282ac"
```

***

#### web3.utils.asciiToHex

Returns the HEX representation of a given ASCII string.

Method:

```
web3.utils.asciiToHex(string)
web3.utils.fromAscii(string) // ALIAS, deprecated
```

Parameter:

`string` - `String`: A ASCII string to convert to a HEX string.

返回值

`String`: The HEX string.

Example:

```js
web3.utils.asciiToHex('I have 100!');
> "0x4920686176652031303021"
```

***

#### web3.utils.hexToBytes

Returns a byte array from the given HEX string.

Method:

```
web3.utils.hexToBytes(hex)
```

Parameter:

`hex` - `String|HEX`: A HEX to convert.

Returns:

`Array`: The byte array.

Example:

```js
web3.utils.hexToBytes('0x000000ea');
> [ 0, 0, 0, 234 ]

web3.utils.hexToBytes(0x000000ea);
> [ 234 ]
```

***

#### web3.utils.toVon

Converts any lat value value into von.

* von
* kvon
* mvon
* gvon
* microlat
* millilat
* lat
* klat
* mlat
* glat
* tlat

```
'von':          '1',
'kvon':         '1000',
'mvon':         '1000000',
'gvon':         '1000000000',
'microlat':     '1000000000000',
'millilat':     '1000000000000000',
'lat':          '1000000000000000000',
'klat':         '1000000000000000000000',
'mlat':         '1000000000000000000000000',
'glat':         '1000000000000000000000000000',
'tlat':         '1000000000000000000000000000000'
```

Method:

```
web3.utils.toVon(number [, unit])
```

Parameter:

*  `number` - `String|Number|BN`: The value. 
*  `unit` - String，(optional, defaults to "lat"): The ether to convert from. 


Returns:

`String|BN`: If a string is given it returns a number string, otherwise a [BN.js](https://github.com/indutny/bn.js/) instance.

Example:

```js
web3.utils.toVon('1', 'lat');
> "1000000000000000000"
```

***

#### web3.utils.fromVon

Converts any von value into a lat value.

Method:

```
web3.utils.fromVon(number [, unit])
```

Converts any von value into a lat value.

Parameter:

`number` - `String|Number|BN`: The value in von.
`unit` - `String`，(optional, defaults to "lat"): The ether to convert to. Possible units are:

* von
* kvon
* mvon
* gvon
* microlat
* millilat
* lat
* klat
* mlat
* glat
* tlat

Returns:

`String|BN`: It always returns a string number.

Example:

```js
web3.utils.fromVon('1', 'lat');
> "0.000000000000000001"
```

***

#### web3.utils.padLeft

Adds a padding on the left of a string, Useful for adding paddings to HEX strings.

Method:

```
web3.utils.padLeft(string, characterAmount [, sign])
web3.utils.leftPad(string, characterAmount [, sign]) // ALIAS
```

Parameter:

*  `string` - `String`: The string to add padding on the left.
*  `characterAmount` - `Number`: The number of characters the total string should have.
*  `sign` - `String`(optional): The character sign to use, defaults to `"0"`.

Returns:

`String`:  The padded string.

Example:

```js
web3.utils.padLeft('0x3456ff', 20);
> "0x000000000000003456ff"

web3.utils.padLeft(0x3456ff, 20);
> "0x000000000000003456ff"

web3.utils.padLeft('Hello', 20, 'x');
> "xxxxxxxxxxxxxxxHello"
```

***

## 内置合约调用指南

### 概述
通过对调用对象 ppos（经济模型相关内置合约） 的 call 或者 send 函数将传进来的参数转换为 rpc 接口 platon_call 或者 platon_sendRawTransaction 调用所需要的参数，然后将交易发送至链上。以及完成 call 与 send 调用入参需要的一些辅助函数。

### 简要使用
在调用`const web3 = new Web3('http://127.0.0.1:6789');`实例化一个web3的时候，系统会自动在 web3 后面附加一个 ppos 对象。也就是说你可以直接使用web3.ppos 调用 ppos 有的一些方法。但是如果要使用ppos对象发送上链的交易，那么除了在实例化`web3`的时候传进去的 provider，还至少需要发送交易签名需要的私钥以及链id，其中的链id可通过rpc接口`admin_nodeInfo`返回的`'chainId': xxx`获取。

当然，为了满足在能任意实例多个ppos(比如我要实例3个ppos给不同的链上同时发送交易调用)，我还会在web3对象上附上一个PPOS对象(注意全部是大写)。你可以调用`new PPOS(setting)`实例化一个ppos对象。一个调用示例如下：

```JavaScript
(async () => {
    const Web3 = require('web3');
    const web3 = new Web3('http://192.168.120.164:6789');
    const ppos = web3.ppos; // 后面例子我都以 ppos 为对象。就不写成 web3.ppos 了。

    // 更新 ppos 的配置，发送上链交易必须要做这一步
    // 由于在实例化web3的时候已传入了 provider, 可以不传入provider了。
    ppos.updateSetting({
        privateKey: 'acc73b693b79bbb56f89f63ccc3a0c00bf1b8380111965bfe8ab22e32045600c',
        chainId: 100,
    })

    let data, reply;

    // 传参以对象形式发送交易： 1000. createStaking() : 发起质押
    const benefitAddress = '0xe6F2ce1aaF9EBf2fE3fbA8763bABaDf25e3fb5FA';
    const nodeId = '80f1fcee54de74dbf7587450f31c31c0e057bedd4faaa2a10c179d52c900ca01f0fb255a630c49d83b39f970d175c42b12a341a37504be248d76ecf592d32bc0';
    const amount = '10000000000000000000000000000';
    const blsPubKey = 'd2459db974f49ca9cbf944d4d04c2d17888aef90858b62d6aec166341a6e886e8c0c0cfae9e469c2f618f5d9b7a249130d10047899da6154288c9cde07b576acacd75fef07ba0cfeb4eaa7510704e77a9007eff5f1a5f8d099e6ea664129780c';
    data = {
        funcType: 1000,
        typ: 0,
        benefitAddress: ppos.hexStrBuf(benefitAddress),
        nodeId: ppos.hexStrBuf(nodeId),
        externalId: 'externalId',
        nodeName: 'Me',
        website: 'www.platon.network',
        details: 'staking',
        amount: ppos.bigNumBuf(amount),
        programVersion: undefined, // rpc 获取
        programVersionSign: undefined, // rpc 获取
        blsPubKey: ppos.hexStrBuf(blsPubKey),
        blsProof: undefined, // rpc 获取
    }
    let pv = await ppos.rpc('admin_getProgramVersion');
    let blsProof = await ppos.rpc('admin_getSchnorrNIZKProve');
    data.programVersion = pv.Version;
    data.programVersionSign = pv.Sign;
    data.blsProof = ppos.hexStrBuf(blsProof);
    reply = await ppos.send(data);
    console.log('createStaking params object reply: ', JSON.stringify(reply, null, 2));

    // 传参以数组形式发送交易： 1000. createStaking() : 发起质押
    data = [
        1000,
        0,
        ppos.hexStrBuf(benefitAddress),
        ppos.hexStrBuf(nodeId),
        'externalId',
        'Me',
        'www.platon.network',
        'staking',
        ppos.bigNumBuf(amount),
        pv.Version,
        pv.Sign,
        ppos.hexStrBuf(blsPubKey),
        ppos.hexStrBuf(blsProof)
    ];
    // 由于上面已调用过，交易会上链，但是业务会失败
    reply = await ppos.send(data);
    console.log('createStaking params array reply: ', reply);

    // 传参以对象形式调用： 1102. getCandidateList() : 查询所有实时的候选人列表
    data = {
        funcType: 1102,
    }
    reply = await ppos.call(data);
    console.log('getCandidateList params object reply: ', reply);

    // 传参以数组形式调用： 1102. getCandidateList() : 查询所有实时的候选人列表
    data = [1102];
    reply = await ppos.call(data);
    console.log('getCandidateList params array reply: ', reply);

    // 重新实例化一个ppos1对象出来调用
    const ppos1 = new web3.PPOS({
        provider: 'http://127.0.0.1:6789',
        privateKey: '9f9b18c72f8e5154a9c59af2a35f73d1bdad37b049387fc6cea2bac89804293b',
        chainId: 100,
    })
    reply = await ppos1.call(data);
})()
```

日志信息输出如下。为了节省篇幅，有删减

```
createStaking params object reply:  {
  "blockHash": "0xdddd6b12919b69169b63d17fece52e8632fe3d8b48166c8b4ef8fdee39a1f35c",
  "blockNumber": "0xb",
  "contractAddress": null,
  "cumulativeGasUsed": "0x14f34",
  "from": "0x714de266a0effa39fcaca1442b927e5f1053eaa3",
  "gasUsed": "0x14f34",
  "logs": [
    {
      "address": "0x1000000000000000000000000000000000000002",
      "topics": [
        "0xd63087bea9f1800eed943829fc1d61e7869764805baa3259078c1caf3d4f5a48"
      ],
      "data": "0xe3a27b22436f6465223a302c2244617461223a22222c224572724d7367223a226f6b227d",
      "blockNumber": "0xb",
      "transactionHash": "0x4bee71e351076a81482e2576e469a8dfaa76da9b6cc848265c10968d6de67364",
      "transactionIndex": "0x0",
      "blockHash": "0xdddd6b12919b69169b63d17fece52e8632fe3d8b48166c8b4ef8fdee39a1f35c",
      "logIndex": "0x0",
      "removed": false,
      "dataStr": {
        "Code": 0,
        "Data": "",
        "ErrMsg": "ok"
      }
    }
  ],
  "logsBloom": "",
  "root": "0x3b7a41cea97f90196039586a3068f6a64c09aa7597898440c3c241a095e37984",
  "to": "0x1000000000000000000000000000000000000002",
  "transactionHash": "0x4bee71e351076a81482e2576e469a8dfaa76da9b6cc848265c10968d6de67364",
  "transactionIndex": "0x0"
}

createStaking params array reply:  { blockHash:
   '0x43351e4a9f1b7173552094bacfd5b6f84f18a6c7c0c02d8a10506e3a61041117',
  blockNumber: '0x10',
  contractAddress: null,
  cumulativeGasUsed: '0x14f34',
  from: '0x714de266a0effa39fcaca1442b927e5f1053eaa3',
  gasUsed: '0x14f34',
  logs:
   [ { address: '0x1000000000000000000000000000000000000002',
       topics: [Array],
       data:
        '0xf846b8447b22436f6465223a3330313130312c2244617461223a22222c224572724d7367223a22546869732063616e64696461746520697320616c7265616479206578697374227d',
       blockNumber: '0x10',
       transactionHash:
        '0xe5cbc728d6e284464c30ce6f0bbee5fb2b30351a591424f3a0edd37cc1bbdc05',
       transactionIndex: '0x0',
       blockHash:
        '0x43351e4a9f1b7173552094bacfd5b6f84f18a6c7c0c02d8a10506e3a61041117',
       logIndex: '0x0',
       removed: false,
       dataStr: [Object] } ],
  logsBloom:'',
  root:
   '0x45ffeda340b68a0d54c5556a51f925b0787307eab1fb120ed141fd8ba81183d4',
  to: '0x1000000000000000000000000000000000000002',
  transactionHash:
   '0xe5cbc728d6e284464c30ce6f0bbee5fb2b30351a591424f3a0edd37cc1bbdc05',
  transactionIndex: '0x0' }

getCandidateList params object reply:  { 
  Code: 0,
  Data:
   [ { candidate1 info... },
     { candidate2 info... },
     { candidate3 info... },
     { candidate4 info... } 
   ],
  ErrMsg: 'ok' }

getCandidateList params array reply:  { 
  Code: 0,
  Data:
   [ { candidate1 info... },
     { candidate2 info... },
     { candidate3 info... },
     { candidate4 info... } 
   ],
  ErrMsg: 'ok' }
```

### API 调用详细说明

#### `updateSetting(setting)`
更新 ppos 对象的配置参数。如果你只需要发送call调用，那么只需要传入 provider 即可。如果你在实例化 web3 的时候已经传入了 provider。那么会ppos的provider默认就是你实例化web3传进来的provider。当然你也可以随时更新provider。

如果你要发送send交易，那么除了provider，还必须要传入发送交易所需要的私钥以及链id。当然，发送交易需要设置的gas, gasPrice, retry, interval这四个参数详细请见`async send(params, [other])`说明。

对传入的参数，你可以选择部分更新，比如你对一个ppos对象，发送某个交易时想使用私钥A，那么你在调用`send(params, [other])`之前执行 `ppos.updateSetting({privateKey: youPrivateKeyA})`更新私钥即可。一旦更新之后，将会覆盖当前配置，后面调用发送交易接口，将默认以最后一次更新的配置。

入参说明：
* setting Object
  * provider String 链接
  * privateKey String 私钥
  * chainId String 链id
  * gas String 燃料最大消耗，请输入十六进制字符串，比如 '0x76c0000'
  * gasPrice String 燃料价格，请输入十六进制字符串，比如 '0x9184e72a000000'
  * retry Number 查询交易收据对象次数。
  * interval Number 查询交易收据对象的间隔，单位为ms。

无出参。

调用示例
```JavaScript
// 同时更新 privateKey，chainId
ppos.updateSetting({
    privateKey: 'acc73b693b79bbb56f89f63ccc3a0c00bf1b8380111965bfe8ab22e32045600c',
    chainId: 100,
})

// 只更新 privateKey
ppos.updateSetting({
    privateKey: '9f9b18c72f8e5154a9c59af2a35f73d1bdad37b049387fc6cea2bac89804293b'
})
```

***

#### `getSetting()`
查询你配置的参数

无入参

出参
* setting Object
  * provider String 链接
  * privateKey String 私钥
  * chainId String 链id
  * gas String 燃料最大消耗
  * gasPrice String 燃料价格
  * retry Number 查询交易收据对象次数。
  * interval Number 查询交易收据对象的间隔，单位为ms。

调用示例
```JavaScript
let setting = ppos.getSetting();
```

***

#### `async rpc(method, [params])`
发起 rpc 请求。一个辅助函数，因为在调用ppos发送交易的过程中，有些参数需要通过rpc来获取，所以特意封装了一个rpc供调用。注意此接口为async函数，需要加await返回调用结果，否则返回一个Promise对象。

入参说明：
* method String 方法名
* params Array 调用rpc接口需要的参数，如果调用此rpc端口不需要参数，则此参数可以省略。
  

出参
* reply rpc调用返回的结果

调用示例
```JavaScript
// 获取程序版本
let reply = await ppos.rpc('admin_getProgramVersion'); 

// 获取所有账号
let reply = await ppos.rpc('platon_accounts')

// 获取一个账号的金额
let reply = await ppos.rpc('platon_getBalance', ["0x714de266a0effa39fcaca1442b927e5f1053eaa3","latest"])
```

***

#### `bigNumBuf(intStr)`
将一个字符串的十进制大整数转为一个RLP编码能接受的buffer对象。一个辅助函数。因为JavaScript的正数范围只能最大表示为2^53，为了RLP能对大整数进行编码，需要将字符串的十进制大整数转换为相应的Buffer。注意，此接口暂时只能对十进制的大整数转为Buffer，如果是十六进制的字符串，您需要先将他转为十进制的字符串。

入参说明：
* intStr String 字符串十进制大整数。
  

出参
* buffer Buffer 一个缓存区。

调用示例
```JavaScript
let buffer = ppos.bigNumBuf('1000000000000000000000000000000000000000000'); 
```

***

#### `hexStrBuf(hexStr)`
将一个十六进制的字符串转为一个RLP编码能接受的buffer对象。一个辅助函数。在ppos发送交易的过程中，我们很多参数需要作为bytes传送而不是string，比如 `nodeId 64bytes 被质押的节点Id(也叫候选人的节点Id)`。而写代码时候的nodeId只能以字符串的形式表现。需要将他转为一个 64 bytes 的 Buffer。

注意：如果你传进去的字符串以 0x 或者 0X 开头，系统会默认为你是表示一个十六进制字符串不对开头的这两个字母进行编码。如果你确实要对 0x 或者 0X 编码，那你必须在字符串前面再加前缀 0x。比如，你要对全字符串 0x31c0e0 (4 bytes) 进行编码，那么必须传入 0x0x31c0e0 。

入参说明：
* hexStr String 一个十六进制的字符串。
  

出参
* buffer Buffer 一个缓存区。

调用示例
```JavaScript
const nodeId = '80f1fcee54de74dbf7587450f31c31c0e057bedd4faaa2a10c179d52c900ca01f0fb255a630c49d83b39f970d175c42b12a341a37504be248d76ecf592d32bc0';
let buffer = ppos.hexStrBuf(nodeId); 
```

***

#### `async call(params)`
发送一个 ppos 的call查询调用。不上链。所以你需要自行区分是否是查询或者是发送交易。入参可以选择对象或者数组。如果你选择传入对象，那么你需要使用规定的字符串key，但是对key要求不做顺序。你可以这样写`{a: 1, b: 'hello'}` 或者 `{b: 'hello', a: 1}`都没问题。

如果你选择以数组作为入参，那么你**必须严格按照入参的顺序依次将参数放到数组里面**。注意，对一些字符串大整数以及需要传入的bytes，请选择上面提供的接口`bigNumBuf(intStr)`跟`hexStrBuf(hexStr)`自行进行转换再传入。

注意此接口为async函数，需要加await返回调用结果，否则返回一个Promise对象。

入参说明：
* params Object | Array 调用参数。
  

出参
* reply Object call调用的返回的结果。注意，我已将将返回的结果转为了Object对象。
  * Code Number 调用返回码，0表示调用结果正常。
  * Data Array | Object | String | Number... 根据调用结果返回相应类型
  * ErrMsg String 调用提示信息。

以调用 `查询当前账户地址所委托的节点的NodeID和质押Id`这个接口，入参顺序从上到下，入参如下所示：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1103)|
|addr|common.address(20bytes)|委托人的账户地址|

调用示例
```JavaScript
let params, reply;

// 以传进入对象进行调用(对于key不要求顺序)
params = {
    funcType: 1103,
    addr: ppos.hexStrBuf("0xe6F2ce1aaF9EBf2fE3fbA8763bABaDf25e3fb5FA")
}
reply = await ppos.call(params);

// 以传入数组对象进行调用
params = [1103, ppos.hexStrBuf("0xe6F2ce1aaF9EBf2fE3fbA8763bABaDf25e3fb5FA")];
reply = await ppos.call(params);
```

***

#### `async send(params, [other])`
发送一个 ppos 的send发送交易调用。上链。所以你需要自行区分是否是查询或者是发送交易。入参可以选择对象或者数组。传入规则请看上述`async call(params)`调用。

由于是一个交易，将会涉及到调用交易需要的一些参数，比如gas，gasPrice。当交易发送出去之后，为了确认交易是否上链，需要不断的通过交易哈希去轮询链上的结果。这就有个轮询次数 retry 与每次轮询之间的间隔 interval。

对于上面提到的 gas, gasPrice, retry, interval 这四个参数，如果other入参有指定，则使用other指定的。如果other入参未指定，则使用调用函数时候`updateSetting(setting)`指定的参数，否则使用默认的数值。

注意此接口为async函数，需要加await返回调用结果，否则返回一个Promise对象。

入参说明：
* params Object|Array 调用参数。
* other Object 其他参数
  * gas String 燃油限制，默认 '0x76c0000'。
  * gasPrice String 燃油价格，默认 '0x9184e72a000000'。
  * retry Number 查询交易收据对象次数，默认 600 次。
  * interval Number 查询交易收据对象的间隔，单位为ms。默认 100 ms。

出参
* reply Object 调用成功！send调用方法返回指定交易的收据对象
  * status - Boolean: 成功的交易返回true，如果EVM回滚了该交易则返回false
  * blockHash 32 Bytes - String: 交易所在块的哈希值
  * blockNumber - Number: 交易所在块的编号
  * transactionHash 32 Bytes - String: 交易的哈希值
  * transactionIndex - Number: 交易在块中的索引位置
  * from - String: 交易发送方的地址
  * to - String: 交易接收方的地址，对于创建合约的交易，该值为null
  * contractAddress - String: 对于创建合约的交易，该值为创建的合约地址，否则为null
  * cumulativeGasUsed - Number: 该交易执行时所在块的gas累计总用量
  * gasUsed- Number: 该交易的gas总量
  * logs - Array: 该交易产生的日志对象数组

* errMsg String 调用失败！如果发送交易返回之后没有回执，则返回错误信息`no hash`。如果发送交易之后有回执，但是在规定的时间内没有查到收据对象，则返回 `getTransactionReceipt txHash ${hash} interval ${interval}ms by ${retry} retry failed`

以调用 `发起委托`这个接口，入参顺序从上到下，入参如下所示：

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1004)|
|typ|uint16(2bytes)|表示使用账户自由金额还是账户的锁仓金额做委托，0: 自由金额； 1: 锁仓金额|
|nodeId|64bytes|被质押的节点的NodeId|
|amount|big.Int(bytes)|委托的金额(按照最小单位算，1LAT = 10^18 von)|


调用示例
```JavaScript
const nodeId = "f71e1bc638456363a66c4769284290ef3ccff03aba4a22fb60ffaed60b77f614bfd173532c3575abe254c366df6f4d6248b929cb9398aaac00cbcc959f7b2b7c";
let params, others, reply;

// 以传进入对象进行调用(对于key不要求顺序)
params = {
    funcType: 1004,
    typ: 0,
    nodeId: ppos.hexStrBuf(nodeId),
    amount: ppos.bigNumBuf("10000000000000000000000")
}
reply = await ppos.send(params);

// 以传入数组对象进行调用
params = [1004, 0, ppos.hexStrBuf(nodeId), ppos.bigNumBuf("10000000000000000000000")];
reply = await ppos.send(params);

// 我不想默认的轮询
other = {
    retry: 300, // 只轮询300次
    interval: 200 // 每次轮询间隔200ms
}
params = [1004, 0, ppos.hexStrBuf(nodeId), ppos.bigNumBuf("10000000000000000000000")];
reply = await ppos.send(params, other);
```

### 内置合约入参详细说明

#### 质押

* 发起质押，send 发送交易。


|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1000)|
|typ|uint16(2bytes)|表示使用账户自由金额还是账户的锁仓金额做质押，0: 自由金额； 1: 锁仓金额|
|benefitAddress|20bytes|用于接受出块奖励和质押奖励的收益账户|
|nodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|externalId|string|外部Id(有长度限制，给第三方拉取节点描述的Id)|
|nodeName|string|被质押节点的名称(有长度限制，表示该节点的名称)|
|website|string|节点的第三方主页(有长度限制，表示该节点的主页)|
|details|string|节点的描述(有长度限制，表示该节点的描述)|
|amount|*big.Int(bytes)|质押的von|
|programVersion|uint32|程序的真实版本，治理rpc获取|
|programVersionSign|65bytes|程序的真实版本签名，治理rpc获取|
|blsPubKey|96bytes|bls的公钥|
|blsProof|64bytes|bls的证明,通过拉取证明接口获取|

* 修改质押信息，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1001)|
|benefitAddress|20bytes|用于接受出块奖励和质押奖励的收益账户|
|nodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|externalId|string|外部Id(有长度限制，给第三方拉取节点描述的Id)|
|nodeName|string|被质押节点的名称(有长度限制，表示该节点的名称)|
|website|string|节点的第三方主页(有长度限制，表示该节点的主页)|
|details|string|节点的描述(有长度限制，表示该节点的描述)|


* 增持质押，send 发送交易。

入参：

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1002)|
|nodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|typ|uint16(2bytes)|表示使用账户自由金额还是账户的锁仓金额做质押，0: 自由金额； 1: 锁仓金额|
|amount|*big.Int(bytes)|增持的von|


* 撤销质押(一次性发起全部撤销，多次到账)，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1003)|
|nodeId|64bytes|被质押的节点的NodeId|

* 发起委托，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1004)|
|typ|uint16(2bytes)|表示使用账户自由金额还是账户的锁仓金额做委托，0: 自由金额； 1: 锁仓金额|
|nodeId|64bytes|被质押的节点的NodeId|
|amount|*big.Int(bytes)|委托的金额(按照最小单位算，1LAT = 10**18 von)|

* 减持/撤销委托(全部减持就是撤销)，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1005)|
|stakingBlockNum|uint64(8bytes)|代表着某个node的某次质押的唯一标示|
|nodeId|64bytes|被质押的节点的NodeId|
|amount|*big.Int(bytes)|减持委托的金额(按照最小单位算，1LAT = 10**18 von)|

* 查询当前结算周期的验证人队列，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1100)|

**查询结果统一格式**

|名称|类型|说明|
|---|---|---|
|Code|uint32| 表示ppos内置合约返回的错误码|
|Data|string| json字符串的查询结果，具体格式参见以下查询相关接口返回值 |
|ErrMsg|string| 错误提示信息|

> 注：以下查询接口（platon_call调用的接口）如无特殊声明，返回参数都按照上述格式返回

返参： 列表

|名称|类型|说明|
|---|---|---|
|NodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|StakingAddress|20bytes|发起质押时使用的账户(后续操作质押信息只能用这个账户，撤销质押时，von会被退回该账户或者该账户的锁仓信息中)|
|BenefitAddress|20bytes|用于接受出块奖励和质押奖励的收益账户|
|StakingTxIndex|uint32(4bytes)|发起质押时的交易索引|
|ProgramVersion|uint32|被质押节点的PlatON进程的真实版本号(获取版本号的接口由治理提供)|
|StakingBlockNum|uint64(8bytes)|发起质押时的区块高度|
|Shares|*big.Int(bytes)|当前候选人总共质押加被委托的von数目|
|ExternalId|string|外部Id(有长度限制，给第三方拉取节点描述的Id)|
|NodeName|string|被质押节点的名称(有长度限制，表示该节点的名称)|
|Website|string|节点的第三方主页(有长度限制，表示该节点的主页)|
|Details|string|节点的描述(有长度限制，表示该节点的描述)|
|ValidatorTerm|uint32(4bytes)|验证人的任期(在结算周期的101个验证人快照中永远是0，只有在共识轮的验证人时才会被有值，刚被选出来时也是0，继续留任时则+1)|

* 查询当前共识周期的验证人列表，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1101)|

返参： 列表

|名称|类型|说明|
|---|---|---|
|NodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|StakingAddress|20bytes|发起质押时使用的账户(后续操作质押信息只能用这个账户，撤销质押时，von会被退回该账户或者该账户的锁仓信息中)|
|BenefitAddress|20bytes|用于接受出块奖励和质押奖励的收益账户|
|StakingTxIndex|uint32(4bytes)|发起质押时的交易索引|
|ProgramVersion|uint32(4bytes)|被质押节点的PlatON进程的真实版本号(获取版本号的接口由治理提供)|
|StakingBlockNum|uint64(8bytes)|发起质押时的区块高度|
|Shares|*big.Int(bytes)|当前候选人总共质押加被委托的von数目|
|ExternalId|string|外部Id(有长度限制，给第三方拉取节点描述的Id)|
|NodeName|string|被质押节点的名称(有长度限制，表示该节点的名称)|
|Website|string|节点的第三方主页(有长度限制，表示该节点的主页)|
|Details|string|节点的描述(有长度限制，表示该节点的描述)|
|ValidatorTerm|uint32(4bytes)|验证人的任期(在结算周期的101个验证人快照中永远是0，只有在共识轮的验证人时才会被有值，刚被选出来时也是0，继续留任时则+1)|

* 查询所有实时的候选人列表，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1102)|

返参： 列表

|名称|类型|说明|
|---|---|---|
|NodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|StakingAddress|20bytes|发起质押时使用的账户(后续操作质押信息只能用这个账户，撤销质押时，von会被退回该账户或者该账户的锁仓信息中)|
|BenefitAddress|20bytes|用于接受出块奖励和质押奖励的收益账户|
|StakingTxIndex|uint32(4bytes)|发起质押时的交易索引|
|ProgramVersion|uint32(4bytes)|被质押节点的PlatON进程的真实版本号(获取版本号的接口由治理提供)|
|Status|uint32(4bytes)|候选人的状态(状态是根据uint32的32bit来放置的，可同时存在多个状态，值为多个同时存在的状态值相加【0: 节点可用 (32个bit全为0)； 1: 节点不可用 (只有最后一bit为1)； 2： 节点出块率低但没有达到移除条件的(只有倒数第二bit为1)； 4： 节点的von不足最低质押门槛(只有倒数第三bit为1)； 8：节点被举报双签(只有倒数第四bit为1)); 16: 节点出块率低且达到移除条件(倒数第五位bit为1); 32: 节点主动发起撤销(只有倒数第六位bit为1)】|
|StakingEpoch|uint32(4bytes)|当前变更质押金额时的结算周期|
|StakingBlockNum|uint64(8bytes)|发起质押时的区块高度|
|Shares|string(0x十六进制字符串)|当前候选人总共质押加被委托的von数目|
|Released|string(0x十六进制字符串)|发起质押账户的自由金额的锁定期质押的von|
|ReleasedHes|string(0x十六进制字符串)|发起质押账户的自由金额的犹豫期质押的von|
|RestrictingPlan|string(0x十六进制字符串)|发起质押账户的锁仓金额的锁定期质押的von|
|RestrictingPlanHes|string(0x十六进制字符串)|发起质押账户的锁仓金额的犹豫期质押的von|
|ExternalId|string|外部Id(有长度限制，给第三方拉取节点描述的Id)|
|NodeName|string|被质押节点的名称(有长度限制，表示该节点的名称)|
|Website|string|节点的第三方主页(有长度限制，表示该节点的主页)|
|Details|string|节点的描述(有长度限制，表示该节点的描述)|

* 查询当前账户地址所委托的节点的NodeID和质押Id，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(1103)|
|addr|common.address(20bytes)|委托人的账户地址|

返参： 列表

|名称|类型|说明|
|---|---|---|
|Addr|20bytes|委托人的账户地址|
|NodeId|64bytes|验证人的节点Id|
|StakingBlockNum|uint64(8bytes)|发起质押时的区块高度|


* 查询当前单个委托信息，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16|代表方法类型码(1104)|
|stakingBlockNum|uint64(8bytes)|发起质押时的区块高度|
|delAddr|20bytes|委托人账户地址|
|nodeId|64bytes|验证人的节点Id|

返参： 列表

|名称|类型|说明|
|---|---|---|
|Addr|20bytes|委托人的账户地址|
|NodeId|64bytes|验证人的节点Id|
|StakingBlockNum|uint64(8bytes)|发起质押时的区块高度|
|DelegateEpoch|uint32(4bytes)|最近一次对该候选人发起的委托时的结算周期|
|Released|string(0x十六进制字符串)|发起委托账户的自由金额的锁定期委托的von|
|ReleasedHes|string(0x十六进制字符串)|发起委托账户的自由金额的犹豫期委托的von|
|RestrictingPlan|string(0x十六进制字符串)|发起委托账户的锁仓金额的锁定期委托的von|
|RestrictingPlanHes|string(0x十六进制字符串)|发起委托账户的锁仓金额的犹豫期委托的von|
|Reduction|string(0x十六进制字符串)|处于撤销计划中的von|

* 查询当前节点的质押信息，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16|代表方法类型码(1105)|
|nodeId|64bytes|验证人的节点Id|

返参： 列表

|名称|类型|说明|
|---|---|---|
|NodeId|64bytes|被质押的节点Id(也叫候选人的节点Id)|
|StakingAddress|20bytes|发起质押时使用的账户(后续操作质押信息只能用这个账户，撤销质押时，von会被退回该账户或者该账户的锁仓信息中)|
|BenefitAddress|20bytes|用于接受出块奖励和质押奖励的收益账户|
|StakingTxIndex|uint32(4bytes)|发起质押时的交易索引|
|ProgramVersion|uint32(4bytes)|被质押节点的PlatON进程的真实版本号(获取版本号的接口由治理提供)|
|Status|uint32(4bytes)|候选人的状态(状态是根据uint32的32bit来放置的，可同时存在多个状态，值为多个同时存在的状态值相加【0: 节点可用 (32个bit全为0)； 1: 节点不可用 (只有最后一bit为1)； 2： 节点出块率低但没有达到移除条件的(只有倒数第二bit为1)； 4： 节点的von不足最低质押门槛(只有倒数第三bit为1)； 8：节点被举报双签(只有倒数第四bit为1)); 16: 节点出块率低且达到移除条件(倒数第五位bit为1); 32: 节点主动发起撤销(只有倒数第六位bit为1)】|
|StakingEpoch|uint32(4bytes)|当前变更质押金额时的结算周期|
|StakingBlockNum|uint64(8bytes)|发起质押时的区块高度|
|Shares|string(0x十六进制字符串)|当前候选人总共质押加被委托的von数目|
|Released|string(0x十六进制字符串)|发起质押账户的自由金额的锁定期质押的von|
|ReleasedHes|string(0x十六进制字符串)|发起质押账户的自由金额的犹豫期质押的von|
|RestrictingPlan|string(0x十六进制字符串)|发起质押账户的锁仓金额的锁定期质押的von|
|RestrictingPlanHes|string(0x十六进制字符串)|发起质押账户的锁仓金额的犹豫期质押的von|
|ExternalId|string|外部Id(有长度限制，给第三方拉取节点描述的Id)|
|NodeName|string|被质押节点的名称(有长度限制，表示该节点的名称)|
|Website|string|节点的第三方主页(有长度限制，表示该节点的主页)|
|Details|string|节点的描述(有长度限制，表示该节点的描述)|



#### 治理

* 提交文本提案，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2000)|
|verifier|discover.NodeID(64bytes)|提交提案的验证人|
|pIDID|string(uint64)|PIPID|

* 提交升级提案，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2001)|
|verifier|discover.NodeID(64bytes)|提交提案的验证人|
|pIDID|string(uint64)|PIPID|
|newVersion|uint32(4bytes)|升级版本|
|endVotingRounds|uint64|投票共识轮数量。说明：假设提交提案的交易，被打包进块时的共识轮序号时round1，则提案投票截止块高，就是round1 + endVotingRounds这个共识轮的第230个块高（假设一个共识轮出块250，ppos揭榜提前20个块高，250，20都是可配置的 ），其中0 < endVotingRounds <= 4840（约为2周，实际论述根据配置可计算），且为整数）|


* 提交取消提案，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2005)|
|verifier|discover.NodeID(64bytes)|提交提案的验证人|
|pIDID|string(uint64)|PIPID|
|endVotingRounds|uint64|投票共识轮数量。参考提交升级提案的说明，同时，此接口中此参数的值不能大于对应升级提案中的值|
|tobeCanceledProposalID|common.hash(32bytes)|待取消的升级提案ID|


* 给提案投票，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2003)|
|verifier|discover.NodeID(64bytes)|投票验证人|
|proposalID|common.Hash(32bytes)|提案ID|
|option|uint8(1byte)|投票选项|
|programVersion|uint32(4bytes)|节点代码版本，有rpc的getProgramVersion接口获取|
|versionSign|common.VesionSign(65bytes)|代码版本签名，有rpc的getProgramVersion接口获取|

* 版本声明，send 发送交易。

|参数|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2004)|
|verifier|discover.NodeID(64bytes)|声明的节点，只能是验证人/候选人|
|programVersion|uint32(4bytes)|声明的版本，有rpc的getProgramVersion接口获取|
|versionSign|common.VesionSign(65bytes)|声明的版本签名，有rpc的getProgramVersion接口获取|

* 查询提案，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|2100)|
|proposalID|common.Hash(32bytes)|提案ID|

返参：Proposal接口实现对象的json字符串

* 查询提案结果，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2101)|
|proposalID|common.Hash(32bytes)|提案ID|

返参：TallyResult对象的json字符串

* 查询提案列表，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2102)|

返参：Proposal接口实现对象列表的json字符串。


* 查询节点的链生效版本，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2103)|

返参：版本号的json字符串，如{65536}，表示版本是：1.0.0。
解析时，需要把ver转成4个字节。主版本：第二个字节；小版本：第三个字节，patch版本，第四个字节。


* 查询提案的累积可投票人数，call 查询

入参：

|名称|类型|说明|
|---|---|---|
|funcType|uint16(2bytes)|代表方法类型码(2105)|
|proposalID|common.Hash(32bytes)|提案ID|
|blockHash|common.Hash(32bytes)|块hash|

返参：
是个[]uint16数组

|名称|类型|说明|
|---|---|---|
||uint16|累积可投票人数|
||uint16|赞成票数|
||uint16|反对票数|
||uint16|弃权票数|

**ProposalType 提案类型定义**

|类型|定义|说明|
|---|---|---|
|TextProposal|0x01|文本提案|
|VersionProposal|0x02|升级提案|
|CancelProposal|0x04|取消提案|

**ProposalStatus 提案状态定义**

对文本提案来说，有：0x01,0x02,0x03三种状态；
对升级提案来说，有：0x01,0x03,0x04,0x05,0x06四种状态。
对取消提案来说，有：0x01,0x02,0x03三种状态；

|类型|定义|说明|
|---|---|---|
|Voting|0x01|投票中|
|Pass|0x02|投票通过|
|Failed|0x03|投票失败|
|PreActive|0x04|（升级提案）预生效|
|Active|0x05|（升级提案）生效|
|Canceled|0x06|（升级提案）被取消|

**VoteOption 投票选项定义**

|类型|定义|说明|
|---|---|---|
|Yeas|0x01|支持|
|Nays|0x02|反对|
|Abstentions|0x03|弃权|

**Proposal接口 提案定义**

子类TextProposal：文本提案

字段说明：

|字段|类型|说明|
|---|---|---|
|ProposalID|common.Hash(32bytes)|提案ID|
|Proposer|common.NodeID(64bytes)|提案节点ID|
|ProposalType|byte|提案类型， 0x01：文本提案； 0x02：升级提案；0x03参数提案；0x04取消提案。|
|PIPID|string|提案PIPID|
|SubmitBlock|8bytes|提交提案的块高|
|EndVotingBlock|8bytes|提案投票结束的块高，系统根据SubmitBlock|


子类VersionProposal：升级提案

字段说明：

|字段|类型|说明|
|---|---|---|
|ProposalID|common.Hash(32bytes)|提案ID|
|Proposer|common.NodeID(64bytes)|提案节点ID|
|ProposalType|byte|提案类型， 0x01：文本提案； 0x02：升级提案；0x03参数提案；0x04取消提案。|
|PIPID|string|提案PIPID|
|SubmitBlock|8bytes|提交提案的块高|
|EndVotingRounds|8bytes|投票持续的共识周期数量|
|EndVotingBlock|8bytes|提案投票结束的块高，系统根据SubmitBlock，EndVotingRounds算出|
|ActiveBlock|8bytes|提案生效块高，系统根据EndVotingBlock算出|
|NewVersion|uint|升级版本|


子类CancelProposal：取消提案

字段说明：

|字段|类型|说明|
|---|---|---|
|ProposalID|common.Hash(32bytes)|提案ID|
|Proposer|common.NodeID(64bytes)|提案节点ID|
|ProposalType|byte|提案类型， 0x01：文本提案； 0x02：升级提案；0x03参数提案；0x04取消提案。|
|PIPID|string|提案PIPID|
|SubmitBlock|8bytes|提交提案的块高|
|EndVotingRounds|8bytes|投票持续的共识周期数量|
|EndVotingBlock|8bytes|提案投票结束的块高，系统根据SubmitBlock，EndVotingRounds算出|
|TobeCanceled|common.Hash(32bytes)|提案要取消的升级提案ID|


**Vote 投票定义**

|字段|类型|说明|
|---|---|---|
|voter|64bytes|投票验证人|
|proposalID|common.Hash(32bytes)|提案ID|
|option|VoteOption|投票选项|

**TallyResult 投票结果定义**

|字段|类型|说明|
|---|---|---|
|proposalID|common.Hash(32bytes)|提案ID|
|yeas|uint16(2bytes)|赞成票|
|nays|uint16(2bytes)|反对票|
|abstentions|uint16(2bytes)|弃权票|
|accuVerifiers|uint16(2bytes)|在整个投票期内有投票资格的验证人总数|
|status|byte|状态|
|canceledBy|common.Hash(32bytes)|当status=0x06时，记录发起取消的ProposalID|

#### 举报惩罚

* 举报双签，send 发送交易。


| 参数     | 类型   | 描述                                    |
| -------- | ------ | --------------------------------------- |
| funcType | uint16(2bytes) | 代表方法类型码(3000)                    |
| typ      | uint8         | 代表双签类型，<br />1：prepareBlock，2：prepareVote，3：viewChange |
| data     | string | 单个证据的json值，格式参照[RPC接口Evidences][evidences_interface] |

* 查询节点是否已被举报过多签，call 查询

入参：

| 参数        | 类型           | 描述                                                         |
| ----------- | -------------- | ------------------------------------------------------------ |
| funcType    | uint16(2bytes) | 代表方法类型码(3001)                                         |
| typ         | uint32         | 代表双签类型，<br />1：prepareBlock，2：prepareVote，3：viewChange |
| addr        | 20bytes        | 举报的节点地址                                               |
| blockNumber | uint64         | 多签的块高                                                   |

回参：

| 类型   | 描述           |
| ------ | -------------- |
| 16进制 | 举报的交易Hash |



#### 锁仓

* 创建锁仓计划，send 发送交易。

入参：

| 参数    | 类型           | 说明                                                         |
| ------- | -------------- | ------------------------------------------------------------ |
| account | 20bytes | `锁仓释放到账账户`                                           |
| plan    | []RestrictingPlan | plan 为 RestrictingPlan 类型的列表（数组），RestrictingPlan 定义如下：<br>type RestrictingPlan struct { <br/>    Epoch uint64<br/>    Amount：\*big.Int<br/>}<br/>其中，Epoch：表示结算周期的倍数。与每个结算周期出块数的乘积表示在目标区块高度上释放锁定的资金。Epoch \* 每周期的区块数至少要大于最高不可逆区块高度。<br>Amount：表示目标区块上待释放的金额。 |

* 获取锁仓信息，call 查询

注：本接口支持获取历史数据，请求时可附带块高，默认情况下查询最新块的数据。


入参：

| 参数    | 类型    | 说明               |
| ------- | ------- | ------------------ |
| account | 20bytes | `锁仓释放到账账户` |

返参：

返回参数为下面字段的 json 格式字符串

| 名称    | 类型            | 说明                                                         |
| ------- | --------------- | ------------------------------------------------------------ |
| balance | string(0x十六进制字符串) | 总锁仓余额-已释放金额                                                     |
| pledge    | string(0x十六进制字符串) |质押/抵押金额 |
| debt  | string(0x十六进制字符串)            | 欠释放金额                                                 |
| plans    | bytes           | 锁仓分录信息，json数组：[{"blockNumber":"","amount":""},...,{"blockNumber":"","amount":""}]。其中：<br/>blockNumber：\*big.Int，释放区块高度<br/>amount：\string(0x十六进制字符串)，释放金额 |

### 内置合约错误码说明
| 错误码    | 说明            |
| ------- | --------------- |
|301000  | Wrong bls public key|
|301001  | Wrong bls public key proof|
|301002  | The Description length is wrong|
|301003  | The program version sign is wrong|
|301004  | The program version of the relates node's is too low|
|301005  | DeclareVersion is failed on create staking|
|301006  | The address must be the same as initiated staking|
|301100  | Staking deposit too low|
|301101  | This candidate is already exist|
|301102  | This candidate is not exist|
|301103  | This candidate status was invalided|
|301104  | IncreaseStake von is too low|
|301105  | Delegate deposit too low|
|301106  | The account is not allowed to be used for delegating|
|301107  | The candidate does not accept the delegation|
|301108  | Withdrew delegation von is too low|
|301109  | This delegation is not exist|
|301110  | The von operation type is wrong|
|301111  | The von of account is not enough|
|301112  | The blockNumber is disordered|
|301113  | The von of delegation is not enough|
|301114  | Withdrew delegation von calculation is wrong|
|301115  | The validator is not exist|
|301116  | The fn params is wrong|
|301117  | The slashing type is wrong|
|301118  | Slashing amount is overflow|
|301119  | Slashing candidate von calculate is wrong|
|301200  | Getting verifierList is failed|
|301201  | Getting validatorList is failed|
|301202  | Getting candidateList is failed|
|301203  | Getting related of delegate is failed|
|301204  | Query candidate info failed|
|301205  | Query delegate info failed|

