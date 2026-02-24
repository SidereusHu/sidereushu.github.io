+++
title = "Post-Quantum Readiness in Blockchain: Threats, Roadmaps, and Migration Strategy II"
date = 2025-07-12T16:57:25+08:00
draft = false
math = true
+++

<br>

## Post-Quantum Cryptography (PQC)

Post-Quantum Cryptography has become a critical solution to counter the threat posed by scalable, controllable quantum computers to current cryptographic systems.

- **Urgency of PQC**: Originates from **Peter Shor’s 1995 algorithm**, which can factor integers and compute discrete logarithms in polynomial time, effectively breaking mainstream schemes like RSA, DH, and ECC.
    
- PQC is **not a single algorithm**, but a set of **parallel technical approaches**, including:
    
    - **Lattice-based cryptography**: The most promising category with well-established theoretical foundations;
        
    - **Hash-based signatures**: Such as SPHINCS+, offering strong security but large signature sizes;
        
    - **Code-based cryptography**: Examples include BIKE and McEliece, characterized by very large keys;
        
    - **Isogeny-based cryptography**: Such as SIKE, featuring compact structures but recently subject to severe cryptanalytic attacks;
        
    - **Multivariate polynomial cryptography**: Like Rainbow, which is vulnerable to algebraic attacks.
        
- These schemes differ greatly in practical terms, especially regarding:
    
    - Key and signature sizes;
        
    - Signing and verification efficiency;
        
    - Maturity of design and security evaluation;
        
    - Sensitivity to block size and verification efficiency in applications like Bitcoin.
<br>

## PQC Algorithm Analysis in the Bitcoin Context

To evaluate the suitability of PQC algorithms for Bitcoin, we compare their **public key/signature sizes and computational efficiency** against the current ECDSA and Schnorr signature schemes[^ChainCodeReport].  
Based on data aggregated from [BIP-360](https://github.com/bitcoin/bips/pull/1670)  [^Bea24a]and the [PQ Signatures Zoo](https://pqshield.github.io/nist-sigs-zoo), the summary is as follows:

- **Hash-based signatures (e.g., SPHINCS+)**: Very small public keys but the largest signatures;
    
- **Lattice-based signatures (e.g., FALCON and CRYSTALS-Dilithium)**: Achieve a good balance between public key and signature sizes;
    
- **Isogeny-based signatures (e.g., SQIsign)**: Smallest signatures and relatively small public keys, but slow and not yet widely vetted.
    

The overall trend indicates PQC technologies are still evolving, with room for improvement in signature size, public key size, and signing/verification efficiency.  
Therefore, locking in a specific scheme too early is suboptimal; a more rational strategy is to **monitor developments continuously and maintain compatibility in design**.

|Metric|ECC (Schnorr)|SPHINCS+|CRYSTALS-Dilithium|FALCON|SQIsign|
|---|---|---|---|---|---|
|Signature Size|~64 bytes|~8–17 KB|~2–3 KB|~0.5–1 KB|~100–300 bytes|
|Public Key Size|~32 bytes|~32 bytes|~1.3 KB|~800 bytes|~200 bytes|
|Signing Time|Fast|Slow|Medium|Fast|Slow|
|Security Maturity|High|High|High|Medium|Low|
|Suitable for Bitcoin?|✅|❌|⚠️|✅|❌|

<br>

## NIST Post-Quantum Standardization Process

To address the long-term threat posed by quantum computing to existing cryptographic standards, NIST launched the **Post-Quantum Cryptography (PQC) standardization process** in December 2016.  This initiative builds on foundational research from PQCrypto conferences (since 2006) and multiple projects in the EU, Japan (e.g., SAFEcrypto, CREST) [^chaincodereport].

The process is globally open, collecting algorithm submissions, conducting public testing and academic review, with the goal to publish algorithms resistant to both classical and quantum attacks.  As of March 2025, NIST has officially released the following standards:

- **FIPS 203**: Encryption algorithm standards (non-signature);
    
- **FIPS 204**: **Module Lattice Signature Standard (ML-DSA)** based on **CRYSTALS-Dilithium**;
    
- **FIPS 205**: **Stateless Hash-Based Signature Standard (SLH-DSA)** based on **SPHINCS+**;
    
- **FIPS 206**: **NTRU-based Fast Fourier Lattice Signature (FN-DSA)** based on **FALCON**, draft expected in 2025.
    

Additionally, another candidate, the LPN-based HQC key encapsulation algorithm, is expected to complete review by 2026 before draft release [NIS25](https://www.nist.gov/news-events/news/2025/03/nist-selects-hqc-fifth-algorithm-post-quantum-encryption).

---

### NIST’s Main Signature Schemes (Current Status)

|FIPS #|Name|Underlying Algorithm|Features|Suitable for Bitcoin?|
|---|---|---|---|---|
|FIPS 204|ML-DSA|Dilithium|Secure, balanced, easy to deploy|✅|
|FIPS 205|SLH-DSA|SPHINCS+|Extremely secure but bulky|❌|
|FIPS 206|FN-DSA|FALCON|Compact, efficient but numerically unstable|✅ (requires floating-point stability improvements)|

- Dilithium is currently the **most balanced scheme** and is widely adopted by Google and Cloudflare;
    
- FALCON is attractive for Bitcoin due to its compactness and speed, but **its floating-point implementation complexity introduces side-channel risks**;
    
- SPHINCS+ is mainly used for ultra-high security scenarios (e.g., backup signatures, critical firmware signing), and is unsuitable for high-frequency signing.






<br>

## References

[^chaincodereport]: Chaincode Labs. *Bitcoin and Quantum Computing: Current Status and Future Directions*. 
[^Bea24a]: H. Beast. *BIP-360: QuBit - Pay to Quantum Resistant Hash. Bitcoin Improvement Proposal (BIP) Pull Request 1670.*  September 2024. 

