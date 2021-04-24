#  *Blockchain*
_Notes: By Harsimran Singh_
- - - -
The motivation of Blockchain is to create a “trust-less” system which through the mechanisms of the system itself implies trust such that no verifying/governing third party is needed.  

What quality do governing/Trusted third Parties possess?
	* Transaction History database for verification (ledger)

In the next part I will explain the mechanics of how this is done.

## Blockchain Theory

### Data Structure

A blockchain is a continuously growing list of records, called blocks, which are linked and secured using cryptography. 

The “chain” in this case is represented by a linked list. The so called “blocks” are nodes in the linked list. The references are cryptographic hashes of the previous block in the chain.

A little mind boggling I know.
Let’s break the structure down further.

The first block in the linked list is called the genesis block it was constructed by the blockchain system itself.  

Each block contains a Previous Hash values as well as its own hash value. The previous hash in terms of the genesis block  is all zeros (at least in the implementation used here) but as we move to the next block we see the hash of the genesis block reflected in the previous has of block 1.

![](README/Blockchain@2x.png)

I know what you must be thinking.

**What if we stored the transactions within the blocks?**

Well here, my faithful reader, lies the power of blockchain and its ability to transcend governing parties. 

Each block can contain a lot more data.  For example one of the most popular uses of blockchain is cryptocurrency where the block can contain:
	* Block ID
	* Transactions
	* Merkle root  (identifies different transactions in a block).
	* hash
	* previous hash
	* nonce (number only used once used in mining, very important)
	* timestamp

More on this later.

### Cryptographic Hashes - SHA256

One thing I quite deliberately left out of the data structures section was the the hash pointers. This was because hashes are so important that they deserve their own section.

Hashes are the “fingerprints” for blocks.

We know that in a blockchain we use hash pointers to reference to the previous node in the linked list.

But… how do we calculate said hashes?

Easy, we use a generic cryptographic hash function like SHA256.

SHA256’s input could be anything but its output will be a 256 bit long hexadecimal sequence (every hexadecimal character is 4 bits so if we divide 256 bits by 4 bits we will have an output of length 64 not very important to know the length but it is good to know). 

**The inputs here in the case of blockchain are all the contents of the blocks.**

The complexity and uniqueness bestowed upon us by the SHA256 cryptographic hash function is perfect for our blocks’ “fingerprints”. 

Fun fact: SHA256 was developed by the NSA.

Could we use other hash functions ? Yes, but we will stick to this one because this is what bitcoin uses. Bitcoin uses SHA256 twice in a row on the contents of blocks.

I’m gonna have to pull up my sleeves here and go a little deeper into hashing algorithms here so please bear with me.

Features of hashing algorithms :
	* deterministic: it means that if we apply the same hash function to the same input  then the output must be the same
	* one-way: it is easy to generate the hash with the hashing algorithm but extremely hard (time-consuming) to restore the original input. We want it to be like a trapdoor function.
	* collision-free: there is an **extremely** low probability of collisions in SHA256. It means that no two different inputs share the same output hash. We want uniqueness.
	* avalanche effect: a little change in the input results in a completely different output . Otherwise crypto-analysts could make predictions about the input based on the output exclusively.



### Decentralized Ledger

Centralized systems come with a lot of vulnerability. Say we were sending money to each other through the bank. The entire transaction is reliant on the trusted third party in this case the bank. 

If say a hacker was to tamper with the transaction history she could send that money to herself and if she modified the one central ledger or worse found a way to destroy it we would be down bad. 

This system is very similar to the client-server model where there is a server at the center of all interactions with the clients. The data and database is present on a single logical server and anyone with the right credentials can access the database.

In decentralized systems there is no central database. In order to hack the system you would need to hack most of the nodes in the network to do so. It is a P2P (peer to peer) decentralized network of nodes.

So let’s bring this back to blockchain how does this matter?

Well it gives you an idea how blockchain is effective at staving away a huge vulnerability that even third parties like banks and governments can’t completely solve. 

We give it this power by ensuring every person in the blockchain network has a copy of the blockchain. If someone tampers the data in a block the cryptographic hash changes as well so the reference pointers are broken.

You may say “well the hacker could change the data in other blocks as well”.

That is extremely hard especially if the blockchain network is big.

Data that has been written to a block cannot be changed or erased this is why it is called an immutable ledger.


