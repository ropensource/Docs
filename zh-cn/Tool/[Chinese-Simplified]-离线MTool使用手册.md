## 安装MTool

### 在线MTool

如果已经安装在线MTool，可以忽略此步骤。

另外，本文档分别介绍Windows和Ubuntu环境下MTool的操作，用户可根据自己的资源进行选择。

#### Windows下安装在线MTool

步骤如下：

- 下载MTool安装包

  在在线机器上，复制链接<https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.8.0.0/mtool-setup.exe>或者 <http://47.91.153.183/mtool/mtool-setup/0.8.0.0/mtool-setup.exe> 到浏览器下载MTool安装包。

- 安装MTool

  双击mtool-setup.exe进行安装。默认安装目录为 C:\tools，建议不要更改此安装目录。弹出界面显示**Completing the mtool Setup Wizard**信息表示安装成功，点击**Finish**即可。

#### Ubuntu下安装在线MTool

步骤如下：

**step1.** 下载mtool工具包

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/0.8.0.0/mtool-client.zip
```

或者

``` bash
wget http://47.91.153.183/mtool/0.8.0.0/mtool-client.zip
```

**step2.** 解压mtool工具包

``` bash
unzip mtool-client.zip && cd mtool-client
```

**step3.** 下载脚本

>[!NOTE|style:flat|label:注意]
>
>脚本下载到<font color=red>mtool-client</font> 目录下，否则脚本无法找到新版本mtool的路径。

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/mtool_install.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/mtool_install.sh
```

**step4.** 执行命令

```
chmod +x mtool_install.sh && ./mtool_install.sh
```

> [!NOTE|style:flat|label:注意]
>
> - 提示 <font color=red>Install mtool succeed.</font> 时，表示 MTool 安装成功，未安装成功时，请通过我们的官方客服联系方式反馈具体问题。
> - 安装完成之后，需要<font color=red>重启终端</font>，让新添加的环境变量生效。

### 离线MTool

安全考虑，离线MTool应该安装在离线机器（不连接任何网络和WIFI）上。

另外，本文档分别介绍Windows和Ubuntu环境下的MTool操作，用户可根据自己的资源进行选择。

#### Windows下安装离线MTool

步骤如下：

- 下载MTool安装包

  在有网络的机器上，复制链接<https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/mtool-setup/0.8.0.0/mtool-setup.exe>或者 <http://47.91.153.183/mtool/mtool-setup/0.8.0.0/mtool-setup.exe> 到浏览器下载MTool安装包。

- 通过安全存储介质（移动U盘或者移动硬盘）将安装文件mtool-setup.exe转到**离线机器**下。

- 在**离线机器**上安装MTool

  双击mtool-setup.exe进行安装。默认安装目录为 C:\tools , 建议不要更改此安装目录。弹出界面显示**Completing the mtool Setup Wizard**信息表示安装成功，点击**Finish**即可。
  
#### Ubuntu下安装离线MTool

步骤如下：

- 下载MTool安装包

  在有网络的机器上，下载mtool安装包，执行命令：

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/mtool/0.8.0.0/mtool-client.zip
```

或者

``` bash
wget http://47.91.153.183/mtool/0.8.0.0/mtool-client.zip
```

- 在**离线机器**上执行命令：

```bash
java -version
```

>[!NOTE|style:flat|label:注意]
>
>- 如果返回 <font color=red>Command 'java' not found</font>，表示没有安装jdk，需要下载jdk。
>- 如果返回版本号相关信息，表示已经安装jdk，不需要下载jdk。

- 在有网络的机器上下载jdk

如果**离线机器**已经安装jdk，跳过此步骤。

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/third-tools/jdk-8u221-linux-x64.tar.gz 
```

或者

``` bash
wget http://47.91.153.183/third-tools/jdk-8u221-linux-x64.tar.gz
```

- 下载安装脚本

