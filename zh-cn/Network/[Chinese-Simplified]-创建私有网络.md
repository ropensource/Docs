

如果你不方便连接到外部网络，也可以选择搭建自己的私有网络。

`PlatON`支持单节点模式和集群模式运行私有网络， 同样，搭建私有网络前请确保服务器本地具备以下条件：

1. 已经按照[PlatON安装指南](/zh-cn/Node/[Chinese-Simplified]-安装节点.md)安装好PlatON环境。
2. 已经按照[节点秘钥](/zh-cn/Node/_[Chinese-Simplified]-环境准备.md#节点秘钥)章节在'~/platon-node/data'目录下生成了节点私钥和节点BLS私钥。


## 单节点

**1.生成创世块配置文件`platon.json`**

在工作目录下创建创世区块文件，拷贝以下内容，修改 `your-node-pubkey` 为[环境准备](/zh-cn/Node/_[Chinese-Simplified]-环境准备.md#节点公私钥)生成的 ***节点公钥*** ，`your-node-blspubkey` 为[环境准备](/zh-cn/Node/_[Chinese-Simplified]-环境准备.md#节点BLS公私钥)生成的 ***节点bls公钥*** ，使本地节点成为共识节点参与共识。修改 `your-account-address` 为[环境准备](/zh-cn/Node/_[Chinese-Simplified]-环境准备.md#钱包文件)生成的 ***Address***。`platon.json`内容如下：

```
{
	"config": {
		"chainId": 299,
		"eip155Block": 1,
		"VMInterpreter": "evm",
		"cbft": {
			"initialNodes": [{
				"node": "enode://your-node-pubkey@127.0.0.1:16789",
				"blsPubKey": "your-node-blspubkey"
			}],
			"amount": 10,
			"period": 20000,
			"validatorMode": "ppos"
		},
		"genesisVersion": 2048
	},
	"economicModel": {
		"common": {
			"maxEpochMinutes": 360,
			"maxConsensusVals": 25,
			"additionalCycleTime": 525960
		},
		"staking": {
			"stakeThreshold": 1000000000000000000000000,
			"operatingThreshold": 10000000000000000000,
			"maxValidators": 101,
			"unStakeFreezeDuration": 28
		},
		"slashing": {
			"slashFractionDuplicateSign": 10,
			"duplicateSignReportReward": 50,
			"maxEvidenceAge": 27,
			"slashBlocksReward": 0
		},
		"gov": {
			"versionProposalVoteDurationSeconds": 1209600,
			"versionProposalSupportRate": 0.667,
			"textProposalVoteDurationSeconds": 1209600,
			"textProposalVoteRate": 0.5,
			"textProposalSupportRate": 0.667,
			"cancelProposalVoteRate": 0.50,
			"cancelProposalSupportRate": 0.667,
			"paramProposalVoteDurationSeconds": 1209600,
			"paramProposalVoteRate": 0.50,
			"paramProposalSupportRate": 0.667
		},
		"reward": {
			"newBlockRate": 50,
			"platonFoundationYear": 10
		},
		"innerAcc": {
			"platonFundAccount": "0xBE0af016941Acaf08Bf5f4ad185155Df6B7388ce",
			"platonFundBalance": 0,
			"cdfAccount": "0x08b2320Ef2482f0a5ad9411CCB1a748BcE7c2823",
			"cdfBalance": 331811981000000000000000000
		}
	},
	"nonce": "0x0376e56dffd12ab53bb149bda4e0cbce2b6aabe4cccc0df0b5a39e12977a2fcd23",
	"timestamp": "0x5bc94a8a",
	"extraData": "0xd782070186706c61746f6e86676f312e3131856c696e757800000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
	"gasLimit": "4712388",
	"alloc": {
		"1000000000000000000000000000000000000003": {
			"balance": "200000000000000000000000000"
		},
		"your-account-address": {
		  "balance": "999000000000000000000"
		}
	},
	"number": "0x0",
	"gasUsed": "0x0",
	"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```


**2.根据创世配置文件初始化创世信息**

```
./platon --datadir ./data init platon.json
```

出现以下提示说明初始化创世信息完成。

```
Successfully wrote genesis state
```

**3.启动节点**

```
./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data/nodekey --cbft.blskey ./data/blskey
```
此时在标准输出中出现 blockNumber 增长的日志记录，**表示共识成功，链成功启动，并成功出块**。


>[!NOTE|style:flat|label:注意]
>
>一定要指定<font color=red>--datadir</font>否则节点将默认初始化主网并连接到主网络。

至此我们就搭建好了一个拥有单节点的 PlatON 私有网络。网络名称为 `platon`，您可以在你的私有 `PlatON` 网络中像在公网一样的单节点中执行任何操作。

**4.后台运行**
一般情况下，platon 进程一直在前台进行，这样我们就不能进行其他操作了，并且如果中途退出该终端，程序将退出。
Ubuntu下可以以nohup方式启动程序：

```
nohup ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data/nodekey --cbft.blskey ./data/blskey &
```

当shell中提示nohup成功后再按下一次回车，确保不会因为误关闭终端引起进程退出。

**Windows不支持后台运行。**

## PlatON 集群环境

`PlatON集群`是有多节点参与的网络环境，这里我们假设你已经可以构建PlatON单节点。并且，我们将在一台服务器构建两个节点组成的网络。更多的节点在操作流程上类似。

为了在本地运行PlatON多节点，你要确保：

- 每个节点实例拥有单独的data目录（--datadir）

- 每个实例运行在不同的端口上，不管是p2p端口还是rpc端口（--port and --rpcport ）

- 节点可以彼此互连

- RPC服务器端口不被占用


**1.创建目录**

在platon-node目录下创建目录data0和data1，作为两个节点的数据目录。分别生成两个节点的coinbase账户。

```
mkdir data0 data1
```


**2.生成秘钥对**

按照[节点秘钥](/zh-cn/Node/_[Chinese-Simplified]-环境准备.md#节点秘钥)章节生成了2对节点私钥和节点BLS私钥。

分别将2个节点的nodekey和nodeblskey保存到'data0'和'data1'

```
echo {node0-nodekey} > ./data0/nodekey 
echo {node0-nodeblskey} > ./data0/nodeblskey 
echo {node1-nodekey} > ./data1/nodekey 
echo {node1-nodeblskey} > ./data1/nodeblskey 
```

**3.编辑创世文件**

修改创世块配置文件`platon.json`。

将两个节点的节点信息加入 **initialNodes** 数组中，因为我们生成的是两个节点组成的集群环境，所以数组长度为2。
需要修改`platon.json`文件：
请将以下文件内容'node0-nodekey'、'node1-nodekey'、'node0-blspubkey'和'node1-blspubkey'分别替换为上一步生成的节点公钥和节点bls公钥。
'your-account-address'替换为[钱包文件](/zh-cn/Node/_[Chinese-Simplified]-环境准备.md#钱包文件)章节生成的钱包地址。


```
……
  "cbft": {
  "initialNodes": [{
		"node": "enode://node0-pubkey@127.0.0.1:16789",
		"blsPubKey": "node0-blspubkey"
	},{
		"node": "enode://node1-pubkey@127.0.0.1:16790",
		"blsPubKey": "node1-blspubkey"
	}],
	……
  "alloc": {
    "your-account-address": {
      	"balance": "999000000000000000000"
    },
    "1000000000000000000000000000000000000003": {
		"balance": "200000000000000000000000000"
	}
  },
……
```

**4.初始化和启动**

分别为节点0和节点1初始化创世块信息：
```
./platon --datadir ./data0 init platon.json && ./platon --datadir ./data1 init platon.json
```

初始化成功后，分别用nohup方式启动节点0和节点1：

```
nohup ./platon --identity "platon" --datadir ./data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data0/nodekey --cbft.blskey ./data0/nodeblskey &

nohup ./platon --identity "platon" --datadir ./data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data1/nodekey --cbft.blskey ./data1/nodeblskey &

```


**5.检查**

通过前面所述的方式进入任意一个节点platon控制台，查看节点是否和对端建立连接以及通过查看blockNumber是否在持续增长来判断集群是否已成功启动。
