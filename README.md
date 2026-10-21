# Overnight Return Anomaly in U.S. Equity Markets

This Python project compares equity ETF returns earned overnight with those earned during regular trading hours. It examines growth and risk, tests the difference in average returns, and checks how extreme observations affect the outcome.

Read the **[one-page project summary](docs/project-summary.pdf)** for a quick overview, or **[open the notebook](Analysis.ipynb)** for the full analysis. The notebook includes saved charts and results, so you can read it without running the code.

## Main findings

From January 2000 through August 2026, overnight returns contributed **85.91% of SPY's cumulative log adjusted return**. Buy-and-hold still finished with more wealth.

| Measure | Overnight-only | Buy-and-hold |
| --- | ---: | ---: |
| Final value of an initial $1 | $6.23 | $8.42 |
| Annualized Sharpe ratio | 0.67 | 0.51 |
| Maximum drawdown | 32.79% | 55.19% |

The baseline uses adjusted prices to approximate returns including dividends. It assumes a zero risk-free rate and leaves out trading costs.

- **Mean returns:** The two-sided paired t-test did not find a statistically significant overnight-minus-intraday mean difference at 5% (`p = 0.1577`). This does not prove the means are equal.
- **Extreme returns:** Removing the top 1% of overnight observations reduced growth from 6.23x to 0.94x. Those dates were identified with hindsight, so this does not show how to trade around them.
- **Other ETFs:** In the shared adjusted-price sample starting in June 2000, overnight-only beat buy-and-hold for QQQ and IWM, but not SPY or DIA.
- **Trend filter:** Training selected a 306-day exponential moving average (EMA). In the 2018-August 2026 historical test, the filter's Sharpe was 0.91 versus 0.88 for overnight-only, and maximum drawdown was 21.75% versus 29.41%. Final wealth was **17.40% lower**. The smaller drawdown came at a substantial cost to growth.
- **Trading costs:** Assumed round-trip costs of 1 and 2 basis points reduced overnight wealth to $3.19 and $1.63. These scenarios show sensitivity to costs; they do not estimate actual execution costs.

The historical pattern is clear, but its reliability remains uncertain.

## Approach

The notebook starts with overnight, intraday, and buy-and-hold growth, then compares risk measures and tests mean returns. Later sections examine extreme observations, changes over time, and an EMA filter. The final section checks how trading costs change the results.

Overnight returns run from the previous trading day's close to the current open; intraday returns run from open to close. All strategies use aligned dates. The code checks that overnight growth multiplied by intraday growth equals buy-and-hold growth. Log returns let the two sessions' contributions be added.

The EMA search tests 990 spans, from 10 to 999 days, on 2000-2017 training data. The code selects the best training result (306 days) and applies it to 2018-August 2026. That test period has already been examined during the project, so it is not a fresh test on unseen data.

## Data and tools

Prices came from Yahoo Finance through `yfinance`. The saved datasets let you reproduce the analysis without downloading new data.

| Dataset | Coverage | Use |
| --- | --- | --- |
| `spy_adjusted.csv` | Jan 3, 2000-Aug 31, 2026 | Main adjusted SPY analysis |
| `etfs_adjusted.csv` | Jun 1, 2000-Aug 31, 2026 | Adjusted SPY, QQQ, IWM, and DIA comparison |

Both main sections use adjusted prices, but their starting dates differ. Compare final wealth within each section rather than across them. The main SPY dataset has 6,705 price rows and 6,704 aligned return observations. The four-ETF dataset has 6,601 price rows.

The adjusted SPY file is built from the original snapshot using `factor = Adj Close / Close`: multiply Open, High, and Low by that factor, set Close to Adj Close, and preserve Volume. This follows [yfinance's auto-adjustment convention](https://github.com/ranaroussi/yfinance/blob/main/yfinance/utils.py). The adjustment used the existing data rather than a new download. Only the adjusted snapshot is included; the earlier unadjusted source is no longer distributed. [`data/provenance.json`](data/provenance.json) records the source settings, adjustment method, and hashes normalized for line endings.

The analysis uses **pandas and NumPy** for data preparation and calculations, **SciPy** for statistical tests, and **Matplotlib** for charts. Package versions are listed in [`requirements.txt`](requirements.txt).

## Run the analysis

The notebook was run with Python 3.14. On Windows, open a terminal in the project folder and run:

```powershell
python -m venv .venv
.venv\Scripts\python -m pip install -r requirements.txt
.venv\Scripts\python -m pip install jupyterlab
.venv\Scripts\python -m jupyterlab Analysis.ipynb
```

Select the environment's kernel and run all cells in order. Keep `use_saved_data = True` to reproduce the saved snapshots. On macOS or Linux, replace `.venv\Scripts\python` with `.venv/bin/python`.

Set `use_saved_data = False` to download adjusted prices for the configured dates. Provider revisions may change the results. September 1, 2026 is the exclusive end date. Check the written findings again after changing the data or parameters.

## Assumptions and limitations

- Adjusted returns approximate returns including dividends. They do not track actual dividend payments or reinvestment, and adjusted price levels are not historical fill prices. The same-day adjustment factor cancels from intraday returns but affects overnight returns across dates.
- Annualization assumes 252 trading days. The risk-free rate and interest on idle cash are zero. Baseline returns leave out spreads, commissions, slippage, taxes, and leverage financing. A separate section shows assumed fixed overnight trading costs.
- The paired test does not explicitly handle dependence across days or changing volatility. The notebook reports Sharpe and drawdown differences without testing whether they are statistically significant.
- Trying 990 EMA spans risks fitting historical noise. The EMA has no separate warm-up period, and the historical test data has already been examined.
- The filter assumes entry at the same closing price used to form its signal. In practice, the signal must be ready before the order cutoff, and the fill may differ from the closing price.

## Further work

- Track dividend payments and use signals available before the closing order cutoff.
- Evaluate the filter with walk-forward tests and nearby EMA spans.
- Apply trading costs to the filter and include interest on idle cash and leverage financing.

Last reviewed: October 21, 2026.
