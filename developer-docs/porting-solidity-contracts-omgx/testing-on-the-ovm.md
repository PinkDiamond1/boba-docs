# Testing on the OVM

### Testing on the OVM <a id="Testing-on-the-OVM"></a>

Our final step is getting our tests working for the OVM. If we make the following change to the `test:ovm` script, so it runs mocha, **there will be plenty of test failures**. Try it!

```text
  "test:evm": "mocha",
- "test:ovm": "echo OVM TESTS NOT IMPLEMENTED",
+ "test:ovm": "mocha",
```

Let’s fix those failures using the `@eth-optimism/ovm-toolchain` package we installed earlier. Remember, this package provides Optimism-specific implementations of Ganache, Waffle, and Buidler.

We’ll need to setup the tests differently depending on whether we are running them against the EVM or OVM, so we’ll use an environment variable called `MODE` to do this. If `MODE=OVM`, we’ll use our new OVM settings, otherwise we’ll fallback to the standard EVM configuration. We can do this easily with the following change in `package.json`:

```text
- "test:ovm": "mocha",
+ "test:ovm": "export MODE=OVM && mocha",
```

Next we’ll create a file in `test/shared` called `config.ts`, where we’ll handle all chain-specific logic. Leave this blank for now, we’ll adjust it in the next section.

#### Provider and Chain ID Fixes <a id="Provider-and-Chain-ID-Fixes"></a>

Waffle starts a local node when a new provider instance is created with `new MockProvider()`. Typically this is a normal EVM instance, but for the OVM tests we need an OVM instance. As a result we’ll use the `MockProvider` from `@eth-optimism/ovm-toolchain` for the OVM tests, instead of Waffles’s standard `MockProvider`.

You may recall seeing `const provider = new MockProvider({...})` in the Uniswap tests, and this is exactly what we’ll update.

Uniswap tokens support the signature-based `permit` method to enable approvals via meta-transactions, and to avoid replay attacks the signature is based on the chain ID. While Ethereum mainnet uses a chain ID of 1, the OVM has a chain ID of 420. That means tests reliant on the chain ID will fail unless we update them. We can handle both of these changes in our `config.ts` as shown below:

```text
/**
 * @dev Handles chain-specific configuration based on whether we are running
 * EVM or OVM tests
 */

import { MockProvider } from 'ethereum-waffle' // standard EVM MockProvider from Waffle
import { waffleV3 } from '@eth-optimism/ovm-toolchain' // custom OVM version of Waffle V3

// Determine which network we are on
const isOVM = process.env.MODE === 'OVM'

// Get provider: We keep the same provider config that Uniswap tests were
// already using, but generate the provider instance based on the test mode
const options: any = {
  ganacheOptions: {
    hardfork: 'istanbul',
    mnemonic: 'horn horn horn horn horn horn horn horn horn horn horn horn',
    gasLimit: 9999999
  }
}
const provider = isOVM ? new waffleV3.MockProvider(options) : new MockProvider(options)

// Get Chain ID
const chainId = isOVM ? 420 : 1

export { provider, chainId }


```

Since `@eth-optimism/ovm-toolchain` does not have type definitions, the TypeScript compiler will complain since the new `MockProvider` implicitly has an `any` type. To avoid having to declare these types ourselves let’s just edit the `tsconfig.json` to turn off this rule:

```text
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
+   "noImplicitAny": false
  }
}

```

We can test out our new provider and chain ID by updating all the instances in the tests, then making sure the EVM tests still run.

Make the following changes in `test/UniswapV2ERC20.spec.ts`

```text
- import { solidity, MockProvider, deployContract } from 'ethereum-waffle'
+ import { solidity, deployContract } from 'ethereum-waffle'
+ import { provider } from './shared/config'

- const provider = new MockProvider({
-   ganacheOptions: {
-     hardfork: 'istanbul',
-     mnemonic: 'horn horn horn horn horn horn horn horn horn horn horn horn',
-     gasLimit: 9999999
-   }
- })
```

