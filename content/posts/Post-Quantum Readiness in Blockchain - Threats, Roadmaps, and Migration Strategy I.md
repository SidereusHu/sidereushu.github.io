+++
title = "Post-Quantum Readiness in Blockchain: Threats, Roadmaps, and Migration Strategy I"
date = 2025-07-10T16:57:25+08:00
draft = false
math = true
+++

<br>

### Bitcoin's Security and the Threat from Quantum Computing

Bitcoin's security relies on a **cryptographic assumption that has long been considered unbreakable under current technological conditions**. However, the emergence of quantum computers **could undermine this assumption within the next decade**.

At the core of Bitcoin’s cryptographic foundation are:

- the **Elliptic Curve Digital Signature Algorithm (ECDSA)**;
    
- and, since 2021, the introduction of **Schnorr signatures**.
    

Both schemes are based on the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**, which is **asymmetric** in nature: deriving the public key from a private key is easy, but reversing the process is believed to require trillions of years even on today’s most powerful supercomputers.  
However, in the face of **cryptographically relevant quantum computers (CRQCs)**, this asymmetry may collapse — **deriving the private key from the public key could take only hours or days**.

Institutions such as the **U.S. National Institute of Standards and Technology (NIST)**, the **U.S. government**, and other international agencies have recommended **phasing out elliptic curve cryptography by 2030** and **fully deprecating it by 2035**.

<br>

### Potential Impact of Quantum Computers on Bitcoin

Once practical quantum computers become available, their impact on Bitcoin could be profound. According to an analysis by [^ChainCodeReport]:

- Approximately **20% to 50% of all circulating Bitcoin (4 to 10 million BTC)** could be at risk of theft;
    
- This risk primarily stems from the ability to **derive private keys from exposed public keys**.
    

The most vulnerable assets include:

- UTXOs that use **insecure script types**;
    
- Funds associated with **“address reuse”**, where the public key has already been revealed;
    
- **Exchange and institutional addresses** that frequently reuse addresses;
    
- **Early-era addresses from the Satoshi period**, such as those using raw public key scripts;
    
- **“Lost coins”**, which remain unmoved and are locked under outdated or unsafe scripts.
    

Among these, **high-value exchange and institutional accounts** are expected to be **top targets for quantum-enabled attackers**.


<br>

### Quantum Threats to Bitcoin Mining and Ecosystem Security

On the other hand, while quantum computing may also impact **Bitcoin mining**, its threat is **considered limited in the foreseeable future**:

- Current mining performance is primarily driven by **clock frequency**;
    
- Quantum miners would have to **compete with highly optimized ASIC machines**;
    
- Even with a **quadratic speedup** from algorithms like **Grover’s**, it would still be difficult to outperform classical miners;
    
- The **hardware performance gap remains significant**.
    

If quantum miners were to dominate in the future, potential risks could include:

- **Double-spending attacks** by small or solo miners;
    
- **Increased centralization** due to disproportionate mining power.
    

Although these scenarios remain distant, exploring **post-quantum mining models** still holds theoretical value and can help prepare for long-term contingencies.

---

### Beyond Signatures and Mining: Ecosystem-Level Risks

In addition to the direct threats to **digital signatures** and **mining**, a broader set of **ecosystem-level vulnerabilities** must also be considered in the post-quantum era:

- For example: **SSL/TLS communication protocols**, **hardware wallets**, and **firmware update mechanisms** may all be compromised by quantum-capable adversaries.
<br>

### Two Types of Quantum Attack Vectors

(**Long-Range Attack vs. Short-Range Attack**)

- **Long-Range Attack**: Targets addresses whose **public keys are already exposed on-chain**, such as P2PK, P2MS, and P2TR. These addresses are **permanently vulnerable**, as their public keys are visible at all times.
    
- **Short-Range Attack**: Targets addresses that **only reveal their public keys when spending**, such as P2PKH. In this case, the attacker must act within a **very short window** — between the time a transaction is broadcast (and the public key is exposed) and when it gets confirmed on the blockchain.
    

