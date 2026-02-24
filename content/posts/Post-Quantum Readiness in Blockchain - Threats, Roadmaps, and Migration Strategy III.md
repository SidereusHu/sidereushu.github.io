+++
title = "Post-Quantum Readiness in Blockchain: Threats, Roadmaps, and Migration Strategy III"
date = 2025-07-13T16:57:25+08:00
draft = false
math = true
+++
 
<br>

## Timeline for Post-Quantum Migration

According to analysis by Chaincode Labs, Bitcoin’s transition to post-quantum cryptography (PQC) can follow two main strategies: a **short-term contingency plan** (cf. Figure 1) and a **long-term comprehensive path** (cf. Figure 2).

The **short-term strategy** focuses on deploying a basic quantum-resistant option within **1 to 2 years**, offering a fallback mechanism in case cryptographically relevant quantum computers (CRQCs) emerge sooner than expected. This involves proposing a minimal PQC signature scheme through a BIP, implementing it in Bitcoin Core, and enabling voluntary migration of vulnerable UTXOs. While not optimized for all use cases, it provides immediate protection for at-risk users and critical institutions. Success depends on close coordination across the technical community and early involvement from major Bitcoin holders.

The **long-term strategy** envisions a broader research and development process to design Bitcoin’s optimal quantum-resistant future. This includes evaluating different PQC algorithms, mitigating their performance trade-offs, and developing robust UTXO migration mechanisms. Drawing from historical upgrades like SegWit and Taproot, which took several years to reach wide adoption, this path could span up to a decade.

Chaincode emphasizes that these two strategies are **not mutually exclusive**. The short-term plan offers immediate protection and preparedness, while the long-term plan focuses on sustainability, optimization, and ecosystem-wide adoption in the face of evolving quantum threats.


