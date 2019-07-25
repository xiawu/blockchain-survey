
# P2P protocol

## Gossip protocol
https://www.cs.cornell.edu/courses/cs6410/2017fa/slides/20-p2p-gossip.pdf
* CAP - Consistency Avaiability Partition
* Paxos vs Gossip
paxos - prioritize consistency
gossip - prioritize availablity

* Gossip - AP
  - always be able to respond to a (read/write) request
  - eventual consistency

* Epidemic Models
  - Proposers and Acceptors
    - Proposer
    In Paxos: clients propose an update to the database
    Epidemic model: a node infects its neighbors
    - Acceptor
    In Paxos: acceptor accepts an update based on one or more proposals
    Epidemic model: a node is infected by a neighbor

* Types of Epidemics
  - Direct Mail
    - Notify all neighbors of an update
    - Timely and reasonably efficient
    - n messages per update
    - Messages sent: O(n) where n is number of neighbors
    - Not fault tolerant -- doesn’t guarantee eventual consistency
    - High volume of traffic with site at the epicenter

  - Anti-Entropy
    - Site chooses random partner to share data
    - Number of rounds til consistency: O(log n)
    - Sites use custom protocols to resolve conflicts
    - Fault tolerant
    
  - Rumor Mongering
    - Sites choose a random neighbor to share information with
    - Transmission rate is tuneable
    - How long new updates are interesting is also tuneable
    - Can use push or pull mechanisms
    - O(ln n) rounds leads to consistency with high probability
    - Push requires O(n ln n) transmissions until consistency
    - Further proved lower bound for all push-pull transmissions: 0(n ln ln n)
    - Pros
      - Fast
      - Low call on resources
      - Fault-Tolerant
      - Less traffic
    - Cons
      - A site can potentially miss an update
  - Backups
    - Anti-entropy can be used to “update” the network regularly after direct mail or rumor mongering
    - If inconsistency found in anti-entropy, run the original algorithm again

## Kademlia
Kademlia was conceived by Petar Maymounkov and David Mazières in 2002, and is often said to have kickstarted the adoption of the third generation of flat-hierarchy computing protocols, as it is immensely more reliable and efficient than both centralized and flood-based approaches for node discovery and routing.

Kademlia-based networks are highly resistant to denial of service attacks and the loss of a group of nodes as the protocol simply routes around the unavailable nodes.

This enables a distributed system that creates resiliency against attacks, downtime, and central points of failure.

Kademlia’s big breakthrough was to minimize internode messaging through its use of XOR metric (described in succeeding section) as a means to define distance between points in the key space.

Thus, if the distance is expressed as log2(n) nodes, this means that for a network with 10,000,000 Kademlia nodes, only about 20 hops would be necessary at most for communication with any subset of nodes.

Another advantage of Kademlia is that the protocol naturally prefers long-lived nodes over newer entrants.

### Kademlia NodeIDs
Kademlia treats each node on a network as a leaf on a binary tree. Generally, each Kademlia node has a 160-bit NodeID (SHA-1), and its position is determined by the shortest unique prefix of its ID.
To assign key-value pairs to particular nodes, Kademlia relies on a notion of distance between two identifiers. Given two 160-bit identifiers, x and y, Kademlia defines the distance between them as the XOR.
From a node point of view, the tree is divided into series of successive sub-trees where the 160th subtree contains the individual node. The Kademlia protocol ensures that each node knows of at least one node on each of its sub-trees. With this guarantee, a node can locate any other node by its ID.

### Routing Tables and K-buckets
The routing table is a binary tree whose leaves are k-buckets. The structure of the Kademlia routing table is such that nodes maintain detailed knowledge of the address space closest to them, and exponentially decreasing knowledge of more distant address space.

The symmetry is useful since it means that each of these closest contacts will be maintaining detailed knowledge of a similar part of the address space, rather than a remote part.

K-buckets are a list of routing addresses of other nodes in the network, which are maintained by each node and contain the IP address, port, and NodeID for peer participants in the system. They prefer the longest-lived nodes, which means that one cannot overtake a node’s routing state by flooding the system with new nodes (thus preventing certain types of DDOS attacks).

