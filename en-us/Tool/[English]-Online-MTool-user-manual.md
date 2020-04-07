
## Install Online MTool

If online MTool is already installed, you can ignore this step.

In addition, this document introduces the operation of MTool under Windows and Ubuntu respectively. Users can choose according to their own resources.

### Install MTool under Windows

Proceed as follows:

- Download MTool installation package

  On the online machine, copy the link <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.11.0/mtool-setup.exe> or <http://47.91.153.183/mtool/mtool-setup/0.11.0/mtool-setup.exe> go to the browser and download the MTool installation package.

- Install MTool

  Double-click mtool-setup.exe to install it. The default installation directory is C:\tools, it is recommended not to change this installation directory. The pop-up interface displays the message **Completing the mtool Setup Wizard**, indicating that the installation was successful. Click **Finish**.

### Install MTool under Ubuntu

Proceed as follows:

**step1.** Download mtool toolkit

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/0.11.0/mtool-client.zip
```

or

```bash
wget http://47.91.153.183/mtool/0.11.0/mtool-client.zip
```

**step2.** Extract the mtool toolkit

```bash
(if ! command -v unzip;then sudo apt install unzip; fi;) && unzip mtool-client.zip && mv mtool-client*/ mtool-client && cd mtool-client
```

**step3.** Download script

> [!NOTE|style:flat|label:Note]
>
> The script is downloaded to the <font color=red>mtool-client</font> directory, otherwise the script cannot find the path of the new version of mtool.

```bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.sh
```

or

```bash
wget http://47.91.153.183/opensource/scripts/mtool_install.sh
```

**step4.** execute command

```bash
chmod +x mtool_install.sh && ./mtool_install.sh
```

> [!NOTE|style:flat|label:Note]
>
> - When the message <font color=red>Install mtool succeed.</font> is displayed, MTool is successfully installed. If it is not successfully installed, please contact our official customer service to provide feedback on specific issues.
> - After installation is complete, you need to <font color=red>restart the terminal</font> for the newly added environment variables to take effect.

## Configure Online MTool

The environment variables of MTool directories under Windows and Ubuntu are different:

- MTool Catalog

  - Windows:`%MTOOLDIR%`

  - Ubuntu:`$MTOOLDIR`

> Explanation:
>
> - MTool directory is replaced with the variable`$MTOOLDIR`;
>
> **`Users choose according to their installed system.`**

### Create Wallet

In PlatON, two wallets are created to participate in the verification node for block generation. If you already have a wallet, you can skip this step by copying the wallet file to the `$MTOOLDIR/keystore` directory.

- Staking wallet

  The staking wallet is used to staking tokens, and only after being successfully staking can it become a candidate node candidate.
  Run the following command to create a staking wallet:

  ```bash
  mtool-client account new staking
  ```

   Enter the password once and confirm the password again to create a wallet file. After the creation is successful, a staking wallet file`staking.json` will be generated in the directory `$MTOOLDIR/keystore`.

- Reward wallet

  It is used to collect block rewards and Staking rewards. Staking rewards are uniformly distributed to verification nodes, which are distributed by the verification nodes themselves.
  Run the following command to create a revenue wallet:

  ```
  mtool-client account new reward
  ```

   Enter the password once and confirm the password again to create the wallet file. After the creation is successful, the staking wallet file`reward.json` will be generated in the directory `$MTOOLDIR/keystore`.

### Configure verification node information

According to the MTool installed by the user on Windows or Ubuntu, select the verification node information configuration on the corresponding system.

#### Configure verification node information under Windows

The steps are as follows:

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

**step3.** Right-click `validator_conf.bat` and select Run as administrator:

> [!NOTE|style:flat|label:Note]
>
> - When prompted <font color=red>Please enter the platon node IP address: </font> please enter the PlatON node server IP address.
> - When prompting <font color=red>Please enter the platon chain id: </font> please enter the chain ID, and choose to input according to whether the network connected to the node is the main network or the test network (test network: 101).
> - When prompting<font color=red>Please enter the delegatedRewardRate (0 ~ 10000): </font> please enter a proportional dividend, ranging from 0 to 10000, Unit(Percentage), If you enter 5000, it means that the dividend ratio is 50%.
> - When prompted <font color=red>Enter your name: </font> please enter the username you entered when configuring PlatON node nginx.
> - When prompted <font color=red>Enter your password: </font> please enter the password you entered when configuring PlatON node nginx.
> - When prompted <font color=red>Enter your platon node name: </font> enter the name of the PlatON node.
> - When prompted <font color=red>Enter your platon node description: </font> please enter a PlatON node description.
> - When the prompt <font color=red>validator conf success</font> is displayed, the script is successfully executed. If the script is not successfully executed, please contact our official customer service to provide feedback.
> - Prompt <font color=red>Please press any key to continue .... </font> please press Enter to close the current cmd window.

#### Configure verification node information under Ubuntu

The steps are as follows:

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
chmod +x validator_conf.sh && ./validator_conf.sh
```

