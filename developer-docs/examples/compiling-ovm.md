---
description: These are the most common errors when compiling your contracts for the OVM
---

# Compiling for OVM

The optimism solc compiler supports Solidity versions 0.5.16, 0.6.12, and 0.7.6.. The first time you run `yarn build` you will typically see many errors relating to your pragmas. If most of your pragmas are around 0.6 you would chose 0.6.12, and so forth. In general, small modifications \(such as replacing `^0.5.17` with `^0.5.16` or specifying a broader range such as `pragma solidity >= 0.5.16 < 0.6.5;`\) will not affect your code and your unit and integration tests will pick up any exceptions.

At this point, solc has all the information to get started. Now, you will see actual code issues, such as:

```text
OVM Compiler Error (insert "// @unsupported: ovm" if you don't want this file to be compiled for the OVM):
 contracts/foo.sol:72:31: ParserError: OVM: ORIGIN is not implemented in the OVM.
        require(msg.sender == tx.origin, "not eoa");

OVM Compiler Error (insert "// @unsupported: ovm" if you don't want this file to be compiled for the OVM):
 contracts/WETH.sol:51:16: ParserError: OVM: SELFBALANCE is not implemented in the OVM. (We have no native ETH -- use deposited WETH instead!)
        return address(this).balance;
               ^-------------------^

Error HH600: Compilation failed
```

Let's now tackle those one by one.

### 1. No native ETH

In many smart contracts, ETH is handled slightly differently than ERC20 tokens, but on L2, there is no native ETH. Instead, L2s use an ERC20 representation of ETH such as wETH or oETH. This means that all ETH-specific functions can be deleted, since there are no longer needed. For example:

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

From a UI/Frontend perspective, 'native' ETH functions are no longer needed and integration test code will also need any ETH-specific tests to be commented out. In the case of the SUSHI port, among other changes, `contracts/mocks/WETH9Mock.sol` can be deleted entirely and many functions in `contracts/uniswapv2/UniswapV2Router02.sol` can also be deleted, such as `removeLiquidityETH` and `swapExactETHForTokens` etc. Removing functions in the contracts also affects the interfaces, of course, e.g. `contracts/uniswapv2/interfaces/IUniswapV2Router01.sol`.

### 2. Timing, `now`, and `block.timestamp`

