# 目录

- [概览](#概览)
- [快速入门](#快速入门)
  - [安装或引入](#安装或引入)
  - [初始化代码](#初始化代码)
- [合约](#合约)
  - [合约示例](#合约示例)
  - [部署合约](#部署合约)
  - [合约call调用](#合约call调用)
  - [合约sendRawTransaction调用](#合约sendrawtransaction调用)
  - [内置合约](#内置合约)
    - [CandidateContract](#CandidateContract)
    - [TicketContract](#TicketContract)
- [web3](#web3)
  - [web3 eth相关 (标准JSON RPC )](#web3-eth相关-标准json-rpc)
  - [新增的接口](#新增的接口)
    - [contract](#contract)
      - [getPlatONData](#contract.getPlatONData)
      - [decodePlatONCall](#contract.decodePlatONCall)
      - [decodePlatONLog](#contract.decodePlatONLog)

## 概览
> Javascript SDK是PlatON面向js开发者，提供的PlatON公链的js开发工具包

### 快速入门

### 安装或引入

通过node.js引入：

`cnpm i https://github.com/PlatONnetwork/client-sdk-js`

### 初始化代码

然后你需要创建一个web3的实例，设置一个provider。为了保证你不会覆盖一个已有的provider，比如使用Mist时有内置，需要先检查是否web3实例已存在。

```js
if (typeof web3 !== 'undefined') {
    web3 = new Web3(web3.currentProvider);
} else {
    web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));
}
```

### 合约

wasm智能合约的编写及其ABI(wasm文件)和BIN(json文件)生成方法请参考 [WASM合约指南](/zh-cn/development/%5BChinese-Simplified%5D-Wasm%E5%90%88%E7%BA%A6%E5%BC%80%E5%8F%91%E6%8C%87%E5%8D%97.md)。

#### 合约示例

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

#### 部署合约

##### 接口声明

    MyContract.deploy(deployData [, callback])

##### 参数说明

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|deployData |String |必选| 16进制格式的签名交易数据|
|callback|Funciton  |可选|回调函数，用于支持异步的方式执行|

##### 示例

````js
const Web3 = require('web3'),
    fs = require('fs'),
    path = require('path'),
    Tx = require('ethereumjs-tx'),
    abi = require('./multisig.cpp.abi.json') //应用程序二进制接口
    ;

const provider = 'http://localhost:6789',
    privateKey='fd098d39e56115762cf28d7d223a4303a42a42451da6e7da37cb40a81a246d98',//私钥
    address='0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b'

const web3 = new Web3(new Web3.providers.HttpProvider(provider))

const MyContract  = web3.eth.contract(abi)

const source = fs.readFileSync(path.join(__dirname, './demo.wasm'));//WebAssembly的二进制代码 bin

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
            console.log("contract deploy transaction hash: " + myContract.transactionHash) // 部署合约的交易哈希值
        } else {
            // 合约发布成功
            console.log("contract deploy transaction address: " + myContract.address)// 部署合约的地址
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

#### 合约call调用

> 调用合约函数，返回常量值

##### 接口声明

    web3.eth.call(Object [, defaultBlock] [, callback])

##### 参数说明

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|Object |String |必选| 返回一个交易对象，同`web3.eth.sendTransaction`。与`sendTransaction`的区别在于，`from`属性是可选的|
|defaultBlock|Number/String |可选|如果不设置此值使用`web3.eth.defaultBlock`设定的块，否则使用指定的块|
|callback|Funciton  |可选|回调函数，用于支持异步的方式执行|

##### 示例

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

#### 合约sendRawTransaction调用

> 发送通过私钥签名的交易

##### 接口声明

    web3.eth.sendRawTransaction(signedTransactionData [, callback])

##### 参数说明

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|signedTransactionData |String |必选|16进制格式的签名交易数据|
|callback|Funciton  |可选|回调函数，用于支持异步的方式执行|

##### 示例

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

#### 内置合约

##### CandidateContract

> PlatON经济模型中候选人相关的合约接口

##### 加载合约

````js
const Web3 = require('web3'),
    wallet = require('../owner.json'),//钱包文件
    toObj=require('./toObj'),//详细代码在下方
    sign = require('./sign'),//签名 详细代码在下方
    abi = require('../abi/candidateConstract.json'),//候选人相关abi
    getTransactionReceipt = require('./getTransactionReceipt'),//详细代码在下方
    candidateContractAddress='0x1000000000000000000000000000000000000001';

const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:6789'));

const calcContract = web3.eth.contract(abi),
    candidateContract = calcContract.at(candidateContractAddress);

function getParams(data = '', value = "0x0") {
    const nonce = web3.eth.getTransactionCount(wallet.address);
    value = web3.toHex(web3.toWei(value, 'ether'));

    const params = {
        from: wallet.address,//钱包地址
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

./toObj.js

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

./sign.js文件

````js
const Tx = require('ethereumjs-tx');

module.exports = (privateKey, data) => {
    if (!privateKey || !data) {
        throw new Error(`sign参数异常`);
    }

    const key = new Buffer(privateKey, 'hex'),
        tx = new Tx(data);

    tx.sign(key);

    const serializeTx = tx.serialize(),
        result = '0x' + serializeTx.toString('hex');

    ;
};
````

./getTransactionReceipt.js

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
            fn(1001, '合约异常，失败');
        }
    } else {
        if (wrapCount--) {
            id = setTimeout(() => {
                getTransactionReceipt(hash, fn);
            }, 1000);
        } else {
            fn(1000, '超时');
            id = '';
        }
    }
}

module.exports = getTransactionReceipt
````

##### CandidateDeposit

> 节点候选人申请/增加质押

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeId|String|必选|节点id, `16`进制格式， `0x`开头|
|owner|String|必选|质押金退款地址, `16`进制格式， `0x`开头|
|fee|String|必选|出块奖励佣金比，以10000为基数(eg：5%，则fee=500)|
|host|String|必选|节点IP|
|port|String|必选|节点P2P端口号|
|Extra|String|必选|附加数据,json格式字符串类型|
|value|BigInteger|必选|质押金额|

备注：质押金额必须大于等于1000000 Energon,value >= 1000000 Energon

Extra描述

```
{
    "nodeName":string,                     //节点名称
    "officialWebsite":string,              //官网 http | https
    "nodePortrait":string,                 //节点logo http | https
    "nodeDiscription":string,              //机构简介
    "nodeDepartment":string                //机构名称
}
```

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
    "Ret":boolean,                         //是否成功 true:成功  false:失败
    "Data":string                          //返回数据
    "ErrMsg":string                        //错误信息，失败时存在
}
```

###### 示例

````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';

let nodeId = '0x15245d4dceeb7552b52d70e56c53fc86aa030eab6b7b325e430179902884fca3d684b0e896ea421864a160e9c18418e4561e9a72f911e2511c29204a857de71a', //[64]byte 节点ID(公钥)
    owner = '0xf216d6e4c17097a60ee2b8e5c88941cd9f07263b', //[20]byte 质押金退款地址
    fee = 500, //uint32 出块奖励佣金比，以10000为基数(eg：5 %，则fee = 500)
    host  = '0.0.0.0', //string 节点IP
    port='38794', //string 节点端口号
    Extra = web3.toHex({
        nodeName: '节点名称',
        nodeDiscription: '节点简介',
        nodeDepartment: '机构名称',
        officialWebsite: 'https://www.platon.network',
        nodePortrait: 'http://192.168.9.86:8082/group2/M00/00/00/wKgJVlr0KDyAGSddAAYKKe2rswE261.png',
        time: Date.now(), //加入时间
    }), //string 附加数据(有长度限制，限制值待定)
    value = 1000000;//质押金额

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
                console.log(`节点候选人申请 / 增加质押成功`);
            } else {
                console.log(`节点候选人申请 / 增加质押失败`);
            }
        } else {
            console.log(`节点候选人申请 / 增加质押失败`)
        }
    } else {
        console.warn(`节点候选人申请 / 增加质押异常`);
    }
});
````

##### CandidateApplyWithdraw

> 节点质押金退回申请，申请成功后节点将被重新排序，发起的地址必须是质押金退款的地址 from==owner

###### 参数

|参数名|类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeId|String|必选|节点id, 16进制格式， 0x开头|
|withdraw|String|必选|退款金额 (单位：E)|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"ErrMsg":string                        //错误信息，失败时存在
}
```

###### 示例

````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';
const nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a', //[64]byte 节点ID(公钥)
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
                console.log(`节点质押金退回申请成功`);
            } else {
                console.log(`节点质押金退回申请失败`);
            }
        } else {
            console.log(`节点质押金退回申请失败`)
        }
    } else {
        console.warn(`节点质押金退回申请异常`)
    }
})
````

##### CandidateWithdraw

> 节点质押金提取，调用成功后会提取所有已申请退回的质押金到owner账户。

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeId|String|必选|节点id, 16进制格式， 0x开头|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"ErrMsg":string                        //错误信息，失败时存在
}
```

###### 示例

````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';
const nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a' //[64]byte 节点ID(公钥)
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
                console.log(`节点质押金提取成功`);
            } else {
                console.log(`节点质押金提取失败`);
            }
        } else {
            console.log(`节点质押金提取失败`)
        }
    } else {
        console.warn(`节点质押金提取异常`);
    }
})
````

##### SetCandidateExtra

> 设置节点附加信息, 发起的地址必须是质押金退款的地址 from==owner

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|Extra|String|必选|附加数据，16进制字符串|

Extra描述

```
{
    "nodeName":string,                     //节点名称
    "officialWebsite":string,              //官网 http | https
     "nodePortrait":string,                 //节点logo http | https
    "nodeDiscription":string,              //机构简介
    "nodeDepartment":string                //机构名称
}
```
**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"ErrMsg":string                        //错误信息，失败时存在
}
```

###### 示例

````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';
let nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a', //[64]byte 节点ID(公钥)
    owner = wallet.address, //[20]byte 质押金退款地址
    time = Date.now(),
    Extra =JSON.stringify({
        nodeName: '节点名称',
        nodeDiscription: '节点简介' + time,
        nodeDepartment: '机构名称' + time,
        officialWebsite: 'www.platon.network',
        nodePortrait: 'URL',
        time: time, //加入时间
    }) //string 附加数据(有长度限制，限制值待定)

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
            console.log(`设置节点附加信息成功`);
        } else {
            console.log(`设置节点附加信息失败`);
        }
    } else {
        console.log(`设置节点附加信息失败`)
    }
})
````

##### GetCandidateWithdrawInfos

> 获取节点申请的退款记录列表

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeId|String|必选|节点id, 16进制格式， 0x开头|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|string|String|解析后的日志数组|

```
{
    "Ret": true,
    "ErrMsg": "success",
    "Infos": [{                        //退款记录
        "Balance": 100,                //退款金额
        "LockNumber": 13112,           //退款申请所在块高
        "LockBlockCycle": 1            //退款金额锁定周期
    }]
}
```

###### 示例

````js
const nodeId = '0xeebeaa496d954f8ee864e6460719755398f1e5b36e7a0c911f527fe3247b02a0a4db17aa59c5235e923602df1aeb26042149b8d2fd71cf990046b08d3b323b9a' //[64]byte 节点ID(公钥)
const data = candidateContract.GetCandidateWithdrawInfos.getPlatONData(nodeId)

const result = web3.eth.call({
    from: wallet.address,
    to: candidateContract.address,
    data: data,
});

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log('获取节点申请的退款记录列表结果:', result2);
````

##### GetCandidateDetails

> (批量)获取候选人信息

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeId|String|必选|节点id, `16`进制格式, `0x`开头,多个`nodeId`通过":"拼接的字符串|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"code":0,                         //错误码
	"data":"[]"                    //json格式字符串
}
```
data描述
```
"[{
    //质押金额
    "Deposit": 200,
    //质押金更新的最新块高
    "BlockNumber": 12206,
    //所在区块交易索引
    "TxIndex": 0,
    //节点Id
    "CandidateId": "6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3",
    //节点IP
    "Host": "192.168.9.76",
    //节点P2P端口号
    "Port": "26794",
    //质押金退款地址
    "Owner": "0xf8f3978c14f585c920718c27853e2380d6f5db36",
    //附加数据
    "Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
    //出块奖励佣金比，以10000为基数(eg：5%，则fee=500)
    "Fee": 500
    //见证票所在交易Hash
    "TxHash": "0xfa0914bc939e76b40f378200cdb2603d342e09553d916aa4e6bee8fbbe273944"
}]"
```

###### 示例

````js
const nodeId = '0x0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47:0xe0b6af6cc2e10b2b74540b87098083d48343805a3ff09c655eab0b20dba2b2851aea79ee75b6e150bde58ead0be03ee4a8619ea1dfaf529cbb8ff55ca23531ed'//多个nodeId通过":"拼接的字符串

const data = candidateContract.GetCandidateDetails.getPlatONData(nodeId)

const result = web3.eth.call({
    from: wallet.address,
    to: candidateContract.address,
    data: data,
});

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log('获取候选人信息结果:', result2);
````

##### GetCandidateList

> 获取所有入围节点的信息列表

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |

**返回值 或 回调**

`string` - `String` json格式字符串

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

###### 示例

````js
const data = candidateContract.GetCandidateList.getPlatONData()

    const result = web3.eth.call({
        from: wallet.address,
        to: candidateContract.address,
        data: data,
    });

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log(`所有入围节点的信息列表:`, result2);
````

##### GetVerifiersList

> 获取参与当前共识的验证人列表

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |

**返回值 或 回调**

`string` - `String` json格式字符串

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

###### 示例

````js
 const data = candidateContract.GetVerifiersList.getPlatONData()

const result = web3.eth.call({
    from: wallet.address,
    to: candidateContract.address,
    data: data,
});

const result1 = candidateContract.decodePlatONCall(result);
const result2 = toObj(result1.data)
console.log('获取参与当前共识的验证人列结果:', result2);
````

##### TicketContract

> PlatON经济模型中票池相关的合约接口

##### 加载合约

````js
const Web3 = require('web3'),
    wallet = require('../owner.json'),//钱包文件
    sign = require('./sign'),//签名 详细代码在下方
    abi = require('../abi/ticketContract.json'),//票池相关abi
    getTransactionReceipt = require('./getTransactionReceipt'),//详细代码在下方
    ticketContractAddress='0x1000000000000000000000000000000000000002';//票池合约地址

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

./sign.js文件

````js
const Tx = require('ethereumjs-tx');

module.exports = (privateKey, data) => {
    if (!privateKey || !data) {
        throw new Error(`sign参数异常`);
    }

    const key = new Buffer(privateKey, 'hex'),
        tx = new Tx(data);

    tx.sign(key);

    const serializeTx = tx.serialize(),
        result = '0x' + serializeTx.toString('hex');

    return result;
};
````

./getTransactionReceipt.js

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
            fn(1001, '合约异常，失败');
        }
    } else {
        if (wrapCount--) {
            id = setTimeout(() => {
                getTransactionReceipt(hash, fn);
            }, 1000);
        } else {
            fn(1000, '超时');
            id = '';
        }
    }
}

module.exports = getTransactionReceipt
````

##### GetTicketPrice

> 获取当前的票价,单位是`E`

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"code":Number,                    /错误码
	"data":String                    //当前票价(单位为E)
}
```

###### 示例

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

> 获取票池剩余票数量

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"code":Number,                    /错误码
	"data":String                    //剩余票数量
}
```

###### 示例

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

> 购买选票，投票给(已存在的)候选人。发送交易的value为 购票数量 * 选票单价

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|count|Number|必选|购票数量|
|price|Number|必选|选票单价(单位为`E`)|
|nodeId|String|必选|节点id, `16`进制格式， `0x`开头|


**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"ErrMsg":string                        //错误信息，失败时存在
    "Data":string                          //返回数据（成功选票的数量 + ":" + 成交票价）
```

###### 示例

````js
const privateKey = '099cad12189e848f70570196df434717c1ccc04f421da6ab651f38297a065cb7';

const
    count = 1,//购票数量
    price = 100000000000000000000,//选票单价 使用GetTicketPrice 接口查询当前票价
    nodeId = '0x0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47'//候选人节点Id

const data = ticketContract.VoteTicket.getPlatONData(count, price, nodeId, {
    transactionType: 1000//交易类型
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
                console.log(`成功选票的数量:${arr[0]},成交票价:${arr[1]}`)
            }
        } else {
            console.warn(`购买选票失败`);
        }
    } else {
        console.warn(`购买选票失败`);
    }
});
````

