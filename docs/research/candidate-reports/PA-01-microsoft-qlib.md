# Candidate Report — Microsoft Qlib

**Stream:** PA-01 (Systems & Engine Survey), also relevant to PA-04 (Search/Optimization/ML) and PA-08 (Agent/Research Automation) via its workflow layer and companion RD-Agent project.
**Status:** Seventh candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, README, direct source reads of `qlib/config.py`) — not general knowledge recall.

## 1. Repository / canonical URL

`microsoft/qlib` — https://github.com/microsoft/qlib. API description: "AI-oriented Quant investment platform... now equipped with https://github.com/microsoft/RD-Agent."

## 2. Version / commit reviewed

HEAD of `main` as of 2026-09-05. Latest tagged release `v0.9.7`, published 2025-08-15T09:49:17Z. Latest commit reviewed: `79633dd` (2026-07-23, "feat(config): add explicit validation for required configuration fields"). Repo `pushed_at`: 2026-09-02T18:23:05Z.

## 3. License — clean, no drift

MIT, verified by decoding the actual `LICENSE` blob directly ("MIT License / Copyright (c) Microsoft Corporation"). Git history on that file shows only one commit ever — the initial 2020-08-14 commit by `microsoftopensource`. No license drift, unlike several prior candidates in this program.

## 4. Maintenance / activity — genuine counter-example to the abandonment pattern

Real, active, not dormant research-ware. 139 contributors, 48,312 stars, 7,644 forks, 472 open issues, `archived: false`. Commits landing regularly through mid-2026; the reviewed commit is GPG-verified and merged via normal PR review with multiple review rounds visible. This is a genuinely maintained Microsoft repo — the maintenance-skepticism check this program requires actually comes back clean here, a real counter-example to NautilusTrader's pre-release instability, LEAN's un-tagged drift, Backtrader's abandonment, and vectorbt's single-maintainer risk.

## 5. Architecture summary

Layered design: `qlib/data` (Expression-engine-based feature/data layer with caching — `D.calendar`/`D.instruments`/`D.features`), `qlib/contrib/model` (30+ model implementations — LightGBM/XGBoost/CatBoost plus many PyTorch models: LSTM/GRU/ALSTM/Transformer/TCN/TabNet/GATS/HIST/TRA), `qlib/workflow` (experiment/recorder pipeline driven by YAML configs), `qlib/backtest` (order-execution/strategy backtest engine), `qlib/rl` (RL-based order-execution, e.g. TWAP/PPO baselines). A separate companion repo `microsoft/RD-Agent` handles LLM-driven automated factor/model R&D — the README frames it explicitly as a **separate GitHub repo**, not code living inside `qlib`. RD-Agent is out of scope for this report and needs its own independent candidate report before Signal Current relies on any of its claims.

## 6. Relevant modules

`qlib/data/{base.py,cache.py,ops.py,pit.py,dataset/,storage/}`, `qlib/contrib/model/` (model zoo above), `tests/{backtest,data_mid_layer_tests,dataset_tests,model,rolling_tests,storage_tests,test_all_pipeline.py,test_workflow.py,test_pit.py}` — all confirmed present via directory listing.

## 7. Tests and test quality

Substantial `tests/` tree with dedicated suites for pipeline, workflow, point-in-time correctness (`test_pit.py`), data-dump integrity (`test_dump_data.py`), backtest, and a `rolling_tests` directory. **`test_pit.py`'s presence is a positive signal** — Qlib explicitly tests for point-in-time/lookahead-bias correctness in its data layer, not just model accuracy. Depth/coverage percentage not independently measured this pass (would require running the suite).

## 8. Deterministic / reproducibility properties

README documents `scripts/check_data_health.py` for auditing dataset integrity (missing-data counts, large step thresholds) — a built-in "check the input before the instrument" mechanism, notable given this project's own global data-integrity rules. Workflow configs are YAML-driven (declarative, versionable). Bit-for-bit backtest reproducibility across runs was not independently verified this pass.

## 9. Asset / timeframe / venue assumptions — confirmed, not cleanly abstracted at the default layer

`qlib/config.py`, read directly, defines `REG_CN`, `REG_US`, `REG_TW` as importable region constants — multi-region *is* structurally supported. **However the default `qlib.init()` config hardcodes China**: `provider_uri: str = "~/.qlib/qlib_data/cn_data"` and `"region": REG_CN` in `_default_config`; the separate high-frequency default config also defaults to `cn_data_1min`/`REG_CN`. More significantly, the high-frequency backtest default (`min_data_shift`) is documented in-source as using "default market time [9:30, 11:29, 1:00, 2:59]" — **the Shanghai/Shenzhen exchange's midday-lunch-break trading schedule**, baked in as the unshifted default for any user running high-freq backtests without explicitly overriding `region`. README's own Quant Dataset Zoo table only lists Alpha158/Alpha360 for US and China markets — no other venues shipped.

