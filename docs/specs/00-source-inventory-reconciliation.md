# Signal Current — Source Inventory & Reconciliation Matrix

**Status:** Specification-phase working baseline  
**Date:** 2026-09-04  
**Authority:** This document reconciles research inputs. It is not yet Signal Current v1.0.  
**Phase rule:** No implementation begins until the canonical specification set is completed, independently reviewed, and frozen.

## 0. Specification Phase Boundary

Signal Current is now in specification, not ideation and not implementation.

The working order is:

**Reconciliation Matrix → Constitution → System Architecture → Research Methodology → Schemas / Contracts → Validation / Portfolio / Agent specifications → Implementation Roadmap → Independent Review → Freeze v1.0 → Build**

The cookbook analogy is retained as a specification discipline:

- **Ingredients** = data, artifacts, components, services, algorithms, adapters.
- **Measurements** = schemas, statistical thresholds, resource budgets, semantics, invariants, acceptance gates.
- **Recipes** = deterministic research, validation, portfolio, deployment, and monitoring workflows.
- **Cookbook** = the complete Signal Current system specification that defines how those recipes compose.

## 1. Authority Model

All prior chats, documents, public-product research, strategy corpora, and book-derived notes are **research inputs**.

None are authoritative simply because they were previously called “canonical,” “normative,” or a “build specification.”

The future **Signal Current v1.0 frozen specification set** becomes authoritative only after reconciliation and independent review.

## 2. Source Inventory

| Source | Role in synthesis | Important material to preserve | Authority |
|---|---|---|---|
| **Build Alpha** chat | Strategy research, platform comparison, workflow exploration | Algomatic public-strategy harvest; taxonomy; primitive library; Gold behavioral family; portfolio experiments; normalized result schemas; BuildAlpha/SQX/Python parity ideas; BuildAlpha v3 orchestration/search/robustness concepts | Research input |
| **Web Scraping Research App / Quant Alpha Explorer** chat | Research-intelligence architecture | Research Intelligence Engine + Alpha R&D Factory separation; papers/blogs/GitHub ingestion; recursive research loops; raw snapshots; relational metadata; vector retrieval; knowledge graph; novelty/robustness/replicability scoring; hypothesis-to-test pipeline | Research input |
| **Algory / Strategy Factory** chat | End-to-end product/lifecycle architecture | Strategy factory lifecycle; StrategyIR; deterministic simulator; validation laboratory; Vault; portfolio engine; MT5 build/conformance; telemetry; health/retraining; agent restrictions; cookbook framing | Research input |
| **Algomatic Strategy Research Corpus** | Concrete research assets | 21 numbered strategy records; 7 auxiliary/legacy seeds; taxonomy; primitives; source anomalies; Gold research family; portfolio hypotheses; schemas; engine mapping; experiment manifests | Research asset |
| **Algory Deep Dive & Build Specification** | Competitive/product analysis | Local-first lifecycle; M1 campaign simulation; real-tick finalist verification; product strengths/weaknesses; deployment/health loop | Derived research artifact |
| **Strategy Factory Engineering Specification & Cookbook v0.1** | Prior synthesis | Artifact chain; gates G0–G12; simulator tiers; validation controls; deployment model; ADR backlog | Derived research artifact; superseded as authority by this synthesis process |
| **López de Prado — Advances in Financial Machine Learning** | Statistical-method source | Purging, embargo, CPCV, event labeling, uniqueness weighting, meta-labeling, HRP, backtest-overfitting awareness | Methodological input to be encoded as engineering requirements |
| **Stefan Jansen — Machine Learning for Trading, 3rd ed.** | Research/ML engineering source | Research-to-production pipeline, leakage control, walk-forward validation, multiple-testing controls, realistic costs, reproducible features/models, evaluation and deployment feedback | Methodological input to be encoded as engineering requirements |
| **BuildAlpha / StrategyQuant X / Python / MT5 public behavior and adapters** | External comparison and verification | External-engine parity, import/export, target-platform behavior, code generation and execution verification | Secondary verification / adapter input |

## 3. Proposed Canonical System Thesis

**Signal Current is an auditable quantitative research, strategy engineering, portfolio, deployment, and lifecycle system with a research-intelligence front end and a deterministic numerical core.**

The system may use agents, LLMs, machine learning, evolutionary search, Bayesian optimization, external research tools, and target trading platforms. None of them becomes the numerical authority.