在有网络的机器上下载install_off_line_mtool.sh脚本：

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/install_off_line_mtool.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/install_off_line_mtool.sh
```

- 通过安全存储介质（移动U盘或者移动硬盘）将压缩文件mtool-client.zip和install_off_line_mtool.sh脚本转到**离线机器**下，如果需要安装jdk，把jdk-8u221-linux-x64.tar.gz和mtool-client.zip，install_off_line_mtool.sh脚本放在同一个目录下。
- 在**离线机器**上安装MTool，执行命令：

如果需要安装jdk，解压jdk-8u221-linux-x64.tar.gz，否则跳过此命令：

```bash
tar -xzvf jdk-8u221-linux-x64.tar.gz
```

执行安装步骤：

``` bash
unzip mtool-client.zip && chmod +x install_off_line_mtool.sh && ./install_off_line_mtool.sh
```

> [!NOTE|style:flat|label:注意]
>
> - 离线机器上需要提前安装unzip。
> - 提示 <font color=red>Install off line mtool succeed. </font> 时，表示离线MTool 安装成功，未安装成功时，请通过我们的官方客服联系方式反馈具体问题。
> - 安装完成之后，需要<font color=red>重启终端 </font>，让新添加的环境变量生效。

## 配置

Windows和Ubuntu下MTool的命令及目录有所区别：

- MTool命令

  - Windows：mtool-client.bat
  - Ubuntu：mtool-client

- MTool目录

  - Windows：`%MTOOLDIR%`

  - Ubuntu：`$MTOOLDIR`

>  说明：
>
>   - MTool命令用变量`$mtool-client`代替；
>   - MTool目录用变量`$MTOOLDIR`代替；
>
>  **`用户根据自己安装的系统进行选择。`**

### 钱包配置

#### 基本概念

* 冷钱包：存储在离线机器上的钱包，不能暴露在互联网。
* 观察钱包：包含冷钱包地址的钱包，无法做交易，只能查看数据。
* 热钱包：暴露在互联网的钱包。

#### 创建冷钱包

如果用户没有钱包，在**离线机器**上执行命令生成质押钱包和收益钱包；如果已经有钱包，可通过存储介质将钱包文件拷贝到离线MTool的解压包的**keystore**目录下，跳过本步骤。

- 创建质押钱包

```bash
$mtool-client account new staking
```

- 创建收益钱包

```bash
$mtool-client account new reward
```

#### 生成观察钱包

- 生成质押观察钱包

在**离线机器**上执行命令生成质押观察钱包：

```bash
$mtool-client create_observewallet --keystore $MTOOLDIR/keystore/staking.json
```

 输入质押冷钱包密码并返回生成的观察钱包，如下：

```
please input keystore password: 
SUCCESS
wallet created at: keystore/staking_observed.json
```

- 将生成的观察钱包文件staking_observed.json拷贝到**在线机器**的**keystore**目录下。
- 生成收益观察钱包

在**离线机器**上执行命令生成收益观察钱包：

```bash
$mtool-client create_observewallet --keystore $MTOOLDIR/keystore/reward.json
```

 输入收益冷钱包密码并返回生成的观察钱包，如下：

```
please input keystore password: 
SUCCESS
wallet created at: keystore/reward_observed.json
```

- 将生成的观察钱包文件reward_observed.json拷贝到**在线机器**的**keystore**目录下。

### 连接到验证节点

如果已经配置验证节点信息，忽略此步骤。

根据用户在Windows或Ubuntu上安装的MTool，选择对应系统上的验证节点信息配置，在**`在线机器`**上执行以下步骤生成验证节点配置信息：

#### Windows下配置验证节点信息

**`在线机器`**是Windows操作系统的操作步骤如下：

**step1.** 浏览器复制链接 <https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.bat>  或者 <http://47.91.153.183/opensource/scripts/validator_conf.bat> 下载脚本。

**step2.** 鼠标右键点击 validator_conf.bat， 选择以管理员身份运行，结果如下：

> [!NOTE|style:flat|label:注意]
>
> - 提示 <font color=red> Please enter the platon node IP address: </font>时，请输入 PlatON 节点服务器 ip 地址。
> - 提示 <font color=red> Please enter the platon chain id: </font>时，请输入链ID。
> - 提示 <font color=red> Please enter the delegatedRewardRate(0~10000):</font>时，请输入比例分红，范围从0到10000。
> - 提示 <font color=red> Enter your name: </font> 时，请输入配置 PlatON节点 nginx 时输入的用户名。
> - 提示 <font color=red> Enter your password: </font>时，请输入配置 PlatON节点 nginx 时输入的密码。
> - 提示<font color=red> Enter your platon node name:</font>时，请输入 PlatON 节点的名称。
> - 提示<font color=red> Enter your platon node description:</font>时，请输入 PlatON 节点描述。
> - 提示<font color=red> validator conf success</font>时，表示脚本执行成功，未执行成功时，请通过我们的官方客服联系方式反馈具体问题。
> - 提示<font color=red> 请按任意键继续. . .</font> 时，请输入回车键关闭当前 cmd 窗口。

#### Ubuntu下配置验证节点信息

**`在线机器`**是Ubuntu操作系统的操作步骤如下：

**step1.** 下载脚本：

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/validator_conf.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/validator_conf.sh
```

