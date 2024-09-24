## Step - 0 : Remove an existing version of geth
1. Remove the current Geth package : ```sudo apt remove --purge -y ethereum ```
2. Clean up the residual files :
```
sudo apt autoremove -y
sudo apt clean
```
3. Verify Geth is uninstalled: ``` geth version ```
4. Locate any remaining Geth binaries: ``` which geth ```
5. Manually remove the Geth binary : ``` sudo rm -rf /usr/local/bin/geth ```
6. Verify that Geth is no longer present: ``` geth version ```
   
## Step - 1 : Commands for installing Geth on Ubuntu
1. Navigate to the directory where you want to download Geth: ``` cd /usr/local/bin ```
2. Check the Architecture : ``` dpkg --print-architecture ```
3. Download the specific version of [Geth 1.12](https://geth.ethereum.org/downloads) :
``` sudo wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.12.0-e501b3b0.tar.gz ```
5. Extract the downloaded tar file: ``` sudo tar -xvf geth-linux-amd64-1.12.0-e501b3b0.tar.gz ```
6. Move the extracted binary to a directory in your PATH: ``` sudo mv geth-linux-amd64-1.12.0-e501b3b0/geth /usr/local/bin/geth ```
7. Remove the downloaded and extracted files: ``` sudo rm -rf geth-linux-amd64-1.12.0-e501b3b0.tar.gz geth-linux-amd64-1.12.0-e501b3b0 ```
8. Verify the installation :  ``` geth version ```
   
==============Old Steps for Installing Geth===================
```
sudo apt-get update
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt update
sudo apt-get install ethereum
sudo apt-get upgrade geth
```
## Step - 2 : Check the version of Geth on the Terminal
```
geth version
```
![Geth_version](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_version.png)

## Step - 3 : Create a Private Ethereum Network
**1. Create a folder named, private_ethereum_setup :**
```
mkdir private_ethereum_setup
```
**2. Create 2 subfolders named node1 and node2 in the folder **private_ethereum_setup****
```
cd private_ethereum_setup   
mkdir node1 node2
```  
![Folder_structure](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/folder_0.png)
  
**3. Create 2 accounts in the folder corresponding to node1 and node2**
```
geth --datadir "node1" account new
```
![Node_1](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_1_account.png)
```
geth --datadir "node2" account new
```
![Node_2](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_2_account.png)
Note : 
- This command will prompt you to enter a passphrase and generates Public and Private Keys.
- For future reference, save the keypair in the file [**network_keypair.txt**](https://github.com/LifnaJos/private_ethereum_setup/blob/main/private_ethereum_setup/network_keypair)
- For each node, save the password in a file, **password.txt** : 
[Node_1](https://github.com/LifnaJos/private_ethereum_setup/blob/main/private_ethereum_setup/node1/password)  and   [Node_2](https://github.com/LifnaJos/private_ethereum_setup/blob/main/private_ethereum_setup/node2/password)

**4. Create a **genesis.json** file in the folder, **private_ethereum_setup****

Note:
- Download the [genesis.json](https://github.com/LifnaJos/private_ethereum_setup/blob/main/private_ethereum_setup/genesis.json) file from the repository
- Edit the **alloc** parameter in the file with the public keys of the accounts created
- The images have a slight error. Do not remove the "0x" when editing the alloc public addresses with your own instead use the entire address with "0x.......etc"

![alloc](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/alloc_field.png)

- Edit the **extradata** withe public key of the signer.
- NOTE: The public Address of the Mining node (the signer) should be entered here. Also remove the beginning two characters "0x" before entering the public address otherwise it will result in error
- Here, its with the public key of Node 1

![extra](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/extradata.png)

**5. Initialize the nodes with the genesis file**
```
geth init --datadir node1 genesis.json
```
![Node_1](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_1_init.png)
```
geth init --datadir node2 genesis.json
```
![Node_2](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_2_init.png)

**6. For configuring the bootnode**
- As we are following geth 1.12 stable version, bootnode needs to be installed separately
```
sudo add-apt-repository -y ppa:ethereum/ethereum
sudo apt-get update
sudo apt-get install bootnode
```
- Create a key for the bootnode and save it to boot.key in the folder
```
bootnode -genkey boot.key
```
![Folder_1](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/folder_1.png)

## Step - 4 :  Establish a Peer-Peer Connection between the nodes along with the bootnode
**1. On the first Terminal, Use **boot.key** to run the bootnode :**
```
bootnode -nodekey boot.key -verbosity 9 -addr :30305
```
![Bootnode](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/bootnode_running.png)

**Note:**
- Open separate terminals for each node, leaving the bootnode running on the first terminal.
- Bootnode is waiting for the Peers to join the network
- In each terminal, run the following command (replacing node1 with node2, node3 wherever appropriate)
- Give each node a **different** IDs for --port and authrpc.port
- The **account address** and **password** file for node 1, node 2, and node 3 must be provided.
- Provide the enode details of the bootnode generated in the step while running Node1 and Node2

**2. On the second Terminal, Run Node 1**
```
geth --datadir node1 --port 30306 --bootnodes enode://2bccaf4b4cf5d10f0e8b49cb68b3c3ad867b6cb40596c78a8b216ae8dd62a174457b9d8839364074047f749914e84b999b92485e988510edca153341a6f6107a@127.0.0.1:0?discport=30305 --networkid 123454321 --unlock 0x98608ADf9c785d54f40cDcf6700E990771b19226 --password node1/password --authrpc.port 8551 --miner.etherbase 0x98608ADf9c785d54f40cDcf6700E990771b19226 --mine
```
![Node_1_run](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_1_run.png)

- Node 1 starts mining

![Node_1_mine](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_1_mining.png)

**Note:**
- In this experiment, we are making Node 1, as the validator to mine the blocks. So, explicitly mention that miner.etherbase with the public address of Node 1.
- The mining starts as soon as this node is up.

**3. On the third Terminal, Run Node 2**
```
geth --datadir node2 --port 30307 --bootnodes enode://2bccaf4b4cf5d10f0e8b49cb68b3c3ad867b6cb40596c78a8b216ae8dd62a174457b9d8839364074047f749914e84b999b92485e988510edca153341a6f6107a@127.0.0.1:0?discport=30305 --networkid 123454321 --unlock 0x7B25e791D24A3F5c453A9E5468cF6cEa2243092C --password node2/password --authrpc.port 8552
```
![Node_2_run](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_2_run.png)

- Node 2, receives the mined details on its terminal

![Node_2_update](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/node_2_update.png)

- Bootnode track the logs of Peers in the network

![Bootnod_update](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/boot_node_update.png)
  
## Step - 5 : Exploring the network by attaching Javascript console to Node 1

Open the fourth terminal and attach the JavaScript Console with Node 1

```
geth attach node1/geth.ipc
```
![Geth](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_ipc.png)

**1. Fetch network status** : 
```
net.peerCount
```
**2. To list the nodes in the network** : 
```
eth.accounts
```
**3. To list the nodes in the network** : 
```
eth.accounts[0]
```

**4. To fetch the number of blocks mined** : 
```
eth.blockNumber
```

**5. To check the balance of the accounts in wei** : 

```
eth.getBalance(eth.accounts[0])
```

``` 
eth.getBalance("0x98608ADf9c785d54f40cDcf6700E990771b19226")
```

**6. To check the balance of the accounts in ether** : 
```
web3.fromWei(eth.getBalance("0x98608ADf9c785d54f40cDcf6700E990771b19226"), "ether")
```

**Note:** 0x98608ADf9c785d54f40cDcf6700E990771b19226 - Public address of the account in Node 1

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_1.png)

**7. To fetch the details of the lastest mined block** : 
```
eth.getBlock(eth.blockNumber)
```

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_2.png)

**7. To fetch the details of a specific block** : 
```
eth.getBlock(<enter a blockNo >)
```

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_3.png)

**8. To check the account balance of the peer machine, provide their Public Key** : 
```
web3.fromWei(eth.getBalance("0x7B25e791D24A3F5c453A9E5468cF6cEa2243092C"), "ether")
```

**Note:** 0x7B25e791D24A3F5c453A9E5468cF6cEa2243092C : Public address of the account in Node 2.

**9. Fetch the details of the peers in the network** : admin.peers

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_4.png)

