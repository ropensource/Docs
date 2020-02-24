## Introduce

In the following example,we will use smart contract for a crowdfunding campaign.the creator of the contract started crowdfunding,and initializes the number of tokens and the duration of the crowdfunding.If the crowdfunding is completed within the specified time, the crowdfunding will be successful.If the crowdfunding switch is turned off, a certain number of tokens based on a fixed exchange rate will be cast and credited to the name of the investor.Otherwise,the crowdfunding fails and the amount of the crowdfunding is returned to the investors.

There are two roles in the contract

- Crowdfunder
- investor

## Crowdfunding process

- 1.Creating a crowdfunding contract refers to the beneficiary
- 2.Deployment crowdfunding contract initializes the number and duration of crowdfunding tokens
- 3.Investors invest
- 4.Determine if crowdfunding is over
-  If the crowdfunding time is not up and the number of crowdfunding tokens has been completed, turn off the crowdfunding switch. Investors are allocated tokens proportionally. Crowdfunding success
-  If the crowdfunding time is up and the amount of crowdfunding tokens has been completed, investors will be allocated tokens in proportion. Crowdfunding success
-  If the crowdfunding time is up and the number of crowdfunding tokens is not completed, the investor tokens will be returned. Crowdfunding failure

## CrowdFunding contract

```
pragma solidity ^0.5.13;

contract CrowdFunding {
    address payable public beneficiaryAddress = address(0x0); //Beneficiary address, set as contract creator
    uint256 public fundingGoal = 100 LAT;  //Crowdfunding target, unit is LAT
    uint256 public amountRaised = 0; //The amount of money raised,the unit is VON
    uint256 public deadline; 
    uint256 public price;  //token price
    bool public fundingGoalReached = false;  //Achieving crowdfunding goals flag
    bool public crowdsaleClosed = false; //Crowdfunding closed

    mapping(address => uint256) public balance; //Save the amount raised by the investor
    
    mapping(address => uint256) public tokenMap; //Save the number of tokens owned by the investor

    //Record received LAT notifications
    event GoalReached(address _beneficiaryAddress, uint _amountRaised);

    //Transfer notice
    event FundTransfer(address _backer, uint _amount, bool _isContribution);
    
    //Check if the address is empty
    modifier validAddress(address _address) {
        require(_address != address(0x0));
        _;
    }

    /**
     * Initialization constructor
     *
     * @param _fundingGoalInlats: Total crowdfunding LAT coin
     * @param _durationInMinutes: Crowdfunding deadline, unit is minute
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
     * fallback functioin
     *
     * you can send money directly to the contract
     */
    function () payable external {

        //check whether to close crowdfunding
        require(!crowdsaleClosed);
        uint amount = msg.value;

        //investor amount accumulated
        balance[msg.sender] += amount;

        //Total invest accumulated
        amountRaised += amount;

        //Transfer operation, how many tokens are transferred to the investor
        tokenMap[msg.sender]  += amount / price;
        
        emit FundTransfer(msg.sender, amount, true);
    }

    /**
     * Determine if the crowdfunding deadline has passed
     */
    modifier afterDeadline() { if (now >= deadline) _; }

    /**
     * Check if the crowdfunding goal has been reached
     */
    function checkGoalReached() public afterDeadline payable{
        if (amountRaised >= fundingGoal){
            //crowdfunding goal has been reached
            fundingGoalReached = true;
            emit GoalReached(beneficiaryAddress, amountRaised);
        }

        //Closing crowdfunding
        crowdsaleClosed = true;
    }


    /**
     * Recover funds
     *
	 * Check if the target or time limit has been reached, and if so, send the full amount to the beneficiary.
     * If the goal is not reached, each investor can return the amount they invested
     */
    function safeWithdrawal() public afterDeadline {

        //If the crowdfunding goal is not reached
        if (!fundingGoalReached) {
            //Get the contracted caller's donated balance
            uint amount = balance[msg.sender];

            if (amount > 0) {
                //Returns all balances of the contract initiator
                msg.sender.transfer(amount);
                emit FundTransfer(msg.sender, amount, false);
                balance[msg.sender] = 0;
            }
        }

        //f the crowdfunding goal is achieved and the contract caller is the beneficiary
        if (fundingGoalReached && beneficiaryAddress == msg.sender) {

            //Give all donations from the contract to the beneficiary
            beneficiaryAddress.transfer(amountRaised);
            
            emit FundTransfer(beneficiaryAddress, amountRaised, false);
        }
    }
}
```

