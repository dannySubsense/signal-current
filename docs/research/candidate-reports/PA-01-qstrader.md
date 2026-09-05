# Candidate Report — QSTrader

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Tenth candidate report for the prior-art research program — completes the original PA-01 engine survey candidate list.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, README, `settings.py`, directory tree, release notes) — not general knowledge recall.

## 1. Repository / canonical URL

`mhallsmoore/qstrader` — https://github.com/mhallsmoore/qstrader, a QuantStart.com project.

## 2. Version / commit reviewed

Latest tag `v0.3.0`, commit `4c59e1584e83fcc2be644b820f827c0dd1b45c02`, released 2024-06-24, on `master` (default branch).

## 3. License — clean, unambiguous

MIT, "Copyright (c) 2015-2024 QuantStart.com, QuarkGluon Ltd" — read directly from the `LICENSE` file and cross-confirmed in README's "License Terms" section. Repo metadata license field agrees (`mit`/SPDX `MIT`). No drift between file and metadata.

## 4. Maintenance / activity — dormant, not mid-build abandonment

`pushed_at: 2024-06-30T17:11:05Z` per repo metadata (queried 2026-09-05) — **~26 months since last push, no commits or releases since v0.3.0.** 14 total contributors, heavily concentrated: creator `mhallsmoore` (241 commits) and one later maintainer `juliettejames` (13 commits, author of all releases from v0.2.3 onward). 18 open issues, several stale — including one flagging that the README's own CI/coverage badges (Travis CI/Coveralls) are broken/dead links.

**Assessment: dormant, not actively maintained, but not abandoned mid-build.** It reached a stable, intentionally-versioned stopping point — v0.3.0 was a deliberate numpy 2.0 migration, a "finishing touch" commit — then activity ceased. This differs from Backtrader's pattern (disabled issue tracker, unreviewed PR backlog): here the project looks feature-complete for its stated scope rather than actively decaying. Not independently verified: whether `juliettejames` still responds to issues today.

## 5. Architecture summary

README states directly: a "schedule-driven" (not tick/order-book-driven) backtesting framework for "long-short equities and ETF" systematic trading strategies, explicitly designed as "a loosely-coupled collection of modules... intent... for users to extend, inherit or fully replace each module." Confirmed by directory structure: `qstrader/{alpha_model, asset, broker, data, exchange, execution, portcon, risk_model, signals, simulation, statistics, system, trading, utils}`. This is a genuine separation of concerns — alpha/signal generation decoupled from portfolio construction (`portcon`), risk (`risk_model`), execution (`execution`), and simulated brokerage/accounting (`broker/simulated_broker.py`). `qstrader/system/qts.py` (`BacktestTradingSession`) is the orchestration entry point; `qstrader/system/rebalance/{buy_and_hold,daily,end_of_month,weekly}.py` define schedule cadence — a **calendar-scheduled rebalance loop**, not an arbitrary event-driven engine with tick-level order-book simulation.

## 6. Relevant modules

`broker/` (abstract base, simulated_broker.py, fee_model/, portfolio/, transaction/ — cash/fee/position accounting), `data/` (backtest_data_handler.py, daily_bar_csv.py — only two data-source implementations exist), `portcon/` (portfolio construction/allocation), `risk_model/`, `alpha_model/`, `execution/`, `asset/` (asset.py, cash.py, equity.py, universe/), `system/rebalance/` (calendar schedule strategies), `statistics/tearsheet.py`.

## 7. Tests and test quality

30 test files under `tests/{unit,integration}/`. Release notes show real, not decorative, test discipline — e.g. v0.2.8: "Adds an integration test to check that target allocations match the expected output, including a date index"; v0.2.7: "Adds a unit test to check that the business day calculation is correct." README claims "a suite of unit and integration tests for the majority of its modules" — consistent with what's observed. CI is via `.travis.yml` + Coveralls, but those badges are reported broken by an open issue, so CI/coverage reporting is likely dead even though the tests exist in-repo. Current pass/fail status was not run this pass — unverified.

## 8. Deterministic / reproducibility properties

CSV-file-driven design (`daily_bar_csv.py`) with explicit date-indexed backtest sessions (`BacktestTradingSession`, `burn_in_dt`), inherently deterministic given fixed input data — no live-data-dependent randomness observed in the module list. This is an architectural inference from module names/README, not a runtime-confirmed property (no backtest was actually run twice to verify).

## 9. Asset / timeframe / venue assumptions — reputation confirmed accurate, not myth

README states explicitly: "long-short equities and ETF based systematic trading strategies." `qstrader/settings.py`, read directly, hardcodes `CURRENCIES: ['USD', 'GBP', 'EUR']` and registers only `ZeroFeeModel` as the single fee model in `SUPPORTED`. Only one bar-data source exists (`daily_bar_csv.py`) — **daily bar frequency only, no intraday/tick support, no live broker/exchange adapters** (`exchange/simulated_exchange.py` is the only exchange implementation). This confirms the "minimal/educational, equities/ETF daily-bar backtester" reputation is accurate — verified via `settings.py` and directory listing, not assumed from reputation alone.

## 10. Hidden defaults or semantic coupling

