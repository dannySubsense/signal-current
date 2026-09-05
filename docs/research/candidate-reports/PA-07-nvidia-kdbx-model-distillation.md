# Candidate Report — NVIDIA KDB-X Model Distillation (NVIDIA-AI-Blueprints + KxSystems fork)

**Stream:** PA-07 (Provenance/Experiment/Workflow Infrastructure), also relevant to PA-03 (Data/Time/Instrument Semantics) via the as-of-join pattern.
**Status:** Second candidate report for PA-07-adjacent work.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE files at both repos, direct source read of `enrichment.py`, KX's own licensing/product pages, NVIDIA's blog) — not general knowledge recall.

## Ambiguity resolved: two distinct real repos exist

The local capture's URL (`github.com/KxSystems/nvidia-kx-samples/tree/main/ai-model-distillation-for-financial-data`) and NVIDIA's own canonical repo (`github.com/NVIDIA-AI-Blueprints/ai-model-distillation-for-financial-data`) are **two different repos with different architectures**, both real, both fetched directly:

- **`NVIDIA-AI-Blueprints/ai-model-distillation-for-financial-data`** (created 2025-11-07, 78 stars, 38 forks, Apache-2.0) — NVIDIA's canonical "Developer Example." Per its own `ARCHITECTURE.md`, its backing stack is **MongoDB + Elasticsearch + Redis + Celery**, with **no KDB-X anywhere**.
- **`KxSystems/nvidia-kx-samples`** (created 2024-12-04, 99 stars, Apache-2.0) — **KX Systems' own fork/adaptation** of the NVIDIA blueprint, adding a `kdbx/` module (`connection.py`, `enrichment.py`, `market_tables.py`, `backtest.py`, `es_adapter.py`, `signals.py`) and a marketing doc replacing MongoDB+Elasticsearch with KDB-X. **This is real code, not vaporware** — `enrichment.py` was read directly.

Both are "developer example"/blueprint-style reference implementations, not production libraries — explicitly framed by NVIDIA as illustrative.

## 1. Repository / project / nature

See above.

## 2. Version / commit reviewed

NVIDIA repo HEAD at fetch: commit `6422dad2` (2026-05-25). No git tags/releases surfaced. KX repo: default branch `main`, `enrichment.py` header dated "Copyright (c) 2026 KX Systems, Inc." — no pinned SHA captured; treat as "current main, fetched 2026-09-05."

## 3. License — the same pattern as the earlier cuOpt report: Apache code, proprietary-EULA runtime

Both repos' root `LICENSE` file = Apache License 2.0 (confirmed by reading the raw file content in both repos). The KX `enrichment.py` file carries an SPDX header confirming Apache-2.0 at the file level too. **However**, the code depends on `pykx` (KX's Python↔q bridge) which requires a **KDB-X license separate from the Apache-2.0 code license**: KX's own licensing page states the Community Edition is free for personal/commercial use but capped at 16GB RAM, 8 connections, 4 secondary threads, 24 CPU cores total, single-instance only, and explicitly forbids circumventing the cap via GPU/disk-tranche tricks. **This is exactly the pattern flagged in the earlier cuOpt candidate report (`PA-05-nvidia-cuopt-portfolio.md`): Apache code, proprietary-EULA runtime dependency, worth watching for across every NVIDIA blueprint candidate in this program.**

## 4. Maintenance / activity

NVIDIA repo: last push 2026-05-25, most recent commits are CI/GPU-runner plumbing — active, actively-run CI as of ~3.5 months before this research. Only 1 open issue. KX repo: pushed 2026-07-06, more recent than NVIDIA's copy — KX is actively iterating on its fork independent of upstream.

## 5. Architecture — what's real vs. rebranded

- **KDB-X is confirmed a real, distinct, current KX Systems product**, not NVIDIA's invention and not an open-source community variant of kdb+ in the FOSS sense — it's KX's "next-gen kdb+" with a free-but-capped Community Edition.
- **As-of join** here is the standard KX `aj` operator, confirmed by reading the actual q code in `enrichment.py`: `aj[`sym`timestamp; lookup; select ... from market_ticks]` — looks up, for each `(sym, timestamp)` key, the most recent row at-or-before that timestamp. **This is a legitimate, well-established point-in-time join pattern (not novel to NVIDIA or KX)** — a "backward join on time" that avoids look-ahead bias.
- **NeMo's role**: distillation is done entirely by NVIDIA NeMo Microservices (Customizer for LoRA fine-tuning, Evaluator for F1 scoring, NIM for serving). **KDB-X plays no role in the distillation step itself** — it is only used (in the KX fork) for enriching training records with point-in-time market data and for backtesting the resulting trading signals.

## 6. Relevant modules

`kdbx/enrichment.py` (`aj`-based point-in-time enrichment, parameterized q strings — explicitly documents "No user values are ever interpolated into the q string," a real injection-safety discipline worth noting), `kdbx/backtest.py`, `kdbx/market_tables.py`, `kdbx/connection.py` (pykx connection pooling) — all in the KX fork only, absent from NVIDIA's canonical repo.

## 7. Tests and test quality

Both repos have a `tests/` tree with `unit/`, `integration/`, `fixtures/`, and `conftest.py`/`pytest.ini` at root — real test infrastructure confirmed by directory listing. Individual test file contents were not fetched to assess assertion quality/coverage — a real gap; recommend a follow-up pass reading `tests/unit/kdbx/*` before any ADOPT/ADAPT decision.

