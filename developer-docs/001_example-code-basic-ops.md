---
description: Learn how to use basic features of Boba (e.g. bridges, basic L2 ops) through example
---

# OVM 2.0 Basic Operations Code examples

To see examples of how to perform basic operations on Boba v2, please see the react code for the [Boba gateway](https://github.com/omgnetwork/optimism-v2/blob/develop/packages/boba/gateway/src/services/networkService.js).

### An L2->L2 transfer

```javascript

//Transfer funds from one account to another, on the L2
async transfer(address, value_Wei_String, currency) {

	let tx = null

	try {
	  
	  if(currency === allAddresses.L2_ETH_Address) {
	    
	    //we are transferring ETH - special call
	    let wei = BigNumber.from(value_Wei_String)

	    tx = await this.provider.send('eth_sendTransaction', 
	      [
	        { 
	          from: this.account,
	          to: address,
	          value: ethers.utils.hexlify(wei)
	        }
	      ]
	    )

	  } else {
	    // we are transferring an ERC20...
	    tx = await this.STANDARD_ERC20_Contract
	    	.attach(currency)
	    	.transfer(
		      address,
		      value_Wei_String
		    )
	    await tx.wait()
	  }
	  
	  return tx
	} catch (error) {
	  console.log("Transfer error:", error)
	  return error
	}
}


```

### An L1->L2 Classical Bridging Operation

```javascript

  //Move ERC20 Tokens from L1 to L2
  async depositErc20(value_Wei_String, currency, currencyL2) {

    const L1_TEST_Contract = this.L1_TEST_Contract.attach(currency)

    let allowance_BN = await L1_TEST_Contract.allowance(
      this.account,
      allAddresses.L1StandardBridgeAddress
    )

	const allowed = allowance_BN.gte(BigNumber.from(value_Wei_String))

	if(!allowed) {
		const approveStatus = await L1_TEST_Contract.approve(
		  allAddresses.L1StandardBridgeAddress,
		  value_Wei_String
		)
		await approveStatus.wait()
		console.log("ERC 20 L1 ops approved:",approveStatus)
	}

	const depositTxStatus = await this.L1StandardBridgeContract.depositERC20(
		currency,
		currencyL2,
		value_Wei_String,
		this.L2GasLimit,
		utils.formatBytes32String(new Date().getTime().toString())
	)

	//at this point the tx has been submitted, and we are waiting...
	await depositTxStatus.wait()

	const [l1ToL2msgHash] = await this.watcher.getMessageHashesFromL1Tx(
		depositTxStatus.hash
	)
	console.log(' got L1->L2 message hash', l1ToL2msgHash)

	const l2Receipt = await this.watcher.getL2TransactionReceipt(
		l1ToL2msgHash
	)
	console.log(' completed Deposit! L2 tx hash:', l2Receipt.transactionHash)

	return l2Receipt
    
  }

```