**The deterministic quant engine is the source of numerical research truth.**

Agents may operate the laboratory. They may discover, extract, propose, organize, schedule, compare, explain, and recommend. They may not fabricate results, edit evidence, query sealed confirmation data through an unauthorized path, override required statistical gates, or promote risk-bearing artifacts without the defined authorization path.

### No Privileged Market Dimension

Signal Current MUST NOT privilege any asset class, instrument, venue, timeframe, resolution, session model, currency, contract type, execution platform, or deployment target in its core scientific semantics.

A strategy MAY consume one or many synchronized data streams and timeframes. Market-specific and venue-specific behavior belongs in explicit typed contracts and metadata, never as hidden engine defaults.

A CampaignSpec selects and constrains a region of the typed compositional research space. A StrategyIR is the fully resolved executable instance of a strategy composition within that space.

## 4. Reconciliation Matrix

| # | Concept | Source chat(s) | Overlap | Contradiction / tension | Proposed canonical decision | Unresolved? |
|---:|---|---|---|---|---|---|
| 1 | Deterministic numerical authority | Algory/Strategy Factory; Build Alpha parity work | Strong convergence on reproducibility and parity | External engines can appear to be coequal sources of truth | **Signal Current deterministic quant engine is the sole source of numerical research truth.** External engines and target platforms are verification/conformance systems, not alternate truth stores. | No |
| 2 | Agent / LLM role | QAE; Build Alpha v3; Strategy Factory | All envision AI-assisted research/orchestration | BuildAlpha-style LLM orchestration could be interpreted as allowing AI to “find” evidence | Agents are **operators over typed tools and artifacts**. They may propose hypotheses and experiments but may not create numerical evidence or bypass gates. | No |
| 3 | Research Intelligence vs Quant Engine | QAE; Strategy Factory | QAE split research intelligence from alpha R&D; Strategy Factory added research loop later | One monolith versus two products | One Signal Current system with **two bounded domains**: Research Intelligence and Quant Laboratory, connected by typed artifact contracts. | No |
| 4 | External research ingestion | QAE; Build Alpha corpus | Papers, websites, GitHub, public strategy descriptions all feed research | Earlier corpus was strategy-site focused; QAE was broad web intelligence | Generalize ingestion to heterogeneous research sources while preserving source-specific extraction. | No |
| 5 | Raw source preservation | QAE; corpus methodology | Provenance and source anomalies both require originals | None | Every acquired source must produce an immutable **SourceSnapshot** or equivalent evidence reference with capture metadata and hash where possible. | No |
| 6 | Vector store | QAE; BuildAlpha copilot idea; Strategy Factory v0.1 | Useful for semantic discovery | Prior spec excluded vector DB as core dependency | Vector retrieval is a **derived discovery index**, never canonical truth. It must be rebuildable from canonical records. No vector database is required for the numerical spine. | No |
| 7 | Knowledge graph | QAE | Papers, strategies, signals, markets, data, evaluations and relationships | Could become a second source of truth | Graph is a derived relationship/index layer over canonical IDs and provenance records. Graph inference may suggest links but cannot assert empirical validity without evidence artifacts. | No |
| 8 | Recursive autonomous research | QAE; Build Alpha orchestration | Both support iterative search and experiment suggestion | Unbounded agents risk runaway search, contamination, or self-confirmation | Recursive research must be **budgeted, logged, provenance-aware, and promotion-gated**. It may create ResearchHypotheses/Campaign drafts, never validation outcomes. | No |
| 9 | Research record vs executable strategy | Build Alpha corpus; Strategy Factory | Corpus has rich human/source schema; Strategy Factory has executable StrategyIR | Treating them as one object loses ambiguity/provenance or pollutes execution semantics | Maintain distinct contracts: **ResearchRecord/Hypothesis** for provenance and ambiguity; **StrategyIR** for fully resolved executable semantics. Promotion from one to the other is explicit and auditable. | No |
| 10 | Strategy as named product vs behavioral family | Build Alpha corpus; Gold program | Primitive taxonomy repeatedly shows multiple named systems share one latent behavior | Generator-oriented platforms encourage counting variants as distinct discoveries | Signal Current researches **behavioral families and primitives**, not merely named strategies. Named strategies are source instances. | No |
| 11 | Strategy grammar / primitives | Build Alpha corpus; Strategy Factory IR | Regime + detector + trigger + exit + stop + sizing + role maps naturally to IR | Exact grammar not yet finalized | StrategyIR must support composable, typed primitives and explicit semantic roles rather than opaque code blobs. It MUST support one or more synchronized input streams/timeframes with explicit alignment and availability semantics. | **Yes — exact schema/hashing** |
| 12 | Entry and exit hypotheses | Build Alpha / Gold | Corpus explicitly found entry ≠ exit | Many strategy records package entry+exit as indivisible | Entry, exit, sizing, regime, and risk logic are independently addressable experiment dimensions and lineage components. | No |
| 13 | Regime conditioning | QAE; Gold; Jansen; future ML | Repeatedly identified as important | Risk of discovering regimes on confirmation data | Regime definitions/models are first-class experimental artifacts and must obey the same evidence boundary and leakage rules as strategies. | No |
| 14 | Public strategies as fixtures | Build Alpha corpus | Exact source-like baselines useful for parity and plumbing | Could bias research toward reproducing reported performance | Public baselines are **engineering fixtures and hypotheses**, not performance targets. Reported author results are provenance only. | No |
| 15 | Source ambiguity and conflicting rules | Build Alpha corpus | Known prose/code discrepancies and impossible reported statistics | Temptation to silently “correct” a source | Preserve ambiguity explicitly. Each plausible interpretation becomes a versioned branch/assumption set. Never silently rewrite source evidence. | No |
| 16 | Cross-engine parity | Build Alpha corpus | Earlier milestone called for same baseline in ≥2 engines | Conflicts with one deterministic engine as numerical truth | Keep external-engine parity as **conformance and regression evidence**. Signal Current owns the reference semantics; BuildAlpha/SQX/Python/MT5 results are comparison artifacts. | No |
| 17 | Search/generation mechanisms | Build Alpha v3; Algory; Strategy Factory | GA, evolutionary search, Bayesian/surrogate, manual and agent proposals all produce candidates | Risk of architecture being tied to one search algorithm | Search is pluggable. Every generator must emit valid StrategyIR candidates and a replayable CampaignSpec; all candidates use the same deterministic simulation/validation path. | No |
| 18 | LLM-generated signals | Build Alpha v3; QAE | Agents can create hypotheses from language/research | LLM output may look authoritative | LLM output is always an **unvalidated proposal artifact**. It receives no evidentiary weight until compiled to valid IR and evaluated by deterministic tools. | No |
| 19 | Search budget and multiple hypotheses | López de Prado; Jansen; Strategy Factory | Search scale creates selection bias | Earlier simple backtests often ignored number of trials | Every campaign records search space, budget, seeds, candidate count, rejection count, selection process, and hypothesis lineage. Statistical controls must account for selection. | No |
| 20 | Simulation tiers | Algory/Strategy Factory | Fast search plus high-fidelity finalist verification is useful | M1 simulator was treated as canonical in a MetaTrader-focused design; broader Signal Current spans multiple assets/horizons | Preserve **tiered simulation**, but fidelity and resolution MUST be selected through explicit market/execution contracts. No timeframe or bar resolution, including M1, is canonical. | No |
| 21 | Fill/execution semantics | Algory; Build Alpha parity; corpus | Signal timing, costs and indicator conventions materially change results | Exact intrabar collision and fill policies still undecided | All order timing, stop/target collision, gaps, spreads, slippage, fees, financing, tick/lot sizes, sessions and margin assumptions must be explicit and versioned. | **Yes — exact semantics ADR** |
| 22 | Deterministic replay | Strategy Factory | Simulation key and event ledger model already proposed | Hardware/library nondeterminism may affect some algorithms | Deterministic modes must reproduce the same event ledger and metrics for an identical versioned input tuple under the supported determinism contract. Non-deterministic algorithms must record seeds and environment. | No |
| 23 | Data stores | QAE; earlier DuckDB/Postgres split; Strategy Factory | Columnar research data + relational metadata is consistent | Exact object-store/queue choices varied | Canonical architecture: immutable columnar market/feature snapshots; analytical scans separate from transactional metadata/lineage; content-addressed artifacts. Exact products remain implementation decisions where not semantically important. | Partly |
| 24 | Market/data scope | QAE; Build Alpha; Strategy Factory | Research spans multiple asset classes, instruments, venues and horizons | Earlier examples risked promoting an operational example into a semantic default | **No privileged market dimension.** Signal Current MUST NOT privilege any asset class, instrument, venue, timeframe, resolution, session model, currency, contract type, execution platform, or deployment target in its core scientific semantics. | No |
| 25 | Broker/platform normalization | Build Alpha parity; Algory | Timezone, sessions, futures rolls, broker costs matter | Exact normalization policy not settled | DataSnapshot and ExecutionContext must explicitly encode venue/broker/calendar/session/roll/contract/currency assumptions. | **Yes — normalization contract** |
| 26 | Exploration vs confirmation | López de Prado; Jansen; Strategy Factory | Strong convergence | Earlier recursive research loop could repeatedly inspect holdout data | Establish separate **Exploration**, **Validation**, and **Sealed Lockbox** evidence zones. Search/tuning cannot access lockbox data. Lockbox opening is audited. | No |
| 27 | Purging and embargo | López de Prado; Strategy Factory | Needed for overlapping financial observations/events | Not every rule backtest uses labeled overlapping events | Required whenever training/validation observations or labels can overlap or leak through temporal adjacency. Applicability decision is machine-recorded; exceptions require justification. | No |
| 28 | CPCV | López de Prado; Strategy Factory | Useful for estimating path sensitivity and reducing single-split dependence | Could be over-applied to every strategy/timeframe | CPCV is a first-class validation method **where sampling/event structure supports it**, not a universal ritual. ValidationPlan records applicability. | **Yes — applicability/default matrix** |
| 29 | Triple-barrier labeling | López de Prado; future ML | Natural fit for event-based classifiers/meta-labels | Rule strategies do not require labels | Required capability for event-label/ML workflows where appropriate; not imposed on deterministic rule strategies. | No |
| 30 | Meta-labeling | López de Prado; Strategy Factory | Preserves interpretable primary signal while ML decides take/skip/size | BuildAlpha-style AI could otherwise replace primary semantics | Preferred first-class ML pattern: keep primary StrategyIR side/intent separate from secondary ML acceptance/sizing model. | No |
| 31 | Sample uniqueness/weighting | López de Prado | Controls overlapping event information | Missing from early corpus backtests | Required for ML/event-sampled experiments when observations overlap materially. | No |
| 32 | Walk-forward validation | Jansen; BuildAlpha; Strategy Factory | Consistent temporal testing theme | Some one-shot historical strategies may not naturally “retrain” | Temporal OOS evaluation is mandatory. Walk-forward is the default for parameterized/model/retraining workflows and otherwise required unless ValidationPlan records why another temporal design is superior. | No |
| 33 | Multiple-testing diagnostics | López de Prado; Jansen; Strategy Factory | Search-scale bias is central | Earlier source backtests often reported a single Sharpe/CAGR | Validation must include trial-aware diagnostics. DSR and/or reality-check-style methods are available where mathematically applicable; raw Sharpe is never sufficient evidence after broad search. | **Yes — exact default diagnostics** |
| 34 | Cost realism | Jansen; Algory; corpus | Costs repeatedly identified as a source of false edge | Public reports often omit costs | CostModel is a versioned required input. Validation includes baseline costs plus adverse-cost stress. No promotion on zero-cost assumptions unless the instrument truly has no modeled cost and this is justified. | No |
| 35 | Perturbation / Monte Carlo | BuildAlpha; Strategy Factory | Parameter perturbation, trade/order resampling and scenario stress all recur | Different tools use “Monte Carlo” for different procedures | Define separate robustness families: parameter sensitivity, execution/cost stress, trade/path resampling, data perturbation, regime stress. Each run records the actual method rather than a generic “Monte Carlo passed.” | No |
| 36 | Lockbox | Strategy Factory; López de Prado/Jansen principles | Strong anti-overfitting safeguard | Human curiosity can contaminate it even if code is blocked | Lockbox must be protected at the data/service permission layer, not merely hidden in UI. Access is an auditable event and may invalidate a research cycle. | No |
| 37 | Strategy promotion / Vault | Algory; Strategy Factory | Useful lifecycle state and passport concept | “Vault” could imply a product-specific UI concept | Retain the underlying **immutable StrategyArtifact registry/passport**. “Vault” may remain a UX name but is not a core domain requirement. | No |
| 38 | Strategy passport | Strategy Factory; corpus provenance | Both want full lineage, assumptions and evidence | None | Every promoted StrategyArtifact carries provenance, IR, data zones, campaign/search budget, costs, validation evidence, complexity, regimes, compatibility and decision history. | No |
| 39 | Behavioral diversification | Build Alpha corpus/Gold; portfolio research | Strong evidence that indicator-name diversity can be fake | Generic portfolio optimizers can reward correlated variants | Portfolio qualification must measure signal, return, exposure, drawdown, regime and latent-family overlap. Parameter/timeframe diversity alone does not count as edge diversity. | No |
| 40 | HRP | López de Prado; Strategy Factory | Strong fit for correlated strategy portfolios | Could become dogmatic single allocator | HRP is a first-class allocation method and benchmark, not the only permissible allocator. Alternatives must be compared under common risk and shared-account simulation. | No |
| 41 | Shared-account simulation | Algory; Strategy Factory | Needed to model margin/capital/exposure interactions | Simple sum-of-equity-curves is insufficient | Portfolio promotion requires shared-capital/event simulation with common calendar/currency, aggregate margin, exposure and risk constraints. | No |
| 42 | Portfolio contribution testing | Build Alpha portfolio research | Leave-one-out and behavioral contribution are already explicit | Optimized weights can hide redundant members | Portfolio evaluation includes leave-one-out contribution, drawdown overlap, tail/correlation stress, concentration, regime coverage and capital utilization. | No |
| 43 | Deployment as artifact promotion | Algory; Strategy Factory | Codegen + conformance + approval | Earlier workflows sometimes treated export as the endpoint | Build and deployment are controlled artifact promotions with compile/build metadata, semantic conformance, target compatibility, approval, rollback and audit. | No |
| 44 | MT5 role | Algory/Strategy Factory; Build Alpha adapters | Strong concrete deployment ecosystem | MT5-centric design conflicts with broader Signal Current scope | MT5 is a deployment/conformance adapter. It MUST NOT define Signal Current research semantics, data semantics, execution ontology, or StrategyIR. Whether it is implemented early is a roadmap decision, not an architectural privilege. | No |
| 45 | Target-platform parity | Algory; Build Alpha parity | Reference-vs-target comparison is essential | Literal numerical identity may be impossible because platform fills differ | Require event/decision conformance within explicit tolerance and explainable difference classes. Target-platform real-tick verification is finalist evidence, not a replacement for reference simulation. | **Yes — tolerance policy** |
| 46 | Incubation / risk graduation | Algory; Strategy Factory | Paper → shadow → small-risk progression | Earlier research app stopped at backtest/report | Preserve as mandatory deployment lifecycle before material risk. Graduation requires evidence and explicit authorization. | No |
| 47 | Live telemetry and health | Algory; Strategy Factory; Jansen production feedback | Deployment feedback closes research loop | Risk that live observations rewrite history | LiveObservation artifacts are append-only operational evidence. They can trigger re-research/retraining but never mutate historical StrategyArtifacts or validation results. | No |
| 48 | Retraining / regeneration | Algory; ML lifecycle | Continuous maintenance is needed | Retuning same object can destroy auditability | Retraining/regeneration creates a **new lineage child artifact** and repeats required gates. Historical versions remain immutable. | No |
| 49 | Autonomous live deployment | Algory automation ideas; agent layer | Automation is useful operationally | Conflicts with human risk authority and numerical evidence boundaries | Fully autonomous risk-bearing deployment is outside v1.0. Agents may prepare and recommend; risk promotion requires the defined human authorization gate. | No |
| 50 | Local-first vs distributed | Algory; Strategy Factory; QAE | Local research and future scale-out both desired | Desktop product versus distributed research service | Domain semantics and APIs must be identical from single-workstation through distributed-worker modes. Compute topology may change; scientific meaning may not. | No |
| 51 | UI / mission control | Strategy Factory; QAE | Long-running workflows need visibility | Risk of building dashboard before semantics | UI is an operator surface over contracts. No UI-first implementation. Every long-running state must be inspectable, but P0 proves numerical/semantic spine first. | No |
| 52 | Agent educational/copilot modes | Build Alpha learning-copilot idea | Tutor/Navigator/Debugger modes could improve usability | Not required for core research engine | Preserve as a future UX capability, separate from the privileged orchestration agent layer. It receives read/limited action permissions through the same tool contracts. | No |
| 53 | Event/audit model | Strategy Factory | Events already support lineage and orchestration | None material | State transitions and consequential actions produce versioned audit events with actor, correlation/causation IDs and artifact references. | No |
| 54 | Numerical-core language | Strategy Factory prior ADR | Python-only and Rust-core variants both proposed | No evidence yet that Rust is required | Specify performance/determinism contracts first; choose implementation language after benchmark. | **Yes** |
| 55 | Worker queue / artifact backend | Strategy Factory prior ADR | Multiple viable technologies | Technology choice can be mistaken for domain architecture | Keep durability/idempotency/content-addressing requirements canonical; defer exact queue/object-store product to ADR after benchmark. | **Yes** |
| 56 | Agent activation criteria | Strategy Factory; QAE | Agents are useful, but typed tools must exist first | Turning agents on too early could encode unstable workflows | Agent harness is not required for P0 numerical spine. Activate after core APIs/artifacts/gates are stable enough to constrain agents. | **Yes — exact phase/gate** |
| 57 | First build slice | Build Alpha corpus; Strategy Factory | Both say prove reproducible plumbing before scaling search | Earlier wording incorrectly elevated a single asset/timeframe fixture into an architectural decision | P0 proves the general contracts through the smallest useful vertical slice. Any operationally first test case has zero semantic privilege. The conformance program MUST progressively exercise heterogeneous assets, timeframes, multi-timeframe strategies, and execution contexts. | No |
| 58 | “More strategies” vs trustworthy laboratory | Build Alpha corpus; Strategy Factory | Both eventually converge on plumbing and trust | Harvesting can become endless ideation | New source ingestion continues, but it cannot delay the specification/build gates. The primary milestone is a trustworthy research laboratory, not corpus size. | No |