![](README/hackerror.png)


Say in the case of cryptocurrency once of these blocks are used in a  transaction. This will cause the entire network to update so that they also have that one transaction. 

But if the one of the people in the network has their blockchain tampered the rest of the network will alert the  person with the tampered blockchain will discard the tampered blocks in the chain and copy the untampered version replaced blocks from the others in the network.


### Mining 

We have discussed blockchain quite a bit . We now know about Decentralized systems and the basic inner workings of blockchain.  The next thing to understand about blockchain is how it handles transactions.

We know when we transact on a centralized system such as the bank our transaction is handles by the bank.

Who handles the transactions and verification on a blockchain network?

Miners.

Miners get a preset reward for every block they mine.
Miners, getting paid is not the aim of mining it is just the by-product. Mining is the mechanism that allows the blockchain to be a decentralized security.

It is about finding the “right” hash values for the blocks and adding the blocks to the blockchain (not all hashes are valid as we learn later).

Miners are the ones who find the hash value for each block. This is a computationally heavy procedure.

The total amount of hashes is 2 to the power of 256.

Mining has an important parameter in bitcoin it is called “difficulty” and it is characterized by leading zeros.

The aim of mining is to generate hashes BUT there is come constraints. Most of the generated SHA256 hashes are not allowed. 

### Generating Hashes (edit)

I wanted to come back and condense the actual actions of generating hashes since the previous three subsections are a little conceptually hard.

In the process of generating hashes we use all the information in the block and feed this data into the SHA256 algorithm to get a 64 character long hash for the block. 

We use the data in the block but most the data is immutable (we cannot change, think transaction history or ID) , but of course we have to change something to change the hash this is why we have something called the “NONCE”. We change this value thus we change the SHA256 hash. The nonce is changed till we get a “right” hash value.

As mentioned briefly in the past section I made the claim that most hashes that are generated are not allowed.  We also introduced the concept if difficulty.  In the instance of bitcoin hashes need to have 18 leading zeros (at the time these notes were written) this are very hard hashes to generate (  the probability of a hash with 18 leading zeros is ( 16^46)/(16^64) =  (2.1*10^-20) %  as you can see that is a minuscule probability).

The NONCE is short for “number only used once” it is a 32 bit unsigned integer so the range is [0-4 billion].

In order to get the “right” hash this number is continually changed until we reach a valid hash (adheres to difficulty amount), this valid hash can also be called the golden hash.

Miners cannot guess the value of the nonce [because avalanche effect] so usually they start with 0 and keep incrementing it by 1.

In practice this sort of looks like:

`while(notGoldenHash(target)){`
`	hash = generateHash();`
`	this.nonce++;`
`}`

Why is mining difficult? This is because of a concept called proof of work to be discussed down the line.

### Consensus

Blockchain nodes are synchronizing their data all the time. There is no central authority to approve the operations (transactions for example) so the majority of the nodes should reach a consensus before updating distributed ledger.

We call this process consensus.

Example: A miner adds a block to the blockchain and notifies the other nodes of the change. The nodes must reach consensus ( whether to approve or reject).

### Byzantine Generals Problem

