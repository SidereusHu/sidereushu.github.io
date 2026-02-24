+++
title = "Reorg Attacks I"
date = 2025-10-23T16:57:25+08:00
draft = false
math = true
+++

<br>

## Core Objective

1. **Core Purpose**: The goal of the attack (by Byzantine validators) is **not** to break the system (such as double-spending), but rather to manipulate block publication and voting so that their "privately held" block (b₁) defeats an honest block (b₂), thereby becoming the main chain.
    
2. **Ultimate Gain**: By making the honest b₂ block become "orphaned," Byzantine validators can "steal" the block rewards that should have belonged to the b₂ proposer, thus obtaining "higher rewards."
    

![](/images/ReorgAttack.png)

## Legend

- `normal block` (white square): A normal, consensus-confirmed block.
- `withheld block` (dashed red square): **A withheld block**. This is the key to the attack—the block has been created and privately voted on, but not broadcast to the network.
- `delayed block` (solid red square): **A delayed release block**. This is the state when a "withheld block" is finally released to the network.
- `orphaned block` (square with diagonal lines): **Orphaned block**. A valid block that was not selected for the main chain.
- `vote for a block` (circle): A vote for a block.
- `delay a block` (arrow with circle): Combined action of Byzantine validators _privately voting_ and _deciding to withhold_ (delay) this block.

## Attack Flow Breakdown

1. **Starting Point (Time = T0)**: b₀ is a `normal block` (white square). This is the commonly recognized starting main chain.
    
2. **Byzantine "Conspiracy" (Time = T1)**: After b₀, b₁ appears. Note that b₁ is drawn as a withheld block (dashed red square).
    - This means vᵢ proposed b₁, and _all_ Byzantine validators _privately_ voted for it (represented by the red circles `vote for a block` and the `delay a block` arrows in the diagram), but they **withheld** b₁ and did not broadcast it to other honest nodes.
    
