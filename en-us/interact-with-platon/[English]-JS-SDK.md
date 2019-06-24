# Index

- []

## Overview
> Javascript SDK is a development toolset for PlatON public chain that PlatON provided for js devlopers.

### Getting started

### Install or Import

Import through node.js:

 `cnpm i https://github.com/PlatONnetwork/client-sdk-js`

### Initialize the code

Now you need to create an instance of web3 and set a provider. To ensure that you won’t override an existing provider (There’s a default one when using Mist), you need to check whether a web3 instance already exists.

```js
if (typeof web3 !== 'undefined') {
    web3 = new Web3(web3.currentProvider);
} else {
    web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));
}
```

### Contracts

For instructions on writing wasm contracts and generating its ABI and BIN files, please refer to [WASM contract guide](/en-us/development/'[English]-Wasm-Contract-Development-Guide.md').

#### Contract example

```
    namespace platon {
        class ACC : public token::Token {
        public:
            ACC(){}
            void init(){
                Address user(std::string("0xa0b21d5bcc6af4dda0579174941160b9eecb6916"), true);
                initSupply(user, 199999);
            }

            void create(const char *addr, uint64_t asset){
                Address user(std::string(addr), true);
                Token::create(user, asset);
            }

            uint64_t getAsset(const char *addr) const {
                Address user(std::string(addr), true);
                return Token::getAsset(user);
            }

            void transfer(const char *addr, uint64_t asset) {
                Address user(std::string(addr), true);
                Token::transfer(user, asset);
            }
        };
    }

    PLATON_ABI(platon::ACC, create)
    PLATON_ABI(platon::ACC, getAsset)
    PLATON_ABI(platon::ACC, transfer)
    //platon autogen begin
    extern "C" {
        void create(const char * addr,unsigned long long asset) {
            platon::ACC ACC_platon;
            ACC_platon.create(addr,asset);
        }
        unsigned long long getAsset(const char * addr) {
            platon::ACC ACC_platon;
            return ACC_platon.getAsset(addr);
        }
        void transfer(const char * addr,unsigned long long asset) {
            platon::ACC ACC_platon;
            ACC_platon.transfer(addr,asset);
        }
        void init() {
            platon::ACC ACC_platon;
            ACC_platon.init();
        }
    }
    //platon autogen end
```

#### Deploying a contract

##### Interface declaration

    MyContract.deploy(deployData [, callback])

##### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|deployData|String|required|Hexadecimal data of signed transaction|
|callback|Funciton |optional|Callback function, used to support asynchronous methods|

##### Example

````js
const Web3 = require('web3'),
    fs = require('fs'),
    path = require('path'),
    Tx = require('ethereumjs-tx'),
    abi = require('./multisig.cpp.abi.json') //application binary entry
    ;

const provider = 'http://localhost:6789',
    privateKey='fd098d39e56115762cf28d7d223a4303a42a42451da6e7da37cb40a81a246d98',
    address='0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b'

const web3 = new Web3(new Web3.providers.HttpProvider(provider))

const MyContract  = web3.eth.contract(abi)

const source = fs.readFileSync(path.join(__dirname, './demo.wasm'));//WebAssembly binary code

const platONData = MyContract.deploy.getPlatONData(source)

const params = {
    nonce: web3.eth.getTransactionCount(address),
    gas: "0x506709",
    gasPrice: "0x8250de00",
    data: platONData,
    from:address
}

const daployData=sign(privateKey,params)

let myContractReturned = MyContract.deploy(daployData, (err, myContract)=> {
    if (!err) {
        if (!myContract.address) {
            console.log("contract deploy transaction hash: " + myContract.transactionHash) 
        } else {
            // Contract successfully deployed
            console.log("contract deploy transaction address: " + myContract.address)
            const receipt = web3.eth.getTransactionReceipt(myContract.transactionHash);
            console.log(`contract deploy receipt:`, receipt);
        }
    } else {
        console.log(`contract deploy error:`, err)
    }
});

function sign(privateKey, data) {
    const key = new Buffer(privateKey, 'hex')
    const tx = new Tx(data)
    tx.sign(key)

    const serializeTx = tx.serialize()
    const result = '0x' + serializeTx.toString('hex')
    return result
}
````

#### Calling contract functions

