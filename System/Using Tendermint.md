# Using Tendermint

这是使用tendermint命令行的教程。假设你已经有了tendermint binary并了解什么是Tendermint和ABCI。

## Directory Root

区块数据根目录是 ~/.tendermint。可以设置TMHOME设置。

## Initialize

初始化root 目录：

```bash
tendermint init validator
```

这回创造一个私钥（priv_validator_key.json），和一个初始化文件（genesis.json）包含了公钥，在$TMHOME/config。这些对使用validator运行一个本地testnet很有必要。

了解更多信息看

```sh
tendermint testnet --help
```

### Genesis

$TMHOME/config/的genesis.json文件定义了初始化TendermintCore state。

#### Fields

## Run

Tendermint会默认链接127.0.0.1:26658。如果你已经下载了kvstore的ABCI，在另一个窗口运行它。或者运行

```sh
tendermint start --proxy-app=kvstore
```

几秒种后，你会看到区块开始流入。注意区块即使没有交易也在规律的产生。看No Empty Blocks配置。

tendermint支持内置的应用包括counter, kvstore和noop，都是用abci-cli实现的。如果不是用go写的需要指定socket的地址

## Transactions

发送交易，使用curl发送交易请求

```sh
curl http://localhost:26657/broadcast_tx_commit?tx=\"abcd\"
```

我们可以通过/status查询链的状态

```sh
curl http://localhost:26657/status | json_pp
curl http://localhost:26657/status | json_pp | grep latest_app_hash
```

### Formatting

当给RPC接口发送交易时，接下来的规则必须遵守：

发送UTF8作为交易数据，使用tx作为参数名，参数值用双引号：

```sh
curl 'http://localhost:26657/broadcast_tx_commit?tx="hello"'
```

可以使用hex进制数，可以省略引号并在数据前加上0x

使用POST，数据使用base64编码，作为json格式发送

```sh
curl http://localhost:26657 -H 'Content-Type: application/json' --data-binary '{
  "jsonrpc": "2.0",
  "id": "anything",
  "method": "broadcast_tx_commit",
  "params": {
    "tx": "aGVsbG8="
  }
}'
```

## Reset

重置

```sh
tendermint unsafe_reset_all
```

## Configuration

tendermint使用config.toml作为配置项，



## No Empty Blocks

默认每秒创建一次区块，可以禁止

```sh
tendermint start --consensus.create_empty_blocks=false
```

在config.toml中配置

```sh
[consensus]
create_empty_blocks = false
```

还可以设置时间间隔

```sh
--consensus.create_empty_blocks_interval="5s"
```

```sh
[consensus]
create_empty_blocks_interval = "5s"
```

## Broadcast API

早些时候，我们使用broadcast_tx_commit端口发送交易。当交易发送到Tendermint，通过Checktx，放到mempool中，广播到其他节点，最终写入区块。

交易有很多阶段，我们提供了多个终端广播交易：

```
/broadcast_tx_async
/broadcast_tx_sync
/broadcast_tx_commit
```

这些分别对应于不处理，通过mempool处理和通过块处理。

- broadcast_tx_async会直接返回结果，不论交易是否合法
- broadcast_tx_sync会返回Checktx的结果
- broadcast_tx_commit会等交易提交到区块中或timeout，如果没通过CheckTx也会直接返回。返回结果包括check_tx和deliver_tx，通过这些ABCI信息与结果交互

broadcast_tx_commit的优点是交易成功提交到区块后返回，但是这可能要花费1秒。使用broadcast_tx_sync更快，但是之后才会提交。

但是mempool不可靠，通过了CheckTx不意味着能被提交，

## Tendermint Networks

tendermint init的时候 genesis.json and priv_validator_key.json创建在 ~/.tendermint/config下。

priv_validator_key.json包含私钥，因此应该被秘密保存；现在我们用明文。last_字段是为了防止签名冲突。

公钥存在genesis.json and priv_validator_key.json。

genesis文件包含的公钥可能参与共识，以及投票权。超过2/3的投票权要被激活。在这个例子中，genesis文件包含了公钥，所以tendermint节点可以用默认的根目录启动。收到int64限制，投票权不能设置超过10^12

如果你想在网络中加入更多节点，有两个方法：

- 增加一个新的validator，通过发起区块，投票参与与共识
- 或者增加一个非validator节点，不直接参与，但是会验证并遵守共识协议

### Peers

####  Seed

seed节点的作用是转发它所知道的其他peer节点的地址。seed节点持续的从网络中获取更多peer。这些被转发的peer地址被存入本地地址库。当你有了这个地址库，你可以直接连接这些地址。基本上seed节点的工作就是转发每个地址。你不需要连接到seed node，你可以收到足够的地址，所以你需要在启动的时候需要他们。seed node把消息发送给你后会立刻断开连接。

#### Persistent Peer

persistent peer是要持续连接的。如果断连了你将用地址库中的另一个地址连回去。重启时你会无视地址库的大小，重连这些peer。

所有的peer依赖他们所知道的peer。这叫做peer交换协议PeX。peer会发布gossiping关于peer和组建网络，存储peer地址。因此你不需要seed节点。

#### Connecting to Peers

启动时连接的peer，可以指定在命令行或config.toml中指定。使用seeds指定seed节点，使用persistent-peers指定peer节点。

```sh
tendermint start --p2p.seeds "f9baeaa15fedf5e1ef7448dd60f46c01f1a9e9c4@1.2.3.4:26656,0491d373a8e0fcf1023aaf18c51d6a1d0d4f31bd@5.6.7.8:26656"
```

或者通过RPC指定

```sh
curl 'localhost:26657/dial_seeds?seeds=\["f9baeaa15fedf5e1ef7448dd60f46c01f1a9e9c4@1.2.3.4:26656","0491d373a8e0fcf1023aaf18c51d6a1d0d4f31bd@5.6.7.8:26656"\]'
```

注意，PeX启用后，在第一次启动后不在需要seed节点了。

同样的，persistent 节点

```sh
tendermint start --p2p.persistent-peers "429fcf25974313b95673f58d77eacdd434402665@10.11.12.13:26656,96663a3dd0d7b9d17d4c8211b191af259621c693@10.11.12.14:26656"

curl 'localhost:26657/dial_peers?persistent=true&peers=\["429fcf25974313b95673f58d77eacdd434402665@10.11.12.13:26656","96663a3dd0d7b9d17d4c8211b191af259621c693@10.11.12.14:26656"\]'
```

### Adding a Non-Validator

启动一个非validator节点很简单。只需要把genesis.json 复制到新机器的~/.tendermint/config，指定seed或peer。如何seed或peer没有指定，节点不会产生block，

### Adding a Validator

增加新的validator最简单的方法是启动网络前在genesis.json中加入。比如创建一个新的`priv_validator_key.json`,把pub_key复制进genesis文件中。

我们可以用命令生成新的priv_validator_key.json

```sh
tendermint gen_validator
```

在两台机器分别启动tendermint start，其中genesis使用同一个文件，priv_validator_key.json使用各自的。

### Local Network