## 5. Canonical Artifact Spine — Proposed for the Architecture/Data Specifications

The reconciliation implies the following conceptual lineage:

**SourceSnapshot → ResearchRecord / ResearchHypothesis → ExperimentDefinition / CampaignSpec → StrategyIR Candidate → SimulationRun → ValidationArtifact → StrategyArtifact → PortfolioArtifact → BuildArtifact → DeploymentArtifact → LiveObservationSet → HealthAssessment → DecisionRecord / ResearchTrigger**

Important distinctions:

**Research evidence is not execution semantics.**  
A ResearchRecord can contain missing information, ambiguities, citations, alternate interpretations, and reported external results. StrategyIR cannot. StrategyIR must be executable and semantically complete.

**A candidate is not a strategy artifact.**  
Candidate StrategyIR objects exist inside experiments. Only a candidate that completes the required validation and promotion gates becomes an immutable StrategyArtifact.

**A backtest is not a deployment.**  
Build, target-platform conformance, incubation, risk approval, telemetry, and health monitoring are separate promotion stages.

## 6. Non-Negotiable Method Requirements Carried Forward

The following are now proposed as Signal Current constitutional requirements, not book citations or “best practice” suggestions:

1. Search/tuning code cannot access the sealed final lockbox.
2. Every numerical result must be attributable to immutable/versioned inputs, engine semantics, cost model, and environment.
3. Every large search records the number and structure of hypotheses tried.
4. Purging/embargo are required when temporal/event overlap can contaminate validation.
5. CPCV is supported and required when the ValidationPlan determines it is applicable.
6. Temporal OOS evaluation is mandatory; walk-forward is the default for adaptive/parameterized/model workflows.
7. Multiple-testing/selection-bias diagnostics are mandatory after broad search; raw Sharpe is not sufficient.
8. Transaction costs and execution assumptions are explicit, versioned, and stressed.
9. ML workflows use leakage-safe feature/label pipelines, immutable model artifacts, environment hashes, and drift monitoring.
10. Triple-barrier labeling, meta-labeling, and uniqueness weighting are first-class capabilities for applicable ML/event workflows.
11. HRP is a first-class portfolio method; portfolio decisions additionally require behavioral-diversification and shared-account evidence.
12. Live data closes the research loop but never rewrites historical research artifacts.
13. Agents cannot create or mutate numerical evidence and cannot bypass validation or risk gates.

