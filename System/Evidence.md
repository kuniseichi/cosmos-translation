evidence是用来举证validator作恶的。有多种Evidence。

evidence的工作原理和mempool原理相似。当evidence被发现时，他会重复发送给所有peer。这保证了evidence发送给尽可能多的peer。在收到evidence，并提交区块后，evidence会从模块中移除。

发送错误的编译数据，或者数据超过了maxMsgSize会导致peer停止。