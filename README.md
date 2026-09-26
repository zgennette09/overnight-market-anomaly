# Overnight Anomaly — Comfortable Polish

This version starts from your original Analysis.ipynb and keeps its 57-cell order,
step-by-step calculations, explicit plots, and EMA search loop. A final conclusion
is added as cell 58. The only custom function is a simplified version of your
existing drawdown function.

## What was cleaned up

- Consistent variable names; imports and settings collected near the top.
- One aligned SPY return table (`returns`) and one main wealth table (`growth`).
- The same return difference is reused for the t-test and rolling chart.
- ETF variables have an `etf_` prefix so they cannot be confused with SPY results.
- Tail thresholds and annual observation counts are calculated once.
- Unused expressions, unnecessary copies, and assignment-to-slice issues removed.
- Drawdowns include the original starting wealth.
- Training dates are 2000–2017; historical test dates are 2018–August 2026.
- The selected EMA uses `ema_span = 340` throughout, with price history carried
  into the test period. Its actual returns supply every reported test metric.
- Interpretations and the conclusion reflect the corrected results.

## Reproduce

Keep the `data` directory beside `Analysis_Comfortable_Polish.ipynb` and run the
notebook from top to bottom. It uses the frozen prices by default. Set
`use_saved_data = False` to download prices through the original yfinance method.
Source settings, checksums, and package versions are in `data/provenance.json`.
The saved run used Python 3.14 and the packages in `requirements.txt`.

To rerun from this directory:

```sh
python -m jupyter nbconvert --to notebook --execute --inplace Analysis_Comfortable_Polish.ipynb
```

The notebook was executed successfully. Key metrics were reconciled to the
previously verified analysis, including the actual 340-day EMA results. The
saved chart images were inspected. The full notebook layout was not opened in
a notebook viewer; to inspect it locally, open the .ipynb in Jupyter or VS Code.
If you change the data or settings, check the static markdown interpretations.

Your original notebook and the earlier polished version are unchanged.
