+++
title = "Reorg Attacks in Ethereum PoS"
date = 2025-11-04T16:57:25+08:00
draft = false
math = true
+++


<br>


In this article, we expand on Reorg Attacks once again. We mainly analyze how Reorg Attacks are launched in the Ethereum PoS Protocol and identify the vulnerable points where attacks can be launched. Before this, combined with our previous meeting discussions, it is necessary to first review the Ethereum PoS Protocol to help us reach consensus on fundamental knowledge.


## I.  Preliminaries

#### 1.1  Gasper

Ethereum now uses a Proof-of Stake (PoS) consensus mechanism called **Gasper**. Gasper integrates two protocols: **Casper the Friendly Finality Gadget (FFG)**, a protocol ensuring the finality of transactions; a modified version of the **Greedy Heaviest-Observed Sub-Tree (HLMD GHOST)** for selecting the canonical chain. 

Namely, every honest validator in the system only proposes new blocks that extend its canonical chain and votes for blocks on its canonical chain. Eventually, one chain will be finalized according to FFG, so the system achieves **safety** (i.e., no double spending) and **liveness** (i.e., transactions submitted to the system are eventually finalized).


#### 1.2  Security properties 

The Ethereum PoS protocol satisfies the following properties. 

	• (Safety) If an honest validator finalizes a chain led by block b, another honest validator finalizes a chain led by block b ′ , and the two chains have the same length, b = b ′ . 
	
	• (Liveness) The length of the finalized chain eventually grows for all honest validators.


(**Time**). Time is divided into *epochs* and each epoch includes 32 slots. Each slot lasts for 12 seconds. Each validator is assigned to one slot in an epoch randomly. 

#### 1.3  Why does Ethereum's Casper FFG choose to vote once every 32 slots (one epoch), rather than every slot, or every 64, 128, or longer intervals?

If voting occurs at every slot, meaning an attestation (vote) must be submitted in each slot (12 seconds), it would cause message explosion. Even with aggregation (aggregated signatures) to reduce data volume, aggregation itself requires additional network communication (BLS signature aggregation involves many-to-many gossip), and validators still need to broadcast, sign, verify, and synchronize across the entire network. **Network bandwidth, signature verification burden, and on-chain storage costs would all rise exponentially.** Moreover, with voting at every slot:

- Vote results would change almost every 12 seconds;
- The consensus mechanism would need to constantly update justified/finalized states;
- Fork choice stability would greatly decrease (due to frequent vote graph oscillations).

Therefore, to control system load and consensus fluctuations, Ethereum designed a "pace controller": **epoch (32 slots)**.

Through simulation and testing, the Ethereum research team found:

> 32 slots (≈ 6.4 minutes) represents a reasonable balance point among network propagation delay, validator count, and finality delay.

PS: *We can refer to the computational model behind Ethereum's research conclusions and, combined with our project background, set an appropriate balance point.*

If the epoch becomes larger and voting frequency decreases, it brings significant security and liveness issues:

1. **Decreased Security**
    - FFG's security proof relies on "2/3 validator's latest votes." If the voting cycle is lengthened, attackers have more time to manipulate network delays or forks, creating conflicting justified checkpoints.
    - The longer the delay, the easier the system falls into an uncertain state unable to determine the canonical chain.
2. **Excessive Finality Delay**
    - Assuming a checkpoint can only be justified once every 64 slots, it would take at least two epochs to finalize (FFG requires two consecutive justifications).
    - This means at least 2×64×12=1536 seconds ≈ 25.6 minutes to achieve finality, which is extremely poor for user experience.
3. **Extended Attack Window**
    - Validator "slashing conditions" (double-signing penalties) are based on constraints between checkpoint votes (source-target relationships).
    - If epoch intervals are too large, attackers can manipulate more chain states between votes (e.g., double vote), and system detection delay increases.



#### 1.4  Basic concepts

(**Time**). Time is divided into *epochs* and each epoch includes 32 slots. Each slot lasts for 12 seconds. Each validator is assigned to one slot in an epoch randomly. 

(**Block**). A block *b* consists of four fields: the *slot number*, the *hash of the parent block*, *a set of attestations*, and *a batch of transactions*.

(**Attestation**). An attestation is a vote by an attestor, denoted as *att*. Each *att* consists of the *slot number*, *hashes of source and target checkpoints*, and *the hash of head block*. The slot number implies the time when the attestation is created. The source and target are used for finality. The source is the last justified checkpoint (to be described shortly) and the target is the last checkpoint block received by the validator. The head field is selected by the HLMD GHOST rule, which is the leaf block of the canonical chain. We say *att* is an attestation for the block in the head field or an attestation for the checkpoint block in the target field, without any ambiguity.

