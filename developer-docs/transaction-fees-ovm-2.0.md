---
description: Learn how transaction fees are calculated under OVM 2.0
---

# Transaction Fees OVM 2.0

## Transaction Fees in OVM 2.0 <a id="frontmatter-title"></a>

This page refers to the **new** state of Boba Network after the OVM 2.0 update (deployed Oct. 28 2021).

### Fees in a nutshell <a id="fees-in-a-nutshell"></a>

Fees on Boba are, for the most part, significantly lower than on the Ethereum mainnet. 

The cost of every transaction is composed of two parts 

1. Your L2 \(execution\) fee, and 
2. Your L1 \(security\) fee. 

At a high level, the L2 fee is the cost to execute your transaction in L2 and the L1 fee is the estimated cost to submit your transaction to L1 \(in a rollup batch\).

1. **L2 execution fee** is charged as `tx.gasPrice * l2GasUsed` \(up to `tx.gasLimit`\). The L2 gas price will vary depending on network congestion.
2. **L1 security fee** is automatically charged as `l1GasPrice * l1GasUsed`. This is the cost of storing the transaction's data on L1.
   * `l1GasPrice` is the same as the normal gas price in L1 Ethereum
   * `l1GasUsed` is calculated as `1.5*(2750 gas + calldataGas)`. Thus, more calldata your transaction includes, the more expensive your L1 fee will be. For example, an ETH transfer has no calldata, so it will have the cheapest L1 fee, whereas large contract deployments can have over 25kb of calldata and will result in a high L1 fee. We currently add a 50% overhead to the L1 fee to ensure the fee paid covers the actual L1 costs.

To obtain ETH on Boba Network you can deposit ETH via[ https://gateway.boba.network](https://gateway.boba.network) on both Rinkeby or Mainnet. Soon you will be able to also deposit ETH for slightly cheaper via [Anyswap](https://anyswap.exchange/#/router).
