# Going Forward

If you made it this far, congrats— you’re done! You’ve successfully converted the core Uniswap V2 repository to be compatible with the OVM! 🎉

A diff of the full set of required changes made be found [here](https://github.com/ScopeLift/uniswap-v2-core/compare/master...ScopeLift:optimism-integration).

Hopefully, you’ve gotten a pretty good sense of what it takes to upgrade your own projects to run on the OVM as well. To review, most projects will require three kinds of changes, which we walked through above:

1. Tooling updates
2. Test suite updates
3. Contract and compiler modifications

Before you start converting other contracts for the OVM, there are a few other important distinctions worth mentioning. Let’s wrap up by reviewing these.

#### OVM vs. EVM: Contract Wallets <a id="OVM-vs-EVM-Contract-Wallets"></a>

Right now, when you run `yarn test:ovm`, all your tests will pas as expected. But let’s say we wanted to do some debugging, and we only want to run the `createPair:gas` test of `UniswapV2Factory.spec.ts`. No problem, let’s just throw a `.only` modifier on that test and re-run our test command:

```text
- it('createPair:gas', async () => {
+ it.only('createPair:gas', async () => {
    const tx = await factory.createPair(...TEST_ADDRESSES)
    const receipt = await tx.wait()
    expect(receipt.gasUsed).to.eq(isOVM ? 4121083 : 2512920)
  })
```

All of a sudden, this test now fails! Like the EVM, gas usage on the OVM is deterministic, so what’s going on here? The answer is simple: for any given account, their first ever transaction is a bit more expensive as a [contract wallet](https://github.com/ethereum-optimism/contracts-v2/tree/master/contracts/optimistic-ethereum/OVM/accounts) is deployed for that user.

#### OVM vs. EVM: Gas, Part II <a id="OVM-vs-EVM-Gas-Part-II"></a>

The gas situation in OVM is actually a bit more complex than described earlier. There are actually three types of gas! However, we can simplify things quite a bit for now so you only need to worry about one type of gas.

As mentioned above the transaction gas limit is 9M gas. This was chosen because it’s high enough to allow most transaction types, but low enough that it’s unlikely the L1 gas limit drops to this value. As a user and a developer, all you currently need to worry about is ensuring that transactions don’t use above 9M gas, otherwise they will revert!

#### Wrapping Up <a id="Wrapping-Up"></a>

You’re now armed with all the knowledge you need to begin converting your Solidity projects to run on Optimism. As you’ve seen, the process is mostly about tooling and testing, with only minor modifications required to your smart contract code. We hope you’re as excited as we are to start writing secure, scaleable contracts on the OVM.