(**Aggregated attestation**). The attestations that share identical *source*, *target*, and *head* can be aggregated into a single aggregated attestation. If attestation is a vote for the block *b* in the head field, it is also considered a vote for all the blocks led by *b*.

(**Finality**). A checkpoint block *cp* might be justified and finalized. Informally, if a block is finalized, its order will never be reversed. Checkpoint *cp* is justified after two-thirds of attestations with *cp* as target are included in the chain. If a checkpoint that extends the justified block *cp* is also justified, *cp* is finalized. When *cp* is finalized, any blocks on the chain led by *cp* are finalized. A block can be justified or finalized according to the Casper FFG protocol.



## II.  Ethereum PoS Protocol for validator $v_i$.

**Global / Local Parameters**

- `t`: Global slot counter (time counter).
- Each validator `vi` maintains locally:
    - `T`: Block tree (locally observed block/fork tree);
    - `P`: Attestation pool (collected attestation and aggregate messages).
        

![](/images/EthPoS0.png)

---

#### 1.  Slot Start — Proposer Phase (lines 01–07)

![](/images/EthPoS1_7.png)

- At the start of slot `t`, if `vi` is selected as the proposer for this slot:
    - Call `FORKCHOICE` to get the current canonical head `p` (line 03).
    - Retrieve unincluded attestation set `atts` from local `P` (typically, proposers package them into new blocks to reduce bandwidth/encourage propagation).
    - Package transactions `txs`, construct `PROPOSE` message and broadcast.
- **Goal**: Advance the chain, provide packed transactions and attestations included in the block to help the network reach consensus on weight.


#### 2.  Attestor Phase (lines 08–14)

![](/images/EthPoS8_14.png)

- At `T + ∆` (allowing for message propagation), committee members vote on the current `FORKCHOICE` output `h`, and report the current `source checkpoint (s)` and `target checkpoint (c)`.
- These attestations are used for Casper FFG's justify/finalize logic (finality).
- Note: Attestations are first sent to the committee (or entire network), then aggregated by aggregators.


#### 3.1  Aggregator Phase (lines 15–19)

![](/images/EthPoS15_19.png)

- Aggregators collect attestations for slot `t` (line 17), use BLS or other aggregated signatures to construct `AGGREGATE`, and broadcast to all validators.
- Aggregation reduces bandwidth and allows most validators to quickly see the overall voting situation for the slot.



##### 3.2  Aggregator Phase: Block Reception Processing (lines 20–23)

![](/images/EthPoS20_23.png)

- Upon receiving a new block, add it to the local block tree `T`.
- If the block is a checkpoint (i.e., the block of the first slot of an epoch), **update checkpoint state based on the block's parent chain** (this point is exploited in Attack-II).


##### 3.3 Aggregator Phase: Receiving attestation / aggregate (lines 24–25)

![](/images/EthPoS24_25.png)

Store received attestations or aggregates in local `P`. These attestations form the basis for weight calculation in FORKCHOICE.




![](/images/ReorgAttack.png)


![](/images/Reorg_Sandwich.png)



![](/images/Reorg_Balancing.png)



#### FORKCHOICE Implementation (HLMD-GHOST form) (lines 26–35)

![](/images/EthPoS26_35.png)

- Starting from the `last justified checkpoint` as root, recursively select the root of the "heaviest weighted subtree" until reaching a leaf node; return the leaf as the current head.
- Tie-break uses alphabetical order (theoretical simplification). Actual implementations may use hash or VRF for more random tie-breaking.
- Line 30 is marked as **the exploitation point for Attack-I**: Attackers manipulate attestations (or proposer boosting) to change subtree weights, thereby influencing FORKCHOICE selection.

## Attack-I Exploitation Point (line 30)

- **Essence**: HLMD-GHOST only selects the heaviest subtree based on attestation weight. If an attacker can control which attestations are seen by which nodes, or artificially increase the "effective weight" of a certain branch (e.g., proposer boosting), they can make a malicious chain appear "heavier," thus being selected by FORKCHOICE.
- **Typical Methods**: Withholding blocks/attestations, selective release, proposer boosting (temporarily increasing the weight of the proposer's proposed branch).
- **Consequences**: Causes reorg, transaction rollback, disrupts liveness (or in extreme cases affects finality).




