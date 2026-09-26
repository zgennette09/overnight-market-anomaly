# Overnight Return Anomaly in U.S. Equity Markets

An empirical analysis of the **overnight return anomaly** in U.S. equity ETFs from 2000–2026.

The project investigates how much of equity-market returns occur between the previous day's close and the next day's open rather than during regular trading hours. It compares overnight, intraday, and buy-and-hold returns while examining risk-adjusted performance, statistical significance, tail events, stability over time, and a simple EMA-based risk filter.

## Key Findings

- **81.95% of SPY's cumulative log price return occurred overnight** from January 2000 through August 2026.
- Despite this, **buy-and-hold generated more terminal wealth** than overnight-only:
  - Buy-and-hold: **$5.27**
  - Overnight-only: **$3.91**
  - Intraday-only: **$1.35**
- Overnight-only had a higher historical **Sharpe ratio**:
  - Overnight: **0.51**
  - Buy-and-hold: **0.42**
- Overnight-only experienced a substantially smaller **maximum drawdown**:
  - Overnight: **34.76%**
  - Buy-and-hold: **56.47%**
- The mean overnight-minus-intraday return difference was **not statistically significant**:
  - Mean difference: **0.0136% per day**
  - p-value: **0.3495**
  - 95% CI: **[-0.0149%, 0.0421%]**
- The effect was not consistent across ETFs. Overnight-only finished ahead of buy-and-hold for **QQQ and IWM**, but behind for **SPY and DIA**.
- Extreme returns had a major effect on compounded performance:
  - Original overnight growth: **3.91×**
  - Without top 1% of overnight returns: **0.59×**
  - Without bottom 1%: **36.02×**
  - Without both tails: **5.46×**
- A **340-day EMA filter**, selected using 2000–2017 training data, improved the historical risk profile during the 2018–2026 test period:
  - Sharpe: **0.75 to 1.03**
  - Maximum drawdown: **29.41% to 20.91%**
  - Terminal wealth was **0.93% lower** than unfiltered overnight-only.

Overall, the results show a strong historical overnight pattern, but do **not** establish a statistically reliable or proven excess-return strategy.

## Research Questions

1. How much of SPY's price return comes from overnight versus intraday returns?
2. Does overnight-only exposure outperform buy-and-hold on a risk-adjusted basis?
3. Does the anomaly appear across other major U.S. equity ETFs?
4. Are mean overnight returns statistically different from mean intraday returns?
5. How sensitive is the anomaly to extreme observations?
6. Has the anomaly changed over time?
7. Can a simple EMA filter improve the overnight strategy?

## Methodology

Daily returns are decomposed into three components:

```math
R_{\text{overnight},t}
=
\frac{O_t}{C_{t-1}} - 1
```

```math
R_{\text{intraday},t}
=
\frac{C_t}{O_t} - 1
```

```math
R_{\text{daily},t}
=
\frac{C_t}{C_{t-1}} - 1
```

where $O_t$ is the opening price and $C_t$ is the closing price.

These satisfy:

```math
(1 + R_{\text{daily}})
=
(1 + R_{\text{overnight}})
(1 + R_{\text{intraday}})
```

The analysis includes:

- Cumulative and log returns
- Annualized Sharpe ratios
- Maximum drawdowns
- Cross-ETF comparisons
- Paired t-tests and confidence intervals
- Skewness and return distributions
- Tail-event sensitivity analysis
- Rolling and five-year period analysis
- EMA parameter optimization
- Historical train/test evaluation

## Data

The main SPY analysis uses **unadjusted daily prices from January 3, 2000 through August 31, 2026**.

The cross-ETF analysis uses adjusted prices for:

- SPY - S&P 500
- QQQ - Nasdaq-100
- IWM - Russell 2000
- DIA - Dow Jones Industrial Average

The ETF comparison begins in June 2000 to maintain a common date range.

Saved datasets are included in `data/` so the analysis can be reproduced without relying on a new Yahoo Finance download.

## EMA Risk Filter

The tail analysis showed that extreme negative overnight returns have a disproportionate effect on compounded performance.

This motivated a simple trend filter:

> Hold overnight only when the previous closing price is above its EMA.

EMA lengths from **10 to 999 days** were evaluated using data from 2000–2017. The selected **340-day EMA** was then evaluated on the 2018–August 2026 historical test period.

| Strategy | Final Wealth | Sharpe | Max Drawdown |
|---|---:|---:|---:|
| Buy & Hold | $2.8744 | 0.735 | 34.10% |
| Overnight | $2.0846 | 0.753 | 29.41% |
| 340-Day EMA | $2.0651 | 1.028 | 20.91% |

The EMA filter did not improve terminal wealth relative to overnight-only, but it substantially improved the historical Sharpe ratio and maximum drawdown.

This suggests the filter may be more useful as a **risk-management mechanism** than as a return-enhancement strategy.

## Limitations

This project is an exploratory historical analysis rather than evidence of a deployable trading strategy.

Important limitations include:

- Transaction costs and slippage are excluded.
- Taxes are excluded.
- Idle cash earns a 0% return.
- Financing costs are excluded from the hypothetical leveraged analysis.
- The main SPY analysis uses unadjusted prices while the cross-ETF comparison uses adjusted prices.
- Searching many EMA parameters introduces the possibility of overfitting.
- The EMA simulation assumes execution conditions that may not be achievable exactly in practice.
- The 2018–2026 test period was examined during development and should therefore be considered a historical holdout rather than untouched future evidence.

Future work could include walk-forward testing, block-bootstrap inference, realistic trading costs, consistent adjusted-price data, additional assets, and further analysis of which tail events the EMA filter avoids or misses.

## Repository Structure

```text
overnight-market-anomaly/
├── Analysis.ipynb
├── README.md
├── requirements.txt
├── data/
│   ├── spy_unadjusted.csv
│   ├── etfs_adjusted.csv
│   └── provenance.json
└── .gitignore
```

## Reproducing the Analysis

Clone the repository:

```bash
git clone https://github.com/zgennette09/overnight-market-anomaly.git
cd overnight-market-anomaly
```

Install the required packages:

```bash
pip install -r requirements.txt
```

Then open and run `Analysis.ipynb` from top to bottom.

By default, the notebook uses the saved datasets included in the repository:

```python
use_saved_data = True
```

Set this to `False` to download the data again using `yfinance`.

## Tools

- Python
- pandas
- NumPy
- SciPy
- Matplotlib
- yfinance
- Jupyter Notebook

## Conclusion

The overnight return anomaly is clearly visible in the historical data, but its interpretation is more complicated than cumulative returns alone suggest.

Most of SPY's cumulative log price return occurred overnight, and overnight exposure historically exhibited a higher Sharpe ratio and smaller maximum drawdown than buy-and-hold. However, the difference between mean overnight and intraday returns was not statistically significant, results varied across ETFs and time periods, and extreme observations had a substantial effect on compounded performance.

The EMA experiment further showed that improving a strategy's **risk profile** does not necessarily improve its **terminal return**.

The results support further investigation of overnight exposure and simple risk filters, but they do not establish a proven excess-return strategy.
