# Convex Spectral Mixtures of AR States — Code & Replication

Code to reproduce the empirical results for

> **Convex Spectral Mixtures of AR States: Uniform Whittle Theory Near Unity and Macroeconomic Forecasting**  
> Zhaoyang Xu

The project models a univariate time series as a finite **convex spectral mixture** of autoregressive atoms. On the time side, the process is a mixture of independent AR(1) states (optionally plus measurement noise). On the frequency side, the spectrum is a convex combination of Poisson kernels, with optional **AR(2) cycle atoms** (rotations) for localized bumps. The repo provides:

- Baseline forecasters: **ARMA**, **Unobserved Components (UC)**, a **spectral‑smoothing AR** predictor, and **ARFIMA**.
- Two implementations of spectral mixtures:
  1. **Whittle mixture** over a boundary‑dense dictionary of signed AR(1) poles and AR(2) cycles (two‑pass grid refine).
  2. **EM mixture** of signed AR(1) + white noise with a **sum‑constraint** closed‑form M‑step for weights/σ².

Outputs include a comparison table (MSE and Gaussian log score) and two figures (spectrum fit; reliability diagrams).

---

## Quick start

```bash
# 1) Create & activate a virtual environment (example: venv)
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate

# 2) Install dependencies
pip install -r requirements.txt

# 3) Run the experiment (writes a CSV, a TeX table, and two figures)
python empirics_benchmarks.py
