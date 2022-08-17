 BENEFITS OF USING MERKLE TREES:
1.Efficient verificaton
2. No delay
3. Reduced size of input 
4 Time saving


Merkle Trees
Merkle Trees are a fundamental concept in blockchain technology. They're a special kind of binary tree which is used to encode large chunks of information. The cool thing about Merkle Trees is that they kind of 'build up' from the bottom-up, and allow you to verify if some value is present in the tree or not without having to loop over every element of the tree. This can be quite useful, as we will see. 🧐

What is a Merkle Tree?
A Merkle tree is a type of hash tree in which each leaf node is labeled with the cryptographic hash of a data block, and each non-leaf node is labeled with the cryptographic hash of its child nodes' labels. The majority of hash tree implementations are binary (each node has two child nodes), but they can also have many more child nodes.

A typical Merkle Tree looks something like this:Image

(Reference from using-merkle-trees-for-nft-whitelists)

🤔 What kind of data structure is a Merkle Tree?


Stack

Skiplist

Tree
Let me explain what is going on. All the leaf nodes of the tree, i.e. nodes that don't have any further children, include hashes of data that you want to encode. Note that the values you want to encode in the tree are always just part of the leaf nodes. Since it is a binary tree, each non-leaf node has two children. As you move up from the leaf nodes, the parents will have the hash of the combined hashes of the leaf nodes, and so on.

🤔 How is each leaf node labeled in Merkle Tree?


Cryptographic hash of the data block

With the data in the node

With some part of data present in the node
🤔 How is each non-leaf node labeled in Merkle Tree?


With the data of the non-leaf node

Cryptographic hash of the non-leaf node's data

Cryptographic hash of its child nodes' values
As you keep doing this, eventually you will end up at the single top-level node, known as the Merkle Tree Root, this will come to play a very important role.

Simple Example
Let's say we have 4 transactions: "Transaction A", B, C and D. All of them are executed in the same block. Each of these transactions is going to get hashed. Let's call those hashes "Hash A", B, C, and D respectively.

The following would be the resulting Merkle Tree of these transactions:

Image

🤔 In case of Merkle Trees, what will be the label of parent node for C and D leaf nodes?

In case of Merkle Trees, what will be the label of parent node for C and D leaf nodes?

Hash CD

Hash C

Hash d
🤔 If node D is replaced with node E, what will be the label of the parent node now?

If node D is replaced with node E, what will be the label of the parent node now?

Hash CD - it will not change

Hash CE - it will change

Hash E - it will become equal to E
Verifying Validity using the Merkle Root
When these transactions get rolled up into a block, the block header would contain the Merkle Root, Hash ABCD. All miners have a copy of all transactions so far, and therefore all the transaction hashes. Any miner can rebuild the Merkle tree on-demand, which means that every miner can independently arrive at the same Merkle root for the same set of transactions.

This allows any miner to verify a fraudulent transaction. Let's say someone tries to introduce a false transaction instead of Transaction D. Let's call this Transaction E. Because this transaction is different from Transaction D, the hash is going to be different as well. The hash of Transaction E is Hash E. The Hash of C and E together is Hash CE, which is different from Hash CD. When Hash AB and CE are hashed together, you get Hash ABCE. Since hash ABCE is different from Hash ABCD, we can conclude that Transaction E is fraudulent.

Image

A miner can recompute the Merkle Root in their own block and try to publish that version to the blockchain, but since every other miner has a different Merkle Root, the fraudulent miner is easily rejected.

Hash Function
We have covered Hashing Functions before when talking about IPFS, but just to recap: to hash Transaction A into Hash A, a one-way cryptographic hash function is used. Once hashed, Hash A cannot be turned into Transaction A; the hash is not reversible.

Each blockchain uses different hash functions, but they all have the same properties in common.

Deterministic
The same input always has the same output when passed into a hashing function.

🤔 Can the same data give two different hashes in a Merkle Tree?


Yes

No
Computationally Efficient
Calculating the hash of an input value is fast.

Cannot be Reversed Engineered
Given a resulting hash, it is near impossible to determine the input. i.e. hash functions are one-way functions. For example: given y, it is difficult to find an x such that h(x) = y

Collision Resistant
Two different inputs never generate the same output.

🤔 Which one of the following is not a property of a hash function?


Collision Resistant

Can be reverse engineered

Computationally Efficient
Benefits of Merkle Trees in Blockchains
Merkle Trees allow for quick verification of data integrity.