> [!NOTE|style:flat|label:Note]
>
> - When prompted <font color=red>Please enter the platon node IP address: </font> please enter the PlatON node server IP address.
> - When the prompt <font color=red>Please enter the platon chain id: </font> please enter the chain ID, and select it according to whether the connected network is the main network or the test network (test network: 101).
> - When prompting <font color=red>Please enter the delegatedRewardRate (0 ~ 10000): </font> please enter a proportional dividend, ranging from 0 to 10000, Unit(Percentage), If you enter 5000, it means that the dividend ratio is 50%.
> - When prompted <font color=red>Enter your name: </font> please enter the username you entered when configuring PlatON node nginx.
> - When prompted <font color=red>Enter your password: </font> please enter the password you entered when configuring PlatON node nginx.
> - When prompted <font color=red>Enter your platon node name: </font> enter the name of the PlatON node.
> - When prompted <font color=red>Enter your platon node description: </font> please enter a PlatON node description.
> - When the prompt <font color=red>validator conf success</font> is displayed, and the validator_config.json content printed at the end is normal, it means that the script is executed successfully. If the script is not executed successfully, please contact us through our official customer service contact .

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

## Detailed Online MTool Operation

### Ordinary transfer operation

- Excuting an order

```bash
mtool-client tx transfer --keystore $MTOOLDIR/keystore/staking.json --amount "1" --recipient $ to_address --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> keystore: path of the wallet sending the transaction
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
mtool-client account balance $keystorename --config $MTOOLDIR/validator/validator_config.json
```

- Variable description

> $ keystorename: wallet file name，example:staking.json

### Query balance based on address

- Excuting an order

```bash
mtool-client account balance -a $address --config $MTOOLDIR/validator/validator_config.json
```

- Parameters

> a: wallet address

### Initiate a staking operation

If the deployment of the consensus node is complete and the block has been synchronized successfully, you can use MTool for staking operations. After the staking fund application is completed, ensure that the balance of the staking account is sufficient, and replace the staking amount according to the user's situation. The minimum threshold for staking is 1 million LAT.

Note: Please keep enough LAT in the staking account, so that the transactions initiated by the subsequent node management have sufficient transaction fees, such as voting for upgrading proposals, and unsecured transactions.

- Excuting an order

```bash
mtool-client staking --amount 1000000 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```
Tip:**please input keystore password:**Enter the password of the staking wallet and press Enter. If the following information is displayed, the staking is successful:

```bash
operation finished
transaction hash:
0x89b964d27d0caf1d8bf268f721eb123c4af57aed36187bea90b262f4769eeb9b
SUCCESS
```

- Parameter Description

> amount: staking number, not less than 1000000lat-staking threshold, no more than 8 decimal places
>
> restrictedamount: not less than 1000000lat- staking threshold, no more than 8 decimal points (staking using locked balance)

### Modify validator information operation

- Excuting an order

```bash
mtool-client update_validator --name VerifierName --url "http://www.platon.com" --identity IdentifyID --delegated-reward-rate 100 --reward 0x33d253386582f38c66cb5819bfbdaad0910339b3 --introduction "Modify the verifier information operation" --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> name: validator name, no more than 30 bytes, supports letters, numbers, spaces, underscores and #, must start with a letter
>
> url: official website path, no more than 70 bytes, composed of alphanumeric characters
>
> identity: identity authentication ID, no more than 140 bytes, corresponds to the `externalId` field in the validator_config.json configuration file
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
mtool-client unstaking --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> None

### Increase staking operation

- Excuting an order

```bash
mtool-client increasestaking --amount 5000000 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> amount: Use the account balance to increase the staking amount (LAT), the minimum added value is not less than 10, and the decimal point does not exceed 8 digits
>
> restrictedamount: use the account balance to increase the amount of staking, not less than 10 staking threshold, the decimal point does not exceed 8

### Submit Text Proposal Action

- Excuting an order

```bash
mtool-client submit_textproposal --pid_id 100 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> pid_id: GitHub ID

### Submit upgrade proposal operation

- Excuting an order

```bash
mtool-client submit_versionproposal --newversion 0.8.0 --end_voting_rounds 345 --pid_id 100 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
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
mtool-client submit_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --end_voting_rounds 12 --pid_id 100 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR_validator/validator
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
mtool-client vote_textproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: text proposal ID, that is, the hash of the proposal transaction, 66 characters, alphanumeric
>
> opinion: voting options, yes, no, abstain-choose one

### Upgrade proposal voting operation

- Excuting an order

```bash
mtool-client vote_versionproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: upgrade proposal ID, that is, the hash of the proposed transaction, 66 characters, alphanumeric

### Cancel proposal voting

- Excuting an order

```bash
mtool-client vote_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: Cancel the proposal ID, that is, the hash of the proposed transaction, 66 characters, composed of alphanumeric characters
>
> opinion: voting options, yes, no, abstain-choose one

### Submit parameter proposal operation

- Excuting an order

```bash
mtool-client submit_paramproposal --pid_id 200 --module $ module --paramname $ paramname --paramvalue $ paramvalue --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
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
mtool-client vote_paramproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
```

- Parameter Description

> proposalid: Cancel the proposal ID, that is, the hash of the proposed transaction, 66 characters, composed of alphanumeric characters
>
> opinion: voting options, yes, no, abstain-choose one

### Version declaration operation

- Excuting an order

```bash
mtool-client declare_version --keystore $MTOOLDIR/keystore/staking.json --config $MTOOLDIR/validator/validator_config.json
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