##### GetCandidateTicketCount

> (批量)获取指定候选人的有效选票数量

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeIds|String|必选|候选人节点Id,`16`进制格式,`0x`开头,多个nodeId通过":"拼接的字符串|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|json格式字符串|

param1描述
```
{
	"code":0,                              //错误码
	"data":"{"nodeId1":10,"nodeId2":1}"    //选票数量
}
```

###### 示例

````js
const nodeIds = '0x0abaf3219f454f3d07b6cbcf3c10b6b4ccf605202868e2043b6f5db12b745df0604ef01ef4cb523adc6d9e14b83a76dd09f862e3fe77205d8ac83df707969b47:0xe0b6af6cc2e10b2b74540b87098083d48343805a3ff09c655eab0b20dba2b2851aea79ee75b6e150bde58ead0be03ee4a8619ea1dfaf529cbb8ff55ca23531ed'//多个nodeId通过":"拼接的字符串

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

> (批量)获取交易的有效选票数量

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|txHashs|String|必选|交易`txHash`列表, `16`进制格式， `0x`开头，多个交易`txHash`通过":"拼接的字符串|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"code":0,                              //错误码
	"data":"{"txHash1":10,"txHash2":1}"    //有效选票数量
}
```

###### 示例

````js
const txHashs = '0x1e869a12c54dbab0889ab3588425bf1908956ec5fd757a457a8d4f16a2bd7b94:0x85414b7d2dfc20ee10318c2c5e39fbca4533bc26bac9b488f7fdcd447b5eace8'//多个txHash通过":"拼接的字符串

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

> 获取指定候选人的票龄

###### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|nodeId|String|必选|节点id, `16`进制格式， `0x`开头|

**返回值 或 回调**

| 参数名 |类型|参数说明|
| :------: |:------: |:------: |
|param1|String|解析后的日志数组|

param1描述
```
{
	"code":0,                        //错误码
	"data":String                    //错误信息，失败时存在
}
```

###### 示例

````js
const nodeId = '0x4f6c8fd10bfb512793f81a3594120c76b6991d3d06c0cc652035cbfae3fcd7cdc3f3d7a82021dfdb9ea99f014755ec1a640d832a0362b47be688bb31d504f62d'//候选人节点Id

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

### web3 eth相关 (标准JSON RPC )
- api的使用请参考[web3j github](https://github.com/ethereum/wiki/wiki/JavaScript-API)

### 新增的接口

#### contract

##### 接口声明

    web3.eth.contract(abi)

##### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|abi|Array|必选|应用程序二进制接口对象|

**返回值 或 回调**

`Object` - 一个合约对象

##### 示例

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

##### 接口声明

    MyContract.myMethod.getPlatONData(param1 [, param2, ...])

##### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|param1/param2/...|String/Number|必选|零或多个函数参数|

**返回值 或 回调**

`Object` - 一个合约对象

##### 示例

#### contract.decodePlatONCall

> 根据abi中fnName下的outputs类型解析call返回结果

##### 接口声明

    MyContract.decodePlatONCall( callResult, [fnName])

##### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|callResult|String|必选|应用程序二进制接口对象|
|fnName|String|可选|不传，callResult将string解析callResult,如果传了，会找到abi中name等于fnName下的outputs，按照outputs.type解析|

**返回值 或 回调**

`code` - 0表示成功
`data` - 解析出来的数据

##### 示例

````js
    MyContract.decodePlatONCall( '0x',)
````

#### contract.decodePlatONLog

> 解析交易回执中logs的某一项

##### 接口声明

    MyContract.decodePlatONLog(log)

##### 参数

| 参数名 |类型|属性|参数说明|
| :------: |:------: |:------: | :------: |
|log||Object|交易回执中logs的某一项|

**返回值 或 回调**

`Array` - 解析后的日志数组

##### 示例

````js
const data=web3.eth.getTransactionReceipt('0xb1335d4db521ddc0b390448f919e5b5af1258b29e7ab4e0d68b0ef315af0cf5f');

let res = myContractInstance.decodePlatONLog(data.logs[0]);
````

