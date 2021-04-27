# Background

### Prerequisites

This guide is targeted at existing smart contract developers. It assumes a basic knowledge of Solidity development and the tools commonly used along with it. This includes writing our tests in JavaScript and using `yarn` for package management.

If you’re brand new to smart contract development, we recommend checking out some of the great resources for getting started, including the [Solidity docs](https://docs.soliditylang.org/en/latest/) themselves. The good news is, almost all of your new knowledge will eventually translate directly to building contracts for the OVM.

### Optimistic Rollups

At a very high level, the OVM is an optimistic implementation of the EVM. Transactions are executed on the OVM, which enables full EVM support, and the resulting state transitions are optimistically assumed to be valid. If someone does not believe a state transition is valid, they can submit a fraud proof to be verified by the EVM. As a result, the EVM only needs to execute computations when there is a dispute about a transaction’s legitimacy.

If you are unfamiliar with Optimistic Rollups and the OVM, these resources can help you learn more:

* [Optimistic Virtual Machine Alpha](https://medium.com/ethereum-optimism/optimistic-virtual-machine-alpha-cdf51f5d49e): A high level introduction and overview of the OVM
* [Optimism: Keeping Ethereum Half-Full](https://www.youtube.com/watch?v=eYeOW4ePgZE): Video introduction with some more depth
* [OVM Deep Dive](https://medium.com/ethereum-optimism/ovm-deep-dive-a300d1085f52): An in-depth article covering the challenges faced and solutions used by the OVM

### The Porting Process

Most existing Solidity projects will have three categories of changes required to get things running on the OVM.

1. **Tooling updates** - The OVM currently works with the [Waffle v3](https://getwaffle.io/) testing framework. If you’re using an older version of Waffle, you’ll need to upgrade. If you’re using a different test framework, you may need to migrate, though future versions of the OVM may support other frameworks in addition to Waffle. In this guide, we’ll upgrade Uniswap from Waffle v2 to Waffle v3.
2. **Test suite updates** - In addition to updating tooling, some tests themselves will need to be modified to account for minor differences in the EVM and the OVM. These include considerations such as gas differences and chain identifiers, which we’ll touch on in this guide. Our test harness will also have to support running on a local OVM node.
3. **Contract and compiler modifications** - Some differences between the EVM and the OVM precipitate changes to the Solidity contracts themselves or the compiler settings. In general, though, _most_ Solidity written for the EVM should “just work” for the OVM. While we’ll touch on some of the cases that might require contract changes, we’ll find that the Uniswap V2 contracts will compile and run unmodified.

