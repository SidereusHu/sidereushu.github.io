
+++
title = "Consensus Layer Signature Scheme Considerations: Evolution from BLS to the Post-Quantum Era"
date = 2025-09-16T16:57:25+08:00
draft = false
math = true
+++

<br>


With the rapid development of quantum computing technology, blockchain systems face unprecedented security challenges. Particularly for the consensus layers of mainstream blockchains like Ethereum, the BLS signature schemes they rely on will become vulnerable in the face of quantum computers. This article delves into the considerations for choosing consensus layer signature schemes, focusing on analyzing the trade-offs between hash-based XMSS schemes and lattice-based Falcon schemes in different scenarios, and exploring new signature paradigms for ZK-native chains.

<br>

## 1. Quantum Threats and Special Requirements of Consensus Layers

### 1.1 Current Challenges

Since Ethereum's The Merge upgrade in 2022, it has adopted a PoS consensus mechanism where validators use BLS signatures to confirm blocks and votes. BLS signatures have excellent aggregation properties—thousands of signatures can be compressed into a single proof, greatly reducing storage and bandwidth requirements. However, these signature schemes based on elliptic curve cryptography are vulnerable to quantum computers running Shor's algorithm.

It is estimated that within 10-15 years, computers with millions of qubits may be able to break ECDSA/BLS. Therefore, finding signature schemes that can both resist quantum attacks and meet the special needs of consensus layers has become an urgent priority.

### 1.2 Special Requirements of Consensus Layers

Consensus layer signature schemes must meet the following key requirements:

- **Post-quantum security**: Ability to resist attacks from Grover's and Shor's algorithms
- **Efficient aggregation capability**: Support for non-interactive aggregation of thousands of validator signatures
- **Low verification cost**: On-chain verification gas costs must be controllable
- **Bandwidth-friendly**: Aggregated signature sizes should be reasonable
- **Implementation simplicity**: Easy to audit, reducing the risk of implementation errors


<br>

## 2. Main Candidates for Post-Quantum Signature Schemes

### 2.1 XMSS: The Conservative Hash-Based Choice

#### Core Principles

XMSS (eXtended Merkle Signature Scheme) is a stateful hash signature scheme that organizes multiple one-time signatures (WOTS+) through a Merkle tree. Its security relies entirely on the one-way property of the underlying hash function, which is the most conservative and widely accepted assumption in cryptography.

#### Advantages in Consensus Layers

1. **Minimal security assumptions**: Only relies on hash functions, no complex algebraic structures needed
2. **Aggregation-friendly**: While XMSS signatures don't support native aggregation, their verification process mainly involves hash operations, making them very suitable for "proof-based aggregation" through ZK-SNARKs/STARKs
3. **Forward security**: Each signing key can only be used once; even if keys are compromised, past signatures remain secure

#### Practical Parameters and Optimizations

In Ethereum proposals, through optimizations like Target Sum Winternitz (TSW) encoding, XMSS signature sizes can be controlled within the 2-4 KB range. Combined with pqSNARKs, thousands of signatures can be aggregated into a single compact proof:

```
Original: 4,096 validator signatures ≈ 16 MB
After aggregation: Single proof < 1 MB
```

### 2.2 Falcon: The Compact Lattice-Based Scheme

#### Core Characteristics

Falcon is based on the NTRU lattice problem and is one of the NIST-standardized post-quantum signature schemes. Its greatest advantage is extremely compact signatures:

- Falcon-512: Signature ~666 bytes
- Falcon-1024: Signature ~1.3 KB

#### Challenges in Consensus Layers

1. **Aggregation complexity**: Falcon doesn't support native aggregation and faces serious challenges when combined with ZK proof systems
2. **Arithmetization difficulties**: Verifying Falcon signatures involves complex polynomial operations and norm calculations, making conversion to ZK circuits extremely costly
3. **Theoretical uncertainties**: Under the Quantum Random Oracle Model (QROM), security proofs rely on "rewinding" techniques that fail. Existing "Falcon aggregation" schemes (like LaBRADOR) require additional algebraic structures and rewinding techniques, but their security proofs are questionable under quantum models (random oracle paradox, infeasibility of quantum rewinding).

### 2.3 Why Choose XMSS Over Falcon?

Let's understand this choice through detailed comparison:

| Dimension | XMSS | Falcon | Consensus Layer Impact |
|-----------|------|--------|----------------------|
| **Security Assumptions** | Hash functions (minimal) | NTRU lattice (more complex) | XMSS more conservative, lower long-term risk |
| **Single Signature Size** | 2-4 KB | < 1 KB | Falcon better individually, but gap narrows after aggregation |
| **Aggregation Scheme** | ZK-friendly (hash circuits) | ZK-difficult (algebraic circuits) | **Decisive factor**: XMSS aggregation efficiency far exceeds Falcon |
| **Implementation Complexity** | Low (hash only) | High (floating-point, FFT, side-channels) | XMSS easier to audit and implement securely |
| **State Management** | Required (stateful) | Not required (stateless) | Falcon more convenient in user scenarios |