The routing table size is asymptotically bounded by O(log₂(n/k)) where n is the actual number of nodes in the network and k is the bucket size, so larger bucket implementations slightly reduce the total number of buckets in the routing table.

### Inter-Peer Messaging
Decentralized protocols like Kademlia require that peers speak the same language so that they may find each other, recognize one another’s position, and exchange messages.
The Kademlia protocol consists of four Remote Procedure Calls (RPCs):

PING: probes a node to see if it’s online
STORE: instructs a node to store a key-value pair
FIND_NODE: returns information about the k nodes closest to the target id
FIND_VALUE: similar to the FIND_NODE RPC, but if the recipient has received a STORE for the given key, it just returns the stored value

### Bitcoin and HyperLedger
gossip-based flooding protocols https://arxiv.org/pdf/1703.08761.pdf
lighting network: https://github.com/lightningnetwork/lightning-rfc/blob/master/02-peer-protocol.md


### Ethereum
https://medium.com/shyft-network-media/understanding-ethereums-p2p-network-86eeaa3345

The official Ethereum client node software, Geth, implements its peer discovery protocol (the RLPx Node Discovery Protocol) based on an overlay maintenance mechanism called Kademlia DHT.
While Kademlia is designed for efficiently locating and storing content in a P2P network, Ethereum’s P2P network is only used to discover new peers.
https://medium.com/coinmonks/a-brief-overview-of-kademlia-and-its-use-in-various-decentralized-platforms-da08a7f72b8f

The node discovery protocol in Ethereum’s blockchain network stack is based on a slightly modified implementation of Kademlia.
Ethereum utilizes the Kademlia’s XOR metric and the k-bucket struct, and similar to Storj, lookup is mostly used to discover new peers.
In Ethereum, the client stores information about other nodes in two data structures. The first is a long-term database, called db, which is stored on disk and persists across client reboots. The second is a short-term database, called table, which contains Kademlia-like buckets which are always empty whenever the client reboots.

Notably, Ethereum’s initial Kademlia implementation was vulnerable to the eclipse attack, whereby an attacker generates a set of Ethereum NodeIDs, and then uses a coordinated strategy to cheaply launch eclipse attacks from two host machines, (each) with just a single IP address.


Storj is able to avoid this because of the aforementioned 4.6.1 and S/Kademlia extensions, where the Satellite vetting process and Proof-of-Work certificate generation makes the cost of NodeID generation non-trivial for potential attackers.

* Protocol specification
Every node has a specific ID that is unique (Ethereum uses the SHA3 hash of your public key, node-IDs also consist of global IP and port so a connection can be established)

Example:
`enode://6f8a80d14311c39f35f516fa664deaaaa13e85b2f7493f37f6144d86991ec012937307647bd3b9a82abe2974e1407241d54947bbb39763a4cac9f77166ad92a0@10.3.58.6:30303?discport=30301 `

Node discovery and network formation are implemented via a kademlia-like UDP. Major differences from Kademlia:
  - packets are signed
  - node ids are public keys
  - DHT-related features are excluded. FIND_VALUE and STORE packets are not implemented.
  - xor distance metric is based on sha3(nodeid)

* `The RLPx Transport Protocol` - https://github.com/ethereum/devp2p/blob/master/rlpx.md
  - RLPx is named after the RLP serialization format.
  - message by ECIES Encryption
  - Node Identity: private key

* Discovery protocol
  - https://github.com/ethereum/devp2p/blob/master/discv4.md


* bootstrap
Ethereum clients are hard-coded with three main peers that are maintained by the Ethereum Foundation. Upon joining the network for the first time, the new peer will ask one of these bootstrap peers for a list of active nodes.

When two nodes want to communicate, they send each other some cryptographic data (public keys and such) to make sure all of the subsequent data transfer is encrypted (using ECDH, ECDHE, ECIES and more elliptic curve cryptography) and cryptographically signed (you can learn more about elliptic curve cryptography here).

Then, both nodes send to each other which protocols and which versions of these protocols they support: The Ethereum protocol is “eth,” Ethereum’s Whisper protocol is “shh” (Get it?), and the Light Ethereum Node Subprotocol is “les.”

* Library
https://github.com/ethereum/devp2p

