# Error Analysis: FINE3300_Group_8_Group_Project_ERROR.ipynb

This document describes all errors found in the ERROR notebook compared to the correct notebook (`FINE3300_Group_8_Group_Project.ipynb`).

---

## Error 1: Swapped `N`/`M` Parameter Naming (Class Definition)

|               | Correct                      | Error                        |
|---------------|------------------------------|------------------------------|
| **Signature** | `__init__(... T, M, N ...)`  | `__init__(... T, N, M ...)`  |
| `M` meaning   | Time steps                   | **Simulations**              |
| `N` meaning   | Simulations                  | **Time steps**               |

The naming convention for time steps and simulations is swapped. The class is internally self-consistent, but the reversed naming causes confusion in all calling code.

---

## Error 2: Fewer Time Steps

|           | Correct   | Error     |
|-----------|-----------|-----------|
| Time steps | `M = 250` | `M = 100` |

Using fewer time steps reduces the accuracy of the GBM path simulation.

---

## Error 3 (Q1): Wrong Risk-Free Rate

```python
# CORRECT:
option = AsianOptionMonteCarlo(S0=100, K=120, r=r, ...)       # r = 0.01

# ERROR:
option = AsianOptionMonteCarlo(S0=100, K=120, r=0.08, ...)    # uses mu instead of r
```

`r=0.08` is the drift (mu) for Option #1, not the risk-free rate. The risk-free rate should be `r=0.01`.

---

## Error 4 (Q2): Stock Price Never Actually Varies

The purpose of Q2 is to analyse option prices across varying stock prices. However, the loop variable `S` is never used:

```python
# CORRECT:
for S in stock_prices:
    option1_fixed_ar = AsianOptionMonteCarlo(S0=S, K=120, r=r, ...)

# ERROR:
for S in stock_prices:
    option1_fixed_ar = AsianOptionMonteCarlo(S0=100, K=120, r=0.08, ...)  # S0=100 hardcoded!
```

Every iteration prices the exact same option, producing flat/noisy lines instead of meaningful sensitivity curves.

---

## Error 5 (Q2): Wrong Risk-Free Rates

```python
# CORRECT:  r=r (0.01) for both options

# ERROR:
# Option 1: r=0.08 (this is mu, not the risk-free rate)
# Option 2: r=0.05 (this is mu, not the risk-free rate)
```

---

## Error 6 (Q3): Wrong Option Labels

The question asks for **Option #4 vs #5**, but the error notebook labels them **"Option 3 vs Option 4"** throughout the code and plot titles.

---

## Error 7 (Q3): Volatility Never Actually Varies

Same class of bug as Error 4. The loop variable `sigma` is never passed to the constructors:

```python
# CORRECT:
for sigma in volatilities:
    option4_fixed_ar = AsianOptionMonteCarlo(..., sigma=sigma, ...)

# ERROR:
for sigma in volatilities:
    option3_fixed_ar = AsianOptionMonteCarlo(..., sigma=0.15, ...)  # hardcoded!
    option4_fixed_ar = AsianOptionMonteCarlo(..., sigma=0.40, ...)  # hardcoded!
```

The sensitivity analysis is meaningless since volatility never changes between iterations.

---

## Error 8 (Q3): Copy-Paste Bug - Pricing Wrong Objects

The `option4_*` objects are created but never actually used for pricing. Instead, `option3_*` is priced a second time:

```python
# ERROR: creates option4 objects but prices option3 objects
option4_fixed_ar = AsianOptionMonteCarlo(S0=100, K=150, ...)       # created but never used
option4_put_price_fixed_ar, _ = option3_fixed_ar.price_option('put')   # prices option3 again!
option4_put_price_fixed_geo, _ = option3_fixed_geo.price_option('put') # prices option3 again!
```

Both "Option 3" and "Option 4" lines on the plot show data from the same underlying option.

---

## Error 9 (Q3): Wrong Risk-Free Rates

```python
# CORRECT:  r=r (0.01) for both options

# ERROR:
# "Option 3": r=0.04 (this is mu for Option #4)
# "Option 4": r=0.08 (this is mu for Option #5)
```

---

## Error 10 (Q4, cell-13): Multiple Delta Computation Errors

| Issue           | Correct       | Error                          |
|-----------------|---------------|--------------------------------|
| Strike prices   | Same K=80     | **K=80 vs K=105** (different!) |
| Maturity        | `T=60/365`    | `T=1/365`                      |
| Risk-free rate  | `r=0.01`      | `r=0.05`                       |

Using different strike prices for the two points in a finite-difference delta calculation makes the result meaningless. Delta requires identical option parameters except for `S0`.

---

## Error 11 (Archive, cells 16-18): T Not Converted to Years

```python
# CORRECT:
T=60/365    # 0.164 years
T=90/365    # 0.247 years

# ERROR:
T=60        # 60 years!
T=90        # 90 years!
```

The maturity is passed in days without dividing by 365, resulting in absurdly long option lifetimes.

---

## Summary Table

| #  | Location | Error                                      | Impact                      |
|----|----------|--------------------------------------------|-----------------------------|
| 1  | Class    | N/M parameter naming swapped               | Confusing                   |
| 2  | Setup    | 100 time steps instead of 250              | Lower accuracy              |
| 3  | Q1       | `r=0.08` instead of `r=0.01`              | Wrong prices                |
| 4  | Q2       | `S0=100` hardcoded, loop var `S` unused    | Plot is meaningless         |
| 5  | Q2       | `r=mu` instead of `r=0.01`                | Wrong prices                |
| 6  | Q3       | Wrong option labels (#3/#4 vs #4/#5)       | Mislabelled                 |
| 7  | Q3       | `sigma` hardcoded, loop var unused         | Plot is meaningless         |
| 8  | Q3       | Prices `option3` objects instead of `option4` | Both lines show same data |
| 9  | Q3       | `r=mu` instead of `r=0.01`                | Wrong prices                |
| 10 | Q4       | Different K values in delta pair           | Delta is meaningless        |
| 11 | Q4       | `T=1/365` instead of `T=60/365`           | Wrong maturity              |
| 12 | Q4       | `r=0.05` instead of `r=0.01`              | Wrong prices                |
| 13 | Archive  | `T=60` / `T=90` not divided by 365        | 60-90 year maturity         |

## Most Critical Errors

Errors **#4, #7, and #8** are the most severe. They cause the Q2 and Q3 sensitivity analyses to be completely invalid because the parameters that should be varying across the loop are hardcoded, and in Q3 the wrong option objects are priced.
