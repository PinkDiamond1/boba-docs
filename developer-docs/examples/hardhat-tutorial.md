# Hardhat Tutorial

## Getting Started with the Optimistic Ethereum: Simple ERC20 Token Hardhat Tutorial

To start off make sure you have a [local Boba](../local-boba.md) running!

Refer to our ERC20 test contract repo for Hardhat [here](https://github.com/omgnetwork/optimism/tree/develop/omgx_examples/hardhat).

1. Install the hardhat plugins.

   ```text
   yarn add @eth-optimism/hardhat-ovm
   yarn add @nomiclabs/hardhat-ethers
   yarn add @nomiclabs/hardhat-waffle
   yarn add hardhat-deploy
   ```

2. Edit `hardhat.config.js` to use the Optimistic Ethereum package.

   ```text
   require('@nomiclabs/hardhat-ethers')
   require('@nomiclabs/hardhat-waffle')
   require('hardhat-deploy')
   require('@eth-optimism/hardhat-ovm')

   ...
   ```

3. In the same file, add `optimism` to the list of networks:

   ```text
   // hardhat.config.js

   module.exports = {
     networks: {
       // Add this network to your config!
       optimism: {
         url: 'http://127.0.0.1:8545',
         // instantiate with a mnemonic so that you have >1 accounts available
         accounts: {
           mnemonic: 'test test test test test test test test test test test junk'
         },
         gasPrice: 15000000,
         ovm: true // This sets the network as using the ovm and ensure contract will be compiled against that.
       },
     },
  
     solidity: '0.6.12',
     ovm: {
       solcVersion: '0.6.12'
     },
     namedAccounts: {
       deployer: 0
     },
   }
   ```

4. Test the contract on Boba. Hardhat will recognize it has not been compiled and compile it for you.

   ```text
   npx hardhat --network optimism test
   ```

5. If you want to interact with the app manually, use the console. You can use the same JavaScript commands to control it you used above.

   ```text
   npx hardhat --network optimism console
   ```

**Change the Solidity Version if Needed**

To run on Optimistic Ethereum a contract needs to be compiled with a variant Solidity compiler. Sometimes the latest version of Solidity supported by Optimistic Ethereum is not the same as the version used by the sample app in HardHat. When that is the case, the `npx hardhat --network optimism test` command fails with an error message similar to:

```text
OVM Compiler Error (insert "// @unsupported: ovm" if you don't want this file to be compiled for the OVM):
 contracts/Greeter.sol:2:1: ParserError: Source file requires different compiler version (current compiler is 0.7.6) - note that nightly builds are considered to be strictly less than the released version
pragma solidity ^0.8.0;
^---------------------^

Error HH600: Compilation failed
```

To solve this problem:

1. Edit the `hardhat.config.js` file to change `module.exports.solidity` to the supported version.
2. Edit your contracts to change the `pragma solidity` line to the supported version.
3. Check the application still works on normal Ethereum.

Running into other issues? Check our OVM compilation guide [here](compiling-ovm.md).

```text
npx hardhat test
```

1. Check the application works on Boba.

```text
npx hardhat --network optimism test
```

### Best Practices for Running Tests

As you may have noticed, in this tutorial we ran all the tests first on the HardHat EVM and only then on Optimistic Ethereum. This is important, because it lets you isolate contract problems from problems that are the result of using Optimistic Ethereum rather than vanilla Ethereum.

### Deployment

In the `hardhat.config` \(or equivalent\) set gasPrice: `gasPrice: 15000000` and in function calls, specify your gasLimit, such as:  


```text
const tx1 = await ERC20.connect(account1).approve(
        await account2.getAddress(),
        INITIAL_SUPPLY,
        {gasLimit: 6400000}
      )
```

### 

