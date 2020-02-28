## Introduce

Smart contract ,is a program ,which can run in the blockchain. smart contract has the features below:

* Anybody can develop smart contract , which stored by the contract accout of blockchain. the accout controled by private key is called externel account.

* Contract account cannot run by itself, before executing a smart contract, it need externel account send transaction to contract account.
	
Solidity language is a contract-oriented high-level programming language created to implement smart contracts. Its syntax is similar to JavaScript's high-level programming language. It is designed to generate virtual machine code in a compiled manner. Using it is easy to create smart contracts. However, as a decentralized smart contract running on the Internet in real sense, it has the following features:

* The PlatON is based on an account model,so Solidity provides a special Address type, which is used to locate user accounts, locate smart contracts, and locate smart contract codes.

* Because Solidity embedded framework supports payment, and provides some keywords, such as payable, it can directly support payment at the Solidity language level, which is very simple to use.

* Data storage uses the blockchain on the network, and every state of the data can be stored permanently, so when developing the Solidity Contract, it is necessary to determine whether the variable uses memory or the blockchain.

* The Solidity operating environment is on a decentralized network, with special emphasis on the way Ethereum smart contracts or function execution is called. Because a simple function call turned into a node code execution on the network, it is a completely distributed programming environment.

* The abnormality mechanism of the Solidity language is also very different. Once an exception occurs, all executions will be retracted. This is mainly to ensure the atomicity of smart contract execution to avoid data inconsistencies in the intermediate state.

This tutorial is mainly to guide users to create a simple HelloWorld smart contract using solidity language on PlatON, compile, deploy, and call this contract through platon-truffle. If you want to use a richer API you can refer to [Java SDK ](/zh-cn/Development/[Chinese-Simplified]-Java-SDK.md) and  [JS SDK](/zh-cn/Development/[Chinese-Simplified]-JS-SDK.md)

## Platon-truffle Introduce 

Platon-truffle is a tool provided by PlatON that can compile, deploy, and invoke smart contracts locally. For specific installation and usage manuals, refer to:

