## Overview

This project is a full-stack quantitative analysis of WTI crude oil futures (NYMEX: CL), built as a final project for an applied data analytics course (Winter 2026). It combines live market data, historical event studies, SQL-based analysis, and machine learning — structured as a professional research deliverable aimed at energy trading desks, portfolio managers, and corporate hedging teams.

**Core finding:** Peak WTI price response to geopolitical crises has fallen from +33.6% (1990 Gulf War) to +7.2% (2026 baseline) — a ~79% decline that tracks directly with the rise of US shale production and the structural compression of the geopolitical risk premium.

---

## Repository Contents

| File | Description |
|------|-------------|
| `WTI_Futures_FINAL.ipynb` | Main analysis notebook — all four parts, fully narrated |
| `WTI_SQL_Queries_FINAL.sql` | Standalone SQL file — 12 queries with comments, outputs, and rationale |
| `Declining_Oil_Volatility_Part3.pptx` | Presentation deck (14 slides) |

---

## Data Sources

| Dataset | Source | Coverage | Notes |
|---------|--------|----------|-------|
| WTI Spot Prices | [FRED API](https://fred.stlouisfed.org/series/DCOILWTICO) (`DCOILWTICO`) | 1990–present, daily | Pulled live at runtime |
| CFTC Commitments of Traders | [CFTC Disaggregated Futures](https://www.cftc.gov/MarketReports/CommitmentsofTraders/index.htm) | 2006–present, weekly | Commercial, Managed Money, Swap Dealer positions |

Both datasets are fetched programmatically — the notebook runs on current data each time it is executed.

---

## Project Structure

### Part 1 — Instrument Mechanics
- WTI futures contract specifications (1,000 bbl, Cushing delivery, $1/bbl = $1,000/contract)
- Live payoff diagram using the most recent week's actual price move
- P&L translation framework for position sizing

### Part 2 — Market Participants
- CFTC COT positioning: Commercial Hedgers vs. Managed Money vs. Swap Dealers
- 18-month net positioning chart with crowding signal interpretation
- Aggregate P&L attribution across participant groups using real price data

### Part 3 — Geopolitical Event Study
Five events studied across four US energy eras:

| Event | Date | Era | Peak 30d Response |
|-------|------|-----|-------------------|
| Gulf War | Aug 1990 | US Dependent | +33.6% |
| Iraq War | Mar 2003 | US Dependent | +16.8% |
| Libya Crisis | Feb 2011 | Transition | +24.9% |
| Oct 7 Conflict | Oct 2023 | US Independent | +7.9% |
| 2026 Baseline | Feb 2026 | US Dominant | +7.2% |

Normalized price overlays, peak response comparison, and post-event volatility analysis across all five events.

### Part 4 — SQL Analysis & Predictive Models

**SQL (SQLite, 12 queries):**
- 3 JOINs (event × price window, 30d/90d lookbacks, self-join for lag comparison)
- 2 window functions (PARTITION BY era, rolling average with ROWS frame)
- 5 GROUP BYs (year, month, era, vol regime, near-event flag)
- 2 subqueries (CTE + scalar subquery, nested FROM-clause)

**Feature Engineering (`map` and `apply`):**
- US energy era labels mapped by year
- Volatility regime classification (Crisis / High-Vol / Normal / Low-Vol)
- RSI signal scoring, near-event proximity flag, within-era vol z-score

**Model 1 — Linear Regression** (3-day ahead price level):

| Metric | Value |
|--------|-------|
| R² | 0.915 |
| MAE | $1.83 / barrel |
| Naive baseline MAE | $8.26 / barrel |

**Model 2 — Logistic Regression** (3-day strong up-move, threshold > 0.8%):

| Metric | Value |
|--------|-------|
| Accuracy | 62% |
| Naive baseline | ~60% |
| ROC-AUC | 0.630 |

Both models use a strict 80/20 **chronological** train-test split — no shuffling, no look-ahead bias.

---

## Key Findings

1. **The geopolitical risk premium is structurally compressed.** Peak crisis response fell ~79% from 1990 to 2026, coinciding with US production doubling from 5M to 13M barrels/day.

2. **Speculator positioning has declined in parallel.** Managed money net longs dropped from ~310,000 contracts (1990) to ~70,000 (2026) — a 77% reduction that limits momentum-driven amplification.

3. **Era-conditioned VaR is essential.** The US Dominant era shows 56% of days in a normal volatility regime vs. 31% in the US Dependent era. Applying 1990-era stress scenarios to today's market significantly over-reserves capital.

4. **Technical signals add modest but real predictive value.** Model 1 reduces MAE 4.5× vs. baseline. Model 2's AUC of 0.63 indicates non-random directional ranking skill — useful for position sizing, not standalone entry signals.

5. **November is the highest-risk month.** SQL seasonality analysis shows 104% annualized vol in November vs. a 48% full-year average.

---

## How to Run

```bash
# Clone the repo
git clone https://github.com/Ran030214/wti-futures-analysis.git
cd wti-futures-analysis

# Install dependencies
pip install fredapi cot-reports pandas numpy matplotlib seaborn scikit-learn

# Open the notebook
jupyter notebook WTI_Futures_FINAL.ipynb
```

> The notebook fetches live data from FRED and CFTC at runtime. A free FRED API key is required — get one at [fred.stlouisfed.org](https://fred.stlouisfed.org/docs/api/api_key.html) and set it in the API Configuration cell.

---

## Tools & Libraries

`Python` · `pandas` · `numpy` · `matplotlib` · `seaborn` · `scikit-learn` · `fredapi` · `cot-reports` · `SQLite` · `SQL`

---
[README.md](https://github.com/user-attachments/files/25806348/README.md)
