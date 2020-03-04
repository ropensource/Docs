
## Introduction

After going online, if for some reason, the chain forks. At this point, all verification nodes need to roll back the node data to the highest block that has reached a consensus state. If the verification node has a backup node, the data rollback operation must be performed in the same way as the verification node.

## Node data backup
It is recommended that the data on the chain be backed up in a timely manner. The backup operation is provided as follows:

- Download data backup script

If the download script fails, set the DNS server to 8.8.8.8.

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/node_back.sh
  ```

  or

  ```bash
  wget http://47.91.153.183/opensource/scripts/node_back.sh
  ```

  **Note: If the user changes the node data directory when installing PlatON, the node_back.sh script also needs to be modified to the same node data directory.**

- Execute data backup script

  ```bash
  chmod u+x node_back.sh && ./node_back.sh
  ```


## Node data rollback

### Back up the current block data
Follow the steps above to back up the node data.

### Deploy new version of platon

Deploy and install according to the upgrade version number (assuming 0.10.0) obtained from the community announcement. Proceed as follows:

- Download script
If the download script fails, set the DNS server to 8.8.8.8.

  ```bash
  wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/scripts/update_platon.sh
  ```

  or

  ```bash
  wget http://47.91.153.183/opensource/scripts/update_platon.sh
  ```

  **Note: If the user changes the node data directory when installing PlatON, the update_platon.sh script also needs to be modified to the same node data directory.**

- Execute the command to update the version

  ```bash
  chmod u+x update_platon.sh && ./update_platon.sh x.x.x nostart 
  ```

  Note: xxx is the version number of the designated upgrade. For the actual version number, please refer to the relevant announcement issued by the community. **nostart** means that the node will not start (the default start). The password of the current user; when the Do you want to continue? Prompt appears, enter: **y**; the execution result is as follows: the version upgrade was successful:

  ```
  Currently installed version：0.9.0==========
  Begin to install platon version：0.10.0==========
  Do you want to continue? [Y/n] y
  Uninstall current platon version：platon0.9.0 successfully==========
  Install platon version：platon0.10.0 successfully==========
  ```
  
  > [!NOTE|style:flat|label:Note]
  >
  >Cases where no version upgrade will be performed:
  >
  > - The specified version does not exist
  > - Specifies that the version to be upgraded is not higher than the installed version
  >
  >Nodes without a version upgrade run using the previous version.

### Roll back backup data

When the data needs to be rolled back, the community will post the relevant off-chain data rollback announcement in the **Off-chain data rollback upgrade announcement** link in the announcement area. The announcement will notify the user to roll back to the specified block height and backup data address. Taking the rollback block height **10000** as an example, the announcement template is as follows:

```bash
The block height for data rollback this time is：【10000】
The block hash of the data rollback is：0xf0be4fe085ad98f355a9797b7d2a3927cc53f2e8354567f6142ab8b954572b3c

1、Please refer to the data rollback scheme：【node_data_backup_and_rollback.md】
2、Download address of the data that needs to be rolled back：【...】
3、The code branch for this off-chain upgrade is：
【code branch：https://github.com/PlatONnetwork/PlatON-Go/tree/pip_v0.7.2】
【commit ID: 5696a3b458099a58f4308949a977f5f1ec9922d5】
4.Target version number：0.10.0
```

If you see this announcement, you can choose the following two methods for data rollback operation.

####  **Roll back data backed up by backup node**

If the node backs up the data itself, the format of the backup file is: data_backup_YYYY_MM_DD_num.tar.gz, where num is the block height at the time of backup. From the files backed up by you, select the file whose block height is not higher than 10000 and which is closest to 10000 for rollback.

* **Filter backup files**

Execute the command on the **backup node**:

```bash
cd ~/platon-node/data && ls -t | awk -F '[_.]' -v t=10000 'BEGIN {min=65535} {d=t-$6; if(d>=0 && min>d){min=d;minfile=$0;}} END {print minfile}'
```
> [!NOTE|style:flat|label:Note]
>
> `~/platon-node/data` is the default node data directory, ** 10000 ** is the height of the rolled-back block, which is based on the actual block height in the announcement, reference: [Rollback Backup Data](#Roll back backup data). This command returns the qualified backup file name, such as:
>
> data_backup_2019_11_05_9900.tar.gz。

* **Copy backup file**

Copy the filtered backup file from the **backup node** to the `~/platon-node/data` directory of the **verification node**.

* **Rollback data**

Execute the command on the **verification node**:
```bash
cd ~/platon-node/data && tar -xzvf data_backup_2019_11_05_9900.tar.gz
```
> [!NOTE|style:flat|label:Note]
>
> ~/platon-node is the default node data directory. data_backup_2019_11_05_9900.tar.gz needs to be modified to the backup file name returned by the **Filter backup files** command.

#### Roll back data downloaded by the community

Get the download path of the backup file from the community bulletin, refer to: [Rollback Backup Data](#Roll back backup data) The download address in the release bulletin, such as：https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/backup/data_backup_2019_11_05_10000.tar.gz.

* **Get backup file**

```bash
cd ~/platon-node/data && wget https://7w6qnuo9se.s3.eu-central-1.amazonaws.com/opensource/backup/data_backup_2019_11_05_10000.tar.gz
```
> [!NOTE|style:flat|label:Note]
>
> `~/platon-node` is the default node data directory. The download address needs to be changed to the actual backup file download address in the announcement.

* **Rollback data**

```bash
tar -xzvf data_backup_2019_11_05_10000.tar.gz
```

> [!NOTE|style:flat|label:Note]
>
> data_backup_2019_11_05_10000.tar.gz needs to be modified to the actual backup file name downloaded.

### Restart node

```shell
cd ~/platon-node && nohup platon --identity "platon"  --datadir ./data --port 16789 --rpc --rpcaddr 127.0.0.1 --maxpeers 25 --rpcport 6789 --rpcapi "platon,debug,personal,admin,net,web3" --nodekey "./data/nodekey" --cbft.blskey ./data/blskey &
```

### Verify rollback

Excuting an command:

```bash
cd ~/platon-node/data && platon attach ipc:platon.ipc -exec platon.blockNumber
```

Execute the above command multiple times and observe the block height value returned by the execution command. It will go through the following three stages:

>- The block height grows to the specified rollback block height, and then stops producing blocks.
>- Any verification node collects 2/3 of the number of validators, that is, 76 or more version declaration transactions, and then continues to produce blocks.
>- Synchronize blocks, and block heights begin to grow.

Note: If the rollback situation is as described above, the data rollback is successful; otherwise, the data rollback fails. During the second phase of collecting verifier version statement transactions, any transaction sent by the node will not be chained and will be temporarily placed in the transaction pool. After the version statement transactions collected by the verification node reach 76 transactions, the transaction pool will be in the transaction pool. The transactions will be packaged one after another. You can refer to the document to issue a version statement transaction:[Online-MTool-user-manual.md](zh-cn/Tool/[English]-Online-MTool-user-manual.md)**Version Statement Operation Chapter**；If you use the offline signature transaction method, you can refer to the document:[Offline-MTool-user-manual.md](zh-cn/Tool/[English]-Offline-MTool-user-manual.md)。

