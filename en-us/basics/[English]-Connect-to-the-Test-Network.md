**Connect to the Test Network**

This document will explain how to connect local **PlatON** nodes to the test network.

Baleyworld test network is now available. Follow the instructions below to connect the local nodes to the test network.

Before connect please be sure that you have already installed **platon** by following the Installation Instructions.

In this guide the default directory is **~/platon-node** on Ubuntu, and **D:\platon-node** on Windows. All the following instructions are performed under the default directory.

**Create an Account**

You can create an account using **PlatON** commands:

- Windows command line:

```
D:\platon-node> mkdir data
D:\platon-node> platon.exe --datadir .\data account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {550ae58b051a8e942f858ef22019c1c622292f7e}
```

- Linux command line:

```
$ mkdir -p data
$ ./platon --datadir ./data account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {550ae58b051a8e942f858ef22019c1c622292f7e}
```

The output is the generated account address.

**Apply for Test Energon**

Use the account address generated in the previous step to apply for test Energon on the PlatON official website.

Please note: Test Energons are of no value and only used for testing network functions. If you are only trying to connect to the test network, there&#39;s no need to apply for them!

**Start the Local Nodes and Connect to the Test Network**

- Windows command line:

```
D:\platon-node> platon.exe --identity platon --datadir .\data --port 16789 --testnet --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --debug --verbosity 3 --rpcaddr 0.0.0.0  --syncmode "full" --gcmode "archive"
```

- Linux command line:

```
$ ./platon --identity platon --datadir ./data --port 16789 --testnet --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --debug --verbosity 3 --rpcaddr 0.0.0.0  --syncmode "full" --gcmode "archive"
```

Options description:

| **Option** | **Description** |
| --- | --- |
| -identity | Specify network name |
| -datadir | Specify the data directory |
| -rpcaddr | Specify RPC server address |
| -rpcport | Specify RPC protocol communication port |
| -rpcapi | Specify name of the node&#39;s RPC API |
| -rpc | Specify HTTP-RPC communication method |
| -testnet | Connect to the test network |

Type **platon -help** for more information on options.

**Find out Whether Successfully Connected**

1. Enter **platon** control panel using **http**
 - Windows command line:

```
 D:\platon-node> platon.exe attach http://localhost:6789
```

 - Linux command line:

```
 $ ./platon attach http://localhost:6789
``` 

2. See whether the node list added the test network nodes

```
> admin.peers
[{
    caps: ["eth/62", "eth/63"],
    id: "23aa343260d06e04107d1cd9a7d12c54cc238719a1523ffe42640210c913218b5940d41511c5adb716da38844a85cdab8b7db0600d242e24168d7df10aebd324",
    name: "PlatONnetwork/V0.6_testsn/v0.6.0-stable-0f651de0/linux-amd64/go1.11",
    network: {
      consensus: false,
      inbound: false,
      localAddress: "192.168.18.181:51828",
      remoteAddress: "54.252.202.130:16789",
      static: false,
      trusted: false
    },
    protocols: {
      eth: {
        head: "0x104fe03d2b2f0b783e808ea7fcd52566d7cde9f36c4a06e950795e0459db5551",
        number: 74822,
        version: 63
      }
    }
},
...

]
``` 
If the node list contains a series of test network nodes, the connection is successful!

