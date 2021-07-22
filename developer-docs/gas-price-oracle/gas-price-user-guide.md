---
description: Learn about OMGX L2 gas price usage
---

# Gas Price User Guide

### How to set gas prices on OMGX L2

{% hint style="info" %}
**DO NOT** change your tx.gasPrice! It must be set to `.015 gwei`

**DO NOT** change your tx.gasLimit! It encodes multiple values and should never be set manually in your wallet
{% endhint %}

### What fee do I pay?

Users can use the maximum gas limit, but it costs more than you actually have to pay. The estimated gas limit is based on `rollup_gasPrices`, `rollup_gasPrices` has `l1GasPrice` and `l2GasPrice`.

Gas is paid in ETH.

### How is that fee calculated?

`gasFee = gasPrice * gasLimit`

`estimatedGasLimit = calculateL1gaslimit (data) * L1GasFee + L2GasPrice * L2estimateExecutionGas`

### How do I get ETH?

* You can deposit ETH via [https://webwallet.rinkeby.omgx.network](https://webwallet.rinkeby.omgx.network) onto OMGX Rinkeby

