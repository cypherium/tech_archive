# Cypherium Data Structures

Description of the core data structures of the Cypherium blockchain

## Hash
Cypherium uses double SHA-3 hash

## Variable length data structure
[Ethereum RLP](https://github.com/ethereum/wiki/wiki/RLP)

## Account
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
Deposit         |8             |
Code            |Variable      |Contract code
Storage         |Variable      |The persistent storage field of the contract

## Microblocks
Microblock header contains `Version`, `MbklHash`, `Timestamp`, `MklRoot`

### Genesis Microblock (within the term of a validation committee)
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Type            |1             |Type of the microblock (genesis, ordinary, final)
KblkHash        |32            |Hash of its keyblock header
Timestamp       |8             |Unix time
MklRoot         |32            |Merkle root
TxCnt           |8             |Number of transactions
Coinbase        |32            |Reward for the miners of the previous keyblock
Tx              |Variable      |Transactions

### Ordinary Microblock
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Type            |1             |Type of the microblock (genesis, ordinary, final)
MblkHash        |32            |Hash of its predecessor microblock header
Timestamp       |8             |Unix time
MklRoot         |32            |Merkle root
TxCnt           |8             |Number of transactions
Tx              |Variable      |Transactions

### Final Microblock
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Type            |1             |Type of the microblock (genesis, ordinary, final)
MblkHash        |32            |Hash of its predecessor microblock header
Timestamp       |8             |Unix time
MblkCosi        |64            |Signature of the current validator committee
MklRoot         |32            |Merkle root
TxCnt           |8             |Number of transactions
Tx              |Variable      |Transactions

### Coinbase
Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
NumCommittee    |8             |Number of recipients
Committee       |Variable      |List of address of the recipients of the coinbase reward
Quantity        |8             |Amount to be sent

### Transaction
`SenderSig` is the signature on `Version`, `Recipient` and `Quantity` hased with 
`Sender`. This is to ensure the authenticity of the `Sender`.

Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Sender          |20            |Sender's address
SenderSig       |32            |Sender's Signature
Recipient       |20            |Recipient of the transaction
Quantity        |8             |Amount to be sent
Data            |Variable      |Optional data field (in case of smart contract)
AvailGas        |8             |Available gas 
GasPrice        |8             |Gasprice

## Keyblocks

### Keyblock
The actual block strcture on the election chain

Its header contains the field `Version`, `Timestamp`, `PredecessorHash`, `MklRoot` 
and `KblCosi`.

The PoWs stored in the field `Pows` are stripped away from the field 
`PredecessorHash` since it is part of the Keyblock header.

Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
Version         |4             |Current version
Timestamp       |8             |Unix time
PredecessorHash |32            |Hash of its predecessor keyblock header 
MklRoot         |32            |Merkle root of the PoWs
KblkCosi        |64            |Cosi of the current keyblock
NumPow          |8             |Number of PoWs/Size of the next committee
Pows            |Variable      |Array of PoWs

### PoW (Proof-of-Work)
PoW header includes the field `Worker` and `Timestamp` and `Difficulty`
and `PredecessorHash`.

The service string thus becomes the POW header. With the `nonce` field 
the validator committee is able to verify the PoW of this particular miner.

Field           |Length (Bytes)|Description
:--------------:|:------------:|------------------
PredecessorHash |32            |Hash of its predecessor keyblock  header 
Difficulty      |8             |
Timestamp       |8             |Unix time
Worker          |20            |Miner's address
Nonce           |8             |A 64-bit nonce
