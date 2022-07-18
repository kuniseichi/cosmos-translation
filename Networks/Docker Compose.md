

# Networks

使用docker-compose启动

```
make build-linux
make build-docker-localnode
make localnet-start
```

绑定26657, 26660, 26662, and 26664端口

## 配置文件

配置文件在docker-compose.yaml，可以增加command只想自定义abci接口

```yaml
  node0:
    container_name: node0
    image: "tendermint/localnode"
    ports:
      - "26656-26657:26656-26657"
    environment:
      - ID=0
      - LOG=$${LOG:-tendermint.log}
    volumes:
      - ./build:/tendermint:Z
    command: node --proxy-app=tcp://abci0:26658
    networks:
      localnet:
        ipv4_address: 192.167.10.2

```

## 日志

node*/tendermint.log

