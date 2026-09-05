# PA-10 — Reuse Decision Register

**Status:** Empty working register

This file records decisions produced by the Prior Art & Reuse Research Program.

| ID | Candidate | Area | Version/Commit | License | Disposition | Signal Current Role | Blocking Questions | ADR / Spec Impact | Status |
|---|---|---|---|---|---|---|---|---|---|
| PA-01-01 | NautilusTrader | Engine/runtime | commit ac22d5c (develop, 2026-09-05); v2.0.0rc4 latest tag | LGPL-3.0 (was GPLv3 until 2020-06-10, commit 1b1aa12) | REFERENCE + PARITY ORACLE candidate (pending legal LGPL review); NOT adopt/fork/wrap while v2 is pre-release | DST (deterministic simulation testing) pattern as design reference; shared-kernel backtest/live parity pattern as architecture precedent | LGPL §3-6 obligations for dynamic-dependency vs adapted-code reuse (needs human legal read); v1→v2 migration instability | Architecture (DST pattern, parity-oracle design) | Research — see full report: `docs/research/candidate-reports/PA-01-nautilustrader.md` |
| PA-01-02 | QuantConnect LEAN | Engine/runtime | commit 23b735d (master, 2026-09-04); no tagged release since v2.4.0.1 (2017) | Apache-2.0 (stable since >=2015, one unresolved 2015-07-10 LICENSE commit not diffed) | REFERENCE only — architecture/determinism/asset-neutrality/coupling NOT yet verified against source | Modular plug-in taxonomy (engine/brokerage/data-provider/indicator separation) as architecture reference only | Engine/, Common/, Data/ source unread this pass — determinism, asset-neutrality, and QuantConnect-cloud coupling all unresolved; do not cite as architecture precedent until read | Architecture (pending follow-up read) | Research — see full report: `docs/research/candidate-reports/PA-01-quantconnect-lean.md` |
| TBD | vectorbt | Exploration | TBD | TBD-verify | REFERENCE / benchmark | High-throughput exploration | License, event parity | Methodology/Architecture | Research |
| TBD | Microsoft Qlib | ML/research | TBD | TBD-verify | REFERENCE / investigate ADAPT | ML pipeline and research workflow | Market assumptions, data semantics | Methodology/Agent | Research |
| TBD | skfolio | Validation/portfolio | TBD | TBD-verify | investigate ADOPT/ADAPT | CPCV, HRP, NCO, portfolio validation | Mathematical parity | Validation/Portfolio | Research |
| TBD | TA-Lib | Indicators | TBD | TBD-verify | investigate ADOPT | Standard indicator numerics | Convention parity/versioning | StrategyIR | Research |

**Rule:** no row advances to Decided until repository version, exact license, semantics, tests, and Signal Current conformance plan have been reviewed.
