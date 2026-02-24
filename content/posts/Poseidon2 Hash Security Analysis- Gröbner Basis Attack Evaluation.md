+++
title = "Poseidon2 Hash Security Analysis: Gröbner Basis Attack Evaluation"
date = 2025-08-09T16:57:25+08:00
draft = false
math = true
+++


<br>


| Work         | Key Idea                                     | Effectiveness on Poseidon                                                           |
| ------------ | -------------------------------------------- | ----------------------------------------------------------------------------------- |
| **[FP20]**   | Closed-form degree expression                | Useful for modeling GB attacks                                                      |
| **[BBLP22]** | Skipping (multiple) full rounds              | Reduces effective non-linear depth                                                  |
| **[ABM24]**  | Round-level GB modeling                      | Shows underestimated vulnerability at κ = 1024                                      |
| **[BBL+24]** | FreeLunch GB attacks                         | Not effective due to low S-box degree                                               |
| **[KLR24]**  | "Six Worlds" framework                       | Not yet applied; potential for future work                                          |
| **[GKR25]**  | Forward GB Attack Exploiting Subspace Trails | original analysis under- or overestimates the number of rounds needed for security. |
| **[BBB+25]** | Iterated resultants                          | Reduces to simple univariate case                                                   |


<br>


## Key Points

1. **Evolution of Attack Methods**: Beyond traditional analytical approaches such as statistical analysis, algebraic attacks (particularly Gröbner basis attacks) have been recognized by the academic community as significantly more effective and have become the primary research focus in recent years.
2. **Parameter Customization Challenges**: Poseidon hash offers extensive customizable parameter space, which **leads to issues of conservative security assumptions and potential overestimation**.
3. **Current Research Status**: The results provided by [ABM24] are excellent, but their acceptance in the community remains limited at present.

In [GKR25], the authors present a comprehensive analysis of Gröbner basis attacks against Poseidon2 in Sponge mode through Table 5. This analysis is based on a two-step approach: the GB step (Macaulay bound) and the FGLM step (`conjectured dI`). Subsequently, the authors derive the minimum partial rounds values $r_P$ that can be configured, as shown in Table 3.

![](/images/GKR25_Table3.png)

![](/images/GKR25_Table5.png)

**Note**: While the authors provide analysis data for both Sponge mode (Table 5) and Compress mode (Table 6), they only provide partial rounds settings similar to Table 3 for the Sponge mode. Based on the data from Table 6 and combining the Gröbner basis attack complexity formula $(d_{MAC})^{\omega}$ with $\omega = 2$ as used in Table 3, we perform the following calculations:


<br>


## Security Threshold Calculation

To achieve 128-bit security, we need to satisfy:

$$(d_{MAC})^2 \geq 2^{128}$$

Taking the square root of both sides, we get the requirement for the Macaulay Bound $d_{MAC}$:

$$d_{MAC} \geq 2^{64}$$

Taking the logarithm, we obtain our required security threshold:

$$\log_2(d_{MAC}) \geq 64$$

Any parameter configuration where $\log_2(d_{MAC})$ is greater than or equal to 64 can be considered secure against this attack.


<br>


## Analysis from Table 5

Now, let's examine **Table 5 (a) GB step**. This table demonstrates how various complexity indicators change when the number of partial rounds $r_P$ increases from 1 to 5, for fixed parameters $t$, $d$, $c$. We focus particularly on the **Macaulay bound d_MAC** row, where the values in parentheses represent the pre-calculated $\log_2(d_{MAC})$ values.

Taking the first row of Table 5 as an example, with parameters $t=3$, $d=1$, $c=1$, $\ell=1$:

- When $r_P = 1$: $\log_2(d_{MAC}) = 27$ (< 64, **not secure**)
- When $r_P = 2$: $\log_2(d_{MAC}) = 33$ (< 64, **not secure**)
- When $r_P = 3$: $\log_2(d_{MAC}) = 51$ (< 64, **not secure**)
- When $r_P = 4$: $\log_2(d_{MAC}) = 87$ (> 64, **secure**)
- When $r_P = 5$: $\log_2(d_{MAC}) = 105$ (> 64, **secure**)

<br>


## Conclusion

For the parameter set $t=3$, $d=1$, $c=1$, $\ell=1$, only 4 partial rounds are required to elevate the complexity of the most powerful Gröbner basis subspace optimization attack above the 128-bit security level.









