# Candidate Report — Zipline-reloaded

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Sixth candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, README, source files, GitHub issue search) — not general knowledge recall.

## 1. Repository / canonical URL / relationship to original Zipline

`stefan-jansen/zipline-reloaded` — https://github.com/stefan-jansen/zipline-reloaded. GitHub API metadata confirms `"fork": true`, with `parent`/`source` both pointing to `quantopian/zipline` — a genuine GitHub fork, not an independent rewrite. Maintained by Stefan Jansen (author of *Machine Learning for Algorithmic Trading*), not Quantopian: README states directly, "Since it closed late 2020... Stefan Jansen... is trying to keep the library up to date." Quantopian the company dissolved in 2020 (confirmed via a README-linked Boston Business Journal article); `quantopian/zipline`'s own `pushed_at: 2024-02-13` is almost certainly a stale merge/sync artifact, not active upstream maintenance.

## 2. Version / commit reviewed

Latest GitHub Release `3.1.1`, published 2025-07-23. Repo `pushed_at: 2026-01-06`; default branch `main`; 30 contributors.

## 3. License — clean, no drift

Apache-2.0, confirmed via GitHub's SPDX detection (`apache-2.0`) and by reading the actual `LICENSE` file content directly. Commit history on `LICENSE`: only two commits ever — the original 2012 addition and a 2018 "add year/name to license" edit, both pre-fork, inherited unchanged from `quantopian/zipline`. No license change post-fork.

## 4. Maintenance / activity — genuinely alive, not a zombie repo

Materially different from the Backtrader pattern in this program. Recent merged PRs as late as 2025-11-13 (dependency bumps), and active open PRs dated Feb–Aug 2026 fixing real correctness bugs ("Fix DataPortal correctness bugs," "Fix infinite loops and data corruption in data layer," "BUG: Avoid duplicate bundle paths"). 1,933 stars, 330 forks, 44 open issues, 30 contributors. But the open-PR backlog — several unmerged bug-fix PRs sitting since February 2026 — suggests a thin single-maintainer bottleneck (Stefan Jansen as sole listed release author), a real bus-factor risk even though the project is alive.

## 5. Architecture summary

Event-driven backtester with a documented Pipeline API (`src/zipline/pipeline/` — `engine.py`, `graph.py`, `term.py`, `domain.py`, `factors/`, `classifiers/`, `filters/`, `loaders/`) for cross-sectional factor computation, a bundle-based ingestion model (`src/zipline/data/bundles/` — `core.py`, `csvdir.py`, `quandl.py`), and simulation/execution machinery (`finance/blotter/`, `finance/ledger.py`, `finance/metrics/`, `finance/slippage.py`, `finance/commission.py`).

## 6. Relevant modules

`pipeline/` (factor/classifier/filter engine), `data/bundles/` (ingestion), `algorithm.py`/`api.py` (user-facing strategy API), `finance/blotter/` + `finance/ledger.py` (order/fill simulation). An `assets/` module (`assets.py`, `futures.py`, `continuous_futures.pyx`, `exchange_info.py`) does support futures and continuous-futures rolls — the asset model is not literally equities-only at the type level.

## 7. Tests and test quality

Substantial suite: dedicated directories for `pipeline`, `finance`, `history`, `metrics`, `events`, `data`, plus `test_algorithm.py`, `test_blotter.py`, `test_clock.py`, `test_data_portal.py`, `test_tradesimulation.py`, and more. CI badges show `ci_tests_full.yml` and `build_wheels.yml` GitHub Actions plus Codecov integration — active CI, not vestigial.

## 8. Deterministic / reproducibility properties — unresolved

No direct evidence gathered on seeded-RNG reproducibility guarantees; would require running the test suite/a backtest, out of scope for a static research pass.

## 9. Asset / timeframe / venue assumptions — central finding: not meaningfully generalized

The fork has **not** meaningfully generalized away from Quantopian's US-equities/daily-or-minute-bar origins. Direct evidence, read from source:

- `data/bundles/core.py` hardcodes `minutes_per_day=390` (NYSE session length) as a default parameter, and literally writes bar-storage files named `daily_equities.bcolz` / `minute_equities.bcolz`.
- `utils/calendar_utils.py`'s `get_calendar()` wrapper special-cases exactly `["us_futures", "CMES", "XNYS", "NYSE"]` for `side="right"` calendar behavior — everything else falls through a generic path, meaning US markets get bespoke calendar treatment other venues don't.
- An **open, unresolved GitHub issue** (found via issue search): "Ingesting data for crypto assets yields UserWarning: Ignoring values because they are out of bounds for uint32" — confirms non-equity/crypto ingestion hits internal numeric-format assumptions (likely a price/volume dtype sized for US-equity price ranges) that maintainers have not fixed.