> In short, **if the public key of a UTXO is known**, quantum adversaries may derive the private key and steal the funds. The difference lies in **how and when the public key is exposed**:

- Some addresses are **“natively exposed”**, meaning their public keys are visible from the beginning, and thus remain under **long-term attack risk**.
    
- Others (e.g., P2PKH) **only reveal the public key when the coin is spent**, offering a **very narrow attack window**, also known as a **"preemptive" or "racing" attack**.


<br>

### Quantum Vulnerabilities by Script Type

> The feasibility of quantum attacks depends not only on the existence of CRQCs (Cryptographically Relevant Quantum Computers), but also on whether the corresponding **public key is accessible**. Therefore, different script types present different levels of vulnerability. Below is a classification by script type:

---

### 🔴 Immediate Vulnerability

- **P2PK (Pay to Public Key)**: The earliest script type, which directly exposes the ECDSA public key in the locking script. Though it accounts for only ~0.025% of UTXOs, it secures **~8.68% of all BTC (~1.72 million BTC)** — largely associated with early Satoshi-era wallets.
    
- **P2MS (Pay to MultiSig)**: A script type that exposes multiple public keys. Now rarely used and secures a relatively small amount of BTC.
    
- **P2TR (Pay to Taproot)**: Despite being a modern script, its **key-path spending still reveals a tweaked public key**, making it vulnerable to quantum attacks. P2TR makes up ~32.5% of UTXOs but secures only ~0.74% of BTC.
    

---

### 🟡 On-Spend Vulnerable Script Types

> P2PK, P2MS, and P2TR are classified as **“immediately vulnerable”** because they expose public keys directly in the output script (`scriptPubKey`).  
> However, some script types **do not reveal the public key until the coin is spent**. These are vulnerable only during the short window **between transaction broadcast and confirmation**.

---

### 🟠 Address Reuse Vulnerability

> Script types such as **P2PKH**, **P2SH**, **P2WPKH**, and **P2WSH** only expose public keys **at the time of spending**, meaning the key is normally revealed once.  
> But if the same address (i.e., the same public key) is **reused**, the associated public key remains **permanently exposed** on-chain, turning a **short-range attack** surface into a **long-range attack** vector.

---

### 🟣 Other Avenues for Public Key Exposure

#### 1. Fork Chain Exposure

> If a user spends an unspent UTXO on a **Bitcoin fork chain** (e.g., Bitcoin Cash, Bitcoin Gold), the public key becomes exposed on the fork — even though the UTXO remains unspent on the Bitcoin mainnet.  
> Thus, the corresponding UTXO on the mainnet becomes a **long-term quantum target**.

#### 2. Leaked Extended Public Keys (xpubs)

> `xpubs` are used in **Hierarchical Deterministic (HD) wallets**, enabling address generation without revealing private keys.  
> If an `xpub` is leaked, all **non-hardened child public keys** derived from it can have their **private keys recovered** by quantum computers.

#### 3. Public Key Exposure in Multi-Sig & Lightning Network

> In **multi-signature wallets** and **Lightning Network** channels, participants must share their public keys to construct transactions or payment channels.  
> Although these public keys are not broadly public, they are **shared within limited parties**, and thus may be **exploited by insiders or malicious actors**.



<br>

## Ecosystem Vulnerabilities

Once **elliptic curve cryptography (ECC)** is broken by quantum computers, the impact will go far beyond Bitcoin — potentially compromising the **entire Internet cryptographic infrastructure**.  
Core protocols like **SSL/TLS** could become insecure, leading to **ecosystem-level attacks** such as **man-in-the-middle (MITM) attacks**, where adversaries intercept and redirect user requests:

- **MITM attacks**: By compromising SSL/TLS, attackers can forge connections to exchanges and **hijack funds**.
    
- **Hardware wallet updates**: Firmware updates may be **tampered with**, silently embedding backdoors.
    
- **Mining pool impersonation**: Attackers may **masquerade as miners** to infiltrate mining pools.
    
