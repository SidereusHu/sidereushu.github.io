+++
title = "Poseidon Hash Algebraic Attacks (Calculation Tools)"
date = 2025-09-29T16:57:25+08:00
draft = false
math = true
+++


<br>



In recent years, "ZK-friendly" hash functions designed for zero-knowledge proof (ZK) scenarios have received widespread attention. They are typically based on carefully constructed algebraic structures to achieve lower constraint counts in arithmetic circuits, thereby enabling higher efficiency in proof systems. However, it is precisely this algebraic friendliness that exposes potential attack surfaces under certain analysis models. Particularly in recent years, algebraic analysis methods—such as Gröbner basis attacks and polynomial degree reduction techniques using subspace trails—have gained significant research interest in the cryptanalysis field and have gradually become one of the core tools for evaluating the security of such hash functions.

This article is based on a recent paper [GKR25] by the Poseidon designers, verifying the main conclusions related to the attack algorithms proposed in that work, making it our theoretical calculation tool.

## Analysis Tool 1: Parameter Bounds Recommended by Poseidon Designers

[GKR25] is the latest work proposed by the Poseidon designers. This work provides the minimum partial rounds required for the Poseidon2 hash function in Sponge mode to achieve 128-bit security in the figure below. The black line in the figure represents the parameter settings recommended by the Poseidon2 designers (where the dashed line includes a safety redundancy of +7.5% · rₚ), and the other colored lines are the current mainstream attack test data conducted by the authors.

![](/images/GKR25_PartFig.png)

From the black line in the figure, we can see that the Partial rounds value rₚ recommended by the Poseidon authors is in the 52-53 range, and with safety redundancy, the rₚ value is in the 56-58 range. This data is calculated from the following theoretical lower bound formula:

$$ r_F = 6 ; (r_f = 3, ; r_f' = 3) , , $$

$$ r_P = \left\lceil 
\max \left\{ \underbrace{1 + \frac{\min\{\kappa, \log_2(p)\}}{\log_2(\alpha)} + \log_{\alpha}(t) - r_F}_{\text{Term1}}, \; r_{GB} \right\}
\right\rceil ,  $$

$$ r_{GB} = \max \left\{ 
\frac{\min\{\log_2(p), \kappa\}}{\log_2(\alpha)} - r_F, \; 
t - 1 + \log_{\alpha}(2) \cdot \min \left\{ \frac{\kappa}{t+1}, \frac{\log_2(p)}{2} \right\} - r_F' 
\right\} .$$

## Verification of Poseidon Authors' Conclusions:

We first select a set of parameters to see if we can verify the authors' conclusions shown in the figure:

- **Parameters**:
    
    - $p \approx 2^{256}$ (thus $\log_2(p) = 256$)
    - $\alpha = 5$
    - $t = 8$
    - $\kappa = 128$
    - $r_F = 6$ (we first don't consider safety redundancy)
- **Calculate intermediate values**:
    
    - $\log_2(\alpha) = \log_2(5) \approx 2.322$
    - $\log_\alpha(t) = \log_5(8) = \frac{\log_2(8)}{\log_2(5)} = \frac{3}{2.322} \approx 1.292$

### **Step 1: Calculate Term1 (Defense against interpolation attacks)**

$$ \text{Term}1 = 1 + \frac{\min{\{128, 256}\}}{2.322} + 1.292 - 6 $$ $$ = 1 + \frac{128}{2.322} + 1.292 - 6 $$ $$ \approx 1 + 55.125 + 1.292 - 6 $$ $$ \approx 51.417 $$

### Step 2: Calculate $r_{GB}$

The $r_{GB}$ formula also contains a max operation. We calculate its main components separately:

- **First part of $r_{GB}$:**

$$ \frac{\min{\{\log_2(p), \kappa}\}}{\log_2(\alpha)} - r_F = \frac{\min{\{256, 128}\}}{2.322} - 6 \approx 55.125 - 6 = 49.125 $$

- **Second part of $r_{GB}$:**
    
    - $\log_{\alpha}(2) = \frac{\log_2(2)}{\log_2(5)} = \frac{1}{2.322} \approx 0.431$
        
    - $\min\left\{\frac{\kappa}{t+1}, \frac{\log_2(p)}{2}\right\} = \min\left\{\frac{128}{8+1}, \frac{256}{2}\right\} = \min\{14.22, 128\} = 14.22$
        
    - $t - 1 + \log_{\alpha}(2) \cdot (14.22) - r_F = 8 - 1 + 0.431 \cdot 14.22 - 6 \approx 7 + 6.13 - 6 = 7.13$
        

Therefore, $r_{GB} = \max{\{49.125, 7.13\}}$. We can see that $r_{GB}$ is at least $49.125$.

### Step 3: Final $r_P$ calculation

$$ r_P = \max{\{\text{Term1}, r_{GB}\}} = \max{\{51.417, r_{GB}\}} $$

Since the value of Term1 (51.417) is greater than the calculated part of $r_{GB}$ (49.125), the final $r_P$ is determined by the defense requirements against interpolation attacks.

$$ r_P \approx 51.417 $$

After rounding up, we get $r_P = 52$. This result matches the authors' recommended parameter range. If we add the 7.5% safety margin, then the final partial rounds would be:

$$\lceil 52+52\times 7.5\% \rceil = \lceil 55.9 \rceil = 56$$

## Parameter Analysis:

After verification, we can now use the above analysis method as an analysis tool to conduct security assessments for any set of analysis data. For example, below we perform a security assessment for the following set of parameters.

**Parameters**: $t=2$, $d=1$, $c=0$, $α=5$, $\kappa=128$, $p=381$

**Pre-calculated common values**:

- $\min{\{\kappa, \log_2(p)\}} = \min{\{128, 381\}} = 128$
- $\log_2(\alpha) = \log_2(5) \approx 2.322$
- $\log_\alpha(t) = \log_5(2) = \log_2(2) / \log_2(5) = 1 / 2.322 \approx 0.4307$
- $\log_\alpha(2) = \log_5(2) \approx 0.4307$

#### Step 1: Calculate $r_{GB}$

Calculation process:

1. **First subterm of $r_{GB}$:**

$$ \frac{\min{\{\log_2(p), \kappa}\}}{\log_2(\alpha)} - r_F $$ $$ = \frac{128}{2.322} - 6 $$ $$ \approx 55.125 - 6 = 49.125 $$

2. **Second subterm of $r_{GB}$:**

$$t - 1 + \log_{\alpha}(2) \cdot \min\left\{ \frac{\kappa}{t+1}, \frac{\log_2(p)}{2} \right\} - r'_F$$

First calculate the $\min{\ldots}$ part:

$$\min\left\{ \frac{128}{2+1}, \frac{381}{2} \right\} = \min\{42.67, 190.5\} = 42.67$$

Then substitute into the complete expression:

$$ = 2 - 1 + 0.4307 \cdot 42.67 - 3 $$ $$ \approx 1 + 18.38 - 3 = 16.38 $$

3. **Determine the value of $r_{GB}$:**

$$ r_{GB} = \max{\{49.125, 16.38\}} = 49.125 $$

#### Step 2: Calculate final $r_P$

Now, $r_P$ is determined by taking the maximum of the other two subterms and then rounding up.

1. **First subterm of $r_P$ (resistance against algebraic attacks):**

$$ 1 + \frac{\min{\{\kappa, \log_2(p)}\}}{\log_2(\alpha)} + \log_\alpha(t) - r_F $$ $$ \approx 1 + \frac{128}{2.322} + 0.4307 - 6 $$ $$ \approx 1 + 55.125 + 0.4307 - 6 $$ $$ = 56.5557 - 6 = 50.556 $$

2. **Second subterm of $r_P$ (resistance against GB attacks):**

This value is the $r_{GB}$ we calculated in the previous step. $$ r_{GB} = 49.125 $$

3. **Determine the value of $r_P$:**

$$ r_P = \lceil \max{\{50.556, 49.125\}} \rceil $$ $$ r_P = \lceil 50.556 \rceil $$ $$ r_P = 51 $$

The result of $r_P = 51$ matches the authors' recommended parameter range. If we add the 7.5% safety margin, then the final partial rounds would be:

$$\lceil 51+51\times 7.5\% \rceil = \lceil 54.825 \rceil = 55$$

<br>

## Analysis Tool 2: Analysis of the Improved Attack Algorithm Proposed in [GKR25]

The improved attack algorithm proposed by the [GKR25] authors is based on the original GB attack algorithm and proposes a forward GB attack algorithm. The attack model Model 1 (FW) it adopts is shown in the following screenshot from the original paper:

![](/images/GKR25_FWModel.png)

## Verification of Poseidon Authors' Conclusions:

![](/images/GKR25_Table3.png)

We first select a set of parameters from Table 3 to see if we can verify the corresponding conclusions in the table. Before this, we restate our goal: find the minimum integer $r_P$ such that the attack complexity $C_{GB} ≥ 2^{128}$.

- Corresponding inequality to be verified: $$\left(1 + 2\sqrt{t - d + \alpha} \cdot r_F + \frac{r_p^\ell}{2}\right)^2 \geq 2^{128}$$
    
- Corresponding equivalent condition after transformation: $$\left(1 + 2\sqrt{t - d + \alpha} \cdot r_F + \frac{r_p^\ell}{2}\right) \geq 2^{64}$$
    

## Test Case 1

We selected the $t=8$ row from the first part of Table 3 ($p \approx 2^{96}, d=3$).

- Table 3 target value: $r_p = 2 - 5$
    
- Known parameters:
    
    - $t = 8, d = 3, \alpha = 3$
    - $R_F = 6 \rightarrow r_F = 3$
    - According to Table3's sponge mode setting, $c = d = 3$
- Derived parameters:
    
    - $r = t - c = 8 - 3 = 5$
    - $\ell = r - d = 5 - 3 = 2$

## Calculation Process:

Substitute the parameters into our inequality condition:

$$\left( \frac{1 + 2\sqrt{8 \cdot 3 \cdot 3^{r_p / 2}}}{8 + 3} \right) \geq 2^{64}$$

$$\left( \frac{1 + 2\sqrt{24 \cdot 3^{2 + 0.5 r_p}}}{11} \right) \geq 2^{64}$$

We solve this inequality for $r_p$. Let $N$ such that $(N) \geq 2^{64}$.

Using approximation $N^{11} \geq 2^{64}$, we get $N \geq (11! \cdot 2^{64})^{1/11}$.

After a series of logarithmic operations and algebraic simplifications (process similar to the previous calculation): $$1+9.798 \cdot 3^{2+0.5r_P} \ge 4.9 \cdot 2^{5.818}$$$$0.7925 \cdot r_P \ge 1.658$$$$r_P \ge 2.09$$

**Conclusion**: The calculation requires $r_P$ to be greater than or equal to 2.09, which rounds up to $r_P = 3$. Table 3 gives the range as $2-5$. Our calculation result **3** falls within this range.

## Test Case 2

We selected the parameters corresponding to the $t=16$ row in Table3 with $p ≈ 2^{128}$, $d=2$.

- Table 3 target value: $r_p = 12$
    
- Known parameters:
    
    - $t = 16, d = 2, \alpha = 3$
    - $R_F = 6 \rightarrow r_F = 3$
    - According to Table3's sponge mode setting, $c = d = 2$
- Derived parameters:
    
    - $r = t - c = 16 - 2 = 14$
    - $\ell = r - d = 14 - 2 = 12$

The calculation process is the same as Test Case 1. The calculation result is $r_p = 11$, which differs from the target value $r_p = 12$ by only one round.

## Test Case 3

We selected the parameters corresponding to the $t=24$ row in Table3 with $p ≈ 2^{256}$, $d=1$.

- Table 3 target value: $r_p = 22$
    
- Known parameters:
    
    - $t = 24, d = 1, \alpha = 3$
    - $R_F = 6 \rightarrow r_F = 3$
    - According to Table3's sponge mode setting, $c = d = 1$
- Derived parameters:
    
    - $r = t - c = 24 - 1 = 23$
    - $\ell = r - d = 23 - 1 = 22$

The calculation process is the same as Test Case 1. The calculation result is $r_p = 21$, which also differs from the target value $r_p = 22$ by only one round.

## Test Case 4

We selected the parameters corresponding to the $t=12$ row in Table3 with $p ≈ 2^{256}$, $d=1$.

- Table 3 target value: $r_p = 10$
    
- Known parameters:
    
    - $t = 12, d = 1, \alpha = 5$
    - $R_F = 6 \rightarrow r_F = 3$
    - According to Table3's sponge mode setting, $c = d = 1$
- Derived parameters:
    
    - $r = t - c = 12 - 1 = 11$
    - $\ell = r - d = 11 - 1 = 10$

The calculation process is the same as Test Case 1. The calculation result is $r_p = 9$, which still differs from the target value $r_p = 10$ by only one round.

<br>

# Parameter Analysis:

After verification, we can now use the above analysis method as an analysis tool to conduct security assessments for any set of analysis data. For example, below we perform a security assessment for the following set of parameters.

### Parameters to Calculate:

- State width: $t = 2$
- Mode: compress, $c = 0$
- Rate: $r = t - c = 2$
- Security requirement: $\kappa=128$
- Modulus: $p=381$
- Output length: $d = 1$
- Full rounds: $R_F = 6 \implies$ half full rounds $r_F = 3$
- S-box: $\alpha = 5$

#### Known constants:

- Attack complexity exponent: $\omega = 2$

##### Intermediate variables:

- Subspace trail length: $\ell = r - d = 2 - 1 = 1$

#### Target formula:

The attack cost $C_{GB}$ must reach the security target of $2^{128}$.

$$ C_{GB} \approx \left( \binom{ 1 + 2\sqrt{t \cdot d} \cdot \alpha^{r_F + \frac{r_P - \ell}{2}} }{ t + d } \right)^{\omega} \geq 2^{128} $$

### Substitute parameters and establish inequality:

Substituting the above parameters into the formula:

$$ \left( \binom{ 1 + 2\sqrt{2 \cdot 1} \cdot 5^{3 + \frac{r_P - 1}{2}} }{ 2 + 1 } \right)^2 \geq 2^{128} $$

### Solve for $r_P$

- First, take the square root of both sides:

$$ \left( \frac{1 + 2\sqrt{2} \cdot 5^{2.5+0.5\cdot r_P}}{3} \right) \geq 2^{64} $$

- For convenience, let the upper term of the binomial coefficient (that longer expression) be $N$: $$ N = 1 + 2\sqrt{2} \cdot 5^{2.5+0.5\cdot r_P} $$ Then the inequality becomes: $$ \binom{N}{3} \geq 2^{64} $$
    
- When $N$ is much larger than $k$, the binomial coefficient can be approximated as: $$ \binom{N}{k} \approx \frac{N^k}{k!} $$ Therefore, $$ \frac{N^3}{3!} \geq 2^{64} \implies \frac{N^3}{6} \geq 2^{64} $$
    
- Rearranging: $$ N^3 \geq 6 \cdot 2^{64} $$
    
- Taking the cube root of both sides: $$ N \geq \sqrt[3]{6 \cdot 2^{64}} = \sqrt[3]{6} \cdot 2^{64/3} $$
    
- Calculate the approximate value of the right side: $$ \sqrt[3]{6} \approx 1.817, \quad 2^{64/3} \approx 2^{21.333} $$ So, $$ N \geq 1.817 \cdot 2^{21.333} $$
    
- Now, we substitute back the expression for $N$ to solve for $r_P$: $$ 1 + 2\sqrt{2} \cdot 5^{2.5+0.5\cdot r_P} \geq 1.817 \cdot 2^{21.333} $$
    
- The +1 on the left side can be ignored in the face of such large numbers. Taking the base-2 logarithm $\log_2$ of both sides: $$ \log_2 (2\sqrt{2}) + (2.5 + 0.5 \cdot r_P) \log_2 (5) \geq \log_2 (1.817) + 21.333 $$
    
- Substituting common logarithm values: $$ \log_2 (2\sqrt{2}) = 1.5, \quad \log_2 (5) \approx 2.322, \quad \log_2 (1.817) \approx 0.862 $$ $$ 1.5 + (2.5 + 0.5 \cdot r_P) \cdot 2.322 \geq 0.862 + 21.333 $$ $$ 1.5 + 5.805 + 1.161 \cdot r_P \geq 22.195 $$ $$ 7.305 + 1.161 \cdot r_P \geq 22.195 $$ $$ 1.161 \cdot r_P \geq 14.89 $$ $$ \lceil r_P \rceil \geq \Bigg\lceil \frac{14.89}{1.161} \Bigg\rceil \approx \lceil 12.825 \rceil = 13  
    $$
Under the parameter settings we provided, after correctly interpreting the FW-GB attack cost formula, to achieve 128-bit security, the required partial rounds value is: $$r_{P}=13$$