## 7. Material Unresolved Decisions to Carry into the Canonical Specs / ADR Set

These are the remaining decisions that should **not** be guessed during drafting:

| ID | Decision | Why it is still open | Where it must be resolved |
|---|---|---|---|
| U-01 | Exact StrategyIR schema, canonical serialization and hashing | Central semantic contract requires deliberate design and golden examples | Data Architecture & Strategy IR |
| U-02 | Fill, intrabar collision and execution semantics | Materially changes results | Architecture + Data/IR |
| U-03 | Broker/venue normalization, futures roll, calendars and session policy | Cross-asset correctness depends on it | Data Architecture |
| U-04 | Validation defaults by asset/timeframe and minimum sample requirements | Cannot be universal without research design | Validation & Statistical Controls |
| U-05 | Exact multiple-testing diagnostic defaults | DSR/reality-check/bootstrap methods have different applicability | Validation & Statistical Controls |
| U-06 | CPCV applicability matrix | Must depend on sampling/event structure | Validation & Statistical Controls |
| U-07 | Target-platform parity tolerance and discrepancy taxonomy | Exact equality may be impossible across execution engines | Deployment & Monitoring |
| U-08 | Numerical core implementation language | Need benchmarks before choosing Python-only vs Rust hot path | Architecture / Roadmap ADR |
| U-09 | Worker queue/job granularity | Operational choice, not yet evidence-driven | Architecture / Roadmap ADR |
| U-10 | Initial artifact-store product | Local filesystem vs S3-compatible is deployment-dependent | Architecture / Roadmap ADR |
| U-11 | Agent harness activation gate and permission matrix | Agents should not precede stable typed tools | Agent & Orchestration |
| U-12 | Authentication/authorization model before live-risk operation | Required for risk-bearing deployment | Deployment + Agent |
| U-13 | Exact P0/P1 acceptance thresholds and benchmark/conformance coverage | Must derive from frozen semantics and required heterogeneity coverage | Implementation Roadmap |