https://libp2p.io/ - More Universal from IPFS community

  
### Storj
The new network utilizes a modified version of Kademlia as the primary source of truth for DNS-like functionality for node lookup, even though the key/value storage aspects of Kademlia aren't needed for the network.

filesystem based on Kademlia
https://storj.io/blog/2016/09/introducing-kfs-a-local-file-store-inspired-by-kademlia/

Using Kademlia solely for node lookup eliminates the need for some other functionality Kademlia would otherwise require, such as owner-based key republishing,neighbor-based key republishing, storage and retrieval of values, and so forth.

In addition, in order for node communication to be secure and offer complete privacy, each peer must communicate with one another in `an encrypted language that only intended participants understand` (as to avoid eavesdroppers and man-in-the middle attacks).

For this reason, we have implemented a number of the S/Kademlia extensions to enable a secure key-based routing protocol where appropriate.
S/Kademlia also provides a baselayer of protection against certain attacks against distributed systems, specifically:
* Sybil Attacks — where a user generates an extreme number of arbitrary identities (NodeIDs) to flood the network.
* Eclipse Attacks — where an attacker attempts to isolate a node or set of nodes in the network graph by ensuring that all outbound connections reach malicious nodes.
https://ieeexplore.ieee.org/document/4447808

The S/Kademlia extensions prevent sybil attacks on the address space by creating a minimum work threshold for node generation. Rather than the proof of work implemented in Bitcoin (and similar consensus protocols), storage NodeID generation requires trailing bits of 0s. This allows us to continue to use Kademlia XOR routing.

This slows down the process of adding new nodes and requires an expenditure of compute for NodeID generation, but the result of that work is incorporated into the NodeID. This allows us to continue to use XOR routing without adding extra steps to verify that a NodeID has completed the work.

In defining the V3 network specifications, we consulted closely with Kademlia author Petar Maymounkov, who is also listed as a contributor.

Storj protects itself from eclipse attacks by using public key hashes as NodeIDs, signatures based on those public keys, the node vetting process, and multiple disjoint network lookups as prescribed by S/Kademlia and Maymounkov’s contributions.

* Overcoming Kademlia’s Limits in Storj V3
There are a small set of limitations that we had to design around to design a globally scalable, highly performant, distributed object storage layer.
First, DHTs such as Kademlia require multiple network round trips for many operations, which makes it difficult to achieve millisecond-level response times.
The overlay cache on a Storj Satellite keeps track of the most recent online nodes. If they’re not found in the overlay cache, then we mark that the nodes must be offline, and therefore the erasure shares are gone, leading into the data repair process.
https://storj.io/blog/2018/12/decentralized-auditing-and-repair-the-low-key-life-of-data-resurrection/

Interestingly, the storage nodes themselves are decoupled from the DHT caching layer, in the sense that they can communicate and organize without ever coming into contact with a Satellite. However, for client upload/download to occur, they need the coordinating agent (the Satellite) to step in and communicate with them.
With each Kademlia FIND_NODE RPC shared on the overlay, the message includes the storage node’s available disk space, per-Satellite bandwidth availability, and any other metadata the network needs. The node discovery cache will collect this information provided by the nodes, optimizing the lookup speed.
The participating storage node then performs an extensive vetting process with the satellite to ensure the availability of the resources it has advertised. This process sets the baseline reputation for the storage node, and factors into earning potential.
Thus, a node that enters the routing tables is considered ‘vetted’ by the network, and lookups only progress through vetted nodes.
This ensures that only nodes with verified disk space have the ability to enter and participate in the routing layer, while also adding insight into the networks capacity and also protecting against attacks.


### IPFS
The Inter Planetary File System (IPFS) also uses Kademlia, with Coral DSHT and S/Kademlia extensions. In IPFS’s implementation, the NodeID contains a direct map to IPFS file hashes. Each node also stores information on where to obtain the file or resource.

A number of projects are looking to utilize the Storj network as an object store for IPFS. One of the more notable is RTrade, which is building out a Storj-backed IPFS node to ensure the availability and durability of its IPFS files.

### SWarm
The primary objective of Swarm is to provide a sufficiently decentralized and redundant store of Ethereum’s public record, in particular to store and distribute dApp code and data, as well as blockchain data.

Participants in the Swarm network are identified in the Kademlia DHT by the hash of the Ethereum address of the Swarm base account.