Futures support exists structurally (`continuous_futures.pyx`, `roll_finder.py`), but crypto/forex are second-class and actively buggy — this is a live, verified bug, not a speculative concern.

## 10. Hidden defaults or semantic coupling

`minutes_per_day=390` is a promoted-default risk of exactly the pattern this project's own global rules warn about — an engineering constant (NYSE's 9:30–4:00 session) baked in as a bundle-writer default that would silently mis-model any non-NYSE-session asset unless explicitly overridden. Bcolz storage format itself (`bcolz_daily_bars.py`, `bcolz_minute_bars.py`) is a legacy dependency whose own maintenance status was not independently verified this pass — flagged, not resolved.

## 11. Performance characteristics

No primary-source benchmark data fetched this pass — unresolved.

## 12. What Signal Current could reuse

The Pipeline API's factor/classifier/filter abstraction as a conceptual reference for cross-sectional research computation; the bundle-ingestion pattern as a reference architecture (not code) for pluggable data sources; the blotter/ledger separation as a reference for order-simulation design.

## 13. What Signal Current should not inherit

The bcolz-based daily/minute bar storage format; the `minutes_per_day=390`/NYSE-session hardcoding; the equities-first calendar special-casing; the crypto/forex ingestion path (actively buggy, unowned).

## 14. Integration / coupling risks

`exchange_calendars` and `bcolz` are both external dependencies with their own maintenance trajectories not verified here. Single-maintainer bottleneck (Stefan Jansen) is a bus-factor risk for any long-term dependence.

## 15. Required parity / golden tests if used as reference

Golden-output comparison against zipline-reloaded's own `tests/finance` and `tests/history` fixtures for equity daily/minute backtests only (its area of actual strength). **Do not use it as a parity oracle for any non-US-equity asset class** given the open, unfixed crypto ingestion bug.

## 16. Proposed disposition

**REFERENCE only** — not ADOPT/WRAP/FORK. It legitimately clears the license and maintenance bars that sank Backtrader (Apache-2.0, clean history, genuinely active development). But its core semantics remain hardwired to US-equity daily/minute bars in ways that would require deep surgery to generalize, and its crypto path is demonstrably broken and unfixed. Use as an architectural/conceptual reference for the Pipeline and bundle patterns; do not adopt, fork, or wrap it as Signal Current's execution engine, given Signal Current's explicit "no privileged market dimension" invariant.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on fork relationship, license, maintenance activity, and the US-equities-centric findings — all read directly from primary sources (GitHub API metadata, LICENSE file content, README, source files `core.py`/`calendar_utils.py`, and a live GitHub issue). Lower confidence on performance and determinism claims, not independently checked this pass.

## 18. Unresolved questions

- bcolz project's own maintenance status — not checked.
- Actual reproducibility/determinism behavior — requires running the test suite, not just reading source.
- Severity/scope of the open bug backlog (several correctness-bug PRs unmerged since Feb 2026) — worth checking whether these are stalled due to reviewer-bandwidth bus-factor risk before any reliance decision.

## Sources

- https://github.com/stefan-jansen/zipline-reloaded (`gh api repos/stefan-jansen/zipline-reloaded`)
- https://github.com/stefan-jansen/zipline-reloaded/blob/main/LICENSE
- https://github.com/stefan-jansen/zipline-reloaded/commits/main/LICENSE
- https://github.com/stefan-jansen/zipline-reloaded/blob/main/README.md
- https://github.com/stefan-jansen/zipline-reloaded/releases (latest: 3.1.1, 2025-07-23)
- https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/data/bundles/core.py
- https://github.com/stefan-jansen/zipline-reloaded/blob/main/src/zipline/utils/calendar_utils.py
- https://github.com/stefan-jansen/zipline-reloaded/pulls (open PRs, Feb–Aug 2026)
- GitHub issue search: crypto ingestion `uint32` warning, `repo:stefan-jansen/zipline-reloaded`
- https://github.com/quantopian/zipline (parent repo metadata)
