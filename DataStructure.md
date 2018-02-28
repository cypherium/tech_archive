# Cypherium Data Structures

Description of the core data strctures of the Cypherium blockchain

## Hash
Cypherium uses double SHA-3 hash

## Variable length data structure
[Ethereum RLP](https://github.com/ethereum/wiki/wiki/RLP)

## Address
The address is similar to that in Bitcoin. The public address will be generated 
using the Ed25519 algorithm and be applied with RIPE message digest to form a 
160-bit (20 bytes) address. 

There are two types of addresses:
1. `Volatile` are the addresses generated for transactions and is intended for one-time use
2. `Static` are the addresses associated with a contract and cannot be replaced

The `Latency` provides a mechanism to recall a already-sent transaction. If the `Latency` field
of an address is larger than 0, the sender is able to recall the transaction within the number 
of hours specified by the `Latency` field.

Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Type            |1             |Address type 
Address         |20            |Ed25519 + RIPEMD-160
Latency         |2             |Transaction latency (in hours)


## Contract related
Once a contract and its code is established they stay as is for a fixed size 
whereas the contract's `storage` increases overtime. Therefore, extra care should 
be taken for the physical `storage` of the Contract. 

### Contract
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Owner           |20            |The address associated with the contract
Code            |Variable      |Contract code
Storage         |Variable      |The persistent storage field of the contract


## Microblocks
Microblock header contains `Version`, `KblkCosi`/`MbklHash`, `Timestamp`, `MklRoot`

### Genesis Microblock
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
KblkCosi        |32            |Its key superblock cosi signature
Coinbase        |32            |Reward for the miner of the previous keyblock
Timestamp       |8             |Unix time
MklRoot         |32            |Merkle root
TxCnt           |8             |Number of transactions
Tx              |Variable      |Transactions

### Ordinary Microblock
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
MblkHash        |32            |Hash of its predecessor microblock header
Timestamp       |8             |Unix time
MklRoot         |32            |Merkle root
TxCnt           |8             |Number of transactions
Tx              |Variable      |Transactions

### Final Microblock
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
MblkHash        |32            |Hash of its predecessor microblock header
Timestamp       |8             |Unix time
MklRoot         |32            |Merkle root
MblkCosi        |64            |Signature of the current validator committee
TxCnt           |8             |Number of transactions
Tx              |Variable      |Transactions

### Coinbase
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
NumCommittee    |8             |Number of recipients
Committee       |Variable      |List of address of the recipients of the coinbase reward
Amount          |8             |Amount to be sent

### Transaction
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Sender          |20            |Sender's address
SenderSig       |32            |Sender's Signature
Recipient       |20            |Recipient of the transaction
Amount          |8             |Amount to be sent
Data            |Variable      |Optional data field (in case of smart contract)
AvailGas        |8             |
GasPrice        |8             |Gasprice

## Keyblocks

### Key Superblock
The actual block strcture on the election chain

Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
PredecessorCosi |32            |Its predecessor key superblock's cosi signature
Timestamp       |8             |Unix time
MklRoot         |32            |Merkle root of the keyblocks
NumKblk         |8             |Number of keyblocks
Kblks           |Variable      |Array of keyblocks
KblkCosi        |64            |Cosi of the current keyblock

### Keyblock
Keyblock header includes the field `Worker` and `Timestamp` and `Difficulty`
The service string thus becomes the keyblock header. With the `nounce` field 
the validator committee is able to verify the PoW of this particular miner.

Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Worker          |20            |Miner's address
Difficulty      |8             |
Timestamp       |8             |Unix time
Nounce          |8             |A 64-bit nounce