The L2 does not have traditional blocks. Control over time, and manipulation of apparent time, is critical for L2, since during a fraud proof, the L1 contacts will need to replay the L2 contracts at specific times _in the past_ to check their correctness. `block.timestamp` returns the last L1 block in which a rollup batch was posted. This means that the `block.timestamp` returned on L2 can lag as many as 10 minutes behind L1. Depending on how `block.timestamp` is being used, this 1-10 min lag could have **serious unexpected implications**. See [OVM-vs-EVM-Block-Timestamps](https://hackmd.io/@scopelift/Hy853dTsP#OVM-vs-EVM-Block-Timestamps) for a more extensive discussion. Briefly, consider:

1. The OVM timestamp lags behind the EVM, so it’s possible that e.g. OVM trades execute up 10 minutes after your specified deadline.
2. `permit` method signatures contain a deadline, and the approval must be sent before that deadline. In certain cases, the approval could take place after the deadline.
3. Bid and auction duration. If you are trying to run an auction with minute scale bid duration, then a 1-10 minute lag relative to L1 could throw that off completely.

### 3. Replace `chainid()` with `uint256 chainId = ___`

```text
contracts/SushiToken.sol
@@ -239,8 +241,8 @@ contract SushiToken is ERC20("SushiToken", "SUSHI"), Ownable {
	...
    function getChainId() internal pure returns (uint) {
-       uint256 chainId;
+       uint256 chainId = 28; //or whatever the L2 ChainID is...
+       //assembly { chainId := chainid() }
```

### 4. Update Depreciated Syntax

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

Example for transferring oETH

```text
function externalCall(address target, bytes memory callData)
external
payable
{
    uint256 msgValue = IERC20(0x4200000000000000000000000000000000000006).balanceOf(address(this));
    IERC20(0x4200000000000000000000000000000000000006).transfer(target,msgValue);
    (bool success, ) = target.call(callData);
    require(success, "External Call execution Failed");
}
```

### 5. No tx.origin

One function call that cannot be replicated in the OVM is `tx.origin`. This is because of account abstraction that occurs on layer 2. On layer 2 there is no distinction between wallets and contracts because all accounts get abstracted to a contract. In most use cases this doesn’t matter because the abstraction doesn’t interfere with any of the functionality or interactions of contracts. However, for `tx.origin` this does play a role because there is no “origin” address in L2; it's replaced by a smart contract. So what can you do if you’re using `tx.origin` in your smart contract and still want to develop on OMGX optimism network? No worries, read along we’ve got you covered!

For the most part `tx.origin` should not be used as a security measure because it opens up a contract to phishing attempts from other “malicious” contracts. `Tx.origin` returns the caller of the original transaction no matter what contract is calling on behalf of that origin. This opens up your contract to security vulnerabilities because once you interact with a contract it can then access any other contract that uses `tx.origin` as validation to drain your funds from that contract.

Consider the following contract

```text
pragma solidity >=0.5.0 <0.7.0;
// THIS CONTRACT CONTAINS A BUG - DO NOT USE contract TxUserWallet { address owner;
constructor() public {
    owner = msg.sender;
}

function transferTo(address payable dest, uint amount) public {
    require(tx.origin == owner);
    dest.transfer(amount);
}
}
```

Another contract you access could access this contract and since it uses `tx.origin` as a security measure the other contract would be able to drain the funds out of this contract.

A more secure alternative would be to replace `tx.origin` with `msg.sender`. This would then correctly identify the malicious contract as an invalid sender and exit out correctly. Overall, in most use cases `tx.origin` should be replaced with `msg.sender` so as to avoid this security vulnerability.

One use case where this replacement is not possible is in the `msg.sender == tx.origin` because replacing `tx.origin` with `msg.sender` would lead to an always true statement. The main reason this is done is to only allow wallets and not contracts to access parts of smart contracts. For example:

```text
function isContract() public {
    require(tx.origin == msg.sender);
    console.log("You're not a contract");
}
```

This is preventing other contracts from accessing this piece of code. It also blocks anyone who uses a multisig wallet which may impact the usability of your smart contract. If you’re using this case of `tx.origin` you should consider why you think this is necessary and see if there’s some other security vulnerability that you’re trying to avoid by making sure only accepting calls from wallets and not smart contracts. One of the most common vulnerabilities that are protected by this syntax are re-entrancy vulnerabilities.

#### Re-entrancy vulnerabilities

A reentrancy attack can occur when you create a function that makes an external call to another untrusted contract before it resolves any effects. If the attacker can control the untrusted contract, they can make a recursive call back to the original function, repeating interactions that would have otherwise not run after the effects were resolved. Let’s breakdown what this means with an example:

```text
function withdraw() external { uint256 amount = balances[msg.sender];
require(msg.sender.call.value(amount)()); balances[msg.sender] = 0; }
```

A malicious contract could recursively call withdraw to drain all the funds out of this contract. Typically a vulnerable function will make an external call using transfer, send, or call exposing itself to other contracts manipulation. This example was only within a single function but another way a contract could be exposed to an attack is if another function calls withdraw\(\). Since that function would call on an untrusted function it would also become untrusted.

#### How to guard against re-entrancy without `tx.origin`

Each case of guarding against re-entrancy is unique but the same general techniques can be applied to secure your contract against these attacks.

Send Transfer Call

Because most reentrancy attacks involve send, transfer, or call functions — it is important to understand the difference between them. send and transfer functions are considered safer because of their limit of 2,300 gas. The gas limit prevents the expensive external function calls back to the target contract. The one pitfall is when a contract sets a custom amount of gas for a send or transfer using msg.sender.call\(ethAmount\).gas\(gasAmount\). The call function is unfortunately much more vulnerable. When an external function call is expected to perform complex operations, you typically want to use the call function because it forwards all remaining gas. This opens the door for an attacker to make calls back to the original function in a single function reentrancy attack, or a different function from the original contract in a cross-function reentrancy attack. Wherever possible, use send or transfer in place of call to limit your security risk.

#### Checks-effects-interactions pattern

Using this guiding principle for developing smart contracts is considered the best practice. Following this pattern determines the way you should structure your smart contracts. First perform any checks, which are normally assert and require statements, at the beginning of the function. If the checks pass the next section is the effects. This refers to resolving the effects of the state of the contract. Finally, we would move on to the interactions with other contracts. By making this last we can confirm that the effects are correctly processed and so that any re-entrance from a malicious contract finds that the state of the contract has already been changed correctly before it can try to launch the attack. Let’s take our bad withdraw function and apply these changes so we now have a secure contract:

```text
function withdraw() external { uint256 amount = balances[msg.sender]; 
balances[msg.sender] = 0;
require(msg.sender.call.value(amount)()); }
```

The simple act of flipping two lines has now safeguarded our contract from malicious attacks. This is accomplished because the balances mapping of `msg.sender` has correctly been updated to 0 so any recursive action would call require\(`msg.sender.call`.value\(amount\)\(\)\); with amount being 0 and not withdraw any more funds than permissible by this contract. Mutexes

Mutexes allow you to place locks on functions that can only be unlocked by the owner of the lock. These are extremely helpful in developing cross-function code that is also safe from re-entrancy attacks. Let’s take a look at an example to see how this would work:

```text
function transfer(address to, uint amount) external { require(!lock); lock = true; if (balances[msg.sender] >= amount) { balances[to] += amount; balances[msg.sender] -= amount; } lock = false; } function withdraw() external { require(!lock); lock = true; uint256 amount = balances[msg.sender]; require(msg.sender.call.value(amount)()); balances[msg.sender] = 0; lock = false; }
```

When using mutexes you should make sure that your mutex always has a way to be unlocked and there’s no path that would allow the mutex to be locked as it would then render your contract inert.

OpenZeppelin has it’s own mutex implementation you can use called ReentrancyGuard. This library provides a modifier you can apply to any function called nonReentrant that guards the function with a mutex.

View the source code for the OpenZeppelin ReentrancyGuard library here: [https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/utils/ReentrancyGuard.sol](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/utils/ReentrancyGuard.sol)

Keeping everything you’ve read in mind you should now be able to transition your contract to not use `tx.origin` and also have a much more robust security implementation!

```text
contracts/SushiMaker.sol
    // Try to make flash-loan exploit harder to do by only allowing externally owned addresses.
-   require(msg.sender == tx.origin, "SushiMaker: must use EOA");
+   //require(msg.sender == tx.origin, "SushiMaker: must use EOA");
```

### 6. TESTS RESULTS: All good EXCEPT evm\_increaseTime and evm\_mine

All tests clear EXCEPT things related to `evm_increaseTime` and `evm_mine`. Note that this does not affect the contracts _per se_ but affects testing.