This serves as their overlay address, the proximity order bins are calculated based on these addresses.

Rather than large object storage, Swarm is best suited for smaller data bits associated with Ethereum smart-contracts.

Swarm uses a max 4k shard size, and a TB is roughly 1000000000kb. Thus, for a larger object, say 1TBs, to be uploaded to Swarm, it would require 250,000,000 nodes (which is only slightly less than the population of the United States).

In comparison, Storj is better suited for storing large objects, as we just need to find enough nodes to cover the erasure shares.


### ZeroNET
ZeroNet network protocol
Every message is encoded using MessagePack
Every request has 3 parameter:
* cmd: The request command
* req_id: The request's unique id (simple, incremented nonce per-connection), the client has to include this when reply to the command.
* params: Parameters for the request

Example request: {"cmd": "getFile", "req_id": 1, "params:" {"site": "1EU...", "inner_path": "content.json", "location": 0}}
Example response: {"cmd": "response", "to": 1, "body": "content.json content", "location": 1132, "size": 1132}
Example error response: {"cmd": "response", "to": 1, "error": "Unknown site"}
https://zeronet.readthedocs.io/en/latest/help_zeronet/network_protocol/

Data exchange is enabled using BitTorrent trackers, and each site is treated like a single Torrent (which is a very intuitive idea). The project supports Tor to help hide your IP address, which makes connections private (though like IPFS, this is not the default).

### Blockstack
Atlas was designed to overcome the structural weaknesses inherent to all distributed hash tables. In particular, it uses an unstructured peer network to maximize resilience against network link failure, and it uses the underlying blockchain (through BNS) to rate-limit chunk announcements.
https://docs.blockstack.org/core/atlas/howitworks.html

This section contains the following sections:

Peer Selection
Comparison to DHTs
Chunk Censorship
Neighbor Censorship
Chunk Propagation
Querying Chunk Inventories
Peer Selection

Atlas peers self-organize into an unstructured peer-to-peer network. The Atlas peer network is a random K-regular graph. Each node maintains K neighbors chosen at random from the set of Atlas peers.

Atlas nodes select peers by carrying out an unbiased random walk of the peer graph. When “visiting” a node N, it will ask for N’s neighbors and then “step” to one of them with a probability dependent on N’s out-degree and the neighbor’s in-degree.

The sampling algorithm is based on the Metropolis-Hastings (MH) random graph walk algorithm, but with a couple key differences. In particular, the algorithm attempts to calculate an unbiased peer graph sample that accounts for the fact that most nodes will be short-lived or unreliable, while a few persistent nodes will remain online for long periods of time. The sampling algorithm accounts for this with the following tweaks:

If the neighbors of the visited node N are all unresponsive, the random walk resets to a randomly-chosen known neighbor. There is no back-tracking on the peer graph in this case.

The transition probability from N to a live neighbor is NOT min(1, degree(neighbor)/degree(N)) like it is in the vanilla MH algorithm. Instead, the transition probability discourages backtracking to the previous neighbor N_prev, but in a way that still guarantees that the sampling will remain unbiased.

A peer does not report its entire neighbor set when queried, but only reports a random subset of peers that have met a minimium health threshold.

A new neighbor is only selected if it belongs to the same BNS fork-set (i.e. it reports as having a recent valid consensus hash).

The algorithm was adapted from the work from Lee, Xu, and Eun in the proceedings of ACM SIGMETRICS 2012.

* Comparison to DHTs
The reason Atlas uses an unstructured random peer network instead of a distributed hash table (DHT) is that DHTs are susceptbile to Sybil attacks. An adaptive adversary can insert malicious nodes into the DHT in order to stop victims from resolving chunks or finding honest neighbors.

* Chunk Censorship
In a DHT, an attacker can censor a chunk by inserting nodes into the peers’ routing tables such that the attacker takes control over all of the chunk’s hash buckets. It can do so at any point in time after the chunk was first stored, because only the peers who maintain the chunk’s hash bucket have to store it. `This is a fundamental problem with structured overlay networks that perform request routing based on content hash—they give the attacker insight as to the path(s) the queries take through the peer graph, and thus reduce the number of paths the attacker must disrupt in order to censor the chunk.`

