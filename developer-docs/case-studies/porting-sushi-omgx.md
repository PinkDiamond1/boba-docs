---
description: Learn how to deploy an existing L1 protocol to OMGX L2
---

# Porting SUSHI to OMGX

## SUSHI

Sushiswap is a DeFi exchange that supports token swapping and many other actions. We started by copying SUSHI's smart contracts into the `packages/analyzer/contracts` folder. Then, we ran:

```
yarn install
yarn analyze
yarn compile
```

We then addressed the warnings and errors one by one.

{% hint style="info" %}
Heads Up

Our documentation is a rapidly improving work in progress. If you have questions or feel like something is missing feel free to ask in our [Discord server](https://omg.eco/support) where we are actively responding, or [open an issue](https://github.com/omgnetwork) in the GitHub repo for this site.
{% endhint %}

## **1. No native ETH**

In many smart contracts, ETH is handled slightly differently than ERC20 tokens, but on L2, there is no native ETH.Instead, people use an ERC20 representation of ETH such as wETH or oETH. This means that all ETH-specific functions can be deleted, since there are no longer needed. For example:

```text
contracts/uniswapv2/interfaces/IUniswapV2Router01.sol
@@ -16,14 +17,15 @@ interface IUniswapV2Router01 {
 ...
 -    function addLiquidityETH(
 -        address token,
 -        uint amountTokenDesired,
 -        uint amountTokenMin,
 -        uint amountETHMin,
 -        address to,
 -        uint deadline
 -    ) external payable returns (uint amountToken, uint amountETH, uint liquidity);
 +    // CHANGE_OMGX
 +    // function addLiquidityETH(
 +    //     address token,
 +    //     uint amountTokenDesired,
 +    //     uint amountTokenMin,
 +    //     uint amountETHMin,
 +    //     address to,
 +    //     uint deadline
 +    // ) external payable returns (uint amountToken, uint amountETH, uint liquidity);
```

From the UI/Frontend perspective, native ETH functions are no longer needed, and integration test code will also need ETH-specific tests to be commented out. Among other changes, `contracts/mocks/WETH9Mock.sol` can be deleted entirely, and many functions in `contracts/uniswapv2/UniswapV2Router02.sol` can also be deleted completely, such as `removeLiquidityETH` and `swapExactETHForTokens` etc. Removing functions in the contracts also affects the interfaces, of course, ****e.g.`contracts/uniswapv2/interfaces/IUniswapV2Router01.sol.`

## **2. Replace `now` with `block.timestamp`**

The L2 does not have traditional blocks and its timing is effectively slaved to L1. Control over time is critical for L2, since during a fraud proof, the L1 contacts will need to replay the L2 contracts at specific times in the past to check their correctness.

```text
contracts/SushiToken.sol
@@ -118,7 +118,9 @@ contract SushiToken is ERC20("SushiToken", "SUSHI"), Ownable { 
    ...
-   require(now <= expiry, "SUSHI::delegateBySig: signature expired");
+   require(block.timestamp <= expiry, "SUSHI::delegateBySig: signature expired");
```

## **3.** Replace `chainid()` with `uint256 chainId =` _`_`_

```text
contracts/SushiToken.sol
@@ -239,8 +241,8 @@ contract SushiToken is ERC20("SushiToken", "SUSHI"), Ownable {
 ...
function getChainId() internal pure returns (uint) {
-       uint256 chainId;
+       uint256 chainId = 420; //or whatever the L2 ChainID is...
+       //assembly { chainId := chainid() }
```

## **4.** Replace `external view` with `external`

```text
contracts/interfaces/IRewarder.sol
@@ -5,5 +5,6 @@ import "@boringcrypto/boring-solidity/contracts/libraries/BoringERC20.sol";
 ...
-   function pendingTokens(uint256 pid, address user, uint256 sushiAmount) external view returns ...
+   function pendingTokens(uint256 pid, address user, uint256 sushiAmount) external returns ...

also needed in

contracts/mocks/ComplexRewarder.sol
contracts/mocks/ComplexRewarderTime.sol
contracts/mocks/RewarderBrokenMock.sol
contracts/mocks/RewarderMock.sol 
```

## **5.** Update Depreciated Syntax

Not strictly L2 related, but updated it to help with future maintainability.

```text
contracts/governance/Timelock.sol
-   (bool success, bytes memory returnData) = target.call.value(value)(callData);
+   (bool success, bytes memory returnData) = target.call{value:value}(callData);

// The following syntax is deprecated: 
// f.gas(...)(), f.value(...)() and (new C).value(...)().
// Replace with:
// f{gas: ..., value: ...}() and (new C){value: ...}().
```

## **6.** No tx.origin

L2 does not support `tx.origin`. **CAUTION New code is needed here to prevent flash-loan exploits CAUTION**. For now, we just commented out the require.

```text
contracts/SushiMaker.sol
    // Try to make flash-loan exploit harder to do by only allowing externally owned addresses.
    -   require(msg.sender == tx.origin, "SushiMaker: must use EOA");
    +   //require(msg.sender == tx.origin, "SushiMaker: must use EOA");
```

That's it!

