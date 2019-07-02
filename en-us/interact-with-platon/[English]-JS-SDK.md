# Index - under construction now

- [TOC]

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
|nodeId|String|required|Hexadecimal node ID, starting with 0x|
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

##### CandidateApplyWithdraw

> Node staking refund application. After successful application, the node's ranking will change accordingly in the candidate pool. The address of application initiator must be the same as the refund address

###### Parameter description

|Parameters|Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeId|String|required|hexadecimal node ID, starting with 0x|
|withdraw|String|required|refund amount (unit: E)|

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
const nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a', //[64]byte node ID (public key)
    withdraw = Number(web3.toWei(5, 'ether'));
const data = candidateContract.CandidateApplyWithdraw.getPlatONData(nodeId, withdraw, {
    transactionType:1002
});
const hash = web3.eth.sendRawTransaction(sign(privateKey, getParams(data)))

getTransactionReceipt(hash, (code, data) => {
    console.log(code, data)
    if (code == 0) {
        let res = candidateContract.decodePlatONLog(data.logs[0], 'CandidateApplyWithdrawEvent');
        if (res.length && res[0]) {
            res = JSON.parse(res[0])
            if (res.ErrMsg == 'success') {
                console.log(`Node staking refund application successful`);
            } else {
                console.log(`Node staking refund application unsuccessful`);
            }
        } else {
            console.log(`Node staking refund application unsuccessful`)
        }
    } else {
        console.warn(`Node staking refund application error`)
    }
})
````

##### CandidateWithdraw

> Node staking withdrawal. After successful call the system, the system will withdraw all requested refund into the owner's account.

###### Parameter description

|Parameters|Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeId|String|required|hexadecimal node ID, starting with 0x|

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
const nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a' //[64]byte node ID (public key)
const data = candidateContract.CandidateWithdraw.getPlatONData(nodeId, {
    transactionType:1003
});
const hash = web3.eth.sendRawTransaction(sign(privateKey, getParams(data)))
console.log('hash', hash)
getTransactionReceipt(hash, (code, data) => {
    console.log(code, data)
    if (code == 0) {
        let res = candidateContract.decodePlatONLog(data.logs[0], 'CandidateWithdrawEvent');
        if (res.length && res[0]) {
            res = JSON.parse(res[0])
            if (res.ErrMsg == 'success') {
                console.log(`Node staking withdraw successful`);
            } else {
                console.log(`Node staking withdraw unsuccessful`);
            }
        } else {
            console.log(`Node staking withdraw unsuccessful`)
        }
    } else {
        console.warn(`Node staking withdraw error`);
    }
})
````

##### SetCandidateExtra

> Set the node's additional information. The address of application initiator must be the same as the refund address. from==owner (to be confirmed)

###### Parameter description

|Parameters|Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|Extra|String|required|additional data in json format|

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
let nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a', //[64]byte node ID (public key)
    owner = wallet.address, //[20]byte staking and refund address
    time = Date.now(),
    Extra =JSON.stringify({
        nodeName: 'My node name',
        nodeDiscription: 'My node description' + time,
        nodeDepartment: 'My institute name' + time,
        officialWebsite: 'www.platon.network',
        nodePortrait: 'URL',
        time: time, //time of join
    }) //string additional data (limited in length with value to be determined)

Extra = web3.toUnicode(Extra);

const data = candidateContract.SetCandidateExtra.getPlatONData(nodeId, Extra, {
    transactionType:1004
});
const hash = web3.eth.sendRawTransaction(sign(privateKey, getParams(data)))

getTransactionReceipt(hash, (code, data) => {
    console.log(code, data)
    let res = candidateContract.decodePlatONLog(data.logs[0]);
    if (res && res[0]) {
        res = JSON.parse(res[0])
        if (res.ErrMsg == 'success') {
            console.log(`Setting node additional information successful`);
        } else {
            console.log(`Setting node additional information unsuccessful`);
        }
    } else {
        console.log(`Setting node additional information unsuccessful`)
    }
})
````

##### GetCandidateWithdrawInfos

> Gets the withdraw records that the node has applied for.

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeId|String|required|Hexadecimal node ID, starting with 0x|

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

