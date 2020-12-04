# GEPx-Blockchain
Welcome to the hands-on build for blockchain!

We will build on the most commonly used private, permissioned blockchain platform - Hyperledger Fabric

The use-case is to build the initial part of a platform to host a power exchange - where power can be sold and bought as a commodity by buyers and sellers. We will create a blockchain platform to allow buyers and sellers to place bids and settle them. We will leverage smart contracts to interact with the blockchain. For more functional details around the use-case, please refer to the documentation shared.

The hands-on activity needs some pre-requisites to be completed. Here are the high level steps:

1. Create a personal AWS free-tier Account
2. Spin up a linux based EC2 server on AWS
3. [Install pre-requisite technologies such as docker (a container to hold the hyperledger fabric), java and node](https://github.com/GE-Blockchain-Demo/GEPx-Blockchain/blob/main/README.md#installing-pre-requisites)
4. [Install hyperledger fabric](https://github.com/GE-Blockchain-Demo/GEPx-Blockchain/blob/main/README.md#installing-hyperledger-fabric)

==============

During the session, we will:

5. [Import use-case specific code](https://github.com/GE-Blockchain-Demo/GEPx-Blockchain/blob/main/README.md#cloning-this-repository)
6. [Start the blockchain network 
7. Create 2 nodes (orgs/peers) and a channel](https://github.com/GE-Blockchain-Demo/GEPx-Blockchain/blob/main/README.md#start-the-blockchain-network--create-2-nodes-orgspeers-and-a-channel)
8. Create an admin to start the bidding session
9. Create companies (users) who will place bids
10. Place bids
11. Stop adding new bids to session by closing session
12. Finalize(Confirm) bids
13. End Session - Run smart contract to settle bids

The smart contract (chaincode) code is already written and provided to you during the hands-on session.

What to expect:

1. This is a technical hands-on session, although instructions are detailed, there is always a chance that something doesnt work.
2. Blockchain is a type of a distributed ledger database. There is no User Interface and pretty screens. Be ready and comfortable with command line interfaces.
3. Try and keep up. There is a lot of work to be done in a short time. The more prepared and attentive you are, the more beneficial the session will be for you.

### IMPORTANT: The EC2 server on AWS is free for the first 750 hours. Ensure you 'stop' the server when not in use to avoid any costs. 

## Steps to run application

### Installing Pre-requisites 
```diff
- Note : '+' sign indiactes command to be executed please copy commands without '+' sign!
```
#1 Login to your Virtual Machine (VM)

- Open command promt from the folder where your .pem file is stored

Start --> Type cmd and press enter key. This opens a new command prompt with the text below (the SSO will be different for you)
```diff
Microsoft Windows [Version 10.0.18363.1198]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\326001408>
```

Change directory to the folder where the .pem file was downloaded. This is usually in the 'Downloads' folder. If you saved it in a different folder, change directory (cd) to that folder.
```diff
+ cd Downloads
```
The output will be:
```diff
Microsoft Windows [Version 10.0.18363.1198]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\326001408>cd Downloads

C:\Users\326001408\Downloads>
```

- Login to your Virtual Machine (EC2 on AWS). Replace the values in the tags <> to your values
```diff
+ ssh -i <pemfile>.pem ec2-user@<public-ip>
```
If this doesn't work, ensure your EC2 is in 'Running' state.
The output will be:
```diff
C:\Users\326001408\Downloads>ssh -i ali1.pem ec2-user@65.0.135.36
The authenticity of host '65.0.135.36 (65.0.135.36)' can't be established.
ECDSA key fingerprint is SHA256:qVqRRgAHO9ibF29lWuVKCyTdWrspDfqvV/feEY3g61g.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '65.0.135.36' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-172-31-12-219 ~]$
```
#1 Create a file install.sh and copy paste following contents to it

Create and open file
```diff
+ vi install.sh
```

Copy this and paste it to the file by right clicking on command promt
```
sudo yum install docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
sudo chmod 666 /var/run/docker.sock
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo yum install git
curl https://dl.google.com/go/go1.15.2.linux-amd64.tar.gz --output go1.15.2.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.15.2.linux-amd64.tar.gz
sudo yum install java-1.8.0-openjdk
sudo yum install -y gcc-c++ make
curl -sL https://rpm.nodesource.com/setup_12.x | sudo -E bash -
sudo yum install -y nodejs
```

Make sure you don't miss any letter from above contents! 
If any letter is missed you can input it
```
Press I key on keyboard
Using arrow keys go to place where you want to input the missing letter
Type your missing letter
```

Save and close file
```diff
Press Esc key
+ type :wq!
```

#2 Give execute permissions to the file

```diff
+ chmod +x install.sh
```

#3 Execute install.sh

```diff
+ ./install.sh
```
While installing promt will ask for inputs. Give 'Y' to all.

#4 Give permissions

```diff
+ sudo chmod 777 /usr/local/bin/docker-compose
```

```diff
+ sudo chmod 777 /usr/local/go/pkg
```

```diff
+ sudo chmod 777 /usr/local/go/src
```

#5 Set Environment Variables
- Open .bash_profile file
```diff
+ vi .bash_profile
```
- Copy paste following to the file
```
export PATH=$PATH:/usr/local/go/bin
export GOPATH=/usr/local/go
```
- Save the file
```diff
press Esc key
+ :wq!
```
- Exit from VM
```diff
+ exit
```
- Restart the VM
```diff
+ ssh -i <pemfile>.pem ec2-user@<public-ip>
```

Now we are done with prerequisite installation!

### Installing Hyperledger Fabric

#1 Goto go/src directory
```diff
+ cd $GOPATH/src
```

#2 Run following command
```diff
+ sudo curl -sSL https://bit.ly/2ysbOFE | bash -s
```
This will install hyperledger fabric including [fabric-samples](https://github.com/hyperledger/fabric-samples) folder which we will use for creating test-network with CA.

### This completes the prerequisite installation setups. See you in the workshop! 
============================================



### Cloning this repository

#1 cd into fabric-sample directory
```diff
+ cd $GOPATH/src/fabric-samples
```

#2 Clone this repo using following command
```diff
+ git clone https://github.com/GE-Blockchain-Demo/GEPx-Blockchain.git
```
(Make sure you clone this repository inside fabric-samples!)

## Start the blockchain network & Create 2 nodes (orgs/peers) and a channel

- For out demo we are creating CA authorized network
- This network will have 2 nodes 
       - org1.peer0
       - org2.peer0
- Both the orgs will come under "mychannel" we are creating with the help of following commands
- Smart-contract will be deployed on this channel as mentioned below which will initiate our blockchain with genesis block

### Create channel with CA

#1 Goto test-network directory
```diff
+ cd $GOPATH/src/fabric-samples/test-network
```

#2 Create Channel with CA
```diff
+ ./network.sh up createChannel -ca
```
This will create a channel to which we will add two nodes(org1 and org2) to the network for POC

### Deploy Smart-Contract on channel

Make sure you are in test-network directory and run following command
```diff
+ ./network.sh deployCC -ccn gepx -ccp ../GEPx-Blockchain/chaincode-go/ -ccep "OR('Org1MSP.peer','Org2MSP.peer')"
```
This will add org1 and org2 to the channel and deploy chaincode named gepx on them. By default it will chreate one peer per org.

Output -
```
2020-12-02 05:09:23.306 UTC [chaincodeCmd] ClientWait -> INFO 001 txid [8e1e1414b2f0e2d2891ea5f8258b9d814a7ae414437f666632fa1f299a505f39] committed with status (VALID) at localhost:7051
2020-12-02 05:09:23.314 UTC [chaincodeCmd] ClientWait -> INFO 002 txid [8e1e1414b2f0e2d2891ea5f8258b9d814a7ae414437f666632fa1f299a505f39] committed with status (VALID) at localhost:9051
Chaincode definition committed on channel 'mychannel'
Using organization 1
Querying chaincode definition on peer0.org1 on channel 'mychannel'...
Attempting to Query committed status on peer0.org1, Retry after 3 seconds.
+ peer lifecycle chaincode querycommitted --channelID mychannel --name gepx
+ res=0
Committed chaincode definition for chaincode 'gepx' on channel 'mychannel':
Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]
Query chaincode definition successful on peer0.org1 on channel 'mychannel'
Using organization 2
Querying chaincode definition on peer0.org2 on channel 'mychannel'...
Attempting to Query committed status on peer0.org2, Retry after 3 seconds.
+ peer lifecycle chaincode querycommitted --channelID mychannel --name gepx
+ res=0
Committed chaincode definition for chaincode 'gepx' on channel 'mychannel':
Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]
Query chaincode definition successful on peer0.org2 on channel 'mychannel'
Chaincode initialization is not required
```
### This completes network creation and smart contract deployment
================================================================================================

### Running Application

To run the POC application goto GEPx-Blockchain/application-javascript
```diff
+ cd $GOPATH/src/fabric-samples/GEPx-Blockchain/application-javascript
```

To make sure all dependecies are installed run
```diff
+ npm install
```

This should give output as
```
found 0 vulnerabilities
```

Now we are ready to run! 
Follow the steps given below

## Create an admin to start the bidding session & Create companies (users) who will place bids

- From application side we will enroll the nodes we have created above in the network
- One admin user will be created on org1 for initiating session
- Any number of companies can be resgistered with our smart-contract as shown bellow 
- These registered companies can participate in bidding process
- Example is given for enrolling 4 companies 2 on node1(org1.peer0) and 2 on node2(org2.peer0)

#1 Enroll network nodes

Command to run-
```diff
+ node enrollNode.js <node>
```

Example - Enroll node1
```diff
+ $ node enrollNode.js org1
```

Output - Enroll node1
```
--> Enrolling the Org1 CA admin
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully enrolled admin user and imported it into the wallet
```

Example - Enroll node2
```diff
+ $ node enrollNode.js org2
```

Output - Enroll node2
```
--> Enrolling the Org2 CA admin
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a CA Client named ca-org2
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2
Successfully enrolled admin user and imported it into the wallet
```

#2 Register Admin

command to run -
```diff
+ node registerEnrollUser.js <node> <Admin>
```

Example - 
```diff
+ $ node registerEnrollUser.js org1 adminuser
```

Output -
```
--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully registered and enrolled user adminuser and imported it into the wallet
```

#3 Create Session - This will initialize the session for bidding

Command to run -
```diff
+ node createSession.js <node> <Admin> <sessionID>
```

Example - 
```diff
+ $ node createSession.js org1 adminuser session1
```

Output -
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Submit Session: Propose a new session
*** Result: committed

--> Evaluate Session: query the session that was just created
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP"
  ],
  "privateBids": {},
  "finalizedBids": {},
  "status": "Open"
}
```

#4 Register companies for bidding

Command to run -
```diff
+ node registerEnrollUser.js <node> <companyID>
```

Example - Register company1 to node1
```diff
+ $ node registerEnrollUser.js org1 company1
```

Output - Register company1 to node1
```
--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully registered and enrolled user company1 and imported it into the wallet
```

Example - Register company2 to node1
```diff
+ $ node registerEnrollUser.js org1 company2
```

Output - Register company2 to node1
```
--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1
Successfully registered and enrolled user company2 and imported it into the wallet
```

Example - Register company3 to node2
```diff
+ $ node registerEnrollUser.js org2 company3
```

Output - Register company3 to node2
```
--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a CA Client named ca-org2
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2
Successfully registered and enrolled user company3 and imported it into the wallet
```

Example - Register company4 to node2
```diff
+ $ node registerEnrollUser.js org2 company4
```

Output - Register company4 to node2
```
--> Register and enrolling new user
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a CA Client named ca-org2
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2
Successfully registered and enrolled user company4 and imported it into the wallet
```
### This completes registering Admin and other users along with initiating Session
====================================================================================

## Place Bids

- Following commands show how to create and submit bids during a session
- Any company can place any number of bids
- The exaple provided bellow places 1 bid by 4 companies 2 of them are sell bid whereas 2 are buy bids
- For submitting bid we need to use BidID generated while bid creation it is highleghted in orange color below

#5 Create and submit bid - This is used to place bid in particular session

Command to run -
1. Create bid
```diff
+ node bid.js <node> <companyID> <sessionID> <Volume> <bidType(sell/buy)>
```

2. Submit bid
```diff
+ node submitBid.js <node> <companyID> <sessionID> <BidID(generated by above command)>
```

Example - Create bid for company1
```diff
+ $ node bid.js org1 company1 session1 1000 sell
```

Output - Create bid for company1
```diff
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

/--> Evaluate Transaction: get your client ID
/*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=

/--> Submit Session: Create the bid that is stored in your organization's private data collection
/*** Result: committed
! *** Result ***SAVE THIS VALUE*** BidID: d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a

/--> Evaluate Session: read the bid that was just created
/*** Result:  Bid: {
  "bidType": "sell",
  "volume": 1000,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}
```

Example - Submit bid for company1
```diff
+ $ node submitBid.js org1 company1 session1 !d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a
```

Output - Submit bid for company1
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

Example - Create bid for company2
```diff
+ $ node bid.js org1 company2 session1 700 sell
```

Output - Create bid for company2
```diff
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

/--> Evaluate Transaction: get your client ID
/*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=

/--> Submit Session: Create the bid that is stored in your organization's private data collection
/*** Result: committed
! *** Result ***SAVE THIS VALUE*** BidID: 40cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09

/--> Evaluate Session: read the bid that was just created
/*** Result:  Bid: {
  "bidType": "sell",
  "volume": 700,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}
```

Example - Submit bid for company2
```diff
+ $ node submitBid.js org1 company2 session1 40cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09
```

Output - Submit bid for company2
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

Example - create bid for company3
```diff
+ $ node bid.js org2 company3 session1 600 buy
```

Output - create bid for company3
```diff
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

/--> Evaluate Transaction: get your client ID
/*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==

/--> Submit Session: Create the bid that is stored in your organization's private data collection
/*** Result: committed
! *** Result ***SAVE THIS VALUE*** BidID: aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250

/--> Evaluate Session: read the bid that was just created
/*** Result:  Bid: {
  "bidType": "buy",
  "volume": 600,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}
```

Example - Submit bid for company3
```diff
+ $ node submitBid.js org2 company3 session1 aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250
```

Output - Submit bid for company3
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: query the session you want to join

--> Submit Session: add bid to the session

--> Evaluate Session: query the session to see that our bid was added
*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```

Example - create bid for company4
```diff
+ $ node bid.js org2 company4 session1 700 buy
```

Output - create bid for company4
```diff
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

/--> Evaluate Transaction: get your client ID
/*** Result:  Bidder ID is eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==

/--> Submit Session: Create the bid that is stored in your organization's private data collection
/*** Result: committed
! *** Result ***SAVE THIS VALUE*** BidID: cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419

/--> Evaluate Session: read the bid that was just created
/*** Result:  Bid: {
  "bidType": "buy",
  "volume": 700,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}
```

Example - Submit bid for company4
```diff
+ $ node submitBid.js org2 company4 session1 cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419
```

Output - Submit bid for company4
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

/--> Evaluate Session: query the session you want to join

/--> Submit Session: add bid to the session

/--> Evaluate Session: query the session to see that our bid was added
/*** Result: session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Open"
}
```
### This completes bid creation 
================================================================================================================

## Stop adding new bids to session by closing session

- The bidding prosess is allowed only during session time slot
- Once session time is over new bids cannot be created only we can confirm existing bids
- Following command shows how session can be stopped by admin user
- In real scenario this call will be a schedulled call

#6 Close Session - This should be used at end of time slot of session. Once the session is closed no more bidding can be done

Command to run -

```diff
+ node closeSession.js <node> <adminID> <sessionID>
```

Example -
```diff
+ $ node closeSession.js org1 adminuser session1
```

Output -
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Submit Session: close session
*** Result: committed

--> Evaluate Session: query the updated session
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {},
  "status": "Close"
}
```
### This completes stop bidding process
===============================================================================================================

## Finalize(Confirm) bids

- Once the session is stopped each user will get some time window to confirm their bids
- The confirmed bids will then be revealed in the blockchain
- For the settlement these bids will be considered
- A bid can be finalized only by the user who has created it

#7 Finalize Bid - This will finalize the bid placed by companies. Only finalized bids will be considered for approval

Command to run -
```diff
+ node finalizeBid.js <node> <comapnyID> <sessionID> <bidID>
```

Example - Finalized bid for company4
```diff
+ $ node finalizeBid.js org2 company4 session1 cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419
```

Output - Finalized bid for company4
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "buy",
  "volume": 700,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

Example - Finalized bid for company3
```diff
+ $ node finalizeBid.js org2 company3 session1 aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250
```

Output - Finalized bid for company3
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org2.example.com/connection-org2.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org2

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "buy",
  "volume": 600,
  "org": "Org2MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

Example - Finalized bid for company2
```diff
+ $ node finalizeBid.js org1 company2 session1 40cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09
```

Output - Finalized bid for company2
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "sell",
  "volume": 700,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "bidType": "sell",
      "volume": 700,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```

Example - Finalized bid for company1
```diff
+ $ node finalizeBid.js org1 company1 session1 d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a
```

Output - Finalized bid for company1
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Evaluate Session: read your bid
*** Result:  Bid: {
  "bidType": "sell",
  "volume": 1000,
  "org": "Org1MSP",
  "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
  "status": "Placed"
}

--> Evaluate Session: query the transaction to see that our bid was added
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "bidType": "sell",
      "volume": 700,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Finalized"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "bidType": "sell",
      "volume": 1000,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Finalized"
    }
  },
  "status": "Close"
}
```
### This completes bid confirmation process
===================================================================================================

## End Session - Run smart contract to settle bids

- This is the final step of our flow
- It will end the session and give approvals to the bid
- The bids will be either approved, partially approved or declined depending on the demand and supply

#8 End Session - This will evaluate the bids and provide approval

Command to run -
```diff
+ node endSession.js <node> <adminID> <sessionID>
```

Example -
```diff
+ $ node endSession.js org1 adminuser session1
```

Output -
```
Loaded the network configuration located at /opt/go/src/github.com/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a file system wallet at /opt/go/src/github.com/fabric-samples/GEPx-Blockchain/application-javascript/wallet/org1

--> Submit the session to end the session
*** Result: committed

--> Evaluate Session: query the updated session
*** Result: Session: {
  "admin": "eDUwOTo6Q049YWRtaW51c2VyLE9VPWNsaWVudCtPVT1vcmcxK09VPWRlcGFydG1lbnQxOjpDTj1jYS5vcmcxLmV4YW1wbGUuY29tLE89b3JnMS5leGFtcGxlLmNvbSxMPUR1cmhhbSxTVD1Ob3J0aCBDYXJvbGluYSxDPVVT",
  "organizations": [
    "Org1MSP",
    "Org2MSP"
  ],
  "privateBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "org": "Org1MSP",
      "hash": "35b51817eceb92b51d57afbce7e010f7fb0e179b8a76660b45389a51fa60bdf7"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "org": "Org2MSP",
      "hash": "0c48debbfbf75582676a4f75ada10f4b2b9965d8d27cd3fff626565e0050fbd8"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "org": "Org2MSP",
      "hash": "256910148268786cf1e64d1a4468ed7c215d7ea1402ca8d33b2ce6c3cd018a61"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "org": "Org1MSP",
      "hash": "08f2f424627a36eb35c0e566e7a79f897a6d015df67bebda2df75dfec3f632f0"
    }
  },
  "finalizedBids": {
    "\u0000bid\u0000session1\u000040cd1b51d68a51e849ed8632d14b64526082d5959a195071869a6f02b0fe6d09\u0000": {
      "bidType": "sell",
      "volume": 700,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTIsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Approved"
    },
    "\u0000bid\u0000session1\u0000aa88a7f7d33232424ac492feb02ae207a15465c674814dc7536ef244cb4ce250\u0000": {
      "bidType": "buy",
      "volume": 600,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTMsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Approved"
    },
    "\u0000bid\u0000session1\u0000cee404e3895c7f6e09f622d130e116aeff655f225f7c225a13ac60ba4f24d419\u0000": {
      "bidType": "buy",
      "volume": 700,
      "org": "Org2MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTQsT1U9Y2xpZW50K09VPW9yZzIrT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzIuZXhhbXBsZS5jb20sTz1vcmcyLmV4YW1wbGUuY29tLEw9SHVyc2xleSxTVD1IYW1wc2hpcmUsQz1VSw==",
      "status": "Approved"
    },
    "\u0000bid\u0000session1\u0000d10a570fa1e9566afcd956858aadf6e20de67157b4dfbb71b705c537c3535a4a\u0000": {
      "bidType": "sell",
      "volume": 1000,
      "org": "Org1MSP",
      "bidder": "eDUwOTo6Q049Y29tcGFueTEsT1U9Y2xpZW50K09VPW9yZzErT1U9ZGVwYXJ0bWVudDE6OkNOPWNhLm9yZzEuZXhhbXBsZS5jb20sTz1vcmcxLmV4YW1wbGUuY29tLEw9RHVyaGFtLFNUPU5vcnRoIENhcm9saW5hLEM9VVM=",
      "status": "Partially Approved"
    }
  },
  "status": "ended"
} 

```
### This completes out last step!!!
===============================================================================================================

### Deleting Database

Once we are done with all execution we can clean up our database using following command

```diff
+ rm -rf wallet
```

### Shut down network

- Go to test-network directory
```diff
+ cd $GOPATH/src/fabric-samples/test-network
```
- Shut down the network
```diff
+ ./network.sh down
```