> Call a contract function and return a constant

##### Interface declaration

    web3.eth.call(Object [, defaultBlock] [, callback])

##### Parameter description

|Parameters|Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|Object |String |Required| Returns a transaction object, just like `web3.eth.sendTransaction`. The difference between them is that the `from` attribute is optional|
|defaultBlock|Number/String |Optional|If this value isn't set then uses the block specified by `web3.eth.defaultBlock`. It uses the specified block otherwise|
|callback|Funciton  |Optional|Callback function, used to support asynchronous methods|

##### Example

````js
const data = contract.getOwners.getPlatONData()

const result = web3.eth.call({
    from: web3.eth.accounts[0],
    to: contract.address,
    data: data
});

console.log('call result:', result);

contract.decodePlatONCall(result)
````

#### Call sendRawTransaction of a contract

> Send a transaction signed by the private key

##### Interface declaration

    web3.eth.sendRawTransaction(signedTransactionData [, callback])

##### Parameter description

|Parameters|Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|signedTransactionData |String |Required| Hexadecimal data of signed transaction|
|callback|Funciton  |Optional|Callback function, used to support asynchronous methods|

##### Example

````js
const Tx = require('ethereumjs-tx');

const privateKey='4484092b68df58d639f11d59738983e2b8b81824f3c0c759edd6773f9adadfe7',
    param_from = web3.eth.accounts[0],
    param_to = wallet.address,
    param_assert = 4
    ;

const data = myContractInstance.transfer02.getPlatONData(param_from, param_to, param_assert);

const hash = web3.eth.sendRawTransaction(sign(privateKey,getParams(data)))
console.log('hash', hash);//0xb1335d4db521ddc0b390448f919e5b5af1258b29e7ab4e0d68b0ef315af0cf5f

const data=web3.eth.getTransactionReceipt(hash);

let res = myContractInstance.decodePlatONLog(data.logs[0]);

function sign(privateKey, data) {
    const key = new Buffer(privateKey, 'hex')
    const tx = new Tx(data)
    tx.sign(key)

    const serializeTx = tx.serialize()
    const result = '0x' + serializeTx.toString('hex')
    return result
}

function getParams(data = '', value = "0x0") {
    const nonce = web3.eth.getTransactionCount(wallet.address)
    value = web3.toHex(web3.toWei(value, 'ether'));

    const params = {
        from: '0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b',
        gasPrice: 22 * 10e9,
        gas: 80000,
        to: myContractInstance.address,
        value,
        data,
        nonce
    }

    return params
}
````

#### Build-in Contract

##### CandidateContract

> Contract entrance relevant to candidates in PlatON's economy model

##### Loading the contract

````js
const Web3 = require('web3'),
    wallet = require('../owner.json'),//wallet file
    toObj=require('./toObj'),//detailed code following
    sign = require('./sign'),//signature; detailed code following
    abi = require('../abi/candidateConstract.json'),// abi relevant to candidates
    getTransactionReceipt = require('./getTransactionReceipt'),// detailed code following
    candidateContractAddress='0x1000000000000000000000000000000000000001';

const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));

const calcContract = web3.eth.contract(abi),
    candidateContract = calcContract.at(candidateContractAddress);

function getParams(data = '', value = "0x0") {
    const nonce = web3.eth.getTransactionCount(wallet.address);
    value = web3.toHex(web3.toWei(value, 'ether'));

    const params = {
        from: wallet.address,//wallet address
        gasPrice: 22 * 10e9,
        gas: 80000,
        to: candidateContract.address,
        value,
        data,
        nonce
    }
    return params;
}
````

Prepare file ./toObj.js

````js
function toObj(str) {
    if (!str || str == '0x') return null;
    let result = null;
    try {
        result = JSON.parse(str)
    } catch (error) {
        console.warn(`toObj error`, error)
        throw new Error(error)
    }
    if (Array.isArray(result)) {
        return result.map(item => {
            try {
                item.Extra ? item.Extra = JSON.parse(item.Extra) : ''
            } catch (error) {
                console.warn('Extra error',item.Extra)
            }
            return item
        })
    } else {
        try {
            result.Extra ? result.Extra = JSON.parse(result.Extra) : ''
        } catch (error) {
            console.warn('Extra error',result.Extra)
        }
        return result
    }
    return result;
}

