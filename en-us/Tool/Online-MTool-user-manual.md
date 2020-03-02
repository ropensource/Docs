# Online MTool User Manual

## 1 Introduction

In order to facilitate node pledge, commission and governance related operations, PlatON provides MTool to assist users.

- MTool supports Ubuntu 18.04 and Windows 10. This document describes the installation and use of Windows 10.
- MTool needs to connect to the verification node via RPC interface
- To ensure node security, it is recommended that the node RPC port be accessed through Nginx proxy. Nginx uses Https and user authentication to strengthen security protection.
- MTool provides two signature methods for transactions such as pledge: online signature and offline signature. This document describes the online signature operation. For offline signature, please refer to [Offline MTool User Manual.md](./Offline-MTool-user-manual.md).

## 2 Install Online MTool

If online MTool is already installed, this step can be ignored. In addition, this document mainly introduces MTool operation in the Windows environment. If the download script fails, please set the DNS server to 8.8.8.8. The steps for installing MTool online are as follows:

**step1.** In the lower left corner of the windows field logo, right-click, **windows powershell (administrator)**, and enter the following command in the powershell interface.

```bash
$ env: chocolateyUseWindowsCompression = 'true'
Set-ExecutionPolicy -ExecutionPolicy Bypass
iex ((new-object net.webclient) .DownloadString ('https://chocolatey.org/install.ps1'))
```

prompt:

```plain
Enforcing policy changes
Execution policies help you prevent execution of untrusted scripts. Changing execution policies can create security risks, as described in the about_Execution_Policies help topic at https: /go.microsoft.com/fwlink/? LinkID = 135170. Do you want to change the execution strategy?
[Y] Yes (Y) [A] All (A) [N] No (N) [L] No (L) [S] Pause (S) [?] Help (default is "N"):
```

Please enter: y and press Enter to end.

**step2.** Copy the link from your browser <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.bat> or <http://47.91.153.183/opensource/scripts/mtool_install.bat> Download the script.

**step3.** Right-click mtool_install.bat and select Run as administrator

> Attention
>
> - When prompted for `Enter password:`, please enter the Enter key.
> - When the message `install and start mtool success` is displayed, MTool is successfully installed. If it is not successfully installed, please contact our official customer contact to feedback specific problems.
> - When prompting `Please press any key to continue ... `, please press Enter to close the current cmd window.

For related configuration, refer to the document [PlatON Node Installation and Deployment Manual.md](./PlatON-Install-manual.md).

## 3 Configure Online MTool

### 3.1 Create Wallet

In PlatON, two wallets are created to participate in the verification node for block generation. If you already have a wallet, you can skip this step by copying the wallet file to the `C:\tools\mtool\current\keystore` directory.

- Pledge wallet
  The pledge wallet is used to pledge tokens. Only after successful pledge can be a candidate node candidate.
  Run the following command to create a pledged wallet:

  ```shell
  mtool-client.bat create_wallet --name staking
  ```

  Enter the password once and confirm the password again to create the wallet file. After the creation is successful, the pledge wallet file `staking.json` will be generated in the directory:`C:\tools\mtool\current\keystore`.

- Earning wallet
  It is used to collect block rewards and Staking rewards. Staking rewards are uniformly distributed to verification nodes, which are distributed by the verification nodes themselves.
  Run the following command to create a revenue wallet:

  ```shell
  mtool-client.bat create_wallet --name reward
  ```

  Enter the password once and confirm the password again to create the wallet file. After the creation is successful, the pledge wallet file `reward.json` will be generated in the directory:` C:\tools\mtool\current\keystore`.

### 3.2 Configure Verification Node Information

**step1.** Browser copy link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.bat> or <http://47.91.153.183/opensource/scripts/validator_conf.bat> Download script

**step2.** Right-click validator_conf.bat and select Run as administrator

> Attention
>
>- When prompting `Please enter the platon node IP address:`, please enter the PlatON node server IP address.
>- When prompted `Please enter the platon chain id:`, please enter the chain ID.
>- When prompted `Enter your name:`, please enter the username you entered when configuring PlatON node nginx.
>- When prompted `Enter your password:`, please enter the password you entered when configuring PlatON node nginx.
>- When prompting `Enter your platon node name:`, enter the name of the PlatON node.
>- When prompted `Enter your platon node description:`, enter the PlatON node description.
>- When the prompt `validator conf success` is displayed, it means that the script has been executed successfully. If the script is not executed successfully, please contact our official customer contact to feedback specific problems.
>- When prompting `Please press any key to continue ... `, please press Enter to close the current cmd window.

## 4 Detailed Online MTool Operation

### 4.1 Initiating a pledge operation

If the consensus nodes are deployed and the blocks have been synchronized successfully, you can use MTool for pledge operations. After the application for pledge funds is completed, ensure that the balance of the pledge account is sufficient, and replace {Pledge Amount} according to the user's situation. The minimum threshold for pledge is 1 million LAT.

- Excuting an order

```bash
mtool-client.bat staking --amount 1000000 --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```
Tip: "please input keystore password:" Enter the password of the pledge wallet, and then press Enter. If the following information is displayed, the pledge is successful:

```bash
operation finished
transaction hash:
0x89b964d27d0caf1d8bf268f721eb123c4af57aed36187bea90b262f4769eeb9b
SUCCESS
```

- Parameter Description

> amount: Pledged number, not less than 1000000lat-pledged threshold, no more than 8 decimal places
>
> restrictedamount: no less than 1000000lat- pledge threshold, no more than 8 decimal places

