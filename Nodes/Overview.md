# Overview

这一部分的重点在如何操作full nodes，validators和light clients。

- Node Types

- Configuration

  - Configure State sync

- Validator Guides

  - Running in Production

    How to secure your keys

  - Remote Signer

- Light Client guides

  - How to sync a light client

- Metrics

- Logging

## Node Types

我们将描述Tendermint中几种不同类型的node。

### Full Node

全节点是参与网络的节点，但其并对网络中的安全没有帮助。

全节点可用作存储整个区块链state。Tendermint有两种形式的state。

1. blockchain state，表示区块链中的区块
2. Application state，表示交易的改动

关于交易是如何修改state的不在Tendermint的范围内，在ABCI范围内的application。

> 如果你还没有度过共识和应用分离的内容，请花几分钟读一下，它能让你更好的理解整个文章的内容。你可以在这找到更多[ABCI][]信息

作为全节点，你正在向网络提供服务，这让它开始共识并让其他节点能够获取当前区块。尽管全节点在网络中只做共识，保护节点安全依然很重要。我们推荐使用防火墙和代理。运行全节点很容易，但是因网络不同而异。优先验证你的应用文档。

### Seed Nodes

seed node提供了一个带有一组可被节点访问peer的node。启动时你必须提供至少一种类型node才能连接网络。通过提供seed node你将迅速放置地址。seed node不作为peer但是当它提供一组peer后会断开节点。

### Sentry Node

sentry node和full node很像。区别是sentry node将有一个或多个私有的peer。这些peer可能是validators或是参与网络的full nodes。sentry node的作用是给validator提供一层防护，和防火墙的功能类似。

### Validators

Validators是参与安全的的node。Validators在Tendermint中有权力，这个权力可以使pos系统中的stake，或者是poa系统中的reputation，或者其他类型的测量单元。运行安全，持续在线的validator对网络的健康很关键。validator必须安全且容错，推荐运行validator伴随2个或更多sentry nodes。

作为validator有可能有更少的weight，这是由应用决定的。应用程序会通知Tendermint如果validator weight增加了或减少了。应用有2中类型的恶意行为可以导致削减validator的权力。查看你将要运行的应用文档获取更多信息























