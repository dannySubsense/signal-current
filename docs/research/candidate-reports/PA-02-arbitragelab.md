# Candidate Report — ArbitrageLab (hudson-and-thames/arbitragelab)

**Stream:** PA-02 (Numerical & Statistical Methods)
**Status:** Fourth candidate report for PA-02.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, `LICENSE.txt`, `pyproject.toml`, README, `CONTRIBUTING.md`, PyPI JSON, vendor pricing page, direct source and test reads) — not general knowledge recall.

## Assumption checked and found wrong: this is not the same pattern as mlfinlab

The prior mlfinlab report found a proprietary license whose text literally contained a leftover "ArbitrageLab Business License Agreement" template — a strong signal ArbitrageLab itself would also be commercial/closed. **Direct verification shows this is not the case.** ArbitrageLab's actual `LICENSE.txt` (fetched and decoded directly) is a genuine, unmodified **BSD 3-Clause License**, copyright "The Hudson and Thames developers," with no business-license template leftover text — confirmed independently via `pyproject.toml` (`license = "BSD-3-Clause"`), GitHub's own SPDX detection, and PyPI metadata. This is exactly the kind of case this project's global rules exist to catch in the *other* direction: a plausible-sounding inference from a related finding, checked and found not to transfer.

What *is* real: hudsonthames.org/arbitragelab states the product "operates on a commercial subscription and licensing model" — Business tier £100/month/user, Enterprise custom — but this covers "access to documentation and dozens of example notebooks," not the code license itself. **Code is BSD-3-Clause open source on GitHub/PyPI; the paid product is hosted documentation/notebooks/support access** — a materially different arrangement than mlfinlab's now-fully-proprietary code license.

## 1. Repository / canonical URL

`hudson-and-thames/arbitragelab` — https://github.com/hudson-and-thames/arbitragelab. Org homepage https://hudsonthames.org/arbitragelab/, docs at readthedocs-hosted.

## 2. Version / commit reviewed

Latest tag/release `1.0.0` (published 2024-05-12T12:38:12Z), last push to `master` 2024-05-19T16:14:19Z, HEAD commit `32ccd567` ("Merge pull request #117... Release 1.0.0"). Confirmed matching version on PyPI (`info.version == "1.0.0"`).

## 3. License — confirmed clean

BSD-3-Clause, verified across `LICENSE.txt`, `pyproject.toml`, GitHub's SPDX field, and PyPI metadata — all consistent. See assumption-check above.

## 4. Maintenance / activity — real codebase, currently dormant

Not an issue-tracker front-end like mlfinlab: 35,619 KB repo size, 16 tags from `0.1.0` (pre-2021) to `1.0.0` (2024), 15 GitHub Releases, a full `arbitragelab/` package with 12 submodules and a matching `tests/` directory (45 test files). But **activity has stalled**: no commits since 2024-05-19 — over 16 months as of this research (2026-09-05). Only 7 open issues, 692 stars/228 forks. **Verdict: a genuine prior open-source codebase, currently dormant/unmaintained, not a paywall front-end.**

## 5. Architecture summary

Poetry-packaged Python library. Submodules: `codependence`, `cointegration_approach`, `copula_approach`, `distance_approach`, `hedge_ratios`, `ml_approach`, `optimal_mean_reversion`, `other_approaches`, `spread_selection`, `stochastic_control_approach`, `tearsheet`, `time_series_approach`, `trading`, `util`.

## 6. Relevant modules confirmed present

- **Cointegration**: `JohansenPortfolio`, `EngleGrangerPortfolio`, `MinimumProfit`, `CointegrationSimulation`, `MultivariateCointegration`, `SparseMeanReversionPortfolio`, plus Hurst/half-life utilities.
- **Distance method**: dedicated `distance_approach` module + `spread_selection` (pairs selection).
- **Copula-based**: `copula_approach` module, with tests `test_copulas.py`, `test_mixed_copula.py`, `test_vinecop_generate_strategy.py`, `test_copula_pairs_selection.py` — uses the `pyvinecopulib` dependency.
- **Kalman filter**: `KalmanFilterStrategy` in `other_approaches`, verified live in `test_kalman_filter.py`.
- **Mean reversion / OU processes**: `optimal_mean_reversion` module with OU model tests (`test_ou_model.py`, `test_ou_model_jurek.py`, `test_ou_model_mudchanatongsuk.py`, `test_xou_model.py`, `test_cir_model.py`, optimal threshold variants Bertram/Zeng).
- **Stochastic control, ML-based pairs selection** (`ml_approach`, `test_optics_dbscan_pairs_clustering.py`, `test_pca_approach.py`, `test_neural_networks.py`), hedge ratio construction, tearsheet/reporting.

