+++
title = "Poseidon Hash Algebraic Attack Analysis (Analyst's Perspective)"
date = 2025-08-18T16:57:25+08:00
draft = false
math = true
+++


<br>


In recent years, "ZK-friendly" hash functions designed for zero-knowledge proof (ZK) scenarios have gained widespread attention. They are typically based on carefully constructed algebraic structures to exhibit lower constraint counts in arithmetic circuits, thereby achieving higher efficiency in proof systems. However, it is precisely this algebraic friendliness that exposes them to potential attack surfaces under certain analytical models. Particularly in recent years, algebraic analysis methods—such as Gröbner basis attacks and polynomial degree reduction techniques utilizing subspace trajectories—have seen significant research interest in the cryptanalysis field and have gradually become one of the core tools for evaluating the security of such hash functions.

This article approaches from an analyst's perspective, based on a recent paper [GKR25], presents some factual conclusions from that work, and provides supplementary analysis for certain conclusions.

|Related Work|Core Method|Effectiveness Analysis for Poseidon|
|---|---|---|
|**[FP20]**|Closed-form degree expressions|Helpful for modeling Gröbner basis attacks|
|**[BBLP22]**|Skipping (multiple) full rounds|Reduced actual nonlinear depth|
|**[ABM24]**|Round-level Gröbner basis modeling|Shows underestimated vulnerabilities at κ = 1024|
|**[BBL+24]**|FreeLunch Gröbner basis attack|Ineffective due to low S-box degree|
|**[KLR24]**|"Six Worlds" analysis framework|Not yet applied to Poseidon, has potential research value|
|**[GKR25]**|Forward Gröbner basis attack utilizing subspace trajectories|Original analysis shows overestimation or underestimation of required security rounds|
|**[BBB+25]**|Iterated Resultants|Reducible to simple univariate cases|

---

<br>

[GKR25] is the latest work proposed by the Poseidon designers. This work presents a series of analytical data for Gröbner basis attacks on Poseidon2 in Sponge mode in Table 5, specifically based on a two-step analysis of GB steps (Macaulay bound) and FGLM steps (`conjectured dI`). The authors then derive the minimum partial rounds value $r_P$ that can be set in Table 3 based on this data.

![](/images/GKR25_Table3.png)

![](/images/GKR25_Table5.png)

The authors provide a series of analytical data for Poseidon2 in both Sponge mode and Compress mode (Table 5 and Table 6 respectively), but do not provide partial rounds settings similar to Table 3. Based on Table 6 data, combined with the Gröbner basis attack complexity formula $(d_{MAC})^{\omega}$, and setting $\omega$ to 2 as in Table 3, we perform the following calculation:

To achieve 128-bit security, we need:

$$(d_{MAC})^2 \geq 2^{128}$$

Taking the square root of both sides, we get the requirement for the Macaulay Bound $d_{MAC}$:

$$d_{MAC} \geq 2^{64}$$

Taking the logarithm, we get the security threshold we need:

$$\log_2(d_{MAC}) \geq 64$$

As long as $\log_2(d_{MAC})$ under a parameter configuration is greater than or equal to 64, we can consider it secure under this attack.

Let's put this into practice and verify this result.


---


## Verification of Poseidon Authors' result

Now, we select a set of parameters from the analytical data table (Table 5) provided by the authors, calculate the required minimum $r_{P}$ value using the "security threshold" method above, and verify whether this value matches the corresponding entry in Table 3. We select the second row of data from Table 5 as our example:

- State width: $t=4$
- Digest length: $d=1$
- Capacity: $c=1$
- S-box: $α=3$

**Security objective:**

- The security level we need to achieve is 128 bits.
- The attack complexity formula is $C_{GB}​≈(d_{MAC}​)^2$.
- Therefore, our security threshold is $\log_{2}​(d_{MAC}​)≥64$.

**Calculation process:**

- When $r_P = 1$: $\log_2(d_{MAC}) = 17$ (< 64, insecure)
- When $r_P = 2$: $\log_2(d_{MAC}) = 35$ (< 64, insecure)
- When $r_P = 3$: $\log_2(d_{MAC}) = 41$ (< 64, insecure)
- When $r_P = 4$: $\log_2(d_{MAC}) = 59$ (< 64, insecure)
- When $r_P = 5$: $\log_2(d_{MAC}) = 113$ (> 64, **secure**)

**Conclusion:** For the parameter set $t=4$, $d=1$, $c=1$, $α=3$, the minimum number of internal rounds $r_P$ required to resist the subspace attack described in the paper and achieve 128-bit security is **5**. This calculated result matches the conclusion in Table 3.

---

<br>

## Parameter Analysis for Compress Mode

After verifying the calculation from Table 5, we can use the "security threshold" method as an assessment tool to evaluate the security of any set of analytical data. Below, we conduct security evaluations for two parameter sets in Compress mode based on the analytical data in Table 6.

![](/images/GKR25_Table6.png)


<br>

### **First Parameter Set**: $t=2$, $d=1$, $c=0$, $α=5$

**Setting security threshold:**

- The security level we need to achieve is 128 bits.
- The attack complexity formula is $C_{GB}​≈(d_{MAC}​)^2$.
- Therefore, our security threshold is $\log_{2}​(d_{MAC}​)≥64$.

**Calculation process:**

After selecting the corresponding data from Table 6, we perform the following calculations:

- When $r_P = 1$: $\log_2(d_{MAC}) = 13$ (< 64, insecure)
- When $r_P = 2$: $\log_2(d_{MAC}) = 19$ (< 64, insecure)
- When $r_P = 3$: $\log_2(d_{MAC}) = 25$ (< 64, insecure)
- When $r_P = 4$: $\log_2(d_{MAC}) = 43$ (< 64, insecure)
- When $r_P = 5$: $\log_2(d_{MAC}) = 61$ (< 64, insecure)
- When $r_P = 6$: $\log_2(d_{MAC}) = 79$ (> 64, **secure**)

**Conclusion**: For the parameter set $t=2$, $d=1$, $c=0$, $α=5$, 6 partial rounds are needed to elevate the complexity of the strongest Gröbner basis subspace optimization attack above the 128-bit security level.

<br>

### **Second Parameter Set**: $t=3$, $d=1$, $c=0$, $α=5$

**Setting security threshold:**

- The security level we need to achieve is 128 bits.
- The attack complexity formula is $C_{GB}​≈(d_{MAC}​)^2$.
- Therefore, our security threshold is $\log_{2}​(d_{MAC}​)≥64$.

**Calculation process:**

After selecting the corresponding data from Table 6, we perform the following calculations:

- When $r_P = 1$: $\log_2(d_{MAC}) = 9$ (< 64, insecure)
- When $r_P = 2$: $\log_2(d_{MAC}) = 15$ (< 64, insecure)
- When $r_P = 3$: $\log_2(d_{MAC}) = 27$ (< 64, insecure)
- When $r_P = 4$: $\log_2(d_{MAC}) = 59$ (< 64, insecure)
- When $r_P = 5$: $\log_2(d_{MAC}) = 87$ (> 64, **secure**)

**Conclusion**: For the parameter set $t=3$, $d=1$, $c=0$, $α=5$, 5 partial rounds are needed to elevate the complexity of the strongest Gröbner basis subspace optimization attack above the 128-bit security level.






