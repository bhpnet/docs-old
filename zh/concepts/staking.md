---
order: 5
---

# Staking

## 简介
本文简要介绍了mint模块、staking模块和distribution模块的功能及命令
## 前提
确保已有BHP网络正常运行，且本地已安装了bhpcli，且版本一致
## 概念
### 投票权重
投票权重是一个共识层面的概念。BHP公链是一个支持BFT的POS区块链网络。在共识过程中，一个验证人集将对提案区块进行投票。如果验证人认为提案块有效，它将投赞成票，否则，它将投反对票。不同验证人的投票所占权重不同。投票的权重称为相应验证人的投票权重。
### 验证人节点
验证人节点是一个BHP全节点。如果其投票权重为零，则它只是一般的全节点或候选验证人。一旦其投票权重为正数，那么它就是一个真正的验证人。
### 委托人
不能或不想运行验证人节点的人仍然可以作为委托人参与到共识过程中。委托人可以将bhp委托给验证人，委托人将从相应的验证人那里获得一定的bhp份额。委托bhp也称为绑定bhp给验证人。特殊的，验证节点的所有者也是委托人，自己委托bhp到自己的全节点上，此全节点就变成了验证节点。验证节点的所有者不仅可以在其自己的验证节点上抵押bhp，而且也可以在其他验证节点上抵押bhp。
::: danger
**验证节点的所有者在解绑自己抵押的bhp时，切勿完全解绑。 一旦完全解绑，该验证人节点将被处于jailed状态，该节点将收不到任何奖励或者佣金，在该节点上委托bhp的委托人的利益也会收到相应的损失。 所以，无论如何请保留至少1bhp在抵押状态。**
**如果一旦验证人全部解委托，可以通过重新`bhpcli tx staking delegate`和`bhpcli tx slashing unjail`的命令来恢复**
:::
### 候选验证人
验证人的数量不能无限增加。太多验证人可能会导致低效的共识，从而降低区块链吞吐率。因此，拜占庭容错的POS区块链网络都有验证人数量上限。在BHP网络中，这个上限是21。如果有超过21个全节点申请加入验证人集，那么只有具有抵押bhp数量排名前21的节点才能成为真正的验证人，其他人将是候选验证人，并将根据他们抵押bhp的数量进行降序排序。一旦一个或多个验证人被从验证人集中踢出，则排名靠前的候选验证人将被自动添加到验证人集中。
### 绑定、解绑和解绑期
验证人节点的所有者必须将他们自己流通的bhp绑定到自己的验证人节点。验证人节点投票权重与绑定的bhp数量成正比，包括所有者自己绑定的bhp和来自其他委托人的bhp。验证人节点的所有者可以通过发送解绑交易来降低他们自己绑定的bhp。委托人同样可以通过发送解绑交易来降低绑定的bhp。但是，这些被解绑的bhp不会立即成为流通的bhp。执行解绑命令之后，在解绑期结束之前，相应的验证人节点的所有者或委托人不能再次在相同的验证人节点上发起解绑交易。通常，BHP网络中解绑期为2周。一旦解绑期结束，被解绑的bhp将自动成为流通的bhp。解绑期机制对POS区块链网络的安全性很重要。此外，如果验证人节点的所有者在自己的验证人节点上没有绑定bhp，则相应的验证人会被踢出验证人集。
### 转委托
委托人可以将其抵押的bhp从一个验证人转移到另一个验证人。这个可以分为两个步骤：从第一个验证人上解绑和把解绑的bhp绑定到另一个验证人上。正如我们上面所说，在解绑期结束之前，解绑操作不能立即完成，这意味着委托人不能立即发送再次转委托交易。
### 作恶证据和惩罚
拜占庭容错POS区块链网络假设拜占庭节点拥有不到总投票权重的1/3，而且要惩罚这些作恶节点。因此有必要收集作恶行为的证据。根据收集到的证据，staking模块将从相应的验证人和委托人中拿走一定数量的bhp。被拿走的bhp会被销毁。此外，作恶验证人将会被踢出验证人集，并被标记为关押(jailed)状态，而且他们的投票权将立刻变为零。在关押期间，这些节点也不是候选验证人。当关押期结束，他们可以发送unjail交易来解除关押状态并再次成为候选验证人。
### 收益
作为委托人，向验证人抵押bhp的份额越多，获得的收益就越多。对于验证人节点的所有者，它将有额外的收益：出块奖励、佣金和交易费。首先，是出块奖励，当验证人为块的提议者时，将会获得额外奖励。其次是佣金，每个验证者可以设置自己的佣金比例，收取委托者质押bhp收益的佣金。最后是交易手续费，每次发送交易需要消耗链上资源，所以必须设置手续费来限制无意义的交易发送。