Atlas uses an unstructured overlay network combined with a 100% chunk replication strategy in order to maximize the amount of work an adversary has to do to censor a chunk. In Atlas, all peers replicate a chunk, and the paths the chunk take through the network are independent of the content and randomized by the software (so the paths cannot be predicted in advance). The attacker’s only recourse is to quickly identify the nodes that can serve the chunk and partition them from the rest of the network in order to carry out a censorship attack. This requires them to have visibility into the vast majority of network links in the Atlas network (which is extremely difficult to do, because in practice Atlas peers maintain knowledge of up to 65536 neighbors and only report 10 random peers when asked).

* Neighbor Censorship
Another problem with DHTs is that their overlay network structure is determined by preferential attachment. Not every peer that contacts a given DHT node has an equal chance of becoming its neighbor. The node will instead rank a set of peers as being more or less ideal for being neighbors. In DHTs, the degree of preference a node exhibits to another node is usually a function of the node’s self-given node identifier (e.g. a node might want to select neighbors based on proximity in the key space).

The preferential attachment property means that an adaptive adversary can game the node’s neighbor selection algorithm by inserting malicious nodes that do not forward routing or lookup requests. The attacker does not even have to eclipse the victim node—the victim node will simply prefer to talk to the attacker’s unhelpful nodes instead of helpful honest nodes. In doing so, the attacker can prevent honest peers from discovering each other and each other’s chunks.

Atlas’s neighbor selection strategy does not exhibit preferential attachment based on any self-reported node properties. A node is selected as a neighbor only if it is reached through an unbiased random graph walk, and if it responds to queries correctly. In doing so, an attacker is forced to completely eclipse a set of nodes in order to cut them off from the rest of the network.

* Chunk Propagation
Atlas nodes maintain an inventory of chunks that are known to exist. Each node independently calculates the chunk inventory from its BNS database. Because the history of name operations in BNS is linearized, each node can construct a linearized sub-history of name operations that can set chunk hashes as their name state. This gives them a linearized sequence of chunks, and every Atlas peer will independently arrive at the same sequence by reading the same blockchain.

Atlas peers keep track of which chunks are present and which are absent. They each construct an inventory vector of chunks V such that V[i] is set to 1 if the node has the chunk whose hash is in the ith position in the chunk sequence (and set to 0 if it is absent).

Atlas peers exchange their inventory vectors with their neighbors in order to find out which chunks they each have. Atlas nodes download chunks from neighbors in rarest-first order in order to prioritize data replication for the chunks that are currently most at-risk for disappearing due to node failure.

   Name operation   |  chunk hashes  |   chunk data    |  Inventory
      history       |  as name state |                 |   vector

+-------------------+
| NAME_PREORDER     |
+-------------------+----------------+
| NAME_REGISTRATION | chunk hash     |  "0123abcde..."       1
+-------------------+----------------+
| NAME_UPDATE       | chunk hash     |    (null)             0
+-------------------+----------------+
| NAME_TRANSFER     |
+-------------------+
| NAME_PREORDER     |
+-------------------+----------------+
| NAME_IMPORT       | chunk hash     |  "4567fabcd..."       1
+-------------------+----------------+
| NAME_TRANSFER     |
+-------------------|
      .  .  .


Figure 2:  Relationship between Atlas node chunk inventory and BNS name state.
Some name operations announce name state in the blockchain, which Atlas interprets as a chunk hash.  The Atlas node builds up a vector of which chunks it has and which ones it does not, and announces it to other Atlas peers so they can fetch chunks they are missing.  In this example, the node's inventory vector is [1, 0, 1], since the 0th and 2nd chunks are present but the 1st chunk is missing.

Querying Chunk Inventories

Developers can query a node’s inventory vector as follows:

>>> import blockstack
>>> result = blockstack.lib.client.get_zonefile_inventory("https://node.blockstack.org:6263", 0, 524288)
>>> print len(result['inv'])
11278
>>>
The variable result['inv'] here is a big-endian bit vector, where the ith bit is set to 1 if the ith chunk in the chunk sequence is present. The bit at i=0 (the earliest chunk) refers to the leftmost bit.

A sample program that inspects a set of Atlas nodes’ inventory vectors and determines which ones are missing which chunks can be found here.


## Relay Network



