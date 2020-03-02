# Offline MTool User Manual

## 1 Introduction

In order to facilitate node pledge, commission and governance related operations, PlatON provides MTool to assist users.

- MTool supports Ubuntu 18.04 and Windows 10. This document describes the installation and use of Windows 10.
- MTool needs to connect to the verification node through RPC interface. For the installation and deployment of verification node, please refer to [PlatON node installation and deployment manual.md](./ PlatON node installation and deployment manual.md)
- To ensure node security, it is recommended that node RPC ports be accessed through Nginx proxy. Nginx uses Https and user authentication to strengthen security protection
- MTool provides two signature methods for transactions such as pledge: online signature and offline signature. This document describes the offline signature operation. For online signature, please refer to [Online MTool User Manual.md](./Online-MTool-user-manual.md)

## 2 Install MTool

### 2.1 Online MTool

If online MTool is already installed, this step can be ignored. The steps for installing MTool online are as follows:

- **step1.** Browser copy link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.bat> or <http://47.91.153.183/opensource/scripts/mtool_install.bat> Download scripts.

- **step2.** Right-click mtool_install.bat and select Run as administrator

> Attention
>
> - When prompted for`Enter password:`, please enter the Enter key.
> - When the message`install and start mtool success` is displayed, MTool is successfully installed. If it is not successfully installed, please contact our official customer contact to feedback specific problems.
> - When prompting`Please press any key to continue ... `, please press Enter to close the current cmd window.

### 2.2 Offline MTool

For security reasons, offline MTool should be installed on an offline machine (not connected to any network and WIFI).

- Download MTool installation package

  On a machine with a network, copy the link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.7.4.1/mtool-setup.exe> or  <http://47.91.153.183/mtool/mtool-setup/0.7.4.1/mtool-setup.exe> Go to the browser and download the MTool installation package.

- Transfer the installation file mtool-setup.exe to**offline machine**via a secure storage medium (mobile U disk or mobile hard disk)

- Install MTool on**offline machine**

  Double-click mtool-setup.exe to install it. The user selects the installation directory by himself, and the pop-up interface displays the message **Completing the mtool Setup Wizard**, which indicates that the installation was successful. Click Finish.

## 3 Configuration

### 3.1 Wallet Configuration

#### 3.1.1 Basic concepts

* Cold wallets: wallets stored on offline machines, not exposed to the Internet
* Observe wallet: wallet containing cold wallet address, cannot make transactions, only view data
* Hot Wallet: Wallet exposed to the Internet

#### 3.1.2 Create a cold wallet

If the user does not have a wallet, execute the command on the**offline machine**to generate a pledge wallet and a profit wallet; if there is already a wallet, you can copy the wallet file to the**keystore**directory of the decompressed package of the offline MTool through the storage medium. Skip this step.

- Create pledge wallet

```bash
mtool-client.bat create_wallet --name staking
```

- Create earnings wallet

```bash
mtool-client.bat create_wallet --name reward
```

#### 3.1.3 Generate Observation Wallet

- Generate pledge observation wallet

Run the command on the **offline machine**to generate the pledge observation wallet:

```bash
mtool-client.bat create_observewallet --keystore %MTOOLDIR%\keystore\staking.json
```

 Enter the pledged cold wallet password and return the generated observation wallet as follows:

```
please input keystore password: 
SUCCESS
wallet created at: keystore/staking_observed.json
```

- Copy the generated observation wallet file staking_observed.json to the keystore directory of the online machine.
- Generate income observation wallet

Run the command on the **offline machine**to generate the income observation wallet:

```bash
mtool-client.bat create_observewallet --keystore %MTOOLDIR%\keystore\reward.json
```

 Enter the earning cold wallet password and return the generated observation wallet, as follows:

```
please input keystore password: 
SUCCESS
wallet created at: keystore/reward_observed.json
```

- Copy the generated observation wallet file reward_observed.json to the**keystore**directory of**online machine**.

### 3.2 Connect to Validation Node

If authentication node information has been configured, ignore this step. Perform the following steps on**`online machine`**to generate verification node configuration information:

- step1. Browser copy link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.bat> or <http://47.91.153.183/opensource/scripts/validator_conf.bat> Download the script.

- step2. Right-click validator_conf.bat and select Run as administrator. The result is as follows:

> Attention
>
> - When prompting`Please enter the platon node IP address:`, please enter the PlatON node server IP address.
> - When prompted`Enter your name:`, enter the username you entered when configuring nginx.
> - When prompted`Enter your password:`, please enter the password you entered when configuring nginx.
> - When prompting`Enter your platon node name:`, enter the name of the PlatON node.
> - When prompted`Enter your platon node description:`, enter the PlatON node description.
> - When the prompt "validator conf success" is displayed, it means that the script has been executed successfully. If the script is not executed successfully, please contact our official customer contact to feedback specific problems.
> - When prompting `Please press any key to continue ... `, please press Enter to close the current cmd window.


## 4 Basic Operation Flow

### 4.1 Generate transaction data

- Generate files to be signed

  Take the pledge operation as an example, execute the pledge operation command on the **online machine**, note that the wallet option command at this time is`--address`

```bash
mtool-client.bat staking --amount 1000000 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

Note: staking_observed.json is the observation wallet, which can be modified based on the actual observation wallet.

Return to generate the corresponding file to be signed:

```bash
operation finished
SUCCESS
File generated on transaction_details/transaction_detail_20191108114241.csv
```

Note: transaction_details/transaction_detail_20191108114241.csv is the transaction file to be signed.

- Copy files to be signed to **offline machine**

  Copy the file to be signed %MTOOLDIR%\transaction_details\transaction_detail_20191108114241.csv under **online machine** to **offline machine** via the storage medium.

### 4.2 Offline Signature Transactions

- Generate transaction signature files

Execute the signing command under **offline machine**to sign the pledged transaction:

```bash
mtool-client.bat offlinesign --filelocation %MTOOLDIR%\transaction_details\transaction_detail_20191108114241.csv
```

Note:%MTOOLDIR%\transaction_details\transaction_detail_20191108114241.csv is the file to be signed generated in the previous step, and is modified to the actual file to be signed.

Enter the corresponding cold wallet password and return the signed file. The file content is as follows:

```csv
 ┌────────┬────────┬──────────────────────────── ────┬────────┬───────┬───────┐
│Type │From │To │Account │Amount │Fee │Nonce │Create │Chain │
│ │ │ │ Type │ │ │ │ Time │Id │
├────────┼────────┼──────────────────────────── ────┼────────┼───────┼───────┤
│STAKING │0xa1548d│0x100000│FREE_AMO│5000000.│0.043210│0 │2019-10│100 │
│ │d61010a7│00000000│UNT_TYPE│00000000│00000000│ │-11T13: │ │
│ │42cd66fb│00000000│ │00000000│0000 │ │54: 06.8│ │
│ │86324ab3│00000000│ │00 │ │ │97 │ │
│ │e2935586│00000000│ │ │ │ │ │ │
│ │4a │02 │ │ │ │ │ │ │
└────────┴────────┴──────────────────── ────┴────────┴───────┴───────┘
Need load 1 wallets for address: [0xa1548dd61010a742cd66fb86324ab3e29355864a]

