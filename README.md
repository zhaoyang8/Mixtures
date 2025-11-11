# Convex Spectral Mixtures of AR States — Code & Replication

Code to reproduce the empirical results for:

**Convex Spectral Mixtures of AR States: Uniform Whittle Theory Near Unity and Macroeconomic Forecasting**  
Zhaoyang Xu

This repository implements baselines (ARMA, UC, spectral–smoothing AR, ARFIMA) and two spectral‑mixture estimators:

1. Whittle mixture over a boundary‑dense dictionary of signed AR(1) poles and AR(2) cycle atoms (two‑pass grid refine).
2. EM mixture of signed AR(1) + white noise with a sum‑constrained closed‑form M‑step for weights and σ², using disturbance smoothing.

Running the main script reproduces Table 1 and Figures 1–2 from the paper and saves them to disk. Artifacts and filenames are created directly by the script.

**Entry point:** `empirics_benchmarks.py`  
**Dataset:** `statsmodels` built‑in quarterly macro data (no external downloads required).

---

## Quick start

```bash
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python empirics_benchmarks.py
```

Expected outputs in the repo folder:

- `Table1_oos_mse_logscore.csv` and `Table1_oos_mse_logscore.tex` — out‑of‑sample MSE & Gaussian log score for all models × series.
- `Figure1_GDP_spectra_weights.png` — smoothed periodogram vs. fitted spectra (pass‑1 vs pass‑2) for GDP, plus weights panels.
- `Figure2_Reliability_GDP_Tbill.png` — reliability diagrams for GDP and T‑bill (observed vs nominal coverage) and Δ log score annotations.

---

## What’s inside

```
.
├── empirics_benchmarks.py   # main script: baselines + mixtures + plots + table
├── requirements.txt
├── LICENSE
└── README.md
```

### Data
Quarterly U.S. macro series from `statsmodels` (`macrodata`): CPI inflation (% q/q ann.), 3‑month T‑bill rate (%), unemployment rate (%), real GDP growth (% ann., from log‑diffs), and M1 growth (% ann., from log‑diffs). Each series is standardized on the training window.

### Baselines
- ARMA(p, q) with p, q ≤ 3 by AIC (SARIMAX).
- Unobserved Components (UC): local level + stochastic cycle.
- Spectral‑smoothing AR: smooth periodogram with a triangular lag window, invert to autocovariances, fit AR(p) by Yule–Walker with AIC.
- ARFIMA(p, d, q): fractional differencing parameter d via GPH on low frequencies; p, q ∈ {0,1,2} by AIC.

### Spectral mixtures
1) Whittle mixture (AR1 + cycles): signed AR(1) poles and AR(2) rotations; nonnegative weights that sum to the sample variance; a two‑pass refine densifies the grid near detected mass. Forecasts use a state‑space model that stacks AR(1) states and 2‑D rotation blocks (cycles).
2) EM mixture (AR1 + white): fixed grid of signed AR(1) poles; EM alternates disturbance smoothing (E‑step) with a closed‑form, sum‑constrained M‑step for {w_k} and σ², which monotonically increases the Gaussian likelihood.

### Programmatic API (optional)

```python
from empirics_benchmarks import run_experiment, save_table1
from empirics_benchmarks import plot_figure1_gdp, plot_figure2_reliability

results_df, details = run_experiment()
save_table1(results_df)

plot_figure1_gdp(details, out_path="Figure1_GDP_spectra_weights.png")
plot_figure2_reliability(details, results_df,
                         series_list=('Real GDP growth (%, ann.)', '3m T-bill rate (%)'),
                         models=('ARMA','UC','Spec. smooth','ARFIMA','Mix (EM AR1)','Mix (AR1+cycles)'))
```

### Reproducibility notes
- The dataset is deterministic; stochasticity comes from numerical optimization.
- The EM variant uses exact disturbance smoothing; results are stable for fixed grids.
- Plots are saved with `matplotlib` and do not require a display backend.

---

## Citation

Xu, Z. (2025). Convex Spectral Mixtures of AR States: Uniform Whittle Theory Near Unity and Macroeconomic Forecasting.

---

## License

See `LICENSE` (MIT by default). You may change to another OSI‑approved license if preferred.
