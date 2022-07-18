abci应用必须提供：
- socket server
- 处理ABCI的handler

# Application Architecture Guide
我们区分两种应用，一种是用户终端应用，如钱包，下载下来用于和系统交互的。一种是ABCI应用，是运行在区块链上的逻辑。交易从用户端发送，ABCI应用执行后通过Tendermint共识提交。

图中的用户端应用是Lunie，在左下。Lunie通过应用暴露的REST API接口交流。通过RPC验证。

[img](https://docs.tendermint.com/v0.35/assets/img/cosmos-tendermint-stack-4k.6aa56af6.jpg)

ABCI应用必须是共识确定的结果，任何外部对state的影响，没经过tendermint可能导致共识失败。因此除了Tendermint，ABCI不能被其他东西访问。

如果ABCI应用是用go写的，它可以被编译进Tendermint。否则，他应该使用unix socket和Tendermint交互。如果使用TCP，则必须要加密和认证链接。

所有从ABCI应用读取的事件都应该通过Tendermint的/abci_query端口，写操作通过/broadcast_tx_*操作。

The Light-Client Daemon提供轻节点，且安全性几乎与全节点等同。它格式化交易广播，验证查询和交易的结果。它可以被当做终端应用一样实现



































