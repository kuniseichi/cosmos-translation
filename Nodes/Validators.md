# Validators

Validators负责将新区块提交到区块链。这些validator通过广播vote参与共识，votes中包含validator私钥加密的签名。

一些pos共识算法目标是创造完整的去中心化系统，所有的stakeholders都参与提交区块（即使有些不总是在线）。Tendermint创建区块的方式不一样。Validators应该在线，并且一组validator被外部程序管理。pos不被需要，但可以在Tendermint共识上层实现。validator需要抵押，也可能不需要抵押。

Validators有加密的键值对，和投票权绑定。

## Becoming a Validator

有两种方式成为validator。

1. 在建立前放在初始状态中
2. ABCI修改

## Setting up a Validator

