The disk space used up is very little compared to the entire set of transactions. The Merkle Root is included in the block header for this reason.

If you have two different sets of transactions, verifying they are the same with a Merkle Tree is faster than verifying each and every single individual transaction to each other. One can verify that a block has not been modified by only knowing the Merkle Root.

🤔 What is one of the following is not a benefit of using a Merkle Tree?


Quick verification of data presence

Uses little disk space

Data is stored sequentially
Use cases outside of the blockchain
Merkle Trees aren't just used in blockchain applications. Some popular applications that use Merkle Trees are:

IPFS
Git
Distributed databases such as AWS DynamoDB and Apache Cassandra use Merkle trees to control discrepancies
🤔 Where are Merkle Trees not used?


IPFS

SQL Databases

Git
Verification of Presence in Merkle Trees
So, how do we actually verify that some data is part of a Merkle Tree?

You don't want the verifier to loop over every leaf node of the Merkle Tree, as it can be quite large, so how can we do this in a more efficient way?

Let's say the Verifier only has the Merkle Root r, that is, the top-level parent node of the tree. You, as a Prover, want to prove to the Verifier that some value K exists in the Merkle Tree.

To do this, you can generate a Merkle Proof. Let's try to understand what a Merkle Proof actually is with an example Merkle Tree.

Image(Referenced Merkle Proofs Explained)

The main idea is as follows: if you can give the Verifier the value of K, along with all the relevant nodes from the tree that get hashed up together to build up the r hash, the Verifier can compare the computed root value against r that they already have. If they are the same hash, it must mean that K was in fact present in the Merkle Tree, as you could not have generated the same Merkle Root hash with different input data.

In the diagram above, let's think about what info must be given to the Verifier that will positively prove to the Verifier that K is part of the Merkle Tree.

Value of K itself (so Verifier can compute H(K) on it's own)
H(L), so the verifier can compute H(KL)
H(IJ) so the verifier can compute H(IJKL)
H(MNOP) so the verifier can compute H(IJKLMNOP)
H(ABCDEFGH) so the verifier can compute H(ABCDEFGHIJKLMNOP)
Again it is important to remember that only one given combination of nodes can generate this unique root r because the Merkle tree is a collision-resistant hash function which means it is a hash function that given two inputs is almost impossible to produce the same output.

For our given example, we only need to provide the following nodes to be able to prove that H[K] actually exists in our nodes:Image

At this point, if the computed value of H(ABCDEFGHIJKLMNOP) matches the previously known value r that the Verifier had, it must be true that K existed in the Merkle Tree, or else the hashes wouldn't be the same.

This is significantly more efficient than looping over the entire Merkle Tree, as for a tree with n number of elements, you only have to provide roughly log(n) elements as part of the proof (one for each 'level' of the tree). This means if you had a LOT of data, Merkle Trees are wayyyyy more efficient than storing arrays or mappings.

🤔 How does a merkle proof work?


We give the verifier the value of the node we want to check along with all other nodes which are required from the tree to hash up and form the root hash r.

We give the verifier the value of the node we want to check and that is good enough to verify that the node exists in the tree

We dont have to give the verifier anything

it can automatically detect if a node exists in the tree
When ENS launched their token contract, they were airdropping the $ENS token to over 100,000 wallet addresses. They were able to deploy their contract, at a time of extremely high gas fees, for a MUCH lower price than what it would've been had they stored the wallet addresses in an array (where even storing a few hundred addresses could easily exceed gas limits of a block) - https://etherscan.io/tx/0xdfc76788b13ab1c033c7cd55fdb7a431b2bc8abe6b19ac9f7d22f4105bb43bff

Use Cases in Smart Contracts
Since the Verifier does not need to store the entire Merkle Tree to verify if something is a part of it, Merkle Trees actually come in quite handy for certain things.

In Sophomore, we created a Whitelist dApp that stored user addresses in a mapping. While that approach works, storing data in smart contract storage is by far the most expensive thing you can do in terms of gas. So what if you had to store 1000 addresses? What if 10,000? What about 100,000? 🤯

At that point, utilizing smart contract storage directly is just infeasible and can easily cost millions of dollars just to whitelist people. On the other hand, you could build up a Merkle Tree and just store the Merkle Root value in the contract - a measly bytes32 value. In this scenario, the contract is now the Verifier, and users who wish to use their whitelist spot for minting NFTs, let's say, become the Provers proving that they are indeed part of the whitelist. Let's see how this would work.
