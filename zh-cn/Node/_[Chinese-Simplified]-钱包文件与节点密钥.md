
## 钱包文件

如果节点参与共识，首先需要有一个可以存收益的账户，我们可以用节点客户端文件生成一个钱包文件，以下以在'data'目录下的keystore文件夹下生成钱包为例：

- Windows

```
mkdir data
platon.exe --datadir .\data account new
```

在出现如下提示后输入2次口令对钱包文件进行加密

```
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {550ae58b051a8e942f858ef22019c1c622292f7e}
```

- Ubuntu

```
mkdir -p data
./platon --datadir ./data account new
```

在出现如下提示后输入2次口令对钱包文件进行加密

```
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {550ae58b051a8e942f858ef22019c1c622292f7e}
```

输出结果'Address'即为生成的账户地址。
> 注意：钱包文件和口令对于生成的该账户地址非常重要，丢失钱包文件或者忘记口令都将导致该账户内的令牌丢失，请对钱包文件做好备份并牢记口令。

说明：以上Windows操作和Ubuntu操作十分类似，Windows下操作除可执行文件多'.ext'外只有路径表达方式不同等细微差别，故后续描述仅以Ubuntu操作为例，对于Windows下的操作不再赘述。

## 节点密钥

### 节点公私钥

每个节点在网络中都有一个唯一的身份标识以便彼此区分，身份标识可以通过可执行文件'keytool'来产生：

```
./keytool genkeypair
```

输出结果:

```
Address   :  0x6877944bC950799C0511beECB7824A818C35920D
PrivateKey:  002925955b165bd33be1d97082df17cd269f10e6f5142f77e2605ed591d314bf
PublicKey :  064a22d0bbf537125f1beeab0efcf77b0a62680d44f5b66a2d12574b159601e662edbb6b57aea5eafabbff8ba5157ef613fe4b176cb8d97ea4951b6815748973
```

其中'PrivateKey'是节点的私钥，'PublicKey'是节点的公钥，公钥用于标识节点身份，可以被公开出去，私钥不能公开并且需要做好备份。

### 节点BLS公私钥

PlatON节点除了需要节点公私钥外还需要一种被称为BLS公私钥的密钥对，这个密钥对在共识协议中将被使用，密钥对通过以下命令获得：

```
./keytool genblskeypair
```

输出结果:

```
PrivateKey:  f22a785c80bd1095beff1f356811268eae6c94abf0b2b4e2d64918957b74783e
PublicKey :  4bf873a66df92ada50a8c6bacb132ffd63437bcde7fd338d2d8696170034a6332e404ac3abb50326ee517ec5f63caf12891ce794ed14f8528fa7c54bc0ded7c5291f708116bb8ee8adadf1e88588866325d764230f4a45929d267a9e8f264402
```

其中'PrivateKey'是节点的BLS私钥，'PublicKey'是节点的BLS公钥，BLS公钥用于共识协议中快速验证签名，可以被公开出去，BLS私钥不能公开并且需要做好备份。

为方便后续操作，在此将节点公私钥和节点BLS公私钥保存在节点所在目录下的'data'目录：

节点公私钥保存:
```
keytool genkeypair | tee >(grep "PrivateKey" | awk '{print $2}' > $node_dir/data/nodekey) >(grep "PublicKey" | awk '{print $3}' > $node_dir/data/nodeid)
```
节点BLS公私钥保存:
```
keytool genblskeypair | tee >(grep "PrivateKey" | awk '{print $2}' > $node_dir/data/blskey) >(grep "PublicKey" | awk '{print $3}' > $node_dir/data/blspub)
```


> [!NOTE|style:flat|label:注意]
>
>  -  $node_dir需改为节点所在目录
>  - 如果节点所在目录下不存在data目录，用户需手动创建