param1 description
```
{
    "Ret": true,
    "ErrMsg": "success",
    "Infos": [{                        //withdrawal records
        "Balance": 100,                //withdrawal value
        "LockNumber": 13112,           //withdrawal request block height
        "LockBlockCycle": 1            //withdrawal value's lock cycle
    }]
}
```

###### Example

````js
const nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a' //[64]byte node ID (public key)
const data = candidateContract.GetCandidateWithdrawInfos.getPlatONData(nodeId)

const result = web3.eth.call({
    from: wallet.address,
    to: candidateContract.address,
    data: data,
});

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log('Result of withdraw records of current node:', result2);
````

##### GetCandidateDetails

> Get details of group of candidates

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeId|String|required|A string created by concatenating multiple nodeId with ‘:’, where node ID is an hexadecimal string starting with 0x|

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

param1 description
```
{
        "code":0,                         //error code
        "data":"[]"                    //json formatted string
}
```
Sample data description
```
"[{
    //Deposit value
    "Deposit": 200,
    //Block height when deposit value last updated
    "BlockNumber": 12206,
    //Transaction index of the current block
    "TxIndex": 0,
    //Node Id
    "CandidateId": "6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3",
    //Node IP
    "Host": "192.168.9.76",
    //Node P2P port number
    "Port": "26794",
    //Deposit refund address
    "Owner": "0xf8f3978c14f585c920718c27853e2380d6f5db36",
    //Additional data
    "Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
    // mining reward ratio, with 10000 as denominator (eg: 5% gives a fee of 500)
    "Fee": 500
    //Hash of witness tickets transaction (updated, tobe removed)
    "TxHash": "0xfa0914bc939e76b40f378200cdb2603d342e09553d916aa4e6bee8fbbe273944"
}]"
```

###### 示例

````js
const nodeId = '0x0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47:0xe0b6af6cc2e10b2b74540b87098083d48343805a3ff09c655eab0b20dba2b2851aea79ee75b6e150bde58ead0be03ee4a8619ea1dfaf529cbb8ff55ca23531ed'// multiple addresses seperated by ":"

const data = candidateContract.GetCandidateDetails.getPlatONData(nodeId)

const result = web3.eth.call({
    from: wallet.address,
    to: candidateContract.address,
    data: data,
});

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log('Candidates details retrieved:', result2);
````

##### GetCandidateList

> Get a list of all qualifying candidate nodes

| Parameter |Type|Description|
| :------: |:------: |:------: |

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| json formated string|

Sample param1 value:
```
[[{
    "Deposit":10000000000000000000000000,
    "BlockNumber":3909,
    "TxIndex":0,"CandidateId":"0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47",
    "Host":"192.168.9.76",
    "Port":"16789",
    "Owner":"0x493301712671ada506ba6ca7891f436d29185821",
    "Extra":"{\"nodeName\":\"innerdevnet9.76-16789\",\"officialWebsite\":\"\",\"time\":1556161028879,\"nodePortrait\":\"1\",\"nodeDiscription\":\"A test network node deployed in Southeast Asia\",\"nodeDepartment\":\"Southeast Asia test node\"}",
    "Fee":500,
    "TxHash":"0x0000000000000000000000000000000000000000000000000000000000000000","TOwner":"0x0000000000000000000000000000000000000000"}],
    [{
        "Deposit":10000000000000000000000000,
        "BlockNumber":3911,
        "TxIndex":0,"CandidateId":"e0b6af6cc2e10b2b74540b87098083d48343805a3ff09c655eab0b20dba2b2851aea79ee75b6e150bde58ead0be03ee4a8619ea1dfaf529cbb8ff55ca23531ed",
        "Host":"192.168.9.76",
        "Port":"16790",
        "Owner":"0x493301712671ada506ba6ca7891f436d29185821",
        "Extra":"{\"nodeName\":\"innerdevnet9.76-16790\",\"officialWebsite\":\"\",\"time\":1556161031038,\"nodePortrait\":\"2\",\"nodeDiscription\":\"A test network node deployed in Singapore\",\"nodeDepartment\":\"Singapore test node\"}",
        "Fee":1000,
        "TxHash":"0x0000000000000000000000000000000000000000000000000000000000000000","TOwner":"0x0000000000000000000000000000000000000000"}]]
```

