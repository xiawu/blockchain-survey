# Storage system

## Swarm 
https://swarm.ethereum.org/
https://github.com/ethersphere/swarm/

Swarm is a distributed storage platform and content distribution network, a native base layer service of the Ethereum web3 stack.

The primary objective of Swarm is to provide a sufficiently decentralized and redundant store of Ethereum’s public record, in particular to store and distribute dapp code and data as well as blockchain data. From an economic point of view, it allows participants to efficiently pool their storage and bandwidth resources in order to provide these services to all participants of the network, all while being incentivised by Ethereum.

`Uploaded content is not guaranteed to persist on the testnet until storage insurance is implemented` (see Roadmap for more details). All participating nodes should consider participation a voluntary service with no formal obligation whatsoever and should be expected to delete content at their will. Therefore, users should under no circumstances regard Swarm as safe storage until the incentive system is functional.



### developer
- Leader: https://www.linkedin.com/in/viktortron

### Sponsors and collaborators
http://status.im
http://livepeer.org
http://jaak.io
http://datafund.io
http://mainframe.com
http://wolk.com
http://riat.at
http://datafund.org
http://216.com
http://cofound.it
http://iconomi.net
http://infura.io
http://epiclabs.io
http://asseth.fr

### Architecture
https://swarm-guide.readthedocs.io/en/latest/architecture.html#architecture

#### Overlay network

##### Logarithmic distance
![swarm xor](swarm-logarithmic-distance.svg)

##### Topology
![swarm topology](swarm-topology.svg)

![swarm kedemlia table](swarm-kademlia-table.svg)

##### Bootstrapping and discovery
The protocol is as follows: Initially, each node has zero as their saturation depth. Nodes keep advertising to their connected peers info about their saturation depth as it changes. If a node establishes a new connection, it notifies each of its peers about this new connection if their proximity order relative to the respective peer is not lower than the peer’s advertised saturation depth (i.e., if they are sufficiently close by). The notification is always sent to each peer that shares a PO bin with the new connection. These notification about connected peers contain full overlay and underlay address information. Light nodes that do not wish to relay messages and do not aspire to build up a healthy kademlia are discounted.

As a node is being notified of new peer addresses, it stores them in a kademlia table of known peers. While it listens to incoming connections, it also proactively attempts to connect to nodes in order to achieve saturation: it tries to connect to each known node that is within the PO boundary of N nearest neighbours called nearest neighbour depth and (2) it tries to fill each bin up to the nearest neighbour depth with healthy peers. To satisfy (1) most efficiently, it attempts to connect to the peer that is most needed at any point in time. Low (far) bins are more important to fill than high (near) ones since they handle more volume. Filling an empty bin with one peer is more important than adding a new peer to a non-empty bin, since it leads to a saturated kademlia earlier. Therefore the protocol uses a bottom-up, depth-first strategy to choose a peer to connect to. Nodes that are tried but failed to get connected are retried with an exponential backoff (i.e., after a time interval that doubles after each attempt). After a certain number of attempts such nodes are no longer considered.

After a sufficient number of nodes are connected, a bin becomes saturated, and the bin saturation depth can increase. Nodes keep advertising their current saturation depth to their peers if it changes. As their saturation depth increases, nodes will get notified of fewer and fewer new peers (since they already know their neighbourhood). Once the node finds all their nearest neighbours and has saturated all the bins, no new peers are expected. For this reason, a node can conclude a saturated kademlia state if it receives no new peers (for some time). The node does not need to know the number of nodes in the network. In fact, some time after the node stops receiving new peer addresses, the node can effectively estimate the size of the network from the depth (depth n implies 2n nodes)

Such a network can readily be used for a forwarding-style messaging system. Swarm’s PSS is based on this. Swarm also uses this network to implement its storage solution.

#### Distributed preimage archive
![swarm DPA](swarm-dpa-chunking.svg)

A DPA is opinionated about which nodes store what content and this implies a few more restrictions:
(1) load balancing of content among nodes is required and is accomplished by splitting content into equal sized chunks (chunking);
(2) there has to be a process whereby chunks get to where they are supposed to be stored (syncing); and
(3) since nodes do not have a say in what they store, measures of plausible deniability should be employed.

