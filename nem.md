# NEM

## Community fund proposal
similar to startup funding

## Technology
https://nem.io/wp-content/themes/nem/files/NEM_techRef.pdf

### Architecuture
* API Gateway
NEM’s blockchain exposes its functionality through a powerful API interface that can be used with any programming language, not a specific “smart contract” language. Existing business logic code can easily mate up and use blockchain where it’s strongest: secure transactions and ledger keeping.

* Custom
Unlike other blockchain technologies, NEM is built from the ground up with powerful modular customization for virtually any application. We call it our Smart Asset system. With it, NEM lets you focus on building exactly what you need, whether that’s a fintech system, tracking logistics, an ICO, document notarization, decentralized authentication, or much more.

* security is on application, not on smart contract
NEM’s architecture provides an incredibly secure and stable platform through its use of Eigentrust++ and an incentivized public node network based on its two-tier architecture. The significant risks inherent in on-blockchain “smart contracts” are eliminated by providing building block customization to NEM functionality that keeps application security in your hands, not on the blockchain.

### Spam guard

### Multisig 2.0

### P2P time sync

### Consensus mechanism
`Harvesting has several advantages over other types of methods.

It requires no special hardware.
It’s environmentally friendly and inexpensive since it uses no extra electricity.
Using the blockchain increases your chances to harvest a block.
You can keep harvesting while your computer is off.

Part of NEM’s founding vision was a system that would give power to users, not to hoarders or mining farmers. Keeping harvesting fair, transparent, and correctly incentivized is central to NEM’s purpose.

`

* Proof of Important
https://nem.io/NEM_techRef.pdf

- Vested staking
`
 POI only counts coins that have been in the account for a number of days.
 10% of the current unvested amount vests each day.
 The higher the number of vested coins, the higher the account’s POI score.
 It takes a minimum of 10,000 vested coins to start harvesting.
`

- Transaction Partner
`
POI rewards users who make transactions with others in the network.
This is part of a network theory calculation that looks at transaction behavior to assign each node an importance score.
Users can’t gain advantage by trading back and forth between a few accounts. The algorithm only counts net transfers over time.
`

- Number and Size of Transactions in the Last 30 days
`
Each transaction (above a minimum size) contributes to your POI score and increases the chances of harvesting a block and collecting its rewards.
Larger and more frequent transactions have a greater impact on the POI score.
This provides an incentive to use XEM as a currency and offsets NEM’s already low transaction fees.
`

* Delegated Harvesting

`NEM is the first blockchain to implement delegated harvesting. This is an efficient way to pool account power without exposing any private keys. To do this, you connect your account to an existing remote node and use that account’s computing power to complete blocks on your behalf.
Each account has a Proof of Importance (POI) score that determines its chances of harvesting a block. With delegated harvesting, you are lending your POI score to that remote node, increasing its chances of harvesting a block on your behalf.
`

## Bounties
https://nem.io/developers/developer-bounties/
https://nem.io/community/marketing-bounties/