`qstrader/settings.py` has a module-level global mutable flag `PRINT_EVENTS`, toggled via `set_print_events()` — global mutable state for logging control, a code smell for anything needing thread-safety or parallel backtests. The currency whitelist (`USD/GBP/EUR`) in `settings.SUPPORTED` is a promoted-default-style hardcoded list that would silently need extension for other currencies — exactly the kind of unexamined constant this project's own global rules flag as needing a citation or PROVISIONAL tag; here it has neither. `LOGGING.DATE_FORMAT` is similarly a hardcoded global.

## 11. Performance characteristics

No benchmark numbers, profiling, or performance claims found anywhere in README, CHANGELOG, or release notes. Given the pandas/CSV-based daily-bar architecture, it is not designed or marketed for large-universe or high-frequency use — inferred from architecture, not measured.

## 12. Dependencies / stack

From release notes: numpy (>=2.0 as of v0.3.0, prior versions pinned <2.0), pandas 2.2.0, matplotlib 3.8.2, seaborn 0.13.2, click >=8.1, build via Hatchling (switched from setuptools in v0.2.5). Python 3.9–3.12 supported as of v0.2.4. All current as of the 2024 freeze point, but will drift further as the ecosystem moves without a maintainer pushing updates.

## 13. What Signal Current could reuse

The module boundary set itself — alpha_model / portcon / risk_model / execution / broker-as-accounting-ledger / rebalance-schedule-as-first-class-object — is a clean, legible reference architecture worth studying even though the code is small: a genuinely well-separated design for schedule-rebalanced multi-asset equity/ETF portfolios. Not much code is reusable as-is given the single-data-source, daily-only, no-live-adapter scope.

## 14. What Signal Current should not inherit

The global mutable `PRINT_EVENTS` logging switch; the hardcoded currency whitelist in `settings.py`; the CSV-only/daily-bar-only data layer as an architectural ceiling; the "loosely coupled, meant to be inherited/overridden" philosophy taken to the extreme of no live-broker adapters — this pushes all production-readiness work onto any adopter with zero reference implementation to learn from for the live-trading path Signal Current needs.

## 15. Integration / coupling risks

None if used purely as a design reference (no runtime dependency). If actually imported as a library: dormant since mid-2024 means any bug found becomes Signal Current's to fix in a fork, with no expectation of merge back. The numpy 2.0 pin in v0.3.0 vs. the current (2026) numpy/pandas ecosystem needs a compatibility check before any real import — untested by this research.

## 16. Required parity / golden tests if used as reference

Not applicable at ADOPT/FORK/WRAP tier — REFERENCE only, so no golden-test parity harness needed now. If Signal Current later borrows the rebalance-schedule abstraction pattern, a golden test would be: given identical CSV bar data and rebalance dates, QSTrader's `BacktestTradingSession` output equity curve reproduced bit-for-bit by Signal Current's equivalent module — speculative future work, not a current requirement.

## 17. Proposed disposition

**REFERENCE.** Not ADOPT/FORK/WRAP/ADAPT (dormant 26 months, single-data-source, no live-trading adapters, too narrow in scope for a production quant research system spanning multiple asset classes/venues). Not REJECT either — the module separation is genuinely instructive, and the MIT license imposes zero constraint on studying or lifting small patterns (e.g., the `rebalance/` schedule-strategy pattern, or the alpha/portcon/risk/execution boundary). Treat it as a documented architectural precedent to consult during `02-ARCHITECTURE.md` drafting, not a dependency or codebase to build on.

## 18. Confidence level

**MEDIUM-HIGH.** High confidence on license, maintenance timeline, module structure, and asset/timeframe scope — all read directly from primary sources. Lower confidence on: current test-suite pass/fail state (not executed), actual runtime determinism (inferred from design, not run), fee-model completeness beyond `settings.py`'s single registered entry, and CI/coverage health beyond the one open "broken badge" issue.

## 19. Unresolved questions

- Does `broker/fee_model/` contain more fee models than the one (`ZeroFeeModel`) registered in `settings.SUPPORTED`, and if so why is `settings.py` not kept in sync — possible undocumented feature/config drift.
- Does the test suite still pass on current numpy/pandas given the ecosystem has moved since June 2024?
- Is `juliettejames` (sole recent-era committer) still responsive to issues, or is the project truly unmaintained going forward — issue response latency not checked.
- Whether QuantStart's separate "Advanced Algorithmic Trading" branch (referenced in README) represents a materially different/older architecture worth a second look — out of scope for this pass.

## Sources

- https://github.com/mhallsmoore/qstrader
- https://api.github.com/repos/mhallsmoore/qstrader
- https://github.com/mhallsmoore/qstrader/blob/master/LICENSE
- https://github.com/mhallsmoore/qstrader/blob/master/README.md
- https://github.com/mhallsmoore/qstrader/releases (v0.2.3 through v0.3.0 release notes)
- https://github.com/mhallsmoore/qstrader/blob/master/qstrader/settings.py
- https://github.com/mhallsmoore/qstrader/tree/master/qstrader
- https://github.com/mhallsmoore/qstrader/tree/master/tests
- https://github.com/mhallsmoore/qstrader/issues
- https://github.com/mhallsmoore/qstrader/tags and /commits
