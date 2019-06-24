# An Efficient Unidirectional Micropayment Channel on Ethereum
This is the accompanying code for the article "An Efficient Unidirectional Micropayment Channel on Ethereum". It consists of:

 -   Three Solidity contracts: $\texttt{EthWord}, \texttt{Pay50}, \texttt{PayMerkle}$.
-   Helper scripts in order to test $\texttt{PayMerkle}$ contract.
## Test Case
### requirements
 1. [Nodejs](https://nodejs.org/en/)
 2. [Web3](https://www.npmjs.com/package/web3)
 3. [ethereumjs-util](https://www.npmjs.com/package/ethereumjs-util)
### Senario
Assume that Alice runs an online service for which she accepts (Ether) payments on a micro-level (i.e., a very low fraction of
Ether that costs less than one dollar). Bob is interested in that service and he wants to utilize it.
#### Channel Setup
Suppose that Bob estimates the number of maximum payments he is willing to make is `4096` with a minimum unit of payments of `1 Wei`. He create a Merkle Tree with number of leaves equal to `channelCapacity = 4096` by running  the following code on Nodejs console.

   ```js
const { MerkleTree } = require('../helpers/merkletree.js');
const Web3 = require('web3');
const { keccak256, bufferToHex } = require('ethereumjs-util');
const crypto = require('crypto');   
const web3 = new Web3(new Web3.providers.HttpProvider('http://127.0.0.1:8545'));
//Create MerkleTree
const channelCapacity = 4096; 
const leaves =[];
const seed = crypto.randomBytes(256);
let rn = keccak256(seed);
for(i = channelCapacity; i > 0; i--){
        let a = web3.eth.abi.encodeParameter('uint256',web3.utils.toWei(`${i}`, "wei"));
        let b = bufferToHex(rn);
        let ab = a + b.slice(2);
        leaves.unshift(ab);//push at the beginning of the array 
        rn = keccak256(rn);
}
const merkleTree = new MerkleTree(leaves);
const treeRoot = merkleTree.getHexRoot();
console.log("seed : " + bufferToHex(seed));
console.log("Tree Root : " + treeRoot);
```
### Channel Deployment
Bob deploys a smart contract $SC$ on Ethereum to act as a trusted third party that holds Bob's balance and settle the final payment to Alice. To deploy $SC$, Bob has to specify the following parameters to $SC$'s constructor that controls the payment channel between him and Alice:
    Alice's address $A_{adr}$ on Ethereum.}
    \item{A timeout value $T_{out}$ before the channel is closed.}
    \item{The root $r$ of the Merkle tree $MT$.}
\end{enumerate}
Additionally, Bob has to send an amount $balance = n \times u$  \texttt{ether} to $SC$ to be held in escrow and pay Alice when she submits a valid Merkle proof.

### close
```js
const claimedValue = 1000;
const claimedLeaf = leaves[claimedValue - 1];
const proof = merkleTree.getHexProof(claimedLeaf);
const claimedLeaf.slice(0,66)
const '0x'+claimedLeaf.slice(66)
```


