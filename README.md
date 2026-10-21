# Overnight Return Anomaly in U.S. Equity Markets

An exploratory Python analysis of overnight and intraday equity ETF returns, with a focus on compounded growth, risk, statistical uncertainty, and extreme observations.

**[Open the analysis notebook](Analysis.ipynb)** for the charts, calculations, and interpretation, or read the **[one-page project summary](docs/project-summary.pdf)**. Saved notebook outputs are included for review without running the code.

## Main findings

For SPY from January 2000 through August 2026, **85.91% of cumulative log adjusted return occurred overnight**. Buy-and-hold still finished with greater wealth.

| Measure | Overnight-only | Buy-and-hold |
| --- | ---: | ---: |
| Final value of an initial $1 | $6.23 | $8.42 |
| Annualized Sharpe ratio | 0.67 | 0.51 |
| Maximum drawdown | 32.79% | 55.19% |

Baseline results use adjusted prices as a dividend-inclusive return proxy, a zero risk-free rate, and no trading costs.

- **Statistical evidence:** A two-sided paired t-test did not establish a mean overnight-minus-intraday difference at 5% (`p = 0.1577`). This does not prove equality.
- **Tail sensitivity:** Removing the top 1% of overnight observations reduced terminal wealth from 6.23x to 0.94x. Dates were identified with hindsight; this is not a trading rule.
- **Across ETFs:** Overnight-only finished ahead of buy-and-hold for QQQ and IWM, but behind for SPY and DIA, in the common adjusted-price sample beginning June 2000.
- **EMA filter:** Training selected a 306-day exponential moving average. In the 2018-August 2026 historical test, filtered Sharpe was 0.91 versus 0.88 and maximum drawdown was 21.75% versus 29.41% for overnight-only. Filtered wealth was **17.40% lower**, a material tradeoff.
- **Costs:** Illustrative round-trip costs of 1 and 2 basis points reduced overnight wealth to $3.19 and $1.63, respectively. These are sensitivity scenarios, not estimates of actual execution costs.

The analysis shows why a strong historical growth pattern does not by itself establish a reliable return advantage.

## Approach

The notebook compares overnight, intraday, and buy-and-hold returns; evaluates Sharpe ratios and drawdowns; tests mean differences; examines tails and time periods; and evaluates a trend filter. It ends with price-basis and trading-cost sensitivity checks.

Overnight runs from the previous trading day's close to the current open; intraday runs from open to close. Returns use aligned dates, and checks verify that overnight and intraday growth multiply to buy-and-hold growth. Log returns provide an additive session decomposition.

The EMA search evaluates 990 spans, from 10 to 999 days, using 2000-2017 training data. The exact winner (306 days) is assigned automatically and evaluated on 2018-August 2026 data. That historical test period has already been examined and is not untouched future evidence.

## Data and tools

Prices came from Yahoo Finance through `yfinance`. Local snapshots make the analysis reproducible without a new download.

| Dataset | Coverage | Use |
| --- | --- | --- |
| `spy_adjusted.csv` | Jan 3, 2000-Aug 31, 2026 | Main adjusted SPY analysis |
| `etfs_adjusted.csv` | Jun 1, 2000-Aug 31, 2026 | Adjusted SPY, QQQ, IWM, and DIA comparison |
| `spy_unadjusted.csv` | Jan 3, 2000-Aug 31, 2026 | Original snapshot; price-basis sensitivity only |

Both main sections now use adjusted prices. Their different starting dates still prevent direct comparisons of terminal wealth across sections. The main SPY sample has 6,705 prices and 6,704 aligned return observations; the four-ETF sample has 6,601 prices.

The adjusted SPY file is derived from the original snapshot using `factor = Adj Close / Close`: multiply Open, High, and Low by that factor, set Close to Adj Close, and preserve Volume. This follows [yfinance's auto-adjustment convention](https://github.com/ranaroussi/yfinance/blob/main/yfinance/utils.py). No market data was redownloaded for the adjustment update. Source settings, transformation details, and line-ending-normalized hashes are in [`data/provenance.json`](data/provenance.json).

The project uses **pandas and NumPy** for preparation and calculations, **SciPy** for statistical tests, and **Matplotlib** for charts. [`requirements.txt`](requirements.txt) records the packages used for the updated run.

## Run the analysis

The notebook was executed with Python 3.14. From the project folder, create an isolated environment and launch JupyterLab on Windows:

```powershell
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
.venv\Scripts\python -m pip install jupyterlab
.venv\Scripts\python -m jupyterlab Analysis.ipynb
```

Select the environment's kernel and run all cells in order. Keep `use_saved_data = True` to reproduce the saved snapshots. On macOS or Linux, replace `.venv\Scripts\python` with `.venv/bin/python`.

Set `use_saved_data = False` to download adjusted prices for the configured dates. Provider revisions may change results; September 1, 2026 is the exclusive end date. The price-basis sensitivity section still uses the original local snapshot and requires matching dates. Recheck the narrative after changing data or parameters.

## Assumptions and limitations

- Adjusted returns approximate dividend-inclusive performance. Adjusted historical levels are not executable prices, and explicit dividend cash flows and reinvestment are not reconstructed. The adjustment factor cancels from same-day intraday returns and affects overnight returns across dates.
- Annualization assumes 252 trading days. The risk-free rate and idle-cash return are zero. Baseline returns exclude spreads, commissions, slippage, taxes, and leverage financing; the notebook separately shows fixed-cost overnight scenarios.
- The paired test does not explicitly account for dependence across days or changing volatility. Sharpe and drawdown differences are descriptive, without significance tests.
- Searching 990 EMA spans creates selection risk. The EMA starts without a separate warm-up period, and the historical test was already examined.
- The filter assumes entry at the same close used to determine its signal. Practical execution requires a signal available before the order cutoff and realistic fills.

## Further work

- Reconstruct distributions and use an executable signal timeline.
- Test the filter with walk-forward evaluation and nearby EMA spans.
- Use a block bootstrap to examine uncertainty under dependence.
- Extend cost scenarios to the filter, idle-cash interest, and leverage financing.

Last reviewed: October 21, 2026.
