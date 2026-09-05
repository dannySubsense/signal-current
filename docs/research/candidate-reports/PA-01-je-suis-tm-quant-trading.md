# Candidate Report — je-suis-tm/quant-trading

**Stream:** PA-01 (Systems & Engine Survey), narrowly as an external-regression-fixture source, not an engine.
**Status:** Eleventh PA-01-area candidate report. Confirms the homelab survey's original "fixture source, not infrastructure" framing.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, README, direct read of `MACD Oscillator backtest.py`) — not general knowledge recall.

## 1. Repository / canonical URL / author

`je-suis-tm/quant-trading` — https://github.com/je-suis-tm/quant-trading. Owner login `je-suis-tm`, commit author name "T.M."

## 2. Version / commit reviewed

No tags/releases. Default branch `master`. Latest commit in the sampled range: `611b73f2c3f577ac5b28aaa19ac8c43d3236c7a5` ("Merge pull request #50... Replace Mutable Default Parameters," 2024-04-14). Repo metadata's `pushed_at: 2026-06-20T12:16:37Z` postdates that commit — the true HEAD SHA at that push was not confirmed (further pagination needed, see §18).

## 3. License — clean

Apache License 2.0, confirmed by decoding the actual `LICENSE` file directly — standard text, no modifications, no added NOTICE restrictions. GitHub's own license detector agrees. Full git history on the file was not walked exhaustively, but no evidence of a prior different license was found.

## 4. Maintenance / activity — static personal collection

Created 2018-04-03. Commit history shows the last substantive content commits are dominated by a 2024-04-14 automated dependency-hygiene merge (a bot PR replacing mutable default parameters) and sparse prior additions ("reference data for bollinger band," 2022-12-05; "ajouter shooting star," 2021-11-10). **Reads as a static personal collection that occasionally receives small fixes, not an actively engineered, versioned library.** 10,683 stars/1,877 forks reflect popularity as a learning resource, not engineering activity. 4 open issues.

## 5. Architecture summary — confirmed not a cohesive library

Repo root listing confirms: a **flat collection of independently-named `.py` scripts** (e.g. `"MACD Oscillator backtest.py"`, `"Pair trading backtest.py"`, `"Parabolic SAR backtest.py"`, `"Dual Thrust backtest.py"`), plus a few subdirectories for larger "projects" (Monte Carlo, Oil Money, Ore Money, Smart Farmers, data, preview). **No `src/`, no package `__init__.py`, no shared core module** — this is a loose set of standalone scripts, each apparently runnable independently, confirming the homelab survey's "fixture source, not infrastructure" framing.

## 6. Modules / strategies actually implemented — verified via file listing and one direct source read

Root listing shows at minimum: Awesome Oscillator, Bollinger Bands Pattern Recognition, Dual Thrust, Heikin-Ashi, London Breakout, MACD Oscillator, Options Straddle, Pair Trading, Parabolic SAR, RSI Pattern Recognition, Shooting Star, VIX Calculator, plus the subdirectory projects. **Directly read `MACD Oscillator backtest.py`** (raw file): defines `macd()`, `signal_generation()`, `plot()`, and a `main()` that takes `input()` from stdin for parameters (`ma1`, `ma2`, etc.) — confirmed by source, not inferred from the README's list.

## 7. Tests and test quality — none

No `test_*.py` or `tests/` directory found. A code search for filenames containing "test" only matched files whose names contain the substring "backtest" (demonstration/plotting scripts, not unit tests). **There is no real test suite — zero automated tests.** This directly matters for §11/§15 below.

## 8. Deterministic / reproducibility properties — not CI-friendly, possibly broken as-is

