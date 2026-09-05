# Candidate Report — vn.py

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Eighth candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, `pyproject.toml`, direct reads of `vnpy/trader/gateway.py` and `vnpy/event/engine.py`, CI workflow, release notes) — not general knowledge recall.

## 1. Repository / canonical URL

`vnpy/vnpy` — https://github.com/vnpy/vnpy. Own repo description (Chinese): "Python-based open-source quant trading platform framework." Homepage `www.vnpy.com`.

## 2. Version / commit reviewed

`master` at HEAD (`pushed_at` 2026-09-01T01:47:09Z; last commit 2026-08-06T14:30:12Z). Latest tagged release: `4.4.0`, published 2026-05-14T12:18:00Z. Note: the repo's `tags` endpoint returns old `v2.1.x` tags from 2015–2019 — the project changed its tag-naming convention over time (no `v` prefix on later releases); do not infer stagnation from the raw tags list without cross-checking `releases`, which shows current activity.

## 3. License — clean, no drift

MIT, read directly from the `LICENSE` blob: "The MIT License (MIT) Copyright (c) 2015-present, Xiaoyou Chen." Confirmed consistent via GitHub's license API (`spdx_id: MIT`) and `pyproject.toml` (`license = {text = "MIT"}`). Git history on `LICENSE` shows only 4 commits, oldest 2015-03-17. One repo-wide history-squash event occurred 2018-12-28→2019-01-04 ("clear all old files" / "Add license file") — this looks like a history rewrite/reset, not a license-content change; MIT text has been stable since. No license drift found.

## 4. Maintenance / activity

Very active: 45,140 stars, 12,435 forks, 128 contributors, 16 open issues / 18 open PRs, created 2015-03-02, still pushed to as of 2026-09-01. CI (`.github/workflows/pythonapp.yml`, read directly) runs `ruff` lint + `mypy` type-check + build on every push/PR to `master`/`dev` — an active, real CI gate, not a stale badge. Satellite gateway repos (`vnpy_ctp`, `vnpy_ctastrategy`, `vnpy_portfoliostrategy`) all pushed within the last ~4 months, also MIT-licensed, none archived.

## 5. Architecture summary

Core `vnpy` package: `event` (generic pub/sub `EventEngine`, 145 lines, no broker-specific code — read in full), `trader` (engine, gateway ABC, object/constant/converter/database/datafeed abstractions, Qt-based `ui`), `alpha` (ML/factor research module using polars/scikit-learn/lightgbm/torch — optional extra), `chart`, `rpc`. Strategy engines (CTA, portfolio, spread, script trading), backtesting, and every broker/data-vendor connector live in **separate `vnpy_*` repos** installed as plugins, not in core. Core targets Windows/Linux/macOS per `pyproject.toml` classifiers, Python ≥3.10.

## 6. Relevant modules

`vnpy/event/engine.py` — generic threaded event loop, timer + handler dispatch, broker-agnostic, verified by direct full read. `vnpy/trader/gateway.py`'s `BaseGateway` (read in full) is an `ABC` with only generic trading primitives (`TickData`, `OrderData`, `OrderRequest`, etc.) — **zero CTP-specific imports or fields in the abstract base**. CTA strategy engine and backtester live in the separate `vnpy_ctastrategy` and `vnpy_ctabacktester` repos — only directory-listed, not source-read this pass; treat their internals as unverified pending a deeper read if pursued further.

## 7. Tests and test quality — real gap

The core repo's `tests/` directory contains exactly one file, `test_alpha101.py`, plus an `alpha` subfolder — test coverage in the core repo is narrow and scoped almost entirely to the `alpha` factor module. No visible unit tests for `EventEngine`, `BaseGateway`, or the trading-object layer. This is confirmed by direct directory listing, not an assumption.

## 8. Deterministic / reproducibility properties — unresolved

Not independently verified — would require running the backtester (a separate repo) against a fixed dataset, out of scope this pass.

## 9. Asset / venue coupling depth — the load-bearing question, answered cleanly

Coupling is **cleanly isolated**, not baked into core. Evidence:

- `BaseGateway`'s abstract class (`vnpy/trader/gateway.py`) has no CTP-specific types or logic — verified by direct full read.
- CTP support ships as its own repo, `vnpy/vnpy_ctp`, a peer of ~90 other similarly-structured `vnpy_*` gateway/datafeed/database repos (`vnpy_ib`, `vnpy_xtp`, `vnpy_tap`, `vnpy_polygon`, `vnpy_rqdata`, and more — full list enumerated via the GitHub org's repo listing).
- The 4.4.0 release notes describe simultaneous, symmetric upgrades across `vnpy_ctp`, `vnpy_esunny`, `vnpy_tora`, `vnpy_rohon`, `vnpy_tts` gateways with no core-repo changes required — evidence the plugin boundary actually holds in practice, not just in the ABC's shape.

**Conclusion: CTP/China-futures coupling is at the adapter layer only; the core event engine and strategy interface are exchange/venue-agnostic by construction.** This is the strongest clean-separation result of any candidate examined so far in this program — a real, validated precedent, not a marketing claim.

## 10. Hidden defaults or semantic coupling

None found in what was read (`BaseGateway`, `event/engine.py`, `pyproject.toml`). One caveat: `Operating System :: Microsoft :: Windows` is listed first in classifiers and CI runs on `windows-latest` only — suggests Windows may be the primary-tested platform even though Linux/macOS are declared supported. A maintenance-quality signal (single-OS CI), not a functional coupling, but worth flagging for a research system likely to run on Linux.

## 11. Performance characteristics

No benchmark data found in README/CHANGELOG this pass.

## 12. What Signal Current could reuse

The gateway/adapter isolation pattern itself — abstract `BaseGateway` + generic event bus + satellite per-venue packages — is a validated real-world precedent for adapter-isolated venue coupling, directly answering the architectural question this report set out to check. Useful as a reference pattern, less so as runnable code, given Signal Current is research/backtest-first while vn.py's core is live-trading/UI-first (PySide6 GUI, Qt event loop dependencies baked into core `trader/ui`).

## 13. What Signal Current should not inherit

The Qt/PySide6 GUI dependency baked into core `trader` package (a heavy, non-headless dependency for a backtest/research-first system); the thin core test coverage (only the `alpha` module tested) — do not treat vn.py's test suite as any kind of golden reference; the CI's single-OS (Windows) coverage.

## 14. Integration / coupling risks

If adopted only for the `alpha` module or gateway pattern, the dependency surface still pulls in `PySide6`, `pyqtgraph`, `qdarkstyle` transitively unless carefully subset — a real packaging risk. The `ta-lib` dependency requires a custom PyPI index (`https://pypi.vnpy.com`) per the CI script, not standard PyPI — a supply-chain detail worth noting before adopting anything.

## 15. Required parity / golden tests if used as reference

Given the core repo has essentially no tests outside `alpha`, any code borrowed from `vnpy_ctastrategy`/`vnpy_ctabacktester`/the core event engine would need Signal Current to write its own golden/parity tests from scratch — none exist upstream to reuse or diff against.

## 16. Proposed disposition

**REFERENCE.** Not ADOPT/FORK/WRAP — GUI-coupled core and thin core test coverage make direct integration costly. Not REJECT — the gateway/adapter isolation pattern is genuinely well-evidenced (90+ peer venue repos sharing one abstract base, verified via direct source read) and directly relevant precedent for how Signal Current should isolate its own future broker/data-vendor adapters. Use as an architecture-pattern reference, not a code dependency.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on license, maintenance activity, and core-vs-gateway architectural separation — all read from primary sources (LICENSE blob, `pyproject.toml`, `gateway.py`, `event/engine.py`, CI workflow, release notes, repo/contributor metadata). Lower confidence on backtester internals, determinism, and performance — those live in unread separate repos.

## 18. Unresolved questions

- Internal quality/determinism of `vnpy_ctastrategy`/`vnpy_ctabacktester` — not read at source level, only listed.
- Whether the "clear all old files" 2018-12-28 history-squash event altered anything beyond LICENSE (worth a broader `git log --follow` diff if pursued further).
- Actual macOS/Linux test coverage given CI is Windows-only.
- No direct evidence of documented determinism/seeding guarantees in the backtester.

## Sources

- https://github.com/vnpy/vnpy
- https://github.com/vnpy/vnpy/blob/master/LICENSE
- https://github.com/vnpy/vnpy/blob/master/pyproject.toml
- https://github.com/vnpy/vnpy/releases/tag/4.4.0
- https://github.com/vnpy/vnpy/blob/master/.github/workflows/pythonapp.yml
- https://github.com/vnpy/vnpy/blob/master/vnpy/trader/gateway.py
- https://github.com/vnpy/vnpy/blob/master/vnpy/event/engine.py
- GitHub REST API: `/repos/vnpy/vnpy`, `/repos/vnpy/vnpy/contributors`, `/repos/vnpy/vnpy/commits`, `/users/vnpy/repos`, `/repos/vnpy/vnpy_ctp`, `/repos/vnpy/vnpy_ctastrategy`
