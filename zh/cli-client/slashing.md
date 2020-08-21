---
order: 12
---

# slashing

## bhpcli tx slashing unjail

在PoS网络中，验证人的收益主要来自于staking抵押获利，但是若验证人不能保持在线，就会被当作一种作恶行为。系统会剥夺其作为验证人参与共识的资格。这样一来，其的状态会变成jailed，且投票权将立刻变为零。这种状态将持续一段时间。当jailed期结束，验证人节点的operator需要执行unjail操作来让节点的状态变为unjailed，再次成为验证人或者候选验证人。

解禁被监禁的验证人

Validator still jailed, cannot yet be unjailed
如果执行解禁操作的tx报错 Validator still jailed, cannot yet be unjailed，意味着该验证人节点还在监禁期内，不能被解禁。您可以查询 signing-info 获取监禁结束时间。

示例

```shell script
bhpcli tx slashing unjail [flags] --from mykey
```

输出

```shell script

```

