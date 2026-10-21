# Overnight Return Anomaly in U.S. Equity Markets

An exploratory Python analysis of how equity ETF returns differ between overnight and regular trading hours. The project examines compounded growth, risk, statistical uncertainty, and the influence of extreme observations, then evaluates a simple trend filter.

**[Open the analysis notebook](Analysis.ipynb)** for the charts, calculations, and interpretation. Saved outputs are included, so the findings can be reviewed without running the code.

## Main findings

For SPY from January 2000 through August 2026, **81.95% of cumulative log price return occurred overnight**. However, buy-and-hold finished with greater wealth than overnight-only.

| Measure | Overnight-only | Buy-and-hold |
| --- | ---: | ---: |
| Final value of an initial $1 | $3.91 | $5.27 |
| Annualized Sharpe ratio | 0.51 | 0.42 |
| Maximum drawdown | 34.76% | 56.47% |

These results use unadjusted SPY prices, a zero risk-free rate, and no trading costs.

- **Statistical evidence:** A two-sided paired t-test did not establish a difference between mean overnight and intraday returns at the 5% level (`p = 0.3495`). This does not prove the means are equal.
- **Tail sensitivity:** Removing the top 1% of overnight observations reduced terminal wealth from 3.91 times the initial investment to 0.59 times. This uses hindsight and is a sensitivity check rather than an achievable trading rule.
- **Differences across ETFs:** In a separate adjusted-price comparison, overnight-only finished ahead of buy-and-hold for QQQ and IWM, but behind for SPY and DIA.
- **Trend filter:** During the 2018–August 2026 historical test, a 340-day exponential moving average (EMA) filter increased the overnight strategy's Sharpe ratio from 0.75 to 1.03 and reduced maximum drawdown from 29.41% to 20.91%. Terminal wealth was 0.93% lower than overnight-only.

The central takeaway is that a strong historical growth pattern does not, by itself, establish a dependable return advantage. Compounding, tail events, and the uncertainty around estimates all affect the interpretation.

## Approach

The notebook addresses seven questions:

1. How much of SPY's price return occurs overnight versus intraday?
2. How do the strategies compare on Sharpe ratio and maximum drawdown?
3. Does the pattern appear across SPY, QQQ, IWM, and DIA?
4. Are average overnight and intraday returns statistically distinguishable?
5. How sensitive is compounded growth to extreme returns?
6. How does the overnight advantage vary over time?
7. Can an EMA filter improve the overnight strategy's performance?

Overnight returns run from the previous trading day's close to the current open; intraday returns run from the current open to close. The analysis aligns the return dates and checks that overnight and intraday growth multiply to buy-and-hold growth. Log returns provide an additive decomposition of the two sessions' contributions.

Methods include paired t-tests with confidence intervals, five-year period comparisons, rolling averages, tail analysis, and an EMA parameter search. The search uses 2000–2017 training data; its best span is 338 days, with a nearby rounded choice of 340 days evaluated in the historical test.

## Data and tools

Prices were obtained from Yahoo Finance through `yfinance`. Local snapshots are included for reproducibility.

| Dataset | Coverage | Price basis |
| --- | --- | --- |
| Main SPY analysis | January 3, 2000–August 31, 2026 | Unadjusted; excludes dividend income |
| Four-ETF comparison | June 1, 2000–August 31, 2026 | Adjusted |

The SPY dataset contains 6,705 price rows and 6,704 aligned return observations. The ETF dataset contains 6,601 price rows. Download metadata and recorded file hashes are in [`data/provenance.json`](data/provenance.json).

The analysis uses **pandas and NumPy** for data preparation and calculations, **SciPy** for statistical tests, and **Matplotlib** for visualization. Package versions are recorded in [`requirements.txt`](requirements.txt).

## Run the analysis

The notebook was executed locally with Python 3.14. From the project folder, the following commands create an isolated environment and launch JupyterLab on Windows:

```powershell
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
.venv\Scripts\python -m pip install jupyterlab
.venv\Scripts\python -m jupyterlab Analysis.ipynb
```

Select the environment's Python kernel and run all cells in order. Keep `use_saved_data = True` to use the supplied snapshots; the notebook reads paths relative to the project folder. On macOS or Linux, replace `.venv\Scripts\python` with `.venv/bin/python`.

Set `use_saved_data = False` to download prices for the configured dates instead. This requires internet access, and provider revisions may change the results. The configured end date, September 1, 2026, is exclusive. Recheck the written interpretations whenever inputs or parameters change.

## Project files

```text
Analysis.ipynb             Analysis, charts, and interpretation
requirements.txt          Pinned analysis dependencies
data/
  spy_unadjusted.csv       Main SPY price snapshot
  etfs_adjusted.csv        Adjusted SPY, QQQ, IWM, and DIA prices
  provenance.json         Source, download settings, and file metadata
```

## Assumptions and limitations

- The main SPY analysis and ETF comparison use different price bases and starting dates. Their magnitudes should not be compared directly; dividend adjustments affect measured overnight returns.
- Annualization assumes 252 trading days. The risk-free rate and return on idle cash are zero. Spreads, commissions, slippage, taxes, and leverage financing are excluded.
- The paired t-test does not explicitly account for dependence across days or changing volatility. Sharpe and drawdown comparisons are descriptive, without significance tests for their differences.
- Searching 990 EMA spans creates selection risk. The historical test period was already examined during the project, so it is not untouched future evidence.
- The EMA signal uses the previous close and assumes entry at that same closing price. Practical execution would require a signal available before the order cutoff and realistic fills.

## Further work

- Apply consistent dividend treatment across the analysis.
- Quantify sensitivity to trading costs and returns on idle cash.
- Evaluate the filter with walk-forward tests and nearby EMA lengths.
- Use a block bootstrap to examine uncertainty while preserving dependence across observations.

Last reviewed: October 21, 2026.
