# Porting COMPOUND to OMGX and Optimism

## COMPOUND

Compound is an algorithmic, autonomous interest rate protocol built for developers, to unlock a universe of open financial applications. The development and deployment framework used by Compound is called Saddle and is a little bit different from Truffle or Hardhat because it's utilizing Solc directly and not the solc-js cross compiled version.

We started by providing `solc` to the development environment, tailored for the Optimistc VM \(OVM\). It's still [WIP](https://github.com/ethereum-optimism/solidity/issues/24) but ought to be released shortly.

If you decide to install Optimistc `solc` system wide or not, it's up to you. Saddle configuration available in `saddle.config.js` allows absolute paths for `solc` binary.

### 1. No native ETH

In many smart contracts, ETH is handled slightly differently than ERC20 tokens, but on L2, there is no native ETH. Instead, L2's use an ERC20 representation of ETH such as wETH or oETH. This means that all ETH-specific functions can be deleted, since there are no longer needed. For example:

```text
--- a/contracts/Lens/CompoundLens.sol
+++ b/contracts/Lens/CompoundLens.sol
	...
-        if (compareStrings(cToken.symbol(), "cETH")) {
-            underlyingAssetAddress = address(0);
-            underlyingDecimals = 18;
-        } else {
-           CErc20 cErc20 = CErc20(address(cToken));
-           underlyingAssetAddress = cErc20.underlying();
-           underlyingDecimals = EIP20Interface(cErc20.underlying()).decimals();
-        }
+        CErc20 cErc20 = CErc20(address(cToken));
+        underlyingAssetAddress = cErc20.underlying();
+        underlyingDecimals = EIP20Interface(cErc20.underlying()).decimals();

         return CTokenMetadata({
             cToken: address(cToken),
@@ -94,15 +89,10 @@ contract CompoundLens {
         uint tokenBalance;
         uint tokenAllowance;

-        if (compareStrings(cToken.symbol(), "cETH")) {
-            tokenBalance = account.balance;
-            tokenAllowance = account.balance;
-        } else {
-           CErc20 cErc20 = CErc20(address(cToken));
-           EIP20Interface underlying = EIP20Interface(cErc20.underlying());
-           tokenBalance = underlying.balanceOf(account);
-           tokenAllowance = underlying.allowance(account, address(cToken));
-        }
+        CErc20 cErc20 = CErc20(address(cToken));
+        EIP20Interface underlying = EIP20Interface(cErc20.underlying());
+        tokenBalance = underlying.balanceOf(account);
+        tokenAllowance = underlying.allowance(account, address(cToken));
```

```text
--- a/contracts/SimplePriceOracle.sol
+++ b/contracts/SimplePriceOracle.sol
@@ -8,11 +8,7 @@ contract SimplePriceOracle is PriceOracle {
     event PricePosted(address asset, uint previousPriceMantissa, uint requestedPriceMantissa, uint newPriceMantissa);

     function getUnderlyingPrice(CToken cToken) public view returns (uint) {
-        if (compareStrings(cToken.symbol(), "cETH")) {
-            return 1e18;
-        } else {
-           return prices[address(CErc20(address(cToken)).underlying())];
-        }
+        return prices[address(CErc20(address(cToken)).underlying())];
```

From a UI/Frontend perspective, 'native' ETH functions are no longer needed and integration test code will also need ETH-specific tests to be commented out or deleted \(for example `tests/Contracts/CEtherHarness.sol`, `spec/certora/contracts/CEtherCertora.sol` and `./tests/Tokens/borrowAndRepayCEtherTest.js`\). Among other changes, `contracts/CEther.sol` and `contracts/Maximillion.sol` can be deleted entirely and many functions in `./scenario/src/Builder/CTokenBuilder.ts` can also be deleted completely, such as deploymments of ETH related functionality.

Removing functions in the contracts also affects the interfaces, of course.

### 2. No tx.origin

L2 does not support `tx.origin`. **CAUTION New code is needed here to prevent flash-loan exploits CAUTION**. For now, we just commented out the `require`.

```text
@@ -1099,7 +1099,6 @@ contract Comptroller is ComptrollerV3Storage, ComptrollerInterface, ComptrollerE
      * @notice Recalculate and update COMP speeds for all COMP markets
      */
     function refreshCompSpeeds() public {
-        require(msg.sender == tx.origin, "only externally owned accounts may refresh speeds");
         refreshCompSpeedsInternal();
     }
```

```text
--- a/contracts/ComptrollerG1.sol
+++ b/contracts/ComptrollerG1.sol
@@ -983,9 +983,6 @@ contract ComptrollerG1 is ComptrollerV1Storage, ComptrollerInterface, Comptrolle
     function adminOrInitializing() internal view returns (bool) {
         bool initializing = (
                 msg.sender == comptrollerImplementation
-                //&&
-                //solium-disable-next-line security/no-tx-origin
-                //tx.origin == admin
         );
         bool isAdmin = msg.sender == admin;
         return isAdmin || initializing;
```

