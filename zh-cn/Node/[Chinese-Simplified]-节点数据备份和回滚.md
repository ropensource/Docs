
## 简介

当上线后，如果因为某些原因，链产生分叉。此时所有验证节点需要将节点数据回滚到达成共识状态的最高区块。如果验证节点有备份节点，也必须和验证节点一样进行数据回滚操作。

## 节点数据备份
链上数据建议及时备份，提供备份操作如下:

- 下载数据备份脚本

如果下载脚本失败，请设置DNS 服务器为8.8.8.8。

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/node_back.sh
  ```

  或者

  ```bash
  wget http://47.91.153.183/opensource/scripts/node_back.sh
  ```

  **注意：如果用户在安装PlatON做了节点数据目录的修改，node_back.sh脚本也需要修改成相同的节点数据目录。**

- 执行数据备份脚本

  ```bash
  chmod u+x node_back.sh && ./node_back.sh
  ```


## 节点数据回滚

### 备份当前区块数据
按照上文步骤备份节点数据。

### 部署新版本platon

根据从社区公告获得的升级版本号（假设为 0.10.0），进行部署安装。步骤如下：

- 下载脚本
如果下载脚本失败，请设置DNS 服务器为8.8.8.8。

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/update_platon.sh
  ```

  或者

  ```bash
  wget http://47.91.153.183/opensource/scripts/update_platon.sh
  ```

  **注意：如果用户在安装PlatON做了节点数据目录的修改，update_platon.sh脚本也需要修改成相同的节点数据目录。**

- 执行命令，更新版本

  ```bash
  chmod u+x update_platon.sh && ./update_platon.sh x.x.x nostart 
  ```

  注： x.x.x为指定升级的版本号，实际版本号可参考社区会发布相关的公告，**nostart**表示节点不启动（默认启动），当出现[sudo] password for platon类似提示时，需要输入当前用户的密码；当出现Do you want to continue?提示时，输入：**y**；执行结果如下表示版本升级成功：

  ```
  当前已安装版本：0.9.0==========
  开始安装：0.10.0版本==========
  Do you want to continue? [Y/n] y
  卸载当前版本：platon0.9.0成功==========
  安装版本：platon0.10.0成功==========
  ```
  
  > [!NOTE|style:flat|label:注意]
  >
  >不进行版本升级的情况：
  >
  > - 指定升级的版本不存在
  > - 指定升级的版本不高于已安装的版本
  >
  >未进行版本升级的节点使用以前的版本运行。

### 回滚备份数据

当需要回滚数据时，社区会在公告区的**链下数据回滚升级公告**链接中发布相关的链下数据回滚公告。公告会通知用户回滚到指定的区块高度以及备份数据地址。以回滚区块高度**10000**为例，公告模板如下：

```bash
本次数据回滚的区块高度为：【10000】
本次数据回滚的区块HASH为：0xf0be4fe085ad98f355a9797b7d2a3927cc53f2e8354567f6142ab8b954572b3c

1、数据回滚方案请参考：【链下数据回滚升级指南.md】
2、需要回滚的数据下载地址：【...】
3、本次链下升级的代码分支为：
【代码分支：https://github.com/PlatONnetwork/PlatON-Go/tree/pip_v0.7.2】
【commit ID: 5696a3b458099a58f4308949a977f5f1ec9922d5】
4.目标版本号：0.10.0
```

如果看到这份公告，可以选择以下两种方式进行数据回滚操作。

####  **回滚备份节点备份的数据**

如果节点自己备份了数据，备份文件的格式为：data_backup_YYYY_MM_DD_num.tar.gz，其中num为备份时的区块高度。从自己备份的文件中选择区块高度不高于10000且最接近于10000的文件用于回滚。

* **筛选备份文件**

在**备份节点**上执行命令：

```bash
cd ~/platon-node/data && ls -t | awk -F '[_.]' -v t=10000 'BEGIN {min=65535} {d=t-$6; if(d>=0 && min>d){min=d;minfile=$0;}} END {print minfile}'
```
> [!NOTE|style:flat|label:注意]
>
>  `~/platon-node`为默认节点数据目录，**t=10000**中的10000为回滚的区块高度，以公告中的实际区块高度为准，参考：[回滚备份数据](#回滚备份数据)。此命令返回符合条件的备份文件名，如：data_backup_2019_11_05_9900.tar.gz。

* **拷贝备份文件**

将筛选出来的备份文件从**备份节点**拷贝到**验证节点**的`~/platon-node/data`目录下。

* **回滚数据**

在**验证节点**上执行命令：
```bash
cd ~/platon-node/data && tar -xzvf data_backup_2019_11_05_9900.tar.gz
```
> [!NOTE|style:flat|label:注意]
>
> ~/platon-node为默认节点数据目录，data_backup_2019_11_05_9900.tar.gz需修改为**筛选备份文件**命令返回的备份文件名。

#### 回滚社区下载的数据

从社区公告中获取备份文件的下载路径，参考：[回滚备份数据](#回滚备份数据) 发布公告中的下载地址，如：https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/backup/data_backup_2019_11_05_10000.tar.gz。

* **获取备份文件**

```bash
cd ~/platon-node/data && wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/backup/data_backup_2019_11_05_10000.tar.gz
```
> [!NOTE|style:flat|label:注意]
>
> `~/platon-node`为默认节点数据目录，下载地址需修改为公告中实际的备份文件下载地址。

* **回滚数据**

```bash
tar -xzvf data_backup_2019_11_05_10000.tar.gz
```

> [!NOTE|style:flat|label:注意]
>
> data_backup_2019_11_05_10000.tar.gz需修改为实际下载的备份文件名。

### 重启节点

```shell
cd ~/platon-node && nohup platon --identity "platon"  --datadir ./data --port 16789 --rpc --rpcaddr 127.0.0.1 --maxpeers 25 --rpcport 6789 --rpcapi "platon,debug,personal,admin,net,web3" --nodekey "./data/nodekey" --cbft.blskey ./data/blskey &
```

### 验证回滚情况

执行命令：

```bash
cd ~/platon-node/data && platon attach ipc:platon.ipc -exec platon.blockNumber
```

执行上面的命令多次，观察执行命令返回的区块高度值，会经历如下三个阶段：

>- 区块高度一直增长到指定回滚区块高度，然后停止出块。
>- 任意一个验证节点收集到验证人数量2/3，即76笔以上的版本声明交易后，继续出块。
>- 同步区块，区块高度开始增长。

说明：如果回滚情况如上描述，则表示数据回滚成功；否则表示数据回滚失败。在第二阶段收集验证人的版本声明交易过程中，节点发送的任何交易都不会上链，都会暂时放在交易池中，等到有验证节点收集的版本声明交易达到76笔后，交易池中的交易会被陆续打包，出块。发版本声明交易可以参考文档：[在线MTool使用手册.md](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册.md)的**版本声明操作章节**；如果是使用离线签名交易方式，可参考文档：[离线MTool使用手册.md](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册.md)。

