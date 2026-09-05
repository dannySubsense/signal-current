# Candidate Report — vectorbt

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Third candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (live repo, LICENSE.md at multiple historical commits, README, `pyproject.toml`, GitHub API/`gh api`) — not general knowledge recall.

## 1. Repository / canonical URL / relationship to vectorbt.pro

`polakowo/vectorbt` — https://github.com/polakowo/vectorbt (homepage vectorbt.dev). A **private** sibling repo `polakowo/vectorbt.pro` exists (confirmed via `gh api repos/polakowo/vectorbt.pro`: visibility private, created 2021-12-09, homepage `vectorbt.pro/pvt_ff8edc14`). The OSS README states it directly: OSS vectorbt is "the community edition of VectorBT PRO, a state-of-the-art hybrid backtesting library." Pro is the closed-source commercial successor; OSS is the free/limited tier, not a deprecated fork.

## 2. Version / commit reviewed

`master` @ commit `34b6d5935e...` (2026-08-02, "Fix formatting of Star History section in README"). Latest tagged release **v1.1.0** (2026-07-05).

## 3. License — genuine two-step drift, verified via commit history on the file itself

Read directly at each historical commit SHA via GitHub Contents API — not assumed from any single snapshot:

- 2017-11-16 (`2aceef2d`): **GPLv3** (content verified: "GNU GENERAL PUBLIC LICENSE Version 3").
- 2020-02-18 (`4646dc70`): still GPLv3, re-created file, terms unchanged.
- 2020-12-22 (`8c4c0cf9`): switched to plain **Apache License 2.0**.
- 2021-08-21 (`71402ac8` → `83cacaac`, renamed `LICENSE.md`): switched again to **Apache-2.0 with Commons Clause** — the current license, last touched 2025-01-01 (`6b4b8be1`, year fix) and 2026-01-03 (unrelated release-note edit).

**This is a real trap, same pattern as the two prior reports in this program (NautilusTrader's GPLv3→LGPLv3, LEAN's undiffed 2015 commit).** GitHub's own repo metadata reports `license.spdx_id: NOASSERTION` for the current state, because Commons Clause isn't a recognized SPDX license — **an automated license scanner would silently miss the restriction entirely.** Commons Clause is non-OSI-approved: it forbids selling a product or service whose value derives substantially from the software (hosting/consulting fees included). This has direct, non-hypothetical relevance if Signal Current is ever offered as a hosted/paid service.

## 4. Maintenance / activity

Very actively pushed (`pushed_at` 2026-08-02). But **heavily single-maintainer**: contributor stats show the author at 1,021 commits vs. the next-highest contributor at 22 (21 total contributors). 9,000 stars, 1,157 forks, 140 open issues. The maintenance-risk pattern is real here, not speculative: the author's commercial focus is now `vectorbt.pro` (private repo, pushed same day as this research, 2026-09-05), while OSS releases (v1.1.0, v1.0.0, v0.28.x) appear to trail pro development by roughly a month at this sample point — one data point, not conclusive, but directionally consistent with "OSS as funnel to paid product."

## 5. Architecture summary

NumPy-array vectorization + Numba JIT for hot paths, plus an **optional Rust engine** (`rust/` directory with `Cargo.toml`, exposed as a `vectorbt-rust` pip extra) "to eliminate JIT overhead." Data integration per `pyproject.toml`'s `full` extra: `yfinance`, `python-binance`, `ccxt`, `alpaca-py` — it's a library that plugs into external data/exchange SDKs rather than owning venue connectivity itself.

## 6. Relevant modules

Per root tree and test-file names: portfolio, indicators, signals, generic, records, labels, returns. `benchmarks/` directory with `BENCHMARKS.md`, `BENCHMARKS_NUMBA.md`, `BENCHMARKS_RUST.md`, and runnable `bench_engine.py`/`bench_matrix.py` scripts.

## 7. Tests and test quality

Substantial suite: `test_portfolio.py` (429KB, by far the largest test file), `test_indicators.py` (129KB), `test_signals.py` (157KB), plus `test_records.py`, `test_base.py`, `test_data.py`, `test_engine.py`, `test_returns.py`, `test_settings.py`, `test_utils.py`, `test_plotting.py`, `test_generic.py`, `test_labels.py`, and a `.github/workflows/tests.yml` CI. `pyproject.toml` declares `pytest`, `pytest-cov`, `pytest-xdist`, `codecov` — coverage is tracked, not just asserted by prose.

## 8. Deterministic / reproducibility properties — unresolved

Not independently verified beyond dependency pins (`numpy>=2.4.6`, `pandas>=3.0.3,<4.0`, `numba>=0.66`). No primary source read on RNG-seeding or float-determinism guarantees.

## 9. Asset / timeframe / venue assumptions — medium confidence, README-level

Vectorization requires rectangular data, so multi-asset support means columns of a shared, regular time index — not independent asynchronous event streams per instrument. This contrasts structurally with an event-driven engine (e.g. NautilusTrader): vectorbt's efficiency comes precisely from assuming a common regular time grid, not from handling async multi-venue/multi-clock event simulation. Not fully verified via source code (README/module-name inference only) — flagged medium confidence, not settled.

