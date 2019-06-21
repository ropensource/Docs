-   [概览](#概览)
-   [快速入门](#快速入门)
    -   [安装或引入](#安装或引入)
        -   [环境要求](#环境要求)
        -   [CocoaPods](#cocoapods)
    -   [初始化代码](#初始化代码)
-   [合约](#合约)
    -   [合约示例](#合约示例)
    -   [部署合约](#部署合约)
    -   [合约call调用](#合约call调用)
    -   [合约sendRawTransaction调用](#合约sendrawtransaction调用)
    -   [合约Event](#合约event)
    -   [内置合约](#内置合约)
        -   [CandidateContract](#candidatecontract)
        -   [TicketContract](#ticketcontract)
-   [web3](#web3)
    -   [web3 eth相关 (标准JSON RPC )](#web3-eth相关-标准json-rpc)

# 概览
> PlatON Swift SDK是PlatON面向Swift开发者，提供的PlatON公链的Swift开发工具包。

# 快速入门

## 安装或引入

### 环境要求
1. swift4.0以上，iOS 9.0以上。

### CocoaPods

2. 在Podfile文件中添加引用
```
pod 'platonWeb3', '~> 0.6.0'
```


## 初始化代码
```
let web3 : Web3 = Web3(rpcURL: "http://192.168.1.100:6789")
```

# 合约

## 合约示例

```
#include <stdlib.h>
#include <string.h>
#include <string>
#include <platon/platon.hpp>

namespace demo {
    class FirstDemo : public platon::Contract
    {
        public:
            FirstDemo(){}
      
            /// 实现父类: platon::Contract 的虚函数
            /// 该函数在合约首次发布时执行，仅调用一次
            void init() 
            {
                platon::println("init success...");
            }

            /// 定义Event.
            PLATON_EVENT(Notify, uint64_t, const char *)

        public:
            void invokeNotify(const char *msg)
            { 
                // 定义状态变量
                platon::setState("NAME_KEY", std::string(msg));
                // 日志输出
                platon::println("into invokeNotify...");
                // 事件返回
                PLATON_EMIT_EVENT(Notify, 0, "Insufficient value for the method.");
            }

            const char* getName() const 
            {
                std::string value;
                platon::getState("NAME_KEY", value);
                // 读取合约数据并返回
                return value.c_str();
            }
    };
}

// 此处定义的函数会生成ABI文件供外部调用
PLATON_ABI(demo::FirstDemo, invokeNotify)
PLATON_ABI(demo::FirstDemo, getName)
```

## 部署合约


### **`platonDeployContract`**


**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| abi | String  | 合约abi |
| bin | Data | 合约bin |
| sender  | String  | 账户地址                   |
| privateKey  | String  | 私钥，需要与账户地址对应                   |
| gasPrice  | BigUInt  | 手续费用，Energon价格                   |
| gas  | BigUInt  | 手续费用，Energon数量                   |
| estimateGas  | Bool  | 是否估算gas                   |
| waitForTransactionReceipt  | Bool  | 是否等待交易回执返回                   |
| timeout  | dispatch_time_t  | 超时时间 时间秒                  |                 |
| completion  | ContractDeployCompletion  | 回调闭包                   |

ContractDeployCompletion定义如下

```
public typealias ContractDeployCompletion = (
_ result : PlatonCommonResult,                  //执行结果
 _ transactionHash: String?                     //交易hash
_ address : String?,                            //合约地址
_ receipt: EthereumTransactionReceiptObject?    //交易回执
) -> ()
```

示例：

```
    func deploy(completion: () -> Void){
        print("begin deploy")
        let bin = self.getBIN()
        let abiS = self.getABI()
        web3.eth.platonDeployContract(abi: abiS!, bin: bin!, sender: sender, privateKey: privateKey, gasPrice: gasPrice, gas: gas, estimateGas: false, waitForTransactionReceipt: true, timeout: 20, completion:{
            (result,hash,contractAddress,receipt) in
            
            switch result{
            case .success:
                self.contractAddress = contractAddress
                print("deploy success, contractAddress: \(String(describing: contractAddress))")
            case .fail(let code, let errorMsg):
                print("error code: \(String(describing: code)), msg:\(String(describing: errorMsg))")
            }
        })
    }
```

## 合约call调用

### **`platonCall`**


**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| code | ExecuteCode  | 交易类型，普通合约传ContractExecute |
| contractAddress | String | 合约地址 |
| functionName  | String  | 函数名称                   |
| from  | String  | 合约调用账户地址，可为空                   |
| params  | [Data]  | 参数，如果无参，传[]                   |
| outputs  | [SolidityParameter]  | 返回值类型                   |
| completion  | ContractCallCompletion  | 回调闭包                   |

ExecuteCode含义如下

```
public enum ExecuteCode {
case Transfer           //主币转账交易
case ContractDeploy     //合约发布
case ContractExecute    //合约调用
case Vote               //投票
case Authority          //权限
case MPCTransaction     //MPC交易
case CampaignPledge     //竞选质押
case ReducePledge       //减持质押   
case DrawPledge         //提取质押
case InnerContract      //内置合约调用
}
```

ContractCallCompletion定义如下

```
public typealias ContractCallCompletion = (
_ result : PlatonCommonResult,      //执行结果
_ data : AnyObject?                 //返回数据
) -> ()
```

示例：

```
    func getName(){
        guard contractAddress != nil else {
            print("deploy contract first!")
            return
        }
        let paramter = SolidityFunctionParameter(name: "whateverkey", type: .string)
        web3.eth.platonCall(code: ExecuteCode.ContractExecute, contractAddress: self.contractAddress!, functionName: "getName", from: nil, params: [], outputs: [paramter]) { (result, data) in
            switch result{
            case .success:
                if let dic = data as? Dictionary<String, String>{
                    print("return: \(String(describing: dic["whateverkey"]))")
                }else{
                    print("return empty value")
                }
            case .fail(let code, let errorMsg):
                print("error code: \(String(describing: code)), msg:\(String(describing: errorMsg))")
            }
        }
    }
```

## 合约sendRawTransaction调用

### **`platonSendRawTransaction`**

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| code | ExecuteCode  | 交易类型，普通合约传ContractExecute |
| contractAddress | String | 合约地址 |
| functionName  | String  | 函数名称                   |
| params  | [Data]  | 参数，如果无参，传[]                   |
| sender  | String  | 账户地址                   |
| privateKey  | String  | 私钥，需要与账户地址对应                   |
| gasPrice  | BigUInt  | 手续费用，Energon价格                   |
| gas  | BigUInt  | 手续费用，Energon数量                   |
| estimateGas  | Bool  | 是否估算gas                   |
| completion  | ContractSendRawCompletion  | 回调闭包                   |

ContractSendRawCompletion定义如下

```
public typealias ContractSendRawCompletion = (
_ result : PlatonCommonResult,          //执行结果
_ data : Data?                          //交易hash
) -> ()
```

示例：

```
    func invokeNotify(msg: String){
        
        guard contractAddress != nil else {
            print("ERROR:deploy contract first!")
            return
        }
        
        let msg_s = SolidityWrappedValue.string(msg)
        let msg_d = Data(hex: msg_s.value.abiEncode(dynamic: false)!)
        
        web3.eth.platonSendRawTransaction(code: ExecuteCode.ContractExecute, contractAddress: self.contractAddress!, functionName: "invokeNotify", params: [msg_d], sender: sender, privateKey: privateKey, gasPrice: gasPrice, gas: gas, value: nil, estimated: false) { (result, data) in
            switch result{
            case .success:
                print("transaction success, hash: \(String(describing: data?.toHexString()))")
                self.invokeNotifyHash = data?.toHexString()
            case .fail(let code, let errorMsg):
                print("error code: \(String(describing: code)), msg:\(String(describing: errorMsg))")
            }
        }
    }

```

## 合约Event

### **`platonGetTransactionReceipt`**


**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| txHash | String  | 交易类型，普通合约传ContractExecute |
| loopTime | Int | 轮询次数 |
| completion  | PlatonCommonCompletion  | 回调闭包                   |

PlatonCommonCompletion定义如下

```
public typealias PlatonCommonCompletion = (
_ result : PlatonCommonResult,          //执行结果
_ obj : AnyObject?                      //返回数据
) -> ()
```

示例：

```
    func Notify(){
        guard self.invokeNotifyHash != nil else {
            print("ERROR:invoke invokeNotify first!")
            return
        }
        web3.eth.platonGetTransactionReceipt(txHash: self.invokeNotifyHash!, loopTime: 15) { (result, data) in
            switch result{
            case .success:
                if let receipt = data as? EthereumTransactionReceiptObject{
                    let rlpItem = try? RLPDecoder().decode((receipt.logs.first?.data.bytes)!)
                    let code = ABI.uint64Decode(data: Data(rlpItem!.array![0].bytes!))
                    let message = ABI.stringDecode(data: Data(rlpItem!.array![1].bytes!))
                    print("code:\(code) message:\(message)")
                }
            case .fail(let code, let errorMsg):
                print("error code: \(String(describing: code)), msg:\(String(describing: errorMsg))")
            }
        }
    }

```

## 内置合约

<!--- 1.1 CandidateContract -->

###  CandidateContract
> PlatON经济模型中候选人相关的合约接口

<!--- 1.1 CandidateDeposit -->

#### **`CandidateDeposit`**
> 节点候选人申请/增加质押

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式， 0x开头 |
| owner | String | 质押金退款地址, 16进制格式， 0x开头 |
| fee | BigInteger |  出块奖励佣金比，以10000为基数(eg：5%，则fee=500) |
| host | String | 节点IP  |
| port | String | 节点P2P端口号 |
| Extra | String | 附加数据，json格式字符串类型 |
| sender  | String  | 账户地址                   |
| privateKey  | String  | 私钥，需要与账户地址对应                   |
| gasPrice  | BigUInt  | 手续费用，Energon价格                   |
| gas  | BigUInt  | 手续费用，Energon数量                   |
| value  | BigUInt  | 质押金额                   |
| completion  | PlatonCommonCompletion  | 回调闭包                   |


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



出参（事件：CandidateDepositEvent）：
* `Ret`: bool 操作结果
* `ErrMsg`: string 错误信息

合约方法
```
    func CandidateDeposit(){
        let nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3";//节点id
        let owner = "0xf8f3978c14f585c920718c27853e2380d6f5db36"; //质押金退款地址
        let fee = UInt64(500)
        let host = "192.168.9.76"; //节点IP
        let port = "26794"; //节点P2P端口号
        
        var extra : Dictionary<String,String> = [:]
        extra["nodeName"] = "xxxx-noedeName"
        extra["nodePortrait"] = "http://192.168.9.86:8082/group2/M00/00/00/wKgJVlr0KDyAGSddAAYKKe2rswE261.png"
        extra["nodeDiscription"] = "xxxx-nodeDiscription"
        extra["nodeDepartment"] = "xxxx-nodeDepartment"
        extra["officialWebsite"] = "https://www.platon.network/"
        
        var theJSONText : String = ""
        if let theJSONData = try? JSONSerialization.data(withJSONObject: extra,options: []) {
            theJSONText = String(data: theJSONData,
                                 encoding: .utf8)!
        }
        
        contract.CandidateDeposit(nodeId: nodeId, owner: owner, fee: fee, host: host, port: port, extra: theJSONText, sender: sender, privateKey: privateKey, gasPrice: gasPrice, gas: gas, value: BigUInt("500")!) { (result, data) in
            switch result{
            case .success:
                print("Transaction success")
                if let data = data as? Data{
                    web3.eth.platonGetTransactionReceipt(txHash: data.toHexString(), loopTime: 15, completion: { (result, receipt) in
                        if let receipt = receipt as? EthereumTransactionReceiptObject{
                            if String((receipt.status?.quantity)!) == "1"{
                                let rlpItem = try? RLPDecoder().decode((receipt.logs.first?.data.bytes)!)
                                if (rlpItem?.array?.count)! > 0{
                                    let message = ABI.stringDecode(data: Data(rlpItem!.array![0].bytes!))
                                    print("message:\(message)")
                                }
                                print("CandidateDeposit success")
                            }else if String((receipt.status?.quantity)!) == "0"{
                                print("CandidateDeposit receipt status: 0")
                            }
                        }
                    })
                }else{
                    print("CandidateDeposit empty transaction hash")
                }
            case .fail(let code, let errMsg):
                print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
            }
        }
    }
```

<!--- 1.2 CandidateApplyWithdraw -->

#### **`CandidateApplyWithdraw`**
> 节点质押金退回申请，申请成功后节点将被重新排序，发起的地址必须是质押金退款的地址 from==owner

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式， 0x开头 |
| withdraw | BigInteger |  退款金额 (单位：E) |
| sender  | String  | 账户地址                   |
| privateKey  | String  | 私钥，需要与账户地址对应                   |
| gasPrice  | BigUInt  | 手续费用，Energon价格                   |
| gas  | BigUInt  | 手续费用，Energon数量                   |
| value  | BigUInt  | 转账金额，一般为nil                   |
| completion  | PlatonCommonCompletion  | 回调闭包                   |

**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果，json格式字符串类型 |

param1描述
```
{
    "Ret":boolean,                         //是否成功 true:成功  false:失败
    "ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
    func CandidateApplyWithdraw(){
        let nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3";
        //退款金额, 单位 E
        let value = BigUInt("500")!
        //must be owner
        let owner = "f8f3978c14f585c920718c27853e2380d6f5db36"
        let ownerPrivateKey = "74df7c508a4e20a3da81b331e2168cff9e6bc085e1968a30a05daf85ae654ed6"
        contract.CandidateApplyWithdraw(nodeId: nodeId,withdraw: value,sender: owner,privateKey: ownerPrivateKey,gasPrice: gasPrice,gas: gas,value: BigUInt(0)) { (result, data) in
            switch result{
            case .success:
                print("CandidateApplyWithdraw success")
                if let data = data as? Data{
                    web3.eth.platonGetTransactionReceipt(txHash: data.toHexString(), loopTime: 15, completion: { (result, receipt) in
                        if let receipt = receipt as? EthereumTransactionReceiptObject{
                            if String((receipt.status?.quantity)!) == "1"{
                                let rlpItem = try? RLPDecoder().decode((receipt.logs.first?.data.bytes)!)
                                if (rlpItem?.array?.count)! > 0{
                                    let message = ABI.stringDecode(data: Data(rlpItem!.array![0].bytes!))
                                    print("message:\(message)")
                                }
                                print("CandidateApplyWithdraw success")
                            }else if String((receipt.status?.quantity)!) == "0"{
                                print("CandidateApplyWithdraw receipt status: 0")
                            }
                        }
                    })
                }else{
                    print("CandidateApplyWithdraw empty transaction hash")
                }
            case .fail(let code, let errMsg):
                print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
            }
        }
    }
```

<!--- 1.3 CandidateWithdraw -->

#### **`CandidateWithdraw`**
> 节点质押金提取，调用成功后会提取所有已申请退回的质押金到owner账户。

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式， 0x开头 |
| sender  | String  | 账户地址                   |
| privateKey  | String  | 私钥，需要与账户地址对应                   |
| gasPrice  | BigUInt  | 手续费用，Energon价格                   |
| gas  | BigUInt  | 手续费用，Energon数量                   |
| value  | BigUInt  | 转账金额，一般为nil                   |
| completion  | PlatonCommonCompletion  | 回调闭包                   |

**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果，json格式字符串类型 |

param1描述
```
{
    "Ret":boolean,                         //是否成功 true:成功  false:失败
    "ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
    func CandidateWithdraw(){
        let nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3";
        contract.CandidateWithdraw(nodeId: nodeId,sender: sender,privateKey: privateKey,gasPrice: gasPrice,gas: gas,value: BigUInt(0)) { (result, data) in
            switch result{
            case .success:
                print("send Transaction success")
                if let data = data as? Data{
                    web3.eth.platonGetTransactionReceipt(txHash: data.toHexString(), loopTime: 15, completion: { (result, receipt) in
                        if let receipt = receipt as? EthereumTransactionReceiptObject{
                            if String((receipt.status?.quantity)!) == "1"{
                                let rlpItem = try? RLPDecoder().decode((receipt.logs.first?.data.bytes)!)
                                if (rlpItem?.array?.count)! > 0{
                                    let message = ABI.stringDecode(data: Data(rlpItem!.array![0].bytes!))
                                    print("message:\(message)")
                                }
                                print("CandidateWithdraw success")
                            }else if String((receipt.status?.quantity)!) == "0"{
                                print("CandidateWithdraw receipt status: 0")
                            }
                        }
                    })
                }else{
                    print("CandidateWithdraw empty transaction hash")
                }
            case .fail(let code, let errMsg):
                print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
            }
        }
    }
```

<!--- 1.4 SetCandidateExtra -->

#### **`SetCandidateExtra`**
> 设置节点附加信息, 发起的地址必须是质押金退款的地址 from==owner

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| Extra | String | 附加数据，json格式字符串类型 |
| sender  | String  | 账户地址                   |
| privateKey  | String  | 私钥，需要与账户地址对应                   |
| gasPrice  | BigUInt  | 手续费用，Energon价格                   |
| gas  | BigUInt  | 手续费用，Energon数量                   |
| value  | BigUInt  | 转账金额，一般为nil                   |
| completion  | PlatonCommonCompletion  | 回调闭包                   |
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

**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果，json格式字符串类型 |

param1描述
```
{
    "Ret":boolean,                         //是否成功 true:成功  false:失败
    "ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
    func SetCandidateExtra(){
        let nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3";//节点id
        var extra : Dictionary<String,String> = [:]
        extra["nodeName"] = "xxxx-noedeName"
        extra["nodePortrait"] = "group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg"
        extra["nodeDiscription"] = "xxxx-nodeDiscription1"
        extra["nodeDepartment"] = "xxxx-nodeDepartment"
        extra["officialWebsite"] = "xxxx-officialWebsite"
        
        var theJSONText : String = ""
        if let theJSONData = try? JSONSerialization.data(withJSONObject: extra,options: []) {
            theJSONText = String(data: theJSONData,
                                 encoding: .utf8)!
        }
        //must be owner
        let owner = "f8f3978c14f585c920718c27853e2380d6f5db36"
        let ownerPrivateKey = "74df7c508a4e20a3da81b331e2168cff9e6bc085e1968a30a05daf85ae654ed6"
        contract.SetCandidateExtra(nodeId: nodeId, extra: theJSONText, sender: owner, privateKey: ownerPrivateKey, gasPrice: gasPrice, gas: gas, value: nil) { (result, data) in
            switch result{
            case .success:
                print("send Transaction success")
                if let data = data as? Data{
                    web3.eth.platonGetTransactionReceipt(txHash: data.toHexString(), loopTime: 15, completion: { (result, receipt) in
                        if let receipt = receipt as? EthereumTransactionReceiptObject{
                            if String((receipt.status?.quantity)!) == "1"{
                                let rlpItem = try? RLPDecoder().decode((receipt.logs.first?.data.bytes)!)
                                if (rlpItem?.array?.count)! > 0{
                                    let message = ABI.stringDecode(data: Data(rlpItem!.array![0].bytes!))
                                    print("message:\(message)")
                                }
                                print("SetCandidateExtra success")
                            }else if String((receipt.status?.quantity)!) == "0"{
                                print("SetCandidateExtra receipt status: 0")
                            }
                        }
                    })
                }else{
                    print("SetCandidateExtra empty transaction hash")
                }
            case .fail(let code, let errMsg):
                print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
            }
        }
    }
```

<!--- 1.5 CandidateWithdrawInfos -->

#### **`CandidateWithdrawInfos`**
> 获取节点申请的退款记录列表

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式， 0x开头 |

**返回**

- String：json格式字符串

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

**合约使用**
```
    func CandidateWithdrawInfos() {
        contract.CandidateWithdrawInfos(nodeId: "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3") { (result, data) in
            switch result{
            case .success:
                if let data = data as? String{
                    print("result:\(data)")
                }
            case .fail(let code, let errMsg):
                print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
            }
        }
    }
```

<!--- 1.6 GetCandidateDetails -->

#### **`GetCandidateDetails`**
> 获取候选人信息

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 多个节点Id通过":"拼接的字符串, 16进制格式 |

**返回**

- String：json格式字符串

```
[
    {
        "Deposit":10000000000000000000000000,
        "BlockNumber":993,
        "TxIndex":0,
        "CandidateId":"e4556b211eb6712ab94d743990d995c0d3cd15e9d78ec0096bba24c48d34f9f79a52ca1f835cec589c5e7daff30620871ba37d6f5f722678af4b2554a24dd75c",
        "Host":"192.168.120.84",
        "Port":"16789",
        "Owner":"0x493301712671ada506ba6ca7891f436d29185821",
        "Extra":"{"nodeName":"nodeTest84","officialWebsite":"","time":1554692841011,"nodePortrait":"4","nodeDiscription":"A test network node deployed in Washington","nodeDepartment":"Washington test node"}",
        "Fee":2000,
        "TxHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
        "TOwner":"0x0000000000000000000000000000000000000000"
    },
    {
        "Deposit":10000000000000000000000000,
        "BlockNumber":987,
        "TxIndex":0,
        "CandidateId":"97e424be5e58bfd4533303f8f515211599fd4ffe208646f7bfdf27885e50b6dd85d957587180988e76ae77b4b6563820a27b16885419e5ba6f575f19f6cb36b0",
        "Host":"192.168.120.81",
        "Port":"16789",
        "Owner":"0x493301712671ada506ba6ca7891f436d29185821",
        "Extra":"{"nodeName":"nodeTest81","officialWebsite":"","time":1554692834651,"nodePortrait":"1","nodeDiscription":"A test network node deployed in Southeast Asia","nodeDepartment":"Southeast Asia test node"}",
        "Fee":500,
        "TxHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
        "TOwner":"0x0000000000000000000000000000000000000000"
    }
]
```

**合约使用**
```
func GetCandidateDetails(){
   var nodes = "0xe4556b211eb6712ab94d743990d995c0d3cd15e9d78ec0096bba24c48d34f9f79a52ca1f835cec589c5e7daff30620871ba37d6f5f722678af4b2554a24dd75c"
    nodes = nodes + ":"
    nodes = nodes + "0x97e424be5e58bfd4533303f8f515211599fd4ffe208646f7bfdf27885e50b6dd85d957587180988e76ae77b4b6563820a27b16885419e5ba6f575f19f6cb36b0"
    self.showLoading()
    contract.GetCandidateDetails(batchNodeIds: nodes) { (result, data) in
        switch result{
        case .success:
            if let data = data as? String{
                let message = "result:\(data)"
                self.showMessage(text: message)
                print(message)
            }
        case .fail(let code, let errMsg):
            let message = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: message)
            print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
        }
    }
}
```



<!--- 1.7 GetCandidateList -->


#### **`GetCandidateList`**
> 获取所有入围节点的信息列表（提名 + 候选）

**入参**

无

**返回**

String：json格式字符串

[][]: 二维数组，不存在返回空二维数组 [0]:提名人列表，不存在返回空数组 [1]:候选人列表，不存在返回空数组


```
[
	[{
		"Deposit": 10000000000000000000000000,
		"BlockNumber": 14035,
		"TxIndex": 0,
		"CandidateId": "cd68759baba315ddf5fb5abeab4c456073a477a240204136c7ef3068d4b03b19093f9b42c96188831a1d8b4130c4fc73b173bc97d40904eb163ec2c989951b4a",
		"Host": "10.10.8.18",
		"Port": "16790",
		"Owner": "0x493301712671ada506ba6ca7891f436d29185821",
		"Extra": "{\"nodeName\":\"v-Test3\",\"officialWebsite\":\"\",\"time\":1554707178046,\"nodePortrait\":\"4\",\"nodeDiscription\":\"A test network node deployed in Washington\",\"nodeDepartment\":\"Washington test node\"}",
		"Fee": 2000,
		"TxHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"TOwner": "0x0000000000000000000000000000000000000000"
	}, {
		"Deposit": 10000000000000000000000000,
		"BlockNumber": 991,
		"TxIndex": 0,
		"CandidateId": "858d6f6ae871e291d3b7b2b91f7369f46deb6334e9dacb66fa8ba6746ee1f025bd4c090b17d17e0d9d5c19fdf81eb8bde3d40a383c9eecbe7ebda9ca95a3fb94",
		"Host": "192.168.120.83",
		"Port": "16789",
		"Owner": "0x493301712671ada506ba6ca7891f436d29185821",
		"Extra": "{\"nodeName\":\"nodeTest83\",\"officialWebsite\":\"\",\"time\":1554692838935,\"nodePortrait\":\"3\",\"nodeDiscription\":\"A test network node deployed in North America\",\"nodeDepartment\":\"North American test node\"}",
		"Fee": 1500,
		"TxHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"TOwner": "0x0000000000000000000000000000000000000000"
	}],
	[{
		"Deposit": 10000000000000000000000000,
		"BlockNumber": 14043,
		"TxIndex": 0,
		"CandidateId": "f9463ae57036e703e22565c0145ee552567988b19dd2b71f2edd8270da4e7298d64d1040370c2d56760c8a6b3a554c0e0d420bc5d16d5d28b265e23a1b22c30f",
		"Host": "10.10.8.24",
		"Port": "16790",
		"Owner": "0x493301712671ada506ba6ca7891f436d29185821",
		"Extra": "{\"nodeName\":\"v-Test7\",\"officialWebsite\":\"\",\"time\":1554707186363,\"nodePortrait\":\"4\",\"nodeDiscription\":\"A test network node deployed in Washington\",\"nodeDepartment\":\"Washington test node\"}",
		"Fee": 2000,
		"TxHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"TOwner": "0x0000000000000000000000000000000000000000"
	},  {
		"Deposit": 1000000000000000000000000,
		"BlockNumber": 14328,
		"TxIndex": 0,
		"CandidateId": "3f64bf13e229869bd1156152e02c10cb677abd448f19ad6103a8b5432e1d2e1ed68803de414bec165534d553ed058f85c8ebeac8ff8ca9180fbf33be69cabdd5",
		"Host": "18.10.1.63",
		"Port": "8809",
		"Owner": "0x493301712671ada506ba6ca7891f436d29185821",
		"Extra": "{\"nodeName\":\"Xuni-19\",\"officialWebsite\":\"https://www.platon.network/\",\"time\":1554707502720,\"nodePortrait\":\"1\",\"nodeDiscription\":\"Xuni-19-nodeDiscription\",\"nodeDepartment\":\"Xuni-19-nodeDepartment\"}",
		"Fee": 500,
		"TxHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"TOwner": "0x0000000000000000000000000000000000000000"
	}]
]

```

**合约使用**
```
func GetCandidateList(){
    self.showLoading()
    contract.GetCandidateList { (result, data) in
        switch result{
        case .success:
            if let data = data as? String{
                let message = "result:\(data)"
                print(message)
                self.showMessage(text: message)
            }
        case .fail(let code, let errMsg):
            let message = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: message)
            print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
        }
    }
}
```

<!--- 1.9 GetVerifiersList -->

#### **`GetVerifiersList`**
> 获取参与当前共识的验证人列表

**入参**

无

**返回**

- String：json格式字符串

```

[{
	"Deposit": 10000000000000000000000000,
	"BlockNumber": 14035,
	"TxIndex": 0,
	"CandidateId": "cd68759baba315ddf5fb5abeab4c456073a477a240204136c7ef3068d4b03b19093f9b42c96188831a1d8b4130c4fc73b173bc97d40904eb163ec2c989951b4a",
	"Host": "10.10.8.18",
	"Port": "16790",
	"Owner": "0x493301712671ada506ba6ca7891f436d29185821",
	"Extra": "{\"nodeName\":\"v-Test3\",\"officialWebsite\":\"\",\"time\":1554707178046,\"nodePortrait\":\"4\",\"nodeDiscription\":\"A test network node deployed in Washington\",\"nodeDepartment\":\"Washington test node\"}",
	"Fee": 2000,
	"TxHash": "0xba390393432dd7f5471d7e3819c9c0e82e3fb6bd1480afcdf249199e70e027e4",
	"TOwner": "0x0549e3b659dbd6c3d3dd36463228b22bd56e86da"
}, {
	"Deposit": 10000000000000000000000000,
	"BlockNumber": 991,
	"TxIndex": 0,
	"CandidateId": "858d6f6ae871e291d3b7b2b91f7369f46deb6334e9dacb66fa8ba6746ee1f025bd4c090b17d17e0d9d5c19fdf81eb8bde3d40a383c9eecbe7ebda9ca95a3fb94",
	"Host": "192.168.120.83",
	"Port": "16789",
	"Owner": "0x493301712671ada506ba6ca7891f436d29185821",
	"Extra": "{\"nodeName\":\"nodeTest83\",\"officialWebsite\":\"\",\"time\":1554692838935,\"nodePortrait\":\"3\",\"nodeDiscription\":\"A test network node deployed in North America\",\"nodeDepartment\":\"North American test node\"}",
	"Fee": 1500,
	"TxHash": "0xf775824b48a7c6a9938cb681b964ae27b680b4874f893f00dbe12cb3f62fbe4c",
	"TOwner": "0x49a6f3db89e76b594284fafe9936e2f384553309"
}]

```

合约使用：
```
func GetVerifiersList(){
    self.showLoading()
    contract.GetVerifiersList { (result, data) in
        switch result{
        case .success:
            if let data = data as? String{
                let message = "result:\(data)"
                self.showMessage(text: message)
                print(message)
            }
        case .fail(let code, let errMsg):
            let message = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: message)
            print("error code:\(code ?? 0) errMsg:\(errMsg ?? "")")
        }
    }
}
```

<!--- 2 TicketContract -->


###  TicketContract
> PlatON经济模型中票池相关的合约接口

<!--- 2.1 GetTicketPrice -->
#### **`GetTicketPrice`**

> 获取票价

**入参**

无


**返回**

- String：当前票价(单位为E)

合约方法

```
func GetTicketPrice(){
    contract.GetTicketPrice { (result, data) in
        switch result{
        case .success:
            if let price = data as? String{
                let text = "price is:\(price)"
                self.showMessage(text: text)
            }
        case .fail(let code, let errMsg):
            let text = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: text)
        }
    }
}
```

<!--- 2.2 GetPoolRemainder -->

#### **`GetPoolRemainder`**
> 获取票价

**入参**

无



**返回**

- String：剩余票数量

合约方法

```
func GetPoolRemainder() {
    contract.GetPoolRemainder { (result, data) in
        switch result{
        case .success:
            if let remain = data as? String{
                let text = "PoolRemainder is:\(remain)"
                self.showMessage(text: text)
            }
        case .fail(let code, let errMsg):
            let text = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: text)
        }
    }
}
```

<!--- 2.3 GetCandidateEpoch -->

#### **`GetCandidateEpoch`**
> 获取候选人票龄

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| candidateId | String  | 候选人节点ID |
| completion |  PlatonCommonCompletion | 回调闭包 |


**返回**

- String：票龄(如果没有查询到返回0)

合约方法

```
    func GetCandidateEpoch(){
        contract.GetCandidateEpoch(candidateId: "0xaafbc9c699270bd33c77f1b2a5c3653eaf756f1860891327dfd8c29960a51c9aebb6c081cbfe2499db71e9f4c19e609f44cbd9514e59b6066e5e895b8b592abf") { (result, data) in
            switch result{
                
            case .success:
                if let epoch = data as? String{
                    let text = "CandidateEpoch is:\(epoch)"
                    self.showMessage(text: text)
                }
            case .fail(let code, let errMsg):
                let text = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
                self.showMessage(text: text)
            }
        }
    }
```


<!--- 2.4 GetTicketCountByTxHash -->

#### **`GetTicketCountByTxHash`**
> (批量)获取交易的有效选票数量

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| ticketId | String  | 多个txHash通过":"拼接的字符串 |
| completion |  PlatonCommonCompletion | 回调闭包 |


**返回**

- String：json格式字符串

```
{
	"0x02b7b41469782764fcbb2d9d4e9461e60ce3f92c098fce12dbffb07634934f74": 2,
	"0x33767704d735a180ef2ce2f18b03e3ae46141f4de71c7f842cf3069aafb4f20e": 2
}
```


合约方法

```
func GetTicketCountByTxHash(){
    guard self.txHashs.count > 0 else {
        self.showMessage(text: "txHashs count is 0, vote for candidate first!")
        return
    }
    self.showLoading()
    contract.GetTicketCountByTxHash(ticketIds: self.txHashs) { (result, data) in
        switch result{
        case .success:
            if let detail = data as? String{
                self.showMessage(text: "GetTicketCountByTxHash :" + detail)
            }
        case .fail(let code, let errMsg):
            let text = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: text)
        }
    }
}
```


<!--- 2.5 GetCandidateTicketCount -->

#### **`GetCandidateTicketCount`**


> (批量)获取指定候选人的有效选票数量

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeIds | [String] | 多个nodeId数组 |
| completion |  PlatonCommonCompletion | 回调闭包 |

**返回**

- String：json格式字符串

```
{
	"cd68759baba315ddf5fb5abeab4c456073a477a240204136c7ef3068d4b03b19093f9b42c96188831a1d8b4130c4fc73b173bc97d40904eb163ec2c989951b4a": 1035
}
```

合约方法

```
func GetCandidateTicketCount(){
    guard self.nodeId != nil else {
        self.showMessage(text: "nodeId is empty")
        return
    }
    contract.GetCandidateTicketCount(nodeIds: [self.nodeId!]) { (result, data) in
        switch result{
        case .success:
            if let tickets = data as? String{
                self.showMessage(text: "GetCandidateTicketCount :" + tickets)
            }
        case .fail(let code, let errMsg):
            let text = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: text)
        }
    }
}
```

<!--- 2.6 VoteTicket -->
#### **`VoteTicket`**
> 给节点投票

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| count | UInt32  | 票数量 |
| price | BigUInt | 票价 |
| nodeId | String | 节点ID |
| sender  | String  | 账户地址|
| privateKey  | String  | 私钥，需要与账户地址对应|
| gasPrice  | BigUInt  | 手续费用，Energon价格|
| gas  | BigUInt  | 手续费用，Energon数量|
| completion  | PlatonCommonCompletion  | 回调闭包|


**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果，json格式字符串类型 |

param1描述
```
{
    "Ret":boolean,                         //是否成功 true:成功  false:失败
    "ErrMsg":string,                       //错误信息，失败时存在
    "Data":"5"                             //成功选票的数量
}
```

合约方法

```
func onVoteWithNodeId(nodeId : String) {
    self.showLoading()
    contract.VoteTicket(count: 2, price: self.ticketPrice!, nodeId: nodeId, sender: sender, privateKey: privateKey, gasPrice: gasPrice, gas: gas) { (result, data) in
        switch result{
        case .success:
            if let data = data as? Data{
                print("vote hash is is:\(data.toHexString())")
                web3.eth.platonGetTransactionReceipt(txHash: data.toHexString(), loopTime: 15, completion: { (result, receipt) in
                    if let receipt = receipt as? EthereumTransactionReceiptObject{
                        if String((receipt.status?.quantity)!) == "1"{
                            guard receipt.logs.count > 0 else {
                                let message = "Fatal Error: receipt.logs count = 0!"
                                print(message)
                                self.showMessage(text: message)
                                return
                            }
                            let rlpItem = try? RLPDecoder().decode((receipt.logs.first?.data.bytes)!)
                            if (rlpItem?.array?.count)! > 0{
                                let message = ABI.stringDecode(data: Data(rlpItem!.array![0].bytes!))
                                self.showMessage(text: message)
                                self.txHashs.append(data.toHexString())
                                return
                            }
                        }else if String((receipt.status?.quantity)!) == "0"{
                            let message = "ERROR:VoteTicket receipt status: 0"
                            print(message)
                            self.showMessage(text: message)
                        }
                    }
                })
            }
        case .fail(let code, let errMsg):
            let text = "error code:\(code ?? 0) errMsg:\(errMsg ?? "")"
            self.showMessage(text: text)
        }
    }
}
```









# web3
## web3 eth相关 (标准JSON RPC )
- 相关api的使用请参考[Web3.swift github](https://github.com/Boilertalk/Web3.swift)
