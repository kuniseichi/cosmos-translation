State sync 允许新节点迅速启动加入网络通过discovering，fetching和恢复状态机快照。

state sync有2个主要的功能：

- 将本地ABCI生成的状态机快照服务给新加入的节点
- 发现现存的snapshots并获取snapshots块

用于启动新节点的state sync程序的更多细节在上方链接。

## State Sync P2P Protocol

当一个新节点开始state syncing，它将询问所有的peer有没有可用的snapshots：

```go
type snapshotsRequestMessage struct{}
```

接收方会查询本地ABCI应用，并发送一个包含最近10个snapshot元数据的消息返回。

节点运行state sync会给本地ABCI应用提供

