**compile CrowdFunding contract**

**step1.** creat new directory for CrowdFunding project 

```
mkdir myCrowdFunding && cd myCrowdFunding
```
- The following commands are performed in the myCrowdFunding directory without special instructions

**step2.** init project

```
truffle init
```

After the command is executed,project directory structure is as follows:

- contracts/: Solidity contract directory
- migrations/: depoly file directory
- test/: test script directory
- truffle-config.js: platon-truffle config

**step3.** move CrowdFunding contract compiled in to myCrowdFunding/contracts/

```
ls myCrowdFunding/contracts/
```
- CrowdFunding.sol

**step4.** fix compile version same as the version setted  in truffle-config.js

```
vim truffle-config.js
```

truffle-config.js content is  as follows:
```
compilers: {
     solc: {
        version: "^0.5.13",    //same as the version declared in CrowdFunding.sol
    }
}
```

**step5.** compile contract

```
truffle compile
```

After the command is executed, project directory structure is as follows:

- build/: Solidity contract directory after compiled
- build/contracts/CrowdFunding.json: the compiled file Corresponding with CrowdFunding.sol

**deploly CrowdFunding contract**

**step1.** create deploy script 

```
cd migrations/ && touch 2_initial_CrowdFunding.js
```

deploy script 2_initial_crowdFunding.js,content is as follows：

```
const CrowdFunding = artifacts.require("CrowdFunding"); //deployment contract class name
module.exports = function(deployer) {
  ​    deployer.deploy(CrowdFunding);
};
```

**step2.** Setting config  information for blockchain in truffle-config.js

```
vim truffle-config.js
```

set blockchain network  info

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

**step3.**  deploy contract

```
truffle migrate
```

if deploy success，you wil see log info as follows:
```
Compiling your contracts...
 Everything is up to date, there is nothing to compile.
 3_initial_CrowdFunding.js
 
    Deploying 'CrowdFunding'
     transaction hash:    0x3a6419cd4169d7cfb430a1fc5632239ac4a01845827f20df9b3229a334c5488b
     Blocks: 0            Seconds: 0
     contract address:    0x02D04C6fD2b0C07c43AA1a329D667f1F1Fc7a907 //contract address
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


**Crowdfunder query crowdfunding：**

**step1.**  Enter the platon-truffle console

```
truffle console
```
- you can execute cmd in console

**step2.**  create contract object
```
var abi = [...]; //ABI of CrowdFunding contract,can get from build/contracts/CrowdFunding.json
var contractAddr = '0x02D04C6fD2b0C07c43AA1a329D667f1F1Fc7a907'; //CrowdFundsing contract address
var crowdFunding = new web3.eth.Contract(abi,contractAddr); 
```

**step3.**  Query the amount raised
```
crowdFunding.methods.amountRaised().call(null,function(error,result){console.log("result:" + result);}); //query the amount raised
```

**step4.**  Crowdfunder judge the success of crowdfunding
```
crowdFunding.methods.safeWithdrawal().send({from:'0xf644cfc3b0dc588116d6621211a82c1ef9c62e9e'}).on('data', function(event){ console.log(event);}).on('error', console.error); 
```

Call contract command description:

- `crowdFunding` Is the contract object we built earlier
- `methods` Fixed syntax specifying that methods in the contract will be obtained
- `safeWithdrawal` Is a method in our Crowdfunding contract to recover funds
- `from` Caller's contract address
- `on` Listen for contract processing result events, and output error logs for failures
