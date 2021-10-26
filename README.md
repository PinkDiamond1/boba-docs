---
description: Learn how to deploy your smart contracts on the Boba Network OVM
---

# Boba Network

{% hint style="info" %}
**!!! Important Update !!!**\
****\
****Boba Network will implement OVM 2.0 on Oct 28th, therefore there are no more custom changes needed for L1 contracts in order to deploy to Boba. Due to a chain re-genesis all contracts will have to be re-compiled and re-deployed. If you're preparing to deploy to Boba, make sure that your contracts run fine on any Ethereum L1 testnet. **No more code re-factoring is required to migrate to Boba Network with OVM 2.0**
{% endhint %}

Boba Network is an Optimistic Rollup that combines the great open source work done by [Optimism](https://community.optimism.io) with the research and development effort of the Enya & Boba team on swap-based onramp, fast exit and cross-chain bridging. We chose to build on Optimism because it is essentially a modified version of Ethereum, which makes it relatively easy to ensure EVM and Solidity compatibility, minimizing the efforts required to migrate smart contracts from L1 to L2.

Boba is maintained by the [OMG](https://omg.network) and [Enya](https://enya.ai) teams.

{% hint style="info" %}
Our documentation is a work in progress. If you have questions or feel like something is missing check out our [Discord server](https://omg.eco/support) where we are actively responding, or [open an issue](https://github.com/omgnetwork) in the GitHub repo for this site.
{% endhint %}

### Direct Support

[Telegram](https://t.me/bobadev)\
[Discord](https://omg.eco/support)

### Porting to Boba and Optimism

**Do you want to deploy your smart contracts to an Optimistic Rollup?**

#### Choose your deployment stack

{% content-ref url="developer-docs/examples/truffle-tutorial.md" %}
[truffle-tutorial.md](developer-docs/examples/truffle-tutorial.md)
{% endcontent-ref %}

{% content-ref url="developer-docs/examples/waffle-tutorial.md" %}
[waffle-tutorial.md](developer-docs/examples/waffle-tutorial.md)
{% endcontent-ref %}

{% content-ref url="developer-docs/examples/hardhat-tutorial.md" %}
[hardhat-tutorial.md](developer-docs/examples/hardhat-tutorial.md)
{% endcontent-ref %}

#### Fix possible errors step by step

{% content-ref url="developer-docs/examples/compiling-ovm.md" %}
[compiling-ovm.md](developer-docs/examples/compiling-ovm.md)
{% endcontent-ref %}

These examples take you through the complete process of deploying an application to **Optimistic Rollups**. It's not very different from simply deploying to Ethereum. We're working hard to make sure any changes to your software stack are relatively minimal.

### Set up The Graph

{% content-ref url="developer-docs/examples/the-graph.md" %}
[the-graph.md](developer-docs/examples/the-graph.md)
{% endcontent-ref %}

### Start up a local Boba

{% content-ref url="developer-docs/local-boba.md" %}
[local-boba.md](developer-docs/local-boba.md)
{% endcontent-ref %}

### **Access Boba Wallet Contracts**

{% content-ref url="developer-docs/boba-wallet-contracts.md" %}
[boba-wallet-contracts.md](developer-docs/boba-wallet-contracts.md)
{% endcontent-ref %}

### **Services**

[Rinkeby Testnet](https://rinkeby.omgx.network)\
[Rinkeby Testnet Addresses](https://docs.omgx.network/developer-docs/rinkeby-testnet-addresses)\
[Rinkeby Metamask Settings](https://docs.omgx.network/developer-docs/rinkeby-metamask-settings)\
[Block Explorer Rinkeby](https://omg.eco/omgx-explorer-rinkeby)\
[Web Wallet Rinkeby](https://omg.eco/omgx-wallet-rinkeby)

Looking ahead, we anticipate the need for L2 solutions to not only scale Ethereum, but to augment it by expanding the capabilities of smart contracts through enabling complex off-chain computations. Boba strives to be at the forefront of this evolution.
