---
description: Learn how to fund your L2 accounts on the Boba 2.0 Rinkeby
---

# Fund L2 Accounts

Refer to the Github Repo [here](https://github.com/omgnetwork/optimism-v2/tree/develop/boba\_examples/init-fund-l2)

## Setup



Install the following:

* [`Node.js` (14+)](https://nodejs.org/en/)
* [`npm`](https://www.npmjs.com/get-npm)
* [`yarn`](https://classic.yarnpkg.com/en/docs/install/)

Install npm packages and build package in the root directory:

```
cd optimism-v2
yarn install
yarn build
```

#### Update .env

Add .env in `/boba-examples/init-fund-l2`

```
L1_NODE_WEB3_URL=https://rinkeby.infura.io/v3/INFURA_KEY
L2_NODE_WEB3_URL=https://rinkeby.boba.network
ADDRESS_MANAGER_ADDRESS=0x93A96D6A5beb1F661cf052722A1424CDDA3e9418
PRIVATE_KEY=
```

#### Move ETH from L1 to L2

Adjust the amount that you want to deposit from L1 to L2 in `/boba-examples/init-fund-l2/src/index.js`

```
cd boba-examples/init-fund-l2
yarn install
yarn start
```
