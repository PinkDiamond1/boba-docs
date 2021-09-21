---
description: Learn more about the Boba Network chain contracts
---

# Chain Contracts

The Chain is composed of a set of contracts running on the Ethereum mainnet. These contracts store ordered lists of:

1. An _ordered_ list of all transactions applied to the L2 state.
2. The proposed state root which would results from the application of each transaction.
3. Transactions sent from L1 to L2, which are pending inclusion in the ordered list.

The chain is composed of the following concrete contracts:

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-canonicaltransactionchain-ctc)[`OVM_CanonicalTransactionChain` \(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/chain/OVM_CanonicalTransactionChain.sol)\(CTC\) <a id="ovm-canonicaltransactionchain-ctc"></a>

The Canonical Transaction Chain \(CTC\) contract is an append-only log of transactions which must be applied to the OVM state. It defines the ordering of transactions by writing them to the `CTC:batches` instance of the Chain Storage Container. The CTC also allows any account to `enqueue()` an L2 transaction, which the Sequencer must eventually append to the rollup state.

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-statecommitmentchain-scc)[`OVM_StateCommitmentChain` \(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/chain/OVM_StateCommitmentChain.sol)\(SCC\) <a id="ovm-statecommitmentchain-scc"></a>

The State Commitment Chain \(SCC\) contract contains a list of proposed state roots which Proposers assert to be a result of each transaction in the Canonical Transaction Chain \(CTC\). Elements here have a 1:1 correspondence with transactions in the CTC, and should be the unique state root calculated off-chain by applying the canonical transactions one by one.

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-chainstoragecontainer)[`OVM_ChainStorageContainer`\(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/chain/OVM_ChainStorageContainer.sol) <a id="ovm-chainstoragecontainer"></a>

Provides reusable storage in the form of a "Ring Buffer" data structure, which will overwrite storage slots that are no longer needed. There are three Chain Storage Containers deployed, two are controlled by the CTC, one by the SCC.

