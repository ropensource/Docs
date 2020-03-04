
## Install MTool

### Online MTool

If online MTool is already installed, you can ignore this step.

In addition, this document introduces the operation of MTool under Windows and Ubuntu respectively. Users can choose according to their own resources.

#### Install Online MTool under Windows

Proceed as follows:

- Download MTool installation package

  On the online machine, copy the link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.8.0.0/mtool-setup.exe> or <http://47.91.153.183/mtool/mtool-setup/0.8.0.0/mtool-setup.exe> go to the browser and download the MTool installation package.

- Install MTool

  Double-click mtool-setup.exe to install it. The default installation directory is C:\tools, it is recommended not to change this installation directory. The pop-up interface displays the message **Completing the mtool Setup Wizard**, indicating that the installation was successful. Click **Finish**.

#### Install online MTool under Ubuntu

Proceed as follows:

**step1.** Download mtool toolkit

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/0.8.0.0/mtool-client.zip
```

or

```bash
wget http://47.91.153.183/mtool/0.8.0.0/mtool-client.zip
```

**step2.** Extract the mtool toolkit

```bash
unzip mtool-client.zip && cd mtool-client
```

**step3.** Download script

> [!NOTE|style:flat|label:Note]
>
> The script is downloaded to the <font color=red> mtool-client </font> directory, otherwise the script cannot find the path of the new version of mtool.

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.sh
```

or

```bash
wget http://47.91.153.183/opensource/scripts/mtool_install.sh
```

**step4.** execute command

```bash
chmod + x mtool_install.sh && ./mtool_install.sh
```

> [!NOTE|style:flat|label:Note]
>
> - When the message <font color=red> Install mtool succeed. </font> is displayed, MTool is successfully installed. If it is not successfully installed, please contact our official customer service to provide feedback on specific issues.
> - After installation is complete, you need to <font color=red> restart the terminal </font> for the newly added environment variables to take effect.

### Offline MTool

For security reasons, offline MTool should be installed on an offline machine (not connected to any network and WIFI).

In addition, this document introduces the operation of MTool under Windows and Ubuntu respectively. Users can choose according to their own resources.

#### Install offline MTool under Windows

Proceed as follows:

- Download MTool installation package

  On a machine with a network, copy the link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.8.0.0/mtool-setup.exe> or <http://47.91.153.183/mtool/mtool-setup/0.8.0.0/mtool-setup.exe> go to the browser and download the MTool installation package.

- Transfer the installation file mtool-setup.exe to **offline machine** via a secure storage medium (mobile U disk or mobile hard disk).

- Install MTool on **offline machine**

  Double-click mtool-setup.exe to install it. The default installation directory is C:\tools. It is not recommended to change this installation directory. The pop-up interface displays the message **Completing the mtool Setup Wizard**, indicating that the installation was successful. Click **Finish**.

#### Install offline MTool under Ubuntu

Proceed as follows:

- Download MTool installation package

  On a machine with a network, download the mtool installation package and execute the command:

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/0.8.0.0/mtool-client.zip
```

or

```bash
wget http://47.91.153.183/mtool/0.8.0.0/mtool-client.zip
```

- Execute command on **offline machine**:

```bash
java -version
```

> [!NOTE|style:flat|label:Note]
>
> - If you return <font color=red> Command 'java' not found </font>it means that jdk is not installed and you need to download jdk.
> - If the version number related information is returned, it means that the jdk is already installed, there is no need to download the jdk.

- Download jdk on a machine with network

If **offline machine** already has jdk installed, skip this step.

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/third-tools/jdk-8u221-linux-x64.tar.gz
```

or

```bash
wget http://47.91.153.183/third-tools/jdk-8u221-linux-x64.tar.gz
```

- Download the installation script