- **DNS attacks**: Redirecting users to **malicious nodes** through DNS manipulation.
    
- **API attacks**: Tampering with third-party APIs widely used by **wallets and exchanges**.
    

These threats are **stealthier than direct cryptographic breakage**, potentially remaining undetected for extended periods due to their **ecosystem-level obfuscation**.

---

## Bitcoin Hash Functions and Grover’s Algorithm

Bitcoin relies on two hash functions: **SHA-256** and **RIPEMD-160**, used for:

- Mining (double SHA-256 on block headers)
    
- Generating transaction IDs
    
- Signing messages
    
- Deriving addresses from public keys (`Hash160`)
    

### Quantum Resistance of Hash Functions

The fundamental property of hash functions is **irreversibility**.  
Even the world’s most powerful supercomputers (e.g., _El Capitan_, as of Nov 2024) would require far more time to break them than to crack ECC. [^SDS+24]

**Grover’s algorithm** offers a **quadratic speedup** for brute-forcing hash functions:

- Classical attack complexity: O(N)O(N)
    
- Quantum attack complexity: O(N)O(N​)
    

For example, **finding a SHA-256 collision** classically requires 22562256 operations.  
With Grover’s algorithm, this is reduced to 21282128, which is still computationally infeasible without **extremely powerful quantum computer**.



## Impact on Bitcoin Mining

The essence of Bitcoin mining is to **find a nonce value** that makes the block header's hash lower than the network difficulty target.  
This process involves repeated **SHA-256 computations**, currently performed almost exclusively by **ASIC hardware**.

---

### 1. **Limitations of Quantum Mining**

**Grover’s algorithm provides sequential, not parallel, speedup**, which leads to key limitations:

- It **cannot scale in parallel** like classical mining does; adding more quantum hardware does **not linearly increase**hash power.
    
- Competing with ASICs would require **a large number of high-speed quantum machines**, making deployment **extremely costly**.
    

---

### 2. Network Difficulty Adjustment

Bitcoin dynamically adjusts its mining difficulty **every 2016 blocks** to maintain a block interval of ~10 minutes.  
If quantum mining is introduced, the protocol will **increase the difficulty accordingly**, which would further **offset any advantage** from Grover’s algorithm.

---

## Forks and the 51% Attack

### 1. **Quantum-Induced Forking Risk**

Research shows that quantum mining could **increase the frequency of blockchain forks**:

- When one miner finds a valid block, other quantum miners must decide whether to **continue or restart their computation**.
    
- **Simultaneous quantum measurements** by multiple miners increase the chance that **multiple valid blocks are found at the same time**, causing **forks**.
    

---

### 2. **Security Concerns**

In a high-fork environment:

- Honest miners' hash power gets **split across multiple chains**;
    
- Adversaries can **concentrate their resources** on attacking one chain, potentially gaining dominance **without owning 51% of total hash power**.
    

This reduces **finality** and **consensus stability**, and significantly **increases the attack surface**.

---

## Comprehensive Quantum Vulnerability Assessment of Bitcoin

| Component             | Quantum Threat                             | Resistance Level               |
| --------------------- | ------------------------------------------ | ------------------------------ |
| **ECC Signatures**    | Critical risk _(Shor's algorithm)_         | Very Low                       |
| **Hash Functions**    | Moderate risk _(Grover's algorithm)_       | Relatively High                |
| **Mining Mechanism**  | Efficiency impacted but adjustable         | Medium                         |
| **Ecosystem Attacks** | High risk _(SSL, DNS, APIs)_               | Depends on external mitigation |
| **Fork Security**     | Potentially severe _(more frequent forks)_ | Low                            |


<br>

## References

[^SDS+24]: E. Strohmaier, J. Dongarra, H. Simon, H. Meuer. *TOP500 List - November 2024 (64th Edition). TOP500.org.* November 19, 2024. 
[^ChainCodeReport]: Chaincode Labs. *Bitcoin and Quantum Computing: Current Status and Future Directions*. 