[Wikipedia Entry on This problem](https://en.wikipedia.org/wiki/Byzantine_fault)

Understanding this problem will make is very easy to understand the consensus concept.

Before we tackle the Byzantine generals problem we will look at the more simple 2 Generals Problem.

The premise of the problem is that 2 generals plan to attack the enemy city but somehow they have to reach a consensus on whether to attack or not. 

They use messages because their camps are apart. The message can be lost.

To avoid total annihilation both generals need to reach consensus.

Best-Case Scenario: Both of them attack in this case they can win the battle.
Worst-Case Scenario: Just one of them attacks and enemy wins

In this example we see that the network itself cannot be trusted because the messages can be lost.

Conclusion: The net-net of the problem is that if the network is corrupted (and cannot  be trusted) there is nothing we can do to reach consensus. 

Usually the network can be statistically Byzantine which means that sometimes it transmits the messages sometimes it doesn’t in this case we have to send 1000 messages to make sure one of them will reach its destination. 

Onto the Byzantine Generals Problem:

In this problem we expand from 2 generals to many.

Assumption:  the network itself works perfectly but the nodes can be corrupted.

Question: How to reach consensus in a distributed network where nodes can be corrupted (traitors)?

The traitors’ aim is to make sure there won’t be any consensus (so some of the generals will attack  some of them will retreat so the enemy will win).

The generals (traitors as well) will broadcast messages through the network.

Majority vote: Every general selects an alternative which have a majority (more than half of the votes).

Example: [attack, attack, retreat, attack] -> means Attack

For more in-depth analysis of the problem I have posted an Wikipedia Link above so you can truly understand the problem.

The point of showing you all this is that for distributed consensus the way we make sure that all the transactions are valid and not created by hackers?

If 51% of the nodes in the network agree on something then the transaction is accepted.

Approaches to handle fault tolerance ( the Byzantine Generals problem):
	1. Proof of work
	2. Proof of state

### Proof of Work

Mining itself is designed to be a difficult operation (computationally expensive). It is hard to find the right hash bit easy to verify it.

Why does proof of work solve the byzantine generals problem? Because it makes it very expensive to become a traitor.

Proof of work was first used to prevent spam emails: You’d perform a small amount of proof of work and attach that in the header of the email + the receiver can check whether you put work Into that before you sent it. (This worked because spammers will not wait years to send >10k emails).

Finding the “golden hash” is so expensive that it would make it really costly to attack the network.

Why is it so expensive? Computers and electricity (this is also the reason my dad yelled at me in my youth for mining bitcoin for an entire month day and night on a mining rig when I ran up the electricity bill to a ridiculous sum. I basically turned the tool shed to a server room and the price of cooling and running the rig kept mounting up).

The original problem is that there is no way to trust everyone in the network. Proof of work makes sure that miners do not cheat. 

This prevents miners from creating/modifying blocks that benefit themselves.

If a miner finds the right hash he/she gets a reward but only if the other miners agree to accept that transaction (others can easily verify with the hashing algorithm).

If a miner created a fraudulent transaction then all the other miners will refuse to accept it as a new block.

Nash Equilibrium (the most common way to define the  solution  of a  [non-cooperative game](https://en.wikipedia.org/wiki/Non-cooperative_game) , game theory term)  is to create valid blocks and this is what secures the networks.

Disadvantages of Proof-of-Work:
	* It is rather inefficient. The main problem does not solve anything .
	* Costly and wasteful (from electricity perspective).



Thus concludes my notes on blockchain.

- - - -
#  *Cryptocurrency*
_Notes: By Harsimran Singh_
- - - -

Bitcoin, the first cryptocurrency, was constructed by Satoshi Nakamoto in 2009.

> It is the first decentralized digital currency, as  the system works without a central bank or single administrator. The network is peer-to-peer and transactions take place between users directly, without an intermediary. These transactions are verified by network nodes through the use of Cryptography and recorded in a public distributed ledger called blockchain.  

There are two important layers in the bitcoin ecosystem:
	1. Technology: The underlying technology is blockchain
	2. Protocol: The protocol is the bitcoin protocol (bitcoin is a currency as well as a protocol). It defines how participants of the network communicate with each other. 




### Cryptocurrency Policies

Bitcoin has a monetary policy which is controlled entirely via software.

There are two parts to this policy:
	1. Halving:  the number of the coins released into the system is halved every 4 years ( well if we are precise it is halved every 210000)
	2. Block Frequency: How much time does it take on average to mine a block

The Bitcoin Network makes sure new blocks are mined every 10 minutes (every 15 seconds for Ethereum). Right now(2018) in the time of writing this set of notes the current difficulty of bitcoin is 18.  The difficulty of mining depends on the block frequency in order to get each block to mined perfectly on time interval.

Over time the difficulty moves up because usually the amount of miners increases. This means in order to keep the block frequency on the desired frequency the difficulty increases over time.

### Mempools

So now we know that miners mine a block after a certain predefined frequency and take these transactions and put them into the blocks. 

But users can transact at any time they are not bound to a certain interval. How do we deal with this?

This is when the Mempool comes in. The Mempool is a data structure that stores unverified transactions until a miner verifies them and puts them into a block.

How do miners select the optimal set of transactions to verify.

Miners want to maximize the reward.

Miners select M transactions from the mempool and create a new black in the blockchain with these transactions (+ find the right hash for the block).

Every transaction has a transaction fee this is the amount the user is willing to pay for making a given transaction.

This leads us to essentially an optimization problem.

A Miners Reward = X BTC + Transaction Fees

We can come up with a solution using the bin packing algorithm/solution. We have a bin with capacity C ( say about 1MB) and we have N items with values t1, t2 , … , tn

What items to include in the bin to maximize profit?

The miner will sort the items and take the ones with the highest transaction fees (miners get a sum of the fees after mining).

This also means if you have a high transaction fee you are more likely to have your transaction verified sooner.  


### Merkle-Tree

Previously in the blockchain notes we assumed that only one transaction was held by each block for the sake of simplicity.
 
There can be anywhere from 100-800 transactions in a single block (in general depending on the Cryptocurrency) .

It is not optimal however to include all these transactions  in a block .

This is why we use a tree-like data structure and store the root of the tree in the header (entries are SHA256 hashes).

In the implementation I used for this project I made it so that this Merkle-root can verify all the transactions and it is just a single hash value.

If any of the transactions in the block changes then the root value is changed as well. This is why we can verify all the transactions with the Merkle-root exclusively. 

Each transaction has an SHA256 id.  Say we have 2 of said transactions. We will take the id of one transaction and the id of the other transaction concatenate them and serve them to our SHA256 function which will output the value we will use for the root. This is why everything can be verified via the root. 

Say we have 4 transactions. We will do the same method above for every pair of two nodes then we will take the two roots we created and concatenate and hash those two values as well and then we will in practice have a tree with 7 nodes. The 4 original transactions the two sub Merkle roots and the actual final Merkel root.

This is a great added security feature as you can see because  if a single detail of the transactions change or even the order of the transactions change the Merkle-root will change as well.

It is part of the block’s header which forms the hash of the block (after applying the SHA256 algorithm).

It is cryptographic proof of which transactions are in the block


### Public Key and Private Key

There is a big problem. All the data is public. Somehow we need to encrypt the transactions and make sure that other nodes in the network can verify these transactions.

Bitcoin uses ECDSA(Elliptic Curve Digital Signature Algorithm) to ensure that funds can only be spent by their rightful owners.
 
Private key: It is a secret number(256 bit integer) known only  to the person that generated it. We can sign a given transaction with the private key

Public Key: It is generated from the private key and no need to keep it secret. It is extremely hard to get the private key from the public key. The public key is a 2D point on an elliptical curve. 

We can verify the message (that has the signature) with the help of the public key.

So for example: Say we create a transaction. The transaction includes the sender, the receiver , and the amount. Then the owner (who is the sender) is going to sign that transaction with a private key. If the transaction has the signature anyone can verify the transaction using the public key.

`verify(transaction,publicKey);`

We identify wallets with the help of public keys. So when sending BTC we have to use public keys.

There is another layer of security: With RIPEMD we can generate a 160 bit long hash which is called the address.

We can use this address when transferring money to a wallet.


### UTXOs

There may be several transactions in the Cryptocurrency system that haven’t been spend.

For example: Todd sends you 0.1 BTC and Adam sends you 0.2 BTC

These are called unspent transaction outputs or UTXOs.

The problem is that there is no accounts in the cryptocurrency network, We have to calculate the balance of a given user (wallet) based on the UTXOs.

We can use previous transactions during the actual transaction.
So the output of the transaction will the input of another transaction. 

Transaction input amount = transaction output amount

So say you want to buy a computer for  0.25BTC . By using the outputs from Todd and Adam (0.3BTC total)  we can send the computer shop 0.25 but, we have 0.05BTC left over well to deal with this issue we can simply just send the 0.05BTC to yourself.  See the input is equal to the output.  The 0.3 BTC as the input and the output is 0.3BTC as well ( BTC sent to shop + BTC sent to self).

After these transactions. The Todd and Adam’s unspent transactions  removed from the UTXO data structure and the 0.05 BTC from yourself is added as a unspent transaction. 


### Wallets

Bitcoin wallets are where bitcoin is stored but in actuality bitcoin is not stored anywhere. There is the blockchain with the transactions and that is all.

In a centralized system the database stores the balance for every client.

How do we end up with the balance for a given wallet?

We calculate the balance using UTXOs.

We just have t consider the unspent transaction outputs that being to us and sum up the BTC values.

We identify users with the help of a public key or with the address. 