Download the install_off_line_mtool.sh script on a networked machine:

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/install_off_line_mtool.sh
```

or

```bash
wget http://47.91.153.183/opensource/scripts/install_off_line_mtool.sh
```

- Transfer the compressed files mtool-client.zip and install_off_line_mtool.sh script to **offline machine** through a secure storage medium (removable U disk or removable hard disk). jdk-8u221-linux-x64.tar.gz and mtool-client.zip, install_off_line_mtool.sh scripts are placed `in the same directory`.
- Install MTool on **offline machine**, execute the command:

If you need to install jdk, decompress jdk-8u221-linux-x64.tar.gz, otherwise skip this command:

```bash
tar -xzvf jdk-8u221-linux-x64.tar.gz
```

Perform the installation steps:

```bash
unzip mtool-client.zip && chmod + x install_off_line_mtool.sh && ./install_off_line_mtool.sh
```

> [!NOTE|style:flat|label:Note]
>
> - Unzip needs to be installed in advance on offline machines.
> - When the prompt <font color=red> Install off line mtool succeed. </font> is displayed, the offline MTool installation is successful. If the installation is not successful, please contact our official customer service to provide feedback on specific issues.
> - After the installation is complete, you need to <font color=red> restart the terminal </font> for the newly added environment variables to take effect.

## Configuration

The environment variables of MTool directories under Windows and Ubuntu are different:

- MTool Catalog
  - Windows: `%MTOOLDIR%`
  - Ubuntu: `$MTOOLDIR`

> Explanation:
>
> - MTool directory is replaced with the variable `$MTOOLDIR`;
>
> **`Users choose according to their installed system.`**

### Wallet configuration

#### basic concepts

* Cold wallet: A wallet stored on an offline machine and cannot be exposed to the Internet.
* Observe wallet: A wallet that contains a cold wallet address, cannot make transactions, but can only view data.
* Hot Wallet: Wallet exposed to the Internet.

#### Create a cold wallet

If the user does not have a wallet, execute the command on the **offline machine** to generate a staking wallet and a profit wallet; if a wallet already exists, you can copy the wallet file to the **keystore** directory of the decompressed package of the offline MTool through the storage medium, Skip this step.

- Create Staking wallet

```bash
mtool-client account new staking
```

- Create Reward wallet

```bash
mtool-client account new reward
```

#### Generate Observation Wallet

- Generate a staking observation wallet

Run the command on the **offline machine** to generate a staking observation wallet:

```bash
mtool-client create_observewallet --keystore $MTOOLDIR/keystore/staking.json
```

 Enter the staking cold wallet password and return the generated observation wallet, as follows:

```
please input keystore password:
SUCCESS
wallet created at: keystore/staking_observed.json
```

- Copy the generated observation wallet file staking_observed.json to the **keystore** directory of **online machine**.
- Generate income observation wallet

Run the command on the **offline machine** to generate the income observation wallet:

```bash
mtool-client create_observewallet --keystore $MTOOLDIR/keystore/reward.json
```

 Enter the earning cold wallet password and return the generated observation wallet, as follows:

```
please input keystore password:
SUCCESS
wallet created at: keystore/reward_observed.json
```

- Copy the generated observation wallet file reward_observed.json to the **keystore** directory of the **online machine**.

### Connect to validation node

If authentication node information has been configured, ignore this step.

According to the MTool installed by the user on Windows or Ubuntu, select the verification node information configuration on the corresponding system, and perform the following steps on **`online machine`** to generate the verification node configuration information:

#### Configure verification node information under Windows

**`Online Machine`** is a Windows operating system with the following steps:

**step1.** Windows key + x, click Windows PowerShell (Administrator) (A), select Yes in the pop-up window, bring up the Administrator: powershell window, and copy the following 2 commands for execution.

```bash
$ env: chocolateyUseWindowsCompression = 'true'
Set-ExecutionPolicy -ExecutionPolicy Bypass
```

prompt:

```plain
Perform policy changes
Execution policies help you prevent execution of untrusted scripts. Changing execution policies can create security risks, as described in the about_Execution_Policies help topic at https: /go.microsoft.com/fwlink/? LinkID = 135170. Do you want to change the execution strategy?
[Y] Yes (Y) [A] All (A) [N] No (N) [L] No (L) [S] Pause (S) [?] Help (default is "N"):
```

Please enter: y and press Enter to end.

**step2.** copy the link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.bat> or <http://47.91.153.183/opensource/scripts/validator_conf.bat> go to the browser and download the script.

**step3.** Right-click validator_conf.bat and select Run as administrator:

> [!NOTE|style:flat|label:Note]
>
> - When prompted <font color=red> Please enter the platon node IP address: </font>please enter the PlatON node server IP address.
> - When prompting <font color=red> Please enter the platon chain id: </font>please enter the chain ID, and choose to input according to whether the network connected to the node is the main network or the test network (test network: 101).
> - When prompting<font color=red> Please enter the delegatedRewardRate (0 ~ 10000): </font>please enter a proportional dividend, ranging from 0 to 10000,Unit(Percentage), If you enter 5000, it means that the dividend ratio is 50%.
> - When prompted <font color=red> Enter your name: </font>please enter the username you entered when configuring PlatON node nginx.
> - When prompted <font color=red> Enter your password: </font>please enter the password you entered when configuring PlatON node nginx.
> - When prompted <font color=red> Enter your platon node name: </font>enter the name of the PlatON node.
> - When prompted <font color=red> Enter your platon node description: </font>please enter a PlatON node description.
> - When the prompt <font color=red> validator conf success </font> is displayed, the script is successfully executed. If the script is not successfully executed, please contact our official customer service to provide feedback.
> - Prompt <font color=red> Please press any key to continue .... </font>please press Enter to close the current cmd window.

#### Configure verification node information under Ubuntu

**`Online Machine`** is the operating procedure of the Ubuntu operating system:

**step1.** Download the script:

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.sh
```