- Platon-truffle develop tools[specific installation](https://github.com/PlatONnetwork/platon-truffle/tree/feature/evm)
- Platon-truffle develop tools[usage manuals](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)



## Create HelloWorld Contract

```
pragma solidity ^0.5.13;

contract HelloWorld {
    
    string name;
    
    function setName(string memory _name) public returns(string memory){
        name = _name;
        return name;
    }
    
    function getName() public view returns(string memory){
        return name;
    }
}
```

Contract Files Description:

- pragma solidity ^0.5.13
  -	`pragma solidity`: solidity version description
  -	`0.5.13`：solidity version
  -	`^` ：Indicates upward compatibility, that is, it can be compiled with a compiler above 0.5.13
- contract HelloWorld
  -	`contract`：contract keyword
  -	`HelloWorld`：contract name
- string name
  -	`name`：contract state variables
  -	`string`：the type of contract state variables 
- function setName(string memory _name) public returns(string memory)
  -	`function`：function keyword
  -	`setName`：function name
  -	`memory`：declare the storage location of param name（ function input parameters and output parameters  must be declared as memory when the parameters type is string）
  -	`_name`：the  local variables
  -	`public`：declare the visibility of the function
  -	`name` = _name：Assignment the local variable to state variable
- function getName() public view returns(string memory)
  -	`view`:this keyword means the function cannot change the blockchain state，which Mainly used for query

## Compile HelloWorld Contract 

**Step1.**  creat new directory for HelloWorld project 

```
mkdir HelloWorld && cd HelloWorld
```

**Step2.**  init project

```
truffle init
```
After the command is executed, project directory structure is as follows:

- `Contracts/`: Solidity contract directory

- `Migrations/`:  depoly file directory

- `Test/`: test script directory

- `Truffle-config.js`: platon-truffle config

**Step3.**  move HelloWorld contract compiled in to HelloWorld/contracts/

```
ls contracts/
```
- HelloWorld.sol 

**Step4.**  fix compile version same as the version setted  in truffle-config.js

```
vim truffle-config.js
```

Truffle-config.js content is  as follows:
```
compilers: {
      solc: {
            version: "^0.5.13",    // same as the version declared in HelloWorld.sol
      }
}
```

**Step5.**  compile contract

```
truffle compile
```
After the command is executed, project directory structure is as follows:

- `Build/`: Solidity contract directory after compiled

- `Build/contracts/HelloWorld.json`: the compiled file Corresponding with HelloWorld.sol  


## Deploly HelloWorld Contract

**Step1.** create deploy script 

```
cd migrations/ && touch 2_initial_helloword.js
```
Suggest replacing script  name  with contract name, for example the deploy script  of HelloWorld contract :2_initial_helloword.js,content is as follows：
```
const helloWorld = artifacts.require("HelloWorld"); //artifacts.require specify deployment contract
	module.exports = function(deployer) {
       deployer.deploy(helloWorld); 
};
```

**Step2.** setting config  information for blockchain in truffle-config.js

```
vim truffle-config.js
```
Set blockchain network  info
```
networks: {
	development: {
       host: "10.1.1.6",     // blockchain server address
       port: 8806,            // server port
       network_id: "*",       // Any network (default: none)
       from: "0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e", //the accout address of deploying contract
       gas: 90000000,
       gasPrice: 50000000004,
	},
}
```

**Step3.**  deploy contract

```
truffle migrate
```

If deploy success，you wil see log info as follows:
```
2_initial_helloword.js
Deploying 'HelloWorld'
transaction hash:    0x2bb5c7f6202225554a823db410fb16cf0c8328a51391f24fb9052a6a8f3033e3 //the transaction hash for deploy contract
Blocks: 0            Seconds: 0
contract address:    0x714E74eEc4b63D9DB72cbB5F78CDD5b5bb60F9dc  //contract address
block number:        142522  //the number of block which stores the transaction 
block timestamp:     1581667696206
account:             0xF644CfC3b0Dc588116D6621211a82C1Ef9c62E9e //the account for deploy contract
balance:             90000000.867724449997417956  //the balance of the account
gas used:            149247 //gas cost of the transaction
gas price:           50.000000004 gVON
value sent:          0 LAT
total cost:          0.007462350000596988 LAT
Saving migration to chain.
Saving artifacts
Total cost:     0.007462350000596988 LAT
```

## Call HelloWorld Contract

**Step1.**  enter the platon-truffle console

```
truffle console
```
- You can execute cmd in console

**Step2.**  create contract object

```json
var abi = [{"constant":false,"inputs":[{"internalType":"string","name":"_name","type":"string"}],"name":"setName","outputs":[{"internalType":"string","name":"","type":"string"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"getName","outputs":[{"internalType":"string","name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"}]; //you can refet to HelloWorld/build/contracts/HelloWorld.json

var contractAddr = '0x9A5015F9A3728ff64f401b9B93E98078BdD48FD1';//contract address
var helloWorld = new web3.eth.Contract(abi,contractAddr); 
```

Description： 

- `abi` the interface provided by the contract to external calls，the abi  in the file compiled ：`HelloWorld/build/contracts/HelloWorld.json` 
- `contractAddr` contract address
- `helloWorld`  contract object created

**Step3.**  call contract

```javascript
helloWorld.methods.setName("hello world").send({
	from: '0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e'
 }).on('receipt', function(receipt) {
 	console.log(receipt);
 }).on('error', console.error);

```

Description：

- `helloWorld` the contract object created
- `methods`  specify the call method
- `setName` the function of the HelloWorld contract，which has a parameter as `hello world`
- `from` the address of caller 
- `on` listen on the result of the contract method executed. if fail, it will print the error info.if success ,the console will print the receipt as belows:

```
{ 
  blockHash:'0x3ae287d1e745e30d0d6c95d5220cc7816cda955e7b2f013c6a531ed95028a794', //the hash of block the transaction located
  blockNumber: 159726, 
  contractAddress: null,
  cumulativeGasUsed: 44820,
  from: '0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e', //the address of caller
  gasUsed: 44820, //gas cost
  logsBloom:
   '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  status: true,
  to: '0x9a5015f9a3728ff64f401b9b93e98078bdd48fd1', //contract address
  transactionHash:'0xb7a41f72d555d4a2d9f2954fbdc5bbbb4c5ce89c836f8704276419ed416b3866', 
  transactionIndex: 0,
  events: {} 
}
```

**Step4.**  query contract

```javascript
helloWorld.methods.getName().call(null,function(error,result){console.log("name is:" + result);})  
```
Description：

- `helloWorld` the contract object created
- `methods` specify the call method
- `getName` the function of the HelloWorld contract，which has no  parameter 
- `call` specify query method
- `function` callback result,we can use console.log to print info.

## FAQ 

* Q. How many commands in platon-truffle？

> Refer to  platon-truffle develop guide[Reference here](https://platon-truffle.readthedocs.io/en/v0.1.0/index.html)

* Q. Why contract syntax cannot verify?

> Solidity 0.4.x has a great different with 0.5.x，detail info refer to [Reference here](https://solidity.readthedocs.io/en/develop/)

* Q.  Why truffle doesn't compile?

> 1. Confirm the contract version same as the version specified in the truffle-config.js.
> 2. Contract syntax be writed in a wrong way.


* Q. Why the contract can not deploy by truffle migrate?

> 1. Confrim the blockchain network info be configured correctly.
> 2. Confirm the account address be configured correctly.

* Q. Deploying a contract with a parameter constructor using the command `truffle migrate` failed.

> For example, A.sol 

```
...
Constructor(uint256 a, string memory b, string memory c) public {}
...

```
> 2_initial_A.js configured as follow：
```
const A = artifacts.require("A");  
module.exports = function(deployer) {
        deployer.deploy(ERC200513Token,100,'PLA','PLAT');//pass the corresponding construction parameters
};
```
