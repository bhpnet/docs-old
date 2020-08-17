---
order: 4
---

# bhpcli query

### bhpcli query tx
按交易Hash查询交易
```shell script
bhpcli query tx EB48FB302674F22B5C012A19D1FF2C67EA332DAAE5EA42C788FE1770BB7C6CFF
```

### bhpcli query block
在给定高度获取区块的验证数据。如果未指定高度，则将使用最新高度作为默认高度。
按高度获取区块信息
```
bhpcli query block <block-height>
```
获取最新的区块信息
```shell script
bhpcli query block 
```