or

```bash
wget http://47.91.153.183/opensource/scripts/validator_conf.sh
```

**step2.** Execute the command:

```bash
chmod + x validator_conf.sh && ./validator_conf.sh
```

> [!NOTE|style:flat|label:Note]
>
> - When prompted <font color=red> Please enter the platon node IP address: </font>please enter the PlatON node server IP address.
> - When prompting <font color=red> Please enter the platon chain id: </font>please enter the chain ID, and choose to input according to whether the network connected to the node is the main network or the test network (test network: 101).
> - When prompting<font color=red> Please enter the delegatedRewardRate (0 ~ 10000): </font>please enter a proportional dividend, ranging from 0 to 10000,Unit(Percentage), If you enter 5000, it means that the dividend ratio is 50%.
> - When prompted <font color=red> Enter your name: </font>please enter the username you entered when configuring PlatON node nginx.
> - When prompted <font color=red> Enter your password: </font>please enter the password you entered when configuring PlatON node nginx.
> - When prompted <font color=red> Enter your platon node name: </font>enter the name of the PlatON node.
> - When prompted <font color=red> Enter your platon node description: </font>please enter a PlatON node description.
> - When the prompt <font color=red> validator conf success </font> is displayed, and the validator_config.json content printed at the end is normal, it means that the script is executed successfully. If the script is not executed successfully, please contact us through our official customer service contact .

#### Verify node information configuration file description

After the configuration of the verification node information is completed, the verification node information file validator_config.json is generated in the validator subdirectory of the MTool installation directory.

```json
{
  "chainId": "101",
  "delegatedRewardRate": "5000",
  "nodePublicKey": "79ca603ef75d5954ec270802fa4e7b9bf045842bb7f3e95b849173f61d8a7cfef82b8687abef67f29645e068ff371da514f32b009b05f48062daa84f0b58ab6d",
  "blsPubKey": "a2e1c2e60eb8bb2af05fff4d07c8fce7c408fbe944be1a58194d9e9c9078cb7bb55b63311c8af107453ac182eef22a04cb9ff28cc3367f1e8459f8dcbe9f5c965a6f377f9ff9bb39a78e4e04fd27876137bb0a9aa4066d0277464f018e989e94",
  "benefitAddress": "0x32bec384344c2dc1ea794a4e149c1b74dd8467ef",
  "nodeAddress": "https://test:test@domain3",
  "nodePort": "16789",
  "nodeRpcPort": "443",
  "nodeName": "water-node",
  "details": "this is water-node",
  "externalId": "MyKeyBaseId",
  "webSite": "http://www.mycompany.com",
  "certificate": "C:/tools/mtool/current/ca.crt"
}
```

