+++
title = "Practical Recommendations (for Poseidon2 + BLS12-381 + Compress Mode)"
date = 2025-08-03T16:57:25+08:00
draft = false
math = true
+++


<br>


**Suggested Parameters:**

| Parameter      | Suggested (Width 2) | Suggested (Width 3) | Notes                                      |
| -------------- | ------------------- | ------------------- | ------------------------------------------ |
| Prime Field    | BLS12-381           | BLS12-381           | Newly supported field in Gnark             |
| Width *t*      | 2                   | 3                   | Width 3 is preferred if Gnark supports it  |
| S-box          | $x^5$               | $x^5$               | Common and secure choice over prime fields |
| Full Rounds    | 8                   | 8                   | Avoid using fewer than 8 rounds            |
| Partial Rounds | 22                  | 14–17               | Based on updated recommendations           |
| Mode           | Compress Mode       | Compress Mode       | Used for input compression in UTXO models  |
| Security Level | ≥128 bits           | ≥128 bits           | Default setting meets the requirement      |

---

### Additional Notes

- **Security Analysis**: Considers recent advances in algebraic attacks such as Gröbner basis methods, [KR21], [BCD+20], [ABM23], and [GKR25].

- **Simplified and updated design guidance**: No longer uses $ x \mapsto x^{-1} $ or the STARKAD family.

- **Clearer round number recommendations**: Based on the latest cryptanalysis.

- **Efficiency emphasis**: Prime fields like **BLS12-381** offer superior performance in ZKP systems such as Gnark.