###### Example
````js
const data = candidateContract.GetCandidateList.getPlatONData()

    const result = web3.eth.call({
        from: wallet.address,
        to: candidateContract.address,
        data: data,
    });

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log(`Result of all qualified candidate nodes:`, result2);
````

##### GetVerifiersList

> Get list of all verifiers of current consensus

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

Sample data of param1:

```
[{
    "Deposit": 11100000000000000000,
    "BlockNumber": 13721,
    "TxIndex": 0,
    "CandidateId": "c0e69057ec222ab257f68ca79d0e74fdb720261bcdbdfa83502d509a5ad032b29d57c6273f1c62f51d689644b4d446064a7c8279ff9abd01fa846a3555395535",
    "Host": "192.168.9.76",
    "Port": "26793",
    "Owner": "0x3ef573e439071c87fe54287f07fe1fd8614f134c",
    "From": "0x3ef573e439071c87fe54287f07fe1fd8614f134c",
    "Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
    "Fee": 9900
}, {
    "Deposit": 200,
    "BlockNumber": 12206,
    "TxIndex": 0,
    "CandidateId": "6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3",
    "Host": "192.168.9.76",
    "Port": "26794",
    "Owner": "0xf8f3978c14f585c920718c27853e2380d6f5db36",
    "From": "0x493301712671ada506ba6ca7891f436d29185821",
    "Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
    "Fee": 500
}]
```

###### Example

````js
 const data = candidateContract.GetVerifiersList.getPlatONData()

const result = web3.eth.call({
    from: wallet.address,
    to: candidateContract.address,
    data: data,
});

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log('Result of all verifiers of current consensus:', result2);
````

##### TicketContract

> Contract interface of ticket pool in PlatON economic model

##### Loading contract

````js
const Web3 = require('web3'),
    wallet = require('../owner.json'),//wallet file
    sign = require('./sign'),// sign logic, detailed code following
    abi = require('../abi/ticketContract.json'),//abi file of ticket pool (tobe updated)
    getTransactionReceipt = require('./getTransactionReceipt'),// detailed code following
    ticketContractAddress='0x1000000000000000000000000000000000000002';//contract address for staking tickets

const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));

const calcContract = web3.eth.contract(abi),
    ticketContract = calcContract.at(ticketContractAddress);

function getParams(data = '', value = "0x0") {
    const nonce = web3.eth.getTransactionCount(wallet.address);

    value = web3.toHex(value)

    const params = {
        from:'0xf8f3978c14f585c920718c27853e2380d6f5db36',//钱包地址
        gasPrice: 22 * 10e9,
        gas: 80000,
        to: ticketContract.address,
        value,
        data,
        nonce
    }
    return params;
}
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

    return result;
};
````

Prepare file ./getTransactionReceipt.js

````js
const Web3 = require('web3'),
    config = require('../config/config.json');
const web3 = new Web3 (new Web3.providers.HttpProvider (config.provider));

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

##### GetTicketPrice

> Get current price of ticket, unit in 'E'

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

Sample data of param1:

```
{
	"code":Number,                   // error code
	"data":String                    // current price of ticket (E)
}
```

###### Example

````js
const data = ticketContract.GetTicketPrice.getPlatONData()

const result = web3.eth.call({
    from: wallet.address,
    to: ticketContract.address,
    data: data,
});

const result1 = ticketContract.decodePlatONCall(result);
console.log('getTicketPrice result:', result1.data);
````

##### GetPoolRemainder

> Get the number of remaining tickets

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

Sample data of param1:
```
{
	"code":Number,                   // error code
	"data":String                    // remaining amount
}
```

###### Example

````js
console.log('getPoolRemainder')
const data = ticketContract.GetPoolRemainder.getPlatONData()

const result = web3.eth.call({
    from: wallet.address,
    to: ticketContract.address,
    data: data,
});

const result1 = ticketContract.decodePlatONCall(result);
console.log('getPoolRemainder result:', result1.data);
````

##### VoteTicket

> Purchase tickets to vote for (existing) candidate. The transaction value should be quantity * price

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|count|Number|required|number of tickets to purchase|
|price|Number|required|price of ticet(unit `E`)|
|nodeId|String|required|node id, hexadecimal, starting with `0x`|

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

Sample data of param1:

