## 简介

​        在下面的例子中，我们将使用合约进行一次众筹。合约创建者发起众筹，并初始化众筹的代币数量及众筹持续的时间。如果在指定时间内众筹完成则此次众筹成功。并关闭众筹开关，根据一个固定兑换比率得到的一定数量的token会被铸造出来，并且会被计入在买方名下。否则众筹失败，把众筹的金额返还给投资者。

在合约中设置了两个角色

- 众筹者
- 投资者

## 众筹的流程

- 1.创建众筹合约指受益人
- 2.部署合约初始化众筹代币数量及持续时间
- 3.投资者进行投资
- 4.判断众筹是否结束
-  如果众筹时间未到，众筹代币数量已完成，关闭众筹开关。给投资者按比例分配token。众筹成功
-  如果众筹时间已到，众筹代币数量已完成，给投资者按比例分配token。众筹成功
-  如果众筹时间已到，众筹代币数量未完成，返还投资者代币。众筹失败

## 众筹合约

```
pragma solidity ^0.5.13;

contract CrowdFunding {
    address payable public beneficiaryAddress = address(0x0); //受益人地址，设置为合约创建者
    uint256 public fundingGoal = 100 LAT;  //众筹目标，单位是LAT
    uint256 public amountRaised = 0; //已筹集金额数量， 单位是VON
    uint256 public deadline; //截止时间
    uint256 public price;  //代币价格
    bool public fundingGoalReached = false;  //达成众筹目标
    bool public crowdsaleClosed = false; //众筹关闭

    mapping(address => uint256) public balance; //保存众筹者对捐赈的金额
    
    mapping(address => uint256) public tokenMap; //保存众筹者所拥有的代币数量

    //记录已接收的LAT通知
    event GoalReached(address _beneficiaryAddress, uint _amountRaised);

    //转帐通知
    event FundTransfer(address _backer, uint _amount, bool _isContribution);
    
    //校验地址是否为空
    modifier validAddress(address _address) {
        require(_address != address(0x0));
        _;
    }

    /**
     * 初始化构造函数
     *
     * @param _fundingGoalInlats 众筹LAT币总量
     * @param _durationInMinutes 众筹截止,单位是分钟
     */
    constructor (
        uint _fundingGoalInlats,
        uint _durationInMinutes
    )public {
	    beneficiaryAddress = msg.sender;
        fundingGoal = _fundingGoalInlats * 1 LAT;
        deadline = now + _durationInMinutes * 1 minutes;
        price = 500 finney; //1个LAT币可以买 2 个代币
    }


    /**
     * 默认函数
     *
     * 默认函数，可以向合约直接打款
     */
    function () payable external {

        //判断是否关闭众筹
        require(!crowdsaleClosed);
        uint amount = msg.value;

        //捐款人的金额累加
        balance[msg.sender] += amount;

        //捐款总额累加
        amountRaised += amount;

        //转帐操作，转多少代币给捐款人
        tokenMap[msg.sender]  += amount / price;
        
        emit FundTransfer(msg.sender, amount, true);
    }

    /**
     * 判断是否已经过了众筹截止限期
     */
    modifier afterDeadline() { if (now >= deadline) _; }

    /**
     * 检测众筹目标是否已经达到
     */
    function checkGoalReached() public afterDeadline payable{
        if (amountRaised >= fundingGoal){
            //达成众筹目标
            fundingGoalReached = true;
            emit GoalReached(beneficiaryAddress, amountRaised);
        }

        //关闭众筹
        crowdsaleClosed = true;
    }


    /**
     * 收回资金
     *
     * 检查是否达到了目标或时间限制，如果有，并且达到了资金目标，
     * 将全部金额发送给受益人。如果没有达到目标，每个贡献者都可以退回
     * 他们贡献的金额
     */
    function safeWithdrawal() public afterDeadline {

        //如果没有达成众筹目标
        if (!fundingGoalReached) {
            //获取合约调用者已捐款余额
            uint amount = balance[msg.sender];

            if (amount > 0) {
                //返回合约发起者所有余额
                msg.sender.transfer(amount);
                emit FundTransfer(msg.sender, amount, false);
                balance[msg.sender] = 0;
            }
        }

        //如果达成众筹目标，并且合约调用者是受益人
        if (fundingGoalReached && beneficiaryAddress == msg.sender) {

            //将所有捐款从合约中给受益人
            beneficiaryAddress.transfer(amountRaised);
            
            emit FundTransfer(beneficiaryAddress, amountRaised, false);
        }
    }
}
```

