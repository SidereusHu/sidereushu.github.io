+++
title = "Justification Withholding Reorg Attack"
date = 2025-11-18
draft = false
math = true
+++


<br>

In Ethereum's PoS Gasper consensus, "Checkpoint justification" is one of the core components of chain security:  
Only when at least _2/3_ of active validators in an epoch submit attestations pointing to a certain checkpoint will that checkpoint be "justified".

The **Justification withholding reorg attack** exploits a vulnerability in this mechanism:

> **Attackers deliberately prevent the honest chain from collecting enough attestations, keeping the honest chain from justifying checkpoints. Meanwhile, the attackers secretly build a hidden chain and accumulate sufficient attestations. Once the timing is right, they release the hidden chain all at once, directly reorganizing an entire epoch of blocks from the honest chain.**

Let's break down the entire attack process with the accompanying diagram.

![](/images/JustificationWithholdingReorg.png)


# 1. Key Attack Strategy:

**Prevent the honest chain from justifying cp0; enable the attacker's hidden chain to justify cp0.  
Finally, reveal the hidden chain → reorganize the honest chain.**

# 2. Why Is This Attack So Significant?

A single attack can cause:

- Reorganization of an entire epoch (32 blocks)
- Complete abandonment of all honest validators' work
- Dramatic weakening of liveness
- Conditions for advanced attacks like MEV reorgs and double-spending

---

# 3. Timeline Breakdown (Step-by-Step Explanation with Diagram)

## Epoch e: Attack Begins

### **(1) Honest proposer produces the last public block b0**

b0 is the last block produced by an honest proposer in epoch e (white b0 on the left side of the diagram).

### **(2) All subsequent proposers in the epoch are Byzantine ⇒ "Block withholding" begins**

Attacker proposers continue producing blocks, but these blocks:

- **Are not broadcast publicly**
- **Are hidden from honest validators**

This forms a "hidden chain" (red dashed line b1 in the upper part of the diagram).

### **(3) Result: Honest chain fails to get 2/3 attestations ⇒ Cannot justify cp0**

Because blocks are hidden on the honest chain, it **doesn't see enough attestations**,  
therefore cp0 cannot be justified on the honest chain.

---

## Epoch e+1: Honest Chain Continues, But Falls Into the Trap

### **(4) Honest proposer produces new checkpoint cp1 on top of b0**

Since the honest chain hasn't had blocks withheld from it, the honest chain's view is:

`cp0 → b0 → ... → cp1`

But here's the critical issue:

- cp1 itself cannot be justified (lacking 2/3 attestations from the previous epoch)
- The honest chain still cannot justify cp0

The honest chain becomes "stuck".

---

## End of Epoch e+1: Attacker Reveals Hidden Chain, Launches Devastating Reorg

### **(5) Byzantine nodes publish the withheld hidden chain all at once**

At this point, the attacker releases:

- The hidden b1
- Checkpoint cp2 built on b1

Moreover, this hidden chain **contains 2/3 attestations supporting cp0** (because these attestations were withheld and not published).

### **(6) Result: Attacker's chain successfully justifies cp0, while honest chain cannot**

In Gasper, justification capability is the decisive factor for checkpoint priority.  
When cp2 is published:

```
Attacker's chain: cp0 → b1 → cp2   (can justify cp0)
Honest chain:     cp0 → b0 → cp1   (cannot justify cp0)
```

The protocol prefers to select **the chain that can justify**.

---

## Final Outcome: An Entire 32 Blocks Reorganized

Since each epoch has 32 slots, and the attack spans epochs e and e+1,  
almost all blocks from epoch e+1 on the honest chain will be orphaned.

Blocks b2, b3, and cp1 marked with shading in the diagram are all discarded.

---

# 4. Why Can This Attack Still Occur in Ethereum 2025's Gasper?

There are 3 key reasons:

### **(1) Gasper's justification completely depends on seeing 2/3 attestations**

If attackers prevent the honest chain from receiving them, this is its weakness.

### **(2) Proposer weight is too significant: later proposers heavily favor the attack**

As long as the last few proposers near the end of an epoch are Byzantine:

- They can control block propagation
- They can deliberately withhold blocks
- They can privately collect attestations
- They can create a chain that appears "healthier" than the honest chain

### **(3) Gasper's "finality gadget" assumes chains will always receive attestations in time**

But in reality: Byzantine proposers may not broadcast blocks.