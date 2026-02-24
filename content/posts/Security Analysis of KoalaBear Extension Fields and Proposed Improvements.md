+++
title = "Security Analysis of KoalaBear Extension Fields and Proposed Improvements"
date = 2025-12-28
draft = false
math = true
+++


## 1.  Motivation

In a finite cyclic group

$$G = \langle g \rangle, \quad |G| = n$$

The discrete logarithm problem (DLP/ECDLP) is formalized as:

**Given $g$ and $y = g^x$, find $x \bmod n$. The secret key $x$ has domain $\mathbb{Z}_n$

Because $g^n = 1$, we have:

$$g^x = g^{x \bmod n}$$

This means: The secret key space is at most the size of the group $n$.

The basic attack is to enumerate $x \in {0, \ldots, n-1}$

Complexity is $O(n)$. Therefore:

- If $n$ is too small, ECDLP is information-theoretically breakable;
- "**Increasing the order** $\Rightarrow$ **improving security**" is definitively established.

All smarter algorithms are essentially "accelerated enumeration":

- Baby-step-Giant-step: $O(\sqrt{n})$ time and space
- Pollard-rho: $O(\sqrt{n})$ time, constant space



## 2.  Security Argument

![](/images/koala8.png)
### 0), Basic Parameters and Preliminary Knowledge:

- **Extension degree (Ext)**: 8
- **KoalaBear base field $p$ size**: $2^{31}$
- **Extension field size**: $(2^{31})^8 = 2^{248}$
- **Pollard's rho generic attack complexity**: $\sqrt{2^{248}} = 2^{124}$
- **Security level**: 124 bits
### 1). Ensure all known specialized attacks are ineffective or inefficient

- **Check Gaudry attack**: Complexity $2^{166}$ 
$$2^{2k(k-1)} \cdot p^{2-2/k} = 2^{112} \cdot 2^{31 \times 1.75} \approx 2^{112} \cdot 2^{54.25} \approx 2^{166.25}$$
    
- **Check Joux-Vitse attack**: Complexity $2^{180}$ 
$$(k-1)!(2^{(k-1)(k-2)}e^k k^{-1/2})^\omega p^2 \approx 2^{180+}$$

- **Check MOV attack**: Embedding degree is sufficiently high 

### 2)., Confirm the most effective attack is a generic attack

- **Pollard's rho generic attack complexity**: $2^{124}$

### 3).  Evaluate whether this complexity is sufficient
    
- If higher security is needed, increase the extension degree..

### 4). Variant Weil Attack over Quartic Extension Fields

Gaudry proposed an index calculus algorithm for the discrete logarithm problem on general abelian varieties of small dimension, and applied this algorithm to the **Weil restriction** of elliptic curves and hyperelliptic curves over small degree extension fields. 

In particular, this attack can solve an elliptic curve discrete logarithm problem defined over $\mathbb{F}_{q^4}$ in heuristic asymptotic running time $\tilde{O}(q^{4/3})$; and an elliptic problem over $\mathbb{F}_{q^4}$ or a **genus 2 problem** over $\mathbb{F}_{q^2}$ in heuristic asymptotic running time $\tilde{O}(q^{3/2})$.
    
- Currently, there is less effective public literature on attacks for $n=9$.  reasons:
    
    - **Algebraic system complexity**: If directly using the Semaev summation polynomial method, the number of variables in the multivariate system is $2^{(n-1)} = 256$, with 9 variables to solve, and the **Gröbner basis complexity is extremely high** and almost infeasible.
	    - $n=9$ versus $n=8$ (which factors as $2 \times 4$) has an advantage—namely, the smallest non-trivial subfield is $\mathbb{F}_{q^3}$ (rather than $\mathbb{F}_{q^2}$), and $q^3$ is already quite large (93 bits), which increases the difficulty for attackers to construct efficient descent curves.
        
    - **Subfield attack difficulty**: Even if descending to $\mathbb{F}_{q^3}$ (small subfield of size $2^{93}$), the complexity of performing index calculus attacks on this extension field (e.g., assuming descending to a genus $g$ curve) is approximately $\tilde{O}((q^3)^{(2-2/g)})$. 
	    - For a relatively small $g$ (e.g., $g=2$), the complexity is approximately $\tilde{O}((2^{93})^{(1)}) = \tilde{O}(2^{93})$, below 128 bits; but if $g$ is generally not that small, and the curve obtained after descent is not necessarily a high-efficiency attack target.
    


## Recommendations

![](/images/KoalarPoseidon.png)

#### 1).  Increase the extension field degree from 8 to 9 (SecLevel: 124 to 139)
#### 2).  Adopt TeddyBear (incompatible with Plonky3)
#### 3).  Two-layer security model design: proof layer, cryptographic layer
#### 4).  Poseidon2 state update issues in the proof layer
#### 5).  Todo the security reduced from proof layer (refer: SP1 paper Dec. 2024)

![](/images/SP1DEC24.png)








