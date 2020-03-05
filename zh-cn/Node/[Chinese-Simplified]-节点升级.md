
PlatON网络在运行期间为保证网络能够不断迭代完善，需要所有节点遵循链上治理流程升级本地节点。其中备选节点需进行投票。

- 每个备选节点对每个提案只能投一次票
- 备选节点必须参与投票，否则将降级为备选节点候选人，不能参与共识获取区块奖励，也不能获得Staking奖励
- 如果错过投票期被降为备选节点候选人，可以通过本地升级到指定版本后发起版本声明交易，重新成为备选节点

## 获取升级提案信息

为保证节点能及时升级，不影响正常参与共识，节点需要及时关注升级提案信息。可以通过社区公告和链上查询获取升级提案的信息。

### 关注社区治理相关的公告

可以通过关注社区的公告及时获取升级提案的信息。当需要节点对升级提案进行投票时，社区会发布相关的[公告](https://forum.latticex.foundation/t/topic/1085)。例如：

```bash
提案详情：
  PIPID：2
  提案地址：https://platscan.test.platon.network/trade-detail?txHash=0x44c2b07551e3195acfc6ef674d78992bfeb445c7804f198c964ae6113af5a0e0

  ProposalID：0x44c2b07551e3195acfc6ef674d78992bfeb445c7804f198c964ae6113af5a0e0

  目标版本号：0.10.0
  投票周期：起始区块高度1079136, 截止区块高度1165480

  本次链上升级代码地址：
  代码分支：https://github.com/PlatONnetwork/PlatON-Go/tree/release-0.10.0
  commit ID: 0130bce6cb6fc4d45590053957214a7a229550ff
```

如上提案详情例子所示：发布的升级提案信息中会描述此次提案的ID、新的版本号、投票周期以及新版本的代码。

### 直接从链上查询

升级提案也可以直接在区块链浏览器 [PlatScan](https://platscan.test.platon.network/proposal) 中查询 。

## 升级操作过程

### 升级节点到指定的版本

根据从社区公告或区块链浏览器中获得的升级提案中的新版本号（假设为0.10.0），然后下载PlatON官方提供的脚本进行节点的升级。在**部署节点的机器**上进行如下步骤的操作：

> 如果下载脚本失败，请设置DNS 服务器为8.8.8.8。

- 首先下载脚本（脚本可存放任意位置）

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/update_platon.sh
  ```

  或者

  ```bash
  wget http://47.91.153.183/opensource/scripts/update_platon.sh
  ```

  **注意：如果在安装PlatON网络的节点时修改了节点数据存放的默认目录地址，则需要将update_platon.sh脚本中的 node_dir=~/platon-node 属性值改成修改后的节点数据目录地址。**

- 执行命令，更新节点版本

  ```bash
  chmod u+x update_platon.sh && ./update_platon.sh 0.10.0 --xxxnet
  ```
  
  **注：**
  
  ​    0.10.0为指定需要升级的版本号（版本号可从社区发布的相关公告中或通过区块链浏览器从链上获取）。
  
  ​    --xxxnet为指定某个网络，不填则默认为主网网络。
  
  ​    当出现 **”[sudo] password for platon:“** 类似提示时，需要输入当前登录机器的用户密码。
  
  ​    当出现**”Do you want to continue?“**提示时，输入：**y**（则表示继续执行，否则反之）。
  
  当执行的结果如下时表示版本升级成功：
  
  ```bash
  Currently installed version: 0.9.0==========
  Begin to install platon version: 0.10.0==========
  Node paused successfully==========
  Do you want to continue? [Y/n] y
  Uninstall current platon version: 0.9.0 successfully==========
  Install platon version: platon0.10.0 successfully==========
  Restart node succeeded============ 
  ```
  
  >当出现如下情况时，不进行版本升级，节点将以之前安装的版本继续运行：
  >
  >- 指定升级的版本不存在。
  >- 指定升级的版本不高于已安装的版本。
  >

### 进行投票操作

以下投票操作需要借助MTool工具实现，该工具分为离线和在线方式，可根据需要自行选择版本使用，详情请参考：

- [在线MTool](/zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册.md)
- [离线MTool](/zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册.md)

>  由于系统不同，MTool的命令和目录也有所不同，所以在下文中使用Ubuntu的以下命令和目录为例（使用时请替换成用户实际系统和安装的实际命令和目录）：
>
>  mtool-client表示MTool命令
>
>  ~/MTool表示MTool目录
>
>  ~/platon-node表示节点数据的目录地址

#### 获取当前区块高度

```bash
cd ~/platon-node/data && platon attach ipc:platon.ipc -exec platon.blockNumber
```

该命令返回当前区块高度，可用于判断是否在提案投票期的范围内，链接地址参考：[关注社区治理相关的公告](#关注社区治理相关的公告)，例如社区发布的投票期为[1079136, 1165480]，如果当前区块高度为1160000，则在投票期范围内；如果当前区块高度大于1165480，则表示过了投票期。只有当处于投票期范围内时则进行如下升级提案的投票操作。

#### 升级提案投票

- 如果在投票期内，则需要进行升级提案投票操作，否则跳过本节操作。

  ```bash
  mtool-client vote_versionproposal --proposalid 0x44c2b07551e3195acfc6ef674d78992bfeb445c7804f198c964ae6113af5a0e0 --keystore ~/MTool/keystore/staking.json --config ~/MTool/validator/validator_config.json
  ```

  **注：**0x44c2b07551e3195acfc6ef674d78992bfeb445c7804f198c964ae6113af5a0e0为社区公告提供的升级提案的ProposalID，详情请参考：[关注社区治理相关的公告](#关注社区治理相关的公告)。

  如果执行以上命令后，投票交易发送成功会返回**投票交易hash**。例如，当出现如下信息时表示投票交易发送成功，否则表示升级提案投票失败：

  ```bash
  operation finished
  transaction hash: 0x344d5c916a070453567fe95c6b79cd86bb70c248f96da98fb0ec3aa6617cc9a3
  SUCCESS
  ```

- 验证投票是否成功

  当投票交易发送成功后，只能表示这笔交易执行成功，所以还需要验证这个投票是否成功了。在**部署节点的机器**上的任意位置下载以下脚本并依次执行命令：

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/verify_transaction.sh
  ```
  
  或者

  ```bash
  wget http://47.91.153.183/opensource/scripts/verify_transaction.sh
  ```
  
  **注意：如果在安装PlatON网络的节点时修改了节点数据存放的默认目录地址，则需要将verify_transaction.sh脚本中的 node_dir=~/platon-node 属性值改成修改后的节点数据目录地址。**
  
  下载完脚本后，首先需要修改执行权限，再执行脚本：
  
  ```bash
  chmod u+x verify_transaction.sh && ./verify_transaction.sh 0x344d5c916a070453567fe95c6b79cd86bb70c248f96da98fb0ec3aa6617cc9a3
  ```
  
  **注：**其中0x344d5c916a070453567fe95c6b79cd86bb70c248f96da98fb0ec3aa6617cc9a3为升级提案投票操作返回的**投票交易Hash**，请根据实际返回值进行替换。
  
  当执行完脚本之后，如果出现如下信息，则表示升级提案投票成功，否则升级提案投票失败。
  
  ```bash
	 Transaction receipt obtained successfully==========
	 Analyzing transaction receipt==========
	 Transaction succeed.
  ```

#### 版本声明

- 如果当前已不在投票期内，且没有完成投票操作，则需要进行版本声明操作，使用以下MTool命令来进行版本声明的操作：

  ```bash
  mtool-client declare_version --keystore ~/MTool/keystore/staking.json --config ~/MTool/validator/validator_config.json
  ```

  **注：**执行以上命令后，如果版本声明交易发送成功，会返回**版本声明交易hash**。例如出现如下信息则表示交易发送成功，否则表示版本声明失败：

  ```bash
  operation finished
  transaction hash: 0x776d5be7363451540b7113771cf4263de6a18973ed8904796a561acf37e58ff2
  SUCCESS
  ```

- 验证版本声明是否成功

  当版本声明交易发送成功时，只能表示这笔交易执行成功，所以还需要验证版本声明是否成功，在**部署节点的机器**上的任意位置下载以下脚本并依次执行命令（**如果已经下载verify_transaction.sh脚本，则无需再次下载，直接执行命令即可**）：

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/verify_transaction.sh
  ```
  
  或者

  ```bash
  wget http://47.91.153.183/opensource/scripts/verify_transaction.sh
  ```
  
  **注意：如果在安装PlatON网络的节点时修改了节点数据存放的默认目录地址，则需要将verify_transaction.sh脚本中的 node_dir=~/platon-node 属性值改成修改后的节点数据目录地址。**
  
  下载完脚本后，首先需要修改执行权限，再执行脚本：
  
  ```bash
  chmod u+x verify_transaction.sh && ./verify_transaction.sh 0x776d5be7363451540b7113771cf4263de6a18973ed8904796a561acf37e58ff2
  ```
  
  注：其中0x776d5be7363451540b7113771cf4263de6a18973ed8904796a561acf37e58ff2为版本声明操作返回的**交易hash**，请根据实际返回值进行修改。
  
  当执行完脚本后，如果出现如下信息，则表示版本声明成功，否则版本声明失败。
  
  ```bash
   Transaction receipt obtained successfully==========
   Analyzing transaction receipt==========
   Transaction succeed.
  ```

## 验证节点升级结果

当执行完上面所有操作后，需要验证操作结果是否成功，则首先需要下载以下的验证脚本，该脚本会执行验证操作并将验证结果打印出来。在**部署节点的机器**上的任意位置下载以下脚本并进行操作：

- 下载验证脚本

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/verify_votResult.sh
  ```

  或者

  ```bash
  wget http://47.91.153.183/opensource/scripts/verify_votResult.sh
  ```

  **注意：如果在安装PlatON网络的节点时修改了节点数据存放的默认目录地址，则需要将verify_votResult.sh脚本中的 node_dir=~/platon-node 属性值改成修改后的节点数据目录地址。**

- 下载rlp工具

  由于验证脚本依赖ppos_tool工具，所以需要在与验证脚本同目录下下载该工具，首先执行以下下载命令：
  
  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/ppos_tool
  ```
  
  或者
  
  ```bash
  wget http://47.91.153.183/opensource/scripts/ppos_tool
  ```
  
- 执行验证脚本

  该命令需要在提案投票截止区块高度后再过1000个块（一个共识轮为250个区块，则为四个共识轮的区块数，假设截止块高为1165480，则在1166480块）之后执行以下脚本检查升级结果：
  
  ```bash
  chmod u+x verify_votResult.sh && chmod u+x ppos_tool && ./verify_votResult.sh 0x44c2b07551e3195acfc6ef674d78992bfeb445c7804f198c964ae6113af5a0e0
  ```
  
  > 其中0x44c2b07551e3195acfc6ef674d78992bfeb445c7804f198c964ae6113af5a0e0为**升级提案的ProposalID**，请根据实际公告中的提案ID进行替换，链接地址参考：[关注社区治理相关的公告](#关注社区治理相关的公告)。
  
  当执行脚本后，打印结果如下则表示升级成功：
  
  ```bash
  Get proposal success ==========
  The effective block of the proposal is: 1166480
  Current block height is: 1166666
  The height of the block has reached the height of the effective block of the proposal, and the verification begins ==========
  Start to verify upgrade proposal voting results ==========
  Upgrade proposal voting result verified successfully ==========
  Start to verify that the proposal version number equals the binary version number equals the effective version number on the chain ==========
  Upgrade proposal version Verification succeeded ==============
  Start to verify the pledge status of the node =============
  Node pledge information obtained successfully ==========
  The node did not exit the verifier list. The upgrade version succeeded. The effective version of the current chain is: 2560
  ```
  
  > 该验证脚本会进行以下检查：
  >
  >  - 提案是否通过
  >
  >  - 当前节点版本号是否跟提案版本号一致
  > 
  >  - 当前节点的质押状态，如果不为0，则退出备选节点候选人
