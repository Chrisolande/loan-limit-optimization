# Credit Limit Increase Optimization

<p align="left">
  <img src="https://img.shields.io/badge/Language-R%20%3E%3D%204.2-276DC3?logo=r&logoColor=white" alt="R" />
  <img src="https://img.shields.io/badge/Ecosystem-Tidyverse-1A568C?logo=rstudio&logoColor=white" alt="Tidyverse" />
  <img src="https://img.shields.io/badge/Environment-Jupyter%20%7C%20Posit%20Ark-FF7F00?logo=jupyter&logoColor=white" alt="Ark" />
  <img src="https://img.shields.io/badge/Domain-Fintech%20%26%20Credit%20Risk-008080" alt="Fintech" />
  <img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License" />
</p>

An end-to-end credit risk and limit strategy engine for consumer lending portfolios. This repository implements a data-driven decision system to optimize credit line increase offers across 30,000 customer accounts over a 1-year horizon to maximize net revenue while adhering to portfolio capital limits and controlling for default risk.

---

## Executive Overview

Extending credit limit increases without risk targeting leads to heavy default losses or capital breaches. Conversely, lending strictly to prime borrowers leaves substantial lending capacity unused and leaves revenue on the table.

This project implements a multi-cycle decision engine that balances revenue, default risk, and portfolio capital capacity:

* **Portfolio Net Present Value:** **+$1,114,736** ($37.16 per customer).
* **Capital Efficiency:** **100%** utilization of the $24.77M capital cap with zero budget breaches.
* **Controlled Credit Risk:** Lowers the default rate per accepted increase to **4.99%** (compared to 6.05% under blanket approvals).
* **+87% Lift:** Near double the net profit of a conservative Prime-Only policy ($595K).

---

## Strategy Comparison & Empirical Results

| Strategy | Portfolio NPV | Gross Revenue | Credit Losses | Capital Cap Usage | Cap Status | Default Rate / Accepted |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **No Increase** | $0 | $0 | $0 | 0.0% | Compliant | N/A |
| **Prime Only** | $594,911 | $692,677 | $97,767 | 51.9% | Compliant | 1.5% |
| **Offer All** | $1,066,904 | $2,400,604 | $1,333,700 | 178.4% | **Breached (+78%)** | 6.1% |
| **Optimal Policy** | **$1,114,736** | **$1,645,996** | **$531,260** | **100.0%** | **Compliant** | **5.0%** |
| *Tiered Step Sizing* | *$1,159,294* | *$1,539,925* | *$380,631* | *100.0%* | *Compliant* | *4.9%* |

### Key Strategy Insights

1. **The Blanket Approval Pitfall:** Offering credit increases to every customer generates high gross revenue ($2.40M), but severe defaults on large, lower-tier loans consume over $1.33M in credit losses while exceeding the capital budget by 78%.
2. **The Ultra-Conservative Flaw:** Limiting offers strictly to prime customers achieves low defaults (1.5%), but leaves 48% of available lending capacity unused, forfeiting over $519,000 in net profit.
3. **The Optimal Policy:** Offers are segmented by both risk tier and account loan size:
   * **Prime Accounts:** Eligible for increases across all loan sizes ($500 to $5,000).
   * **Near-Prime Accounts:** Fully eligible up to $3,500; selectively offered between $3,500 and $4,000; cut off above $4,000.
   * **Sub-Prime Accounts:** Offered only on loan sizes up to $2,500, capturing profitable small-ticket revenue while protecting against large loss exposure.

---

## Strategy Dashboard

![Credit Limit Optimization Dashboard](figures/r_dashboard.png)

*The dashboard displays the strategy comparisons, loan-size cutoff policy rules, capital allocation curves, 1,000-run simulation distributions, sensitivity stress tests, and unit break-even recovery economics.*

---

## Core System Architecture

```text
  Customer Portfolio Data (30,000 accounts)
                    │
                    ▼
  ┌──────────────────────────────────────────────┐
  │ 1. Eligibility & Risk Segmentation Engine    │
  │    • 60-day cycle eligibility enforcement    │
  │    • Performance-based credit tier mapping   │
  └──────────────────────┬───────────────────────┘
                         │
                         ▼
  ┌──────────────────────────────────────────────┐
  │ 2. Dynamic Decision & Capital Budgeting      │
  │    • Expected customer repayment tracking    │
  │    • Portfolio exposure capacity balancing   │
  │    • Granular loan-size cutoff generation    │
  └──────────────────────┬───────────────────────┘
                         │
                         ▼
  ┌──────────────────────────────────────────────┐
  │ 3. Stress-Testing & Robustness Validation    │
  │    • Macro scenario analysis (Base/Stress)   │
  │    • 1,000-run Monte Carlo simulation        │
  └──────────────────────────────────────────────┘
```

1. **Eligibility & Account Segmentation:** Enforces 60-day cooldown periods between increases and categorizes accounts based on historical payment behavior.
2. **Capital-Constrained Allocation:** Solves the optimal decision rules across all customer accounts simultaneously so that the portfolio's total exposure hits the exact capital budget without exceeding it.
3. **Macroeconomic Robustness:** Simulates behavior across changing market conditions (inflation, interest rates, and unemployment shocks), verifying that credit rules contract automatically during economic stress to safeguard capital.
4. **Simulation Validation:** A 1,000-iteration Monte Carlo simulation confirms the stability of the policy ($1.085M simulated average vs. $1.084M projected), with 0% probability of portfolio loss.

---

## Project Structure

```text
├── data/
│   ├── loan_limit_increases.csv        # 30,000 customer portfolio records
│   └── loan_limit_increases_10k.csv    # 10,000 record test sample
├── figures/
│   ├── r_dashboard.png                 # 6-panel strategy dashboard
│   ├── r_01_strategy_npv.png           # Strategy NPV comparison chart
│   ├── r_02_policy_map.png             # Offer decision rules across loan sizes
│   ├── r_03_frontier.png               # Capital utilization vs. net return curve
│   ├── r_04_monte_carlo.png            # Simulation distribution (1,000 runs)
│   ├── r_05_sensitivity.png            # Stress grid across default & recovery rates
│   └── r_06_breakeven.png              # Unit economics break-even analysis
├── outputs/
│   ├── strategy_results.csv            # Summary metrics across all tested strategies
│   ├── validation.csv                  # Analytical vs. simulation verification table
│   ├── sensitivity.csv                 # Scenario stress testing results
│   └── policy_summary_by_tier_loan.csv # Granular offer allocation by loan bucket
├── loan_limit_optimization.ipynb       # Reproducible R analysis notebook
└── README.md
```

---

## Getting Started

### Prerequisites

* **R (>= 4.2)**
* **Jupyter Notebook** with the [Ark R kernel](https://github.com/posit-dev/ark) or `IRkernel`
* R packages: `tidyverse`, `scales`, `gridExtra`, `matrixStats`, `knitr`

To install the required R packages:

```r
install.packages(c("tidyverse", "scales", "gridExtra", "matrixStats", "knitr"))
```

### Running the Notebook

To execute the notebook and regenerate all figures, metrics, and summary tables:

```bash
jupyter nbconvert --to notebook --execute --inplace loan_limit_optimization.ipynb
```

---

## License

This project is licensed under the MIT License.
