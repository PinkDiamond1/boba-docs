---
description: Learn how bridging assets between Ethereum L1 and OMGX L2 works.
---

# Bridging L1 and L2

Apps on OMGX can be made to interact with apps on Ethereum via a process called "bridging". In a nutshell, **contracts on OMGX can send messages to contracts on Ethereum, and vice versa**.

### L1 &lt;&gt; L2 Basics

At a high level, this process is pretty similar to the same process for two contracts on Ethereum \(with a few caveats\). **Communication between L1 and L2 is enabled by two special smart contracts called the "messengers"**. Each layer has its own messenger contract which serves to abstract away some lower-level communication details, a lot like how HTTP libraries abstract away physical network connections.

We won't get into _too_ much detail about these contracts here — the only thing you really need to know about is the `sendMessage` function attached to each messenger:

```text
function sendMessage(
    address _target,
    bytes memory _message,
    uint32 _gasLimit
) public;
```

Look familiar? It's the same as that `call` function we used earlier. We have an extra `_gasLimit` field here, but `call` has that too. This is basically equivalent to:

```text
address(_target).call{gas: _gasLimit}(_message);
```

Except, of course, that we're calling a contract on a completely different network!

We're glossing over a lot of the technical details that make this whole thing work under the hood but whatever. Point is, it works! Want to call a contract on OMGX from a contract on Ethereum? It's dead simple:

```text
// Pretend this is on L2
contract MyOMGXContract {
    doSomething() public {
        // ... some sort of code goes here
    }
}

// And pretend this is on L1
contract MyOtherContract {
    function doTheThing(address myOMGXContractAddress, uint256 myFunctionParam) public {
        ovmL1CrossDomainMessenger.sendMessage(
            myOMGXContractAddress,
            abi.encodeWithSignature(
                "doSomething(uint256)",
                myFunctionParam
            ),
            1000000 // use whatever gas limit you want
        )
    }
}
```

{% hint style="info" %}
**Using the messenger contracts**

These messenger contracts`OVM_L1CrossDomainMessenger, OVM_L2CrossDomainMessenger, OVM_L1CrossDomainMessengerFast` always come pre-deployed to each of our networks. You can find the exact addresses of these contracts on our various deployments [inside of the OMGX repo](https://github.com/omgnetwork/optimism)
{% endhint %}

### Caveats

**\#Communication is not instantaneous**

Calls between two contracts on Ethereum happen synchronously and atomically within the same transaction. That is, you'll be told about the result of the call right away. Calls between contracts on OMGX and Ethereum happen _asynchronously_. If you want to know about the result of the call, you'll have to wait for the other contract send a message back to you.

**\#Accessing msg.sender**

Contracts frequently make use of `msg.sender` to make decisions based on the calling account. For example, many contracts will use the [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) pattern to selectively restrict access to certain functions. Because messages are essentially shuttled between L1 and L2 by the messenger contracts, **the `msg.sender` you'll see when receiving one of these messages will be the messenger contract** corresponding to the layer you're on.

In order to get around this, we added a `xDomainMessageSender` function to each messenger:

```text
function xDomainMessageSender() public returns (address);
```

If your contract has been called by one of the messenger contracts, you can use this function to see who's _actually_ sending this message. Here's how you might implement an `onlyOwner` modifier on L2:

```text
modifier onlyOwner() {
    require(
        msg.sender == address(ovmL2CrossDomainMessenger)
        && ovmL2CrossDomainMessenger.xDomainMessageSender() == owner
    );
    _;
}
```

### Understanding the Challenge Period <a id="understanding-the-challenge-period"></a>

One of the most important things to understand about L1 ⇔ L2 interaction is that **messages sent from Layer 2 to Layer 1 cannot be relayed for at least one week, unless you use the OMGX Fast Exit Liquidity Pool.** The fast-exit liquidity pool makes use of our custom `OVM_L1CrossDomainMessengerFast` and enables near instant withdrawals from L2 ⇔ L1. If you decide to opt out of using the LP, messages you send from Layer 2 will only be received on Layer 1 after this one week period has elapsed. We call this period of time the "challenge period" because it's a result of one of the core security mechanisms of the Optimistic Rollup: the transaction result challenge.

{% hint style="info" %}
OMGX charges a small convenience fee for making use of the fast-exit liquidity pool. Any associated risks of removing the 7 day challenge period only apply to the liquidity pool users.
{% endhint %}

Optimistic Rollups are "optimistic" because they're based around the idea of publishing the _result_ of a transaction to Ethereum without actually executing the transaction on Ethereum. In the "optimistic" case, this transaction result is correct and we can completely avoid the need to perform complicated \(and expensive\) logic on Ethereum. 

However, we still need some way to prevent incorrect transaction results from being published in place of correct ones. Here's where the "transaction result challenge" comes into play. Whenever a transaction result is published, it's considered "pending" for a period of time known as the challenge period. During this period of time, anyone may re-execute the transaction _on Ethereum_ in an attempt to demonstrate that the published result was incorrect.

If someone successfully executes this challenge, then the result is scrubbed from existence and anyone can publish another result in its place \(hopefully the correct one this time, financial punishments make incorrect results _very_ costly for their publishers\). Once the window for a given transaction result has fully passed without a challenge the result can be considered fully valid \(or else someone would've challenged it\).

#### On the length of the challenge period

We've set the challenge period to be exactly seven days on the OMGX mainnet. We believe this is a reasonable balance between security and usability, with an emphasis on increased security to start. 