##### Redundancy
The area of the fully connected neighbourhood defines an area of responsibility.
A storer node is responsible for (storing) a chunk if the chunk falls within the node’s area of responsibility. Let us assume, then,
(1) a forwarding strategy that relays requests along stable nodes and
(2) a storage strategy that each node in the nearest neighbourhood (of mimimum R peers) stores all chunks within the area of responsibility. As long as these assumptions hold, each chunk is retrievable even if R−1 storer nodes drop offline simultaneously.
As for (2), we still need to assume that every node in the nearest neighbour set can store each chunk.

Further measures of redundancy, e.g. (Erasure coding)[https://en.wikipedia.org/wiki/Erasure_code], will be implemented in the future.

##### Caching and purging Storage
What a node stores is determined by the access count of chunks: if we reach the capacity limit for storage the oldest unaccessed chunks are removed.
On the one hand, this is backed by an incentive system rewarding serving chunks. This directly translates to a motivation, that a content needs to be served with frequency X in order to make storing it profitable.
On the one hand , frequency of access directly translates to storage count.
On the other hand, it provides a way to combine proximity and popularity to dictate what is stored.

This storage protocol is designed to result in an autoscaling elastic cloud where a growth in popularity automatically scales. An order of magnitude increase in popularity will result in an order of magnitude more nodes actually caching the chunk resulting in fewer hops to route the chunk, ie., a lower latency retrieval.

##### Synchronisation
Smart synchronisation is a protocol of distribution which makes sure that these transfers happen.

Apart from access count which nodes use to determine which content to delete if capacity limit is reached, `chunks also store their first entry index`. This is an arbitrary monotonically increasing index, and nodes publish their current top index, so `virtually they serve as timestamps of creation`. This index helps keeping track what content to synchronise with a peer.

![swarm syncing](swarm-syncing-high-level.svg)
 
#### Data layer
There are 4 different layers of data units relevant to Swarm:

* message: p2p RLPx network layer. Messages are relevant for the devp2p wire protocols

* chunk: fixed size data unit of storage in the distributed preimage archive

* file: the smallest unit that is associated with a mime-type and not guaranteed to have integrity unless it is complete. This is the smallest unit semantic to the user, basically a file on a filesystem.

* collection: a mapping of paths to files is represented by the swarm manifest.
This layer has a mapping to file system directory tree. Given trivial routing conventions, a url can be mapped to files in a standardised way, allowing manifests to mimic site maps/routing tables. As a result, Swarm is able to act as a webserver, a virtual cloud hosting service.

The actual storage layer of Swarm consists of two main components, the localstore and the netstore.

The local store consists of an in-memory fast cache (memory store) and a persistent disk storage (dbstore).

The NetStore is extending local store to a distributed storage of Swarm and implements the distributed preimage archive (DPA).

![swarm storage layer](swarm-storage-layer.svg)

##### Files
The component that chunks the files into the merkle tree is called the chunker.
Our chunker implements the bzzhash algorithm which is parallellized tree hash based on an arbitrary chunk hash.

##### Manifests

#### Components
![swarm-high-level-components](swarm-high-level-components.svg)

### Technology
* BZZ - Scalable distributed storage solution and content distribution network
ENS is the system that Swarm uses to permit content to be referred to by a human-readable name, such as “theswarm.eth”. It operates analogously to the DNS system, translating human-readable names into machine identifiers - in this case, the Swarm hash of the content you’re referring to. By registering a name and setting it to resolve to the content hash of the root manifest of your site, users can access your site via a URL such as bzz://theswarm.eth/.
  - ENS video: https://youtu.be/pLDDbCZXvTE
  - Register domain name: https://manager.ens.domains/


* ACT - Access control and Authentication
Swarm supports restricting access to content through several access control strategies:
- Password protection - where a number of undisclosed parties can access content using a shared secret (pass, act)
- Selective access using Elliptic Curve key-pairs:
  - For an undisclosed party - where only one grantee can access the content (pk)
  - For a number of undisclosed parties - where every grantee can access the content (act)


* PSS - Secure real-time node-to-node push messaging
pss (Postal Service over Swarm) is a messaging protocol over Swarm with strong privacy features. The pss API is exposed through a JSON RPC interface described in the API Reference, here we explain the basic concepts and features.


* Feeds - Asynchronous pull messaging
If you are using Feeds in conjunction with an ENS resolver contract, only one initial transaction to register the “Feed manifest address” will be necessary. This key will resolve to the latest version of the Feed (updating the Feed will not change the key).

If you are using Feeds in conjunction with an ENS resolver contract, only one initial transaction to register the “Feed manifest address” will be necessary. This key will resolve to the latest version of the Feed (updating the Feed will not change the key).

  - Manifests
  In general manifests declare a list of strings associated with Swarm hashes. A manifest matches to exactly one hash, and it consists of a list of entries declaring the content which can be retrieved through that hash. 

  - Encryption
  Swarm uses (Counter mode encryption)[https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Counter_(CTR)] to encrypt and decrypt content. When you upload content to Swarm, the uploaded data is split into 4 KB chunks. These chunks will all be encoded with a separate randomly generated encryption key. The encryption happens on your local Swarm node, unencrypted data is not shared with other nodes. The reference of a single chunk (and the whole content) will be the concatenation of the hash of encoded data and the decryption key. This means the reference will be longer than the standard unencrypted Swarm reference (64 bytes instead of 32 bytes).

When your node syncs the encrypted chunks of your content with other nodes, it does not share the full references (or the decryption keys in any way) with the other nodes. This means that other nodes will not be able to access your original data, moreover they will not be able to detect whether the synchronized chunks are encrypted or not.

When your data is retrieved it will only get decrypted on your local Swarm node. During the whole retrieval process the chunks traverse the network in their encrypted form, and none of the participating peers are able to decrypt them. They are only decrypted and assembled on the Swarm node you use for the download.

    - Counter mode encryption
    https://zh.wikipedia.org/wiki/%E5%88%86%E7%BB%84%E5%AF%86%E7%A0%81%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F
    密码学中，分组（block）密码的工作模式（mode of operation）允许使用同一个分组密码密钥对多于一块的数据进行加密，并保证其安全性。[1][2] 分组密码自身只能加密长度等于密码分组长度的单块数据，若要加密变长数据，则数据必须先被划分为一些单独的密码块。通常而言，最后一块数据也需要使用合适填充方式将数据扩展到匹配密码块大小的长度。一种工作模式描述了加密每一数据块的过程，并常常使用基于一个通常称为初始化向量的附加输入值以进行随机化，以保证安全[1]。
    工作模式主要用来进行加密和认证。[1][3] 对加密模式的研究曾经包含数据的完整性保护，即在某些数据被修改后的情况下密码的误差传播特性。后来的研究则将完整性保护作为另一个完全不同的，与加密无关的密码学目标。部分现代的工作模式用有效的方法将加密和认证结合起来，称为认证加密模式。      虽然工作模式通常应用于对称加密[2]，它亦可以应用于公钥加密，例如在原理上对RSA进行处理，但在实用中，公钥密码学通常不用于加密较长的信息，而是使用结合对称加密和公钥加密的混合加密方案[1]。
    

* SW3 (Swap, Swear and Swindle) - Accounting and accountability

* Swarm DB - Trustless database services


## Storj

### Status
* Release Tardigrade.io - Decentralized cloud storage

* With Tardigrade cloud storage, your files are encrypted and split into pieces client-side before being distributed across our network of high-performance storage nodes.

* Performance
With multi-threaded, concurrent downloads you can get your files 20% faster than Amazon S3.

* Security & Privacy
No one can compromise or view your data without your permission. Client-side AES-256-GCM encryption is standard on every file.

* Durability
`Reed-Solomon erasure coding` enables the highest levels of durability for all files uploaded to the Tardigrade network.

* Open Source
Audit our code, read our white paper, and know exactly how everything works. Check us out on GitHub.

* Affordable
Predictable, flat pricing. S3-compatible object storage that is a fraction of the price.

* Easy to Use
With our simple s3-compatible library, you can use a single function for uploading and downloading.

* waitlist
The first 10,000 developers on the Tardigrade.io waitlist will get 1TB free for their first 30 days after production launch.

* Become a Storage Node Operator
Nodes can begin earning STORJ tokens and building node reputation ahead of our beta (Pioneer release) and production (Voyager release) launches coming later this year.

`
Revenue caculations: https://storj.io/storage-node-estimator/
Revenue by Storj token equal to $10 per TB per month
`

* The Open Source Partner Program
With this program, every time one of our OS approved partners has a user that stores data on Tardigrade using our connectors, we will share a generous portion of every dollar earned with our open-source partners.
Forever. . .

* Innovations in Token Governance
https://storj.io/blog/2018/12/an-overview-of-tokens-uses-flows-and-policies-at-storj-labs/
  - The STORJ token is used to provide incentives for storage node operators to contribute stable, performant, long-term storage and bandwidth to the network
  - The STORJ token provides users with an effective and efficient option for buying storage and bandwidth. While we quote prices today in dollars, users can pay in STORJ token or fiat.
  - The STORJ token enables Storj Labs to efficiently and effectively conduct the network. Specifically, it enables us to compensate large numbers of operators (there were over 70,000 operators in the V2 network) in large numbers of countries (over 180 countries and territories in V2). The token also enables programmatic payments (e.g. via smart contracts), and micropayments.
  - STORJ token is used to provide demand partners (e.g. the many members of our Open Source Partner Program) with incentives for driving demand to the network.
  - STORJ token serves as a medium of exchange for Satellite operators within the Storj Labs “Tardigrade” branded and curated storage network.
  - STORJ token can serve as a medium of exchange in completely decentralized, non-Tardigrade networks.


### Storage Nodes
Storage nodes are selected to store data based on various criteria: ping time, latency, throughput, bandwidth caps, sufficient disk space, geographic location, uptime, history of responding accurately to audits, and so forth. In return for their service, nodes are paid.

Because storage nodes are selected via changing variables external to the protocol, node selection is an explicit, non-deterministic process in our framework. This means that we must keep track of which nodes were selected for each upload via a small amount of metadata; we can’t select nodes for storing data implicitly or deterministically as in a system like Dynamo [25]. As with GFS [26], HDFS [27], or Lustre [28], this decision implies the requirement of a metadata storage system to keep track of selected nodes.

### Uplink
This peer class represents any application or service that implements libuplink and wants to store and/or retrieve data.
This peer class performs encryption, erasure encoding, and coordinates with the other peer classes on behalf of the customer/client.

### Satellite
This peer class participates in the node discovery system, caches node address information, stores per-object metadata, maintains storage node reputation, aggre-gates billing data, pays storage nodes, performs audits and repair, and manages authorization and user accounts.
The Satellite instance is made up of these components:
* A full node discovery cache
* A per-object metadata database indexed by encrypted path
* An account management and authorization system
* A storage node reputation, statistics, and auditing system
* A data repair service
* A storage node payment service

### Authorization
Our initial metadata authorization scheme uses macaroons.
We use macaroons to restrict which operations can be applied and to which encrypted paths they can be applied

* What are macaroons?
You can think of a macaroon as a cookie, in a way. Cookies are small bits of data that your browser stores and sends to a particular website when it makes a request to that website. If you're logged into a website, that cookie can store a session ID, which the site can look up in its own database to check who you are and give you the appropriate content.

A macaroon is similar: it's a small bit of data that a client (like lncli) can send to a service (like lnd) to assert that it's allowed to perform an action. The service looks up the macaroon ID and verifies that the macaroon was initially signed with the service's root key. However, unlike a cookie, you can delegate a macaroon, or create a version of it that has more limited capabilities, and then send it to someone else to use.

Just like a cookie, a macaroon should be sent over a secure channel (such as a TLS-encrypted connection), which is why we've also begun enforcing TLS for RPC requests in this release. Before SSL was enforced on websites such as Facebook and Google, listening to HTTP sessions on wireless networks was one way to hijack the session and log in as that user, gaining access to the user's account. Macaroons are similar in that intercepting a macaroon in transit allows the interceptor to use the macaroon to gain all the privileges of the legitimate user.

### Audits
Auditors, such as Satellites, will send a challenge to a storage node and expect a valid response. A challenge is a request to the storage node in order to prove it has the expected data.

Some distributed storage systems, including the previous version of Storj [37], discuss Merkle tree proofs, in which audit challenges and expected responses are generated at the time of storage as a form of proof of retrievability [47]. By using a Merkle tree [72], the amount of metadata needed to store these challenges and responses is negligible.

Proofs of retrievability can be broadly classified into limited and unlimited schemes.The Merkle tree variety used in our previous version is one such limited scheme. Unfortunately, in such a scheme, the challenges and expected responses must be pre- generated.As we learned with our previous version, without a periodic regeneration of these challenges, a storage node can begin to pass most audits without storing all of the requested data by keeping track of which challenges exist and then saving only the expected responses. 

we began to consider Reed-Solomon erasure coding to help us solve this problem.
An assumption in our storage system is that most storage nodes behave rationally, and incentives are aligned such that most data is stored faithfully. As long as that assumption holds, Reed-Solomon is able to detect errors and even correct them, via mechanisms
 such as the Berlekamp-Welch error correction algorithm [39, 73]. We are already using Reed-Solomon erasure coding [59] on small ranges (stripes), so as discussed in the HAIL system [41], we use erasure coding to read a single stripe at a time as a challenge and then validate the erasure share responses. This allows us to run arbitrary audits without pre-generated challenges.

### Storage node reputation
Storage node reputation can be divided into four subsystems.

The first subsystem is a proof of work identity system, the second subsystem is the initial vetting process, the third subsystem is a filtering system, and finally, the fourth system is a preference system.

The goal of the first system is to require a short proof that the storage node operator is invested, through time, stake, or resources.
Initially, we are using proof of work. storage nodes require a proof of work as part of identity generation.

We will let Satellite operators set per-Satellite minimum difficulty required for new data storage.

The second subsystem slowly allows nodes to join the network. When a storage node first joins the network, its reliability is unknown. As a result, it will be placed into a vetting process until enough data is known about it.

Every time a file is uploaded, the Satellite will select a small number of additional unvetted storage nodes to include in the list of target nodes. The Reed-Solomon parameters will be chosen such that these unvetted storage nodes will not affect the durability of the file, but will allow the network to test the node with a small fraction of data until we are sure the node is reliable. After the storage node has successfully stored enough data for a long enough period (at least one payment period), the Satellite will then start including that storage node in the standard selection process used for general uploads. It will also give the node a signed message claiming that the vetting process is completed, and that the storage node may now enter other nodes’ routing tables. Importantly, storage nodes get paid during this vetting period, but don’t receive as much data.

The filtering system is the third subsystem; it blocks bad storage nodes from participating. In addition to simply not having done a sufficient proof of work, certain actions a storage node can take are disqualifying events. The reputation system will be used to filter these nodes out from future uploads, regardless of where the node is in the vetting process. Actions that are disqualifying include: failing too many audits; failing to return data, with reasonable speed; and failing too many uptime checks.

The last subsystem is a preference system. After disqualified storage nodes have been filtered out, remaining statistics collected during audits will be used to establish a prefer- ence for better storage nodes during uploads.These statistics include performance char- acteristics such as throughput and latency, history of reliability and uptime, geographic location, and other desirable qualities.

### Payments
Payments by clients may be through any mechanism (STORJ, credit card, invoice, etc.), but payments to storage nodes are via the Ethereum-based ERC20 [77] STORJ token.

### Bandwidth allocation
To solve this problem, we turn to Neuman’s Proxy-based authorization and accounting for distributed systems [79]. This accounting protocol more correctly measures re- source usage in a delegated and decentralized way.

### Satellite reputation
Storage node operators can elect to automatically trust a Storj Labs provided collec- tion of recommended Satellites that adhere to a strict set of quality controls and payment service level agreements (SLAs). To protect storage node operators, if a Satellite operator wants to be included in the “Tardigrade” approved list, the Satellite operator may be re- quired to adhere to a set of operating, payment, and pricing parameters and to sign a business arrangement with Storj Labs. See section 4.21 for more details.





## IPFS

### protocol overview
![protocol overview](ipfs-sketch.png)

![protocol overview](ipfs-overview.png)

### Proof-of-Replication
![por](ipfs-por.png)

### Proof-of-Spacetime
![pos](ipfs-pos.png)

## Blockstack

### Message Transport
Filecoin uses libp2p for all network communications. libp2p provides transport-agnostic services for peer discovery, naming, routing, pubsub channels and a distributed record store, and there are full or partial implementations in a number of languages.

### Blockstack Naming Service (BNS)
a BNS node implements a replicated name database. Each BNS node keeps itself synchronized to all of the other ones in the world, so queries on one BNS node will be the same on other nodes. BNS nodes allow a name’s owner to bind up to 40Kb of off-chain state to their name, which will be replicated to all BNS nodes via the Atlas network.

BNS nodes extract the name database log from an underlying blockchain (Blockstack Core currently uses Bitcoin, and had used Namecoin in the past). BNS uses the blockchain to establish a shared “ground truth” for the system: as long as two nodes have the same view of the blockchain, then they will build up the same database.

### Atlas Network
Atlas is designed to integrate with BNS in order to allow users to store name state off-chain, encoded as a DNS zone file. The overwhelmingly-common use-cases in Blockstack are:
* Storing a name’s routing information for its owners’ Gaia datastores.
* Storing BNS subdomain transactions and associated state.

### Gaia
![gaia](blockstack-gaia.png)