Make similar changes in `test/UniswapV2Factory.spec.ts` **and** `test/UniswapV2Pair.spec.ts`:

```text
- import { solidity, MockProvider, createFixtureLoader } from 'ethereum-waffle'
+ import { solidity, createFixtureLoader } from 'ethereum-waffle'
+ import { provider } from './shared/config'

- const provider = new MockProvider({
-   ganacheOptions: {
-     hardfork: 'istanbul',
-     mnemonic: 'horn horn horn horn horn horn horn horn horn horn horn horn',
-     gasLimit: 9999999
-   }
- })
```

We update the chain ID in `test/shared/utilities.ts`:

```text
+ import { chainId } from './config'

function getDomainSeparator(name: string, tokenAddress: string) {
  return keccak256(
    defaultAbiCoder.encode(
      ['bytes32', 'bytes32', 'bytes32', 'uint256', 'address'],
      [
        keccak256(toUtf8Bytes('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)')),
        keccak256(toUtf8Bytes(name)),
        keccak256(toUtf8Bytes('1')),
-       1
+       chainId,
        tokenAddress
      ]
    )
  )
}
```

and again in `test/UniswapV2ERC20.spec.ts`:

```text
- import { provider } from './shared/config'
+ import { provider, chainId } from './shared/config'

expect(await token.DOMAIN_SEPARATOR()).to.eq(
      keccak256(
        defaultAbiCoder.encode(
          ['bytes32', 'bytes32', 'bytes32', 'uint256', 'address'],
          [
            keccak256(
              toUtf8Bytes('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)')
            ),
            keccak256(toUtf8Bytes(name)),
            keccak256(toUtf8Bytes('1')),
-           1            
+           chainId,
            token.address
          ]
        )
      )
    )
```

Ok, done! Run `yarn test:evm` to make sure all tests still pass.

Now run `yarn test:ovm` and some tests pass! But a lot still fail. You should see something like this:

```text
  UniswapV2ERC20
    ✓ name, symbol, decimals, totalSupply, balanceOf, DOMAIN_SEPARATOR, PERMIT_TYPEHASH (1271ms)
    ✓ approve (335ms)
    ✓ transfer (524ms)
    ✓ transfer:fail (381ms)
    ✓ transferFrom (868ms)
    ✓ transferFrom:max (855ms)
    ✓ permit (820ms)

  UniswapV2Factory
    1) "before each" hook for "feeTo, feeToSetter, allPairsLength"

  UniswapV2Pair
    2) "before each" hook for "mint"


  7 passing (19s)
  2 failing

  1) UniswapV2Factory
       "before each" hook for "feeTo, feeToSetter, allPairsLength":
     RuntimeError: VM Exception while processing transaction: out of gas
      at Function.RuntimeError.fromResults (node_modules/ganache-core/lib/utils/runtimeerror.js:94:13)
      at BlockchainDouble.processBlock (node_modules/ganache-core/lib/blockchain_double.js:627:24)
      at process._tickCallback (internal/process/next_tick.js:68:7)

  2) UniswapV2Pair
       "before each" hook for "mint":
     RuntimeError: VM Exception while processing transaction: out of gas
      at Function.RuntimeError.fromResults (node_modules/ganache-core/lib/utils/runtimeerror.js:94:13)
      at BlockchainDouble.processBlock (node_modules/ganache-core/lib/blockchain_double.js:627:24)
      at process._tickCallback (internal/process/next_tick.js:68:7)
```

#### Gas and Compiler Fixes <a id="Gas-and-Compiler-Fixes"></a>

Time to fix the above test failures. The error message is `out of gas`, so maybe our gas limits aren’t high enough. In `config.ts` we specify a very high gas limit of 9,999,999. What’s going on?

Notice the failures are in the `beforeEach` hooks. The only thing we do in those hooks is deploy a contract. It seems our contract deployment is failing with a misleading error message. Let’s dig into why.