```
{
	"Ret":boolean,                         // Return value, true means success, false means fail
	"ErrMsg":string                        // Error messages in case of fail
	"Data":string                          // Data return（number of successful votes  + ":" + deal price）
}
```

###### Example

````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';

const
    count = 1,// number of ticket
    price = 100000000000000000000,// current ticket price which can be retrieved by call GetTicketPrice 
    nodeId = '0x0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47'// node ID

const data = ticketContract.VoteTicket.getPlatONData(count, price, nodeId, {
    transactionType: 1000// transaction type
});

const value = price * count

const hash = web3.eth.sendRawTransaction(
    sign(privateKey, getParams(data, value))
);

getTransactionReceipt(hash, (code, data) => {
    let res = ticketContract.decodePlatONLog(data.logs[0]);
    if (res.length && res[0]) {
        res = JSON.parse(res[0]);
        if (res.ErrMsg == 'success') {
            if (res.Data) {
                const arr = res.Data.split(':')
                console.log(`number of successful votes:${arr[0]},deal price:${arr[1]}`)
            }
        } else {
            console.warn(`failed to purchase tickets`);
        }
    } else {
        console.warn(`failed to purchase tickets`);
    }
});
````

##### GetCandidateTicketCount

> Get patch valid ticket count of candidates

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeIds|String|required| Hexadecimal node ID of the candidate, starting with `0x`. Multiple node ID cancatenating with ':'|

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String|string in json format|

Sample data of param1:
```
{
	"code":0,                              // error code
	"data":"{"nodeId1":10,"nodeId2":1}"    // number of tickets
}
```

###### Example

````js
const nodeIds = '0x0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47:0xe0b6af6cc2e10b2b74540b87098083d48343805a3ff09c655eab0b20dba2b2851aea79ee75b6e150bde58ead0be03ee4a8619ea1dfaf529cbb8ff55ca23531ed'// Multiple node ID cancatenating with ':'

const data = ticketContract.GetCandidateTicketCount.getPlatONData(nodeIds)

const result = web3.eth.call({
    from: wallet.address,
    to: ticketContract.address,
    data: data,
});

const result1 = ticketContract.decodePlatONCall(result);
console.log('GetCandidateTicketCount result:', result1);
````

##### GetTicketCountByTxHash

> Get patch ticket count by transaction hashs

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|txHashs|String|required| Hexadecimal list of transaction hash numbers, starting with `0x`. Multiple transaction hashs cancatenating with ':'|

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

Sample data of param1:
```
{
	"code":0,                              // error code
	"data":"{"txHash1":10,"txHash2":1}"    // number of valid tickets
}
```

###### Example

````js
const txHashs = '0x1e869a12c54dbab0889ab3588425bf1908956ec5fd757a457a8d4f16a2bd7b94:0x85414b7d2dfc20ee10318c2c5e39fbca4533bc26bac9b488f7fdcd447b5eace8'// Multiple transaction hashs cancatenating with ':'

const data = ticketContract.GetTicketCountByTxHash.getPlatONData(txHashs)

const result = web3.eth.call({
    from: wallet.address,
    to: ticketContract.address,
    data: data,
});

const result1 = ticketContract.decodePlatONCall(result);
console.log('GetTicketCountByTxHash result:', result1);
````

##### GetCandidateEpoch

> Get epoch of a specific candidate

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|nodeId|String|required| Hexadecimal node ID of the candidate, starting with `0x`|

**Return value or Callback**

| Parameter |Type|Description|
| :------: |:------: |:------: |
|param1|String| data array of log parsing|

Sample data of param1:
```
{
	"code":0,                        // error code
	"data":String                    // error msg, when error return
}
```

###### Example

````js
const nodeId = '0x4f6c8fd10bfb512793f81a3594120c76b6991d3d06c0cc652035cbfae3fcd7cdc3f3d7a82021dfdb9ea99f014755ec1a640d832a0362b47be688bb31d504f62d'// node ID of candidate

const data = ticketContract.GetCandidateEpoch.getPlatONData(nodeId)

const result = web3.eth.call({
    from: wallet.address,
    to: ticketContract.address,
    data: data,
});

const result1 = ticketContract.decodePlatONCall(result);
console.log('getCandidateEpoch result:', result1);
````