![PQC_MigrationInShortTerm](/images/Short-Term-PQC-Migration.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Figure 1.  Timeline of estimate to establish short-term contingency measures.**

<br>

![PQC_MigrationInLongTerm](/images/Long-Term-PQC-Migration.png)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Figure 2.  Timeline for comprehensive quantum resistance path.**


<br>

## Government Post-Quantum Plans and Timelines

Governments worldwide are actively responding to the potential massive disruption posed by quantum computers to existing cryptographic systems by formulating strategies for **migration to post-quantum cryptography (PQC)**.  
As of mid-2025, more than 15 countries and regions have issued official guidance on PQC transition. Most follow the NIST (U.S. National Institute of Standards and Technology) standardization process, while some develop indigenous algorithms independently. The general timeline for transition completion spans **2027 to 2035**, with **2030** considered a critical milestone.

---

### United States

The U.S. has established the most comprehensive plan, including:

- **National Security Memorandum 10 (May 2022)**: Signed by President Biden, titled _“Advancing America’s Leadership in Quantum Computing While Mitigating Risks to Vulnerable Cryptographic Systems”_.  
    The goal is to **mitigate quantum risks as much as possible by 2035**.
    
- **NIST Transition Deadlines**:  
    Phasing out **RSA-2048 and ECC-256 by 2030**, with **complete prohibition by 2035**, while allowing hybrid classical-PQC schemes during the transition.
    
- **NSA’s CNSA 2.0 Suite**:  
    Requires software and network equipment upgrades by **2030**, with browsers and operating systems completing upgrades by **2033**.
    
- **Presidential Executive Order 14144 (January 2025)**:  
    _“Strengthening National Cybersecurity Innovation”_ emphasizes collaboration with other nations and industry to encourage adoption of NIST’s PQC standards.
    

---

### United Kingdom

The UK’s National Cyber Security Centre (NCSC), part of GCHQ intelligence agency, published guidance in **March 2025** outlining a three-phase transition plan:

1. **By 2028**: Identify cryptographic services needing upgrades and develop migration plans.
    
2. **2028–2031**: Prioritize upgrading critical systems and adapt according to PQC progress.
    
3. **2031–2035**: Complete full migration of systems and products to PQC.
    

The UK plan explicitly bases on **NIST standards** and aligns with **IETF protocol standardization efforts**.

---

### European Union

The EU drives its PQC roadmap through the European Telecommunications Standards Institute (ETSI) Quantum-Safe Cryptography (QSC) working group, established in 2015:

- Published quantum-safe migration guidelines [ETS20] and key establishment standards [ETS25];
    
- Provides technical reports endorsing **NIST standards** as well.
    

---

### China

China pursues a more **independent and autonomous PQC development path**.  
In February 2025, the **Commercial Cryptography Institute** launched the “Next Generation Commercial Cryptography Algorithms Program” (NGCC) [^ICC25], developing domestic PQC algorithms aligned with **national security requirements**.  
China emphasizes **technological self-reliance** and **has not publicly released a detailed transition timeline**.


<br>

## Industry Progress in Post-Quantum Cryptography (PQC)

Several **well-known companies relying on cryptography in their products or services** have already started migrating to post-quantum cryptography (PQC) solutions.

---

### Cloudflare

- **Date: October 2022**  
    Cloudflare announced that **all websites and APIs served through Cloudflare support a post-quantum hybrid key exchange mechanism**.
    
- **Technical Details**: This mechanism combines classical elliptic curve cryptography (ECC) with the **post-quantum Kyber algorithm** to form a **hybrid scheme**.
    
- **Security Advantage**: Attackers must break both ECC and Kyber algorithms simultaneously to compromise keys, significantly enhancing security.
    

---

### Google

- **Date: August 2023**  
    Google announced that its Chrome browser started supporting a **post-quantum hybrid key generation mechanism** similar to Cloudflare’s.
    
- **Motivation and Background**: Google emphasized the urgency of updating TLS (Transport Layer Security) to use quantum-resistant session keys to prevent **“Harvest Now, Decrypt Later”** attacks, i.e.,
    

> Attackers collect encrypted data now and decrypt it later once universal quantum computers become available.

---

### Signal

- **Date: September 2023**  
    Signal, a leading privacy-focused encrypted messaging app, announced adding **post-quantum security layers** to its communication encryption.
    
- **Implementation**: Introduced **CRYSTALS-Kyber post-quantum key encapsulation mechanism (KEM)** atop its original ECC-based encryption to enhance security.
    

---

### Apple

- **Date: February 2024**  
    Apple announced a new encryption protocol **PQ3** designed for iMessage.
    
- **PQ3 Features**:
    
    - Uses PQC for **initial key agreement** and **ongoing message exchange**;
        
    - Supports automatic recovery mechanisms, enabling rapid restoration of security even if a key is compromised, thereby enhancing overall resilience.
        

---

### Industry Trend Summary

With PQC algorithms continuously standardized and maturing, **an increasing number of companies will deploy PQC mechanisms**, marking a shift from “experimental defense” to “mainstream security requirement.”

---

### Why Choose a “Hybrid Encryption” Strategy?

- No single PQC algorithm is yet proven 100% secure;
    
- Hybrid schemes allow a **gradual transition without interrupting existing security guarantees**;
    
- They prevent a “single point of failure” — even if one algorithm is broken in the future, the other may still provide protection.
    

---

### Why Kyber?

- Kyber is the **first post-quantum key encapsulation algorithm (KEM) standardized by NIST**;
    
- Based on **lattice cryptography**, currently regarded as highly resilient to quantum attacks;
    
- Has relatively small key and ciphertext sizes, making it suitable for performance-sensitive applications such as TLS and mobile communications.
    

---

### Drivers for Industry Action

- Clear government timelines (e.g., NIST’s 2030/2035 plans) create regulatory pressure;
    
- Quantum threats may not be immediate, but **data harvesting attacks are already ongoing**, requiring early preparation to protect future privacy;
    
- Companies gain a first-mover advantage in security and can strengthen market trust.



<br>

## Philosophical Debate

If a **universal, breakable quantum computer** eventually becomes reality, the Bitcoin community will face a difficult decision:  
**Should it take action to handle currently exposed public-key UTXOs that are vulnerable to quantum attacks?**

This question fundamentally touches Bitcoin’s core values, such as **property rights, censorship resistance, forward compatibility,** and **conservatism**.

---

## The “Burn” Position

### Core Arguments

- By “burning” quantum-vulnerable UTXOs, these coins become unusable, thereby **protecting property rights and network integrity**.
    
- Bitcoin developer Jameson Lopp[^Lop25a] , in his article [“Against Allowing Quantum Recovery of Bitcoin” (Lop25a)] and BDML talks, argues:
    
    - Allowing quantum computers to seize these bitcoins results in a wealth transfer—from **those who lost keys**to **winners of the quantum arms race**.
        
    - The “burn” strategy treats quantum vulnerability as a **protocol-level bug** that should be conservatively fixed, like previous vulnerabilities.
        

### Economic Effects

- “Burning” reduces total Bitcoin supply, thereby **increasing the value of remaining coins**.
    
- Coordinated “burn” events can **reduce market uncertainty and volatility**.
    

---

## The “Steal” Position

### Core Arguments

- Opponents of “burning” argue:
    
    - It **violates user property rights** and amounts to confiscation.
        
    - Bitcoin’s design intent is **complete user sovereignty over their assets**, allowing spending at any time.
        
    - Some users may be unaware of quantum risks or unable to migrate assets promptly.
        

### Moral and Technical Dilemmas

- Protocol changes that render some UTXOs permanently unspendable introduce **third-party control**, violating Bitcoin’s decentralization principles.
    
- Without intervention, the “steal” path becomes the **default option**—once quantum computers mature, early attackers could seize all vulnerable assets, causing serious **wealth redistribution**.



<br>

### Migration Pathways Overview

Migrating Bitcoin to quantum-resistant signatures is one of the most historically significant undertakings in the Bitcoin ecosystem. Even if a quantum-safe signature scheme gains technical consensus and integrates into Bitcoin Core, migrating millions of UTXOs to quantum-safe scripts requires unprecedented network coordination. This section discusses practical migration pathways, on-chain resource requirements, activation methods, user education, and coordination mechanisms[^chaincodereport].

- Quantum-resistant migration is not only a technical challenge but also a **network-wide collaboration and governance issue**.
    
- It involves **block space, soft forks, user key management, activation mechanisms**, and more.
    
- All Bitcoin holders need basic awareness of migration pathways to develop effective risk mitigation strategies against quantum threats.
    

---

### UTXO Migration

As of May 2025, Bitcoin’s UTXO set contains approximately 190 million UTXOs. Only a portion exposes public keys and is thus vulnerable to **long-range attacks**. However, all UTXOs are potentially susceptible to **short-range attacks**during spending and transaction confirmation windows, as described earlier in “On-Spend Vulnerable Script Types.”

Ideally, **all UTXOs** should migrate to quantum-resistant scripts by spending old UTXOs in a transaction and creating new UTXOs secured by post-quantum signatures. In practice, some UTXOs are inaccessible (e.g., lost keys), creating the “**philosophical dilemma: burn or steal**.”

2024 research estimates:

- **Theoretical fastest migration time**: Using 100% block space for migration transactions, migrating all UTXOs would take approximately 76–142 days (model-dependent).
    
- **Realistic scenario**: Using only 25% block space, it would take 305–568 days.
    
- In reality, only an urgent migration of **exposed public-key UTXOs** is necessary.
    

**Key points**:

- UTXO migration is resource-intensive and costly.
    
- Without on-chain scaling, existing **block size and block interval** severely limit migration throughput.
    
- **Signature aggregation** and **batching transactions** are potential optimizations.
    

---

### Migration Mechanisms

Several proposed mechanisms balance **user participation, consensus rule changes, and technical complexity**.

---

#### Commit-Delay-Reveal Protocol (CDR) and Variants

**Background**:

- Initially proposed in 2018, with variants by Adam Back, Tim Ruffing, and others.
    
- Designed to securely migrate old UTXOs to post-quantum scripts even after ECC compromise.
    

**Three-phase process**:

1. **Commit phase**:
    
    - User creates a commitment transaction hashing the old ECC and new post-quantum public keys.
        
    - The hash is committed on-chain via `OP_RETURN`.
        
    - Transaction valid under current consensus rules.
        
    - Soft fork needed to enforce that the UTXO can only be spent by knowledge of both private keys.
        
2. **Delay phase**:
    
    - Introduces a mandatory delay period (e.g., 6 months) to prevent reorg attacks by ensuring chain finality.
        
    - Funds are locked and unusable during this period.
        
3. **Reveal phase**:
    
    - User submits a reveal transaction disclosing the original ECC and new post-quantum keys.
        
    - The transaction is signed with the post-quantum signature.
        
    - Verifies that the reveal matches the commit hash.
        

**Advantages**:

- High security under CRQC presence; allows safe migration of unexposed UTXOs post-ECC compromise.
    
- Supports migration after quantum attacks occur.
    

**Disadvantages**:

- Cannot migrate UTXOs with already exposed public keys (e.g., P2PK).
    
- Requires user participation in two transactions.
    
- Requires users to hold post-quantum coins for fees, creating a **bootstrapping problem**.
    
- At least one, more likely two soft forks needed (one for PQ signatures, one for CDR semantics).
    
- Block space constraints remain.
    

---

#### Improved Scheme: Lifted FawkesCoin Protocol

**Improvements**:

- Resolves the “user must already hold PQ coins” problem in original CDR.
    
- Employs **zero-knowledge proofs (ZKPs)** using the `PICNIC` PQ signature to prove knowledge of a private key without revealing the public key.
    
- Circumvents the paradox of needing to reveal a public key to commit.
    

**Additional capabilities**:

- HD wallet users can prove derivation relationships using seeds.
    
- Partial private key loss is recoverable via master seed.
    
- Can protect the majority of Bitcoin accumulated since HD wallets became standard.
    

**Costs and challenges**:

- Requires integrating ZKPs into Bitcoin protocol, a more disruptive change than single PQ signature adoption.
    
- PICNIC and related ZK systems are complex and not yet widely deployed.
    

---

### Commentary & Opinion

Bitcoin’s quantum-resistant migration is a **gradual but urgent** process:

1. **Comprehensiveness vs Reality**:
    
    - Ideally migrate all UTXOs.
        
    - Practically prioritize active, spendable, high-risk UTXOs.
        
    - Inaccessible funds (lost keys) remain a “philosophical problem.”
        
2. **Technical vs Social Coordination**:
    
    - Technical solutions (CDR, ZKP lifting) are early-stage and require extensive validation.
        
    - Real challenges lie in **activation, education, consensus coordination, and incentive design**.
        
3. **Future research directions**:
    
    - Design **quantum-resistant coin creation mechanisms** to solve bootstrapping.
        
    - Simplify CDR workflows (e.g., automated wallet support).
        
    - Explore **soft-fork-free or incremental transition mechanisms**.
        
    - Analyze Grover’s algorithm effectiveness on various Bitcoin script structures to inform prioritization.



<br>

## References

[^Lop25a]: J. Lopp. *Against Allowing Quantum Recovery of Bitcoin. Cypherpunk Cogitations.* March 16, 2025.
[^chaincodereport]: Chaincode Labs. *Bitcoin and Quantum Computing: Current Status and Future Directions*. 
[^ICC25]: Institute of Commercial Cryptography Standards, China. *Announcement on Launching the Next-generation Commercial Cryptographic Algorithms Program (NGCC).* February 5, 2025. 

