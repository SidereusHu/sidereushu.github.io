+++
title = "Cryptographic Sponge Functions: The Foundation of ZK-Friendly Hash Constructions"
date = 2025-07-26T16:57:25+08:00
draft = false
math = true
+++


<br>

In modern cryptography, particularly in zero-knowledge proofs and high-performance hash designs (such as Poseidon, Rescue, and Griffin), one fundamental building block appears repeatedly: the **cryptographic sponge function**.  
It not only offers an elegant absorb-and-squeeze paradigm but also strikes a principled balance between **security and efficiency**.

<br>

![SpongeFunc](/images/SpongeFunc.png)

<br>

---

### I. What is a Sponge Function?

A **sponge** is a flexible cryptographic structure used to absorb an input of arbitrary length and squeeze out a fixed or extensible output. Its power lies in its simplicity, relying only on a single state and a permutation function.

- The internal state size is $b = r + c$, where:
    
    - $r$: the **rate**, the portion of the state used for input/output;
        
    - $c$: the **capacity**, the hidden part that ensures security.
        

The execution process consists of two phases:

1. **Absorbing Phase**  
    The input message is split into $r$-bit blocks. Each block is XORed into the first $r$ bits of the state, followed by applying a permutation $f$.
    
2. **Squeezing Phase**  
    Output is read from the first $r$ bits. After each extraction, $f$ is applied again until the desired output length is achieved.
    

This process mimics the idea of "soaking a sponge with water and squeezing it out" — the internal state acts as a container that processes and diffuses the message before producing the final output.

<br>

---

### II. Generic Attacks and Indifferentiability

In cryptographic analysis, we often distinguish between:

- **Generic attacks**: leverage only the sponge framework itself, not the structure of the permutation.
    
- **Structural attacks**: exploit specific algebraic properties or implementation details of the permutation $f$.
    

Sponge constructions are notable for offering formal guarantees against generic attacks. To argue this formally, we use:

### **The Indifferentiability Framework**

This is a simulation-based model aiming to prove:

> “If an attacker can distinguish the sponge from a random oracle, then they must also be able to distinguish the internal permutation $f$ from a truly random one.”

Thus, if the permutation $f$ behaves like a random permutation, the entire sponge behaves like a random oracle.

<br>

---

### III. What is an Externally Visible Weakness?

In the indifferentiability model, only **input-output behavior** is visible to an attacker. Any internal details — including the state — are hidden. Therefore, only the following constitute valid attacks:

- **Output collisions**: distinct inputs lead to the same output.
    
- **Second preimage attacks**: given a hash value, find another message mapping to it.
    
- **Length extension attacks**: predict $H(M | X)$ based on $H(M)$.
    

✅ **Internal collisions** or state coincidences are not considered attacks — unless they become externally observable.

This principle significantly simplifies security analysis: we only need to reason about observable behavior.

<br>

---

### IV. How to Claim Security in the Random Sponge Model?

Just as we use idealized models like **random oracles** or **ideal ciphers** for conventional primitives, we can model sponges via:

> **The Random Sponge** — a theoretical sponge behaving like a random function.

Thus, a sponge construction is secure if it is **indifferentiable** from a random sponge — i.e., if no adversary can distinguish it through queries alone.

### Example of a Security Claim:

> “We assert that our sponge-based design is secure if it does not exhibit any externally visible weaknesses that would not appear in a random sponge.”

In other words:

- If an attacker can distinguish the implementation from an ideal sponge via I/O interactions alone, the construction is insecure.
    

<br>

---

### V. Hermetic Sponge Strategy: Black-Box Security

The original Keccak paper described a sponge design philosophy that underpins its provable security:

> _“Build a sponge on top of a permutation $f$ which itself is cryptographically strong, and claim that any attack better than $2^{c/2}$ implies a structural weakness in $f$.”_

This is called the:

### **Hermetic Sponge Strategy**

The core idea is to **seal the permutation inside a black box**, and reduce the sponge’s security to whether the box behaves like a random oracle. Specifically:

- Only the rate part ($r$ bits) is ever exposed;
    
- The capacity ($c$ bits) remains fully hidden;
    
- If an attack works better than $2^{c/2}$, it must exploit the permutation's structure.
    

This is in contrast to classic constructions like Merkle–Damgård, which build security through incremental compression. The sponge instead assumes **non-observability and indistinguishability** as its defense mechanism.

> **Note:** Hermetic Sponge assumes an ideal world with no side-channel visibility. It doesn't cover power, timing, or cache attacks.

<br>

---

### VI. Collisions and Second Preimage Attacks

Sponge functions offer different security bounds depending on how they're parameterized:

#### 1. **Output Collisions**

- The goal is to find $M_1 \ne M_2$ such that:
    
    $H(M_1)=H(M_2)$
    
- If the output length is $n < c$, the attack complexity is approximately $2^{n/2}$.
    

#### 2. **Internal Collisions**

- The attacker aims to create two different messages that end in the same internal state after absorption;
    
- This leads to the same output when squeezed;
    
- Complexity is roughly $2^{c/2}$.
    

#### Comparison:

- If $n < c$, output collisions are easier;
    
- If $n \geq c$, it’s better to aim for inner collisions.
    

This is why in practice, designers often ensure:

> **Capacity $c ≥ 2$ × output length $n$**

<br>

---

### VII. Throughput vs. Security: Balancing r and c

The sponge’s performance and security depend on how we divide the state:

- $b = r + c$: total internal state is fixed;
    
- Increasing $r$: improves throughput;
    
- Increasing $c$: improves security (resistance to generic attacks).
    

Typical settings:

|Goal|Recommended Setting|Explanation|
|---|---|---|
|128-bit security|$c = 256$, $r = b - 256$|Collision resistance ≈ $2^{128}$|
|High throughput|Increase $r$, reduce $c$|Favor speed over generic security|
|High security|Use $c \gg n$|Avoid inner collisions and extension|

This trade-off is critical in the parameterization and round-count design of sponge-based hashes like Poseidon and Griffin.

<br>

---

### VIII. Conclusion: Sponge as a Philosophy

Sponge functions are not merely algorithmic tricks — they embody a **design philosophy rooted in observability and compositional soundness**.

- Security isn't built from compression chaining, but from **black-box indistinguishability**;
    
- The sponge doesn't try to block every attack, but instead limits what the attacker can see;
    
- Provable security derives from **indifferentiability**, not ad-hoc resistance mechanisms.
    

This is why sponge functions underpin Keccak (SHA-3), Poseidon (ZK-friendly hashing), Rescue (algebraic soundness), and other modern cryptographic constructions.