## 用户操作-交易
### 成为验证人
1. 执行`bhpcli keys add demokey`来创建一个地址，然后从其他拥有bhp的地址转账部分bhp到此地址，如30000bhp，即3000000000000abhp。
2. 通过`bhpd init demonode`，初始化一个节点，这里用demonode为此节点名字。
3. 下载测试网的config.toml和genesi.json ，复制替换到 ~/.bhpd/config/ 目录下。需要更改~/.bhpd/config/config.tmol里的moniker为demonode。
4. 启动全节点。`bhpd start`，稍等片刻，快速开始同步数据到最新高度，
5. 通过以下命令即可成为验证者，因为需要手续费，所以这里暂时只委托20000bhp。这里的moniker可以不同于3的moniker。也可以添加--gas设置自己想出的gas数量，默认是200000. fee = gas * gasPrices , gas或者gas-prices设置过低会导致交易发送失败。
```bash
bhpcli tx staking create-validator \
--amount=2000000000000abhp \
--pubkey=$(bhpd tendermint show-validator) \
--moniker="DemoValidator" \
--details="I'm a good validator" \
--chain-id=testing \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.01" \
--min-self-delegation="1" \
--gas-prices="2.5abhp" \
--from=demokey
```
6. 通过`bhpcli query staking validators --chain-id="testing"`来查询是否添加成功。
### 编辑验证人信息
可以编辑验证人信息，如网站，不修改的信息，保持创建时的值。--gas省略，使用默认值，以下都是如此。再次使用成为验证人第6步命令即可查询修改是否成功。
```bash
bhpcli tx staking edit-validator \
--website "https://bhpa.io" \
--from demokey \
--gas-prices="2.5abhp" \
--chain-id "testing"
```
### 委托bhp到验证人
其中验证人地址可从成为验证人第5步查询得到，如 bhpvaloper1fw09gul7lltrv7w04grwdcpptsent26qsgv6g9，前缀是bhpvoloper
```bash
bhpcli tx staking delegate \
验证者地址 \
500000000000abhp \
--from demokey \
--chain-id testing \
--gas-prices="2.5abhp"
```
我们可以委托任何地址中的bhp到任何验证人，执行上述命令即可，不一定只能用demokey地址来委托。
### 转委托
用户可以切换委托从一个验证人到另一个验证人。
```bash
bhpcli tx staking redelegate \
原验证人地址 \
新验证人地址 \
250000000000abhp \
--from demokey \
--chain-id testing \
--gas-prices="2.5abhp"
```
### 解除委托
如果用户委托了bhp到验证人上，想解除委托可以执行以下命令，默认解绑器是2周，即2周后，委托的bhp才可以流通。
```bash
bhpcli tx staking unbond \
验证人地址 \
200000000000abhp \
--from demokey \
--chain-id testing \
--gas-prices="2.5abhp"
```
### 解除关押
当此验证人离线未参与共识，将会被jailed，通过`
bhpcli tx slashing unjail --from demokey --chain-id testing`
可以解除关押，重新参与共识，但由于离线原因，作为惩罚会有小部分委托的bhp被销毁。
### 获取委托收益
#### 普通用户获取收益
此命令可以获取普通用户委托在一个验证人上的收益，想一次获取所有委托收益，见下文。
```bash
bhpcli tx distribution withdraw-rewards \
验证人地址 \
--from demokey \
--chain-id testing \
--gas-prices="2.5abhp"
```
#### 验证人所有者获取收益
和普通用户获取收益命令类似，--from 则需要使用创建验证人时的key名字，
再加上 --commission 即可提取委托收益和用户委托的佣金收益。
### 委托人获取所有委托的收益
如果用户委托bhp到多个验证人，想一次性获取收益，可以执行下面的命令。
```bash
bhpcli tx distribution withdraw-all-rewards \
--from demokey \
--chain-id testing \
--gas-prices="2.5abhp"
```

## 用户操作-查询
### 查询所有验证者
```bash
 bhpcli query staking validators --chain-id=testing
```
### 查询一个用户的所有委托
```bash
bhpcli query staking delegations $(bhpcli keys show demokey -a) --chain-id testing
```
### 查询一个验证人的所有委托
```bash
bhpcli query staking delegations-to  验证人地址 --chain-id=testing
```
### 查询一个用户在一个验证人上的委托
```bash
bhpcli query staking delegation \
$(bhpcli keys show demokey -a) \
验证人地址 --chain-id=testing
```