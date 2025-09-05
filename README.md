# Ven-Factor-Lens-Replicant
Estimate your portfolio’s factor exposures and risk decomposition using investable ETF proxies (a “Ven-Lens-style” approach), then print clean tables.
Pipeline:

1. Set up & data
*Loads packages and your tickers + share counts.

*Pulls daily prices from Yahoo via tidyquant, starting 2016-01-01 (edit start_date as needed).

2. Portfolio construction (buy-and-hold)
*Computes an as-of weight table on a single common valuation date (the min of each symbol’s last available date, so weights sum to 100%).
*Builds a constant-shares portfolio value series and converts it to monthly log returns ret_m$port_ret.

3. Factor panel (ETF proxies)
*Creates macro & style factors with ETF spreads (e.g., Credit = HYG − IEF, Value = VLUE − ACWI, LowRisk = SPLV − SPY).
*Joins your portfolio returns to monthly factor returns → df.

4. Model = LASSO → OLS (parsimonious selection)
*Uses LASSO (cv.glmnet) to select a small, relevant set of factors for your portfolio.
*Refits OLS on that subset to get interpretable betas and t-stats.

5. Risk decomposition & diagnostics
*Computes factor risk contributions (using 
𝑏
⊤
Σ
𝑏
b
⊤
Σb) and each factor’s % of total variance.
*Reports annualized alpha, idiosyncratic vol, and (Adj.) R².

Outputs (printed):
*exposures_tbl — Selected Factor Exposures (beta & t-stat).
*risk_tbl — Risk Contribution by Factor (absolute + % of total).
*stats_tbl — Model Diagnostics (alpha, idio vol, R², Adj. R²).
* A nicely formatted weights table (as-of date, shares, price, $ position, % weight).

Factor glossary (proxies)
*EQ_Global = ACWI (global equity beta)
*Rates = IEF (duration)
*Credit = HYG − IEF (credit spread beta)
*Commodities = DBC
*EM_vs_DM = EEM − ACWI
*USD = UUP (USD up)
*LocalInflation = TIP − IEF (breakeven proxy)
*US_vs_World = SPY − ACWI (home-bias tilt)
*Value = VLUE − ACWI, Quality = QUAL − ACWI, Momentum = MTUM − ACWI
*LowRisk = SPLV − SPY (low-vol vs market), Size = IJR − SPY
*Trend = DBMF (managed-futures trend beta)

Assumptions & how to customize
*Buy-and-hold: returns reflect constant shares (no rebalancing). To model periodic rebalancing, rebuild ret_m from re-weighted constituent returns.
*Dates/tickers: edit start_date, tickers, and shares. (Note: Yahoo uses "BRK-B".)
*Factor set: add/remove proxies by editing factor_syms and the factors block.
*Selection: to lock a fixed factor set (no LASSO), skip glmnet and run OLS on your chosen factors.

Caveats: ETF proxies ≠ vendor factors; data are from Yahoo (occasional gaps/splits fixes), and results depend on your sample window and holdings coverage
