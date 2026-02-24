+++
title = "Unrealized Justification Reorg Attack"
date = 2025-11-11
draft = false
math = true
+++


<br>

Ethereum now uses a Proof-of-Stake (PoS) consensus mechanism called **Gasper**. Gasper integrates two protocols: **Casper the Friendly Finality Gadget (FFG)**, a protocol ensuring the finality of transactions; and a modified version of the **Greedy Heaviest-Observed Sub-Tree (HLMD GHOST)** for selecting the canonical chain.

In the unrealized justification reorg attack, the attacker manipulates the justified update of checkpoints to alter the "filtered/visible" view of the block tree for honest nodes. Specifically, the attacker creates and publishes a branch containing a new checkpoint, causing honest nodes to filter out the original branch (which is canonical according to weight) from their local fork-choice viable space, thereby achieving reorganization or blocking finality.

The unrealized justification reorg attack reveals a vulnerability in the interaction between Ethereum PoS's GHOST layer and FFG layer:

- The FFG layer is responsible for finality, but its justified state is directly used by the GHOST layer (as the starting point for fork choice);
    
- The attacker can forge a "logically reasonable but not actually realized" justified checkpoint;
    
- This causes the GHOST layer to discard the honest branch based on incorrect state;
    
- Thus resulting in chain reorganization or even degraded security.
    

![](/images/UnrealizedJustificationReorgAttack.png)

## Attack Steps:

1. During normal operation in epoch `e`, honest validators maintain the canonical chain `c1` with checkpoint `cp0`, where `b1`, `b2`, and `b3` are blocks in various slots within the epoch (`b3` is the last block of the epoch, and `b1` is an earlier block, specifically at slot 22).
    
2. In epoch `e`, block `b1` (slot 22) happens to be the **first block containing sufficient attestations to justify `cp0`** (i.e., when `b1` is seen, there are enough attestations pointing to it to make `cp0` justifiable), but in the honest global view, this justification is not immediately triggered on the canonical chain (or is not considered as last-justified by honest nodes).
    
3. In epoch `e+1`, the attacker (or a controlled proposer) constructs and publishes a checkpoint `cp1`, deliberately setting its parent to `b1` (although the more reasonable parent on the honest chain should be `b3`). Thus, `cp1` belongs to an "early fork" chain `c2` (extending from the earlier `b1`), rather than the honest `c1`.
    
4. When honest nodes receive this `cp1` (along with its accompanying attestations/aggregates), according to pseudocode lines 22–23, they **update the checkpoint state**: locally treating `cp0` as justified on chain `c2` (or pointing `cp0`'s last-justified to the `c2` view), thereby switching the last-justified root to the copy of `cp0` under `c2`.
    
5. Once `cp0` becomes last-justified under `c2`, HLMD-GHOST's FORKCHOICE (which selects the heaviest subtree starting from last-justified) will **use the subtree under `cp0` in `c2` as the root** to continue selecting the heaviest subtree. Therefore, `c1` (the honest chain) will be viewed as a "non-root path" and **filtered out** (i.e., FORKCHOICE no longer explores down `c1`), thus achieving de-selection/de-visibility of the honest chain.
    
6. To ensure that `cp1` can justify `cp0`, the attacker needs `b1` to appear at a position within the epoch such that `cp1` can obtain sufficient attestations. In the diagram example: `b1` is chosen to be at slot ⌈32 × 2/3⌉ = 22.
    

<br>

## Parameterized Calculation: Why Choose Slot 22? Why Orphan at Most 10 Blocks?

**Key number**: To mark a checkpoint `cp0` as **justified**, ⌈32 × 2/3⌉ = 22 validator attestations are required (because checkpoints are based on epochs, each epoch has a fixed committee size, and the finality threshold is 2/3 of total weight; in terms of quantity, rounding up gives 22).

**Attacker's strategy**: Let `b1` appear at the 22nd slot of epoch `e` (counting from 1 to 32), so that after `b1` is included/published, **at most 32 − 22 = 10 subsequent slot blocks can still appear** (these subsequent blocks are produced by honest proposers in the same epoch but after `b1`). Therefore, when the attacker publishes `cp1` with `b1` as parent in epoch `e+1`, at most these 10 subsequent honest blocks will be orphaned (no longer canonical). This design allows the attacker to keep the "damage" within an upper bound (compared to choosing a very early `b1` which would orphan more blocks and be more easily detected).

**Summary**:

- 22 is because ⌈2/3 × 32⌉;
    
- 10 = 32 − 22 is the maximum number of honest blocks orphaned (within that epoch).
    

<br>

## Attack Prerequisites and Cost

To successfully launch this type of unrealized-justification reorg, the attacker typically needs to satisfy:

1. **Control a proposer for an epoch** (specifically, the proposer who can propose `cp1` in epoch `e+1`), or be able to tamper with the proposer's message to accept the attacker-provided parent.
    
2. **Replicate/obtain sufficient attestations or exploit network distribution**, i.e., be able to make honest nodes see `cp1` and accept its accompanying attestations (at least meeting the condition for `cp0` to be viewed as justified). This typically requires the attacker to control which messages are seen by which validators (targeted gossip), or release pre-aggregated attestations at the appropriate time.
    
3. The attacker does not need to control 2/3 of the stake, but needs precise control over message propagation timing and parent specification.
    
4. The attacker bears certain risks: if honest nodes can detect selective/anomalous parent specification behavior in a short time, or if the protocol has additional checks on checkpoint updates (such as requiring aggregates or time windows), the attack will fail or the attacker will be slashed (if their behavior violates slashing conditions).
    

**Attack cost**: Resources related to attack success opportunity include proposer control (stake or time window), network layer control (partitioning or selective forwarding), and the risk of being discovered/punished.

<br>

## Feasible Mitigation Measures

To resist unrealized-justification reorgs, it is recommended to place defenses on three parallel layers:

1. **Propagation redundancy and aggregate verifiability** (network + message format level) — Only switch `last_justified` to that checkpoint when a verified aggregated attestation (≥2/3 signatures) is seen and the aggregate has been confirmed by other nodes through multiple independent propagation paths.
    
2. **Conservative in-place decision strategy** (node level) — Nodes must meet the condition of "sufficient evidence and multi-path visibility" before locally updating `last-justified` / changing the fork-choice starting point; introduce "freshness / stability" checks for suspicious parents and reduce their weight.
    
3. **Detection/alerting/operational tools** (monitoring layer) — Record parent pointers, attestation arrival timelines, automatically score and alert, and can trigger alternative propagation or replacement aggregators.