operation finished
SUCCESS
File generated on transaction_signature / transaction_signature_20191108114625.csv
total: 1, to be signed: 1
success: 1, failure: 0
```

Note: where transaction_signature/transaction_signature_20191108114625.csv is a signed transaction file.

- Copy transaction signature files to online machines

Copy the signed file transaction_signature_20191108114625.csv on the offline machine to the online machine via the storage medium.

### 4.3 Sending Signature Transactions

- Execute online transaction order on **online machine** to complete pledge operation

```bash
mtool-client.bat send_signedtx --filelocation %MTOOLDIR%\transaction_signature\transaction_signature_20191108114625.csv --config %MTOOLDIR%\validator\validator_config.json
```

Note: transaction_signature_20191108114625.csv is the transaction signature file generated in the previous step, and is modified to the actual signature file.

Enter`yes` to return the transaction result:

```bash
Send Transaction? (Yes | no)
yes
transaction 1 success
transaction hash: 0xf14f74386e6ef9027c48582d7faed3b50ab1ffdd047d6ba3afcf27791afb4e9b
SUCCESS
total: 1
success: 1, failure: 0
```

Note: prompting success and returning a transaction hash indicates that the signed transaction was sent successfully, otherwise the signed transaction failed to be sent.

## 5 Detailed MTool operation

This chapter mainly describes the relevant commands for generating transaction to-be-signed files in csv format on the online machine (except [5.5 commissioned reward generation report operation](# 55- commissioned reward generation report operation)), the generated csv file will Save in %MTOOLDIR%\transaction_details directory. Refer to [4 Basic Operation Flow](#4-Basic Operation Flow) for the complete process of sending offline signature transactions.

### 5.1 Initiating a pledge operation

If the consensus nodes are deployed and the blocks have been synchronized successfully, you can use MTool for pledge operations. After the application for pledge funds is completed, ensure that the balance of the pledge account is sufficient, and replace {Pledge Amount} according to the user's situation. The minimum threshold for pledge is 1 million LAT.

- Excuting an order

```bash
mtool-client.bat staking --amount 1000000 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```
- Parameter Description

> address: Pledge observation wallet path
>
> amount: Pledged number, not less than 1000000lat- pledge threshold, no more than 8 decimal places (use free amount pledge)
>
> restrictedamount: no less than 1000000lat- pledge threshold, no more than 8 decimal places

### 5.2 Modify validator information

- Excuting an order

```bash
mtool-client.bat update_validator --name VerifierName --url "www.platon.com" --identity IdentifyID --reward 0x33d253386582f38c66cb5819bfbdaad0910339b3 --introduction "Modify the verifier information operation" --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json --a
```

- Parameter Description

> name: validator name, no more than 30 bytes, supports letters, numbers, spaces, underscores and #, must start with a letter
>
> url: official website path, no more than 70 bytes, composed of alphanumeric characters
>
> identity: identity authentication ID, no more than 140 bytes
>
> reward: return address, 42 characters (alphanumeric)
>
> introduction: introduction, brief description of the validator, no more than 280 bytes, English is recommended
>
> a: When the command is executed, the version verifier configuration file is also updated

### 5.3 Unpledge operation

- Excuting an order

```bash
mtool-client.bat unstaking --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> None

### 5.4 Increase pledge operation

- Excuting an order

```bash
mtool-client.bat increasestaking --amount 5000000 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> amount: Use the account balance to increase the pledge amount (LAT), the minimum added value is not less than 10, and the decimal point does not exceed 8 digits (use a free amount to increase the pledge)
>
> restrictedamount: use the account balance to increase the amount of pledge, not less than 10 pledge threshold, the decimal point does not exceed 8

### 5.5 Commissioning rewards to generate report operations

Before performing this operation, you need to start the MTool service on the **online machine**. If it is already started, you can skip this step.

- Execute the start MTool service command

```bash
mtool-server.bat
```

This command will generate **Allocation Reward Details Report** and **Allocation Reward Summary Report**. The reports are stored in the csv file format.

- Edit delegated incentive plan profile

If the reward_config.json file is already configured, you can skip this step.

```bash
copy %MTOOLDIR%\validator\reward_config.json.example %MTOOLDIR%\validator\reward_config.json
```

Edit the reward_config.json file. The file template description is as follows:

```json
{
  // Pledged reward configuration
 "staking": {
    // Reward method: AVERAGE (average distribution of commissioned people), PERCENT (weight distribution of total amount commissioned by the client)
 "rewardMethod": "AVERAGE",
    // Node commission percentage
"commissionRatio": 0.1
 },
    // Block reward configuration
 "block": {
    // Reward method: AVERAGE (average distribution of commissioned people), PERCENT (weight distribution of total amount commissioned by the client)
 "rewardMethod": "AVERAGE",
    // Node commission percentage
"commissionRatio": 0.2
 },
  // Transaction fee reward configuration
 "trade": {
    // Reward method: AVERAGE (average distribution of commissioned people), PERCENT (weight distribution of total amount commissioned by the client)
 "rewardMethod": "AVERAGE",
    // Node commission percentage
"commissionRatio": 0.3
},
  // Payer of transaction fee, DELEGATOR (from the client) VALIDATOR (from the validator)
"feePayer": "DELEGATOR"
}
```

-Edit validation node configuration file

If the startup parameter of the node is added with the **--db.nogc** option, indicating that the node is an archive node, this step can be ignored; otherwise it is a non-archive node and only saves the transaction information of the last 100 blocks to obtain The reward details need to be connected to the archive node. The currently open archive nodes are:**52.233.38.169: 6789** and **13.235.119.163: 6789**. You need to modify the **nodeAddress** parameter in the validator configuration file validator_config.json, other parameters are unchanged:

```json
{
    "nodeRpcPort": "6789",
    "nodeAddress": "http://52.233.38.169"
}
```

- Excuting an order

```bash
mtool-client.bat gen_reward --start_block 10751 --end_block 107500 --rewardconfig %MTOOLDIR%\validator\reward_config.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> address: Must be the wallet address specified by the validator node
>
> start_block: the height of the starting block for issuing incentive statistics, which is 1 or an integer multiple of 10750 (settlement cycle) plus 1
>
> end_block: The cutoff block for issuing incentive statistics is high, which is an integral multiple of 10750 (settlement cycle). The end block must be greater than the start block.
>
> rewardconfig: delegate incentive plan configuration file

