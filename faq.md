---
description: Frequently asked questions to get started on OMGX
---

# FAQ

## What is OMGX?

OMGX is an Optimistic Rollup that combines the great open source work done by [Optimism](https://community.optimism.io/) with the research and development effort of the OMG team on swap-based onramp, fast exit and cross-chain bridging.

We chose to build on Optimism because it is essentially a modified version of Ethereum, which makes it relatively easy to ensure EVM and Solidity compatibility, minimizing the efforts required to migrate smart contracts from L1 to L2.

## Is OMGX a side chain?

OMGX is not a side chain. Side chains are their own blockchain systems with entirely separate consensus mechanisms. OMGX lives _inside_ of Ethereum as a series of smart contracts that are capable of executing Ethereum transactions. Whereas side chains rely on their own consensus mechanisms for security, OMGX, as a child chain, instead relies on the security of Ethereum itself.

## What's the difference between OMGX and Ethereum?

OMGX is mostly identical to Ethereum. You can create and interact with Solidity smart contracts \(just like you would on Ethereum\) using the same wallet software you're already familiar with. 

## Is OMGX safe?

OMGX is just as safe as the Ethereum chain. Optimistic Rollups are safe as long as Ethereum itself is "live" \(not actively censoring transactions\). This security model is backed by a system of "fraud proofs," whereby users are paid to reveal bad transaction results published to the OMGX Optimism based chain.

## Is there a delay moving assets from OMGX to Ethereum?

Designed with the end users in mind and based on our experience in developing Quasar, the fast-exit option for OMG Plasma, we developed a swap-based mechanism to deliver a smooth user experience for moving funds across chains, whether you are going from L1 to L2, L2 to L1, or between two L2s \(as long as they are both EVM-compatible\). 

The users who choose to take advantage of this benefit will pay a small convenience fee that is shared among the liquidity providers of the pools backing the swaps. Acting as liquidity providers as described above is just the first of several staking opportunities we will roll out to the community. The higher level goal is to encourage broad-based participation in the operations and governance of OMGX. As the only tokenized EVM-compatible L2, we are in a unique position to use our token responsibly for the long-term sustainability of the network.

## How are developers incentivized to build on OMGX? <a id="f274"></a>

Well the high gas fees of Ethereum itself act as a pretty strong incentive for developers to move to layer 2s in general. As for OMGX specifically, our pitch to them is: this is not just about scaling Ethereum. Once you’re on OMGX, we’re also creating this amazing future for you. You’ll be able to tap into more advanced compute capabilities that are not available to you today. We also have plans to create an ecosystem fund to incentivize some of the early-stage projects who are just starting out but doing something really interesting. It’s going to take some time to put something like that together. That’s in our plans.

## How will fees be calculated on OMGX? <a id="330e"></a>

Based on what we know and based on what we can tell, a realistic number is probably a 50 X saving compared to the mainchain. Numbers that are bandied about that are much larger than that, say 1000 X or 2000 X cost reduction, we are currently unable to recapitulate on how those numbers are arrived at. So we don’t see that. As best as we can tell, we’re looking at about 50 X cost reduction compared to the mainchain.

## Does that mean that there could be a potential for that to improve over time? <a id="43c5"></a>

Yes, absolutely. Part of that, without wasting people’s time on the details, part of has to do with the extent to which certain data elements can be compressed as they’re injected into the CTC on the L1. That’s easily imaginable to have another 2 X coming off of that, which would bring the total savings up to about 100 X. But in terms of our mainnet, that we’re working on, we’re looking at 50 X as we get started.

## Will there be a fee token on OMGX? <a id="ba3e"></a>

Currently, on the testnet, the fee token is ETH because that’s the native token for [Optimism](https://optimism.io/). Our plans are to support the OMG token as the fee token on OMGX. Ideally, we’d like to do both natively, but supporting two native tokens is going to require non-trivial engineering work. So we’re prioritizing supporting the OMG token as the fee token initially. The longer-term vision is to make it as easy as possible for OMGX to support multiple fee tokens or the popular ones.

## What is the theoretical TPS for OMGX? <a id="42ca"></a>

The 50 X number is also in the right ballpark here. Both for cost savings, and our TPS relative to L1, we’re talking about 50 X improvement.

## What kind of incentivized role do OMG stakers have? <a id="b4dd"></a>

To answer the question we need to understand the differences between OMGX and ETH2, where you have stakers. OMGX is not a proof-of-stake protocol, right? So the “stakers” on OMGX would not be performing the same kind of role as stakers on ETH2. From the get-go, we’re going to begin by offering opportunities for the community to participate in the liquidity pools that underpin the swap-based mechanism for moving funds between L1 and L2. It’s a form of liquidity staking and the stakers in these pools will receive a portion of the convenience fees that users pay into, using these features. That’s the initial plan for staking on OMGX, a form of liquidity staking. Over time, we’ll work on making various components of OMGX easier to run so that there’s broad community participation in the operation of the network, and operating these nodes will come with rewards.

## Will there be any requirements for $OMG staking? <a id="00a1"></a>

Just to provide context here. It is incredibly important for decentralized platforms and apps to have the property of being accessible to normal people with normal computers to normal wallets. If we end up with a system where only really wealthy people can participate and make more money, then we’ve failed. It is incredibly important to us to make sure that anyone can contribute to the system, and can also benefit from the system. The last thing we want is some absurdly high requirement to participate. That’s not only true for some kind of minimum ETH requirements, that also influences how we’re trying to design the code and also the deployment of the system. We want to make it as intelligible and as accessible to the largest number of people possible. That influences what we do on the technology side, but also influences how we design systems for people to get engaged and participate, and also benefit from the system. The simple answer here is that we will do absolutely everything we can to avoid any kind of significant barriers to entry, like very high requirements, or something like that.

## Will there be cross-chain liquidity with other chains? <a id="261a"></a>

Our strategy is to build as many bridges as possible. The idea is to create the best user experience out there. I think users will want to have as much flexibility as possible to move their funds around wherever they want these funds to be and wherever their favorite projects happen to be running. As to who will have to build that connection, if the platforms are open and they’ve gone mainnet and anyone could deploy contracts on them, we can just deploy our swap-based movement contracts on both sides and enable these bridges.

In terms of building them. There’s really good news in the sense that if both L2s are EVM compatible, then once the code has been written for a bridge, then nothing prevents that same bridge from being used to bridge across many different L2s. So it’s not like a custom bridge has to be developed for every different L2 to L2. Once the code has been written it can be very readily deployed across many of the L2s. Assuming they are truly EVM compatible, much of the work really only needs to be done once.

## How will OMG Plasma be utilized going forward? <a id="c586"></a>

OMG Plasma has really important advantages and technical features that are currently unrivaled. For example, with OMG Plasma, in principle, we can do 64,000 transactions per block. Plasma has been out there for a while and that means we have considerably more trust in the performance of Plasma relative to solutions that simply haven’t been battle-tested. The way we’re thinking about Plasma right now is as one of two key choices that we want to offer to people. Because there’s no conflict between a plasma as architecture and OMGX, they’re complimentary. As a baseline, one of our engineering goals from the user perspective is to make it as easy as possible to select the correct technology for a particular transaction. From a wallet perspective, our design goal is when the user interacts with a wallet, that perhaps even on a per transaction basis, they’re able to pick and choose where they would like to operate. That of course means we need bridges that make it super easy to move back and forth from Plasma to OMGX and then OMGX to other places. This is not so much about telling people what they should do. This is about making it incredibly easy for people, as they interact with OMG in general, to choose whatever underlying platform they want for a particular transaction or for a particular type of operation.

## Wen OMGX Mainnet? <a id="1935"></a>

All of us are doing absolutely everything we can to have the mainnet launch as quickly as possible. However, it is important to make sure that everything works right. The important consideration here is not only OMGX as a technology, it also has to do with all the other things that need to work right. That’s on the deployment and on the CI/CD end and all the infrastructure that’s needed to support the entire system. So right now we’re spending a lot of time on the deployment side of the story, because we want the entire system to perform gracefully in the limit of a large number of transactions. The only way we can convince ourselves that the system is ready is to test it. We do not have a specific date. We’re trying to do this as quickly as possible, consistent with the system scaling up properly when it’s hammered by use, and also consistent with the system being safe. We’re working on a very aggressive timescale and we are strong believers that the best way to mature a technology, and the best way to test the technology, is to get it out in the open and have people use it. So what we’re not going to do is fiddle with it endlessly and try and make it perfect, and fiddle more and try to make it perfect, and delay, delay, delay. So we’re finding a balance between convincing ourselves that it does what we want it to do in the limit of a large number of transactions. But we also see great benefit in having the technology out there so people can use it and try it out. We are trying to be as aggressive here as we possibly can.

## What is the advantage of OMGX versus other scaling solutions? <a id="038a"></a>

At a high level, first of all, why did we choose to go down this route of scaling Ethereum through building on Optimism? Because Optimism itself is a modified version of Ethereum, which makes it the approach that is easiest to stay in sync with Ethereum going forward, as Ethereum itself evolves. That forward compatibility, with as little engineering investment as possible, is important to us because it's important to developers. Other approaches that require a much heavier engineering effort to stay in sync with Ethereum would necessarily mean that there will be a longer gap between changes in Ethereum itself, to those changes being emulated in these other L2 approaches.

Why would someone use OMGX versus Optimism? A number of reasons. First, we support Fast Exit out of the gate. The traditional seven-day exit window still exists. If you don’t want to pay the convenience fee and you’re willing to wait seven days to withdraw your funds, you can. But if you don’t want to wait, you can pay the convenience fees and withdraw your funds right away. We’ve designed that mechanism in such a way that it’s market-driven, namely, we’re not dependent on any one specific partner to fund these pools. It’s really up to the community and the market. If there’s demand for additional currency pairs for these swaps across the L1-L2 boundaries, the mechanism is there for a liquidity provider to step up and provide those swaps and make those available to the broader community.

Secondly, we are going to be enhancing OMGX with these off-chain compute capabilities that we’ll cover in later questions. Of course, other chains will have their own innovations as well, but this is something that is unique to us as far as we can tell. It’s important to enable these off-chain compute capabilities because they make it possible for smart contracts to tap into advances in computer science that have happened over the last two decades that non-smart contract developers have gotten used to. This speaks to the point that Jan brought up earlier. It’s important for us to broaden the participation of the community as much as possible. This means enabling as many developers to build on OMGX as we can.

## Is OMG building any of its own dapps for OMGX? Will there be a dapp incubator? <a id="84fa"></a>

I love the question that you added at the end. Currently, we’re really focused on bringing as many projects to OMGX as possible, versus building our own. We wouldn’t rule anything out, but really the current focus is developer adoption. In terms of creating an incubator, that’s something that we might do down the road, but currently, there are so many developers that are hurting from high gas fees on the main chain. There’s such high demand for a scalable cost-effective solution. That’s our main focus right now.

## It is said that OMGX is the only tokenized EVM-compatible L2. Why is this an advantage? <a id="baf9"></a>

We believe it’s important for the OMG community to see that the token has real use on this Network. The OMG token also gives us the ability to create tokenomics and incentive systems that are not dependent on ETH itself. It’s a layer of flexibility that we believe will be beneficial to OMG token holders.

## What is the long-term strategy for OMGX? <a id="6d51"></a>

The long-term strategy for OMGX is to not just scale Ethereum, but also augment Ethereum. What we mean by that is giving smart contract developers the ability to invoke algorithms off-chain and incorporate the results from those algorithms into their smart contracts. This opens up the possibilities of using things like machine learning classifiers and multi-factor payoff models, or lending models that have been trained in the “real world”, and take advantage of them in smart contracts.

## What does the timeline look for this product? <a id="a6f8"></a>

I think this is what Jan addressed a little earlier. We don’t have a specific date for going mainnet yet. Our approach is to launch a mainnet as soon as we can. We need to make sure that the whole infrastructure behind it is in place to be able to meet demands and be able to scale with the demand. As Jan mentioned earlier, what we’re not going to do is keep fiddling with it until it’s perfect. We’re going to move as quickly as we can, but also make sure that any funds that are deposited on OMGX are going to be safe. That’s a key priority for us.

## Will fast exit liquidity pools be siloed? <a id="64cc"></a>

There are two parts to that. There’s the underlying technical infrastructure. Then there is the user experience as they use the wallet. There’s a lot we can do at the level of the wallet to make the user experience as seamless as possible. For example, you can imagine a system where you have a slider bar that goes from 0% to 100%. With the slider bar, a user could indicate their preference about how they want to allocate funds across multiple liquidity pools. That’s more of a UI question. From an underlying smart contract perspective, there do need to be well-defined liquidity pools that live on the different chains or L2s. There’s no obvious way to just have one liquidity pool. There does have to be specifically allocated pools on all the chains that want to use that type of bridge. But then again, from a wallet UI perspective, we can certainly try to make that as seamless as possible.

## Has the staking model been updated to fix the single-operator system? <a id="8adb"></a>

That’s a really, really important point. If we’re serious about censorship resistance. If we’re serious about distributed anything. It’s got to have the property of actually being distributed. So whenever we have to resort to centralized anything, then that’s an immediate problem that is very much on our radar. There are very practical issues in terms of what we can decentralize first and most readily. Our priority right now is building a system where a large number of people are incentivized to run verifiers and fraud provers because that’s the very first thing. If we have a system with a centralized sequencer and no one is verifying things, and no one is running a fraud prover, then we are in a place we don’t want to be at all. The zero-order thing right now is to make it very easy for a large number of people to run verifiers that pay close attention to what the sequencer is doing and having fraud provers. That’s step one. Step two is then to start relaxing constraints on the unitary sequencer. There’s some very sort of practical scaling issues that arise when relaxing the single sequencer constraint. So the immediate goal is to make it as easy as possible for a large number of people to run validators or verifiers, and also run fraud provers.

## What’s the difference between the OMGX platform and what Optimism is working on? <a id="6ea8"></a>

In terms of differences between OMGX and Optimism, one is our fast-exit mechanism available from day one is swap-based, community-driven, and market-based, meaning the market will be able to add additional currency pairs to swap between the L1/L2 boundary. It is not dictated by us, or by any one project that we partner with. Another key difference is, going forward, we are going to be adding the ability for smart contract developers to take advantage of more complex algorithms off-chain and integrate those results back into the smart contracts.

One additional thing to add, and I’m speaking to everyone on this call right now. We are not some little island in some lab in some basement all by ourselves trying to develop these technologies. We are extremely eager to have the community participate. We are extremely eager to get feedback. We’re extremely eager to get questions. We are extremely eager to get issues added to the Github. So just personally speaking, and I’m sure this is true for Alan and everyone else on our side. Don’t be scared to ask questions. Don’t be scared to tell us what we need to do differently, because a large part of why we’re actually doing this reflects our belief in a decentralized way of doing things. That fundamentally involves this being a distributed community project. So to make that absolutely clear. If you have questions, if you have suggestions, if you want to get engaged, this is something that’s incredibly important to us. Every single person who comes to us and says: “Hey, you guys are complete fools. You should actually be doing it this other way.” We love that kind of feedback. I’d like to encourage every single person on this call. If you have ideas, if you want to get engaged, if you want to help us in any way, please let us know. Right away! This is something we are very excited and happy about. Please do that as much as you can, don’t be scared.

## Could you provide more details about how your swap-based mechanism is different from Optimism? You also said something about doing compute off-chain and then connecting it to L2. Could you explain? <a id="4236"></a>

If you’re familiar with AMM DEXs, what we’ve implemented to enable L1 to L2 movement is similar to that model. Optimism will have their own way of dealing with the exit window, the challenge period. They’ve spoken about that. We believe our model is more community-driven and market-driven, and it enables broad-based participation. You’re able to participate as a liquidity provider and earn part of the fees. We think that’s beneficial to the community. I’ll let Jan speak to the off-chain compute question.

In terms of the swap, as Alan said, the underlying principle is 100% equivalent to what, for example, Uniswap does or Sushi does where you’re able to swap from one token to another. The only difference here is that the swap pair is something like ETH and oETH. So instead of swapping tokens on L1, what you’re doing is swapping across a chain boundary. But the underlying equations are the same. The general ideas are the same. It’s just that you’re swapping across that boundary. In terms of the off-chain compute. That’s probably a longer conversation, but the central idea is that right now there’s a very significant gap between the types of computations you can do on L1 versus the types of computations that people are used to doing these days. Insurance companies, banks, normal businesses, they’re all used to being able to do very sophisticated computations at specific endpoints. They don’t even think about that. It’s only when they get exposed to the blockchain world that some have to get used to extremely limited, extremely slow, and extremely costly compute. Typically that surprises them because they take it for granted that they should be able to do very sophisticated things at very, very low cost and very quickly.

From a strategic perspective, if that gap continues to exist, that poses a very significant barrier to entry. If I’m a normal bank, if I’m a normal insurance company or if I’m a normal business and I say, “Oh, I’m interested in using a distributed approach.” And then I tell you, you know, here are all the things you can’t do. That’s a really big barrier to future growth of normal companies operating on blockchains. That’s why we think it’s very important to make it as easy as possible for traditional businesses to take advantage of existing compute infrastructure from smart contracts that they deploy. It’s not necessarily about supporting new types of compute, it’s to make it as easy as possible for smart contracts to trigger off-chain compute at established endpoints that are quoted and deployed on many many different platforms. For practical purposes, we’re focusing on AWS Lambda. So the zero-order goal is to make it possible for daemons that are watching smart contract interactions to trigger AWS Lambda events. Then to have the compute results from those events be pushed back into smart contracts at a later time.

## What is the main thing OMG is doing, that Polygon is not doing, to be called a Layer 2? <a id="fcf3"></a>

One of the key distinctions for a Layer 2 is that it derives security from Ethereum itself and therefore we don’t need to run our own suite of validators because we’re not an independent side chain or proof of stake protocol. Everything is secured by Ethereum. Now there’s no free lunch in a world. We need to pay Ethereum for that security, but that’s the trade. I can’t comment on Polygon. They would need you to speak to their own architecture. But if you look at how low their fees are and the fact that they have their own set of validators, it’s a fundamentally different architecture altogether.

## What is the token going to be used for on this platform? Can contracts on OMGX call other contracts? <a id="4d03"></a>

We are working on the ability to support using OMG tokens to pay for gas on OMGX. Currently, it is still ETH and it takes work to make that change, but we’re working on it. In terms of whether you can call other contracts. Absolutely! OMGX is just a modified version of Ethereum. What you can do on L1, you can do on L2 as well. There are just a few opcodes that are not readily supported because those opcodes don’t have the equivalent concept on L2. But those changes are minor and they are covered by the [porting guide](https://github.com/omgnetwork/omgx_contracts-analyzer) that we have published on Github.

## How excited are you for the future of OMG? <a id="93d2"></a>

Well, we’re so excited that this is all we’re working on these days.

We are super excited. I’ve now been through three crypto winters. I’ve been in the space for a while. Based on what I hear from my friends in other parts of the Internet world, in other parts of the “normal business” world, I am more excited than I’ve ever been for the overall possibility or overall opportunity for the distributed world. I think the L2s are a really really critical part of that. It’s almost like a log jam has been broken or a door has been opened. Now finally, all the pieces are coming together that allow us to build on decentralized apps and exchanges that work at scale. We have a very clear technology path to that and the possibility of building something that a very large number of people will be able to use — and will be easy to use — is just incredibly exciting to us. We’re totally in love with this, at least speaking for the people I work closely with.