### web3

----

### web3 eth related (standard SON RPC)
- For api usage please refer to [web3j github](https://github.com/ethereum/wiki/wiki/JavaScript-API)

### Newly added interfaces

#### contract

##### interface declaration

    web3.eth.contract(abi)

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|abi|Array|required|Application binary object|

**Return value or Callback**

`Object` - contract object

##### Example

````js
const abi=[
    {
        "name": "initWallet",
        "inputs": [
            {
                "name": "owner",
                "type": "string"
            },{
                "name": "required",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "submitTransaction",
        "inputs": [
            {
                "name": "destination",
                "type": "string"
            },{
                "name": "from",
                "type": "string"
            },{
                "name": "value",
                "type": "uint64"
            },{
                "name": "data",
                "type": "string"
            },{
                "name": "len",
                "type": "uint64"
            },{
                "name": "time",
                "type": "uint64"
            },{
                "name": "fee",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "false",
        "type": "function"
    },{
        "name": "confirmTransaction",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "revokeConfirmation",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "executeTransaction",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [],
        "constant": "false",
        "type": "function"
    },{
        "name": "isConfirmed",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "int32"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getRequired",
        "inputs": [],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getConfirmationCount",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getTransactionCount",
        "inputs": [
            {
                "name": "pending",
                "type": "int32"
            },{
                "name": "executed",
                "type": "int32"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "uint64"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getTransactionList",
        "inputs": [
            {
                "name": "from",
                "type": "uint64"
            },{
                "name": "to",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getOwners",
        "inputs": [],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    },{
        "name": "getConfirmations",
        "inputs": [
            {
                "name": "transactionId",
                "type": "uint64"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    },
    {
        "name": "getTransactionIds",
        "inputs": [
            {
                "name": "from",
                "type": "uint64"
            },{
                "name": "to",
                "type": "uint64"
            },{
                "name": "pending",
                "type": "int32"
            },{
                "name": "executed",
                "type": "int32"
            }
        ],
        "outputs": [
            {
                "name": "",
                "type": "string"
            }
        ],
        "constant": "true",
        "type": "function"
    }
]

const MyContract = web3.eth.contract(abi);

const myContractInstance = MyContract.at('0x91b0ac240b62de2f0152cac322c6c5eafe730a84');

````

````js
var MyContract = web3.eth.contract(abi);

// instantiate by address
var contractInstance = MyContract.at([address]);

// deploy new contract
var contractInstance = MyContract.new([contructorParam1] [, contructorParam2], {data: '0x12345...', from: myAccount, gas: 1000000});

// Get the data to deploy the contract manually
var contractData = MyContract.new.getData([contructorParam1] [, contructorParam2], {data: '0x12345...'});
// contractData = '0x12345643213456000000000023434234'
````

#### contract.getPlatONData

##### interface declaration

    MyContract.myMethod.getPlatONData(param1 [, param2, ...])

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|param1/param2/...|String/Number|required| 0 or more functions as parameters|

**Return value or Callback**
`Object` - a contract object

##### Example

to be added.

#### contract.decodePlatONCall

> Return the result by analyzing call with the output's type under fnName in abi

##### interface declaration

    MyContract.decodePlatONCall( callResult, [fnName])

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|callResult|String|required|application binary interface object|
|fnName|String|optional|if not set, callResult will use string to analyze callResult. If set, it will find that the name in abi equals to outputs under fnName, and analyze based on outputs.type.|


**Return value or Callback**

`code` - 0  indicate success
`data` - data after analyze

##### Example

````js
    MyContract.decodePlatONCall( '0x',)
````

#### contract.decodePlatONLog

> Analyze a part of logs in transaction receipt

##### interface declaration

    MyContract.decodePlatONLog(log)

###### Parameter description

| Parameter |Type|Attribute|Description|
| :------: |:------: |:------: | :------: |
|log||Object|a part of logs in transaction receipt|

**Return value or Callback**
`Array` - analyzed log array

##### Example

````js
const data=web3.eth.getTransactionReceipt('0xb1335d4db521ddc0b390448f919e5b5af1258b29e7ab4e0d68b0ef315af0cf5f');

let res = myContractInstance.decodePlatONLog(data.logs[0]);
````

- End -

