-   [概览](#概览)

-   [快速入门](#快速入门)
    -   [安装或引入](#安装或引入)
    -   [初始化代码](#初始化代码)
-   [合约](#合约)
    -   [合约示例](#合约示例)
    -   [合约骨架生成](#合约骨架生成)
    -   [加载合约](#加载合约)
    -   [部署合约](#部署合约)
    -   [合约call调用](#合约call调用)
    -   [合约sendRawTransaction调用](#合约sendrawtransaction调用)
    -   [内置合约](#内置合约)
        -  [CandidateContract](#CandidateContract)
        -  [TicketContract](#TicketContract)
-   [web3](#web3)
    -   [web3 eth相关 (标准JSON RPC )](#web3-eth相关-标准json-rpc)
    -   [新增的接口](#新增的接口)
        -   [ethPendingTx](#ethpendingtx)

# 概览
> Java SDK是PlatON面向java开发者，提供的PlatON公链的java开发工具包


# 快速入门

## 安装或引入

### 环境要求
1. jdk1.8

### maven
1. 配置仓库
   
    1. maven项目配置
    ```
    <repository>
	    <id>platon-public</id>
	    <url>https://sdk.platon.network/nexus/content/groups/public/</url>
	</repository>
    ```

    2. gradle项目配置
    ```
    repositories {
        maven { url "https://sdk.platon.network/nexus/content/groups/public/" }
    }
    ```

2. maven方式引用

    Java 8:
    ```
    <dependency>
        <groupId>com.platon.client</groupId>
        <artifactId>core</artifactId>
        <version>0.6.0</version>
    </dependency>
    ```
    Android:
    
    ```
    <dependency>
        <groupId>com.platon.client</groupId>
        <artifactId>core</artifactId>
        <version>0.6.0-android</version>
    </dependency>
    ```
    
3. gradle方式引用

    Java 8:
    ```
    compile "com.platon.client:core:0.6.0"
    ```
    Android:
    
    ```
    compile "com.platon.client:core:0.6.0-android"
    ```
    
### 合约骨架生成工具
1. 安装包下载 https://download.platon.network/latest/client-sdk.zip
2. 解压后说明
```
.
+-- _bin
|   +-- client-sdk.bat                 //windows执行程序
|   +-- client-sdk                     //linux执行程序
+-- _lib
|   +-- xxx.jar                        //类库
|   +-- ...
```
3. 到bin目录执行 ./client-sdk

```
              _      _____ _     _
             | |    |____ (_)   (_)
__      _____| |__      / /_     _   ___
\ \ /\ / / _ \ '_ \     \ \ |   | | / _ \
 \ V  V /  __/ |_) |.___/ / | _ | || (_) |
  \_/\_/ \___|_.__/ \____/| |(_)|_| \___/
                         _/ |
                        |__/

Usage: client-sdk version|wallet|solidity|truffle|wasm ...
```

## 初始化代码
```
Web3j web3 = Web3j.build(new HttpService("http://localhost:6789"));
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

## 合约骨架生成
1. 以下操作使用（[FirstDemo.wasm](https://download.platon.network/latest/doc/FirstDemo.wasm)）和（[FirstDemo.cpp.abi.json](https://download.platon.network/latest/doc/FirstDemo.cpp.abi.json)）作为骨架生成示例。关于wasm智能合约的编写及其ABI(wasm文件)和BIN(json文件)生成方法请参考 [WASM合约指南](/zh-cn/development/%5BChinese-Simplified%5D-Wasm合约开发指南.md)。
2. 使用合约骨架生成工具生成Java合约骨架：
```
client-sdk wasm generate --javaTypes </path/to/FirstDemo.wasm> </path/to/FirstDemo.cpp.abi.json> -o </path/to/src/main/java> -p <com.your.organisation.name> -t WASM
```
注意：把尖括号内的内容替换成你自己的内容

## 部署合约
```
//Java 8
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));
//Android
Web3j web3j = Web3jFactory.build(new HttpService("http://localhost:6789"));


Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

byte[] dataBytes = Files.readBytes(new File("<wasm file path>"));
String binData = Hex.toHexString(dataBytes);

Firstdemo contract = Firstdemo.deploy(web3j, credentials, binData, new DefaultWasmGasProvider()).send();
```

## 加载合约
```
//Java 8
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));
//Android
Web3j web3j = Web3jFactory.build(new HttpService("http://localhost:6789"));

Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

byte[] dataBytes = Files.readBytes(new File("<wasm file path>"));
String binData = Hex.toHexString(dataBytes);

Firstdemo contract = Firstdemo.load(binData, "0x<address>", web3j, credentials, new DefaultWasmGasProvider());
```


## 合约call调用
```
//abi中方法描述	constant=true 的生成的骨架方法里面调用的是call查询
String name = contract.getName().send();
System.out.println(name);
```

## 合约sendRawTransaction调用
```
//abi中方法描述	constant=false 的生成的骨架方法里面调用的是sendRawTransaction
TransactionReceipt transactionReceipt =  contract.invokeNotify("hello").send();

//如果合约方法中有event，可以通过下面方法获得event内容
List<NotifyEventResponse> eventResponses = contract.getNotifyEvents(transactionReceipt);
for(NotifyEventResponse r:eventResponses) {
    System.out.println(r.param1);
    System.out.println(r.param2);
}

```

## 内置合约
###  CandidateContract
> PlatOn经济模型中候选人相关的合约接口

#### 加载合约
```
//Java 8
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));
//Android
Web3j web3j = Web3jFactory.build(new HttpService("http://localhost:6789"));

Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

CandidateContract contract = CandidateContract.load(web3j, credentials, new DefaultWasmGasProvider());
```

#### **`CandidateDeposit`**
> 节点候选人申请质押

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id,16进制格式,0x开头 |
| owner | String | 质押金退款地址, 16进制格式,0x开头 |
| fee | BigInteger |  出块奖励佣金比,以10000为基数(eg: 5%,则fee=500) |
| host | String | 节点IP  |
| port | String | 节点P2P端口号 |
| Extra | String | 附加数据,json格式字符串类型 |
| value | BigInteger | 质押金额 |

备注：质押金额必须大于等于1000000Energon,value >= 1000000Energon

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
| param1 | String | 执行结果,json格式字符串类型 |

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"Data":string                          //返回数据
	"ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 
//质押金退款地址
String owner = "0xf8f3978c14f585c920718c27853e2380d6f5db36";
//出块奖励佣金比，以10000为基数(eg：5%，则fee=500)
BigInteger fee =  BigInteger.valueOf(500L);
//节点IP
String host = "192.168.9.76";
//节点P2P端口号
String port = "26794";
//附加数据
JSONObject extra = new JSONObject();
//节点名称
extra.put("nodeName", "xxxx-noedeName");
//节点logo
extra.put("nodePortrait", "http://192.168.9.86:8082/group2/M00/00/00/wKgJVlr0KDyAGSddAAYKKe2rswE261.png");
//机构简介
extra.put("nodeDiscription", "xxxx-nodeDiscription");
//机构名称
extra.put("nodeDepartment", "xxxx-nodeDepartment");  
//官网
extra.put("officialWebsite", "https://www.platon.network/");  
//质押金额, 单位 E
BigInteger value = new BigInteger("200000000000000000000".replaceAll(" ", ""));
  
//调用接口
TransactionReceipt receipt = contract.CandidateDeposit(nodeId, owner, fee, host, port, extra.toJSONString(),value).send();
logger.debug("CandidateDeposit TransactionReceipt:{}", JSON.toJSONString(receipt));

//查看返回event
List<CandidateDepositEventEventResponse>  events = contract.getCandidateDepositEventEvents(receipt);
for (CandidateDepositEventEventResponse event : events) {
	 logger.debug("CandidateDeposit event:{}", JSON.toJSONString(event.param1));
} 
```

#### **`CandidateApplyWithdraw`**
> 节点质押金退回申请，申请成功后节点将被重新排序，发起的地址必须是质押金退款的地址 from==owner

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式,0x开头 |
| withdraw | BigInteger |  退款金额 (单位:E) |


**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果,json格式字符串类型 |

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"Data":string                          //返回数据
	"ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 
//退款金额, 单位 E
BigInteger value = new BigInteger("100");

//调用接口
TransactionReceipt receipt = ownerContract.CandidateApplyWithdraw(nodeId,value).send();
logger.debug("TransactionReceipt:{}", JSON.toJSONString(receipt));

//查看返回event
List<CandidateApplyWithdrawEventEventResponse>  events = ownerContract.getCandidateApplyWithdrawEventEvents(receipt);
for (CandidateApplyWithdrawEventEventResponse event : events) {
	 logger.debug("event:{}", JSON.toJSONString(event.param1));
}
```

#### **`CandidateWithdraw`**
> 节点质押金提取，调用成功后会提取所有已申请退回的质押金到owner账户。

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id,16进制格式, 0x开头 |


**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果,json格式字符串类型 |

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"Data":string                          //返回数据
	"ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 

//调用接口
TransactionReceipt receipt = contract.CandidateWithdraw(nodeId).send();
logger.debug("TransactionReceipt:{}", JSON.toJSONString(receipt));

//查看返回event
List<CandidateWithdrawEventEventResponse>  events = contract.getCandidateWithdrawEventEvents(receipt);
for (CandidateWithdrawEventEventResponse event : events) {
    logger.debug("event:{}", JSON.toJSONString(event.param1));
}
```

#### **`SetCandidateExtra`**
> 设置节点附加信息, 发起的地址必须是质押金退款的地址 from==owner

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式, 0x开头 |
| Extra | String | 附加数据,json格式字符串类型 |

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
| param1 | String | 执行结果,json格式字符串类型 |

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"Data":string                          //返回数据
	"ErrMsg":string                        //错误信息，失败时存在
}
```

**合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 

//附加数据
JSONObject extra = new JSONObject();
//节点名称
extra.put("nodeName", "xxxx-noedeName");
//节点logo
extra.put("nodePortrait", "group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg");
//机构简介
extra.put("nodeDiscription", "xxxx-nodeDiscription1");
//机构名称
extra.put("nodeDepartment", "xxxx-nodeDepartment");  
//官网
extra.put("officialWebsite", "xxxx-officialWebsite");  


//调用接口
TransactionReceipt receipt = ownerContract.SetCandidateExtra(nodeId,extra.toJSONString()).send();
logger.debug("TransactionReceipt:{}", JSON.toJSONString(receipt));

//查看返回event
List<SetCandidateExtraEventEventResponse>  events = contract.getSetCandidateExtraEventEvents(receipt);
for (SetCandidateExtraEventEventResponse event : events) {
    logger.debug("event:{}", JSON.toJSONString(event.param1));
}
```

#### **`GetCandidateWithdrawInfos`**
> 获取节点申请的退款记录列表

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id,16进制格,0x开头 |

**返回**

- String：json格式字符串

```
{
	"Ret": true,                      
	"ErrMsg": "success",
	"Data": [{                        //退款记录
		"Balance": 100,                //退款金额
		"LockNumber": 13112,           //退款申请所在块高
		"LockBlockCycle": 1            //退款金额锁定周期
	}]
}
```

**Java SDK合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 
 
//调用接口
String result = contract.GetCandidateWithdrawInfos(nodeId).send();
logger.debug("GetCandidateWithdrawInfos:{}",result);
```
**Android SDK合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 
 
//调用接口
String result = contract.CandidateWithdrawInfos(nodeId).send();
logger.debug("CandidateWithdrawInfos:{}",result);
```

#### **`GetCandidateDetails`**
> (批量)获取候选人信息

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String  | 节点id, 16进制格式, 0x开头,多个nodeId通过":"拼接的字符串 |

**返回**

- String：json格式字符串

```
{
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
}
```

**Java SDK合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 
 
//调用接口
String result = contract.GetCandidateDetails(nodeId).send();
logger.debug("GetCandidateDetails:{}",result);  
```

**Android SDK合约使用**
```
//节点id
String nodeId = "0x6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3"; 
 
//调用接口
String result = contract.CandidateDetails(nodeId).send();
logger.debug("CandidateDetails:{}",result);  
```

#### **`GetCandidateList`**
> 获取所有入围节点的信息列表

**入参**

无

**返回**

- String：json格式字符串，内部结构为一个二维数组，表示：**[[提名列表],[候选列表]]**
```
[
    [{
        "Deposit": 11100000000000000000,
        "BlockNumber": 13721,
        "TxIndex": 0,
        "CandidateId": "c0e69057ec222ab257f68ca79d0e74fdb720261bcdbdfa83502d509a5ad032b29d57c6273f1c62f51d689644b4d446064a7c8279ff9abd01fa846a3555395535",
        "Host": "192.168.9.76",
        "Port": "26793",
        "Owner": "0x3ef573e439071c87fe54287f07fe1fd8614f134c",
        "Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
        "Fee": 9900,
        "TxHash":"0xfa0914bc939e76b40f378200cdb2603d342e09553d916aa4e6bee8fbbe273944"
    }], 
    [{
        "Deposit": 200,
        "BlockNumber": 12206,
        "TxIndex": 0,
        "CandidateId": "6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3",
        "Host": "192.168.9.76",
        "Port": "26794",
        "Owner": "0xf8f3978c14f585c920718c27853e2380d6f5db36",
        "Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
        "Fee": 500,
        "TxHash":"0xfa0914bc939e76b40f378200cdb2603d342e09553d916aa4e6bee8fbbe273944"
    }]
]
```

**Java SDK合约使用**
```
String nodeInfoList = contract.GetCandidateList().send();
logger.debug("GetCandidateList:{}",nodeInfoList);
```

**Android SDK合约使用**
```
String nodeInfoList = contract.CandidateList().send();
logger.debug("CandidateList:{}",nodeInfoList);
```

#### **`GetVerifiersList`**
> 获取参与当前共识的验证人列表

**入参**

无

**返回**

- String：json格式字符串

```
[{
	"Deposit": 11100000000000000000,
	"BlockNumber": 13721,
	"TxIndex": 0,
	"CandidateId": "c0e69057ec222ab257f68ca79d0e74fdb720261bcdbdfa83502d509a5ad032b29d57c6273f1c62f51d689644b4d446064a7c8279ff9abd01fa846a3555395535",
	"Host": "192.168.9.76",
	"Port": "26793",
	"Owner": "0x3ef573e439071c87fe54287f07fe1fd8614f134c",
	"Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
	"Fee": 9900,
	"TxHash":"0xfa0914bc939e76b40f378200cdb2603d342e09553d916aa4e6bee8fbbe273944"

}, {
	"Deposit": 200,
	"BlockNumber": 12206,
	"TxIndex": 0,
	"CandidateId": "6bad331aa2ec6096b2b6034570e1761d687575b38c3afc3a3b5f892dac4c86d0fc59ead0f0933ae041c0b6b43a7261f1529bad5189be4fba343875548dc9efd3",
	"Host": "192.168.9.76",
	"Port": "26794",
	"Owner": "0xf8f3978c14f585c920718c27853e2380d6f5db36",
	"Extra": "{\"nodeName\":\"xxxx-noedeName\",\"officialWebsite\":\"xxxx-officialWebsite\",\"nodePortrait\":\"group2/M00/00/12/wKgJVlw0XSyAY78cAAH3BKJzz9Y83.jpeg\",\"nodeDiscription\":\"xxxx-nodeDiscription1\",\"nodeDepartment\":\"xxxx-nodeDepartment\"}",
	"Fee": 500,
	"TxHash":"0xfa0914bc939e76b40f378200cdb2603d342e09553d916aa4e6bee8fbbe273944"
}]
```

**Java SDK合约使用**
```
String result = contract.GetVerifiersList().send();
logger.debug("GetVerifiersList:{}",result);
```

**Android SDK合约使用**
```
String result = contract.VerifiersList().send();
logger.debug("VerifiersList:{}",result);
```

###  TicketContract
> PlatOn经济模型中票池相关的合约接口

#### 加载合约
```
//Java 8
Web3j web3j = Web3j.build(new HttpService("http://localhost:6789"));
//Android
Web3j web3j = Web3jFactory.build(new HttpService("http://localhost:6789"));

Credentials credentials = WalletUtils.loadCredentials("password", "/path/to/walletfile");

TicketContract contract = TicketContract.load(web3j, credentials, new DefaultWasmGasProvider());
```

#### **`GetTicketPrice`**
> 获取当前的票价

**入参**

无

**返回**

- String：当前票价(单位为E)

合约使用：
```
String price = contract.GetTicketPrice().send();
logger.debug("Ticket price: {}",price);
```

#### **`GetPoolRemainder`**
> 获取票池剩余票数量

**入参**

无

**返回**

- String：剩余票数量

合约使用：
```
String detail = contract.GetPoolRemainder().send();
logger.debug("{}",detail);
```

#### **`VoteTicket`**
> 购买选票，投票给(已存在的)候选人。

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| count | BigInteger  | 购票数量 |
| price | BigInteger | 选票单价(单位为E) |
| nodeId | String |  候选人节点Id,16进制格式, 0x开头  |


**返回事件**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| param1 | String | 执行结果,json格式字符串类型 |

param1描述
```
{
	"Ret":boolean,                         //是否成功 true:成功  false:失败
	"ErrMsg":string,                       //错误信息,失败时存在
	"Data":"5"                             //返回数据（成功选票的数量 + ":" + 成交票价）
}
```

**合约使用**
```
//选票单价
String priceStr = contract.GetTicketPrice().send();
BigInteger price =  new BigInteger(priceStr);
//购票数量
BigInteger count = BigInteger.valueOf(5L);
//候选人节点Id
String nodeId = "0x4f6c8fd10bfb512793f81a3594120c76b6991d3d06c0cc652035cbfae3fcd7cdc3f3d7a82021dfdb9ea99f014755ec1a640d832a0362b47be688bb31d504f62d";

//调用接口
TransactionReceipt receipt = contract.VoteTicket(count, price, nodeId).send();
logger.debug("TicketContract TransactionReceipt:{}", JSON.toJSONString(receipt));

//查看返回event
List<VoteTicketEventEventResponse>  events = contract.getVoteTicketEventEvents(receipt);
JSONObject result = null;
for (VoteTicketEventEventResponse event : events) {
	result = JSON.parseObject(event.param1);
	logger.debug("TicketContract event:{}", result);
} 

```

#### **`GetCandidateTicketCount`**
> (批量)获取指定候选人的有效选票数量

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String |  候选人节点Id,16进制格式,0x开头,多个nodeId通过":"拼接的字符串 |

**返回**

- String：json格式字符串

```
{
                  
	"error": "string",              //错误信息
	"Ret": {                        //返回值
    	  "nodeId1" : 10,           //节点id : 有效票
          "nodeId2" : 1,
          "nodeId3" : 3
	}
    
}
```

**合约使用**
```
//节点id
String nodeId = "0x4f6c8fd10bfb512793f81a3594120c76b6991d3d06c0cc652035cbfae3fcd7cdc3f3d7a82021dfdb9ea99f014755ec1a640d832a0362b47be688bb31d504f62d";
String countStr = ticketContract.GetCandidateTicketCount(ids).send();
Map<String,Integer> map = JSON.parseObject(countStr,Map.class);
    map.forEach((k,v)->{
            logger.error("nodeId {} : count {} ",k ,v);
    });
```


#### **`GetCandidateEpoch`**
> 获取指定候选人的票龄

**入参**

| **参数名** | **类型** | **参数说明** |
| ------ | ------ | ------ |
| nodeId | String |  候选人节点Id,16进制格式,0x开头  |

**返回**

- String：票龄(如果没有查询到返回0)


**合约使用**
```
//节点id
String nodeId = "0x4f6c8fd10bfb512793f81a3594120c76b6991d3d06c0cc652035cbfae3fcd7cdc3f3d7a82021dfdb9ea99f014755ec1a640d832a0362b47be688bb31d504f62d";

String detail = contract.GetCandidateEpoch(nodeId).send();

logger.debug("{}",detail);
```


# web3
## web3 eth相关 (标准JSON RPC )
- java api的使用请参考[web3j github](https://github.com/web3j/web3j)

## 新增的接口
### ethPendingTx
>查询待处理交易

**参数**

 无

**返回值**

`Request<?, EthPendingTransactions>`
EthPendingTransactions属性中的transactions即为对应存储数据

**示例**
```
Request<?, EthPendingTransactions> req = web3j.ethPendingTx();
EthPendingTransactions res = req.send();
List<Transaction> transactions = res.getTransactions();
```
