---
description: >-
  Learn how to interface with the Boba wallet contracts to retrieve on-chain
  data.
---

# Boba Wallet Contracts

For the full documentation on how to interact with the Boba wallet contracts, visit [our Github](https://github.com/omgnetwork/optimism/blob/develop/packages/omgx/wallet-frontend/src/services/networkService.js).

### 1. Get Balances

To retrieve L2 oETH balance:

```text
this.provider.getBalance(address).
```

To retrieve ERC20 tokens, use abi from the `L2StandardERC20` contracts and run

```text
ERC20Contract.balanceOf(address)
```

Refer to the full `getBalances` documentation below:

```text
 async getBalances () {

    try {

      const rootChainBalance = await this.L1Provider.getBalance(this.account);
      const ERC20L1Balance = await this.L1ERC20Contract.connect(this.L1Provider).balanceOf(this.account);

      const childChainBalance = await this.L2Provider.getBalance(this.account);
      const ERC20L2Balance = await this.L2ERC20Contract.connect(this.L2Provider).balanceOf(this.account);

      // //how many NFTs do I own?
      const ERC721L2Balance = await this.ERC721Contract.connect(this.L2Provider).balanceOf(this.account);
      // console.log("ERC721L2Balance",ERC721L2Balance)
      // console.log("this.account",this.account)
      // console.log(this.ERC721Contract)

      //let see if we already know about them
      const myNFTS = getNFTs()
      const numberOfNFTS = Object.keys(myNFTS).length;

      if(Number(ERC721L2Balance.toString()) !== numberOfNFTS) {

        //oh - something just changed - either got one, or sent one
        console.log("NFT change detected!")

        //we need to do something
        //get the first one

        let tokenID = null
        let nftTokenIDs = null
        let nftMeta = null
        let meta = null

        //always the same, no need to have in the loop
        let nftName = await this.ERC721Contract.getName();
        let nftSymbol = await this.ERC721Contract.getSymbol();

        for (var i = 0; i < Number(ERC721L2Balance.toString()); i++) {

          tokenID = BigNumber.from(i)
          nftTokenIDs = await this.ERC721Contract.tokenOfOwnerByIndex(this.account, tokenID);
          nftMeta = await this.ERC721Contract.getTokenURI(tokenID);
          meta = nftMeta.split("#")

          const time = new Date(parseInt(meta[1]));

          addNFT({
            UUID: this.ERC721Address.substring(1, 6) + "_" + nftTokenIDs.toString() +  "_" + this.account.substring(1, 6),
            owner: meta[0],
            mintedTime: String(time.toLocaleString('en-US', { day: '2-digit', month: '2-digit', year: 'numeric', hour: 'numeric', minute: 'numeric', hour12: true })),
            url: meta[2],
            tokenID: tokenID,
            name: nftName,
            symbol: nftSymbol
          })
        }

      } else {
        // console.log("No NFT changes")
        //all set - do nothing
      }
```

### 2. Deposit ETH from L1 to Boba L2

```text
depositETHL2 = async (value='1') => {

    try {
      const depositTxStatus = await this.L1StandardBridgeContract.depositETH(
        this.L2GasLimit,
        utils.formatBytes32String((new Date().getTime()).toString()),
        {value: parseEther(value)}
      );
      await depositTxStatus.wait();

      const [l1ToL2msgHash] = await this.watcher.getMessageHashesFromL1Tx(depositTxStatus.hash);
      console.log(' got L1->L2 message hash', l1ToL2msgHash);

      const l2Receipt = await this.watcher.getL2TransactionReceipt(l1ToL2msgHash);
      console.log(' completed Deposit! L2 tx hash:', l2Receipt.transactionHash);

      this.getBalances();

      return l2Receipt;

    } catch {
      return false;
    }
  }
```

### 3. Standard exit from Boba L2 

```text
async exitOMGX(currency, value) {
    const allowance = await this.checkAllowance(currency, this.L2StandardBridgeAddress);
    // need the frontend updates
    if (BigNumber.from(allowance).lt(parseEther(value))) {
      const res = await this.approveErc20(parseEther(value), currency, this.L2StandardBridgeAddress)
      if (!res) return false;
    }
    const tx = await this.L2StandardBridgeContract.withdraw(
      currency,
      parseEther(value),
      this.L1GasLimit,
      utils.formatBytes32String((new Date().getTime()).toString()),
      { gasPrice: 0 }
    )
    await tx.wait();

    const [L2ToL1msgHash] = await this.watcher.getMessageHashesFromL2Tx(tx.hash)
    console.log(' got L2->L1 message hash', L2ToL1msgHash)

    return tx
  }
```

