# SPY Implied Volatility Surface (CRR Inversion)
CRR-based implied volatility surface construction from SPY option chains, calibrated via numerical root-finding.

## Implementation Hierarchy
| Notebook | Status | Objective |
| :--- | :--- | :--- |
| **`SPY_Vol_Surface.ipynb`** <br> [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/KSHETRIMAYUMSHAHILSINGH/equity-derivatives-valuation/blob/main/SPY_Vol_Surface.ipynb) | **Canonical** | Volatility extraction via Brent root-finding on CRR binomial trees. Validated for surface rendering. |
| **`Equity_Derivatives_Implied_Volatility_Surface.ipynb`** <br> [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/KSHETRIMAYUMSHAHILSINGH/equity-derivatives-valuation/blob/main/Equity_Derivatives_Implied_Volatility_Surface.ipynb) | **Variant** | Analytical baseline utilizing closed-form Black-Scholes inversion. Deployed as a continuous-time cross-check against the discrete CRR canonical model. |
| **`Equity_Derivatives_Valuation_Risk_Framework_Implied_Volatility_Surface.ipynb`** <br> [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/KSHETRIMAYUMSHAHILSINGH/equity-derivatives-valuation/blob/main/Equity_Derivatives_Valuation_Risk_Framework_Implied_Volatility_Surface.ipynb) | **Exploratory** | Independent implementation attempt; unresolved dependency. |


## Live Analytics
* [**SPY IV Surface (Canonical)↗**](https://kshetrimayumshahilsingh.github.io/equity-derivatives-valuation/SPY_Vol_Surface.html)
* [**SPY IV Surface (Variant) ↗**](https://kshetrimayumshahilsingh.github.io/equity-derivatives-valuation/spy_iv_surface.html)
* [**Volatility Surface (Exploratory) ↗**](https://kshetrimayumshahilsingh.github.io/equity-derivatives-valuation/volatility_surface.html) 

## Methodology
### Data Ingestion & Filtering
* **Source:** Yahoo Finance (Ticker: SPY)
* **Universe:** First 6 listed expiries.
* **Liquidity Filter:** Minimum volume > 0; Moneyness restriction ($K/S \in [0.85, 1.15]$).
* **Parameters:** $T = \text{days to expiry} / 365.25$; r = 4.3%, q = 1.3% (static, not term-structure fit; not dated to a specific quote snapshot).

### Calibration
* **Approach (Canonical, `SPY_Vol_Surface.ipynb`):** Cox-Ross-Rubinstein binomial tree (N=120) inverted via Brent's root-finding algorithm.
* **Variant Approach (`Equity_Derivatives_Implied_Volatility_Surface.ipynb`):** BSM closed-form pricing formula inversion via the same Brent's-method root-finder. With r=4.3% > q=1.3%, early exercise is never actually optimal for calls under these parameters (verified numerically), so the American/European premium is ~0; residual gaps vs. the Canonical surface are N=120 tree discretization noise (a few vol-bps, sign varies by strike/maturity), not a systematic model bias.
* **Constraint:** No-arbitrage bound validation prior to inversion.
* **Front-Month Selection:** Shortest maturity with > 4 non-zero volume contracts.
* **Target:** Bid/ask mid where quoted; last-trade price otherwise.

### Visualization
* **Cross-Sectional:** 2D Volatility Smile (Moneyness vs. IV).
* **Surface:** 3D Volatility Mesh (Moneyness × Maturity × IV).

## Assumptions & Limitations
* Flat, static r and q rather than a fitted term structure or index-implied dividend curve.
* No post-solve arbitrage-consistency checks across strikes/maturities (e.g. no butterfly or calendar arbitrage filtering).
* Last-trade fallback for calibration target can lag for illiquid strikes.
* Solver convergence rate and NaN/failed-inversion frequency not currently logged or reported.
* Canonical (CRR) and Variant (BSM) surfaces are not cross-validated against each other. Given r > q, they should track closely; treat a large or one-directional gap as a bug signal, not an expected feature.
* The hosted Exploratory HTML (`volatility_surface.html`) was generated before the `calculate_iv_complete` regression — treat it as a stale artifact, not evidence the current notebook runs.