**OVM vs. EVM: Gas**

One important difference between the EVM and the OVM is the concept of “blocks.” While EVM networks consist of an ever-growing chain of blocks, blocks don’t exist on the OVM. As such, there’s no concept of a _block_ gas limit. Instead there is a _transaction_ gas limit, which is currently set to 9,000,000.

You can read about this, and other gas related differences, [here](https://hackmd.io/Inuu-T_UTsSXnzGtrLR8gA#Gas-in-the-OVM).

**OVM vs. EVM: Compiling**

Spoiler: Our contract deployment is failing because when we compiled it for the OVM, it became too big!

Both the Ethereum mainnet and the OVM have a 24 kB limit on deployed contract size—you cannot deploy contracts bigger than this. Compiling for the OVM results in an increase in contract size compared to compiling for the EVM, and the default compiler settings are especially bloat-inducing. Fortunately, we can shrink it with the optimizer.

Let’s edit our `.waffle-ovm.json` file to match the compiler settings used by Uniswap, which gives us this:

```text
{
  "compilerVersion": "./node_modules/@eth-optimism/solc",
  "outputType": "all",
  "compilerOptions": {
    "outputSelection": {
      "*": {
        "*": [
          "evm.bytecode.object",
          "evm.deployedBytecode.object",
          "abi",
          "evm.bytecode.sourceMap",
          "evm.deployedBytecode.sourceMap",
          "metadata"
        ],
        "": ["ast"]
      }
    },
    "evmVersion": "istanbul",
    "optimizer": {
      "enabled": true,
      "runs": 999999
    }
  }
}
```

Run `yarn test:ovm`, and almost all tests pass! \(There’s a few small gas-related failures we’ll adjust later after we review what just happened\).

We turned on the optimizer with a setting of 999,999 runs. This shrank our contract size significantly, bringing us back under the 24 kB size limit! It’s important to understand how the Solidity optimizer works, because even though 999,999 runs works here, that may not always be the case.

The `runs` parameter [specifies](https://www.reddit.com/r/ethdev/comments/jigz5o/ask_the_solidity_team_anything_1/gahssci?utm_source=share&utm_medium=web2x&context=3) “roughly how often each opcode of the deployed code will be executed across the lifetime of the contract”. A value of 1 produces shorter code that is more expensive to execute, while large values produce longer code that is cheaper. So you can see how we got a bit lucky in this case—because a large number of runs produces code that’s bigger in size, it’s possible with some contracts that using a large number of runs would result in a contract that is still to big deploy.

When compiling with the OVM, at a minimum be sure to turn on the optimizer with `runs` set to 1. Even a small value will result in a significant reduction in contract size!

_One other note on OVM compilation: The `deployedBytecode` output of solc is currently broken. This will most likely not affect you, as contract deployments deploy the `bytecode`, but it’s worth being aware of_.

**Final Gas Tweaks**

There were two tests that failed because they look for an exact gas usage. Because the OVM is not identical to the EVM, gas usage differs a bit. From the failure messages we can see what the expected gas usage should be for the OVM, so let’s fix this quickly.

First we need to be able to identify which mode we’re using in the tests, so let’s export that in `config.ts`:

```text
- export { provider, chainId }
+ export { provider, chainId, isOVM }
```

In `UniswapV2Factory.spec.ts`, let’s update the failing test:

```text
- import { provider } from './shared/config'
+ import { provider, isOVM } from './shared/config'

- expect(receipt.gasUsed).to.eq(2512920)
+ expect(receipt.gasUsed).to.eq(isOVM ? 4121083 : 2512920)
```

And similarly in `UniswapV2Pair.spec.ts`:

```text
- import { provider } from './shared/config'
+ import { provider, isOVM } from './shared/config'

- expect(receipt.gasUsed).to.eq(73462)
+ expect(receipt.gasUsed).to.eq(isOVM ? 657108 : 73462)
```

Finally, run `yarn test:evm` and `yarn test:ovm`, and everything should pass!