### 7.1 Clarification: parameterized research space vs scientific invariants

Signal Current is a typed compositional research system.

**Composable/parameterized dimensions include:**
- asset class, instrument, venue and currency;
- one or more timeframes/resolutions/data streams;
- data source and normalization policy;
- indicators, transforms, features and strategy primitives;
- regime, trigger, entry, exit, stop and sizing logic;
- execution and cost models;
- validation procedures;
- portfolio methods;
- deployment adapters.

**Scientific invariants are not menu choices:**
- no look-ahead;
- point-in-time correctness;
- immutable evidence and lineage;
- deterministic replay where the mode is declared deterministic;
- explicit execution semantics;
- separation of exploration, validation and sealed confirmation;
- required multiple-testing/selection-bias controls;
- agent prohibition from manufacturing numerical evidence or bypassing gates.

### 7.2 Anti-contamination requirement

**Fixture Isolation Principle:** Test cases, public strategies, broker conventions, platform conventions, data-provider conventions, and implementation examples exist to challenge the architecture, not define it.

No assumption introduced for a test case, asset class, timeframe, venue, provider, broker, or deployment platform may become implicit core behavior. Any such behavior MUST be represented through an explicit typed and versioned contract.

## 8. Canonical Document Set to Produce Next

| Order | Canonical document | Primary question |
|---:|---|---|
| 01 | **Signal Current Constitution** | What can never be violated? |
| 02 | **System Architecture Specification** | What are the bounded domains, components, interfaces, states and invariants? |
| 03 | **Research Methodology Specification** | How does an idea become a legitimate experiment? |
| 04 | **Data Architecture & Strategy IR** | What are the canonical data, artifact, schema, lineage and executable strategy contracts? |
| 05 | **Validation & Statistical Controls** | What evidence is required before promotion, and how is self-deception constrained? |
| 06 | **Portfolio, Deployment & Monitoring** | How do validated strategies become portfolios, builds, deployments and monitored live systems? |
| 07 | **Agent & Orchestration Layer** | What may agents do, with which tools, under which permissions and gates? |
| 08 | **Implementation Roadmap** | In what sequence do we build and prove the system without violating the specification? |

## 9. Freeze Rule

No earlier chat, research note, public product, agent recommendation, or prior “normative” document may override the frozen Signal Current specification.

After the eight documents are drafted:

**Independent review → contradiction/coverage repair → v1.0 freeze → implementation.**

Until then, all decisions remain specification work.
