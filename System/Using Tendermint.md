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



























