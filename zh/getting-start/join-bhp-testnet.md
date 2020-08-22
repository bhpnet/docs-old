---
order: 3
---

# 加入公共测试网
有关最新测试网络的信息，请参阅[测试网络存储库](https://github.com/bhpnet/bhp)，包含了所使用的BHP的正确版本和genesis文件。

:::tip
需要先 [安装 bhp](install-bhp.md)
:::

## 运行全节点

```bash
# 初始化节点
bhpd init moniker=<your-custom-name> --chain-id=testing

# 下载测试网公开的 config.toml 和 genesis.json
curl -o ~/.bhpd/config/config.toml https://raw.githubusercontent.com/bhpnet/launch/master/testnet/config.toml
curl -o ~/.bhpd/config/genesis.json https://raw.githubusercontent.com/bhpnet/launch/master/testnet/genesis.json

# 启动节点（也可使用 nohup 或 systemd 等方式后台运行）
bhpd start
```

:::tip
您可能会看到一些连接错误，这没关系，P2P网络正在尝试查找可用的连接
testnet/config.toml
可以添加几个[社区公开节点](https://github.com/bhpnet/launch/blob/master/testnet/community-peers.md)到`config.toml`中的`persistent_peers`。
:::

:::tip
从零开始追赶区块需要很长时间，您也可以下载[测试网数据快照](#TODO)以减少同步时间
:::

## 升级为验证人节点

### 创建钱包

您可以[创建新的钱包](../cli-client/keys.md#创建密钥)或[导入现有的钱包](../cli-client/keys.md#通过助记词恢复密钥)，然后从交易所或其他任何地方转入一些BHP到您刚刚创建的钱包中：

```bash
# 创建一个新钱包
bhpcli keys add <key-name>
```

:::warning
在安全的地方备份好助记词！如果您忘记密码，这是恢复帐户的唯一方法。
:::

### 确认节点同步状态

```bash
# 可以使用此命令安装 jq
# apt-get update && apt-get install -y jq

# 如果输出为 false, 则表明您的节点已经完成同步
bhpcli status | jq .sync_info.catching_up
```

### 创建验证人

只有节点已完成同步时，才可以运行以下命令将您的节点升级为验证人：

```bash
bhpcli stake create-validator \
    --pubkey=$(bhpd tendermint show-validator) \
    --moniker=<your-validator-name> \
    --amount=<amount-to-be-delegated, e.g. 10000abhp> \
    --commission-rate=0.1 \
    --gas=100000abhp \
    --fee=0.6abhp \
    --chain-id=testing \
    --from=<key-name> \
    --commit
```

:::warning
**重要**

一定要备份好 home（默认为〜/.bhpd/）目录中的 `config` 目录！如果您的服务器磁盘损坏或您准备迁移服务器，这是恢复验证人的唯一方法。
:::

如果以上命令没有出现错误，则您的节点已经是验证人或候选人了（取决于您的Voting Power是否在前21名中）