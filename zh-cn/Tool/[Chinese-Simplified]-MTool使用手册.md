### 简介

为了便于节点进行质押，委托以及治理等相关的操作，PlatON提供了MTool来辅助用户。

- MTool可支持Ubuntu 18.04和Windows 10。
- MTool需要通过RPC接口连接到验证节点，验证节点的安装部署可参考[成为验证节点](zh-cn/Node/[Chinese-Simplified]-成为验证节点.md)。
- 为保证节点安全，建议节点RPC端口通过Nginx代理访问，Nginx使用Https和用户认证加强安全防护。
- MTool对质押等交易提供两种签名方式：在线签名和离线签名。

### 应用场景

- 如果用户只有一台在线机器直接发送交易，则可以参考[在线MTool使用手册](zh-cn/Tool/[Chinese-Simplified]-在线MTool使用手册.md)。
- 如果用户条件允许的话，出于安全考虑，可以在离线的机器上签名交易，由在线机器发送签名后的交易，则可参考[离线MTool使用手册](zh-cn/Tool/[Chinese-Simplified]-离线MTool使用手册.md)。

