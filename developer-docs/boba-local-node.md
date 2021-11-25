---
description: Learn how to run a local Boba node
---

# Boba Local Node

## Running a Boba node (replica)

The [boba\_community/boba-node](https://github.com/omgnetwork/optimism-v2/tree/develop/boba\_community/boba-node) repo runs a replica of the Boba L2geth, which is useful for generating analytics for blockexplorers and other specialized use cases.

### Critical Considerations

1. Running a local Boba node (replica) does not allow you to mine ETH, OMG, or BOBA. There is no mining on L2.
2. If you are a developer and are looking for best possible rpc read data (lowest possible latency) you are **strongly advised** not to run your own node, but to use [**https://lightning-replica.boba.network**](https://lightning-replica.boba.network). This is an autoscaling rpc endpoint that speaks directly to the core Boba L2 geth.
3. The Boba L2 is (at present) a single proposer/sequencer system and the only way to write transactions is via [**https://mainnet.boba.network**](https://mainnet.boba.network). You cannot use a local node to write transactions.
4. If you are a developer and your application _does not need autoscaling and low latency_, and can tolerate sync delays, you can run your own Boba node (replica). This replica will try to follow the core L2 geth via data provided by Infura and [**https://lightning-replica.boba.network**](https://lightning-replica.boba.network), so it will necessarily lag behind [**https://lightning-replica.boba.network**](https://lightning-replica.boba.network).
5. If you are a developer, please design your rpc connectors in a resource efficient manner. Notably, calling `eth_getLogs(fromBlock: 0)` 1000 times per second serves no conceivable purpose since the Ethereum blocktime is 12 seconds and the Boba blocktime is > 1 second. All that does is to degrade your replica and trigger rate-limiting and/or IP blocking at [**https://lightning-replica.boba.network**](https://lightning-replica.boba.network). If you have what you think is a valid reason for calling `eth_getLogs(fromBlock: 0)` 1000 times per second we would love to hear from you what you think that reason is.

### Start Replica service

**Requirements**: you will need a command line and Docker. Before filing GitHub issues, please make sure Docker is installed and _running_.

**Open a terminal window**. Clone the project and install needed dependencies:

```
$ git clone git@github.com:omgnetwork/optimism-v2.git
$ cd optimism-v2
$ yarn install
$ yarn build
```

Then, add your Infura key to `boba_community/boba-node/docker-compose-replica.yaml`. If you do not have an Infura key, you can obtain one for free from [Infura](https://infura.io).

```
x-l1_rpc_dtl: &l1_rpc_dtl
  DATA_TRANSPORT_LAYER__L1_RPC_ENDPOINT: 'https://mainnet.infura.io/v3/YOUR_INFURA_KEY'

x-l1_rpc_geth: &l1_rpc_geth
  ETH1_HTTP: 'https://mainnet.infura.io/v3/YOUR_INFURA_KEY'
```

Next, build the packages:

```
docker-compose -f docker-compose-replica.yml build
```

Finally, bring up the services

```
docker-compose -f docker-compose-replica.yml up
```

The system will start and the `Replica L2 Geth` will begin to sync with the Boba L2. **The sync process can take 1/2 hour to complete**. During the sync, you will see the Replica gradually catch up with the Boba L2. Once it has synced up with the Boba L2, you can use the replica for rpc reads.
