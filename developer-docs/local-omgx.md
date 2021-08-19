---
description: Learn how to set up a local OMGX system.
---

# Local Boba



* [Starting a local basic Optimism L1/L2 with OMGX contracts and services](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#starting-a-local-basic-optimism-l1-l2-with-omgx-contracts-and-services)
  * [Starting a local basic Optimism L1/L2](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#starting-a-local-basic-optimism-l1-l2)
    * [Overall Setup](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#overall-setup)
  * [\(Re\)Building the entire system or parts of the base L1/L2](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#-re-building-the-entire-system-or-parts-of-the-base-l1-l2)
  * [\(Re\)Building the entire system or parts of the OMGX contracts and services](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#-re-building-the-entire-system-or-parts-of-the-omgx-contracts-and-services)
    * [Viewing docker container logs](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#viewing-docker-container-logs)
    * [Running unit tests](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#running-unit-tests)
    * [Running integration tests](https://github.com/omgnetwork/optimism/blob/develop/README_OMGX.md#running-integration-tests)

## Starting a local basic Optimism L1/L2 with Boba contracts and services

You can change the BUILD and DAEMON values to control if everything is rebuilt \(`BUILD=1`, very slow\), and if you want to see all the debug information \(`DAEMON=0`\)

```text
$ cd ops
$ BUILD=1 DAEMON=0 ./up_local.sh
```

### Starting a local basic Optimism L1/L2

You can change the BUILD and DAEMON values to control if everything is rebuilt \(`BUILD=1`, very slow\), and if you want to see all the debug information \(`DAEMON=0`\)

```text
cd ops
export COMPOSE_DOCKER_CLI_BUILD=1 # these environment variables significantly speed up build time
export DOCKER_BUILDKIT=1
docker-compose build 
docker-compose up -V
```

The `-V` setting is critical, since otherwise your Docker images may have stale information in them from previous runs, which will confuse the `data-transport-layer`, among other things.

#### Overall Setup

Clone the repository, open it, and install nodejs packages with `yarn`:

```text
git clone git@github.com:omgnetwork/optimism.git
cd optimism
yarn clean
yarn install
yarn build
```

Packages compiled when on one branch may not be compatible with packages on a different branch. **You should recompile all packages whenever you move from one branch to another.** Use the below commands to recompile the packages.

### \(Re\)Building the entire system or parts of the base L1/L2

If you want to run an Optimistic Ethereum node OR **if you want to run the integration tests**, you'll need to build the rest of the system.

If you want to make a change to a container, you'll need to take it down and rebuild it. For example, if you make a change in l2geth:

```text
cd ops
docker-compose stop -- l2geth
docker-compose build -- l2geth
docker-compose start l2geth
```

For the typescript services, you'll need to rebuild the `builder` so that the compiled files are re-generated, and then your service, e.g. for the batch submitter

```text
cd ops
docker-compose stop -- batch_submitter
docker-compose build -- builder batch_submitter
docker-compose start batch_submitter
```

Source code changes can have an impact on more than one container. **If you're unsure about which containers to rebuild, just rebuild them all**:

```text
cd ops
docker-compose down
docker-compose build
docker-compose up
```

Finally, **if you're running into weird problems and nothing seems to be working**, run:

```text
cd optimism
yarn clean
yarn build
cd ops
docker-compose down -v
docker-compose build
docker-compose up
```

### \(Re\)Building the entire system or parts of the OMGX contracts and services

```text
cd ops
export COMPOSE_DOCKER_CLI_BUILD=1 # these environment variables significantly speed up build time
export DOCKER_BUILDKIT=1
docker-compose build 
docker-compose -f docker-compose.yml -f docker-compose-omgx-services.yml up -V
```

To build individual OMGX services:

```text
docker-compose -f "docker-compose-omgx-services.yml" build -- omgx_message-relayer-fast
```

Note: First you will have to comment out various dependencies in the `docker-compose-omgx-services.yml`.

**Viewing docker container logs**

By default, the `docker-compose up` command will show logs from all services, and that can be hard to filter through. In order to view the logs from a specific service, you can run:

```text
docker-compose logs --follow <service name>
```

#### Running unit tests

Before running tests: **follow the above instructions to get everything built.** Run unit tests for all packages in parallel via:

```text
yarn test
```

To run unit tests for a specific package:

```text
cd packages/package-to-test
yarn test
```

#### Running integration tests

Follow above instructions for building the whole stack. Build and run the integration tests:

```text
cd integration-tests
yarn build:integration
yarn test:integration
```

### Front End Development

Start a local L1/L2. You can change the BUILD and DAEMON values to control if everything is rebuilt \(`BUILD=1`, very slow\), and if you want to see all the debug information \(`DAEMON=0`\)

```text
$ cd ops
$ BUILD=1 DAEMON=1 ./up_local.sh
```

Typically, you will only have to build everything once, and after that, you can save time by setting `BUILD` to `0`:

```text
$ cd ops
$ BUILD=0 DAEMON=1 ./up_local.sh
```

Then, open a second terminal window and navigate to `packages/omgx/wallet-frontend`, and run

```text
$ yarn build
$ yarn start
```

and the frontend should start up in a local browser. You can also develop on the Rinkeby testnet - in that case, you do not need to run a local L1/L2. If you would like to do that, just change the .env settings:

```text
# This is for working on the wallet, pointed at the OMGX Rinkeby testnet
REACT_APP_INFURA_ID=
REACT_APP_ETHERSCAN_API=
REACT_APP_POLL_INTERVAL=20000
SKIP_PREFLIGHT_CHECK=true
REACT_APP_WALLET_VERSION=1.0.10
REACT_APP_WALLET_SERVICE=https://api-service.rinkeby.omgx.network/
REACT_APP_BUYER_OPTIMISM_API_URL=https://n245h0ka3i.execute-api.us-west-1.amazonaws.com/prod/
REACT_APP_ETHERSCAN_URL=https://api-rinkeby.etherscan.io/api?module=account&action=txlist&startblock=0&endblock=99999999&sort=asc&apikey=
REACT_APP_OMGX_WATCHER_URL=https://api-watcher.rinkeby.omgx.network/
REACT_APP_SELLER_OPTIMISM_API_URL=https://pm7f0dp9ud.execute-api.us-west-1.amazonaws.com/prod/
REACT_APP_SERVICE_OPTIMISM_API_URL=https://zlba6djrv6.execute-api.us-west-1.amazonaws.com/prod/
REACT_APP_WEBSOCKET_API_URL=wss://d1cj5xnal2.execute-api.us-west-1.amazonaws.com/prod
```

