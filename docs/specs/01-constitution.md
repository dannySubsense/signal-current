# Signal Current Constitution

**Status:** Draft — not yet reviewed, not yet frozen.
**Authority:** This document states what can never be violated. It is not implementation guidance, not a library list, not a roadmap. Every clause here traces to `docs/specs/00-source-inventory-reconciliation.md` §6 (Non-Negotiable Method Requirements) or §7.1 (Scientific Invariants) — nothing below is invented for this document.

## 1. What Signal Current is

Signal Current is an auditable quantitative research, strategy engineering, portfolio, deployment, and lifecycle system with a research-intelligence front end and a deterministic numerical core.

**The deterministic quant engine is the sole source of numerical research truth.** Agents, LLMs, and external tools may operate the laboratory — discover, propose, organize, compare, explain, recommend — but numerical evidence originates only from the deterministic engine.

## 2. No privileged market dimension

Signal Current MUST NOT privilege any asset class, instrument, venue, timeframe, resolution, session model, currency, contract type, execution platform, or deployment target in its core scientific semantics.

Market-specific and venue-specific behavior belongs in explicit typed contracts and metadata, never as an implicit engine default. Any assumption introduced for a test case, asset class, timeframe, venue, provider, broker, or deployment platform MUST NOT become implicit core behavior (Fixture Isolation Principle).

## 3. Evidence boundaries

1. Every numerical result must be attributable to immutable/versioned inputs, engine semantics, cost model, and environment.
2. Research evidence is not execution semantics. A ResearchRecord may hold ambiguity, citation, alternate interpretation. StrategyIR may not — it must be executable and semantically complete. Promotion from one to the other is explicit and auditable.
3. A candidate is not a strategy artifact. Only a candidate that completes required validation and promotion gates becomes an immutable StrategyArtifact.
4. A backtest is not a deployment. Build, target-platform conformance, incubation, risk approval, telemetry, and health monitoring are separate promotion stages.
5. Live data closes the research loop but never rewrites historical research artifacts. LiveObservation artifacts are append-only.
6. Retraining or regeneration creates a new lineage child artifact and repeats required gates. Historical versions remain immutable.

## 4. Exploration, validation, and the sealed lockbox

Exploration, validation, and sealed confirmation are separate evidence zones. Search and tuning code cannot access the sealed final lockbox. Lockbox access is an auditable event and may invalidate a research cycle.

## 5. Statistical discipline — non-negotiable, not "best practice"

1. Purging and embargo are required whenever training/validation observations or labels can overlap or leak through temporal adjacency.
2. CPCV is supported and required when the ValidationPlan determines it is applicable.
3. Temporal out-of-sample evaluation is mandatory. Walk-forward is the default for adaptive/parameterized/model workflows.
4. Multiple-testing and selection-bias diagnostics are mandatory after any broad search. Raw Sharpe is never sufficient evidence.
5. Every large search records the number and structure of hypotheses tried — search space, budget, seeds, candidate count, rejection count, selection process, and hypothesis lineage.
6. Transaction costs and execution assumptions are explicit, versioned, and stressed. No promotion on a zero-cost assumption unless the instrument truly has no modeled cost and this is justified.
7. ML workflows use leakage-safe feature/label pipelines, immutable model artifacts, environment hashes, and drift monitoring.
8. Triple-barrier labeling, meta-labeling, and sample-uniqueness weighting are first-class capabilities for applicable ML/event workflows — not imposed on deterministic rule strategies that don't need them.
9. HRP is a first-class portfolio method. Portfolio decisions additionally require behavioral-diversification and shared-account evidence — parameter/timeframe diversity alone is not edge diversity.

## 6. Agent authority boundary

Agents may operate the laboratory: discover sources, propose hypotheses, plan experiments, operate typed tools, compare evidence, explain results.

Agents may NOT:
- invent or mutate numerical evidence;
- alter immutable evidence;
- bypass validation or risk gates;
- access sealed confirmation/lockbox data through an unauthorized path;
- promote a risk-bearing artifact outside the defined authorization path.

LLM output is always an unvalidated proposal artifact. It receives no evidentiary weight until compiled to valid IR and evaluated by deterministic tools.

## 7. Reproducibility and lineage

1. Deterministic modes must reproduce the same event ledger and metrics for an identical versioned input tuple under the supported determinism contract. Non-deterministic algorithms must record seeds and environment.
2. State transitions and consequential actions produce versioned audit events with actor, correlation/causation ID, and artifact references.
3. Every acquired source produces an immutable SourceSnapshot or equivalent evidence reference with capture metadata and hash where possible.
4. No implementation decision may silently override a frozen specification. Post-freeze changes require an explicit amendment/ADR and a version change.

## 8. Authority model

All prior chats, documents, public-product research, strategy corpora, and prior "canonical" or "normative" documents are research inputs, not authority. External repositories are prior art, not authority. No external library's internal ontology may silently become Signal Current's domain model. The future Signal Current v1.0 frozen specification set becomes authoritative only after reconciliation and independent review.

## 9. Amendment

This Constitution may be revised before v1.0 freeze as contradictions are discovered during Architecture, Data/StrategyIR, Validation, Portfolio/Deployment, and Agent specification work. After v1.0 freeze, amendment requires an explicit ADR and version change — this document does not silently drift.
