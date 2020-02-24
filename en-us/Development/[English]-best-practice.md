## Introduction 

This guide introduces users to some key points that need to be paid attention to in the development of smart contracts, mainly in the practice of actual development. Users can use this guide to quickly understand how to set a reasonable fee for a transaction, how to avoid losing the fee due to transaction failure, and how to encode a more standardized smart contract.


## Reasonable Cost Setting

When you need to deploy a contract on the main network of PlatON, you need to set a reasonable fee limit. The fee limit refers to the upper limit of the energy consumption cost of smart contract deployment/execution in PlatON. This restriction is mainly accomplished through Gas. Gas is the fuel value of the PlatON network world, which determines the normal operation of the PlatON network ecosystem. Gas is usually used to measure how much "work" is required to perform certain actions, and these workloads are the amount of fees that need to be paid to the PlatON network in order to perform the action. In a simple understanding, Gas is the commission for absenteeism on the network, and is paid by LAT. Any transaction, contract execution, and data storage on the network need to use Gas.

PlatON is similar to Ethereum's blockchain system. It uses `LAT` for payment and maintenance networks. One LAT is divided into:`mLAT/uLAT/gVON/mVON/kVON/VON`, where `VON` is the smallest unit.

Gas consists of two parts: GasLimit and GasPrice. `GasLimit` is the maximum` Gas` consumption (minimum 21,000) that a user is willing to pay to perform an operation or confirm a transaction. GasPrice is the unit price of each Gas.

When a user sends a transaction, `GasLimit` and` GasPrice` are set, and the product of the two (GasLimit * GasPrice`) is the user's transaction cost, and the cost is rewarded to the absentee as a commission.

The higher the GasPrice of the transaction, the higher the execution priority of the transaction and the greater the transaction cost. After each transaction is completed, the remaining unused Gas will be returned to the sender's address account. It is important to note that if the execution of the transaction fails because the GasLimit is set too low, the Gas will not be returned to the user's address at this time, and the user still needs to pay the energy cost for the failed transaction. Therefore, regardless of whether the transaction is executed successfully, the sender of the transaction will need to pay a certain amount of computational fees to absenteeism.

In the PlatON network, the maximum gas limit is `4,700,000` and the minimum is` 22,000`. Too low or too high will cause transaction failure. When deploying large contracts or running complex functions, you can increase the gas limit, such as: `1,000,000`. If it is a normal transfer, set it to the lowest value. The actual value needs to be estimated based on the size and complexity of the contract. Before the contract is released, the interface `platon_estimateGas` can be called to make an approximate estimate to avoid failure due to inadequacy. [Click to view JSON-RPC reference document](https://github.com/PlatONnetwork/Docs/blob/master/en-us/Development/%5BEnglish%5D-JSONRPC-SDK.md)

**LAT Unit Conversion**

| Unit | VON Value | VON |
|:---|:---|:---|
| VON | 1 | 1 VON |
| kVON | 1e3 VON | 1,000  |
| mVON | 1e6 VON | 1,000,000 |
| gVON | 1e9 VON | 1,000,000,000 |
| LAT | 1e18 VON | 1,000,000,000,000,000,000 |
| mLAT | 1e24 VON | 1,000,000,000,000,000,000,000,000 |
| gLAT | 1e27 VON | 1,000,000,000,000,000,000,000,000,000 |

## Avoid Timeouts 

Sending transactions on the `PlatON` network does not have the concept of timeout, but it will eventually stop after reaching the GasLimit upper limit. If the limit value is lower than the consumption required for contract deployment, the transaction sending fails and the corresponding processing fee will be deducted. The fee setting cannot be infinite, because in the network, the block itself has a maximum Gas upper limit. When the `GasLimit` of the transaction exceeds this value, the transaction will not be accepted.

If a `call` is called for a published contract (call is a stateless operation in the contract logic), there is a 5s timeout limit. If the contract logic is not executed within 5s, the virtual machine will time out and force exit Query failed.

To avoid contract-related transaction failures, try breaking large contracts into smaller pieces and referencing each other as needed. To avoid infinite loops, be aware of common pitfalls and recursive calls.

## Punishment For Illegal Operations

If the smart contract is not compiled by a standard valid compiler, or the instruction code is changed at will, the opcode will be invalid. This type of contract not only fails to be deployed and executed successfully, but also generates a full amount (`GasLimit * GasPrice`) penalty. The transaction fee for the current transaction will be deducted. This is a very strong penalty. If the operator does not pay attention At this point, keep retrying, then the cost will be higher and the cost will be heavier.

In general, invalid opcodes have the following conditions:

1. Manually changed the instruction code for the normally compiled contract;
2. The contract compiler version is not consistent with the contract version supported by the network lock;

When operating a contract in the PlatON network, you must first confirm the smart contract version supported by the current network, and then select the compiler of the corresponding version pair.

The normal operation is to use the latest `Truffle`/`PlatON-CDT` officially provided by PlatON to compile/deploy/execute the contract. At the same time, before switching to the main network operation, it must be validated on the test network.


## Coding Standards

### Naming Rules

Basic Rules:

* Use complete descriptive information that accurately describes variables, fields, classes, interfaces, etc.
* Use mixed case (except special characters) to improve the readability of the name.
* Use terminology within the blockchain industry.
* Use as few abbreviations as possible. If you must use them, it is recommended to use public abbreviations and custom abbreviations.
* Avoid using names that are similar or just distinguish between upper and lower case.
* The directory uses lowercase uniformly, without special symbols.
* For smart contracts, the file name is consistent with the contract name.
* The naming suggestion is to use the hump name uniformly.


### Document Format For Smart Contracts
 
File Layout Rules:
 
* Generally more than 1000 lines of program code is difficult to read, try to avoid the situation that the number of lines of code in a file is too long. Each contract document should contain only a single contract class or contract interface.

Order Of Files:

* Notes on files: All contract source files have a note at the beginning, which lists the copyright statement, file name, function description, and creation and modification records of the file.
* File/Package reference: In the contract source file, the first non-comment line is the compiler version, followed by the reference statement.
* Remarks for class or interface: Comments should be made before class and interface definitions, including descriptions of classes and interfaces, latest modifiers, version numbers, reference links, etc.
* The order of member variables: first the public level, then the protection level, and finally the private level.
* Functions: Functions within a contract should be grouped by module, not by scope or access permissions.


### Suggestions

* In the smart contract, to get the value of the state variable modified by the public, there is no need to write a function of `get`.
* In a smart contract, if an anonymous function modified by payable is added to the contract, the contract address can accept LAT transfers.


