- Generate detailed report of distribution rewards

```
Period: block1201-block1500
"Plan: block reward (20.00%, AVERAGE), fee reward (30.00%, AVERAGE), staking reward (10.00%, AVERAGE), Fee payer: (DELEGATOR)"
Validator: liyf-test
Reward address: 0xa1548dd61010a742cd66fb86324ab3e29355864a (Balance: 50000000000000000000000 LAT)
Total reward: 60000 LAT
Total block reward: 20000 LAT
Total fee reward: 30000 LAT
Total staking reward: 10000 LAT
Delegators: 1
Total distribution: 46000 LAT
Delegator, Block reward, Fee reward, Staking reward, Total reward
0xc1553f9deadecdbb304e4f557fca196f81ea02cd, 20000, 30000, 10000, 60000
```

Note: This report is the distribution reward for a single settlement cycle. If there are multiple settlement cycles in the command parameters from **start_block** to **end_block**, multiple distribution reward detailed reports will be generated. The generated file is in the C:\tools\mtool\current\reward_data\VerifierName\Date\ directory. Where VerifierName is the name of the verifier, and Date is the date when the report was generated.

- Generate distribution reward summary

```
Period: block1201-block1500
"Plan: block reward (20.00%, AVERAGE), fee reward (30.00%, AVERAGE), staking reward (10.00%, AVERAGE), Fee payer: (DELEGATOR)"
Validator: liyf-test
Reward address: 0xa1548dd61010a742cd66fb86324ab3e29355864a (Balance: 50000000000000000000000 LAT)
Total reward: 60000 LAT
Total block reward: 20000 LAT
Total fee reward: 30000 LAT
Total staking reward: 10000 LAT
Delegators: 1
Total distribution: 46000 LAT
Adjusted Total Distribution: 46000 LAT (Actual distribution award has been adjusted)
Delegator, Block reward, Fee reward, Staking reward, Issued reward, Distribute rewards, Actual distribute reward
0xc1553f9deadecdbb304e4f557fca196f81ea02cd, 20000, 30000, 10000, 6000, 54000, 54000
```

Note: This report is a summary of all allocation details related to the start and end blocks in the command parameters from **start_block** to **end_block**; if there are multiple settlement periods between the start and end block intervals, the allocation of multiple settlement periods will be awarded The details are summarized in this report, and the generated files are under: C:\tools\mtool\current\reward_data\VerifierName\ Where VerifierName is the name of the verifier.

### 5.6 Entrusted Incentive Issuance Operation

