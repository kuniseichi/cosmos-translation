# What is Tendermint

Tendermint是一款能保障安全和一致性在许多机器间复制的应用。从安全角度讲，即使1/3的机器出现任意的故障，Tendermint也能运行。从一致性角度讲，每台正常的机器能看到相同的交易日志，计算出相同的结果。安全和一致性是分布式系统的基础问题；它在一些列应用中的容错，起到重要作用，包括货币，选举，基础结构等。

BFT可以容忍机器因任何原因的failing，包括变得恶意。BFT理论有些老了，但是实现是最近才流行的，由于像Bitcoin和Ethereum之类的区块链技术获得巨大的成功。区块链技术就是BFT的更现代化的设置后的充足，强调p2p网络和加密认证。

Tendermint由两种组件组成：区块链共识引擎和通用的应用接口。共识引擎，是Tendermint的核心，确保交易在每台机器都按相同的顺序执行。应用接口，叫做ABCI，能让交易以任意程序语言执行。不像其他区块链共识方案，使用在机器上预编译(像是kv存储，或是奇怪的脚本语言)，开发者可以使用任意的语言和开发环境使用Tendermint的BFT状态机器。

Tendermint设计的易于使用，易于理解，高性能，可用于广泛搭建应用。

## Tendermint vs. X

Tendermint和两种软件类似。第一种是kv store类似Zookeeper，etcd，consul等没有BFT共识的。第二种是区块链技术，由加密货币组成像Bitcoin，Ethereum，以及另外一种搭建账本设计如Hyperledger的Burrow

### Zookeeper, etcd, consul

Zookeeper, etcd, and consul 都采用kv存储，无BFT共识的算法。Zookeeper使用Paxos的一个版本，叫做Zookeeper自动广播，etcd和consul使用Raft共识算法，这种算法更新更简单。一个典型的集群包含3-5台机器，可以容纳1/2的机器错误，但是只要有一个单独的拜占庭错误就可以摧毁系统。

每个提供的kvstore特性都有些不同，但是主要是提供分布式系统基础服务，就像动态配置，服务发现，锁，leader选举等。

Tendermint本质上是一样的软件，但是有两个不同：

- 它是BFT容错，意味着可以容忍1/3的任意的错误-包括hacking和恶意攻击。
- 它不特指一个特殊的应用，像奇妙的kv store。它重点在任意状态机的复制，这样开发者就可以构建适合他们的应用逻辑，从kv存储到加密货币到投票平台甚至更多。

### Bitcoin, Ethereum, etc

Tendermint在传统加密货币像Bitcoin，Ethereum，出现。目标是提供一个比Bitcoin POW更高效安全的共识算法。在早些时候，Tendermint内部有一个简单的货币，参与共识，用户必须绑定单位的货币去安全质押，如果做了不好的行为会被撤回，这让Tendermint变成了POS算法。

那时起，Tendermint发展成一个具有通识目标的区块链引擎可以承载任意的应用状态。这意味着它可以成为其他区块链软件共识引擎的替代。所以可以用当前的Ethereum代码基础，无论是Rust或者Go或Haskell，作为ABCI应用运行在Tendermint共识上。我们确实做了tendermint/ethermint。我们还计划做其他的类似Bitcoin，ZCash和其他应用。

另一个Tendermint加密货币应用的例子是Cosmos network

### Other Blockchain Projects

fabric采用一个类似于Tendermint的方式，他对管理state更固执，并且需要所有的应用运行在docker中，叫做chaincode。它使用PBFT实现。

Burrow是以太坊虚拟机和以太坊交易及其的实现，还有额外的功能包括名称注册，权限和本地合约以及可选的区块链API。它使用Tendermint作为工时引擎，并提供一个特殊的应用state。

## ABCI Overview

ABCI允许用任意的语言实现的拜占庭容错的应用。

### Motivation

到目前为止，所有的区块链都是一个整体的设计。每个区块链都是一个应用处理所有的去中心化账本；包括p2p链接，交易王波，区块共识，账号，完成合约，用户登记权限等。

使用单片架构在计算机科学中是不好的实现。它让代码组件变得很很难复用，并且会导致代码库分叉复杂结果。没有模块化设计将导致“屎山”。

单片设计可能导致的另一个问题是限制区块链语言。

我们从区块链应用的应用状态的细节分解共识引擎和p2p层。我们通过抽象应用接口，这用socketx协议实现。

因此我们有了interface，ABCI和它的主要实现，Tendermint接口协议。

### Intro to ABCI

共识引擎通过socket协议和ABCI交流。

做一个类比，让我们讨论已知的加密货币，Bitcoin使用UTXO数据库。如果想在ABCI上层创建比特币系统，Tendermint负责

- 在节点间分享区块和交易
- 使交易顺序不可改变

应用将负责

- 维护UTXO数据库
- 验证交易的加密签名
- 防止花费不存在的交易
- 允许客户端查询UTXO数据库

tendermint通过在应用程序和共识程序间提供一个非常简单的api来分解区块链。

ABCI包含三种从core发送到应用的信息类型。

DeliverTX消息在应用中经常出现。区块链每笔交易都使用这种消息。应用























