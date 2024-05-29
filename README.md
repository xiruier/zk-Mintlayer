# zk-Mintlayer
zk-evm layer3 built on Mintlayer

## The requirements of the ZK Thunder network

MintLayer serves as the DA layer of zk Thunder network.
The main programming language is rust.
Select zkSync EVM as our zkEVM.


Submit zk proof of zkEVM rollup to MintLayer for verification resolution. Add a verification function for zkEVM proof submission in mintlayer-core/script/src/script.rs or mintlayer-core/script/src/interpreter.rs. Return the boolean value of the verification to zkEVM internally, and when the return value is true, the block within zkEVM is finalized.

As mentioned earlier, in the EVM, the actual data storage includes: stack, memory, calldata, storage, code (at a specific address), log (triggering events). Storage is the most expensive of the above storage data, but it's the only one that's permanent. All other storage structures, like stack, memory, calldata, are cleared after a function call is completed; they are temporary storage locations during transaction processing.

Calldata can also store arbitrary data  bytes, but unlike others, it can only be read from this area, not written to. So, reading and accessing data from calldata is much cheaper. For some data that needs to be frequently read but not modified, it's better to store it in calldata rather than memory. If a function's parameter doesn't need to modify memory parameters, it can be defined as calldata.

When using Ethereum as a data availability layer, ZK-rollups write transactions to Ethereum as calldata. Calldata is where data is stored for external calls to smart contract functions. The information in calldata is published on the blockchain, allowing anyone to independently reconstruct the rollup state. Data in calldata is not considered part of the Ethereum state but more like a log, so the storage cost is lower.

When integrating MintLayer L2 as the zkEVM L3's DA layer, we aim to have a spot to store data similar to calldata. This way, we can write zkEVM L3 transaction data into MintLayer2 using it. Additionally, we can call the location where the data is stored externally through script-based smart contracts. Furthermore, these stored data can be published on the blockchain, allowing individuals to independently reconstruct the zkEVM L3 state. This data, like that stored in calldata, won't become a part of the MintLayer L2 state but more like keeping a diary, with relatively low storage costs.

On MintLayer, we are shifting our focus to scripts and script opcodes. We are aware that by using these special script opcodes like OP_RETURN, 
we can fill arbitrary data unrelated to MintLayer L2 state and MintLayer L2 transaction block, essentially creating a super-sized decomposed database.

With the Taproot upgrade, in order to support Schnorr Signatures, the upper limit on Bitcoin script size has been removed. 
As long as the transaction size is smaller than the MintLayer L2 block size, 
we can store zkEVM L3 block transaction data that we want to keep without affecting the original state validation in the MintLayer script, 
achieving a similar effect to OP_RETURN.

