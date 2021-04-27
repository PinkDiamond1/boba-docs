---
description: Migrate code quicker with the right tooling support
---

# Tooling

{% hint style="info" %}
**What's this?**  
  
A collection of useful JavaScript/TypeScript tooling plugins that you might need when building on top of Optimistic Ethereum!
{% endhint %}

### Required tooling 

OMGx as an optimism based Ethereum infrastructure solution relies on [hardhat](https://hardhat.org/). Hardhat automatically compiles your Solidity contracts with the OVM compiler. Defaults to Solidity version 0.7.6, but also has support for 0.5.16 and 0.6.12.

For detailed information refer to the Github repo [here](https://github.com/ethereum-optimism/plugins).

### Installation

Installation is super simple.

First, grab the package. Via `npm`:

```text
npm install @eth-optimism/plugins
```

Via `yarn`:

```text
yarn add @eth-optimism/plugins
```

Next, import the plugin inside your `hardhat.config.js`:

```text
// hardhat.config.js

require("@eth-optimism/plugins/hardhat/compiler")
```

Or if using TypeScript:

```text
// hardhat.config.ts

import "@eth-optimism/plugins/hardhat/compiler"
```

### Configuration

**By default, this plugin will use OVM compiler version 0.7.6**. Configure this plugin by adding an `ovm` field to your Hardhat config:

```text
// hardhat.config.js

require("@eth-optimism/plugins/hardhat/compiler")

module.exports = {
    ovm: {
        solcVersion: 'X.Y.Z' // Your version goes here.
    }
}
```

Has typings so it won't break your Hardhat config if you're using TypeScript.