## 8. Deterministic / reproducibility properties

Not verified beyond directory listing. The as-of join itself (`aj`) is deterministic given a fixed data snapshot, but the pipeline's random/LoRA training seeds were not checked for pinning.

## 9. Asset / timeframe / venue assumptions

The demo dataset is explicitly synthetic per KX's own value-prop doc — the end-to-end "verified working" table (read in full) reports ingestion of only **104 records**, 2-epoch/20-step LoRA fine-tuning, on synthetic tickers. This is a toy validation run, not a production-scale backtest.

## 10. Hidden defaults / semantic coupling — the central finding: a real result conflation

The KX value-proposition doc's own "verified working" table reports **base model F1 = 0.095, fine-tuned F1 = 0.062** — i.e., fine-tuning made the toy demo *worse*, contradicting the polished 0.85–0.95 F1 headline numbers quoted in both READMEs. **Those headline numbers come from a different, larger-scale run (5K–25K samples) documented in NVIDIA's canonical repo, not from the small KDB-X integration demo.**

**The local capture's file conflates these two different result sets** — its "Results" table cites the 5K/10K/25K numbers as if validating the KDB-X-integrated pipeline, but the actual KDB-X pipeline's own reported number is the worse 0.062/0.095 pair. **This is exactly the kind of "certified garbage from an unexamined confound" pattern this project's own global data-integrity rules warn about** — anyone treating the 0.95 F1 as validation of the KDB-X-specific integration would be wrong. The local capture at `/home/d-tuned/life/resources/AlgoTradingIdeas/nvidia-kdbx-model-distillation.md` should be corrected to separate these two result sets explicitly.

## 11. Performance claims

The 98% cost-reduction and F1 numbers are NVIDIA-reported, from NVIDIA's own blog and README, not independently reproduced — same caution flagged in the prior cuOpt report applies verbatim.

## 12. What Signal Current could reuse

The `aj`-based backward-in-time join *pattern* (point-in-time enrichment, no look-ahead) is legitimate and worth citing in the Data Architecture spec as a concept — it's a decades-old KX/kdb+ idiom, not NVIDIA IP, and Signal Current can implement the equivalent semantics in plain SQL/pandas (`pandas.merge_asof` already implements this exact operation) without touching KDB-X, NeMo, or GPUs at all.

## 13. What Signal Current should not inherit

The KDB-X/pykx runtime dependency (proprietary EULA with hard RAM/thread/connection caps layered under an Apache-2.0 code license); the NeMo/NIM/GPU stack for distillation; and — critically — the conflated benchmark numbers described in §10.

## 14. Integration / coupling risks

Same GPU/CUDA and vendor-runtime concerns as the earlier cuOpt candidate report. GPU/CUDA hard requirements were not explicitly stated in either README fetched — treat the local capture's "likely needs A100/H100" as speculative and unconfirmed, not fact.

## 15. Required parity / golden tests if used as reference

A golden test proving `aj` enrichment produces byte-identical output to `pandas.merge_asof` on the same synthetic dataset, plus a test asserting no look-ahead (enriched timestamp always ≤ event timestamp), before trusting any derived signal from either implementation.

## 16. Proposed disposition

**REFERENCE** — cite the as-of-join concept and the NeMo distillation pattern in the Data Architecture / Provenance spec's prior-art section. Not ADOPT/FORK/ADAPT/WRAP: the actual code has a proprietary-licensed runtime dependency, unverified test quality, and a self-reported result that contradicts its own headline claim. If Signal Current wants a concrete point-in-time join to build against, **`pandas.merge_asof` (BSD-licensed) is the vendor-neutral equivalent worth treating as the real reference implementation**, not this blueprint.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on repo existence, license text, and architecture — all read directly from primary sources. Medium confidence on maintenance quality and test rigor (directory-listing level only, not content-read). Medium on performance claims (vendor-only, not independently reproduced, flagged as such).

## 18. Unresolved questions

- Exact contents/assertion quality of `tests/unit/kdbx/*` — not read.
- Whether KX's fork will be merged upstream or remains permanently divergent from `NVIDIA-AI-Blueprints`.
- GPU/CUDA hard requirements were not explicitly stated in either README fetched — the local capture's "likely needs A100/H100" is speculative and unconfirmed, flagged as still unverified rather than repeated as fact.

## 19. Verification verdict on the local capture's claims

**Partially held up, with one material correction and one conflation flagged.** The KDB-X-vs-MongoDB/Elasticsearch/HNSW architectural claims and the `aj` mechanism check out against primary source. **The "98% cost reduction / 0.95 F1" headline numbers are real but come from NVIDIA's larger non-KDB-X validation run, not the actual KDB-X-integrated demo, whose own reported F1 (0.062 fine-tuned vs 0.095 base) is worse, not better** — the local file's single merged "Results" table obscures this and should not be cited as evidence the KDB-X integration itself was validated at that quality level.

## Sources

- https://github.com/NVIDIA-AI-Blueprints/ai-model-distillation-for-financial-data
- https://github.com/KxSystems/nvidia-kx-samples
- https://kx.com/products/kdbx/
- https://kx.com/blog/kdb-x-ga-built-for-developers/
- https://docs.kx.com/product/licensing/usage-restrictions.htm
- https://developer.nvidia.com/blog/build-efficient-financial-data-workflows-with-ai-model-distillation/
- Local file: `/home/d-tuned/life/resources/AlgoTradingIdeas/nvidia-kdbx-model-distillation.md`