## 10. Hidden defaults or semantic coupling — unresolved

Not verified in code this pass — would require reading `vectorbt/portfolio/nb.py` or equivalent for default order/fill assumptions (default fill price, default fee/slippage). Flagged for a follow-up code-level pass before adoption.

## 11. Performance characteristics

The repo carries dedicated `benchmarks/BENCHMARKS.md`, `BENCHMARKS_NUMBA.md`, `BENCHMARKS_RUST.md`, and runnable benchmark scripts — a materially stronger evidence basis than marketing prose, and stronger than either prior PA-01 candidate report in this program. The benchmark markdown content itself was not opened this pass — **do not quote any specific throughput number until those three files are actually read.**

## 12. What Signal Current could reuse

The vectorized-portfolio-simulation pattern as a fast pre-filter/screening layer for large parameter sweeps; its indicator/signal factory conventions; its benchmark methodology (dedicated benchmark scripts + markdown reports) as a template for Signal Current's own performance-evidence discipline.

## 13. What Signal Current should not inherit

- The Commons-Clause-style "everything through one maintainer, OSS as funnel to paid pro" governance model — conflicts with Signal Current needing a stable, independently-inspectable dependency, not one whose primary development energy has moved behind a paywall.
- The implicit look-ahead risk of vectorized backtesting: matrix-wide operations across a regular grid make it easy to accidentally reference future rows/columns (e.g. an incorrectly shifted signal) in ways an event-driven bar-by-bar engine structurally prevents. If adopted even as a screening tool, every vectorized signal must be paired with an explicit shift/lag audit, never trusted by default.
- The "fast iteration, run thousands of ideas" philosophy is directly in tension with Signal Current's evidence-gate design ("hard to fool ourselves with") — a tool optimized for high-volume speculative sweeps invites p-hacking-style overfitting unless wrapped in Signal Current's own multiple-testing controls.

## 14. Integration / coupling risks

Commons Clause licensing risk if Signal Current is ever offered as a paid/hosted product; single-maintainer bus factor; dependency on Numba/Rust toolchain version pinning (`numpy>=2.4.6`, Python `>=3.11,<3.15` per `pyproject.toml` — an aggressive floor that could force Signal Current onto bleeding-edge Python/NumPy); the regular-grid assumption may not compose with any async/multi-venue data model Signal Current adopts later.

## 15. Required parity / golden tests if used as reference

Golden-output comparison against a known-correct event-driven reference engine on a small deterministic fixture (fixed OHLCV series, fixed fee/slippage) to confirm no off-by-one/look-ahead in fills; explicit test asserting behavior under irregular/missing-bar data (silent forward-fill vs. error?); a truncation/confound check per this project's own data-integrity rule — assert no silent row-dropping when feeding non-rectangular multi-asset data.

## 16. Proposed disposition

**REFERENCE** only — not ADOPT/WRAP/FORK. Use as a documented comparison point for vectorized-backtest performance claims and as a source of indicator/signal-factory naming conventions. Do not take a runtime dependency on it given (a) the Commons Clause commercial-use restriction, (b) single-maintainer/pro-migration risk, (c) a look-ahead-risk profile misaligned with Signal Current's evidence-gate philosophy. If a fast screening layer is ever wanted, only behind an isolated adapter with mandatory golden/parity tests (§15), and only with explicit sign-off on the Commons Clause implications before any hosted/paid use of Signal Current.

## 17. Confidence level

MEDIUM-HIGH on license history, repo metadata, contributor/activity stats, and dependency manifest (all read directly from primary sources). MEDIUM-LOW on architecture internals (portfolio simulation semantics, hidden defaults, determinism) — not verified against actual `vectorbt/portfolio/nb.py` source, only inferred from module/test-file names and README prose.

## 18. Unresolved questions

- Exact default-fill/fee/slippage semantics in the portfolio simulation core (needs a source read of `vectorbt/portfolio/nb.py` or equivalent).
- Actual benchmark numbers in `benchmarks/BENCHMARKS*.md` (files identified, not opened).
- Whether OSS vectorbt receives meaningfully less ongoing maintenance attention than vectorbt.pro (directional evidence only — one data point, not conclusive).
- RNG/float determinism guarantees, not checked.

## Sources

- https://github.com/polakowo/vectorbt (`gh api repos/polakowo/vectorbt`)
- https://github.com/polakowo/vectorbt/blob/master/LICENSE.md
- https://github.com/polakowo/vectorbt/commit/83cacaac (Create LICENSE.md), `/commit/71402ac8` (Commons Clause), `/commit/8c4c0cf9` (Apache 2.0), `/commit/2aceef2d` (GPLv3), `/commit/6b4b8be1` (license year fix)
- https://raw.githubusercontent.com/polakowo/vectorbt/master/README.md
- https://github.com/polakowo/vectorbt.pro (`gh api repos/polakowo/vectorbt.pro`)
- https://github.com/polakowo/vectorbt/blob/master/pyproject.toml
- https://github.com/polakowo/vectorbt/releases
- https://github.com/polakowo/vectorbt/tree/master/tests
- https://github.com/polakowo/vectorbt/tree/master/benchmarks
