# blockstack

## Introduction
Blockstack的设计优化源于下列属性:
1. 易使用。
去中心化应用应该像现在的互联网应用一样容易被终端用户所使用。此 外，开发去中心化应用应该像在今天的云上开发一样容易。

2. 可扩展。
去中心化应用应该可以支持互联网级别的用户量，也就是数亿到十亿的用户量。为了达到这点，网络(包括区块链)必须可以随用户数和运行的应用数 量进行扩展。

3. 用户控制。
采用去中心化计算的应用应该默认由用户控制。用户应该可以提供自己的计算和存储资源，而不是依赖于应用运营的服务器。

我们的存储系统(Gaia，见第4章)和认证协议(见第5章)是我们平台的基础组件——其使得应用不发起一个区块链交易，就可以和用户的私人数据存储交互，并且完成用户认证。Stacks区块链仅用于在去中心化的环境中，以一种一致的方式协调全局状态的变换(例如:注册一个全局唯一的用户名)。

可靠的云存储 vs.对等存储:
在Blockstack上构建的应用，其数据存储和用户是一体的(使用用户自己的私人数据锁柜)，不需要在服务器端保存任何用户访问凭。
这种方式不仅将用户数据交由用户自己控制，而且为开发人员降低了复杂度:开发人员无需运行服务器和数据库，从而代替用户支付云服务的账单。
此外，我们避免了点对点存储[18]固有的可靠性和性能问题，在一个去中心化的广域文件系统中改变了现有云存储提供商的位置——区块链层只存储指向用户数据锁柜的指针。

适用开发者的全栈SDK:Blockstack提供全栈方法，为开发去中心化应用所需的所有层提供了默认的选项。开发者SDK将区块链的复杂性和其他开发技术抽离;
应用开发者能够使用Blockstack SDKs(第6章)轻松构建他们的应用。技术栈的不同层次是模块化的，可以根据需要使用其他技术。

与同期的去中心化计算方法除了这些不同外，我们的智能合约语言也做了独特的设计决策来优化智能合约的安全和可预测性

## Stacks blockchain
燃烧比特币参与选举。
Stacks区块链实现的原生Stacks代币激活了Blockstack网络上的几项基础操作:
1. 注册数字资产的燃料。Stacks代币用来注册不同种类的数字资产，例如:用户名， 域名，软件授权，播客，还有一些其他的。

2. 注册/执行智能合约的燃料。执行智能合约需要燃料以支付验证合约正确性并执行合约的开销。Stacks代币也被用来核销在Stacks区块链上存储智能合约的成本。

3. 交易手续费。Stacks代币被用来支付交易手续费，以此Stacks区块链才能记录该交易。

4. 锚定的应用链。对于在Blockstack上广受欢迎的应用，我们的区块链有一个可扩展的入口，应用可以在Stacks区块链上初始化自己的区块链。这样的“应用链”燃烧Stacks代币挖矿。

## Tunnable Proof-of-Work

## Clarity智能合约语言
LISP语言变种。支持静态分析。
创造一个非图灵完备语言是一个重要的设计考虑。在区块链这种恶意的网络环境中，这一点为编程带来了许多好处。
1. 图灵不完备使静态分析能够决定执行一个指定交易的成本。这允许网络预先清楚地知道向一个指定交易收取多少手续费。这也会提升客户端的体验，因为对客户端来说广播一个交易的成本可知了，所以能容易地传达给用户。

2. 图灵不完备允许静态分析可以快速决定一些重要属性，例如单个交易可能调用了哪几个合约。这提升了用户体验，因为客户端可以警告用户关于一个给定交易的任何潜在副作用。

3. 改进的、精确的静态分析将允许程序员充满信心地分析他们的智能合约，在上线之前发现任何可能的缺陷和错误。

在我们智能合约语言Clarity(清)中的另一个关键设计决策是，选择一门解释型语言，而不是编译型语言(例如，编译成WASM)。与同时期的其他方法相比，我们不采用编译器的设计决策是一个根本的不同。采用这种设计决策的主要原因，是对程序实现的bug归因的能力。

## Gaia
Gaia的设计哲学是，以一种终端用户无需信任底层云服务提供商的方式重用现有的云服务提供商和基础设施。我们看待云存储服务提供商(像Amazon S3, Google Cloud Storage，甚至一个本地磁盘)只是作为一个通道(dumb drives)，在上面存储加密过的，和/或签名过的数据。云服务提供商看不到用户的数据;他们只能看到加 密的数据块。

## Blockstack Auth
Radiks 对于希望穿透复杂的社交图谱分享数据的应用来说，对数据建立索引通常是有用的和最有效的。
Radiks系统是一个服务器和客户端的程序库，用来构建并与这样的索引交互。
Radiks程序库使开发人员可以在应用内创建跨用户的结构化数据集，可以通过字段的值查询。
这要求一个服务器端的组件处理索引和查询，可关键这不是用户信任的计算环境的一部分。
其只能看到数据的密文和一些必要的元数据，后者用于构建索引以及通过索引应答查询。

## Mining for DApps
- Application Mining program
https://app.co/mining#what-is-app-mining


## People
https://www.larrysalibra.com/about/


## Dapp browser
https://browser.blockstack.org/profiles

## App Mining Eligibility Requirements
DApps that are eligible for application mining must:

* Implement Blockstack authentication
* Invite registration and use by the general public
* Storing data in a Gaia storage hub is optional. Potentially in the future it may be required.

As you work through this Zero-to-DApp tutorial, you’ll build and deploy a sample application that meets these requirements. While the application you build won't be eligible for application mining, completing the tutorial makes you eligible for a free, limited edition t-shirt:

## demo
https://github.com/blockstack/animal-kingdom
https://github.com/blockstack/stackit

## Netlify account
从git开始的部署工具。
联想到 DApp部署工具。

## Submit DApp
https://app.co/submit


## File encryption
https://www.youtube.com/watch?v=saZj0ZKRNl0
https://asecuritysite.com/encryption/ecc3


Blockstack Core implements BNS and Atlas, the storage routing system for Gaia. Blockstack Core nodes form the backbone of the Blockstack network. Each node indexes the Bitcoin blockchain and maintains a full replica of all names, public keys, and storage routing information. This makes the Blockstack network particularly resilient to node failure---applications only need to talk to a single Blockstack Core node to work, and a new or recovering node can quickly reconstruct all of its missing state from its peers.

Power users are encouraged to run local Blockstack Core nodes on their laptops or home/office networks in order to have reliable access to the Blockstack network. Your local node maintains the same state as the rest of the Blockstack Core nodes, so it will keep serving names, public keys, and storage routes even if upstream nodes are unreachable or go offline.

## Blockstack Naming Service (BNS)
https://docs.blockstack.org/core/naming/introduction.html


## Stacks - https://stackstoken.com/
Stacks (STX) token is the native utility token of the Blockstack network and the only token currently available as part of an SEC-qualified sale. Stacks tokens are consumed as "fuel" when users register digital assets, like usernames, or when they register/execute smart contracts. Using Stacks, developers can build and distribute apps that let users maintain ownership of their data and protect their digital rights.

## App Mining Algorithm
* https://blog.blockstack.org/app-mining-game-theory-algorithm-design/

## Blockstack bounty
https://signature.vc


## blockstack browser
https://blockstack.org/try-blockstack
https://github.com/blockstack/blockstack-browser/tree/master/app

## Usage
### Running a Blockstack Core Node
```
pip install blockstack
```
```
blockstack-core configure
```