**10. Perform Transactions between peers in the network** : 
```
eth.sendTransaction({from:"0x98608ADf9c785d54f40cDcf6700E990771b19226", to:"0x7B25e791D24A3F5c453A9E5468cF6cEa2243092C", value: web3.toWei(10, "ether"), gas:30000})
```

**Note:** The response will the hash of the transaction

**11. Check the balances of sender and receiver** 
```
web3.fromWei(eth.getBalance("0x98608ADf9c785d54f40cDcf6700E990771b19226"), "ether")
```
``` 
web3.fromWei(eth.getBalance("0x7B25e791D24A3F5c453A9E5468cF6cEa2243092C"), "ether")
```
  
![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_5.png)

**12. To check the details of the transaction on Node 1 Terminal**

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_node_1_explore.png)

**13. To get the details of the block in which the transaction is added** :
```
web3.eth.getTransaction("0x97dcc5cd3f903ad988faea6998795118df5e7105876fd7776c53c2021233b1ab")
```

**Note:** 0x97dcc5cd3f903ad988faea6998795118df5e7105876fd7776c53c2021233b1ab : Hash of the Transaction

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_6.png)

**14. To check the contents in the Mempool - Transaction Pool** : 
```
txpool.content
```

**Note:** Perform a set of 5 transactions as discussed in the above step and then, check the mempool

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_8.png)

**15. To check the status of the Mempool - Transaction Pool** : 
```
txpool.status
```

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_7.png)

**10. To check the transactions initiated by a client, which are in the pool** : 
```
txpool.contentFrom("0x98608ADf9c785d54f40cDcf6700E990771b19226")
```
**Note:** 0x98608ADf9c785d54f40cDcf6700E990771b19226 : Public address of the account in Node

![Geth_explore](https://github.com/LifnaJos/private_ethereum_setup/blob/main/images/geth_explore_9.png)

## Acknowledgement
* [Go Ethereum Documentation : End-to-end example](https://geth.ethereum.org/docs/fundamentals/private-network#end-to-end-example)

* [Go Ethereum - Github Developers Community](https://github.com/ethereum/go-ethereum/issues/27850) for helping me in resolving the issues in performing the mining on Private Ethereum Network setup using Geth.

* [Basic Writing & Formatting Syntax](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
