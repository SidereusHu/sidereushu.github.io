+++
title = "Overview and Evaluation of Round-by-Round Modeling Analysis for Poseidon Hash"
date = 2025-09-02T16:57:25+08:00
draft = false
math = true
+++


<br>



Poseidon is a **ZK-friendly hash function**, and its security evaluation largely relies on theoretical modeling of the complexity of **Gröbner basis (GB) attacks**. Early security analyses (such as [GKR+19], [GLR+20]) primarily employed **input–output modeling** (directly converting the input-output relationship of the entire function into a polynomial equation system) and calculated the so-called **degree of regularity** on this model to estimate the complexity of GB attacks.

In [GLR+20], another approach was mentioned—**round-level modeling**: introducing new variables for each S-Box in each round, thereby decomposing the entire function into equation systems for multiple rounds.

Notably, recent work [ABM24] **revisited the feasibility of round-by-round modeling**. They directly referenced the approach from [GLR+20] but further used **practical experiments** to validate the effectiveness of round-by-round modeling in GB attacks, rather than merely relying on theoretical speculation.

<br>

## ABM24's Method

Under the **round-by-round modeling** model used in [ABM24]:

- The input and output of each S-Box are independent variables;
- Polynomial equations for each round only involve the S-Box inputs/outputs and linear layer of the current round;
- This results in lower degree equations in the system (especially when some rounds are linearized), facilitating faster solving by GB algorithms.

Under this modeling, one key indicator of the attack is **$D_{\mathrm{reg}}$ (degree of regularity)**, which affects the complexity estimation of Gröbner basis solving.

[ABM24] argued that $D_{\mathrm{reg}}$ was overestimated, meaning that while the system was previously considered more difficult to solve, it might actually not be that hard.

[ABM24] discovered an assumption error in previous $D_{\mathrm{reg}}$ calculations:

- Originally assumed the equation system was a **regular system**, but it actually wasn't.
    
- Therefore, the correct number of equations and variables should be:
$$(R_{F}−1)⋅t+R_{P}+r$$ where:
- $R_{F}$ = number of full rounds
- $R_{P}​$ = number of partial rounds. 
- $t$ = state size - $r$ = rate, with $r≥t/3$

- Under this correction, the more precise $D_{\mathrm{reg}}$ estimation formula becomes: $$D_{\mathrm{reg}} ≈ \frac {r⋅R_{F}} {2} + R_{P} + α$$(where $α$ is a small constant related to system structure)
    

<br>

## ABM24's Conclusions

- **Main Findings**
    
    - [ABM24] analyzed Poseidon's security assumptions and found that the degree growth rate (degree of regularity) is slower than originally assumed by the designers.
        
    - This means that under certain parameters, the recommended number of rounds is insufficient to achieve the claimed security level.
        
- **Specific Impact**
    
    - **1024-bit security level**: Clearly demonstrated a complete attack instance (i.e., security was significantly overestimated).
        
    - **512-bit and 384-bit security levels**: Security analysis is also invalid, but they did not successfully provide complete attacks—the authors believe other factors in the design may have hindered the final step of the attack.
        
    - **128-bit and 256-bit security levels**: No vulnerable parameter sets were found; the designers' original security estimates remain valid at these levels.
        
- **Scope Limitations**
    
    - Their conclusions lean toward **asymptotic analysis**, not claiming all parameters are problematic, but pointing out that under certain high-security-level parameters, theoretical resistance is worse than expected.

<br>

## Poseidon Authors' Evaluation of [ABM24]

- Poseidon authors accepted [ABM24]'s correction in their updated paper, incorporating the new $D_{\mathrm{reg}}$ formula.
    
- **However**, Poseidon authors stated that this correction **has no impact on currently used parameters**, as commonly used instances still satisfy security conditions. Specifically:
    
    - Security level at **128-bit**
    - Modulus size around 64 bits or 256 bits
    - Common SNARK parameters $t∈{3,5}$
    - Common FRI parameters $t∈{12,24}$
    - All of these remain unaffected.

Therefore, Poseidon authors continued with their original security analysis framework:
$$\Big(\frac {V+D_{\mathrm{reg}}}{D_{\mathrm{reg}}}\Big)^2 \geq 2^{\kappa}$$ where:

- Total number of variables $V=(R_{F}-1)\cdot t + R_{P} +r$
- $\kappa$ is the target security strength

Summary of Poseidon authors' evaluation of [ABM24]:

> [ABM24] pointed out that $D_{\mathrm{reg}}$ estimates in early round-by-round modeling were too high and provided a more accurate calculation formula. Poseidon authors acknowledged this correction and incorporated it into their analysis framework, but found it has no security impact on currently deployed Poseidon instances.

<br>

## Poseidon2 Authors' Evaluation of [ABM24]

- The Poseidon2 paper acknowledged including ABM24's results in their security analysis references (i.e., they at least considered its conclusions).
    
- Poseidon2 believes [ABM24]'s results **will not affect** the number of rounds for any $\text{Poseidon}^{π}$ function instance, because the parameter space of $\text{Poseidon}^{π}$ (especially modulus size $p$, state width $t$, security target $κ$) does not fall within the region where [ABM24] found reduced security margins.
    
- Poseidon2 authors believe [ABM24]'s results have no practical impact, so they do not **adjust the number of rounds** in Poseidon2's parameter selection and security analysis due to this, nor do they specifically discuss [ABM24]'s attack model.
    

<br>

## [GKR25] Authors' Evaluation of [ABM24]

- [GKR25] (a new paper by the Poseidon author team) **did not adopt this round-by-round modeling** approach.
    
    - Reasoning: Poseidon's designers had already argued in early work ([GKR+19] Appendix C.2.2) that round-by-round modeling is **weaker** than directly modeling the entire input-output relationship (i.e., less favorable to attackers).
        
    - In other words, if your attack is based on input–output modeling, you might find lower complexity Gröbner basis attacks, while the round-by-round decomposition adopted by [ABM24] would actually increase complexity.
        
    - Additionally, the affected instances given by [ABM24] (1024-bit security) have very **uncommon** parameter choices, far higher than security levels used in actual deployments (typically 128-bit or 256-bit).
        
- **Open Problems**
    
    - Although Poseidon authors believe round-by-round modeling is weaker, they also acknowledge that there is currently no strict mathematical proof. So [GKR25] treats this as an open problem—future researchers could attempt to formally verify whether "round-by-round modeling" is indeed always weaker than input–output modeling under all parameters and conditions.