**step2.** 执行命令：

```bash
chmod +x validator_conf.sh && ./validator_conf.sh
```

> [!NOTE|style:flat|label:注意]
>
> - 提示 <font color=red> Please enter the platon node IP address: </font>时，请输入 PlatON 节点服务器 ip 地址。
> - 提示 <font color=red> Please enter the platon chain id: </font>时，请输入链ID。
> - 提示 <font color=red> Please enter the delegatedRewardRate(0~10000):</font>时，请输入比例分红，范围从0到10000。
> - 提示 <font color=red> Enter your name: </font> 时，请输入配置 PlatON节点 nginx 时输入的用户名。
> - 提示 <font color=red> Enter your password: </font>时，请输入配置 PlatON节点 nginx 时输入的密码。
> - 提示<font color=red> Enter your platon node name:</font>时，请输入 PlatON 节点的名称。
> - 提示<font color=red> Enter your platon node description:</font>时，请输入 PlatON 节点描述。
> - 提示<font color=red> validator conf success</font>，并最后打印出的validator_config.json内容正常时，表示脚本执行成功，未执行成功时，请通过我们的官方客服联系方式反馈具体问题。


## 基本操作流程

Windows和Ubuntu下MTool的命令及目录有所区别：

- MTool命令

  - Windows：mtool-client.bat
  - Ubuntu：mtool-client

- MTool目录

  - Windows：`%MTOOLDIR%`

  - Ubuntu：`$MTOOLDIR`

>  说明：
>
>   - MTool命令用变量`$mtool-client`代替；
>   - MTool目录用变量`$MTOOLDIR`代替；
>
>  **`用户根据自己安装的系统进行选择。`**

### 生成交易数据

- 生成待签名文件

  以质押操作为例，在**在线机器**上执行质押操作命令， 注意此时的钱包选项命令为`--address`

```bash
$mtool-client staking --amount 1000000 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

注：staking_observed.json为观察钱包，根据实际观察钱包进行修改。

返回生成对应的待签名文件：

```bash
operation finished
SUCCESS
File generated on transaction_details/transaction_detail_20191108114241.csv
```

注：其中transaction_details/transaction_detail_20191108114241.csv为待签名交易文件。

- 拷贝待签名文件到**离线机器**

  通过存储介质把**在线机器**下的待签名文件`$MTOOLDIR/transaction_details/transaction_detail_20191108114241.csv`拷贝到**离线机器**。

### 离线签名交易

- 生成交易签名文件

在**离线机器**下执行签名命令对该质押交易签名：

```bash
$mtool-client offlinesign --filelocation $MTOOLDIR/transaction_details/transaction_detail_20191108114241.csv
```

注：`$MTOOLDIR/transaction_details/transaction_detail_20191108114241.csv`为上一步骤生成的待签名文件，修改为实际的待签名文件。

输入对应冷钱包密码并返回签完名的文件，文件内容如下：

```csv
 ┌────────┬────────┬────────┬────────┬────────┬────────┬────────┬───────┬───────┐