module.exports = toObj;
````

Prepare file ./sign.js

````js
const Tx = require('ethereumjs-tx');

module.exports = (privateKey, data) => {
    if (!privateKey || !data) {
        throw new Error(`sign parameter error`);
    }

    const key = new Buffer(privateKey, 'hex'),
        tx = new Tx(data);

    tx.sign(key);

    const serializeTx = tx.serialize(),
        result = '0x' + serializeTx.toString('hex');

    ;
};
````

Prepare file ./getTransactionReceipt.js

````js
const Web3 = require('web3');
const web3 = new Web3 (new Web3.providers.HttpProvider ('http://localhost:6789'));

let wrapCount = 60;
function getTransactionReceipt(hash, fn) {
    let id = '',
        result = web3.eth.getTransactionReceipt(hash),
        data = {};
    if (result && result.transactionHash && hash == result.transactionHash) {
        clearTimeout(id);
        if (result.logs.length != 0) {
            fn(0, result);
        } else {
            fn(1001, 'Contract error, failed');
        }
    } else {
        if (wrapCount--) {
            id = setTimeout(() => {
                getTransactionReceipt(hash, fn);
            }, 1000);
        } else {
            fn(1000, 'timeout');
            id = '';
        }
    }
}

module.exports = getTransactionReceipt
````

##### CandidateDeposit

> Apply to be candidate node/increase staking

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeId|String|required|Hexadecimal node ID, starting wiht 0x|
|owner|String|optional|staking and refund address, hexadecimal, starting wiht 0x|
|fee|String|required|mining reward ratio, with 10000 as denominator (eg: 5% gives a fee of 500)|
|host|String|required|node IP|
|port|String|required|node's P2P port number|
|Extra|String|required|additional data in json format|
|value|BigInteger|required|staking amount|

Note: staking amount must be greater than or eaqual to 1,000,000 Energon (amount >= 1,000,000 Energon)

`Extra` parameter description
```
{
    "nodeName":string,                     //node name
    "officialWebsite":string,              //official web site, http | https
    "nodePortrait":string,                 //node logo http | https
    "nodeDiscription":string,              //node description
    "nodeDepartment":string                //node organization name
}
```

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

param1 description
```
{
    "Ret":boolean,                         //Boolean return value, true for success, false for failure
    "Data":string                          //return data
    "ErrMsg":string                        //error message when failed
}
```

###### Example


````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';

let nodeId = '0x15245d4dceeb7552b52d70e56c53fc86aa030eab6b7b325e430179902884fca3d684b0e896ea421864a160e9c18418e4561e9a72f911e2511c29204a857de71a', //[64]byte node ID (public key)
    owner = '0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b', //[20]byte staking and refund address
    fee = 500, //uint32 mining reward ratio, with 10000 as denominator(eg：5 %，fee = 500)
    host  = '0.0.0.0', //string node IP
    port='38794', //string  node port number
    Extra = web3.toHex({
        nodeName: 'Node Name',
        nodeDiscription: 'Description',
        nodeDepartment: 'Organization',
        officialWebsite: 'https://www.platon.network',
        nodePortrait: 'http://192.168.9.86:8082/group2/M00/00/00/wKgJVlr0KDyAGSddAAYKKe2rswE261.png',
        time: Date.now(), // Date of join
    }), //string, additional data with limited length (value to be determined)
    value = 1000000;// staking amount

Extra = web3.toUnicode(Extra);

const data = candidateContract.CandidateDeposit.getPlatONData(nodeId, owner, fee, host, port, Extra, {
    transactionType:1001
});

const hash = web3.eth.sendRawTransaction(sign(privateKey, getParams(data, value)))
getTransactionReceipt(hash, (code, data) => {
    if (code == 0) {
        let res = candidateContract.decodePlatONLog(data.logs[0]);
        if (res.length && res[0]) {
            res = JSON.parse(res[0])
            if (res.ErrMsg == 'success') {
                console.log(`Candidate node application / add staking successfully`);
            } else {
                console.log(`Candidate node application / add staking unsuccessfully`);
            }
        } else {
            console.log(`Candidate node application / add staking unsuccessfully`);
        }
    } else {
        console.warn(`Candidate node application / add staking error`);
    }
});
````

--- continue ---