3. **Honest Validators' "Open Play" (Time = T2)**: In the next slot:
    - An _honest_ validator (who is _unaware_ of b₁'s existence) proposes block b₂ based on b₀.
    - b₂ is broadcast normally. At this moment, for honest nodes, the main chain is: b₀ → b₂

4. **Attack Execution and Fork Competition (Time = T2 instant)**: Just as b₂ is proposed, Byzantine validators _immediately_ release their privately held b₁ to the network: b₁'s status changes from `withheld block` (dashed) to **`delayed block`** (solid red square).
    
    - **Result**: At this point, a **fork** appears in the network:
        - **Honest chain**: b₀ → b₂
        - **Byzantine chain**: b₀ → b₁

5. **Consensus Ruling (Time = T3)**:
    
    - Honest chain b₀ → b₂ weight = b₀'s weight + b₂'s weight (possibly only the b₂ proposer and a few nodes that just received it voted).
    - Byzantine chain b₀ → b₁ weight = b₀'s weight + b₁'s weight (b₁ proposer + _all_ Byzantine validators' premeditated votes).
    - Due to the Byzantine validators' "premeditated" votes, once the b₁ chain is released, its weight immediately exceeds the b₂ chain.

---

## Sandwich Reorg Attack

The **"Sandwich Reorg Attack"** is a sophisticated attack against Ethereum's consensus **HLMD-GHOST fork choice rule**. In Ethereum's consensus (particularly Casper FFG + LMD-GHOST), **LMD-GHOST (Latest Message Driven GHOST)** is a voting weight-based fork choice rule:

- Each validator votes (attestation) for what they consider the "heaviest chain."
- Each fork's "weight" = the number of validator votes received (weighted).
- The chain head is selected as the branch with **the highest cumulative weight**.

### "Sandwich" Attack Concept

> The attacker inserts their own hidden chain **between two honest blocks**, causing the network's weight judgment to reverse.

In Ethereum, each slot has a proposer. To **reduce empty blocks and accelerate finality convergence**, the system gives the current slot's proposer a **temporary weight boost**, known as "proposer boosting."

- This boost is typically about **70% of the total weight**.
- Meaning: The proposer's own block's branch receives temporary weighting to encourage the chain to advance toward the new block.

This mechanism itself is beneficial—but attackers can **abuse it**, leading to reorganization.


![](/images/Reorg_Sandwich.png)

### Attack Timeline

|Time|Action|Explanation|
|---|---|---|
|**t**|b₀|This is the common ancestor (starting point) of the chain.|
|**t+1**|Attacker vᵢ proposes b₁ (red block)|But the attacker **temporarily does not broadcast** b₁ (dashed border).|
|**t+2**|Honest validator proposes b₂ (gray block)|Since the attacker hasn't published b₁, everyone believes b₂ is the legitimate newest chain inheriting from b₀. Thus, most validators vote for b₂, making the b₂ chain **have higher weight** at this time.|
|**t+3**|Attacker vᵢ publishes b₁ and has accomplice vⱼ propose b₃ with b₁ as parent|The key here: b₃ uses the **proposer boosting** mechanism, gaining a 70% weight bonus for the current slot. Since this weight exceeds honest validators' voting power (less than 70%), the branch b₀→b₁→b₃ **is considered the "heavier chain"**. Thus, the original b₂ chain is reorganized (reorg'd) out.|

---

## Balancing Attack

### Attack Structure

- b₀ (white square) is the common ancestor/base chain head, existing at time t and accepted by honest nodes as the current head.
- In two consecutive slots t+1 and t+2, **both proposers are Byzantine**, vᵢ (at t+1) and vⱼ (at t+2) respectively.
- vᵢ generates block b₁ (red dashed box—withheld) at t+1, but **does not immediately broadcast** it.
- vⱼ at t+2 both publishes their own proposed b₂ and **simultaneously releases the previously hidden b₁**. Note that both b₁ and b₂ **have b₀ as parent** (binary fork, forming two parallel chains).

![](/images/Reorg_Balancing.png)

### Vote Splitting Strategy

- The attacker divides honest validators into two groups V₁ and V₂, with both groups **equal in size (or weight)**.
- The attacker prepares two sets of attestations:
    - a₁: Supporting the chain with b₁ as head (internally collected or disguised as "evidence that b₁ is heavier"), releasing a₁ only to V₁.
    - a₂: Supporting the chain with b₂ as head, releasing a₂ only to V₂.

- **Result**: V₁ members, after seeing a₁, believe b₁ is heavier and only vote for b₁; V₂ members, after seeing a₂, only vote for b₂. Thus, at the end of t+2, the accumulated attestation weight of both chains is **equal** (or nearly equal), so neither can form more than 2/3 support to finalize (under the classic 2/3 finality setting), nor can either be clearly selected as the long-term main chain by HLMD-GHOST → resulting in the system's long-term inability to achieve finality (liveness is compromised), causing system availability to collapse.

### Why This Blocks Finality (Intuitive Reason)

- In voting weight-based fork-choice (HLMD-GHOST), finality depends on **continuously appearing consecutive checkpoints that can obtain >2/3 weight support**.
- Through careful "information distribution" (selective release of attestations), the attacker **partitions the information view** of honest validators into two groups, causing each fork to receive only about half of the honest votes.
- Since no branch can gain enough majority votes, no checkpoint can accumulate beyond the _finality threshold_ (e.g., >2/3), so the chain cannot finalize—this is liveness being blocked (not safety being broken, but progress/availability being blocked).

### Attack Prerequisites

To successfully and continuously launch a balancing attack, the attacker needs at least some or all of the following capabilities/conditions:

1. **Control of two consecutive slot proposers (both consecutive proposers are Byzantine)**
    
    - This is the core assumption in the diagram: the attacker has block production rights in consecutive proposer slots, enabling them to create and delay/parallel publish forked blocks b₁, b₂.
    
2. **Selective information propagation (message partition/targeted gossip) capability**:
    
    - The attacker must be able to control which honest validators see which attestations (or blocks) first, i.e., can send messages only to V₁ or V₂. This can be accomplished through **network layer partitioning** (actual network division or delay) or through selective forwarding of certain messages.
    - Note: The attacker does not need to control honest validators' signing authority; only needs to control information visibility (who-sees-what).
    
3. **Sufficient stake/weight distribution conditions (weakened version)**:
    
    - To make both sides' weights exactly balanced, the attacker doesn't need to hold a large proportion of stake, but needs to be able to divide honest validators into two groups with roughly equal weight (this can be achieved through social engineering, network delays, directed information propagation, etc.). If honest validator weights are very uneven or difficult to divide equally, the attack cost will be higher.

4. **Sustained repeated launch capability**:
    
    - To keep the chain continuously unable to finalize, the attacker must repeat the above operations over multiple epoch/slot cycles (especially when checkpoint spans are large). If it's only a one-time operation, the network may still recover finality subsequently.

**Summary**: The attack heavily relies on network layer manipulation and proposer control. In environments where the network is partially controllable or the attacker can control several proposers, this is a realistic threat.

---

## Success Probability / Cost 

- If proposer election is random and public, and the attacker cannot long-term control consecutive proposers, the attack success probability is low.

- If the attacker can long-term control several proposers (or occupy a high proportion of proposer positions through Byzantine nodes), or can temporarily partition the network/selectively forward, the success probability significantly increases.

- Costs include: acquiring consecutive proposers (stake cost or election probability), constructing and maintaining network control costs for targeted gossip, bearing possible slashing/penalty risks (if detection and penalty mechanisms exist).