## 10. Hidden defaults or semantic coupling — the central finding

The CN-region default in `qlib/config.py` is exactly the PROMOTED DEFAULT pattern this project's own global rules exist to catch: an engineering default (China-region data path and intraday calendar), correct for the environment it was written in, silently inherited by anyone who calls `qlib.init()` without explicit `region=REG_US`. A Signal Current integrator who misses that override gets silent CN data-path conventions and a CN intraday trading calendar — no error, no warning found in this pass. If Qlib is ever adopted even partially, this must become an explicit, checked override, never left implicit.

## 11. Performance characteristics

README benchmark tables reference published IC/backtest metrics on Alpha158/Alpha360 + CSI300/CSI500 datasets for the shipped models — these are Qlib's own reported numbers, not independently reproduced in this pass. Treat as vendor-reported until re-run.

## 12. What Signal Current could reuse

- The declarative YAML workflow/experiment-recorder pattern (`qlib/workflow`) as a design reference for config-driven, reproducible pipeline definitions.
- The `qlib/data` Expression-engine idea (features as composable operator expressions, e.g. `Ref($close,1)`, `Mean($close,3)`) as a pattern for a typed factor-expression layer.
- `test_pit.py`'s point-in-time testing discipline as a parity/golden-test template.
- RD-Agent as a reference for automated-research-agent design — but it is a separate repo/product with its own maturity/license profile requiring its own independent candidate report before any reliance; do not fold RD-Agent claims into this Qlib disposition.

## 13. What Signal Current should not inherit

The CN-region-as-silent-default posture in `config.py`; the implicit CN lunch-break trading-hour assumption baked into `min_data_shift`'s documented default; tight coupling of the model zoo to the PyTorch/LightGBM/XGBoost dependency stack if Signal Current wants a lighter core.

## 14. Integration / coupling risks

Adopting Qlib's data layer wholesale imports its region-default assumptions transitively (§9/§10) — any wrapper must force explicit region/calendar configuration at every entry point and add a fail-closed assertion, per this project's own assert-convention practice. Qlib's binary/columnar on-disk data format (`qlib/data/storage`) is a proprietary structure requiring its own `dump_data`/`check_data_health` tooling — a real ingestion-pipeline dependency, not a drop-in.

## 15. Required parity / golden tests if used as reference

- A region-default assertion test — confirm a Signal Current wrapper never silently falls back to `REG_CN`/`cn_data`.
- A PIT/lookahead golden test modeled on `test_pit.py`, run against Signal Current's own venue/timeframe data, not Qlib's CN sample data.
- A data-health parity check (missing-bar/step-size distribution) using Signal Current's own thresholds, not Qlib's CN-tuned defaults for `missing_data_num`/`large_step_threshold_*` shown in the README example.

## 16. Proposed disposition

**REFERENCE** (architecture/pattern reference only) — not ADOPT/WRAP. Qlib is real, actively maintained, MIT-licensed, non-abandoned — the maintenance check comes back clean, a genuine counter-example to the other six candidates. But its data layer, defaults, and on-disk format are structurally CN/US-equity-daily-centric (explicit CN default config, CN trading-hour default), and its dependency footprint (PyTorch/LightGBM/XGBoost, proprietary binary data store) is heavy for a "wrap and reuse" strategy. Best use: a studied architecture/pattern reference for the workflow-config and expression-engine ideas, and a CONFORMANCE COMPARATOR candidate specifically for point-in-time-correctness testing methodology — not infrastructure Signal Current runs directly.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on license, maintenance activity, and the CN-default coupling — all directly read from primary sources. Medium confidence on performance and reproducibility claims (Qlib's own reported benchmarks, not independently re-run), and on test-suite quality (existence confirmed, depth/pass-rate not measured).

## 18. Unresolved questions

- Whether Qlib's reported IC/backtest numbers reproduce on a clean run — not verified.
- Actual test coverage percentage and CI pass-rate history — not pulled.
- RD-Agent's own license/maintenance/architecture profile — explicitly out of scope for this Qlib-only report and needs its own candidate report before Signal Current relies on it.
- Licensing/terms attached to the *data* Qlib ships or downloads (Yahoo/CN vendor data) versus the MIT code license — README references a Yahoo collector script but data-licensing terms were not independently checked this pass.

## Sources

- https://api.github.com/repos/microsoft/qlib
- https://github.com/microsoft/qlib/releases
- https://github.com/microsoft/qlib/blob/main/LICENSE
- https://api.github.com/repos/microsoft/qlib/commits?path=LICENSE
- https://api.github.com/repos/microsoft/qlib/contributors
- https://github.com/microsoft/qlib/blob/main/README.md
- https://github.com/microsoft/qlib/blob/main/qlib/config.py
- https://github.com/microsoft/qlib/tree/main/tests
- https://github.com/microsoft/qlib/tree/main/examples/benchmarks/LightGBM
- https://github.com/microsoft/RD-Agent (referenced only, not independently audited this pass)
