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

























