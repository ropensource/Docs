
PlatON 是实行民主治理的区块链项目，验证节点由所有 Energon 持有者共同推选，以维护和发展 PlatON 网络。得票数最多的101名节点将成为备选节点，从中用 VRF 随机选出25个验证节点，参与管理整个 PlatON 网络。

本节描述如何操作成为验证节点。

##  操作步骤

须按顺序完成以下步骤

### 同步时间
验证节点的时间需要保持正确，因此需要和时间服务器实时同步，以Ubuntu系统为例，说明如何进行时间同步

#### 安装NTP(自动同步)

安装ntp，并且设置开机自启
```bash
sudo apt-get -y install ntp  &&  sudo systemctl enable ntp
```

#### 查看同步情况
输入以下命令查看同步情况
```bash
ntpq -p
```
返回

![ntpq返回](时钟同步.assets/ntpq.png)


属性说明：

- remote – 用于同步的远程节点或服务器

- refid – 远程的服务器进行同步的更高一级服务器

- st – 远程节点或服务器的 Stratum（级别，NTP 时间同步是分层的）

- t  类型 u: unicast（单播） 或 manycast（选播） 客户端, b: broadcast（广播） 或 multicast（多播） 客户端, l: 本地时钟, s: 对称节点（用于备份）, A: 选播服务器, B: 广播服务器, M: 多播服务器,

- when 距离上次请求时间（秒）

- poll   本机和远程服务器多长时间进行一次同步（秒）

- reach  用来测试能否和服务器连接，每成功连接一次它的值就会增加，

- offset  主机与远程节点或服务器时间源的时间偏移量，offset 越接近于0，主机和 NTP 服务器的时间越接近（毫秒）

- delay – 从本地到远程节点或服务器通信的往返时间（毫秒）


remote前面符号的说明:

（ + ）良好的且优先使用的远程节点或服务器（包含在组合算法中）
  ( * ）当前作为优先主同步对象的远程节点或服务器

###  安装节点
节点服务器须为Ubuntu 18.04，请按照[安装节点](zh-cn/Node/_[Chinese-Simplified]-安装节点.md)中Ubuntu的部分进行操作。

### 生成节点密钥与共识密钥
节点在启动时需要节点公私钥与BLS公私钥，BLS公私钥在共识协议中将被使用。
密钥生成方式参照[节点密钥](/zh-cn/Node/_[Chinese-Simplified]-钱包文件与节点密钥#节点密钥),将生成的密钥转移到节点所在目录。

> [!NOTE|style:flat|label:注意]
>
>  如果没有预先生成密钥，节点在启动时自动在节点的data目录下生成。

### 启动节点加入网络
根据用户自身需要，启动节点，选择加入公有网络，或者加入私有网络，操作见文档：
- [加入私有网络](zh-cn/Network/[Chinese-Simplified]-创建私有网络.md)
- [加入公有网络](zh-cn/Network/[Chinese-Simplified]-连接公有网络.md)


### 配置 nginx

安全考虑，不建议节点 rpc 端口对外直接开放（节点服务器默认Ubuntu 18.04）。可以考虑使用 Nginx 进行反向代理，并通过用户鉴权和 HTTPS 加强 Nginx 端口的安全性。如果用户在安装PlatON做了节点数据目录的修改，nginx_conf.sh脚本也需要修改成相同的节点数据目录。 Nginx 配置步骤如下：

**step1.** 下载 nginx_conf.sh

``` bash
wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/nginx_conf.sh
```

或者

``` bash
wget http://47.91.153.183/opensource/scripts/nginx_conf.sh
```

**step2.** 执行脚本

``` bash
chmod +x nginx_conf.sh && ./nginx_conf.sh
```

> [!NOTE|style:flat|label:注意]
>
> - 提示 `[sudo] password for` 时，输入当前账户密码。
> - 提示 `Enter your name:` 时，输入用户名，提示 `Enter your password:` 时，输入密码。务必牢记用户名和密码，后续MTool 配置验证节点信息时需要填写。
> - 提示 `ngnix conf succeed` 时，表示配置 nginx 成功，未配置成功时，请通过我们的客服联系方式反馈具体问题。


###  配置MTool并发起质押
为了便于节点进行质押，委托以及治理等相关的操作，PlatON提供了MTool来辅助用户。
MTool对质押等交易提供两种签名方式：在线签名和离线签名。根据用户需要选择对应的操作方式。

#### 在线MTool操作流程
  按照以下流程安装在线MTool并发起质押成为质押节点:
- [安装MTool](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册#安装在线MTool)
- [配置MTool](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册#配置在线MTool)
- [创建钱包](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册#创建钱包)
- [配置验证节点信息](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册#配置验证节点信息)
- [发起质押操作](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册#发起质押操作)

#### 离线MTool操作流程
按照以下流程安装离线MTool并发起质押成为质押节点:
- [安装MTool](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册#安装MTool)
- [配置MTool](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册#配置)
- [钱包配置](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册#钱包配置)
- [连接到验证节点](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册#连接到验证节点)
- [发起质押操作](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册#发起质押操作)

> [!NOTE|style:flat|label:注意]
>
> 离线交易的签名须离线完成，操作流程参考[离线MTool使用手册](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册#基本操作流程)。
> 利用节点工具 MTool 执行节点质押与委托等操作，对于有能力的开发者，可以在 Java SDK 和 Javascript SDK 基础上面开发自己的节点工具，SDK请参考文档。
> -  [Java-SDK](zh-cn/Development/[Chinese-Simplified]-Java-SDK.md)
> - [JavaScript-API](zh-cn/Development/[Chinese-Simplified]-JS-SDK.md)


## 验证节点确认

完成质押操作后，可在 PlatON 官方区块链浏览器 [PlatScan](https://platscan.test.platon.network) 上点击主页上方验证节点列表，查看所有验证节点，或者在输入框输入节点名称查询节点具体信息，查看是否成为验证节点。











