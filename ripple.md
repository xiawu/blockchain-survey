# Ripple

## TPS: 1500
## block time: 4-5 seconds
## open network decision: https://xrpl.org/blog/2017/decent-strategy-update.html
* orginal node is 25 validators controlled by ripple company.

## Make money
### How to be a ripple validator?
https://xrpl.org/manage-the-rippled-server.html#reasons-to-run-a-validator
https://xrpl.org/run-rippled-as-a-validator.html

* register by fill the google form
https://docs.google.com/forms/d/e/1FAIpQLScszfq7rRLAfArSZtvitCyl-VFA9cNcdnXLFjURsdCQ3gHW7w/viewform

* node types
- validator node
Participate in consensus.
Vote on fees and amendments.

- stock node
Store ledger history.
Allow API calls.
Protect validating nodes.

* storage
- 9TB

* How to be a good validator
- Available
A good validator is always running and submitting validation votes for every proposed ledger. Strive for 100% uptime.

- In agreement
A good validator's votes match the outcome of the consensus process as often as possible. To do otherwise could indicate that your validator's software is outdated, buggy, or intentionally biased. Always run the latest rippled release without modifications. Watch rippled releases  to be notified of new releases.

- Issuing timely votes
A good validator's votes arrive quickly and not after a consensus round has already finished. To keep your votes timely, make sure your validator meets the recommended system requirements, which include a fast internet connection.

- Identified
A good validator has a clearly identified owner. Providing domain verification is a good start. Ideally, XRP Ledger network UNLs include validators operated by different owners in multiple legal jurisdictions and geographic areas. This reduces the chance that any localized events could interfere with the impartial operations of trusted validators.


### XRP ledger gateway
* Need to contact the ripple company by email
* include enterprise features
* Withdrawal and Deposit fees
* Transfer fees
* Interest on XRP Ledger-backed funds
* financial exchanges
* i think similar to HEP




## consensus algorithm: trusted validators
https://xrpl.org/consensus.html
`
Each new "ledger version" in the XRP Ledger (the equivalent of a "block") contains the full current state of all balances, so a server can synchronize with the network in minutes instead of spending hours downloading and re-processing the full transaction history.
`
* Transaction expired
* validate process
*** Canonical order: https://github.com/ripple/rippled/blob/8429dd67e60ba360da591bfa905b58a35638fda1/src/ripple/app/misc/CanonicalTXSet.cpp#L25-L36

*** Larger than 80% trusted validators got same hash, the new version ledger version validated.
`Servers in the network recognize a ledger instance as validated when a supermajority of the peers have signed and broadcast the same validation hash. Going forward, transactions are applied to this updated and now validated ledger with sequence number N+1.
`
## Freeze non-XRP currencies
### Individual freeze
### Global freeze
### No freeze
## censorship
`
The XRP Ledger's system of trusted validators uses a small amount of human interaction to achieve better distribution of authority than other decentralized systems.
`
## Supply
* Each account in the XRP Ledger must hold a small amount of XRP in reserve.
* escrow by ripple company.
- About  how to manage the escrow account for XRP supply https://ripple.com/insights/ripple-to-place-55-billion-xrp-in-escrow-to-ensure-certainty-into-total-xrp-supply/
*** establish 55 contracts of 1 billion XRP expired month from 0 to 55
*** XRP’s rate of supply assuming 50% is used per month.
* Distribution
- TOTAL XRP HELD BY RIPPLE 6,524,991,927
- TOTAL XRP DISTRIBUTED 42,566,596,173*
- TOTAL XRP PLACED IN ESCROW 50,900,000,000
* Ripple has sold on average 300M XRP per month for the past 18 months.

## Fee

* Neutral Fees
- reserve requirement (50XRP -> 20XRP)
- transaction cost

* Optional Fees
- Transfer fees for issued currencies
- Trust line quality



## Technology
* Escrow
https://xrpl.org/escrow.html
- Escrow is designed as a feature to enable the XRP Ledger to be used in the Interledger Protocol  and with other smart contracts.
- Combination Escrow
- Time-based Escrow
- Conditional Escrow

* Payment Channels
https://xrpl.org/use-payment-channels.html
Payment Channels are an advanced feature for sending "asynchronous" XRP payments that can be divided into very small increments and settled later.


* Cryptographic keys
- secp256k1
- similar to bitcoin: https://xrpl.org/accounts.html#address-encoding
- support multiple signatuer algorithm

* Amendments
`The Amendments system provides a means of introducing new features to the decentralized XRP Ledger network without causing disruptions. The amendments system works by utilizing the core consensus process of the network to approve any changes by showing continuous support before those changes go into effect. An amendment normally requires 80% support for two weeks before it can apply.
`
* Fee Voting

* Deposit Authorization
`Deposit Authorization is an optional feature of an account in the XRP Ledger. With Deposit Authorization enabled, transactions cannot send value of any kind to the account unless the sender of those transactions is the account itself. This includes transfers of XRP and issued currencies.
`
* DEX
** Cross-Currency Payments
`In the XRP Ledger, you can send cross-currency payments that exchange one or more issued currencies, XRP, or both. Like direct XRP payments, these payments use the Payment transaction type. Cross-currency payments within the XRP Ledger are fully atomic, meaning that either the payment fully executes or no part of it executes.By default, cross-currency payments deliver a fixed amount to their destination at a variable cost to their source. Cross-currency payments can also be partial payments, which deliver a variable amount to the destination within a fixed sending limit.
`
** Auto-Bridging
`https://xrpl.org/blog/2014/introducing-offer-autobridging.html`

* Complex Payment Types

* Research
- https://arxiv.org/abs/1802.07240

* Use Cases
- Open a Payment Channel to Enable an Inter-Exchange Network
https://xrpl.org/open-a-payment-channel-to-enable-an-inter-exchange-network.html