Key insight: **The core need of consensus layers is aggregation**, and XMSS's hash structure is naturally suited for ZK proof systems, while Falcon's lattice-based structure leads to explosive proof generation costs.

<br>

## 3. Proof-Based Aggregation: The Synergy of XMSS + ZK

### 3.1 Workflow

```
1. Collection phase: Aggregator collects thousands of XMSS signatures
2. Proof generation: Run verification program, generate ZK proof for "all signatures are valid"
3. On-chain verification: Only need to verify a single compact ZK proof
```

### 3.2 Why Does XMSS Excel in This Model?

The core operation for verifying XMSS signatures is hash computation. "Circuit-friendly" hash functions like Poseidon and Rescue are extremely efficient in the arithmetization process. In contrast, Falcon verification involves:

- Complex operations on polynomial rings
- High-precision norm calculations
- Lattice basis reduction related operations

The circuit representation of these operations may be several orders of magnitude larger than hashes, directly causing explosive growth in proof generation time and costs.

### 3.3 Deep Considerations of Quantum Security

Under the Quantum Random Oracle Model (QROM), many classical security proof techniques fail. Particularly, the Falcon + ZK combination faces the "rewinding paradox":

- Classical proofs rely on repeatedly "rewinding" queries to extract keys
- Quantum attackers query in superposition states, breaking the foundation of rewinding arguments
- XMSS's simple hash structure avoids these theoretical pitfalls

<br>

## 4. New Paradigms for ZK-Native Chains: Poseidon Preimage Proofs

### 4.1 Conceptual Innovation

In ZK-native chains, a new signature paradigm is emerging: using "proof of knowing preimage x such that Poseidon(x) = Y" as identity authentication. This essentially uses the ZK proof system itself as the signature mechanism.

### 4.2 Advantage Analysis

1. **Circuit nativeness**: Poseidon is designed specifically for ZK circuits with minimal constraint counts
2. **Flexible composition**: Can bind arbitrary context (timestamps, chain state, etc.) in proofs
3. **Privacy enhancement**: Only reveals the proof, leaking no information about the preimage

### 4.3 Practical Challenges

```
Traditional signature generation: Millisecond level
ZK proof generation: Second to minute level (depending on circuit scale)

Verification comparison:
Traditional signature verification: O(1) simple operations
SNARK verification: O(1) but larger constants
STARK verification: O(log n) or higher
```

Key trade-off: While proof generation is much slower, if the entire system is ZK-native, this approach can bring unprecedented composability and privacy features.

<br>

## 5. Practical Recommendations: Layered Strategy

Based on the above analysis, we recommend adopting a layered strategy:

### 5.1 Consensus Layer

**Choice: XMSS + pqSNARKs**

Rationale:
- Fixed validator set, manageable state management
- Aggregation is core requirement, XMSS + ZK combination is optimal
- Conservative security assumptions, suitable for long-term critical infrastructure

### 5.2 Execution Layer

**Choice: Falcon or other compact schemes**

Rationale:
- Diverse users, stateless is more practical
- Single signature size directly affects gas costs
- No need for large-scale aggregation

### 5.3 ZK-Native Application Layer

**Choice: Poseidon preimage proofs**

Rationale:
- Deep integration with application logic
- Clear advantages in composability and privacy
- Proof costs can be amortized through batch processing


<br>

## Conclusion

The choice of consensus layer signature schemes is not just a technical decision, but a security commitment for the next decade or even longer. XMSS represents a conservative yet pragmatic choice—through the simplest cryptographic assumptions, combined with innovative proof aggregation technology, it lays a solid foundation for blockchain consensus in the post-quantum era.

While lattice-based schemes like Falcon are superior in some metrics, the special needs of consensus layers—particularly large-scale signature aggregation—make XMSS a more suitable choice. Meanwhile, the rise of ZK-native chains brings entirely new design spaces for signature schemes, and innovative approaches like Poseidon preimage proofs demonstrate the enormous potential of deep integration of cryptographic primitives.

Ultimately, there is no "optimal" signature scheme, only the most appropriate choice for specific scenarios. Through a layered strategy, we can adopt the most suitable schemes at the consensus layer, execution layer, and application layer respectively, building a post-quantum blockchain system that is both secure and efficient.

<br>


## References

1. RFC 8391 - XMSS: eXtended Merkle Signature Scheme
2. NIST Post-Quantum Cryptography Standardization
3. Ethereum Research Forum - Consensus Layer PQC Discussions
4. Hash-Based Multi-Signatures for Post-Quantum Ethereum
5. Poseidon: A New Hash Function for Zero-Knowledge Proof Systems
6. Falcon: Fast-Fourier Lattice-based Compact Signatures over NTRU
7. The Lean Consensus Roadmap
8. Target Sum Winternitz: An Improvement to the Winternitz Signature Scheme