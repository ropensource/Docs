**User Guide**  **of ctool**

**Introduction**

ctool: a small tool set that can be used to quickly deploy contracts, send transactions (make contract call), and make other inquiries.

- Windows version

Currently only windows version is available. Versions for other platforms are still in development.

**User Manual**

``` 
$ ctool.exe <command> [--addr contractAddress] [--type txType(default:2)] [--func funcInfo] --abi <abi_path> --code <wasm_path> [--config <config_path>]
```

- **command**  - the command to be executed, including: deploy, invoke, getTxReceipt (get transaction receipt)
- **abi\_path** - path for the ABI file, which is **firstdemo.cpp.abi.json** in the example
- **wasm\_path** - path for the WASM file, which is **firstdemo.wasm** in the example
- **config\_path** - path the configuration file, which is used to configure nodes and account information

**Command options**

- **--addr** : the contract address to be invoked
- **--type** : transaction type.  Value 2 indicates normal transaction, and 5  indicates MPC transaction. Default value is 2 if nothing specified.
- **--func** : function name of current contract to be invoked, such as: --func 'sayHello'

Please note: if the configuration file's path is unspecified in the command line, the program will read the config file - **config.json** - in the current directory.

**Configuration file**

Example:

``` 
{

 "url":"http://127.0.0.1:8545",

 "from":"0x60ceca9c1290ee56b98d4e160ef0453f7c40d219"

}
```

- **url** - the address of PlatON **JSON-RPC**
- **from** - the wallet address of the transaction sender. Please nte that it requires the node to guarantee that the account is already unlocked

**Deploy**  **a contract**

This step is used to demonstrate how to publish a contract. This step need two files, one ending in **.wasm** (the contract binary) file, and another ending in **.json** ( the contract port description). About how to acquire these two files please see: **WASM contract development guide**

``` 
$ ctool.exe deploy --abi ./demo.cpp.abi.json --code ./demo.wasm --config ./config.json

trasaction hash: 0xdb0f9a28fcd447702e8d5961f47144d1ea830979e3c984acc8f72c0dca8bdcfc

contract address: 0x43355c787c50b647c425f594b441d4bd751951c1
```

After the commands executes, it returns the transaction hash and the contract address.

**Sending the transaction**

After successfully deployed the contract, the next step is toinvoke it. Assume that the test contract contains the method **sayHello**. Now we invoke this method:

``` 
$ ctool.exe invoke -addr "0x43355c787c50b647c425f594b441d4bd751951c1" --func 'sayHello("HelloWorld")' --abi ./demo.cpp.abi.json --config ./config.json
```
**Inquiring the result**

Further assume that, in the previous step we used the contract method **sayHello**. Then we will read the values stored by this method. This action of inquiry is called a **call**. Assume the test contract contains **char \* getWorld()**, then the deployment is as follows:

``` 
$ ctool.exe invoke -addr"0x43355c787c50b647c425f594b441d4bd751951c1" --func 'getWorld()' --abi ./demo.cpp.abi.json --config ./config.json
```
The expected resulting output on the screen is: **HelloWorld**.

**Inquiring the transaction receipt**

Sometimes you need to inquire the receipt information after the transaction is sent. You can:

``` 
$ ctool.exe getTxReceipt 0x0b8996dadd6fd821f055affd1f95dbdf718d288a17e4ac5ed4133f3393bca44d(Transaction hash number)
```

**FAQs and Tips**

**Tips**

1. Before using ctool please be sure that your node is functional.
2. Usually the more obvious errors will output onto the terminal after executing ctool.
3. Using ctool to send transactions requires the current account to be unlocked on its associated nodes.

**FAQs**

1. Cannot deploy your contract?

A: Please make sure that Energon setting in the configuration file is reasonable, and that the account is unlocked on associating nodes.

**More questions**

If you have other questions or you can't find the answer here, please submit an issue.