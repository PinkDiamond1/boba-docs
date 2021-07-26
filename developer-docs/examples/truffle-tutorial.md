# Truffle Tutorial

## Getting Started with Optimistic Ethereum: Simple ERC20 Token Truffle Tutorial

Hi there! Welcome to our Optimistic Ethereum ERC20 Truffle example!

You can find the repo with all working Truffle examples [here](https://github.com/omgnetwork/optimism/tree/develop/examples/truffle)

If you're interested in writing your first L2-compatible smart contract using Truffle as your smart contract testing framework, then you've come to the right place! This repo serves as an example for how go through and compile/test/deploy your contracts on both Ethereum and Optimistic Ethereum.

Let's begin!

### Prerequisites

Please make sure you've installed the following before continuing:

* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/en/download/)
* [Yarn 1](https://classic.yarnpkg.com/en/docs/install#mac-stable)
* [Docker](https://docs.docker.com/engine/install/)

### Setup

To start, clone this `optimism` repo, enter it, and install all of its dependencies:

```text
git clone https://github.com/omgnetwork/optimism
cd /examples/truffle
yarn install
```

### Step 1: Compile your contracts for Optimistic Ethereum

Compiling a contract for Optimistic Ethereum is pretty easy! First we'll need to install the [`@eth-optimism/solc`](https://www.npmjs.com/package/@eth-optimism/solc). Since we currently only support `solc` versions `0.5.16`, `0.6.12`, and `0.7.6` for Optimistic Ethereum contracts, we'll be using version `0.7.6` in this example. Let's add this package:

```text
yarn add @eth-optimism/solc@0.7.6-alpha.1
```

Next, we just need to add a new `truffle-config-ovm.js` file to compile our contracts.

Create `truffle-config-ovm.js` and add the following to it:

```text
const mnemonicPhrase = "candy maple cake sugar pudding cream honey rich smooth crumble sweet treat"
const HDWalletProvider = require('@truffle/hdwallet-provider')

module.exports = {
  contracts_build_directory: './build-ovm',
  networks: {
    omgx: {
      provider: function () {
        return new HDWalletProvider({
          mnemonic: {
            phrase: mnemonicPhrase
          },
          providerOrUrl: 'http://127.0.0.1:8545'
        })
      },
      network_id: 28,
      host: '127.0.0.1',
      port: 8545,
      gasLimit: 6400000,
    }
  },
  compilers: {
    solc: {
      // Add path to the optimism solc fork
      version: "node_modules/@eth-optimism/solc",
      settings: {
        optimizer: {
          enabled: true,
          runs: 1
        },
      }
    }
  }
}
```

Here, we specify the new custom Optimistic Ethereum compiler we just installed and the new build path for our optimistically compiled contracts. We also specify the network parameters of a local Optimistic Ethereum instance. This local instance will be set up soon, but we'll set this up in our config now so that it's easy for us later when we compile and deploy our Optimistic Ethereum contracts.

And we're ready to compile! All you have to do is specify the `truffle-config-ovm.js` config in your `truffle` command, like so:

```text
yarn truffle compile --config truffle-config-ovm.js
```

Our `truffle-config-ovm.js` config file tells Truffle that we want to use the Optimistic Ethereum solidity compiler.

Yep, it's that easy. You can verify that everything went well by looking for the `build-ovm` directory that contains your new JSON files.

Here, `build-ovm` signifies that the contracts contained in this directory have been compiled for the OVM, the **O**ptimistic **V**irtual **M**achine, as opposed to the Ethereum Virtual Machine. Now let's move on to testing!

### Step 2: Testing your Optimistic Ethereum contracts

Woot! It's finally time to test our contract on top of Optimistic Ethereum. But first we'll need to get a local version of an Optimistic Ethereum node running...

Since we're going to be using Docker, make sure that Docker is installed on your machine prior to moving on \(info on how to do that [here](https://docs.docker.com/engine/install/)\). **We recommend opening up a second terminal for this part.** This way you'll be able to keep the Optimistic Ethereum node running so you can execute some contract tests.

{% page-ref page="../local-omgx.md" %}

With your local instance of Optimistic Ethereum up and running, let's test your contracts! Since the two JSON RPC provider URLs \(one for your local instance Ethereum and Optimistic Ethereum\) have already been specified in your Truffle config files, all we need to do next is run the test command.

To do that, run:

```text
yarn truffle test ./test/erc20.spec.js --network omgx --config truffle-config-ovm.js
```

Notice that we are using `truffle-config-ovm.js` to let `truffle` know that we want to use the `build-ovm` folder as our path to our JSON files. \(Remember that these JSON files were compiled using the Optimistic Ethereum solidity compiler!\)

Additionally, we also specify the network we are testing on. In this case, we're testing our contract on `optimistic_ethereum`.

You should see a set of passing tests for your ERC20 contract. If so, congrats! You're ready to deploy an application to Optimistic Ethereum. It really is that easy.

### Step 3: Deploying your Optimistic Ethereum contracts

Going through this routine one more time. Now we're going to deploy an Optimisic Ethereum contract using `truffle`. For Truffle based deployments, we're going to use Truffle's `migrate` command to run a migrations file for us that will deploy the contract we specify.

First, let's create that migrations file. Create a new directory called `migrations` in the topmost path of your project and create a file within it called `1_deploy_ERC20_contract.js`.

Next, within `1_deploy_ERC20_contract.js`, we're going to add the following logic:

```text
const ERC20 = artifacts.require('ERC20')

module.exports = function (deployer, accounts) {
  const tokenName = 'My Optimistic Coin'
  const tokenSymbol = 'OPT'
  const tokenDecimals = 1

  // deployment steps
  deployer.deploy(
    ERC20, 
    10000, 
    tokenName, 
    tokenDecimals, 
    tokenSymbol,
    { gasPrice: 0 }
  )
}
```

To quickly explain this file, first we import our artifact for our ERC20 contract. Since we specified the build directory in our Truffle configs, Truffle knows whether we want to use either an Ethereum or Optimistic Ethereum contract artifact.

Now we're ready to run our migrations file! Let's go ahead and deploy this contract:

```text
yarn truffle migrate --network omgx --config truffle-config-ovm.js
```

This should deploy against a local \(in-memory\) Optimistic Ethereum node that was spin up when we started the integrations repo.

After a few seconds your contract should be deployed! 

