---
description: Frequently asked questions to get started on Boba
---

# FAQ

## What is Boba Network?

Boba Network is an Optimistic Rollup that combines the great open source work done by [Optimism](https://community.optimism.io/) with the research and development effort of the Enya team on swap-based onramp, fast exit and cross-chain bridging.

We chose to build on Optimism because it is essentially a modified version of Ethereum, which makes it relatively easy to ensure EVM and Solidity compatibility, minimizing the efforts required to migrate smart contracts from L1 to L2.

## Is Boba a side chain?

Boba is not a side chain. Side chains are their own blockchain systems with entirely separate consensus mechanisms. Boba Network lives _inside_ of Ethereum as a series of smart contracts that are capable of executing Ethereum transactions. Whereas side chains rely on their own consensus mechanisms for security, Boba, as a child chain, instead relies on the security of Ethereum itself.

## What's the difference between Boba and Ethereum?

Boba is mostly identical to Ethereum. You can create and interact with Solidity smart contracts \(just like you would on Ethereum\) using the same wallet software you're already familiar with. 

## Is Boba safe?

Boba Network is just as safe as the Ethereum chain. Optimistic Rollups are safe as long as Ethereum itself is "live" \(not actively censoring transactions\). This security model is backed by a system of "fraud proofs," whereby users are paid to reveal bad transaction results published to the Boba Optimism based chain.

## Is there a delay moving assets from Boba to Ethereum?

We developed a swap-based mechanism to deliver a smooth user experience for moving funds across chains, whether you are going from L1 to L2, L2 to L1, or between two L2s \(as long as they are both EVM-compatible\). 

The users who choose to take advantage of this benefit will pay a small convenience fee that is shared among the liquidity providers of the pools backing the swaps. Acting as liquidity providers as described above is just the first of several staking opportunities we will roll out to the community. The higher level goal is to encourage broad-based participation in the operations and governance of Boba. As the only tokenized EVM-compatible L2, we are in a unique position to use our token responsibly for the long-term sustainability of the network.

## How are developers incentivized to build on Boba? <a id="f274"></a>

While the high gas fees of Ethereum itself act as a pretty strong incentive for developers to move to layer 2s in general. As for Boba specifically, our pitch to them is: this is not just about scaling Ethereum. Once you’re on Boba Network, we’re also creating this amazing future for you. You’ll be able to tap into more advanced compute capabilities that are not available to you today. We also have plans to create an ecosystem fund to incentivize some of the early-stage projects who are just starting out but doing something really interesting. It’s going to take some time to put something like that together. That’s in our plans.

## What is the advantage of Boba versus other scaling solutions? <a id="038a"></a>

At a high level, first of all, why did we choose to go down this route of scaling Ethereum through building on Optimism? Because Optimism itself is a modified version of Ethereum, which makes it the approach that is easiest to stay in sync with Ethereum going forward, as Ethereum itself evolves. That forward compatibility, with as little engineering investment as possible, is important to us because it's important to developers. Other approaches that require a much heavier engineering effort to stay in sync with Ethereum would necessarily mean that there will be a longer gap between changes in Ethereum itself, to those changes being emulated in these other L2 approaches.

Why would someone use Boba versus Optimism? A number of reasons. First, we support Fast Exit out of the gate. The traditional seven-day exit window still exists. If you don’t want to pay the convenience fee and you’re willing to wait seven days to withdraw your funds, you can. But if you don’t want to wait, you can pay the convenience fees and withdraw your funds right away. We’ve designed that mechanism in such a way that it’s market-driven, namely, we’re not dependent on any one specific partner to fund these pools. It’s really up to the community and the market. If there’s demand for additional currency pairs for these swaps across the L1-L2 boundaries, the mechanism is there for a liquidity provider to step up and provide those swaps and make those available to the broader community.

## The incentive contract for verification proofs is disabled

In the current release of the Boba Network protocol, there may be rare cases in which the Sequencer submits a state root \(transaction result\) which is invalid and therefore could be challenged. As a result, we have not yet deployed the [Bond Manager ](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/verification/OVM_BondManager.sol)contract which compensates Verifier nodes for gas spent when submitting state root challenges. Verifier nodes in a default configuration do not run the [TypeScript service which submits challenges ](https://github.com/ethereum-optimism/optimism/blob/8d67991aba584c1703692ea46273ea8a1ef45f56/packages/contracts/test/contracts/OVM/verification/OVM_FraudVerifier.spec.ts)in the event of mismatched state roots. Additionally, our upgrade keys have the ability to directly remove state roots without going through an uncompensated state root challenge.



