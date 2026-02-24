
+++
title = "Research on VK Uniqueness and Salt in Plonk Setup Phase"
date = 2025-10-11T16:57:25+08:00
draft = false
math = true
+++

<br>

## Plonk's Setup Phase

In Plonk's **Setup Phase (SRS Generation Phase)**, we first obtain a Structured Reference String (SRS). The SRS contains powers of a secret exponent $τ$ (used for polynomial commitments):

$$SRS={g^{τ^0},g^{τ^1},g^{τ^2},\ldots,g^{τ^n}}$$

This step is **one-time and global (universal SRS)**.

Then, for a specific circuit $C$, we derive the Proving Key (PK) and Verifying Key (VK):

$$(PK,~VK)\leftarrow \mathrm{Plonk.Setup}(C, SRS)$$

The PK tuple contains polynomials for each gate, permutation, and constraint in the circuit (commitments encoded with $\tau$); the VK tuple contains public polynomial commitments + selector domain information + constant verification structures.

<br>

## Implementation of VK Uniqueness in Plonk

**Mathematical Expression of Circuits**: In PLONK, the complete structure and constraints of a circuit $C$ are ultimately defined mathematically through a set of selector polynomials ${q_L,q_R,q_M,q_O,q_C}$ and permutation polynomials ${\sigma_{S1},\sigma_{S2},\sigma_{S3}}$. These polynomials collectively encode what type each gate is (addition gate, multiplication gate, or custom gate) and the wiring relationships between gates.

**VK is a Commitment to the Circuit**: The core components of VK are precisely the commitments to these polynomials that define the circuit structure. Specifically, it contains (but is not limited to) commitment values evaluated at the secret point $s$ in the SRS for these polynomials:

$$vk={\ldots,[q_L(s)]_1,[q_R(s)]_1,\ldots,[\sigma_{S1}(s)]_1,\ldots}$$

When the semantic substance of the statement differs, the circuit structure will be different.

Example:

> User A proves "I know the proving key PK_A corresponding to VK_A"  
> User B proves "I know the proving key PK_B corresponding to VK_B"

If the circuit logic corresponding to these two VKs is different (e.g., different number of arithmetic gates, different constraint matrices),  
then their structures ${q_L,q_R,q_M,q_O,q_C}$ must be different.  
Therefore, the resulting VK (Verification Key) must also be different.

That is to say:

- If "knowing PK_A" and "knowing PK_B" require implementing different logic in the circuit → the circuits are different;
    
- If both call the same verifier function, just with different parameters → the circuit is the same.
    

<br>

### **Using Salt to Achieve VK Uniqueness**

For situations requiring "different VK for each user", this can be achieved by:

- Adding user-specific constants in the circuit definition (such as public key, salt, session id);
    
- Or "compiling" these constants into the circuit when generating it;
    
- Thus making the circuit structure $C_i$ different, and VK consequently different.
    

Formally:

$$C_i=C(logic,~user_{salt_i})\quad \Rightarrow \quad vk_i=Setup(C_i,~SRS)$$

Compared to modifying coefficients which can also make VK different, this approach means:

- The circuit logic must change;
    
- The circuit must be recompiled (requiring regeneration of the constraint system);
    
- Cannot be universally reused.
    

While the "salt" approach is:

- Adding a public input constant to the circuit;
    
- This constant participates in some hash or gate constraint;
    
- Logically still the same, but the circuit instance (i.e., specific coefficient values) is different;
    
- Therefore VK is also uniquified, but the circuit template can still be universal.
    

<br>

## Concerns and Recommendations Regarding Salt in the Project Description

The concerns mainly stem from understanding the traditional use of "salt" in cryptography, such as in hash functions or key derivation functions (KDF):

- **Traditional Salt (e.g., password hashing)**:
    
    - **Purpose**: Increase entropy to defend against precomputation attacks (such as rainbow tables).
        
    - **Properties**: Must be random or at least unique.
        
    - **Security concerns**: How to securely store salt, how to ensure its randomness.
        

However, the **role and security model** of this "salt" used to achieve VK uniqueness in PLONK circuits is **completely different**. We can dispel readers' concerns by redefining the purpose of this "salt".

#### The "Salt" Here is Not an Entropy Source, but a "Domain Separator"

It would be more accurate to understand this "salt" as a **public, deterministic identifier**. Its purpose is not to add randomness, but to perform **domain separation**—that is, to cryptographically split a universal circuit template into multiple unrelated, unique instances.