> [!NOTE|style:flat|label:Parameter Description] 
>
> - chainId: The chain of Beleyworld is 101.
> - delegatedRewardRate: It is used to set the proportion of dividends to the delegate.
> - nodePublicKey: Node ID, Can be viewed through the **nodeid** file under the node data directory **data**.
> - blsPubKey: BLS public key, Can be view it through the **blspub** file under the node data directory **data**.
> - benefitAddress: benefit wallet address.
> - nodeAddress: The node address, There are two cases of using Nginx and not using Nginx: 
>   - If you use Nginx, you need to use the **https** protocol, the format is: `https://test:test@domain3`.
>   - If you do not use Nginx, you need to use the **http** protocol. If MTool and the node are on the same machine or in the same local area network, you can use the intranet IP, otherwise use the public IP, the format is: `http://18.238.183.12`.
> - nodePort: The P2P port of the node. The default is 16789.
> - nodeRpcPort: There are two cases of using Nginx and not using Nginx:
>   - If Nginx is used, the port defaults to 443.
>   - If Nginx is not used, the port defaults to 6789.
> - nodeName: the name of the node.
> - details: node description information.
> - externalId: Corresponds to the avatar displayed by the browser. It can be registered on the official website of [keybase.io](https://keybase.io/). The corresponding value is: Register an account and generate a 16-bit public key.
> - webSite: company website.
> - certificate: ca certificate address. This parameter can be deleted if Nginx reverse proxy is not used.

## Basic operation flow

MTool commands and directories are different on Windows and Ubuntu:

### Generate transaction data

- Generate files to be signed

  Take the staking operation as an example, execute the staking operation command on the **online machine**, note that the wallet option command at this time is `--address`

```bash
mtool-client staking --amount 1000000 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
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

  Copy the to-be-signed file `MTOOLDIR/transaction_details/transaction_detail_20191108114241.csv` under the **online machine** to the **offline machine** via the storage medium.

### Offline Signature Transaction

- Generate transaction signature files

Execute the signing command under **offline machine** to sign the staking transaction:

```bash
mtool-client offlinesign --filelocation $MTOOLDIR/transaction_details/transaction_detail_20191108114241.csv
```

Note: `$MTOOLDIR/transaction_details/transaction_detail_20191108114241.csv` is the file to be signed generated in the previous step, and is modified to the actual file to be signed.

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
└────────┴────────┴──────────────────────────── ────┴────────┴───────┴───────┘
Need load 1 wallets for address: [0xa1548dd61010a742cd66fb86324ab3e29355864a]

operation finished
SUCCESS
File generated on transaction_signature/transaction_signature_20191108114625.csv
total: 1, to be signed: 1
success: 1, failure: 0
```

Note: transaction_signature/transaction_signature_20191108114625.csv is a signed transaction file.

- Copy transaction signature files to online machines

Copy the `signed file` transaction_signature_20191108114625.csv on the **offline machine** to the **online machine** via the storage medium.

### Send a signed transaction

- Execute online transaction order on **online machine** to complete the staking operation

```bash
mtool-client send_signedtx --filelocation $MTOOLDIR/transaction_signature/transaction_signature_20191108114625.csv --config $MTOOLDIR/validator/validator_config.json
```

Note: transaction_signature_20191108114625.csv is the transaction signature file generated in the previous step, and is modified to the actual signature file.

Enter `yes` to return the transaction result:

```bash
Send Transaction? (Yes | no)
yes
transaction 1 success
transaction hash: 0xf14f74386e6ef9027c48582d7faed3b50ab1ffdd047d6ba3afcf27791afb4e9b
SUCCESS
total: 1
success: 1, failure: 0
```

Note: prompting success and returning a transaction hash indicates that the signature transaction was sent successfully, otherwise the signature transaction failed to be sent.

## Detailed MTool operation

This section mainly describes the relevant commands (except for query operations) to generate csv-format transaction to-be-signed files on the **online machine**. The generated csv files will be saved in the `$MTOOLDIR/transaction_details` directory. Refer to [Basic Operation Flow] (#Basic Operation Flow) for the complete process of sending offline signature transactions.

### Ordinary transfer operation

- Excuting an order

```bash
mtool-client tx transfer --address $MTOOLDIR/keystore/staking_observed.json --amount "1" --recipient $ to_address --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> address: Observed wallet path to send the transaction
>
> amount: transfer amount, unit: LAT
>
> recipient: receiving address

### View wallet list

- Excuting an order

```bash
mtool-client account list
```

### Query balance based on wallet name

- Excuting an order

```bash
mtool-client account balance $ keystorename --config $MTOOLDIR/validator/validator_config.json
```

- Variable description

> $ keystorename: wallet file name，example:staking.json

### Query balance based on address

- Excuting an order

```bash
mtool-client account balance -a $ address --config $MTOOLDIR/validator/validator_config.json
```

- Parameters

> a: wallet address

### Initiate a staking operation

If the consensus nodes are deployed and the blocks have been synchronized successfully, you can use MTool for the staking operations. After the staking fund application is completed, ensure that the balance of the staking account is sufficient, and replace staking Amount according to the user's situation. The minimum threshold for staking is 1 million LAT.

Note: Please keep enough LAT in the staking account, so that subsequent transactions managed by the initiating node have sufficient transaction fees, such as voting for upgrade proposals, and unsecured transactions.

- Excuting an order

```bash
mtool-client staking --amount 1000000 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```
- Parameter Description

> address: staking observation wallet path
>
> amount: staking number, not less than 1000000lat- staking threshold, no more than 8 decimal places (use free amount for staking)
>
> restrictedamount: not less than 1000000lat- staking threshold, no more than 8 decimal points (using locked balance for staking)

### Modify validator information operation

- Excuting an order

```bash
mtool-client update_validator --name VerifierName --url "www.platon.com" --identity IdentifyID --delegated-reward-rate 100 --reward 0x33d253386582f38c66cb5819bfbdaad0910339b3 --introduction "Modify the verifier information operation" --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> name: validator name, no more than 30 bytes, supports letters, numbers, spaces, underscores and #, must start with a letter
>
> url: official website path, no more than 70 bytes, composed of alphanumeric characters
>
> identity: identity authentication ID, no more than 140 bytes, corresponds to the `externalId` field in the validator_config.json configuration file.
>
> delegated-reward-rate: delegated reward ratio, unit: million points, integer, range 0 ~ 10000, If you enter 5000, it means that the dividend ratio is 50%
>
> reward: return address, 42 characters (alphanumeric)
>
> introduction: introduction, brief description of the validator, no more than 280 bytes, English is recommended
>
> a: When executing the command, use the values in the configuration file as parameters to modify the verifier information

### Decommissioning operation

- Excuting an order

```bash
mtool-client unstaking --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> None

### Increase staking operation

- Excuting an order

```bash
mtool-client increasestaking --amount 5000000 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> amount: Use the account balance to increase the staking amount (LAT), the minimum added value is not less than 10, and the decimal point does not exceed 8 digits (use a free amount to increase the staking)
>
> restrictedamount: use the account lock balance to increase the amount of staking, not less than 10 staking threshold, the decimal point does not exceed 8 (use the lock balance to increase the staking)

### Submit Text Proposal Action

- Excuting an order

```bash
mtool-client submit_textproposal --pid_id 100 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> pid_id: GitHub ID

### Submit upgrade proposal operation

- Excuting an order

```bash
mtool-client submit_versionproposal --newversion 1.0.0 --end_voting_rounds 10 --pid_id 100 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> newversion: target upgrade version, x.x.x, number punctuation
>
> end_voting_rounds: the number of voting consensus rounds, the number of voting consensus rounds N, must satisfy 0 < N <= 4838 (about 2 weeks)
>
> pid_id: GitHub ID

### Submit Cancel Proposal

- Excuting an order

```bash
mtool-client submit_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --end_voting_rounds 12 --pid_id 100 --address $MTOOLDIR/keystore/staking_observed.json --config $ atorTOvalidator/validator
```

- Parameter Description

> proposalid: the ID of the proposal that needs to be cancelled
>
> end_voting_rounds: the number of voting consensus rounds, the number of voting consensus rounds N, must satisfy 0 < N <= 4838 (about 2 weeks)
>
> pid_id: GitHub ID

### Text proposal voting operation

- Excuting an order

```bash
mtool-client vote_textproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: text proposal ID, that is, the hash of the proposal transaction, 66 characters, alphanumeric
>
> opinion: voting options, yes, no, abstain-choose one

### Upgrade proposal voting operation

- Excuting an order

```bash
mtool-client vote_versionproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: upgrade proposal ID, that is, the hash of the proposed transaction, 66 characters, alphanumeric

### Cancel proposal voting

- Excuting an order

```bash
mtool-client vote_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: Cancel the proposal ID, that is, the hash of the proposed transaction, 66 characters, composed of alphanumeric characters
>
> opinion: voting options, yes, no, abstain-choose one

### Submit parameter proposal operation

- Excuting an order

```bash
mtool-client submit_paramproposal --pid_id 200 --module $ module --paramname $ paramname --paramvalue $ paramvalue --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> module: governance module parameters
>
> paramname: the name of the governance module parameter, pay attention to the case of the letters
>
> paramvalue: Governance module parameter value
>
> pid_id: GitHub ID

### Parameter proposal voting operation

- Excuting an order

```bash
mtool-client vote_paramproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: Cancel the proposal ID, that is, the hash of the proposed transaction, 66 characters, composed of alphanumeric characters
>
> opinion: voting options, yes, no, abstain-choose one

### Version declaration operation

- Excuting an order

```bash
mtool-client declare_version --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> None

### View help

- Excuting an order

```bash
mtool-client -h
```

- Parameter Description

> None

 