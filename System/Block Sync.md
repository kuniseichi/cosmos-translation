# Block Sync

在pow的区块链中，同步链就相当于保持最新的共识：下载区块，寻找工作量做大的。在pos中，共识程序更复杂，他会进行很多轮的交流来决定那个区块应该提交。使用程序同步区块可能需要很长的时间。下载区块检查validator的merkle tree比运行真实时间的共识协议更快。

## Using Block Sync

为了支持更快的同步，tendermint提供 blocksync模式，默认启动，可以在config.toml中通过--blocksync.enable=false切换。

在这个模式中，tendermint程序同步速度比real-time共识程序同步速度块上百倍。

