**编译众筹合约：**

**step1.** 为众筹合约创建新目录

```
mkdir myCrowdFunding && cd myCrowdFunding
```
- 以下命令如果没有特殊说明都在myCrowdFunding目录下进行

**step2.** 使用platon-truffle初始化一个工程

```
truffle init
```

在操作完成之后，就有这样的一个项目结构：

- contracts/: Solidity合约目录
- migrations/: 部署脚本文件目录
- test/: 测试脚本目录
- truffle-config.js: platon-truffle 配置文件

**step3.** 将编写好的众筹合约放至myCrowdFunding/contracts/目录下

```
ls myCrowdFunding/contracts/
```
- 将看到 CrowdFunding.sol

**step4.** 修改platon-truffle 配置文件truffle-config.js，将编译器版本修改对应的solidity合约中的版本号

```
vim truffle-config.js
```

truffle-config.js 修改部分内容如下
```
compilers: {
     solc: {
        version: "^0.5.13",    // 此版本号与CrowdFunding.sol中声明的版本号保持一致
    }
}
```

**step5.** 编译合约

```
truffle compile
```

在操作完成之后，就有这样的一个目录结构：

- build/: Solidity合约编译后的目录
- build/contracts/CrowdFunding.json CrowdFunding.sol对应的编译文件

**部署众筹合约：**

**step1.** 添加部署脚本文件

```
cd migrations/ && touch 2_initial_CrowdFunding.js
```

部署脚本文件为:2_initial_crowdFunding.js，内容如下所示：

```
const CrowdFunding = artifacts.require("CrowdFunding"); //需要部署的合约名称 
module.exports = function(deployer) {
  ​    deployer.deploy(CrowdFunding);
};
```

**step2.** 修改truffle-config.js中链的配制信息

```
vim truffle-config.js
```

将truffle-config.js中的区块链相关配制修改成您真实连接的链配制

```
networks: {
	development: {
       host: "10.1.1.6",     // 区块链所在服务器主机
       port: 8806,            // 链端口号
       network_id: "*",       // Any network (default: none)
       from: "0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e", //部署合约账号的钱包地址
       gas: 90000000,
       gasPrice: 50000000004,
	},
}
```

**step3.**  部署合约

```
truffle migrate
```

部署成功将输出如下信息：
```
Compiling your contracts...
 Everything is up to date, there is nothing to compile.
 3_initial_CrowdFunding.js
 
    Deploying 'CrowdFunding'
     transaction hash:    0x3a6419cd4169d7cfb430a1fc5632239ac4a01845827f20df9b3229a334c5488b
     Blocks: 0            Seconds: 0
     contract address:    0x02D04C6fD2b0C07c43AA1a329D667f1F1Fc7a907 //部署后的合约地址
     block number:        280532
     block timestamp:     1581751224032
     account:             0xF644CfC3b0Dc588116D6621211a82C1Ef9c62E9e
     balance:             90000000.806077629992489796
     gas used:            379154
     gas price:           50.000000004 gVON
     value sent:          0 LAT
     total cost:          0.018957700001516616 LAT
 
     Saving migration to chain.
     Saving artifacts
     Total cost:     0.018957700001516616 LAT
```


**众筹者查询众筹情况：**

**step1.**  进入platon-truffle控制台

```
truffle console
```
- 以下调用查询将在truffle控制台中进行

**step2.**  构建的众筹合约对象
```
var abi = [...]; //众筹合约的ABI，从编译后的文件获取
var contractAddr = '0x02D04C6fD2b0C07c43AA1a329D667f1F1Fc7a907'; //众筹合约地址
var crowdFunding = new web3.eth.Contract(abi,contractAddr); 
```

**step3.**  查询已筹集金额
```
crowdFunding.methods.amountRaised().call(null,function(error,result){console.log("result:" + result);}); //查询已筹集金额
```

**step4.**  众筹者判断众筹是否成功
```
crowdFunding.methods.safeWithdrawal().send({from:'0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e'}).on('data', function(event){ console.log(event);}).on('error', console.error); 
```

调用合约命令说明：

- `crowdFunding` 是我们之前构建的合约对象
- `methods` 固定语法，指定将获取合约中的方法
- `safeWithdrawal` 是我们众筹合约中的一个方法，用于收回资金
- `from` 调用者的合约地址 
- `on` 是监听合约处理结果事件，失败输出错误日志
