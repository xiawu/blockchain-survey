# TRON

## Reward
- Candidate reward(Vote Reward): 127 candidates updated once every 6 hours will share 115200 TRX. The reward will be split in accordance to the votes each candidate receives. Each year, Total reward of candidates will be 168192,000 TRX each year.

- Super Representative reward(Block Reward): The TRON Protocol network will generate one block every 3 seconds, with each block awarding 32 TRX to super representatives. A total of 336,384,000 TRX will be awarded annually to twenty-seven super representatives.

- There will be no inflation on the TRON network before January 1, 2021, and the TRON Foundation will award all block rewards and candidate rewards prior to that date.

## Committee
Each SR has the right to propose and vote on proposals. When a proposal receives 19 votes or more, it is approved and the new network parameters will be applied in the next maintenance period (3 days).

### Dynamic network parameters
- MAINTENANCE_TIME_INTERVAL
- ACCOUNT_UPGRADE_COST: Modify the cost of applying for SR account.
- CREATE_ACCOUNT_FEE: Modify the account creation fee. 
- TRANSACTION_FEE
- ASSET_ISSUE_FEE: 1024 TRX
- WITNESS_PAY_PER_BLOCK
- WITNESS_STANDBY_ALLOWANCE
- CREATE_NEW_ACCOUNT_FEE_IN_SYSTEM_CONTRACT
- CREATE_NEW_ACCOUNT_BANDWIDTH_RATE
- ALLOW_CREATION_OF_CONTRACTS
- REMOVE_THE_POWER_OF_THE_GR
- EXCHANGE_CREATE_FEE
- MAX_CPU_TIME_OF_ONE_TX

## TRX

TRX is the name of the currency used in the TRON network.
* Vote
  * TRX can be used to vote for super representatives and obtain bandwidth.
* Freezing
  * Freezing the TRX balance in a wallet gives the user TRON Power (TP), which is used to vote for Super Representatives (SRs).
* sun
  * The smallest denomination of TRX is sun, so 1 TRX equals 1,000,000 sun.

`To keep the network operating smoothly, TRON network only allows every account to initiate a transaction for free once every 10 seconds.`

## Architecture

### Storage Layer

* block storage and state storage
* graph database

### Core Layer

* smart contract module
  * Java
* account management module
* consensus module

### Application Layer

* Google Protobuf

### Node Types

* Super Representative Witness (SR Full Node)
  * block production
* Full Node
  * APIs
  * broadcast transactions and blocks
* Solidity Node
  * synchronize irrevocable blocks
  * provide inquiry APIs 

## TRON Protocol

[TRON Protobuf protocol](TRON_Protobuf_Protocol_document.md)

### Account

* TRON uses an account model
* Tron's signature algorithm is ECDSA, and the curve used is SECP256K1.

### Resource Model

TRON network has 4 types of resources:
* Bandwidth
* CPU
* Storage
* RAM

TRON network imports two resource conceptions: 
Bandwidth points and Energy.

* Bandwidth points
  * Except for query operation, any transaction consumes Bandwidth points.
  * By Freezing TRX to get Bandwidth Points
  * Every account has a fixed amount of free Bandwidth Points(5000) every day
* Energy
  * Each command of smart contract consume system resource while running, we use 'Energy' as the unit of the consumption of the resource.
  * Freeze TRX to get energy

### Rewards Calculation

* Super representative brokerage
  * The default ratio is 20%, which can be modified by the super representative
* Vote Rewards
  * Vote rewards are 160 TRX every block
* Block Rewards
  * Block rewards are 16 TRX every block, and SRs create blocks one by one
* Voter Rewards

## Transactions

### Transaction Contract Types
 
As the basic transaction unit on the TRON network, contracts exist in different forms. 

[Transaction List](https://github.com/tronprotocol/java-tron/blob/develop/protocol/src/main/protos/core/Tron.proto#L241)

* Use gRPC
* Ease to add transaction type
  * [Multi Signature](https://developers.tron.network/docs/multi-signature)



