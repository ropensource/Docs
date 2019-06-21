
If you are unable to or don’t want to connect to the live public network, you may choose to establish your own private network.

Please be sure that you have already installed `platon` by following the [Installation Instructions](/en-us/basics/[English]-Installation-Instructions.md).

In this guide the default directory is `~/platon-node` on Ubuntu, and `D:\platon-node` on Windows. 

All the following instructions are performed under the default directory.

## Single node environment

1. Run the public and private key pair generation tool `ethkey` to generate a node ID and a node private key.

- Windows command line:

```
D:\platon-node> ethkey.exe genkeypair
Address : 0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey: 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127
```

- Ubuntu command line:

```
$ chmod u+x ethkey
$ ./ethkey genkeypair
Address : 0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey: 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127
```
PublicKey is the ***node ID***, and PrivateKey its corresponding ***node private key***.

2. Generate a node coinbase account. ((Coinbase account is the wallet address for miners, which means that the gas and reward for the current block should be directed towards the coinbase address specified by the miner. Since there is no miner for the genesis block, this address would simply be 0x0000000000000000000000000000000000000000.) For testing, you can pre-fund the account in the genesis block.

- Windows command line:

```
D:\platon-node> platon.exe --datadir .\data account new
WARN [12-08|18:15:35.628] Sanitizing cache to Go's GC limits provided=1024 updated=660
INFO [12-08|18:15:35.630] Maximum peer count ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {566c274db7ac6d38da2b075b4ae41f4a5c481d21}
```

- Ubuntu command line:

```
$ ./platon --datadir ./data account new
WARN [12-08|18:15:35.628] Sanitizing cache to Go's GC limits provided=1024 updated=660
INFO [12-08|18:15:35.630] Maximum peer count ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {566c274db7ac6d38da2b075b4ae41f4a5c481d21}
```
Please safely keep the generated **Address**.

3. Generate the genesis block configuration file `platon.json`

Create the genesis block file under the default directory. Copy the following data, and Change `your-node-pubkey` into the node ID you acquired in step 1, making the local node participate the consensus. Change `your-account-address` into the coinbase account generated in step 2. The contents of `platon.json` is as following:

```
{
    "config": {
    "chainId": 100,
    "homesteadBlock": 1,
    "eip150Block": 2,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 3,
    "eip158Block": 3,
    "byzantiumBlock": 4,
    "cbft": {
      "initialNodes": ["enode://your-node-pubkey@127.0.0.1:16789"]
    }
  },
  "nonce": "0x0",
  "timestamp": "0x5bc94a8a",
  "extraData": "0x00000000000000000000000000000000000000000000000000000000000000007a9ff113afc63a33d11de571a679f914983a085d1e08972dcb449a02319c1661b931b1962bce02dfc6583885512702952b57bba0e307d4ad66668c5fc48a45dfeed85a7e41f0bdee047063066eae02910000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x99947b760",
  "difficulty": "0x1",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
    "your-account-address": {
      "balance": "999000000000000000000"
    },
    "1000000000000000000000000000000000000000": {
      "balance": "0x52b7d2dcc80cd400000000"
    }
  },
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

4. Generate the node's private key file

Please note: the echo command line argument is the node private key and needs to be replaced with the node private key generated in step 1.

- Windows command line:

```
D:\platon-node> mkdir .\data\platon
D:\platon-node> echo 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36> .\data\platon\nodekey
D:\platon-node> type .\data\platon\nodekey
```

- Ubuntu command line:

```
$ mkdir -p ./data/platon
$ echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > ./data/platon/nodekey
$ cat ./data/platon/nodekey
```

5. Initialize the genesis state according to the genesis configuration file.

- Windows command line:

```
D:\platon-node> platon.exe --datadir .\data init platon.json
```

- Ubuntu command line:

```
$ ./platon --datadir ./data init platon.json
```
The following prompt indicates that a genesis node is successfully created:

```
Successfully wrote genesis state
```

6. Start the node

- Windows command line:

```
D:\platon-node> platon.exe --identity "platon" --datadir .\data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data\platon\nodekey"
```

- Ubuntu command line:

```
$ ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey"
```

***Option description:***

| Option | Description |
| :------------ | :------------ |
| --identity | Specify network name |
| --datadir | Specify the data directory path |
| --rpcaddr | Specify RPC server address |
| --rpcport | Specify RPC protocol communication port |
| --rpcapi | Specify name of the node's RPC API |
| --rpc | Specify HTTP-RPC communication method |
| --nodiscover | Do not enable node discovery |
| --nodekey | Specify node private key file save path |

At this time, log records containing the words "blockNumber" and "growth" appearing in the standard output indicates that **consensus was reached, the chain started successfully, and that blocks are being successfully created**.

We have now built a Platon private network containing a single node with the network name `platon` and a network `ID` of `100`.
You can perform any operation on it, just like on a single node in a public network.

7. Running in the background

So far we have let the `platon` process run in the foreground, which prevents us from performing other commands. Also, if we quit the terminal, program terminates.
Now start the program with nohup on Ubuntu:

```
$ nohup ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey" &
```

When the shell prompts that nohup is successful, hit enter again to ensure that the termination of the process due to accidentally closing the terminal window won’t occur.
Background running is not supported by Windows.


## PlatON cluster

A `PlatON Cluster` is a multi-node network environment. Here we assume you are already able to establish a single PlatON node. Now we will build a network consisting of two nodes. Networks consisting of more nodes follow a similar workflow.

In order to run multiple `platon` nodes locally, you must ensure that:

- Each node instance has a separate data directory (--datadir)

- Each instance runs on a different port, both `platon` and rpc (--port and --rpcport )

- Each node must know about the other

- The IPC port must be either restricted or unique

1. Create two data directories called data0 and data1 in platon-node directory and two new coinbase accounts for each of the two nodes.

- Windows command line:

```
D:\platon-node> mkdir data0
D:\platon-node> platon.exe --datadir .\data0 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {9a568e649c3a9d43b7f565ff2c835a24934ba447}

D:\platon-node> mkdir data1
D:\platon-node> platon.exe --datadir .\data1 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {ce3a4aa58432065c4c5fae85106aee4aef77a115}
```

- Ubuntu command line:

```
$ mkdir -p data0
$ ./platon --datadir ./data0 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {9a568e649c3a9d43b7f565ff2c835a24934ba447}

$ mkdir -p data1
$ ./platon --datadir ./data1 account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {ce3a4aa58432065c4c5fae85106aee4aef77a115}

```

2. Run`ethkey` to generate a node ID and a node private key for each of the two nodes.

- Windows command line:

```
D:\platon-node> ethkey.exe genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey :  8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127

D:\platon-node> ethkey.exe genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06
```

- Ubuntu command line:

```
$ chmod u+x ethkey
$ ./ethkey genkeypair
Address   :  0xA9051ACCa5d9a7592056D07659f3F607923173ad
PrivateKey:  1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36
PublicKey : 8917c748513c23db46d23f531cc083d2f6001b4cc2396eb8412d73a3e4450ffc5f5235757abf9873de469498d8cf45f5bb42c215da79d59940e17fcb22dfc127

$ ./ethkey genkeypair
Address   :  0x44b8d81F443DdEF731CE02831203afcd9F2027da
PrivateKey:  9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320
PublicKey :  1b22ffc514b806c752b3f145aa644173469e2b425b4847c9ce7c318451a1a249d061aa66058df501b90dc329369ecee475c7ba52b31b25edad44aac0d9847e06
```
PublicKey is the ***node ID***, and PrivateKey is the corresponding ***node private key***.

3. Modify the genesis configuration file `platon.json`.

Add the node information of the two nodes to the **initialNodes** array. Since we are generating a cluster consisting of two nodes, the array length will be two. 

Modify the platon.json file:

- Replace `node0-pubkey` with the ***node ID*** of node 0 generated in step 2.

- Replace `node1-pubkey` with the ***node ID*** of node 1 generated in step 2.

- Replace `node0-account-address` with the ***Address*** of node 0 generated in step 1.

- Replace `node1-account-address` with the ***Address*** of node 1 generated in step 1.

```
……
  "cbft": {
  "initialNodes": [
    "enode://node0-pubkey@127.0.0.1:16789",
    "enode://node1-pubkey@127.0.0.1:16790"]
  }
……
  "alloc": {
    "node0-account-address": {
      "balance": "999000000000000000000"
    },
    "node1-account-address": {
      "balance": "999000000000000000000"
    },
    "1000000000000000000000000000000000000000": {
      "balance": "0x52b7d2dcc80cd400000000"
    }
  },
……
```

4. Create a file called `nodekey` for each node and save the node's private key in it. Place the `nodekey` for node 0 under directory `platon-node/data0/platon`, and the one for node1 under directory `platon-node/data1/platon`. Note that the echo command line argument is the node private key and needs to be replaced with the ***node private key*** generated in step 2.

- Windows command line:

```
D:\platon-node> mkdir .\data0\platon
D:\platon-node> echo 1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36 > .\data0\platon\nodekey
D:\platon-node> type .\data0\platon\nodekey

D:\platon-node> mkdir .\data1\platon
D:\platon-node> echo 9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320 > .\data1\platon\nodekey
D:\platon-node> type .\data1\platon\nodekey
```

- Ubuntu command line:

```
$ mkdir -p ./data0/platon
$ echo "1abd1200759d4693f4510fbcf7d5caad743b11b5886dc229da6c0747061fca36" > ./data0/platon/nodekey
$ cat ./data0/platon/nodekey

$ mkdir -p ./data1/platon
$ echo "9baaf2b3e0dadae0e265de63c70421364fb4415022cd3885c4bbeec0539a5320" > ./data1/platon/nodekey
$ cat ./data1/platon/nodekey
```

5. Initialize the genesis information for node 0 and start the node.

- Windows command line:

```
D:\platon-node> platon.exe --datadir .\data0 init platon.json
D:\platon-node> platon.exe --identity "platon" --datadir .\data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data0\platon\nodekey"
```

- Ubuntu command line:

```
$ ./platon --datadir ./data0 init platon.json
$ ./platon --identity "platon" --datadir ./data0 --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data0/platon/nodekey"
```

6. Initialize the genesis information for node 1 and start the node.

- Windows command line:

```
D:\platon-node> platon.exe --datadir .\data1 init platon.json
D:\platon-node> platon.exe --identity "platon" --datadir .\data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey ".\data1\platon\nodekey"
```
Note that on Windows, all nodes except the first one must be started with additional -ipcdisable option.

- Ubuntu command line:

```
$ ./platon --datadir ./data1 init platon.json
$ ./platon --identity "platon" --datadir ./data1 --port 16790 --rpcaddr 0.0.0.0 --rpcport 6790 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data1/platon/nodekey"

```

7. The cluster is set up successfully when both nodes indicate that consensus was reached and that blocks are being inserted into the chain.

8. Nohup running

Just like a single node, to run the program with nohup on Linux in the background, the following commands can be used to start the nodes:
```bash
$ nohup ./platon ... --nodekey "./data0/platon/nodekey" >> node0.log 2>&1 &

$ nohup ./platon ... --nodekey "./data1/platon/nodekey" >> node1.log 2>&1 &
```
Similarly, hit enter again each time after nohup executes.


## Enabling MPC for a node

The MPC computation feature is a secure multi-party computation feature supported by the `PlatON` platform that provides the infrastructure for privacy computations. More about MPC please click [here](/en-us/development/[English]-PlatON-Privacy-Contract-Guide.md). Since MPC must be participated by two or more nodes, users can build a multi-node network by referring to the [PlatON Cluster](#platon-cluster) section. Note that the nodes involved in computing must all have the MPC function turned on. The initialization process is as follows:

**1. Check the `MPC` dependency library**

Before enabling MPC function, make sure that all dependency libraries have been installed. If not, please refer to [here](/en-us/basics/installation/%5BEnglish%5D-Ubuntu-MV-Installation-Instructions.md?id=dependency-package-installation) for installation.

**2. Add the following parameters at node startup**

```
--mpc --mpc.ice ./{mpc-ice-config-file} --mpc.actor 0xa7e6d8a00ba33ea732b2c924e1edc4e4b753e9ca
```
Parameters Description:

| Options     | Description                                                  |
|:-----------|:----------------------------------------------------------- |
| --mpc       | (Required) Turn on mpc calculation function                  |
| --mpc.ice   | (Optional) The mpc node initializes the ice configuration. Used to configure MPC communication port, default port is 8201 |
| --mpc.actor | (Optional) mpc specified wallet address, which is consistent with the privacy contract participant address (can be set via rpc interface eth_setActor(Address)) |

> **Note: If multiple computing nodes are started on same machine, need to use -moc.ice parameter to configure different communication ports.**

**3. Configuration file settings**

If the `--mpc.ice` option is enabled when the node is started, you need to configure the `{mpc-ice-config-file}` file in the node working directory to configure the `MPC communication port` . File configuration is as follows, If you need to modify the port, you only need to modify 8201 as the required port.

```
MpcNode.Server.Endpoints=default -p 8201
```

**4. Start the node**

```
$ ./platon --identity "platon" --datadir ./data --port 16789 --rpcaddr 0.0.0.0 --rpcport 6789 --rpcapi "db,eth,net,web3,admin,personal" --rpc --nodiscover --nodekey "./data/platon/nodekey" --mpc --mpc.ice ./{mpc-ice-config-file} --mpc.actor 0xa7e6d8a00ba33ea732b2c924e1edc4e4b753e9ca
```

**5. View the log**

When the node is started with the above parameters, the following information will be output into `./log/platon_mpc_xxx.log` (xxxx is the current node process number):

```
MPC ENGINE INIT SUCCESS
```

If the above information is shown, then mpc is initialized successfully.

## Enable VC function for a node

More about VC please click [here](/en-us/development/[English]-Verifiable-Contract).

Add the following parameters when starting the node:

```
--vc --vc.actor 0xd7398978d04565ccf44097106e1cb7e9148d8ec9 --wasmlog wasm.log
```

Options description:

| Options    | Description                                                  |
| :--------- | :----------------------------------------------------------- |
| --vc       | (Required) Enable VC calculation function                    |
| --vc.actor | (Required) is used to specify the account which will publish calculated result and the certificate on the chain |
| --wasmlog  | Specify this parameter to export the VC calculation log to `wasm.log`. If not specified, the log will be output to the node log |