Before performing this operation, you need to perform other operations. For details, refer to [5.5 Entrusted Reward Generation Report Operation](#55-Entrusted Reward Generation Report Operation) **Execute the start MTool service command** and **edit the commissioned incentive plan configuration file.** step.

Entrusted incentive distribution will backfill transaction hash and transaction status on the basis of the reward distribution file, and generate **reward distribution result report**. The generated file is in the C:\tools\mtool\current\reward_data\VerifierName\ directory. Where VerifierName is the name of the verifier.

- Edit validation node configuration file

If the startup parameter of the node is added with the **--db.nogc** option, indicating that the node is an archive node, this step can be ignored; otherwise it is a non-archive node and only saves the transaction information of the last 100 blocks to obtain The reward details need to be connected to the archive node. The currently open archive nodes are:**52.233.38.169: 6789** and **13.235.119.163: 6789**. You need to modify the **nodeAddress** parameter in the validator configuration file validator_config.json, other parameters are unchanged:

```json
{
    "nodeRpcPort": "6789",
    "nodeAddress": "http://52.233.38.169"
}
```

- Excuting an order

```bash
mtool-client.bat reward_divide --address %MTOOLDIR%\keystore\reward_observed.json --config %MTOOLDIR%\validator\validator_config.json --reward_file %MTOOLDIR%\validator\reward_config.json
```

- Parameter Description

> reward_file: the name of the file issued by the commission incentive

- Reward Distribution Results Report

```
Period: block1201-block1500
"Plan: block reward (20.00%, AVERAGE), fee reward (30.00%, AVERAGE), staking reward (10.00%, AVERAGE), Fee payer: (DELEGATOR)"
Validator: liyf-test
Reward address: 0xa1548dd61010a742cd66fb86324ab3e29355864a (Balance: 50000000000000000000000 LAT)
Total reward: 60000 LAT
Total block reward: 20000 LAT
Total fee reward: 30000 LAT
Total staking reward: 10000 LAT
Delegators: 1
Total distribution: 46000 LAT
Adjusted Total Distribution: 46000 LAT (Actual distribution award has been adjusted)
Total txn fee: 0.000021 LAT
Delegator, Block reward, Fee reward, Staking reward, Issued reward, Distribute rewards, Actual distribute reward, Tx fee, Actual reward, Txn hash, "status"
0xc1553f9deadecdbb304e4f557fca196f81ea02cd, 20000, 30000, 10000, 6000, 54000, 54000, 0.00021, 53999.999979, 0x4e5e2f63c8c3d63424749fccfafa4f08e73ce7cdfb1f2fdad5da2147357a72d9, S
```

Note: Among them, 0x4e5e2f63c8c3d63424749fccfafa4f08e73ce7cdfb1f2fdad5da2147357a72d9 is the transaction hash, and Success is the transaction status, which indicates that the entrustment incentive issuance transaction is successful.

### 5.7 Submit Text Proposal

- Excuting an order

```bash
mtool-client.bat submit_textproposal --pid_id 100 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> pid_id: GitHub ID

### 5.8 Submit Upgrade Proposal

- Excuting an order

```bash
mtool-client.bat submit_versionproposal --newversion 1.0.0 --end_voting_rounds 10 --pid_id 100 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> newversion: target upgrade version, x.x.x, number punctuation
>
> end_voting_rounds: the number of voting consensus rounds, the number of voting consensus rounds N, must satisfy 0 <N <= 2419 (about 2 weeks)
>
> pid_id: GitHub ID

### 5.9 Submit Cancel Proposal

- Excuting an order

```bash
mtool-client.bat submit_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --end_voting_rounds 12 --pid_id 100 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: The ID of the proposal that needs to be canceled, that is, the hash of the proposal transaction, 66 characters, alphanumeric
>
> end_voting_rounds: the number of voting consensus rounds, the number of voting consensus rounds N, must satisfy 0 <N <= 2419 (about 2 weeks)
>
> pid_id: GitHub ID

### 5.10 Voting actions for text proposals

- Excuting an order

```bash
mtool-client.bat vote_textproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: text proposal ID, that is, the hash of the proposed transaction, 66 characters, alphanumeric
>
> opinion: voting options, yes, no, abstain-choose one

### 5.11 Upgrade proposal voting operation

- Excuting an order

```bash
mtool-client.bat vote_versionproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: upgrade proposal ID, that is, the hash of the proposed transaction, 66 characters, alphanumeric

### 5.12 Cancel proposal voting

- Excuting an order

```bash
mtool-client.bat vote_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address %MTOOLDIR%\keystore\staking_observed.json –config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: Cancel proposal ID, that is, the hash of the proposed transaction, 66 characters, composed of alphanumeric characters
>
> opinion: voting options, yes, no, abstain-choose one

### 5.13 Version declaration operation

- Excuting an order

```bash
mtool-client.bat declare_version --address %MTOOLDIR%\keystore\staking_observed.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> None

### 5.14 View help

- Excuting an order

```bash
mtool-client.bat help
```

- Parameter Description

> None
