# Signal Current Constitution

**Status:** Draft — corrected per Sol's cold review 2026-09-05, not yet re-reviewed.
**Authority:** This document states what can never be violated. It is not implementation guidance, not a library list, not a roadmap. It is sourced from `docs/specs/00-source-inventory-reconciliation.md` in full — not solely §6 or §7.1. Every substantive clause below carries an inline citation to a specific section or Reconciliation Matrix row so a reader can verify traceability without trusting this blanket statement.

**Scope note (cross-reference):** `docs/INVARIANTS.md` states generic engineering-process hygiene for this repo (review discipline, delegation, push policy). This Constitution states Signal Current's domain-specific scientific/research invariants (no privileged market dimension, evidence boundaries, statistical discipline, agent authority over numerical evidence). Neither document covers the other's job — check both. `docs/INVARIANTS.md` §5 already cross-references this file; this note makes the reference bidirectional.

## 1. What Signal Current is

Signal Current is an auditable quantitative research, strategy engineering, portfolio, deployment, and lifecycle system with a research-intelligence front end and a deterministic numerical core. *(Reconciliation §3, Proposed Canonical System Thesis.)*

**The deterministic quant engine is the sole source of numerical research truth.** Agents, LLMs, and external tools may operate the laboratory — discover, propose, organize, compare, explain, recommend — but numerical evidence originates only from the deterministic engine. *(Reconciliation §3; Matrix row 1.)*

## 2. No privileged market dimension

Signal Current MUST NOT privilege any asset class, instrument, venue, timeframe, resolution, session model, currency, contract type, execution platform, or deployment target in its core scientific semantics. *(Reconciliation §3; Matrix row 24.)*

Market-specific and venue-specific behavior belongs in explicit typed contracts and metadata, never as an implicit engine default. Any assumption introduced for a test case, asset class, timeframe, venue, provider, broker, or deployment platform MUST NOT become implicit core behavior (Fixture Isolation Principle). *(Reconciliation §7.2.)*

## 3. Evidence boundaries

1. Every numerical result must be attributable to immutable/versioned inputs, engine semantics, cost model, and environment. *(Reconciliation §6 item 2; Matrix row 1.)*
2. Research evidence is not execution semantics. A ResearchRecord may hold ambiguity, citation, alternate interpretation. StrategyIR may not — it must be executable and semantically complete. Promotion from one to the other is explicit and auditable. *(Reconciliation §5; Matrix row 9.)*
3. A candidate is not a strategy artifact. Only a candidate that completes required validation and promotion gates becomes an immutable StrategyArtifact. *(Reconciliation §5.)*
4. A backtest is not a deployment. Build, target-platform conformance, incubation, risk approval, telemetry, and health monitoring are separate promotion stages. *(Reconciliation §5; Matrix row 43.)*
5. Live data closes the research loop but never rewrites historical research artifacts. LiveObservation artifacts are append-only. *(Reconciliation §6 item 12; Matrix row 47.)*
6. Retraining or regeneration creates a new lineage child artifact and repeats required gates. Historical versions remain immutable. *(Matrix row 48.)*

## 4. Exploration, validation, and the sealed lockbox

Exploration, validation, and sealed confirmation are separate evidence zones. Search and tuning code cannot access the sealed final lockbox. Lockbox access is an auditable event and may invalidate a research cycle. *(Reconciliation §6 item 1; Matrix rows 26 and 36.)*

## 5. Statistical discipline — non-negotiable, not "best practice"

1. Purging and embargo are required whenever training/validation observations or labels can overlap or leak through temporal adjacency. *(Reconciliation §6 item 4; Matrix row 27.)*
2. CPCV is supported and required when the ValidationPlan determines it is applicable. *(Reconciliation §6 item 5; Matrix row 28 — applicability matrix itself remains open per Matrix Unresolved U-06.)*
3. Temporal out-of-sample evaluation is mandatory. Walk-forward is the default for adaptive/parameterized/model workflows. *(Reconciliation §6 item 6; Matrix row 32.)*
4. Multiple-testing and selection-bias diagnostics are mandatory after any broad search. Raw Sharpe is never sufficient evidence. *(Reconciliation §6 item 7; Matrix rows 19 and 33.)*
5. Every large search records the number and structure of hypotheses tried — search space, budget, seeds, candidate count, rejection count, selection process, and hypothesis lineage. *(Reconciliation §6 item 3; Matrix row 19.)*
6. Transaction costs and execution assumptions are explicit, versioned, and stressed. No promotion on a zero-cost assumption unless the instrument truly has no modeled cost and this is justified. *(Reconciliation §6 item 8; Matrix row 34.)*
7. ML workflows use leakage-safe feature/label pipelines, immutable model artifacts, environment hashes, and drift monitoring. *(Reconciliation §6 item 9.)*
8. Triple-barrier labeling, meta-labeling, and sample-uniqueness weighting are first-class capabilities for applicable ML/event workflows — not imposed on deterministic rule strategies that don't need them. *(Reconciliation §6 item 10; Matrix rows 29, 30, and 31.)*
9. HRP is a first-class portfolio method. Portfolio decisions additionally require behavioral-diversification and shared-account evidence — parameter/timeframe diversity alone is not edge diversity. *(Reconciliation §6 item 11; Matrix rows 39, 40, and 41.)*

