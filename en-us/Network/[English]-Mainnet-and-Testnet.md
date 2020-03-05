## Introduction

PlatON has two public networks. One is the independent main net that has not yet been officially launched. The network ID is 100.

The other is Beleyworld, a test net of which the latest version has officially launched on 2020-2-20 Beijing time for developers. The network ID is 101.


## Ready

Before joining the PlatON public network, please ensure that the server has the following conditions:

1. You have installed the PlatON environment or compiled the PlatON executable files `platon` and `keytool` according to [PlatON Installation Guide](/en-us/Node/_[English]-Install-Node.md).

2. According to [NodeKey](/en-us/Node/_[English]-WalletFile-and-KeyPair.md#Node-key) chapter in the '~/platon-node/data' directory node private key and node BLS private key are generated.

This section assumes that the server is Ubuntu 18.04 and the working directory where the executable file is located in `~/platon-node`. Note that all subsequent command line operations are performed in the working directory.



## Mainnet

Coming soon ...

## Testnet

Anyone can join the testnet.

Execute the following command to start the PlatON node to join the testnet:

```bash
nohup ./platon --identity platon --datadir ./data --port 16789 --testnet --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodekey ./data/nodekey --cbft.blskey ./data/blskey --verbosity 3 --rpcaddr 127.0.0.1 --syncmode "full" > ./data/platon.log 2>&1 &
```

If you want to start the node in archive mode, execute the following command:

```bash
nohup ./platon --identity platon --datadir ./data --port 16789 --db.nogc --testnet --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodekey ./data/nodekey --cbft.blskey ./data/blskey --verbosity 3 --rpcaddr 127.0.0.1 --syncmode "full" > ./data/platon.log 2>&1 &
```

***prompt:***

| Options         | Description           |
|:------------ |:------------------------ |
| --identity | Specify the network name |
| --datadir | Specify the data directory path |
| --rpcaddr | Specify the rpc server address |
| --rpcport | Specify the rpc protocol communication port |
| --rpcapi | Specify the rpcapi name open by the node |
| --rpc | Specify http-rpc communication method |
| --nodekey | Specify the node private key file |
| --cbft.blskey | Specify the node bls private key file |
| --testnet | Specify the test net to connect to, running the main net is not set by default |

The meaning of more parameters can be viewed through the `platon --help` command.



If the node is successfully started, it will automatically establish a connection with the node nearest to it through the node discovery protocol. After the connection is successful, block synchronization will be started. We can also observe the number of node connections and whether the block number has increased.

### platon console

Enter the 'platon' console

```
./platon attach http://localhost:6789
```

### View peers

View all peers connected to the current node.

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

### View the current block number

View the block number of the current node.

```
> platon.blockNumber
2235
>
```


A series of test network nodes appear in the node list and the block number is increasing, which means that the connection is successful!


> [!NOTE|style:flat|label:Note]
>
> Testing <font color=red>Energon</font> has no value and is limited to experience testing network functions. If you just connect to the test network, you don't need to apply!


