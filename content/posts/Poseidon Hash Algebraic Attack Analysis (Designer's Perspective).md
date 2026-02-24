+++
title = "Poseidon Hash Algebraic Attack Analysis (Designer's Perspective)"
date = 2025-08-26T16:57:25+08:00
draft = false
math = true
+++


<br>


In recent years, "ZK-friendly" hash functions designed for zero-knowledge proof (ZK) scenarios have gained widespread attention. They are typically based on carefully constructed algebraic structures to exhibit lower constraint counts in arithmetic circuits, thereby achieving higher efficiency in proof systems. However, it is precisely this algebraic friendliness that exposes them to potential attack surfaces under certain analytical models. Particularly in recent years, algebraic analysis methods—such as Gröbner basis attacks and polynomial degree reduction techniques utilizing subspace trajectories—have seen significant research interest in the cryptanalysis field and have gradually become one of the core tools for evaluating the security of such hash functions.

This article approaches from a designer's perspective, based on a recent paper [GKR25], presents some factual conclusions from that work, and provides supplementary analysis for certain conclusions.

|Related Work|Core Method|Effectiveness Analysis for Poseidon|
|---|---|---|
|**[FP20]**|Closed-form degree expressions|Helpful for modeling Gröbner basis attacks|
|**[BBLP22]**|Skipping (multiple) full rounds|Reduced actual nonlinear depth|
|**[ABM24]**|Round-level Gröbner basis modeling|Shows underestimated vulnerabilities at κ = 1024|
|**[BBL+24]**|FreeLunch Gröbner basis attack|Ineffective due to low S-box degree|
|**[KLR24]**|"Six Worlds" analysis framework|Not yet applied to Poseidon, has potential research value|
|**[GKR25]**|Forward Gröbner basis attack utilizing subspace trajectories|Original analysis shows overestimation or underestimation of required security rounds|
|**[BBB+25]**|Iterated Resultants|Reducible to simple univariate cases|

[GKR25] is the latest work proposed by the Poseidon designers. This work provides the minimum partial rounds required for the Poseidon2 hash function to achieve 128-bit security in Sponge mode implementation, as shown in the figure below. The black line in the figure represents the parameter settings recommended by the Poseidon2 designers (where the dashed line indicates security redundancy of $+7.5\% \cdot r_{P}$), while other colored lines represent the authors' test data for current mainstream attacks.


![](/images/GKR25_PartFig.png)


From the black line in the figure, we can see that the Poseidon authors recommend partial rounds $r_{P}$ values in the $52-53$ range. This data is calculated from the following theoretical lower bound formula:

$$ r_F = 6 ; (r_f = 3, ; r_f' = 3) , , $$

$$ r_P = \left\lceil 
\max \left\{ \underbrace{1 + \frac{\min\{\kappa, \log_2(p)\}}{\log_2(\alpha)} + \log_{\alpha}(t) - r_F}_{\text{Term1}}, \; r_{GB} \right\}
\right\rceil ,  $$

$$ r_{GB} = \max \left\{ 
\frac{\min\{\log_2(p), \kappa\}}{\log_2(\alpha)} - r_F, \; 
t - 1 + \log_{\alpha}(2) \cdot \min \left\{ \frac{\kappa}{t+1}, \frac{\log_2(p)}{2} \right\} - r_F' 
\right\} .$$

---

<br>

## Verification of Poseidon Authors' results:

We first select a parameter set to verify whether we can confirm the authors' results shown in the figure:

- **Parameters**:
    
    - $p \approx 2^{256}$ (therefore $\log_2(p) = 256$)
    - $\alpha = 5$
    - $t = 8$
    - $\kappa = 128$
    - $r_F = 6$ (we first ignore the security margin)
    
- **Calculate intermediate values**:
    
    - $\log_2(\alpha) = \log_2(5) \approx 2.322$
    
    - $\log_\alpha(t) = \log_5(8) = \frac{\log_2(8)}{\log_2(5)} = \frac{3}{2.322} \approx 1.292$


<br>

### Step 1: Compute Term 1 (defense against interpolation attacks)

$$ \mathrm{Term}  1 = 1 + \frac{\min\{128, 256\}}{2.322} + 1.292 - 6 $$ $$ = 1 + \frac{128}{2.322} + 1.292 - 6 $$ $$ \approx 1 + 55.125 + 1.292 - 6 $$ $$ \approx 51.417 $$
<br>

### Step 2: Compute Term 2 ($r_{GB}$)

The formula for $r_{GB}$ also contains a $\max$ operation. We'll calculate its main components separately (temporarily ignoring the unknown $r'_{GB}$ in the formula):

- **First part of $r_{GB}$:**

$$ \frac{\min{\log_2(p), \kappa}}{\log_2(\alpha)} - r_F = \frac{\min\{256, 128\}}{2.322} - 6 \approx 55.125 - 6 = 49.125 $$

- **Second part of $r_{GB}$:**
    
    - $\log_{\alpha}(2) = \frac{\log_2(2)}{\log_2(5)} = \frac{1}{2.322} \approx 0.431$
        
    - $\min\left\{\frac{\kappa}{t+1}, \frac{\log_2(p)}{2}\right\} = \min\left\{\frac{128}{8+1}, \frac{256}{2}\right\} = \min\{14.22, 128\} = 14.22$
        
    - $t - 1 + \log_{\alpha}(2) \cdot (14.22) - r_F = 8 - 1 + 0.431 \cdot 14.22 - 6 \approx 7 + 6.13 - 6 = 7.13$
        

Therefore, $r_{GB} = \max{\{49.125, 7.13\}}$. We can see that $r_{GB}$ is at least $49.125$.

<br>

### Step 3: Final $r_P$ Calculation

$$ r_P = \max{\{\text{Term } 1\},~~ r_{GB}} = \max{\{51.417, r_{GB}\}} $$

Since Term 1's value ($51.417$) is greater than the calculated part of $r_{GB}$ ($49.125$), the final $r_P$ is determined by the defense requirements against interpolation attacks.

$$ r_P \approx 51.417 $$

After rounding, we get $r_P = 52$.

---

<br>

## Parameter Analysis:

After verification, we can use the above calculation method as an assessment tool to evaluate the security of any set of analytical data. Below, we conduct security evaluations for the following two parameter sets.

**Parameters**: $t=2$, $d=1$, $c=0$, $α=5$, $\kappa=128$, $p=381$

**Pre-calculated common values**:

- $\min\{\kappa, \log_2(p)\} = \min\{128, 381\} = 128$

- $\log_2(\alpha) = \log_2(5) ≈ 2.322$

- $\log_α(t) = \log_5(2) = \frac{\log_2(2)}  {\log_2(5)} = 1 / 2.322 ≈ 0.4307$

- $\log_α(2) = \log_5(2) ≈ 0.4307$

<br>

#### Step 1: Calculate $r_{GB}$

Calculation process:

1. **First sub-term of $r_{GB}$:**

$$
\frac{\min\{\log_2(p), \kappa\}}{\log_2(\alpha)} - r_F
$$
$$
= \frac{128}{2.322} - 6
$$
$$
\quad\quad\quad~~~ \approx 55.125 - 6 = 49.125
$$


2. **Second sub-term of $r_{GB}$:**

$$t - 1 + \log_{\alpha}(2) \cdot \min\left\{ \frac{\kappa}{t+1}, \frac{\log_2(p)}{2} \right\} - r'_F$$

First compute the $\min{\ldots}$ part:

$$\min\left\{ \frac{128}{2+1}, \frac{381}{2} \right\} = \min\{42.67, 190.5\} = 42.67$$

Then substitute into the full expression:

$$
= 2 - 1 + 0.4307 \cdot 42.67 - 3
$$
$$
\approx 1 + 18.38 - 3 = 16.38
$$

3. **Determine the Value of $r_{GB}$:**

$$ r_{GB} = \max{\{49.125, 16.38\}} = 49.125 $$

<br>

#### Step 2: Calculate the Final $r_P$

Now, $r_P$ is determined by taking the maximum of two other sub-terms and then rounding up.

1. **First sub-term of $r_P$ (resistance against algebraic attacks):**

$$
1 + \frac{\min\{\kappa, \log_2(p)\}}{\log_2(\alpha)} + \log_\alpha(t) - r_F
$$
$$
\approx 1 + \frac{128}{2.322} + 0.4307 - 6
$$
$$
\approx 1 + 55.125 + 0.4307 - 6
$$
$$
= 56.5557 - 6 = 50.556
$$


2. **Second sub-term of $r_P$ (resistance against GB attacks):**

This value is the $r_{GB}$ we calculated in the previous step. 
$$ r_{GB} = 49.125 $$

3. **Determine the value of $r_P$:**


$$
r_P = \lceil \max\{50.556, 49.125\} \rceil
$$
$$
r_P = \lceil 50.556 \rceil
$$
$$
r_P = 51
$$