## 6. Agent authority boundary

Agents may operate the laboratory: discover sources, propose hypotheses, plan experiments, operate typed tools, compare evidence, explain results. *(Reconciliation §3; Matrix row 2.)*

Agents may NOT:
- invent or mutate numerical evidence; *(Reconciliation §3; Matrix row 2.)*
- alter immutable evidence; *(Reconciliation §6 item 13.)*
- bypass validation or risk gates; *(Reconciliation §6 item 13; §7.1.)*
- access sealed confirmation/lockbox data through an unauthorized path; *(Reconciliation §3; Matrix row 26.)*
- serve as, substitute for, or impersonate the human authorization required to promote any material-risk-bearing artifact, regardless of how confident or well-tested the agent's judgment is. Promotion of any material-risk-bearing artifact requires explicit HUMAN authorization; no agent-defined or agent-only authorization path ever satisfies this gate. *(Matrix row 49.)*

LLM output is always an unvalidated proposal artifact. It receives no evidentiary weight until compiled to valid IR and evaluated by deterministic tools. *(Matrix row 18.)*

## 7. Determinism, stochastic methods, and non-replayable output

Numerical evidence divides into three categories with different evidentiary treatment. Conflating them — in particular, treating a recorded seed as sufficient to make inherently non-replayable output count as evidence — is prohibited.

1. **Deterministic replay.** Under the supported determinism contract, deterministic modes must reproduce an identical event ledger and metrics for an identical versioned input tuple. This is the default evidentiary standard for the numerical core. *(Reconciliation §7.1; Matrix row 22.)*
2. **Seedable stochastic computation.** Non-deterministic but seedable algorithms (e.g. randomized search, bootstrap resampling) must record seeds and environment, but a single run is never sufficient trust. They additionally require an immutable input snapshot plus replicated or statistical evidence across runs before promotion. *(Matrix row 22, read together with Matrix row 1's requirement that results be attributable to versioned inputs and environment.)*
3. **Inherently non-replayable output.** Live LLM inference and live market-data feeds cannot be made replayable by recording a seed or environment metadata — there is no fixed input tuple to replay against. Such output MUST be classified as proposal material only and MUST NOT be certified as numerical evidence, regardless of seed-recording. *(Matrix row 18, extended to the live-data case by Matrix row 1's sole-numerical-authority principle.)*

## 8. External engines and libraries are conformance systems, never truth stores

No external engine, library, or target platform — however well-tested, however widely adopted — may become an alternate source of numerical research truth or a coequal authority alongside the deterministic quant engine. External engines and target platforms exist to verify, compare against, or build toward Signal Current's reference semantics; they never define or supersede them. *(Reconciliation Matrix row 1: "External engines and target platforms are verification/conformance systems, not alternate truth stores"; reinforced by Matrix row 16 on cross-engine parity and Matrix row 44 on MT5's role: "MT5 is a deployment/conformance adapter. It MUST NOT define Signal Current research semantics, data semantics, execution ontology, or StrategyIR.")*

This applies to every adopted third-party library without exception, present or future.

## 9. Reproducibility and lineage

1. State transitions and consequential actions produce versioned audit events with actor, correlation/causation ID, and artifact references. *(Matrix row 53.)*
2. Every acquired source produces an immutable SourceSnapshot or equivalent evidence reference with capture metadata and hash where possible. *(Matrix row 5.)*
3. No implementation decision may silently override a frozen specification. Post-freeze changes require an explicit amendment/ADR and a version change. *(Reconciliation §§1 and 9.)*

## 10. Authority model

All prior chats, documents, public-product research, strategy corpora, and prior "canonical" or "normative" documents are research inputs, not authority. External repositories are prior art, not authority. *(Reconciliation §1 and §9.)*

No external library's internal ontology may silently become Signal Current's domain model. *(This clause has no single directly matching source clause in the reconciliation document. It is stated here as an explicit synthesis of two grounded sources: §7.2's Fixture Isolation Principle — "No assumption introduced for a test case, asset class, timeframe, venue, provider, broker, or deployment platform may become implicit core behavior" — applied to library internals rather than test fixtures, and Matrix row 44's requirement that MT5 "MUST NOT define Signal Current research semantics, data semantics, execution ontology, or StrategyIR," generalized from MT5 specifically to any adopted library. This is a synthesis, not a directly cited requirement, and is flagged as such rather than silently presented as a one-to-one citation.)*

The future Signal Current v1.0 frozen specification set becomes authoritative only after reconciliation and independent review. *(Reconciliation §1 and §9.)*

## 11. Amendment

This Constitution may be revised before v1.0 freeze as contradictions are discovered during Architecture, Data/StrategyIR, Validation, Portfolio/Deployment, and Agent specification work. After v1.0 freeze, amendment requires an explicit ADR and version change — this document does not silently drift. *(Reconciliation §9.)*
