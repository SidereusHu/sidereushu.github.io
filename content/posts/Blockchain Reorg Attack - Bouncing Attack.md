+++
title = "Blockchain Reorg Attack: Bouncing Attack"
date = 2025-12-01T16:57:25+08:00
draft = false
math = true
+++


# 1. Background and Core Concept

Gasper is the sub-protocol responsible for deterministic finality in Ethereum's PoS consensus. Its core objectives are:

- To continuously advance **Justification** and **Finalization** of the chain when the majority of honest participants are online;
- To form a probabilistically secure chain selection through honest majority voting when forks exist.

However, if an attacker controls:

- Close to **1/3 of the voting power**;
- The ability to **delay attestation broadcasts** (i.e., temporary network inconsistency);

They can create the following situation:

> **Make the last justified checkpoint repeatedly switch between two chains, preventing either chain from achieving Finalization.**

This is known as the **Bouncing Attack**.

The core exploitation points are:

1. Gasper requires blocks to receive **2/3 votes to be Justified**;
    
2. HLMD-GHOST directly **prunes branches that conflict with the last Justified Checkpoint**;
    
3. If the attacker can:
    
    - Allow the honest chain to receive 1/3 votes (insufficient for Finalization);
    - Later release votes for their hidden chain to leapfrog Justification;
    
    Then they can:
    
    > Force the entire network to "bounce back and forth" between two chains, with neither chain ever accumulating the 2/3 votes needed for finality.
    

---

# 2. Initial Attack Conditions

As shown in the diagram, with **cp0** already Justified, two forked chains emerge:

- `c1`: The main chain being built by honest nodes
    - Its checkpoint is `cp1`, which will extend to `b0 → cp1' → ...`
- `c2`: The hidden fork maintained by the attacker
    - Its checkpoint is `cp2`, not yet public, but "justifiable" from the attacker's perspective

Here, **"justifiable"** means:

> **Once the attacker releases their delayed attestations, cp2 can immediately achieve 2/3 votes and become the new last justified checkpoint.**

![](/images/BouncingAttack.png)

---

# 3. Attack Process (Unfolding by Epoch)

## 3.1 Epoch e+1: Honest Nodes Continue Building Chain c1

Honest validators extend on `c1`:

`cp0 → cp1 → b0 → cp1'`

Meanwhile:

- The attacker keeps new blocks and votes on `c2` from being broadcast,
- Ensuring `cp2` can be Justified at any time, but doesn't execute it yet.

At this point:

- cp1 is the canonical checkpoint in the honest world,
- cp2 is the "ready to activate" justifiable checkpoint controlled by the attacker.

---

## 3.2 Epoch e+2: Honest Chain cp1' Receives Partial Votes

As the epoch progresses:

- The honest chain `cp1'` receives over **1/3 attestations**,
- But hasn't yet received the sufficient 2/3, therefore:

`cp1' is justifiable but not justified.`

In other words:

- cp1' has reached the potential threshold for Finalization,
- But still requires consensus to progress further.

---

## 3.3 Attacker Reveals Hidden Votes, Making cp2 Overtake

At this moment, the attacker suddenly:

- **Releases all delayed votes for cp2**,
- cp2 immediately receives **2/3 attestations**,

Thus:

`cp2 becomes the new last justified checkpoint`

---

# 4. Gasper's Chain Pruning Mechanism is Triggered

Gasper (specifically HLMD-GHOST) stipulates:

> All **chains that conflict with the latest justified checkpoint** must be pruned from the choice tree.

Therefore:

- `c1` conflicts with the newly Justified `cp2`,
- `c1` is immediately **pruned**,
- `c2` becomes the new **canonical chain**,

Honest nodes are forced to:

> **Abandon the chain c1 they just extended and switch to extending c2**.

---

# 5. Attack Continues in a Loop, Achieving "Bouncing"

Now the attack state resets to:

- Honest nodes begin continuing to build c2;
- The honest chain's new checkpoint (e.g., cp2') starts accumulating votes;
- When it reaches 1/3 but is not yet Justified:

The attacker can again:

1. Create a justifiable checkpoint on the old chain `c1`;
2. Withhold votes;
3. Release them at the opportune moment;
4. Make the last justified checkpoint bounce back to `cp1'`.

This repeats:

> The last justified checkpoint "bounces" between `c1` and `c2`.

The final effect:

- **Neither chain can accumulate two consecutive 2/3 votes**,
- Therefore:

`The system cannot achieve Finalization for extended periods → liveness is critically threatened`

---

# 6. Necessary Conditions for the Attack

In summary, the bouncing attack succeeds based on:

### Condition ①: Attacker possesses at least 1/3 voting power

Ensuring:

- Can prevent the honest chain from obtaining 2/3 attestations,
- Can elevate the hidden chain to 2/3 at any time.

### Condition ②: Attacker can temporarily control attestation propagation

For example:

- The network is temporarily asynchronous ("can only be launched when the network is temporarily asynchronous"),
- Causing honest validators to be unaware of the attacker's hidden votes.

### Condition ③: Gasper's fork choice rule must enforce:

- **Conflicting chain pruning (HLMD-GHOST filter)**

This is the fundamental protocol reason the attack can succeed.

---

# 7. Attack Impact

This attack does not:

- Enable double-spending;
- Rewrite state;
- Compromise safety.

But it can:

> **Permanently block Finalization, causing deterministic finality to disappear.**

In other words:

- The chain continues running (safety is OK),
- But from a protocol perspective:

`The chain remains in a "suspended, unfinalized" state, and the value of on-chain certainty collapses.`

For financial applications, this is a **fatal liveness attack**.

---

# 8. Can the Attack Continue After GST?

The paper points out:

> The attack launch requires the network to be temporarily asynchronous (before GST), But once successful:

`Even after GST (when the network returns to synchrony), the attack can continue to be maintained.`

The reason is:

- The attacker already controls:
    1. 1/3 voting power to prevent the honest chain from independently Finalizing;
    2. Another chain that can overtake at any time through hidden votes.

Therefore, the entire bouncing process no longer requires network anomalies to continue.

---

# 9. Summary

The Bouncing Attack is a structural weakness of the Gasper protocol under partially asynchronous network conditions. Its core mechanism lies in:

1. The **2/3 voting requirement** for Justification;
2. HLMD-GHOST's **last justified checkpoint pruning rule**;
3. The attacker **possessing 1/3 voting power and the ability to delay broadcasts**.

Leveraging these conditions:

- The attacker can repeatedly make the last Justified Checkpoint switch between two chains,
- Preventing any single chain from completing two consecutive Justifications,
- Ultimately causing the **system to fail to achieve Finalization, impacting liveness but not compromising safety**.