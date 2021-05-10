---
description: Learn more about the OMGX fraud proof fast exit solution.
---

# Quasar

## **What is Quasar?**

Quasar is a market-driven liquidity pool solution to earn interest, borrow liquid funds, and quickly withdraw assets for arbitrage through OMGX. 

## **Why Quasar?**

Withdrawing ETH and ERC-20 assets from Ethereum Layer-2 solutions takes too long, making arbitrage and cross-chain movements difficult.

<table>
  <thead>
    <tr>
      <th style="text-align:left">What is a Quasar?</th>
      <th style="text-align:left"><b>The 3 Big Benefits</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">A Quasar is a secured smart contract tied to a liquidity pool.</td>
      <td
      style="text-align:left">
        <p></p>
        <ul>
          <li>Allows users to quickly exit their funds from OMGX for a fee*. Making
            arbitrage and cross-chain movements cheaper and faster.</li>
          <li>Enables lenders to earn interest** on their funds by supplying liquidity
            to the pool.</li>
          <li>Provides borrowers liquid funds in exchange for the collateral.</li>
        </ul>
        </td>
    </tr>
  </tbody>
</table>

### Quasar Construction

Each Quasar has a liquidity pool on Ethereum that provides funds to the exiting user in return for equivalent funds \(minus an additional fast-exit fee\) on Layer-2 OMGX.

![Quasar Liquidity Pool ](https://lh5.googleusercontent.com/g3G-x4ntoko1xmBHfXqVQJ7gCrCFaQWIixtedqNYkmEYXjxPUm2Q-cXpc70j7cRljGm48bkENdut238kN6zGvowpL9tmcBBuZMP9aRUEda7tzjehZGj5vXugJOqoUvhc6EeD2GmF)

## **Quasar Fast Exit Mechanism**

If Alice wants to exit OMG Network but does not want to wait for the standard exit period \(currently two weeks\) to mature, she can swap funds with a Quasar. 

| **Alice gets a ticket from the Quasar smart contract on Ethereum, reserving the funds that she will exit. This ticket is valid for a certain amount of time before the funds are released back to the Quasar.** | **Next, Alice sends the funds to the Quasar address on OMGX Layer-2. This transaction results in an “inclusion proof” that Alice can use to confirm she has transferred the funds.** | **Now, Alice can present the inclusion proof to the Quasar contract on Layer 1. The Quasar verifies that the proof is valid and transfers the reserved funds to Alice.** |
| :--- | :--- | :--- |


  
****

  




