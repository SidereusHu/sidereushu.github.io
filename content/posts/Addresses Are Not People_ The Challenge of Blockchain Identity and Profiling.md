+++
title = "Addresses Are Not People: The Challenge of Blockchain Identity and Profiling"
date = 2026-02-24T16:57:25+08:00
draft = false
math = true
+++


<br>
  
> In traditional financial risk control, we analyze "people"—individuals or legal entities with names, ID numbers, and credit histories. But in the on-chain world, we face "addresses"—strings of 42 hexadecimal characters. The chasm between address and identity, between transactions and behavior, is the core battleground for Web3 risk control.

---

## I. The Nature of Addresses: Pseudonymity and the Identity Dilemma

### 1.1 What Could Be Behind an Address?

When we look at an Ethereum address like `0x28c6c06298d514db089934071355e5743bf21d60`, it could represent:

```
┌─────────────────────────────────────────────────────────────┐
│                    What an Address Can Represent            │
├─────────────────────────────────────────────────────────────┤
│  • A real user's main wallet                                 │
│  • One of many wallets belonging to the same user            │
│  • An exchange's hot wallet (representing millions of users)│
│  • A smart contract (code, not a person)                     │
│  • A multi-signature wallet (controlled by multiple people)  │
│  • A DAO treasury (owned by a community)                     │
│  • A hacker's temporary intermediate address                 │
│  • A victim's address with a stolen private key              │
└─────────────────────────────────────────────────────────────┘
```

This complex "one-to-many" and "many-to-one" mapping is the fundamental challenge of on-chain identity.

### 1.2 The Double-Edged Sword of Pseudonymity

Blockchain's "pseudonymity" is often mistaken for "anonymity." Actually:

- **Anonymity**: Completely untraceable, like cash transactions.
- **Pseudonymity**: Trading under a pseudonym (address), with all actions publicly auditable.

This means: **Once an address is linked to a real-world identity, its entire historical behavior becomes exposed.**

Several typical cases from 2024 illustrate this:

**Case 1: Tornado Cash Developer Arrest (2024)**

Alexey Pertsev was sentenced by a Dutch court to 64 months in prison for developing Tornado Cash. Prosecutors used on-chain analysis to trace fund flows connected to the mixer. The verdict sparked significant controversy—should developers be held responsible for user actions? But from a technical perspective, it demonstrated the power of on-chain tracking: even with privacy tools, fund flows can be analyzed.

**Case 2: On-Chain Footprint of the Lazarus Group**

According to Chainalysis's 2024 report, the Lazarus Group stole over $600 million in crypto assets in 2023. Despite using complex obfuscation techniques, on-chain analytics firms were able to trace most of the funds. Their behavioral patterns—rapid dispersal after large hacks, using specific bridges, and final conversion via OTC desks—form a recognizable "fingerprint."

### 1.3 The Three Levels of Identity Resolution

```
                Identity Resolution Pyramid

              ┌─────────────┐
              │ Attribution │ ← Address → Real Identity (Hardest)
              │    Layer    │   Requires off-chain data/legal cooperation
              ├─────────────┤
              │  Clustering │ ← Multiple Addresses → Same Entity
              │    Layer    │   Based on behavior patterns & fund flows
              ├─────────────┤
              │   Layer     │ ← Address → Type/Role
              │Classification│   Based on on-chain features
              └─────────────┘
```

Current technology handles the "Classification Layer" well, has some capability in the "Clustering Layer," but the "Attribution Layer" remains highly dependent on off-chain information.

---

## II. Technical Framework for Address Profiling

### 2.1 Core Dimensions of a Profile

A comprehensive address profile should include the following dimensions:

```
┌────────────────────────────────────────────────────────────────┐
│                    Address Profiling Framework                 │
├────────────────┬───────────────────────────────────────────────┤
│   Dimension    │  Content                                      │
├────────────────┼───────────────────────────────────────────────┤
│  Basic         │ Type (EOA/Contract), Balance, First Active,   │
│  Attributes    │ Code Features                                  │
├────────────────┼───────────────────────────────────────────────┤
│  Behavioral    │ Transaction Frequency, Active Hours,          │
│  Features      │ Amount Distribution, Operation Preferences    │
├────────────────┼───────────────────────────────────────────────┤
│  Relationship  │ Interacting Counterparties, Fund Sources/Sinks│
│  Network       │ Community Affiliation                          │
├────────────────┼───────────────────────────────────────────────┤
│  Tag System    │ Entity Tags, Behavior Tags, Risk Tags,        │
│                │ Contract Tags                                  │
├────────────────┼───────────────────────────────────────────────┤
│  Risk          │ Risk Score, Risk Factors, Historical          │
│  Assessment    │ Event Association                              │
└────────────────┴───────────────────────────────────────────────┘
```

