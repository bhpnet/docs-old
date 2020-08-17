---
order: 3
---

# Join The Testnet
有关最新测试网络的信息，请参阅[测试网络存储库](#TODO)，包括要使用的正确版本的BHP以及有关创世纪文件的详细信息。

:::tip
Requirements: [install bhp](install-bhp.md)
:::

## Run a Full Node

```bash
# initialize node configurations
bhpd init moniker=<your-custom-name> --chain-id=testing

# download mainnet public config.toml and genesis.json
curl -o ~/.bhpd/config/config.toml https://raw.githubusercontent.com/bhpnet/launch/master/testnet/config.toml
curl -o ~/.bhpd/config/genesis.json https://raw.githubusercontent.com/bhpnet/launch/master/testnet/genesis.json

# start the node (you can also use "nohup" or "systemd" to run in the background)
bhpd start
```

:::tip
You may see some connection errors, it does not matter, the P2P network is trying to find available connections
testnet/config.toml
Try to add some of the [Community Peers](https://github.com/bhpnet/launch/blob/master/testnet/community-peers.md) to `persistent_peers` in the config.toml
:::

:::tip
It will take a long time to sync from scratch, you can also download the [testnet data snapshot](#TODO) to reduce the time spent on synchronization
:::

## Upgrade to Validator Node

### Create a Wallet

You can [create a new wallet](../cli-client/keys.md#create-a-new-key) or [import an existing one](../cli-client/keys.md#recover-an-existing-key-from-seed-phrase), then get some IRIS from the exchanges or anywhere else into the wallet you just created, .e.g.

```bash
# create a new wallet
bhpcli keys add <key-name>
```

:::warning
write the seed phrase in a safe place! It is the only way to recover your account if you ever forget your password.
:::

### Confirm your node has caught-up

```bash
# if you have not installed jq
# apt-get update && apt-get install -y jq

# if the output is false, means your node has caught-up
bhpcli status | jq .sync_info.catching_up
```

### Create Validator

Only if your node has caught-up, you can run the following command to upgrade your node to be a validator.

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
**Important**

Backup the `config` directory located in your bhp home (default ~/.bhpd/) carefully! It is the only way to recover your validator.
:::

If there are no errors, then your node is now a validator or candidate (depending on whether your delegation amount is in the top 21)
