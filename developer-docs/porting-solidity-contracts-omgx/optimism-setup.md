# Optimism Setup

### Dependencies

Now that we’ve migrated to Waffle v3, let’s install the tools we’ll need to work with Optimism. These tools require Node v10. We recommend using a Node version manager, such as [Volta](https://docs.volta.sh/guide/getting-started).

If you are using Volta, run `volta pin node@10 && volta pin yarn`. This will automatically update `package.json` to specify the required node version. \(Note that if we only pin node, but not yarn, volta will complain\).

There’s two Optimism packages we’ll need:

* Typically you compile contracts to be deployed on the EVM, but here we’ll use [`@eth-optimism/solc`](https://www.npmjs.com/package/@eth-optimism/solc) to compile the contracts for the OVM. The Uniswap contracts use Solidity 0.5.16, so when installing this package we must specify that to ensure we get the right version. From the npm page, we see the most recent version of 0.5.16 is called `0.5.16-alpha.7`
* [`@eth-optimism/ovm-toolchain`](https://www.npmjs.com/package/@eth-optimism/ovm-toolchain) provides wrappers and plugins for common tools. For example, it provides custom implementations of Ganache, Waffle V3, and Buidler which are compatible with the OVM. _\(Note: even though Buidler was_ [_replaced by Hardhat_](https://medium.com/nomic-labs-blog/buidler-has-evolved-introducing-hardhat-4bccd13bc931)_, this package is not yet compatible with Hardhat\)._

Let’s install both of these with:

```text
$ yarn add --dev @eth-optimism/solc@0.5.16-alpha.7 @eth-optimism/ovm-toolchain 
```

### Compiling for the OVM

We’re just about ready to compile our contracts for the OVM! Let’s prepare a few final things first.

When compiling, we need to make sure to use the OVM compiler we just installed. The existing compile script reads the EVM compiler settings from `.waffle.json`. We’ll create another file to manage OVM compiler settings, called `.waffle-ovm.json`, and point it to the OVM compiler as follows:

```text
{
  "compilerVersion": "./node_modules/@eth-optimism/solc"
}
```

All compiler settings can be specified in this file, but for now this will be the only setting.

Finally, let’s setup our OVM `package.json` scripts. To be explicit, we’ll append `:evm` to the existing scripts in `package.json`, and add a few new `:ovm` ones. The result should look like this:

```text
"scripts": {
  "lint": "yarn prettier ./test/*.ts --check",
  "lint:fix": "yarn prettier ./test/*.ts --write",
  "clean": "rimraf ./build/",
  "precompile:evm": "yarn clean",
  "precompile:ovm": "yarn clean",
  "compile:evm": "waffle .waffle.json",
  "compile:ovm": "waffle .waffle-ovm.json",
  "pretest:evm": "yarn compile:evm",
  "pretest:ovm": "yarn compile:ovm",
  "test:evm": "mocha",
  "test:ovm": "echo OVM TESTS NOT IMPLEMENTED",
  "prepublishOnly": "yarn test:evm && yarn test:ovm"
},
```

Let’s make sure things are working. Check that all tests still pass when running `yarn test:evm`.

Now run `yarn test:ovm`. This won’t run any tests yet, but it will compile the contracts for the OVM. If you did everything correctly, you should see `OVM TESTS NOT IMPLEMENTED` in the terminal, along with two compiler warnings:

```text
contracts/UniswapV2Factory.sol:15:46: Warning: OVM: Taking arguments in constructor may result in unsafe code.
    constructor(address _feeToSetter) public {
                                             ^

contracts/test/ERC20.sol:6:43: Warning: OVM: Taking arguments in constructor may result in unsafe code.
    constructor(uint _totalSupply) public {
```

These are new compiler warnings specific to the OVM, so let’s understand what’s going on here.

### OVM vs. EVM: Constructor Arguments

In this section we’ll look at the warnings we saw above when compiling for the OVM. While, this guide focuses on some of the OVM vs. EVM differences that specifically impact Uniswap V2, you can read about other OVM vs. EVM differences [here](https://hackmd.io/elr0znYORiOMSTtfPJVAaA?view) and [here](https://hackmd.io/Inuu-T_UTsSXnzGtrLR8gA).

`UniswapV2Factory.sol` is responsible for deploying new pairs, and its constructor takes one argument which the compiler warns may result in unsafe code. \(Learn more about this argument in the Uniswap [docs](https://uniswap.org/docs/v2/smart-contracts/factory/#feetosetter)\). This warning is emitted because not all EVM opcodes are supported in the OVM! As a result, when your encoded constructor arguments are added to the contract bytecode, there is a chance this encoding contains unsupported opcodes. If it does, contract deployment will fail. As explained [here](https://hackmd.io/Inuu-T_UTsSXnzGtrLR8gA#Constructor-Parameters-may-be-Unsafe):

> Only a few opcodes are banned, so this is a relatively unlikely event. However, if you have a strong requirement that your contract can be successfully deployed multiple times, with absolutely any parameters, it is problematic. In that case, you will need to remove constructor arguments and replace them with an `initialize(...)` method which can only be called once on the deployed code.

Because the `UniswapV2Factory` constructor simply saves the constructor argument to storage, we can safely ignore the warning here. Similarly, the test `ERC20` contract only mints `_totalSupply` tokens at construction, so this operation should also be safe.

### OVM vs. EVM: Block Timestamps

The difference between block timestamps on the OVM and the EVM don’t naturally surface during the migration process, but Uniswap does rely on `block.timestamp` in a few places so it’s important to mention. If your contracts rely on `block.timestamp`, you’ll want to understand these differences and consider their impact carefully.

The OVM does not have blocks; it just has an ordered list of transactions. As such,`block.timestamp` returns the “Latest L1 Timestamp”, which corresponds to the timestamp of the last L1 block in which a rollup batch was posted. Be aware that this will lag behind the EVM’s `block.timestamp` by about 1–10 minutes.

Uniswap uses `block.timestamp` for a few different purposes, so let’s see how the behavior of each changes as a result of the differing functionality.

#### **Trade Deadlines, permit signatures, and auctions**

When executing a trade you can set a deadline, and if your transaction is mined after this deadline the transaction will be reverted. \(This actually occurs in the [router contract](https://github.com/Uniswap/uniswap-v2-periphery/blob/master/contracts/UniswapV2Router02.sol), outside of the repository we cloned, but is worth mentioning anyway\). The OVM timestamp lags behind the EVM, so it’s possible that your OVM Uniswap trades execute up 10 minutes after your specified deadline.

Similarly, you can use the `permit` method to give approval to an address to spend your LP tokens with just a signature. These signatures contain a deadline, and the approval must be sent before that deadline. Just like with trade deadlines, this means it’s possible that this approval is executed after your desired deadline.

One other major area timestamps come into play is with bid and auction durations. [MakerDAO](https://makerdao.com/en/) used have to bid durations of 10 minutes, and it’s important to know that **due to the timestamp lag, these 10 minute durations would be unsafe on the OVM!** Note that these durations have since [increased](https://blog.makerdao.com/recent-market-activity-and-next-steps/) since it turns out they weren’t ideal on L1 either.

#### **Price Oracle**

Uniswap uses a [cumulative-price variable](https://uniswap.org/docs/v2/core-concepts/oracles/) to enable developers to build safe Oracles on top of Uniswap. This variable tracks the “sum of the Uniswap price for every second in the entire history of the contract”, meaning it must know the current time whenever it’s updated. Because timestamps lag, the resulting price from an OVM Uniswap oracle will likely vary a bit from an EVM Uniswap oracle that had identical historical states.

###  <a id="Testing-on-the-OVM"></a>

