# Candidate Report — NautilusTrader

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** First real candidate report for the prior-art research program — all prior PA-01..PA-10 content was survey-skeleton only until this report.
**Researched:** 2026-09-05, via primary-source fetches (live repo, LICENSE file, docs, GitHub API) — not general knowledge recall.

## 1. Repository / canonical URL

`nautechsystems/nautilus_trader` — https://github.com/nautechsystems/nautilus_trader
Created 2018-06-25 (verified via `gh api repos/nautechsystems/nautilus_trader`).

## 2. Version / commit reviewed

HEAD of `develop` branch (repo default), commit `ac22d5cf4a7e55ba93b233bba5b04de4723b3d3d`, pushed 2026-09-05T14:37 UTC — same day as this research. Latest tagged pre-release: `v2.0.0rc4` (2026-09-02). Last stable non-RC tag: `v1.231.0` (2026-08-02). The project is mid-transition from a Cython-based v1 core to a Rust+PyO3 v2 core (per `MIGRATION_V2.md` and README).

## 3. License — exact, with historical conflict flagged

**Current: GNU LGPL v3.0.** Confirmed by reading the live `LICENSE` file at `master` (full LGPLv3 text) and cross-checked via `gh api repos/.../license` → `LGPL-3.0`.

**This is not a static fact — it changed.** `git log` on the LICENSE file shows the repo was **GPLv3 until 2020-06-10**, when commit `1b1aa1286d97b9dc7f132c5bd1583e0e13d5662e` ("Update LICENSE") replaced the full GPLv3 text with LGPLv3 text — confirmed by reading the diff directly (`-GNU GENERAL PUBLIC LICENSE` / `+GNU LESSER GENERAL PUBLIC LICENSE`). No further license changes since 2020 (only a non-substantive 2024-03-31 cleanup commit touching the file).

Do not assume MIT/Apache from reputation — it is copyleft (LGPL), and was full GPL for its first two years.

## 4. Maintenance / activity

Per `gh api` on 2026-09-05: 28,401 stars, 3,695 forks, 129 open issues, 21 open PRs, last push same day as this query. Contributors endpoint returned 30 (first page only — total contributor count not confirmed, see §18). Repo runs dedicated nightly workflows (`nightly-tests.yml`, `nightly-miri.yml`, `nightly-merge.yml`) and carries a formal `AI_POLICY.md` / `AGENTS.md` / `CLAUDE.md` in-repo — an unusually mature engineering process for an OSS project.

## 5. Architecture summary

Rust-native core with Python as the control plane via PyO3 (per `docs/concepts/architecture.md`, `dst.md`, README). Single-threaded, synchronous, event-driven core, intended to produce deterministic event ordering. Backtest, sandbox, and live modes share one `NautilusKernel` struct and the same cache-then-publish flow — parity is **architectural** (same code path), not merely tested-for after the fact. A `MessageBus` provides pub/sub, request/response, and command/event routing; an `Actor`/`Component` trait pair separates message dispatch from lifecycle management.

