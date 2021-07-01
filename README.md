# OMGX Optimistic Rollup

OMGX is an Optimistic Rollup that combines the great open source work done by [Optimism](https://community.optimism.io/) with the research and development effort of the OMG team on swap-based onramp, fast exit and cross-chain bridging. We chose to build on Optimism because it is essentially a modified version of Ethereum, which makes it relatively easy to ensure EVM and Solidity compatibility, minimizing the efforts required to migrate smart contracts from L1 to L2.

OMGX is maintained by the [OMG](https://omg.network) and [Enya](https://enya.ai) teams.

{% hint style="info" %}
Our documentation is a work in progress. If you have questions or feel like something is missing check out our [Discord server](https://omg.eco/support) where we are actively responding, or [open an issue](https://github.com/omgnetwork) in the GitHub repo for this site.
{% endhint %}

### Direct Support

[Telegram](https://t.me/OMGXsupport)  
[Discord](https://omg.eco/support)

### Porting to OMGX and Optimism

**Do you want to deploy your smart contracts to an Optimistic Rollup?**

#### Choose your deployment stack

* [Truffle Deployment](https://github.com/omgnetwork/optimism/tree/develop/examples/truffle)
* [Waffle Deployment](https://github.com/omgnetwork/optimism/tree/develop/examples/waffle)
* [Hardhat Deployment](https://github.com/omgnetwork/optimism/tree/develop/examples/hardhat)

These examples take you through the complete process of deploying an application to **Optimistic Rollups**. It's not very different from simply deploying to Ethereum. We're working hard to make sure any changes to your software stack are relatively minimal.

### **Local OMGX**

[Start up a local OMGX system](https://github.com/omgnetwork/optimism/tree/develop/examples/hardhat)

Looking ahead, we anticipate the need for L2 solutions to not only scale Ethereum, but to augment it by expanding the capabilities of smart contracts through enabling complex off-chain computations. OMGX strives to be at the forefront of this evolution.

#### [Case Study: Porting to OMGX and Optimism](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md)

* [SUSHI](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#sushi)
* [0. Basics](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#0-basics)
* [1. No native ETH](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#1-no-native-eth)
* [2. Timing, `now`, and `block.timestamp`](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#2-timing---now---and--blocktimestamp)
* [3. Replace `chainid()` with `uint256 chainId = ___`](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#3-replace--chainid----with--uint256-chainid)
* [4. Update Depreciated Syntax](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#5-update-depreciated-syntax)
* [5. No tx.origin](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#6-no-txorigin)
* [6. TEST RESULTS: evm\_increaseTime and evm\_mine](https://github.com/omgnetwork/optimism/blob/develop/omgx_utilities/contracts-analyzer/PORTING.md#7-tests-results--all-good-except-evm-increasetime-and-evm-mine---workaround-pending)

### **Services**

[Rinkeby Testnet](https://rinkeby.omgx.network/)  
[Rinkeby Testnet Addresses](https://docs.omgx.network/developer-docs/rinkeby-testnet-addresses)  
[Rinkeby Metamask Settings](https://docs.omgx.network/developer-docs/rinkeby-metamask-settings)  
[Block Explorer Rinkeby](https://omg.eco/omgx-explorer-rinkeby)  
[Web Wallet Rinkeby](https://omg.eco/omgx-wallet-rinkeby)

### \*\*\*\*