The MACD script takes **interactive stdin input** (`ma1=int(input('ma1:'))`) rather than config/CLI args — not scriptable or CI-friendly as-is. It also imports `fix_yahoo_finance`, a defunct/legacy Yahoo Finance shim (the script's own comment: "need to get fix yahoo finance package first") — an unpinned, likely broken live-data dependency, meaning **the script may not even run today out-of-the-box** without substituting a data source. No `requirements.txt` and no CI workflows exist — no captured/pinned environment, no CI verifying these scripts still run.

## 9. Asset / timeframe / venue assumptions

Scripts assume daily OHLC equity/FX/commodity data from Yahoo Finance-style sources (ticker-based), no venue/exchange abstraction, no explicit timeframe parameterization beyond moving-average window lengths passed at runtime via `input()`.

## 10. Hidden defaults or semantic coupling — named, not hidden, but load-bearing

The README explicitly states: "all trades are frictionless. No slippage, no surcharge, no illiquidity." This is a **named, admitted** assumption, not hidden — but it is a semantic constant load-bearing to every one of the 13+ strategy scripts' presented results.

## 11. Performance rigor — the central finding, directly relevant to fixture-safety

Per the README's own admission, backtests are frictionless (no transaction costs, slippage, or liquidity constraints) and presented as illustrative in-sample demonstrations with plots, not out-of-sample validated results. No evidence anywhere in the repo of walk-forward testing, cost modeling, or statistical significance testing. **This is naive in-sample-only demonstration code, self-admitted by the author.** This substantially limits its use as a "parity reference" for realistic backtest outputs — it can validate that a signal-generation formula matches (e.g., MACD crossover logic), but **not** that a P&L outcome is realistic.

## 12. What Signal Current could reuse

The individual, narrowly-scoped signal-generation functions (e.g., `macd()`, `signal_generation()`) are useful as **golden-formula references** for verifying indicator math (moving averages, crossover logic) against a known, publicly-cited implementation — exactly the "fixture" role the homelab survey flagged, not as runnable infrastructure.

## 13. What Signal Current should not inherit

The frictionless/no-cost backtest assumption; the interactive-stdin execution model; the lack of tests/CI/pinned dependencies; the flat-script architecture; reliance on `fix_yahoo_finance` or any single legacy data source.

## 14. Integration / coupling risks

Apache-2.0 is permissive and compatible with reuse (attribution + NOTICE-carry required), so licensing is not a blocker. Risk is code rot (unpinned legacy dependency, no CI) and the temptation to copy backtest *methodology* (the frictionless assumption) rather than just formula logic.

## 15. Required parity / golden tests if used as reference

For each strategy pulled in as a fixture: (a) freeze a fixed OHLC input series (not live-fetched) and assert signal-generation output matches the reference script's output bit-for-bit or within float tolerance; (b) explicitly do **not** parity-test P&L/backtest numbers, only signal/indicator values, given the admitted frictionless assumption; (c) pin the exact commit SHA of the reference file used as the external regression fixture, since the repo has no version tags.

## 16. Proposed disposition

**REFERENCE, narrowly, as a per-indicator external regression fixture for the signal-generation math only. REJECT as infrastructure or as a source of backtest-methodology precedent.** This confirms the homelab survey's original framing: useful as named individual fixtures, not as production or even architectural precedent, given zero tests, no CI, frictionless-only economics, and an interactive/non-deterministic execution model.

## 17. Confidence level

**HIGH** on license, file/directory structure, absence of tests/CI/requirements, and the frictionless-backtest admission — all directly read from primary sources. **MEDIUM** on the precise current HEAD commit, since `pushed_at` (2026-06-20) postdates the newest commit inspected (2024-04-14) and further pagination was not run to find the exact latest SHA.

## 18. Unresolved questions

- What is the exact commit at `pushed_at: 2026-06-20T12:16:37Z` — a substantive change or a metadata-only event (e.g., topic/description edit)?
- Whether any of the 13+ scripts have been updated post-2024 to fix the `fix_yahoo_finance` dependency — not checked file-by-file.
- Full LICENSE history (was it always Apache-2.0 since 2018) — not verified via full commit-log diff.

## Sources

- https://github.com/je-suis-tm/quant-trading
- https://api.github.com/repos/je-suis-tm/quant-trading
- https://github.com/je-suis-tm/quant-trading/blob/master/LICENSE
- https://raw.githubusercontent.com/je-suis-tm/quant-trading/master/README.md
- https://raw.githubusercontent.com/je-suis-tm/quant-trading/master/MACD%20Oscillator%20backtest.py
- https://github.com/je-suis-tm/quant-trading/commit/611b73f2c3f577ac5b28aaa19ac8c43d3236c7a5
