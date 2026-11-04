# Ven-Factor-Lens-Replicant

Estimate your portfolio's factor exposures and risk decomposition using investable ETF proxies, then print clean summary tables. The idea was inspired by an interview with one of Venn (2 Sigma) heads of risk in portfolio construction.

---

## Pipeline

### 1. Setup & Data
- Loads packages, your tickers, and share counts.
- Pulls daily prices from Yahoo Finance via `tidyquant`, starting `2016-01-01` (edit `start_date` as needed).

### 2. Portfolio Construction (Buy-and-Hold)
- Computes an as-of weight table on a single common valuation date (the earliest of each symbol's last available date), so weights sum to 100%.
- Builds a constant-shares portfolio value series and converts it to monthly log returns (`ret_m$port_ret`).

### 3. Factor Panel (ETF Proxies)
- Creates macro and style factors from ETF spreads (e.g., Credit = HYG − IEF, Value = VLUE − ACWI, LowRisk = SPLV − SPY).
- Joins portfolio returns to monthly factor returns in `df`.

### 4. Model: LASSO → OLS
- Uses LASSO (`cv.glmnet`) to select a small, relevant set of factors.
- Refits OLS on that subset for interpretable betas and t-stats.

### 5. Risk Decomposition & Diagnostics
- Computes factor risk contributions using **b′Σb** and each factor's share of total variance.
- Reports annualized alpha, idiosyncratic volatility, R², and adjusted R².

---

## Outputs

| Object | Description |
|---|---|
| `exposures_tbl` | Selected factor exposures (beta and t-stat) |
| `risk_tbl` | Risk contribution by factor (absolute and % of total) |
| `stats_tbl` | Model diagnostics (alpha, idio vol, R², adj. R²) |
| Weights table | As-of date, shares, price, $ position, % weight |

---

## Factor Glossary

| Factor | Proxy | Captures |
|---|---|---|
| EQ_Global | ACWI | Global equity beta |
| Rates | IEF | Duration |
| Credit | HYG − IEF | Credit spread beta |
| Commodities | DBC | Broad commodities |
| EM_vs_DM | EEM − ACWI | Emerging vs. developed markets |
| USD | UUP | U.S. dollar strength |
| LocalInflation | TIP − IEF | Breakeven inflation |
| US_vs_World | SPY − ACWI | Home-bias tilt |
| Value | VLUE − ACWI | Value style |
| Quality | QUAL − ACWI | Quality style |
| Momentum | MTUM − ACWI | Momentum style |
| LowRisk | SPLV − SPY | Low volatility vs. market |
| Size | IJR − SPY | Small vs. large cap |
| Trend | DBMF | Managed-futures trend |

---

## Assumptions & Customization

- **Buy-and-hold:** Returns reflect constant shares with no rebalancing. To model periodic rebalancing, rebuild `ret_m` from re-weighted constituent returns.
- **Dates and tickers:** Edit `start_date`, `tickers`, and `shares`. Note that Yahoo uses `BRK-B` rather than `BRK.B`.
- **Factor set:** Add or remove proxies by editing `factor_syms` and the factors block.