The README explicitly caveats parity limits: live execution introduces venue, transport, timing, persistence, external-activity, and reconciliation behavior a simulation may not reproduce (cited from README's summary of `docs/concepts/live.md#backtest-and-live-differences` — that section itself was not independently fetched this session, see §18).

## 6. Relevant modules

From live `crates/` and `python/nautilus_trader/` directory listings: `backtest`, `execution`, `data`, `indicators`, `risk`, `portfolio`, `trading`, `analysis`, `adapters` (crypto/FX/equities/futures/options/betting venues), `persistence`, `infrastructure` (Redis), `model` (instruments/orders/positions), `system` (kernel).

## 7. Tests and test quality

`python/tests/` has `unit/`, `integration/`, `acceptance/` (including `test_backtest.py`, `test_blackbox.py`) plus doc-example tests. Rust side has per-crate `tests/` (e.g. `crates/model/tests/` covering instruments, order book, positions, events). CI includes `nightly-miri.yml` (Rust undefined-behavior detector — a strong low-level rigor signal), `codeql-analysis.yml`, `security-audit.yml`, `openssf-scorecard.yml`, and `dst.yml`. This is a real, multi-layer suite, not a token one. No coverage-percentage figure was found in any source fetched this session.

## 8. Deterministic / reproducibility properties — standout finding

`docs/concepts/dst.md` documents a formal **Deterministic Simulation Testing (DST)** contract, modeled on FoundationDB's approach: a seed-controlled runtime where "one seed determines task scheduling, timer firings, and random values… Two runs with the same seed, binary, configuration, and platform produce identical observable behavior," with failing seeds replayable. The doc states explicitly that a change to it is a contract change for consumers. This is the same reproducible-claim discipline Signal Current's own rules require of itself — a genuine architectural precedent worth citing.

## 9. Asset / timeframe / venue assumptions

README states explicitly: "NautilusTrader is asset-class-agnostic. Any venue with a REST API or WebSocket feed can be integrated through modular adapters." Verified adapters span crypto CEX/DEX, FX, equities, futures, options, and sports betting. No hardcoded timeframe bias found in fetched docs; bar/tick/order-book data types are all first-class per the README feature list.

## 10. Hidden defaults or semantic coupling (partial — not fully audited)

- Rust MSRV is pinned to "generally equal to the latest stable release of Rust" (README) — an aggressive default that could break downstream builds unexpectedly.
- Redis is described as "optional" for state persistence (README); the in-memory default's replay-guarantee semantics without Redis were not verified this session — flagged, not resolved.

## 11. Performance characteristics

Rust core + `mimalloc` allocator + `tokio` async runtime (README; `Cargo.toml` presence confirmed via contents listing). A `codspeed` benchmark badge, `performance.yml` CI workflow, and `BENCHMARKING.md` exist, but no specific benchmark numbers were fetched or read this session — flagged as unresolved (§18).

## 12. What Signal Current can reuse

- The DST methodology (seed-controlled deterministic replay contract) as a design reference for Signal Current's own deterministic-replay invariant.
- The shared-kernel parity pattern (one code path for backtest/live) as an architectural precedent worth citing in the System Architecture spec.
- The message-bus / actor-component separation as a candidate pattern for the Agent & Orchestration layer.

## 13. What Signal Current should not inherit

- LGPL-3.0 copyleft obligations, if any code is copied or statically linked rather than referenced as a dynamic dependency (see §14).
- The crypto/CEX-first adapter ecosystem's execution-venue ontology (OCO/OUO/OTO and other contingency order types) should not be assumed as Signal Current's execution model without independent justification — this is exactly the kind of implicit-default risk the project's Fixture Isolation Principle warns against.
- Coupling to internal APIs mid-migration (v1 Cython → v2 Rust) — anything adapted today has a defined shelf life.

## 14. Integration / coupling risks

LGPL-3.0 permits dynamic linking/use as a library without copyleft propagating to Signal Current's own code. **Any modification to NautilusTrader source itself, or static linking under some interpretations, would require review of LGPL §3-6 obligations — this needs a human/legal read, not an agent's determination**, before any FORK or ADAPT disposition is finalized. The project is v1→v2 mid-migration; anything adapted today should be treated as unstable upstream.

## 15. Required parity / golden tests if used as a conformance comparator

- Deterministic-replay tests using NautilusTrader's own DST seed contract (`docs/concepts/dst.md`) as the reference procedure.
- Golden backtest output comparison on a fixed instrument/bar dataset between NautilusTrader's engine and Signal Current's own engine, carrying explicit truncation/parse-status columns as first-class per this project's data-integrity rule (never silently discard bytes).

## 16. Proposed disposition

**REFERENCE** (architecture and DST pattern) + **CONFORMANCE COMPARATOR candidate** for backtest-correctness checks, **pending license review**.

**Do NOT adopt, fork, or wrap as the core engine now** — v2 is pre-release (`rc4`) and Signal Current's own spec should not couple to an unstable upstream mid-migration.

## 17. Confidence level

MEDIUM-HIGH on facts fetched directly this session (license text/history, repo metrics, doc content, test directory structure — all primary-source read). LOW on the items listed in §18, which were not independently verified.

## 18. Unresolved questions

- Actual contributor count (API returned first page = 30 only, not confirmed total).
- Whether any published, reproducible benchmark numbers exist — `BENCHMARKING.md`/the codspeed dashboard were not read this session.
- Full text of `docs/concepts/live.md#backtest-and-live-differences` — only cited via README's summary, not independently read.
- Legal analysis of LGPL-3.0 obligations for Signal Current's specific reuse pattern (dynamic dependency vs. adapted/copied code) — requires a human legal read, out of this session's scope.
- Whether Redis-optional persistence changes the DST determinism guarantee — `dst.md` was not read in full.

## Sources

- https://github.com/nautechsystems/nautilus_trader
- https://raw.githubusercontent.com/nautechsystems/nautilus_trader/master/LICENSE
- https://github.com/nautechsystems/nautilus_trader/commit/1b1aa1286d97b9dc7f132c5bd1583e0e13d5662e
- https://raw.githubusercontent.com/nautechsystems/nautilus_trader/develop/README.md
- https://raw.githubusercontent.com/nautechsystems/nautilus_trader/develop/docs/concepts/architecture.md
- https://raw.githubusercontent.com/nautechsystems/nautilus_trader/develop/docs/concepts/dst.md
- `gh api repos/nautechsystems/nautilus_trader` (live, 2026-09-05)
- `gh api repos/nautechsystems/nautilus_trader/releases`
- `gh api repos/nautechsystems/nautilus_trader/contents/...` (crates, python package, tests dirs, workflows)
