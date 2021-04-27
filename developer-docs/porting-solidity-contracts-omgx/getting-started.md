# Getting Started

Start by cloning the [Uniswap V2 Core](https://github.com/Uniswap/uniswap-v2-core) repo. Install dependencies with `yarn` and run tests with `yarn test` to make sure all tests pass.

**NOTE**: This repo currently has a dependency resolution problem when installing with a lockfile. If a `yarn.lock` file exists, it is recommended to delete it before running `yarn`.

#### Package Upgrades <a id="Package-Upgrades"></a>

Because we want to support running tests on the EVM and OVM, let’s setup the tasks for these two test suites with the following changes to `package.json`:

```text
- "test": "mocha",
- "prepublishOnly": "yarn test"
+ "prepublishOnly": "yarn test",
+ "test:evm": "yarn compile && mocha",
+ "test:ovm": "yarn compile && echo OVM TESTS NOT IMPLEMENTED"
```

We’ll also need to update a few dependencies. Uniswap uses [Waffle](https://getwaffle.io/) v2 for their test suite, but the Optimism tooling requires Waffle v3. So let’s make one more change to `package.json`:

```text
- "ethereum-waffle": "^2.4.1",
+ "ethereum-waffle": "^3.2.1",
```

Run `yarn` to update our dependencies, and run the new `yarn test:evm` command to run the standard EVM test suite. **It will now fail with many errors**, due to breaking changes introduced with the Waffle upgrade.

In the next section, we’ll resolve the errors caused by these dependency upgrades, including our jump from v4 to v5 of the [ethers](https://docs.ethers.io/v5/) JavaScript library.

#### EVM Test Suite Updates <a id="EVM-Test-Suite-Updates"></a>

To resolve the breaking changes introduced by upgrading our test suite, make the changes detailed below. Note that this section is unrelated to the core changes required to deploy and test Uniswap on the OVM, but is still required.

_For more information on the breaking changes between Waffle v2 and v3, and between ethers v4 and v5, see the_ [_Waffle_](https://ethereum-waffle.readthedocs.io/en/latest/migration-guides.html#migration-from-waffle-2-5-to-waffle-3-0-0) _and_ [_ethers_](https://docs.ethers.io/v5/migration/ethers-v4/) _migration guides._

In `test/UniswapV2ERC20.spec.ts`:

```text
- import { MaxUint256 } from 'ethers/constants'
+ import { MaxUint256 } from '@ethersproject/constants'
- import { bigNumberify, hexlify, keccak256, defaultAbiCoder, toUtf8Bytes } from 'ethers/utils'
+ import { BigNumber } from '@ethersproject/bignumber'
+ import { defaultAbiCoder } from '@ethersproject/abi'
+ import { hexlify } from '@ethersproject/bytes'
+ import { keccak256 } from '@ethersproject/keccak256'
+ import { toUtf8Bytes } from '@ethersproject/strings'

const provider = new MockProvider({
+ ganacheOptions: {
    hardfork: 'istanbul',
    mnemonic: 'horn horn horn horn horn horn horn horn horn horn horn horn',
    gasLimit: 9999999
+ }    
})
  
- expect(await token.nonces(wallet.address)).to.eq(bigNumberify(1))
+ expect(await token.nonces(wallet.address)).to.eq(BigNumber.from(1))
```

In `test/shared/utilities.ts`:

```text
- import { Web3Provider } from 'ethers/providers'
+ import { Web3Provider } from '@ethersproject/providers'

- import { BigNumber, bigNumberify, getAddress, keccak256, defaultAbiCoder, toUtf8Bytes, solidityPack } from 'ethers/utils'
+ import { BigNumber } from '@ethersproject/bignumber'
+ import { defaultAbiCoder } from '@ethersproject/abi'
+ import { getAddress } from '@ethersproject/address';
+ import { keccak256 } from '@ethersproject/keccak256'
+ import { pack as solidityPack } from '@ethersproject/solidity'
+ import { toUtf8Bytes } from '@ethersproject/strings'

export function expandTo18Decimals(n: number): BigNumber {
-  return bigNumberify(n).mul(bigNumberify(10).pow(18))
+  return BigNumber.from(n).mul(BigNumber.from(10).pow(18))
}

- ;(provider._web3Provider.sendAsync as any)(
+ ;(provider.provider.sendAsync as any)(

export function encodePrice(reserve0: BigNumber, reserve1: BigNumber) {
-  return [reserve1.mul(bigNumberify(2).pow(112)).div(reserve0), reserve0.mul(bigNumberify(2).pow(112)).div(reserve1)]
+  return [reserve1.mul(BigNumber.from(2).pow(112)).div(reserve0), reserve0.mul(BigNumber.from(2).pow(112)).div(reserve1)]
}
```

In `test/UniswapV2Factory.spec.ts`:

```text
- import { AddressZero } from 'ethers/constants'
+ import { AddressZero } from '@ethersproject/constants'
- import { bigNumberify } from 'ethers/utils'
+ import { BigNumber } from '@ethersproject/bignumber';

const provider = new MockProvider({
+ ganacheOptions: {
    hardfork: 'istanbul',
    mnemonic: 'horn horn horn horn horn horn horn horn horn horn horn horn',
    gasLimit: 9999999
+ }    
})

- const loadFixture = createFixtureLoader(provider, [wallet, other])
+ const loadFixture = createFixtureLoader([wallet, other], provider)

await expect(factory.createPair(...tokens))
      .to.emit(factory, 'PairCreated')
-     .withArgs(TEST_ADDRESSES[0], TEST_ADDRESSES[1], create2Address, bigNumberify(1))
+     .withArgs(TEST_ADDRESSES[0], TEST_ADDRESSES[1], create2Address, BigNumber.from(1))
```

In `test/shared/fixtures.ts`:

```text
- import { Web3Provider } from 'ethers/providers'
+ import { Web3Provider } from '@ethersproject/providers'

- export async function factoryFixture(_: Web3Provider, [wallet]: Wallet[]): Promise<FactoryFixture> {
+ export async function factoryFixture([wallet]: Wallet[], _: Web3Provider): Promise<FactoryFixture> {
    const factory = await deployContract(wallet, UniswapV2Factory, [wallet.address], overrides)
    return { factory }
  }
  
- export async function pairFixture(provider: Web3Provider, [wallet]: Wallet[]): Promise<PairFixture> {
+ export async function pairFixture([wallet]: Wallet[], provider: Web3Provider): Promise<PairFixture> {
-   const { factory } = await factoryFixture(provider, [wallet])
+   const { factory } = await factoryFixture([wallet], provider)
    ...
    return { factory, token0, token1, pair }
  }
```

In `test/UniswapV2Pair.spec.ts`:

```text
- import { BigNumber, bigNumberify } from 'ethers/utils'
+ import { BigNumber } from '@ethersproject/bignumber'
- import { AddressZero } from 'ethers/constants'
+ import { AddressZero } from '@ethersproject/constants'

const provider = new MockProvider({
+ ganacheOptions: {
    hardfork: 'istanbul',
    mnemonic: 'horn horn horn horn horn horn horn horn horn horn horn horn',
    gasLimit: 9999999
+ }    
})

- const loadFixture = createFixtureLoader(provider, [wallet, other])
+ const loadFixture = createFixtureLoader([wallet, other], provider)

// Make the below change everywhere you see `bigNumberify`
- bigNumberify
+ BigNumber.from
```

You can now run `yarn test:evm` and all tests should pass!

