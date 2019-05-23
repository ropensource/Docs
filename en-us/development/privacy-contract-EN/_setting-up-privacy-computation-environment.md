[TOC]
# Setting Up Privacy Computing Development Environment On Linux

> ***This example demonstrated here requires running on Ubuntu 16.04.***

## Environmental requirements

### Hardware requirements

- CPU architecture

At present, the official only supports the `Intel`2 generation and above `CPU` architecture. Before setting up the environment, it is necessary to check whether the machine meets the minimum requirements.

```bash
$ cat /proc/cpuinfo
```

For example, the following output :

```bash
Model name : Intel(R) Xeon(R) CPU E5-2620 v2 @ 2.10GHz
```

Among them, `E5-2620 v2` indicates that the `CPU` is Intel's third-generation architecture, which meets the requirements.

> **CPU architecture must meet Intel v2 or above, otherwise it will not work properly**

### Software requirements

- Ubuntu16.04

The specified operating system is Ubuntu 16.04.

- JDK1.8+

Please refer to: [oracle official website](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), install and configure the environment variables `CLASSPATH`, `PATH`, `JAVA_HOME`.

- Maven3.3.9+

Please refer to: [maven official website](http://maven.apache.org/download.cgi) to complete the installation.

## Building a privacy computing network
A privacy computing network with two computing nodes is built, where the computing nodes are all `PlatON` node clients.

### Installing the PlatON node client

Install the `platon` node client with MPC functionality:

```bash
# add PPA
$ sudo add-apt-repository ppa:platonnetwork/platon
$ sudo apt-get update

# install platon with mpc
$ sudo apt-get -f install platon-all
```
After the installation, the executable files such as  `platon`, `ethkey` will be installed to `/usr/bin`.

### Creating two wallet for data nodes

The privacy calculation consists of two computing participants, each of them creates a wallet file as follows:

```bash
$ mkdir -p data
$ ./platon --datadir ./data account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {0x9a568e649c3a9d43b7f565ff2c835a24934ba447}Copy to clipboardErrorCopied
$ ./platon --datadir ./data account new
Your new account is locked with a password. Please give a password. Do not forget this password.
Passphrase:
Repeat passphrase:
Address: {0xce3a4aa58432065c4c5fae85106aee4aef77a115}Copy to clipboardErrorCopied
```

The above commands perform two operations to create wallets,  and we got two wallet addresses as follows:

> `0x9a568e649c3a9d43b7f565ff2c835a24934ba447`

> `0xce3a4aa58432065c4c5fae85106aee4aef77a115`

### Building a private network

Refer to [PlatON Cluster Environment](zh-cn/basics/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#PlatON+%e9%9b%86%e7%be%a4%e7%8e%af%e5%a2%83) to build a private network and connect compute nodes to the private network.

#### Method 1: Connecting to the testnet network

Refer to the [Test Network Connection Guide](zh-cn/basics/[Chinese-Simplified]-Connection Test Network) to connect the compute nodes to the test network.

#### Method 2: Connecting to the private network

Refer to the [PlatON Cluster Environment](zh-cn/basics/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#PlatON+%e9%9b% 86%e7%be%a4%e7%8e%af%e5%a2%83) document to build a private network and connect compute nodes to the private network.

### Turning on MPC function

Refer to [Enable MPC Calculation Function](zh-cn/basics/[Chinese-Simplified]-%e7%a7%81%e6%9c%89%e7%bd%91%e7%bb%9c#%e4%b8% Ba%e8%8a%82%e7%82%b9%e5%90%af%e7%94%a8MPC%e5%8a%9f%e8%83%bd), when restarting `platon`, will be `-mpc The .actor` corresponding configuration is the data node wallet address generated in the previous step: `0x9a568e649c3a9d43b7f565ff2c835a24934ba447`, `0xce3a4aa58432065c4c5fae85106aee4aef77a115`.

## Installing privacy contract development toolkit
### Downloading the installation package
Download the privacy contract development tool [installation package](https://download.platon.network/0.5/platon-ubuntu-amd64-mpc-toolkit.tar.gz):
```bash
wget https://download.platon.network/0.5/platon-ubuntu-amd64-mpc-toolkit.tar.gz
tar -xvzf platon-ubuntu-amd64-mpc-toolkit.tar.gz
```
After executing the above command, the privacy contract development tool is installed into the `platon-mpc-toolkit` working directory. The directory structure is as follows:

```bash
./
├── compiler
├── config.json.template
├── create_testnet_wallet.sh
├── ctool
├── ctool.config.json.template
├── install-deps.sh
├── mpc
│ ├── bin
│ ├── code
│ ├── config
│ ├── generated-code
│ ├── include
│ ├── sample
│ ├── script
│ └── user
├── pwasm
│ ├──boost
│ ├──build
│ ├── CMakeLists.txt
│ ├── CMakeModules
│ ├── COPYING
│ ├── external
│ ├── hello
│ ├── lib
│ ├── libc
│ ├── libc++
│ ├── platonlib
│ ├── pwas-origin
│ ├── rapidjson
│ ├── README.md
│ ├── script
│ ├── test
│ └── user
├── readme.txt
├── sample
│ ├── SimpleAddProto.cpp
│ ├── SimpleAddProto.proto
│ └── YaoMillionairesProblem.cpp
├── web3j -> mpc/bin/web3j/bin/web3j
```
Description:
- **compiler**
Privacy contract compilation script
- **ctool**
Privacy contract release tool
- **install-deps.sh**
Third-party dependent installation script
- **create_testnet_wallet.sh**
Create a wallet tool script
- **mpc**
Mpc related toolset
- **pwas**
`pWASM` related toolset
- **sample**
Privacy contract instance
- **config.json.template**
Configuration template for `compiler` script
- **ctool.config.json.template**
Configuration template for `ctool`

Switch to the `platon-mpc-toolkit` directory and install third-part dependencies:
```bash
bash install-deps.sh
```
At this point, the privacy computing development environment is installed. **Enjoy! **

### compiler instructions

**function：**

The `compiler` is a bash executable script to compile privacy contract, it generates binary privacy contract and some Java wrapped files.

**usage:**

```bash
bash ./compiler -c {your config file} -i {your privacy contract} -o {output directory}
```

**Parameter Description:**

* -c: privacy contract file
* -o: output directory
* -i: configuration file for compiler
* -p:`protobuf` definition file
