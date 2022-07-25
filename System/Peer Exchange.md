# Peer Strategy and Exchange

这里我们里列出了peerStore的设计大纲，以及它如何被Peer Exchange Reactor使用以确保能连接到好的peer并把peer gossip给其他人

## Peer Types

peer是特殊的，他们被当做是持续存在的，这意味着如果连接失败了我们自动连接他们。一些peer是private，这意味着我们不会把他们放进store中发给其他peer。

## Discovery

peer的发现从seed开始

当我们没有足够的peer时

1. 询问已经存在的peer
2. 访问seed

启动时，我们会立即访问persistent_peers，并会和persistent_peers保持联系。如果连接段了，或者连接失败，我们将每5s请求一次，持续几分钟，然后切换到exponential backoff schedule，这些都在配置persistent_peers_max_dial_period中。

dial_period = min(persistent_peers_max_dial_period,exponential_backoff_dial_period) 

## Listening