### 4.2 Modify Verifier Information Operation

- Excuting an order

```bash
mtool-client.bat update_validator --name VerifierName --url "www.platon.com" --identity IdentifyID --reward 0x33d253386582f38c66cb5819bfbdaad0910339b3 --introduction "Modify the verifier information operation" --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json --a
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

### 4.3 Decommissioning operation

- Excuting an order

```bash
mtool-client.bat unstaking --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> None

### 4.4 Increase pledge operation

- Excuting an order

```bash
mtool-client.bat increasestaking --amount 5000000 --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> amount: use the account balance to increase the pledge amount (LAT), the minimum value added is not less than 10, and the decimal point does not exceed 8 digits
>
> restrictedamount: use the account balance to increase the amount of pledge, not less than 10 pledge threshold, the decimal point does not exceed 8 digits (use lock balance balance pledge)

### 4.5 Commissioning rewards to generate report operations

Before performing this operation, you need to start the MTool service. If it is already started, you can skip this step.

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
`` `
```

- Edit validation node configuration file

If the startup parameter of the node is added with the **--db.nogc** option, indicating that the node is an archive node, this step can be ignored; otherwise it is a non-archive node and only saves the transaction information of the last 100 blocks to obtain The reward details need to be connected to the archive node. The currently open archive nodes are: **52.233.38.169: 6789** and **13.235.119.163: 6789**. You need to modify the **nodeAddress** parameter in the validator configuration file validator_config.json, other parameters are unchanged:

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

Note: This report is a summary of all allocation details related to the start and end blocks in the command parameters from **start_block** to **end_block**; if there are multiple settlement periods between the start and end block intervals, the allocation of multiple settlement periods will be rewarded The details are summarized in this report. The generated file is in the C:\tools\mtool\current\reward_data\VerifierName\ directory. Where VerifierName is the name of the verifier.

### 4.6 Entrusted Incentive Issuance Operation

Before performing this operation, you need to perform other operations. For details, refer to [4.5 Commissioning rewards to generate report operations](#4.5-Commissioning-rewards-to-generate-report-operations). step.

Entrusted incentive distribution will backfill transaction hash and transaction status on the basis of the reward distribution file, and generate **reward distribution result report**. The report is stored in the csv file format. The generated file is in the C:\tools\mtool\current\reward_data\VerifierName\ directory. Where VerifierName is the name of the verifier.

- Edit validation node configuration file

If the startup parameter of the node is added with the **--db.nogc** option, indicating that the node is an archive node, this step can be ignored; otherwise it is a non-archive node and only saves the transaction information of the last 100 blocks to obtain The reward details need to be connected to the archive node. The currently open archive nodes are: **52.233.38.169: 6789** and **13.235.119.163: 6789**. You need to modify the **nodeAddress** parameter in the validator configuration file validator_config.json, other parameters are unchanged:

```json
{
    "nodeRpcPort": "6789",
    "nodeAddress": "http://52.233.38.169"
}
```

- Excuting an order

```bash
mtool-client.bat reward_divide --keystore %MTOOLDIR%\keystore\reward.json --config % MTOOLDIR%\validator\validator_config.json --reward_file %MTOOLDIR%\validator\reward_config.json
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

### 4.7 Submit Text Proposal

- Excuting an order

```bash
mtool-client.bat submit_textproposal --pid_id 100 --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> pid_id: GitHub ID

### 4.8 Submit Upgrade Proposal

- Excuting an order

```bash
mtool-client.bat submit_versionproposal --newversion 0.8.0 --end_voting_rounds 345 --pid_id 100 --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> newversion: target upgrade version, x.x.x, number punctuation
>
> end_voting_rounds: the number of voting consensus rounds, the number of voting consensus rounds N, must satisfy 0 <N <= 2419 (about 2 weeks)
>
> pid_id: GitHub ID

### 4.9 Submit Cancel Proposal

- Excuting an order

```bash
mtool-client.bat submit_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --end_voting_rounds 12 --pid_id 100 --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: The ID of the proposal that needs to be canceled, that is, the hash of the proposal transaction, 66 characters, alphanumeric
>
> end_voting_rounds: the number of voting consensus rounds, the number of voting consensus rounds N, must satisfy 0 <N <= 2419 (about 2 weeks)
>
> pid_id: GitHub ID

### 4.10 Text proposal voting operation

- Excuting an order

```bash
mtool-client.bat vote_textproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: text proposal ID, that is, the hash of the proposed transaction, 66 characters, alphanumeric
>
> opinion: voting options, yes, no, abstain-choose one

### 4.11 Upgrade proposal voting operation

- Excuting an order

```bash
mtool-client.bat vote_textproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: upgrade proposal ID, that is, the hash of the proposed transaction, 66 characters, alphanumeric

### 4.12 Cancel proposal voting

- Excuting an order

```bash
mtool-client.bat vote_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --keystore %MTOOLDIR%\keystore\staking.json –config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> proposalid: Cancel proposal ID, that is, the hash of the proposed transaction, 66 characters, composed of alphanumeric characters
>
> opinion: voting options, yes, no, abstain-choose one

### 4.13 Version declaration operation

- Excuting an order

```bash
mtool-client.bat declare_version --keystore %MTOOLDIR%\keystore\staking.json --config %MTOOLDIR%\validator\validator_config.json
```

- Parameter Description

> None

### 4.14 View help

- Excuting an order

```bash
mtool-client.bat help
```

- Parameter Description

> None

