## Step - 1 : Installing Geth on Ubuntu
* sudo add-apt-repository -y ppa:ethereum/ethereum
* sudo apt-get update
* sudo apt-get install ethereum
* sudo apt-get upgrade geth

## Step - 2 : Check the version of Geth on the Terminal
* geth version

## Step - 3 : Create a Private Ethereum Network

**Method - 1** : Clone the private_ethereum_setup.git

**Method - 2** : Follow the steps to create nodes in the Private Ethereum Network
1. Create a folder named, private_ethereum_setup :
* mkdir private_ethereum_setup

2. Create 2 subfolders named node1 and node2 in the folder **private_ethereum_setup**
* cd private_ethereum_setup   
* mkdir node1 node2
   
3. Create 2 accounts in the folder corresponding to node1 and node2
* geth --datadir "node1" account new
* geth --datadir "node2" account new
   
Note : 
- This command will prompt you to enter a passphrase and generates Public and Private Keys.
- For future reference, save the keypair in the file **network_keypair.txt**
- For each node, save the password in a file, **password.txt**

4. Create a **genesis.json** file in the folder, **private_ethereum_setup**

Note:
- Download the file from the repository
- Edit the **alloc** parameter in the file with the public keys of the accounts created
  
5. Initialize the nodes with the genesis file
* geth init --datadir node1 genesis.json

* geth init --datadir node2 genesis.json

6. For configuring the bootnode, create a key for the bootnode and save it to boot.key in the folder
* bootnode -genkey boot.key
 
## Step - 4 :  Establish a Peer-Peer Connection between the nodes along with the bootnode
1. On the first Terminal, Use **boot.key** to run the bootnode :
* bootnode -nodekey boot.key -verbosity 9 -addr :30305

**Note:**
- Open separate terminals for each node, leaving the bootnode running on the first terminal. 
- In each terminal, run the following command (replacing node1 with node2, node3 wherever appropriate, and giving each node a **different** --port and authrpc.port IDs. 
- The **account address** and **password** file for node 1, node 2, and node 3 must be provided.

2. On the second Terminal, Run Node 1
* geth --datadir node1 --port **30306** --bootnodes enode://e8fbf28286a8bc26e53cbe61c4bef720b2d844b74aca3260517a57502732bb2871a0dc20cb019a2696a0690507a60c54b99fe6030924923267d6cbb9e9d4a2a9@127.0.0.1:0?discport=30305 --networkid 123454321 --unlock **0x2EC436918AfE50376b0a7454f6E0987fF0eFECbb** --password **node1/password** --authrpc.port **8551** --miner.etherbase **0x2EC436918AfE50376b0a7454f6E0987fF0eFECbb** --mine

**Note:**
- In this experiment, we are making Node 1, as the validator to mine the blocks. So, explicitly mention that miner.etherbase with the public address of Node 1.
- The mining starts as soon as this node is up.

3. On the third Terminal, Run Node 2
* geth --datadir node2 --port **30307** --bootnodes enode://e8fbf28286a8bc26e53cbe61c4bef720b2d844b74aca3260517a57502732bb2871a0dc20cb019a2696a0690507a60c54b99fe6030924923267d6cbb9e9d4a2a9@127.0.0.1:0?discport=30305 --networkid 123454321 --unlock **0x629B332FBBCAE2eB49da020dA66fa8f4faB11EFb** --password **node2/password** --authrpc.port **8552**

**Note:**
- Node 2, receives the mined details on its terminal

## Step - 5 : Exploring the network by attaching Javascript console to Node 1
* geth attach node1/geth.ipc

**1. Fetch network status**
* > net.peerCount

**2. Fetch the details of the peers in the network**
* > admin.peers

**3. To list the nodes in the network**
* > eth.accounts

**4. To check the balance of the accounts associated with the Node**
* > web3.fromWei(eth.getBalance("**0x2EC436918AfE50376b0a7454f6E0987fF0eFECbb**"), "ether")
Here, the response will be in ethers
* > eth.getBalance(eth.accounts[0])
Here, the balance will be in wei
Here, **0x2EC436918AfE50376b0a7454f6E0987fF0eFECbb** : Public address of the account in Node 1

**5. To check the account balance of the peer machine, provide their Public Key**
* > eth.getBalance("**0x629B332FBBCAE2eB49da020dA66fa8f4faB11EFb**")
Here, **0x629B332FBBCAE2eB49da020dA66fa8f4faB11EFb** : Public address of the account in Node 2.

**6. Perform Transactions between peers in the network**
* > eth.sendTransaction({from:"0x2ec436918afe50376b0a7454f6e0987ff0efecbb",
to:"0x629B332FBBCAE2eB49da020dA66fa8f4faB11EFb", value: web3.toWei(10,
"ether"), gas:30000})
Here, the response will the hash of the transaction

**7. To check the details of the transaction**
- Go to Terminal 2, where Node 1 is running and mining the blocks

**8. To get the details of the block in which the transaction is added**
* > web3.eth.getTransaction("**0x97dcc5cd3f903ad988faea6998795118df5e7105876fd7776c53c2021233b1ab**")
Here, **0x97dcc5cd3f903ad988faea6998795118df5e7105876fd7776c53c2021233b1ab** : Hash of the Transaction

**8. To check the contents in the Mempool - Transaction Pool**
- Perform a set of 5 transactions as discussed above in step 6
- Then, check the mempool
* > txpool.content

**9. To check the status of the Mempool - Transaction Pool**
* > txpool.status

**10. To check the transactions initiated by a client, which are in the pool**
* > txpool.contentFrom("**0x2ec436918afe50376b0a7454f6e0987ff0efecbb**")
Here, **0x2ec436918afe50376b0a7454f6e0987ff0efecbb** : Public address of the account in Node