│Type    │From    │To      │Account │Amount  │Fee     │Nonce   │Create │Chain  │
│        │        │        │Type    │        │        │        │Time   │Id     │
├────────┼────────┼────────┼────────┼────────┼────────┼────────┼───────┼───────┤
│STAKING │0xa1548d│0x100000│FREE_AMO│5000000.│0.043210│0       │2019-10│100    │
│        │d61010a7│00000000│UNT_TYPE│00000000│00000000│        │-11T13:│       │
│        │42cd66fb│00000000│        │00000000│0000    │        │54:06.8│       │
│        │86324ab3│00000000│        │00      │        │        │97     │       │
│        │e2935586│00000000│        │        │        │        │       │       │
│        │4a      │02      │        │        │        │        │       │       │
└────────┴────────┴────────┴────────┴────────┴────────┴────────┴───────┴───────┘
Need load 1 wallets for address: [0xa1548dd61010a742cd66fb86324ab3e29355864a]

operation finished
SUCCESS
File generated on transaction_signature/transaction_signature_20191108114625.csv
total: 1, to be signed: 1
success: 1, failure: 0
```

注：其中transaction_signature/transaction_signature_20191108114625.csv为已签名交易文件。

- 将交易签名文件拷贝到在线机器

通过存储介质把**离线机器**上的**`已签名文件`** transaction_signature_20191108114625.csv 拷贝到**在线机器**。

###  发送签名交易

- 在**在线机器**执行交易上链命令完成质押操作

```bash
$mtool-client send_signedtx --filelocation $MTOOLDIR/transaction_signature/transaction_signature_20191108114625.csv --config $MTOOLDIR/validator/validator_config.json
```

注：其中transaction_signature_20191108114625.csv为上一步骤生成的交易签名文件，修改为实际的签名文件。

输入`yes`返回交易结果：

```bash
Send Transaction? (yes|no)
yes
transaction 1 success
transaction hash: 0xf14f74386e6ef9027c48582d7faed3b50ab1ffdd047d6ba3afcf27791afb4e9b
SUCCESS
total: 1
success: 1, failure: 0
```

注：提示success并返回transaction hash表示签名交易发送成功，否则发送签名交易失败。

##  MTool操作详解

此章节主要描述在**在线机器**上生成csv格式的交易待签名文件的相关命令（除了查询操作），生成的csv文件会保存在`$MTOOLDIR/transaction_details`目录下。完整的发送离线签名交易流程可参考[基本操作流程](#基本操作流程)。

另外Windows和Ubuntu下MTool的命令及目录有所区别：

- MTool命令

  - Windows：mtool-client.bat
  - Ubuntu：mtool-client

- MTool目录

  - Windows：`%MTOOLDIR%`

  - Ubuntu：`$MTOOLDIR`

>  说明：
>
>   - MTool命令用变量`$mtool-client`代替；
>   - MTool目录用变量`$MTOOLDIR`代替；
>
>  **`用户根据自己安装的系统进行选择。`**

### 普通转账操作

- 执行命令

```bash
$mtool-client tx transfer --address $MTOOLDIR/keystore/staking_observed.json --amount "1" --recipient $to_address --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

>address: 发送转账交易的观察钱包路径
>
>amount：转账金额，单位：LAT
>
>recipient：接收地址

### 查看钱包列表

- 执行命令

```bash
$mtool-client account list
```

### 根据钱包名称查询余额

- 执行命令

```bash
$mtool-client account balance $keystorename --config $MTOOLDIR/validator/validator_config.json
```

- 变量说明

>$keystorename：钱包文件名称

### 根据地址查询余额

- 执行命令

```bash
$mtool-client account balance -a $address --config $MTOOLDIR/validator/validator_config.json
```

- 参数

> a：钱包地址

### 发起质押操作

​		如果共识节点部署完成，并且已经同步区块成功，您就可以使用MTool进行质押操作。质押资金申请完成后，确保质押账户余额足够，根据用户情况替换{质押金额}，质押最低门槛为100万LAT。

- 执行命令

```bash
$mtool-client staking --amount 1000000 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```
- 参数说明

> address: 质押观察钱包路径
>
> amount: 质押数，不少于1000000lat-质押门槛，小数点不超过8位（使用自由金额质押）
>
> restrictedamount: 不少于1000000lat-质押门槛，小数点不超过8位（使用锁仓余额质押）