## 7. Tests and test quality

45 test files in `tests/` covering nearly every module 1:1. `CONTRIBUTING.md` states "We require 100% coverage" and references `.coveragerc`. Real unittest-based tests using a bundled `test_data/stock_prices.csv` fixture (e.g., `test_kalman_filter.py` reads real TIP/IEF price data) — genuine, not decorative. The 100%-coverage claim was not independently re-run or verified this pass.

## 8. Deterministic / reproducibility properties — unresolved

Not independently verified — would require running the test suite and checking for seeded randomness in `ml_approach`/`stochastic_control_approach` (neural nets, DBSCAN).

## 9. Asset / timeframe / venue assumptions

Test fixtures use daily equity/ETF closes (TIP, IEF) — suggests library defaults assume daily-bar equity-like data. A generic `futures_roller` test file exists but was not deeply inspected this pass.

## 10. Hidden defaults or semantic coupling — unresolved

Not audited at the code level this pass — would require reading source of `optimal_mean_reversion`/`stochastic_control_approach` for hardcoded thresholds. Flagged as a required follow-up before any ADOPT/ADAPT decision.

## 11. Performance characteristics

No benchmarks found this pass; none claimed anywhere reviewed.

## 12. What Signal Current could reuse

The conceptual architecture/module taxonomy (cointegration vs. distance vs. copula vs. stochastic-control vs. ML pairs-selection, citing Krauss' taxonomy per README) is a useful reference for organizing Signal Current's own strategy library. Specific OU-model closed-form threshold formulas (Bertram, Zeng) are citable academic implementations worth using as a **parity oracle** to validate any from-scratch Kalman/OU implementation.

## 13. What Signal Current should not inherit

The BSD-3-Clause code itself is legally reusable, but do not assume free ongoing docs/support access (that's the paid tier). Do not treat this as a maintained upstream dependency (dormant since May 2024). Do not port hidden defaults without independently sourcing them per this project's own numeric-provenance rule.

## 14. Integration / coupling risks

Heavy, somewhat dated pinned dependencies (`numpy 1.23.5`/`1.26.4`, `pandas 2.0.0`, `statsmodels 0.14.0`, `pyvinecopulib 0.6.5`, `arch 5.5.0`) — likely to conflict with a modern stack; would need isolation/vendoring rather than direct install.

## 15. Required parity / golden tests if used as reference

Golden-value tests against the OU-model closed-form threshold papers (Bertram 2010, Zeng & Lee), Engle-Granger/Johansen cointegration test statistics vs. `statsmodels`/`arch` ground truth, and Kalman filter update sequence vs. a textbook reference implementation — each with a cited academic source per this project's provenance rule.

## 16. Proposed disposition

**REFERENCE**, with elements as **PARITY ORACLE** for specific closed-form OU/threshold formulas. License permits reuse (BSD-3-Clause, confirmed) and the codebase is real and substantive, but it is unmaintained (16+ months stale), dependency-pinned to an aging stack, and its paid layer (docs/support) means Signal Current can't lean on ongoing vendor support. Not ADOPT/FORK — too stale to take on as a live dependency. Use as documentation/design reference and as a checked-against oracle for a small number of well-cited closed-form formulas, not as installed infrastructure.

## 17. Confidence level

**HIGH** on license, version, module inventory, and maintenance timeline — all read directly from GitHub API/raw files/PyPI/vendor pricing page. **MEDIUM** on architecture-detail correctness inside individual algorithm files (not deep-read) and test-suite actual pass/coverage (not executed).

## 18. Unresolved questions

- Does the BSD-3-Clause GitHub/PyPI package ship the *full* algorithm implementations, or are some paid-tier notebooks/features held back from the public repo? The pricing page's "access to documentation and dozens of example notebooks" wording suggests notebooks may be gated even though code isn't — worth confirming by diffing docs-referenced APIs against what's actually importable from the public package.
- Whether `pip install arbitragelab` succeeds cleanly with a modern Python/numpy/pandas stack.
- Whether the 100%-coverage claim in `CONTRIBUTING.md` is actually true today (untested).

## Sources

- https://github.com/hudson-and-thames/arbitragelab
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/LICENSE.txt
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/pyproject.toml
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/README.md
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/CONTRIBUTING.md
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/DEV_NOTE
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/arbitragelab/cointegration_approach/__init__.py
- https://raw.githubusercontent.com/hudson-and-thames/arbitragelab/master/tests/test_kalman_filter.py
- https://pypi.org/pypi/arbitragelab/json
- https://hudsonthames.org/arbitragelab/
- GitHub API: `/tags`, `/releases`, `/commits`, `/contents/arbitragelab`, `/contents/tests`
