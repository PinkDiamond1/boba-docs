---
description: Learn more about the Boba Network execution contracts
---

# Execution Contracts

The Execution contracts implement the Optimistic Virtual Machine, or OVM. Importantly, these contracts must execute in the same deterministic manner, whether a transaction is run on Layer 2, or Layer 1 \(during a challenge\).

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-executionmanager)[`OVM_ExecutionManager`\(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/execution/OVM_ExecutionManager.sol) <a id="ovm-executionmanager"></a>

The Execution Manager \(EM\) is the core of our OVM implementation, and provides a sandboxed environment allowing us to execute OVM transactions deterministically on either Layer 1 or Layer 2. The EM's run\(\) function is the first function called during the execution of any transaction on L2. For each context-dependent EVM operation the EM has a function which implements a corresponding OVM operation, which will read state from the State Manager contract. The EM relies on the Safety Checker to verify that code deployed to Layer 2 does not contain any context-dependent operations.

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-safetychecker)[`OVM_SafetyChecker`\(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/execution/OVM_SafetyChecker.sol) <a id="ovm-safetychecker"></a>

The Safety Checker verifies that contracts deployed on L2 do not contain any "unsafe" operations. An operation is considered unsafe if it would access state variables which are specific to the environment \(ie. L1 or L2\) in which it is executed, as this could be used to "escape the sandbox" of the OVM, resulting in non-deterministic challenges. That is, an attacker would be able to challenge an honestly applied transaction. Note that a "safe" contract requires opcodes to appear in a particular pattern; omission of "unsafe" opcodes is necessary, but not sufficient.

The following opcodes are disallowed:

* `ADDRESS`
* `BALANCE`
* `ORIGIN`
* `EXTCODESIZE`
* `EXTCODECOPY`
* `EXTCODEHASH`
* `BLOCKHASH`
* `COINBASE`
* `TIMESTAMP`
* `NUMBER`
* `DIFFICULTY`
* `GASLIMIT`
* `GASPRICE`
* `CREATE`
* `CREATE2`
* `CALLCODE`
* `DELEGATECALL`
* `STATICCALL`
* `SELFDESTRUCT`
* `SELFBALANCE`
* `SSTORE`
* `SLOAD`
* `CHAINID`
* `CALLER`\*
* `CALL`\*
* `REVERT`\*

\* The `CALLER`, `CALL`, and `REVERT` opcodes are also disallowed, except in the special case that they appear as part of one of the following strings of bytecode:

1. `CALLER PUSH1 0x00 SWAP1 GAS CALL PC PUSH1 0x0E ADD JUMPI RETURNDATASIZE PUSH1 0x00 DUP1 RETURNDATACOPY RETURNDATASIZE PUSH1 0x00 REVERT JUMPDEST RETURNDATASIZE PUSH1 0x01 EQ ISZERO PC PUSH1 0x0a ADD JUMPI PUSH1 0x01 PUSH1 0x00 RETURN JUMPDEST`
2. `CALLER POP PUSH1 0x00 PUSH1 0x04 GAS CALL`

Opcodes which are not yet assigned in the EVM are also disallowed.

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-statemanager)[`OVM_StateManager`\(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/execution/OVM_StateManager.sol) <a id="ovm-statemanager"></a>

The State Manager contract holds all storage values for contracts in the OVM. It can only be written to by the Execution Manager and State Transitioner. It runs on L1 during the setup and execution of a challenge. The same logic runs on L2, but has been implemented as a precompile in the L2 go-ethereum client.

#### [\#](https://community.optimism.io/docs/protocol/protocol.html#ovm-statemanagerfactory)[`OVM_StateManagerFactory`\(opens new window\)](https://github.com/omgnetwork/optimism/blob/develop/packages/contracts/contracts/optimistic-ethereum/OVM/execution/OVM_StateManagerFactory.sol) <a id="ovm-statemanagerfactory"></a>

The State Manager Factory is called by a State Transitioner's init code, to create a new State Manager for use in the challenge process.

