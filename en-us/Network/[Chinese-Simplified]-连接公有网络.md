## 简介

PlatON目前有2个公有网络，一个是还未正式上线的、独立运行的PlatON主网络，网络ID为100。

另外一个是已经于北京时间2020-2-20日正式上线的用来对开发者开放的测试网络贝莱世界，网络ID为101。

## 准备

在加入PlatON公有网络前请确保服务器本地具备以下条件：

1. 已经按照[PlatON安装指南](/zh-cn/Node/_[Chinese-Simplified]-安装节点.md)安装好PlatON环境或编译好PlatON可执行文件'platon'和'keytool'。
2. 已经按照[节点密钥](/zh-cn/Node/_[Chinese-Simplified]-钱包文件与节点密钥.md#节点密钥)章节在'~/platon-node/data'目录下生成了节点私钥和节点BLS私钥。


本章节假设服务器为Ubuntu18.04，可执行文件所在工作目录为 `~/platon-node`，注意后续所有命令行操作均在工作目录下进行。

## 加入主网

敬请期待...

## 加入贝莱世界

任何人、任何组织都可以加入PlatON测试网络贝莱世界。

执行以下命令即可启动验证节点加入贝莱世界：

```bash
./platon --identity platon --datadir ./data --port 16789 --testnet --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodekey ./data/nodekey --cbft.blskey ./data/blskey --verbosity 3 --rpcaddr 127.0.0.1 --syncmode "full"
```
如果要启动归档节点加入贝莱世界，请执行以下命令：
```bash
./platon --identity platon --datadir ./data --port 16789 --db.nogc --testnet --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodekey ./data/nodekey --cbft.blskey ./data/blskey --verbosity 3 --rpcaddr 127.0.0.1 --syncmode "full"
```

***提示：***

| 选项         | 描述                     |
|:------------ |:------------------------ |
| --identity   | 指定网络名称             |
| --datadir    | 指定data目录路径         |
| --rpcaddr    | 指定rpc服务器地址        |
| --rpcport    | 指定rpc协议通信端口      |
| --rpcapi     | 指定节点开放的rpcapi名称 |
| --rpc        | 指定http-rpc通讯方式     |
| --nodekey    | 指定节点私钥文件         |
| --cbft.blskey| 指定节点bls私钥文件      |
| --testnet    | 指定连接到测试网络，不指定默认运行主网络 |

更多参数意义通过`platon --help`命令查看。

当platon启动成功后，正常情况下会通过节点发现协议自动和距离自己最近的节点建立连接，连接成功后会启动区块同步，所以判断加入网络是否成功可以通过查看节点的peers同时确认当前节点块高是否增长来判断。

### 进入`platon`控制台

通过`http`方式进入`platon`控制台

```
./platon attach http://localhost:6789
```

### 查看节点的peers

在`platon`控制台中通过执行以下命令查看当前节点连接的所有peers。


```
> admin.peers
[{
    caps: ["cbft/1", "platon/62", "platon/63"],
    id: "0dd4e447cf23f4bfc94b1568bae626bf4894ce2e9d5ca474e3cc73ec7e9d4de550fffc1e2fc64cca25d42aecf6169cf8f8c0f4fe6adb847c33dc6ceb6f001bd1",
    name: "PlatONnetwork/platon/v0.8.0-unstable-c5fc6b19/linux-amd64/go1.11.11",
    network: {
      consensus: true,
      inbound: true,
      localAddress: "127.0.0.1:16789",
      remoteAddress: "127.0.0.1:47706",
      static: false,
      trusted: false
    },
    protocols: {
      cbft: {
        commitBn: 0,
        highestQCBn: 0,
        lockedBn: 0,
        protocolVersion: 1
      },
      platon: {
        head: "0x88a4fe315ce13b3010abf4ab5d120f25a21ac2ccae8ec563ad259e47e24b24bc",
        number: 0,
        version: 63
      }
    }
},
...
]
```

### 查看当前块高

通过在`platon`控制台中执行以下命令查看当前节点的块高。

```
> platon.blockNumber
2235
>
```

节点列表中出现一系列测试网络节点并且块高在不断增长，则表示连接成功！


>[!NOTE|style:flat|label:注意]
>
>测试<font color=red>Energon</font>没有任何价值，仅限于体验测试网络功能。如仅仅只是连接测试网络，无需申请！