### 2.2 Address Type Identification

The most basic profiling dimension is identifying the address type:

```
Address Type Determination Logic
═══════════════════════════════

                    ┌──────────────┐
                    │   Address    │
                    └──────┬───────┘
                           │
                    ┌──────▼───────┐
                    │ get_code()   │
                    └──────┬───────┘
                           │
              ┌────────────┴────────────┐
              │                         │
        code == 0x               code != 0x
              │                         │
              ▼                         ▼
        ┌──────────┐             ┌──────────┐
        │   EOA    │             │ Contract │
        └──────────┘             └────┬─────┘
                                      │
                          ┌───────────┼───────────┐
                          │           │           │
                          ▼           ▼           ▼
                    ┌─────────┐ ┌─────────┐ ┌─────────┐
                    │ Token   │ │  DEX    │ │ Other   │
                    │ERC20/721│ │ Router  │ │ DeFi    │
                    └─────────┘ └─────────┘ └─────────┘
```

**Further contract classification** relies on:

1. **Interface Detection**: Calling standard methods (e.g., ERC-20's `totalSupply()`)
2. **Code Features**: Analyzing function selectors in the bytecode
3. **Known Address Databases**: Comparison with sources like Etherscan, Dune
4. **Interaction Patterns**: Analyzing call patterns in historical transactions

### 2.3 Designing a Tag System

A mature tagging system needs to be multi-dimensional and hierarchical:

```
Tag Classification System
════════════════════════

┌─ Entity Tags
│   ├─ CEX (Centralized Exchange)
│   │   ├─ CEX_HOT_WALLET
│   │   └─ CEX_COLD_WALLET
│   ├─ DEX (Decentralized Exchange)
│   ├─ MINING_POOL
│   ├─ WHALE
│   └─ VC_FUND
│
├─ Behavior Tags
│   ├─ HIGH_FREQUENCY_TRADER
│   ├─ ARBITRAGEUR
│   ├─ MEV_BOT
│   ├─ SANDWICH_ATTACKER
│   ├─ AIRDROP_HUNTER
│   └─ LIQUIDITY_PROVIDER
│
├─ Risk Tags
│   ├─ OFAC_SANCTIONED [CRITICAL]
│   ├─ MIXER_USER [HIGH]
│   ├─ PHISHING [CRITICAL]
│   ├─ SCAM [CRITICAL]
│   ├─ HACK_RELATED [CRITICAL]
│   └─ MONEY_LAUNDERING [HIGH]
│
└─ Contract Tags
    ├─ ERC20_TOKEN
    ├─ ERC721_NFT
    ├─ UNISWAP_PAIR
    ├─ LENDING_PROTOCOL
    ├─ BRIDGE
    └─ MULTISIG_WALLET
```

**Sources of Tags**:
- **Static Sources**: Known address databases (e.g., OFAC sanctions list)
- **Dynamic Inference**: Automatic tagging based on on-chain behavior
- **Community Contributions**: e.g., Etherscan's tagging system
- **Commercial Data**: Databases from companies like Chainalysis, Elliptic

---

## III. Behavioral Feature Analysis: From Transactions to Patterns

### 3.1 Core Dimensions of Behavior Analysis

Transaction data contains rich behavioral information. Through statistical analysis, the following features can be extracted:

```
Behavioral Feature Extraction Framework
═══════════════════════════════════════

┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  Time Dimension                  Amount Dimension               │
│  ───────────────                  ───────────────               │
│  • Transaction Frequency         • Average Transaction Amount   │
│  • Active Hour Distribution       • Amount Distribution         │
│  • Intra-day vs Weekly Patterns    (Micro/Small/Large)          │
│  • Account Age                    • Max Single Amount           │
│                                  • Total Volume                 │
│                                                                 │
│  Operation Dimension              Relationship Dimension        │
│  ──────────────────               ──────────────────────        │
│  • Frequent Function Calls        • Unique Interacting Addresses│
│  • Contract Interaction Ratio      • Top Counterparties         │
│  • DeFi Operation %                • Exchange Interaction Freq. │
│  • NFT Activity                    • Association with Risk      │
│                                     Addresses                    │
└─────────────────────────────────────────────────────────────────┘
```

### 3.2 Temporal Pattern Recognition

The temporal distribution of transactions can reveal the "actor" behind an address:

```
Temporal Pattern Classification
═══════════════════════════════

BUSINESS_HOURS
    │
    │  ████████████████
    │  █              █
    │  █              █
    └──┴──────────────┴──
       9:00          18:00

    → Could be: Institutions, professional traders

BOT_LIKE
    │
    │  ████████████████████████
    │  ████████████████████████
    │  ████████████████████████
    └──────────────────────────
       0:00                24:00

    → Could be: Arbitrage bots, MEV bots

CONCENTRATED
    │
    │              ████
    │              ████
    │              ████
    └──────────────┴────
                   Specific period

    → Could be: Scheduled tasks, responses to specific events

OFF_HOURS
    │
    │  ████              ████████
    │  ████              ████████
    │  ████              ████████
    └──┴────────────────┴────────
       0:00   9:00  18:00   24:00

    → Could be: Users in different time zones, amateur traders
```

### 3.3 Suspicious Behavior Pattern Detection

Based on behavioral features, the following suspicious patterns can be identified:

```
Suspicious Behavior Patterns
════════════════════════════

1. New Account Large Volume
   ┌────────────────────────────────────────┐
   │  Account Age < 7 days && Total Volume  │
   │  > 10 ETH                               │
   │  → Risk Signal: Possible money mule    │
   └────────────────────────────────────────┘

2. Probing Pattern (Small then Large)
   ┌────────────────────────────────────────┐
   │  Multiple small txs followed by        │
   │  a sudden large transfer               │
   │  → Risk Signal: Possible test before   │
   │    attack                              │
   └────────────────────────────────────────┘

3. Automated High Frequency
   ┌────────────────────────────────────────┐
   │  Uniform time distribution &&           │
   │  Avg daily txs > 50                     │
   │  → Risk Signal: Bot behavior, needs    │
   │    further analysis                     │
   └────────────────────────────────────────┘

4. Rapid Fund Movement
   ┌────────────────────────────────────────┐
   │  Funds transferred out shortly         │
   │  after receipt (<1 hour)               │
   │  → Risk Signal: Potential fund         │
   │    intermediary                        │
   └────────────────────────────────────────┘

5. Mixer Interaction Chain
   ┌────────────────────────────────────────┐
   │  Direct or indirect interaction with   │
   │  known mixer addresses                 │
   │  → Risk Signal: High risk, requires    │
   │    manual review                       │
   └────────────────────────────────────────┘
```

---

## IV. Relationship Graphs: From Point to Network

### 4.1 Why Graph Analysis is Necessary

Analysis of individual addresses is often insufficient. Real risk often hides within **relationship networks**:

- A "clean" address might have fund flows with multiple risky addresses
- Money launderers use multiple intermediary addresses to obscure the source of funds
- The same entity may control multiple seemingly unrelated addresses

**Orbit Chain Attack Case (January 2024)**:

The attacker stole approximately $81 million and subsequently moved funds through the following path:

```
Attack Address
    │
    ├──→ Intermediate 1 ──→ Tornado Cash
    │
    ├──→ Intermediate 2 ──→ Intermediate 3 ──→ Exchange
    │
    └──→ Intermediate 4 ──→ Bridge ──→ Other Chain
```

Graph analysis clearly visualizes these fund flows, even through multiple layers.

### 4.2 Core Capabilities of Graph Analysis

```
Graph Analysis Capability Matrix
═════════════════════════════════

┌─────────────────┬────────────────────────────────────────┐
│    Capability   │  Application Scenario                  │
├─────────────────┼────────────────────────────────────────┤
│  Neighbor       │ Identify directly interacting addresses│
│  Discovery      │ Most basic relationship analysis       │
│  (1-hop)        │                                        │
├─────────────────┼────────────────────────────────────────┤
│  Multi-hop      │ Discover indirectly related addresses  │
│  Expansion      │ Trace fund flows                       │
│  (N-hop)        │                                        │
├─────────────────┼────────────────────────────────────────┤
│  Path Finding   │ Find fund path between two addresses   │
│                 │ Source tracing analysis                │
├─────────────────┼────────────────────────────────────────┤
│  Centrality     │ Identify key nodes (e.g., fund hubs)   │
│  Analysis       │ Discover hidden important addresses    │
├─────────────────┼────────────────────────────────────────┤
│  Community      │ Cluster addresses possibly belonging    │
│  Detection      │ to the same entity                      │
│                 │ Entity attribution                      │
└─────────────────┴────────────────────────────────────────┘
```

### 4.3 Risk Propagation Model

Risk is not isolated; it propagates through the network:

```
Risk Propagation Illustration
═════════════════════════════

        [High-Risk Address]
        Risk Score: 100
              │
              │ Direct Interaction
              ▼
        [Address A]
        Risk Score: 100 × 0.8 = 80
              │
              │ Indirect Interaction (2 hops)
              ▼
        [Address B]
        Risk Score: 80 × 0.5 = 40
              │
              │ Further Indirect (3+ hops)
              ▼
        [Address C]
        Risk Score: 40 × 0.3 = 12

Risk Propagation Rules:
- Direct interaction with high-risk address → inherits 80% risk
- Indirect interaction (2 hops) → inherits 50% risk
- Further indirect (3+ hops) → inherits 30% risk
- Transaction amount and frequency affect propagation weight
```

---

## V. Industry Practices and Cutting-Edge Developments

### 5.1 Comparison of Mainstream On-Chain Analysis Tools

```
On-Chain Analysis Tool Comparison (2024)
═════════════════════════════════════════

┌────────────────┬────────────┬────────────┬────────────┬──────────┐
│    Dimension   │ Chainalysis│  Elliptic  │  TRM Labs  │  Nansen  │
├────────────────┼────────────┼────────────┼────────────┼──────────┤
│  Chains        │    30+     │    30+     │    25+     │   15+    │
│  Covered       │            │            │            │          │
├────────────────┼────────────┼────────────┼────────────┼──────────┤
│  Address Tags  │   1B+      │    500M+   │    500M+   │   300M+  │
├────────────────┼────────────┼────────────┼────────────┼──────────┤
│  Real-time     │     ✓      │     ✓      │     ✓      │    ✓     │
│  Monitoring    │            │            │            │          │
├────────────────┼────────────┼────────────┼────────────┼──────────┤
│  Smart         │     ✓      │     ✓      │     ✓      │    ✓     │
│  Contract      │            │            │            │          │
│  Analysis      │            │            │            │          │
├────────────────┼────────────┼────────────┼────────────┼──────────┤
│  API Pricing   │ Enterprise │ Enterprise │ Enterprise │  Tiered  │
├────────────────┼────────────┼────────────┼────────────┼──────────┤
│  Main Clients  │  LE/Exchanges│ Banks/Exchanges│ Exchanges │ Research │
└────────────────┴────────────┴────────────┴────────────┴──────────┘
```

### 5.2 Industry Trends (2024-2025)

**Regulatory Trends**

1.  **MiCA Implementation (Dec 2024)**: The EU's Markets in Crypto-Assets Regulation came into effect, imposing stricter KYT (Know Your Transaction) requirements on VASPs. Exchanges must be able to trace fund sources and identify suspicious transactions.

2.  **FATF Travel Rule Expansion**: More jurisdictions are implementing the Travel Rule, requiring VASPs to transmit sender/receiver information during transfers. This drives the adoption of on-chain analysis tools.

3.  **US SEC Enforcement**: In 2024, the SEC initiated enforcement actions against several DeFi projects, making on-chain analysis a crucial forensic tool.

**Technological Trends**

1.  **Rise of Privacy Computing**: The maturation of ZK technology brings new privacy solutions (e.g., privacy features on Aztec, zkSync), challenging traditional on-chain analysis.

2.  **Demand for Cross-Chain Analysis**: With the development of cross-chain bridges and multi-chain ecosystems, tracking funds across chains becomes essential. Fund flows through protocols like Wormhole and Stargate require specialized analytical capabilities.

3.  **AI/ML Applications**: Machine learning is increasingly used for anomaly detection, entity clustering, and risk prediction. However, "explainability" remains a key requirement for regulatory compliance.

### 5.3 In-Depth Case Study

**Case: The 2024 Munchables Attack and Recovery**

In March 2024, the Blast L2-based NFT game Munchables suffered an insider attack, losing approximately $62.5 million. Noteworthy points:

1.  **On-Chain Tracing**: Immediately after the attack, on-chain analysis quickly identified the attacker's address.
2.  **Community Pressure**: By analyzing the attacker's other on-chain activities, the community inferred a possible identity.
3.  **Funds Recovery**: Under community pressure, the attacker (identified as a former developer) returned all funds.

This case demonstrates:
- The deterrent power of transparent on-chain data
- The strength of community-driven on-chain analysis
- The fragility of pseudonymity—once a clue exists, the entire history becomes traceable.

---

## VI. Limitations and Future of Address Profiling

### 6.1 Current Technological Limitations

```
Boundaries of Address Profiling Capability
═══════════════════════════════════════════

Able to Do ✓                     Difficult to Do ✗
────────────────────────────────────────────────────
✓ Identify address types          ✗ Determine real-world identity
✓ Detect known risky addresses    ✗ Identify novel attack patterns
✓ Analyze transaction patterns    ✗ Predict future behavior
✓ Build relationship graphs       ✗ Penetrate privacy protocol protection
✓ Trace main chain fund flows     ✗ Trace complex cross-chain paths
✓ Identify bots/high-freq behavior ✗ Distinguish legitimate arbitrage from malicious attacks
```

### 6.2 The Challenge of Privacy Technologies

Emerging privacy technologies pose significant challenges to profiling analysis:

```
Privacy Tech vs. Profiling Analysis
═══════════════════════════════════

┌───────────────┬──────────────────────┬────────────────────┐
│   Technology  │  Principle           │  Impact on         │
│               │                      │  Profiling         │
├───────────────┼──────────────────────┼────────────────────┤
│ Tornado Cash  │  Mixing pool breaks   │  Fund source       │
│               │  links                │  hard to trace     │
├───────────────┼──────────────────────┼────────────────────┤
│ zkSync        │  ZK proofs hide       │  Tx details        │
│ Privacy       │  transaction details  │  invisible         │
├───────────────┼──────────────────────┼────────────────────┤
│ Aztec         │  Full-chain privacy   │  Complete          │
│               │                       │  anonymization     │
├───────────────┼──────────────────────┼────────────────────┤
│ Privacy       │  Ring signatures +    │  Almost            │
│ Coins (XMR)   │  stealth addresses    │  untraceable       │
├───────────────┼──────────────────────┼────────────────────┤
│ Cross-Chain   │  Funds "jump chains"  │  Increases         │
│ Bridges       │                       │  tracing complexity│
└───────────────┴──────────────────────┴────────────────────┘
```

But privacy tech is not without solutions:
- **On/Off Ramp Analysis**: Focus on points where funds enter or exit privacy protocols.
- **Amount Correlation**: Specific amounts can form fingerprints.
- **Temporal Correlation**: Analyzing the correlation between deposit and withdrawal times.
- **Behavioral Patterns**: Even with privacy, behavioral habits may expose identity.

### 6.3 Future Development Directions

**Technical Level**:
1.  **AI-Enhanced Analysis**: Using Graph Neural Networks (GNNs) for more accurate entity clustering.
2.  **Real-Time Risk Scoring**: Millisecond-level transaction risk assessment.
3.  **Unified Cross-Chain View**: Building address profiles that span multiple chains.

**Product Level**:
1.  **Decentralized Credit Systems**: An "on-chain credit score" built from behavioral data.
2.  **Verifiable Credentials**: Proving "I am not a risky address" while preserving privacy.
3.  **Risk-Control-as-a-Service**: Providing embedded risk control capabilities for DeFi protocols.

---

## VII. Conclusion: Finding Balance Between Transparency and Privacy

Address profiling is not just a technical problem; it's a value trade-off between **transparency and privacy**, **security and freedom**.

As risk control practitioners, we need to recognize:
- The tools we build can be used both to protect users and to surveil them.
- Technology is not neutral; every design decision carries a value judgment.
- The best risk control lets good actors operate freely while making it impossible for bad actors to hide.

From address to identity, from transaction to behavior, from point to network—this is a process of continuous deepening. But no matter how technology evolves, **understanding the nature of data, respecting user privacy, and adhering to compliance boundaries** remain the fundamental qualities of a Web3 risk control professional.

In the next article, we will explore how to design a **risk rule engine**—transforming these profiling capabilities into executable risk control strategies.

---

*This article is the second in the "Web3 Risk Control System Practice" series. The author is building a complete on-chain risk monitoring system and welcomes discussion.*

---

## Appendix: Key Concepts Quick Reference

| Concept | Explanation |
|---------|-------------|
| EOA | Externally Owned Account, controlled by a private key |
| Pseudonymity | Trading under a pseudonym (address), actions public but identity hidden |
| KYT | Know Your Transaction |
| Address Clustering | Grouping multiple addresses likely belonging to the same entity |
| Centrality | Graph theory concept measuring a node's importance in a network |
| Risk Propagation | Risk transferring through transaction relationships in an address network |
| Mixer | Protocol mixing multiple users' funds to break transaction links |
| Travel Rule | FATF rule requiring VASPs to transmit originator/beneficiary information |

---

## References

- Chainalysis 2024 Crypto Crime Report
- Elliptic 2024 State of Cross-chain Crime
- FATF Updated Guidance for Virtual Assets (2023)
- MiCA Regulation Full Text (EU 2023/1114)
- Tornado Cash Protocol Analysis - Flashbots Research
- Lazarus Group On-chain Footprint - TRM Labs Report


