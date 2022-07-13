# Quick Start

## Overview

这是一个快速启动指南。你将对Tendermint是如何工作的，以及如何启动tendermint有一个大概的印象。确保你已经下载了binary。

## Initialization

运行：

```sh
tendermint init validator
```

将为一个本地的单节点创建必须的文件，并放在$HOME/.tendermint下

```sh
$ ls $HOME/.tendermint

config  data

$ ls $HOME/.tendermint/config/

config.toml  genesis.json  node_key.json  priv_validator.json
```

对一个本地单节点来说，这些配置足够。集群的配置在后面。

## Local Node

用一个简单的in-process应用开始Tendermint：

```sh
tendermint start --proxy-app=kvstore
```

> kvstore是一个非持久化应用，如果你想持久化运行使用--proxy-app=persistent_kvstore

区块开始流入

```
I[01-06|01:45:15.592] Executed block                               module=state height=1 validTxs=0 invalidTxs=0
I[01-06|01:45:15.624] Committed state                              module=state height=1 txs=0 appHash=
```

检查状态：

```sh
curl -s localhost:26657/status
```

### Sending Transactions

KVstore app运行的时候，我们可以发送一些交易：

```sh
curl -s 'localhost:26657/broadcast_tx_commit?tx="abcd"'
```

检查是否生效：

```sh
curl -s 'localhost:26657/abci_query?data="abcd"'
```

我们可以使用key value发送交易：

```sh
curl -s 'localhost:26657/broadcast_tx_commit?tx="name=satoshi"'
```

然后查询key：

```sh
curl -s 'localhost:26657/abci_query?data="name"'
```

值以hex返回。

## Cluster of Nodes

4台机器IP1, IP2, IP3, IP4，每台都执行以下：

```sh
curl -L https://git.io/fFfOR | bash
source ~/.profile
```

这将下载go和其他依赖，获取tendermint源码，然后编译tendermint二进制文件。

然后使用tendermint testnet命令创建四个配置文件目录文件(放在./mytestnet)复制到每台机器相应的位置。

查看nodeid

```sh
tendermint show_node_id --home ./mytestnet/node0
tendermint show_node_id --home ./mytestnet/node1
tendermint show_node_id --home ./mytestnet/node2
tendermint show_node_id --home ./mytestnet/node3
```

然后，在每台机器上运行：

```sh
tendermint start --home ./mytestnet/node0 --proxy-app=kvstore --p2p.persistent-peers="ID1@IP1:26656,ID2@IP2:26656,ID3@IP3:26656,ID4@IP4:26656"
tendermint start --home ./mytestnet/node1 --proxy-app=kvstore --p2p.persistent-peers="ID1@IP1:26656,ID2@IP2:26656,ID3@IP3:26656,ID4@IP4:26656"
tendermint start --home ./mytestnet/node2 --proxy-app=kvstore --p2p.persistent-peers="ID1@IP1:26656,ID2@IP2:26656,ID3@IP3:26656,ID4@IP4:26656"
tendermint start --home ./mytestnet/node3 --proxy-app=kvstore --p2p.persistent-peers="ID1@IP1:26656,ID2@IP2:26656,ID3@IP3:26656,ID4@IP4:26656"
```

当第三台机器启动时，区块开始因为超过三分之二的validator在线。持久化peer可以在config.toml中被指定。

接下来交易可以像在本地单节点发送一样。