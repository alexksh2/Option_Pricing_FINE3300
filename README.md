# FINE3300 Group 8 - Asian Option Pricing via Monte Carlo Simulation

## Overview

This project prices **Asian options** using **Monte Carlo simulation** with Geometric Brownian Motion (GBM) for stock price path generation. Asian options are path-dependent derivatives whose payoff depends on the average price of the underlying asset over a specified period, rather than just the terminal price.

The project evaluates 5 different option contracts across multiple configurations:
- **Strike types:** Fixed and Floating
- **Averaging methods:** Arithmetic and Geometric
- **Option types:** Call and Put

## Project Structure

| File | Description |
|------|-------------|
| `FINE3300_Group_8_Group_Project.ipynb` | Main Jupyter notebook with all code, outputs, and analysis |
| `FINE3300_Group_8_Group_Project.pdf` | PDF export of the completed notebook |

## Methodology

### Monte Carlo Pricing Engine

The core `AsianOptionMonteCarlo` class simulates stock price paths using the discrete GBM formula:

$$S_{t+1} = S_t + r \cdot S_t \cdot \Delta t + \sigma \cdot S_t \cdot \sqrt{\Delta t} \cdot Z$$

where $Z \sim N(0,1)$.

**Parameters:**
- Initial stock price ($S_0$): 100
- Risk-free rate ($r$): 0.01
- Time steps ($M$): 250
- Simulations ($N$): 8,500 (determined via convergence analysis in Q1)

### Option Contracts

| Option | Strike ($K$) | Drift ($\mu$) | Volatility ($\sigma$) | Maturity (days) |
|--------|-------------|---------------|----------------------|-----------------|
| #1 | 120.00 | 0.08 | 0.20 | 30 |
| #2 | 80.00 | 0.05 | 0.20 | 30 |
| #3 | 100.00 | 0.08 | 0.25 | 90 |
| #4 | 80.00 | 0.04 | 0.15 | 60 |
| #5 | 150.00 | 0.08 | 0.40 | 90 |

## Analysis Questions

### Q0: Pricing All 5 Options
Prices each option across all 8 combinations of strike type (fixed/floating), averaging method (arithmetic/geometric), and option type (call/put).

### Q1: Simulation Performance (Option #1)
- Convergence analysis: option price vs. number of simulations ($N = 100$ to $10{,}000$)
- Standard error vs. number of simulations
- Standard error vs. volatility ($\sigma = 0.1$ to $1.0$)
- Determines optimal $N = 8{,}500$ for balancing accuracy and computational cost

### Q2: Varying Stock Price (Option #1 vs #2)
Compares call option prices across stock prices ($S_0 = 0$ to $200$) for Options #1 (deep OTM, $K=120$) and #2 (deep ITM, $K=80$), illustrating the effect of moneyness.

### Q3: Varying Volatility (Option #4 vs #5)
Compares put option prices across volatilities ($\sigma = 0.05$ to $0.30$) for Options #4 ($K=80$) and #5 ($K=150$), demonstrating how volatility impacts option value differently based on moneyness.

### Q4: Delta Hedging (Option #4)
Computes the numerical delta ($\Delta = \frac{\partial C}{\partial S}$) via finite differences across stock prices for all strike/averaging combinations, analyzing delta behaviour for hedging purposes.

## Requirements

- Python 3.x
- NumPy
- Matplotlib

## Usage

Open and run the Jupyter notebook:

```bash
jupyter notebook FINE3300_Group_8_Group_Project.ipynb
```

All cells can be executed sequentially. A random seed (`random.seed(42)`) is set for reproducibility.

## Course Information

- **Course:** FINE3300 - Python for Finance
- **Institution:** Schulich Business School
- **Group:** 8