### 修改验证人信息操作 

- 执行命令

```bash
$mtool-client update_validator --name VerifierName --url "www.platon.com" --identity IdentifyID --delegated-reward-rate 100 --reward 0x33d253386582f38c66cb5819bfbdaad0910339b3 --introduction "Modify the verifier information operation" --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json --a
```

- 参数说明

> name：验证人名称,不超过30字节，支持字母、数字、空格、上下划线及#,必须字母开头
>
> url：官网路径,不超过70字节，数字字母组成
>
> identity：身份认证ID，不超过140字节
>
> delegated-reward-rate：委托奖励比例，单位：万分比，整数，范围0~10000
>
> reward：收益地址，42字符（字母数字）
>
> introduction：简介，验证人简要介绍说明，不超过280字节，建议英文
>
> a：执行命令时，用配置文件里面的值作参数去修改验证人信息

### 解质押操作

- 执行命令

```bash
$mtool-client unstaking --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> 无

### 增加质押操作

- 执行命令

```bash
$mtool-client increasestaking --amount 5000000 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> amount：用账户余额来增加质押量(LAT)，不少于10最小增加值，小数点不超过8位（使用自由金额增加质押）
>
> restrictedamount： 用账户锁仓余额来增加质押量，不少于10质押门槛，小数点不超过8位（使用锁仓余额增加质押）

### 提交文本提案操作

- 执行命令

```bash
$mtool-client submit_textproposal --pid_id 100 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> pid_id：GitHub ID

### 提交升级提案操作

- 执行命令

```bash
$mtool-client submit_versionproposal --newversion 1.0.0 --end_voting_rounds 10 --pid_id 100 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> newversion：目标升级版本，x.x.x，数字加标点
>
> end_voting_rounds：投票共识轮数，投票共识轮数N，必须满足0 < N <= 2419（约为2周）
>
> pid_id：GitHub ID

### 提交取消提案操作

- 执行命令

```bash
$mtool-client submit_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --end_voting_rounds 12 --pid_id 100 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> proposalid：需要被取消的提案ID，即发起提案交易的hash，66字符，字母数字组成
>
> end_voting_rounds：投票共识轮数，投票共识轮数N，必须满足0 < N <=2419（约为2周）
>
> pid_id：GitHub ID

### 文本提案投票操作

- 执行命令

```bash
$mtool-client vote_textproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> proposalid：文本提案ID，即发起提案交易的hash，66字符，字母数字组成
>
> opinion：投票选项，yes、no、abstain-三选一

### 升级提案投票操作

- 执行命令

```bash
$mtool-client vote_versionproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> proposalid：升级提案ID，即发起提案交易的hash，66字符，字母数字组成

### 取消提案投票操作

- 执行命令

```bash
$mtool-client vote_cancelproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> proposalid：取消提案ID，即发起提案交易的hash，66字符，字母数字组成
>
> opinion：投票选项，yes、no、abstain-三选一

###  提交参数提案操作

- 执行命令

```bash
$mtool-client submit_paramproposal --pid_id 200 --module $module --paramname $paramname --paramvalue $paramvalue --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> module：治理模块参数
>
> paramname：治理模块参数名，注意字母大小写
>
> paramvalue：治理模块参数值
>
> pid_id：GitHub ID

###  参数提案投票操作

- 执行命令

``` bash
$mtool-client  vote_paramproposal --proposalid 0x444c3df404bc1ce4d869166623514b370046cd37cdfa6e932971bc2f98afd1a6 --opinion yes --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> proposalid：取消提案ID，即发起提案交易的hash，66字符，字母数字组成
>
> opinion：投票选项，yes、no、abstain-三选一

### 版本声明操作

- 执行命令

```bash
$mtool-client declare_version --address $MTOOLDIR/keystore/staking_observed.json --config $MTOOLDIR/validator/validator_config.json
```

- 参数说明

> 无

### 查看帮助

- 执行命令

```bash
$mtool-client -h
```

- 参数说明

> 无