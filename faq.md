---
description: Frequently asked questions to get started on OMGx
---

# FAQ

## What is OMGx?

OMGx is an optimistic rollup that combines the great work done by the [Optimism team](https://community.optimism.io) with the research and development effort of the OMG team on fast exits and bridging in order to deliver the best user experience for moving liquidity into and out of OMGx. OMGx is currently available in private test net, and will enter public test net in a few weeks. We are ramping up developer support as quickly as we can to accommodate as many projects as possible on the test net.

## Is OMGx a side chain?

Nope. Side chains are their own blockchain systems with entirely separate consensus mechanisms. OMGx lives _inside_ of Ethereum as a series of smart contracts that are capable of executing Ethereum transactions. Whereas side chains rely on their own consensus mechanisms for security, OMGx instead relies on the security of Ethereum itself.

## What's the difference between OMGx and Ethereum?

OMGx is mostly identical to Ethereum. You can create and interact with Solidity smart contracts \(just like you would on Ethereum\) using the same wallet software you're already familiar with. Simply connect your wallet to an Optimistic Ethereum node and you're ready to go!

There are, however, a few very minor differences between Optimistic Ethereum and Ethereum that developers may have to account for. For instance, we disable infrequently used operations like `CALLCODE`. If you're a developer, these differences could potentially require a few minor tweaks to your contract code. Please refer to the [Complete EVM/OVM Comparison](https://community.optimism.io/docs/protocol/evm-comparison.html) for a full list of differences.

## Is OMGx safe?

Absolutely. Optimistic Rollups are safe as long as Ethereum itself is "live" \(not actively censoring transactions\). This security model is backed by a system of "fraud proofs," whereby users are paid to reveal bad transaction results published to the Optimistic Ethereum chain.

## Is there a delay moving assets from OMGx to Ethereum?

OMGx provides exit liquidity pools via Quasar and therefore does not have any exit periods unlike other L2 solutions. 

## What is Quasar?

OMGx provides exit liquidity pools via Quasar and therefore does not have any exit periods unlike other L2 solutions. 

## What are standard changes needed to run on OMGx?

SELFBALANCE \(=&gt;basically, remove, since no native ETH - treat ETH just like any other ERC20\) and ORIGIN \(`tx.origin` -&gt; `msg.sender`, unless there is a very very good reason for `tx.origin` in which case more complicated

###  <a id="what-s-the-difference-between-optimistic-ethereum-and-ethereum"></a>



