# Signal Current — System Architecture Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix) and `docs/specs/01-constitution.md`. Not yet independently reviewed. Frozen only after the full
eight-document set clears Frank's binding spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gaps G8/G11; @architect, 2026-09-05, per Frank's spec-gate attempt-1 finding F3 (§12 "zero remaining occurrences" rescoped). @architect, 2026-09-05, per Frank's spec-gate attempt-2 findings F2 (§3.1/§3.2/§5 amended to include independent reproduction) and F3-minor (§12 overshot sentence removed, kickoff doc added to historical-reference list). @architect, 2026-09-05, per Frank's
spec-gate attempt-3 minor finding — replaced §12's "at line ~183" reference with a section/heading-name
reference (kickoff doc's "Prior-art research program" section), since line numbers drift.

**Primary question this document answers:** What are the bounded domains, components, interfaces, states
and invariants of Signal Current?

**Traceability convention:** every substantive clause below cites a specific Reconciliation Matrix row or
section, or a specific Constitution clause. No blanket document-level traceability claim is made. Where a
decision is explicitly unresolved in the Matrix (`U-01`..`U-13`), this document defines the *contract/
boundary* that decision must satisfy once made — it does not invent the missing value or product choice.

---

## 1. Scope and non-goals

This document defines bounded domains, the artifact-lineage state machine, component responsibilities,
interfaces between domains, the Exploration/Validation/Sealed-Lockbox permission boundary, and the P0
build-slice boundary. It does NOT define:

- exact StrategyIR schema/serialization/hashing (deferred to document 04, per U-01);
- exact fill/execution semantics (deferred to document 04, per U-02);
- exact broker/venue normalization contract fields (deferred to document 04, per U-03);
- validation statistical defaults (deferred to document 05, per U-04/U-05/U-06);
- deployment parity tolerance and auth model (deferred to document 06, per U-07/U-12);
- agent permission matrix (deferred to document 07, per U-11);
- numerical-core language, worker/queue product, artifact-store product, and P0/P1 acceptance
  thresholds (deferred to document 08, per U-08/U-09/U-10/U-13).

Where this document must reference one of those undecided items to describe a boundary, it states the
contract the eventual decision must satisfy, tags the item `PROVISIONAL`, names the resolving condition,
and names Danny as owner of the decision (not of the underlying research/benchmark work itself, unless
otherwise stated).

---

## 2. Bounded domains

Per Matrix row 3: "One Signal Current system with two bounded domains: Research Intelligence and Quant
Laboratory, connected by typed artifact contracts." This architecture extends that decision to a full
domain map, because the artifact spine (Matrix §5) and the promotion-gate lifecycle (Constitution §3, §4)
span more than those two domains once portfolio, deployment, and monitoring responsibilities are made
explicit.

| Domain | Responsibility | Constitution/Matrix anchor | P0 status |
|---|---|---|---|
| **Research Intelligence** | Ingest heterogeneous sources (papers, code, public strategy descriptions), preserve raw evidence, extract candidate hypotheses, maintain derived discovery indices (vector, graph). Produces `ResearchRecord`/`ResearchHypothesis`. | Matrix rows 3, 4, 5, 6, 7, 8 | In P0 scope, minimal — only enough to prove SourceSnapshot → ResearchRecord promotion, not full ingestion breadth |
| **Quant Laboratory** | Compile hypotheses/campaigns into `StrategyIR` candidates, run deterministic simulation, run validation/statistical controls, gate promotion into immutable `StrategyArtifact`. Owns the deterministic numerical core. | Constitution §1, §3; Matrix rows 1, 9, 17, 20-35 | Core of P0 |
| **Portfolio** | Combine validated `StrategyArtifact`s into `PortfolioArtifact` under shared-capital/shared-account simulation, behavioral-diversification and contribution testing. | Matrix rows 39-42; Constitution §5.9 | Out of P0 (depends on ≥1 StrategyArtifact existing first) |
| **Deployment & Monitoring** | Build/conformance/incubation/risk-approval promotion of a `PortfolioArtifact` or `StrategyArtifact` into a `DeploymentArtifact`; append-only `LiveObservationSet`; `HealthAssessment`; feedback into new research triggers. | Matrix rows 43-49; Constitution §3.4, §3.5, §3.6 | Out of P0 |
| **Agent & Orchestration** | Operates typed tools across the above domains: proposes hypotheses, plans experiments, compares evidence, explains results. Never a numerical-truth source. | Constitution §6; Matrix rows 2, 8, 18, 56 | **Explicitly not P0** — see §6 below |

Domains communicate only through the typed artifact contracts named in §3, never through shared mutable
state, shared database tables read/written by more than one domain's canonical service, or an internal
API of one domain called directly by another's business logic. This satisfies Matrix row 3's "connected by
typed artifact contracts" and generalizes it across all five domains, not only the original two, because
the same contamination risk (one domain's internal ontology leaking into another) applies uniformly —
this generalization is flagged here as an explicit synthesis, not a direct 1:1 citation, consistent with
Constitution §10's own precedent for stating a generalized synthesis rather than silently presenting it as
a one-to-one citation.

---

## 3. Canonical artifact lineage (state machine)

Per Matrix §5, the canonical artifact spine is:

```
SourceSnapshot
  -> ResearchRecord / ResearchHypothesis
  -> ExperimentDefinition / CampaignSpec
  -> StrategyIR Candidate
  -> SimulationRun
  -> ValidationArtifact
  -> StrategyArtifact
  -> PortfolioArtifact
  -> BuildArtifact
  -> DeploymentArtifact
  -> LiveObservationSet
  -> HealthAssessment
  -> DecisionRecord / ResearchTrigger
```

`DecisionRecord`/`ResearchTrigger` can feed back into new `ResearchHypothesis` or `CampaignSpec` objects,
closing the loop without mutating any upstream artifact (Constitution §3.5, §3.6).

### 3.1 What each artifact IS at architecture level

This section states identity, immutability class, and the domain that owns writes. Full field-level
schema is document 04's job (U-01 for `StrategyIR` specifically).

| Artifact | Identity (architecture level) | Immutability | Owning domain | Anchor |
|---|---|---|---|---|
| `SourceSnapshot` | An immutable capture of one external source at one point in time, with capture metadata and hash where possible. | Immutable, append-only | Research Intelligence | Matrix row 5 |
| `ResearchRecord` / `ResearchHypothesis` | A provenance-carrying research claim. May hold ambiguity, citation, alternate interpretation, missing information. Not executable. | Versioned; superseding record creates a new version, does not overwrite | Research Intelligence | Constitution §3.2; Matrix row 9 |
| `ExperimentDefinition` / `CampaignSpec` | A typed, replayable definition of a search/experiment: search space, budget, seeds, generator config. Selects and constrains a region of the compositional research space. | Immutable once an experiment run references it | Quant Laboratory | Matrix rows 3 (thesis), 17, 19 |
| `StrategyIR Candidate` | A fully resolved, executable strategy composition emitted by a generator (manual, GA, Bayesian, agent-proposed) against a `CampaignSpec`. Executable and semantically complete — no ambiguity permitted. | Immutable once created; a candidate is never mutated after creation, only superseded by a new candidate | Quant Laboratory | Constitution §3.2; Matrix rows 9, 11, 17 |
| `SimulationRun` | The deterministic (or seedable-stochastic, per Constitution §7) execution of one `StrategyIR Candidate` against one versioned input tuple, producing an event ledger and metrics. | Immutable | Quant Laboratory | Constitution §7; Matrix row 22 |
| `ValidationArtifact` | The output of the Validation & Statistical Controls process against one or more `SimulationRun`s: purge/embargo application, CPCV (where applicable), OOS evaluation, multiple-testing diagnostics, cost stress, and an independent-reproduction record (Constitution §3 item 7; Matrix row 59). Records applicability decisions, not just pass/fail. | Immutable | Quant Laboratory | Constitution §5, §3 item 7; Matrix rows 27-35, 59 |
| `StrategyArtifact` | An immutable, promoted strategy "passport": IR, provenance, campaign/search budget, cost model, validation evidence, complexity, regime coverage, decision history. Only exists after required gates pass. | Immutable; retraining creates a new lineage child, never a mutation | Quant Laboratory | Constitution §3.3, §3.6; Matrix rows 37, 38, 48 |
| `PortfolioArtifact` | A promoted combination of `StrategyArtifact`s under shared-capital/shared-account simulation with behavioral-diversification and contribution evidence. | Immutable | Portfolio | Constitution §5.9; Matrix rows 39-42 |
| `BuildArtifact` | A compiled/generated target-platform representation of a `StrategyArtifact`/`PortfolioArtifact`, with build metadata. | Immutable | Deployment & Monitoring | Constitution §3.4; Matrix row 43 |
| `DeploymentArtifact` | A `BuildArtifact` that has passed target-platform conformance, incubation stage-gate, and explicit human risk authorization. | Immutable; rollback creates a new `DeploymentArtifact` state transition event, not a silent edit | Constitution §3.4, §6; Matrix rows 43, 46, 49 |
| `LiveObservationSet` | Append-only operational evidence from a live/incubating deployment. | Append-only, never mutates historical artifacts | Deployment & Monitoring | Constitution §3.5; Matrix row 47 |
| `HealthAssessment` | A derived evaluation of a `LiveObservationSet` against expected behavior; may trigger `DecisionRecord`/`ResearchTrigger`. | Immutable per assessment run | Deployment & Monitoring | Matrix row 47 |
| `DecisionRecord` / `ResearchTrigger` | A versioned audit record of a human or gated decision, or a machine-generated trigger to open new research. | Immutable, append-only | Cross-domain (owned by whichever domain the decision concerns) | Constitution §9.1; Matrix row 53 |

### 3.2 Promotion gates between stages

Every arrow in the spine above is a promotion gate, not a free state transition. Per Constitution §3
and §9.1, every gate transition:

1. is a versioned audit event with actor, correlation/causation ID, and artifact references (Matrix row 53);
2. is one-directional — no gate silently reverses an upstream artifact;
3. requires the specific evidence named for that gate (e.g., `ValidationArtifact` requires purge/embargo
   application record, OOS evaluation, multiple-testing diagnostics per Constitution §5, and — additional to
   and never a substitute for those method-level checks — an independent-reproduction record showing someone
   other than the original author/authoring agent reran the validation against the same content-addressed
   inputs and obtained the same result, per Constitution §3 item 7 and Matrix row 59);
4. for gates promoting a material-risk-bearing artifact (`DeploymentArtifact` risk-tier promotion, per
   Constitution §6 and Matrix row 49), requires explicit human authorization that no agent may serve as,
   substitute for, or impersonate, regardless of agent confidence.

The exact gate names, numbering (e.g. a "G0-G12" style gate ladder was used in a prior, superseded
synthesis per the Matrix's source inventory row for "Strategy Factory Engineering Specification &
Cookbook v0.1" — that document is a research input, not authority, per Matrix §1), and per-gate acceptance
thresholds are validation/roadmap-document scope (documents 05 and 08), not this document's. This document
fixes only that gates exist, are ordered as above, are audited, and that human authorization is mandatory
before any material-risk promotion.

---

## 4. Exploration / Validation / Sealed Lockbox as an enforced architectural boundary

Per Constitution §4 and Matrix row 26: exploration, validation, and sealed confirmation are separate
evidence zones, and this must be enforced beyond a UI convention or a stated rule — it is stated here as
an architectural component boundary, per this document's mandate (US-9's "not just the Constitution
statement").

### 4.1 Enforcing component

A dedicated **Data Access Gateway** component sits in front of all market/feature data storage. It is the
only path by which any Research Intelligence or Quant Laboratory process reads market/feature data. It is
not a convention followed by callers — it is the sole code path; there is no direct storage access from
generator, search, or validation code.

- **Exploration zone access:** search/tuning/generator code and `CampaignSpec` execution may only read data
  tagged `exploration-eligible` by the gateway's access-control metadata.
- **Validation zone access:** `ValidationArtifact`-producing code may read exploration-zone data plus a
  distinct validation-zone data slice, under the same gateway.
- **Sealed Lockbox access:** a `StrategyIR Candidate` may be evaluated against lockbox data only after it
  has already passed the validation zone's required gates. This access is:
  - mediated exclusively by the gateway (never bypassed, never handed to search/tuning code, per
    Constitution §6 "may NOT ... access sealed confirmation/lockbox data through an unauthorized path");
  - logged as a versioned audit event distinct from ordinary reads (Constitution §9.1, Matrix row 53);
  - capable of invalidating the research cycle that produced the candidate, per Constitution §4 — i.e., a
    second lockbox access request against a materially-modified candidate is itself an audited event that
    a human reviewer can use to void the prior cycle.
- **Access-control enforcement point:** at the data/service permission layer (Matrix row 36: "Lockbox must
  be protected at the data/service permission layer, not merely hidden in UI"), meaning the gateway itself
  performs the check — not a UI affordance, not caller-side discipline.

### 4.2 What is NOT specified here

The exact schema of the access-control metadata (e.g., what tags a `CampaignSpec` or dataset carries) is
document 04's job. The exact audit-log schema is also document 04's job. This document fixes only that
the gateway is a single enforced component, that it is the sole access path, and that its enforcement is
at the service layer.

---

## 5. Components

| Component | Responsibility | Domain | Anchor |
|---|---|---|---|
| **Ingestion Service** | Acquires heterogeneous external sources, produces immutable `SourceSnapshot`s with capture metadata/hash. | Research Intelligence | Matrix rows 4, 5 |
| **Extraction/Hypothesis Service** | Extracts candidate `ResearchRecord`/`ResearchHypothesis` from `SourceSnapshot`s. Preserves ambiguity explicitly (Matrix row 15) rather than silently resolving it. | Research Intelligence | Matrix rows 9, 15 |
| **Discovery Index (vector/graph)** | Derived, rebuildable semantic/relationship index over canonical `ResearchRecord`s. Never authoritative; must be fully rebuildable from canonical records if lost. | Research Intelligence | Matrix rows 6, 7 |
| **Campaign/Generator Service** | Accepts a `CampaignSpec`, invokes one or more pluggable generators (manual, GA, evolutionary, Bayesian/surrogate, agent-proposed), emits `StrategyIR Candidate`s. All generators emit the same IR shape and use the same downstream simulation/validation path. | Quant Laboratory | Matrix row 17 |
| **Data Access Gateway** | Sole mediated path to market/feature data; enforces Exploration/Validation/Lockbox zone boundary; audits lockbox access. | Quant Laboratory (cross-cutting) | Constitution §4; Matrix rows 26, 36 |
| **Deterministic Simulation Engine** | Executes a `StrategyIR Candidate` against a versioned input tuple; produces a `SimulationRun` (event ledger + metrics). Supports tiered fidelity, selected via explicit contract, never a hardcoded default resolution. Sole source of numerical research truth (Constitution §1, §8). | Quant Laboratory | Constitution §1, §8; Matrix rows 1, 20-22 |
| **Validation & Statistical Controls Service** | Applies purge/embargo, CPCV where applicable, OOS/walk-forward evaluation, multiple-testing diagnostics, cost-model stress; produces `ValidationArtifact`. Records applicability decisions machine-readably, not just outcomes. Also the sole component authorized to compute and populate `IndependentReproductionRecord.matched` — by comparing the original run's content-addressed output against a distinct actor's reproduction run — never a directly-asserted boolean. | Quant Laboratory | Constitution §5, §3 item 7; Matrix rows 27-34, 59 |
| **Promotion/Gate Service** | Owns the audited state-transition machine of §3.2; the only component authorized to mint an immutable `StrategyArtifact`, `PortfolioArtifact`, `BuildArtifact`, or `DeploymentArtifact`. Enforces the human-authorization gate for material-risk promotions. Also enforces the independent-reproduction gate (Constitution §3 item 7; Matrix row 59): refuses `StrategyArtifact` promotion unless the candidate's `ValidationArtifact` carries a resolved `IndependentReproductionRecord` (per document 05 §2/§2.1). | Cross-cutting (Quant Laboratory / Portfolio / Deployment) | Constitution §6, §3 item 7; Matrix rows 49, 59 |
| **Portfolio Service** | Combines `StrategyArtifact`s under shared-capital simulation; runs behavioral-diversification, HRP/alternative-allocator comparison, leave-one-out contribution testing. | Portfolio | Matrix rows 39-42 |
| **Build/Conformance Service** | Compiles a promoted artifact toward a target platform; runs conformance checks within an explicit tolerance policy (U-07, deferred). | Deployment & Monitoring | Matrix rows 43-45 |
| **Incubation/Risk-Graduation Service** | Manages paper → shadow → small-risk progression; enforces human authorization before material risk (Constitution §6; Matrix row 49). | Deployment & Monitoring | Matrix row 46 |
| **Telemetry/Health Service** | Ingests append-only `LiveObservationSet`s; produces `HealthAssessment`s; may emit `ResearchTrigger`s. Never mutates historical artifacts. | Deployment & Monitoring | Constitution §3.5; Matrix row 47 |
| **Audit/Event Log** | Central, append-only record of every state transition and consequential action across all domains, with actor/correlation/causation IDs. | Cross-cutting | Constitution §9.1; Matrix row 53 |
| **Agent Tool Layer** *(not P0)* | Typed tool surface through which agents propose hypotheses, plan campaigns, compare evidence, explain results. No direct write path to any immutable artifact. | Agent & Orchestration | Constitution §6; Matrix rows 2, 56 — see §6 below |

---

## 6. Agent & Orchestration layer is not on the P0 critical path

Per Matrix row 56 ("Agent harness is not required for P0 numerical spine. Activate after core
APIs/artifacts/gates are stable enough to constrain agents") and this sprint's explicit instruction: the
Agent Tool Layer is named in §2 and §5 as a future bounded domain/component for completeness of the domain
map, but it is **not architected as a load-bearing P0 component**. Concretely:

- No P0 component listed in §5 has the Agent Tool Layer as a required dependency for its own function.
- The Data Access Gateway, Deterministic Simulation Engine, Validation Service, and Promotion/Gate Service
  must all function correctly with zero agent involvement — a human or a script can drive every P0
  workflow through the same typed contracts an agent would eventually use.
- Exact activation gate criteria and the agent permission matrix are U-11, deferred in full to document 07
  (Agent & Orchestration Layer). This document fixes only the negative constraint: P0 does not depend on
  agents existing.

---

## 7. P0 build slice

Per Matrix row 57: "P0 proves the general contracts through the smallest useful vertical slice. Any
operationally first test case has zero semantic privilege. The conformance program MUST progressively
exercise heterogeneous assets, timeframes, multi-timeframe strategies, and execution contexts."

### 7.1 What P0 must prove

P0 is architecturally satisfied when the following path executes end-to-end through real (not stubbed)
components, for at least one `StrategyIR Candidate`:

```
SourceSnapshot -> ResearchRecord -> CampaignSpec -> StrategyIR Candidate
  -> SimulationRun -> ValidationArtifact -> StrategyArtifact
```

i.e., P0 stops at `StrategyArtifact`. Portfolio, Deployment, and Monitoring domains are out of P0's
critical path (they require ≥1 `StrategyArtifact` as a precondition and are not needed to prove the
research/validation spine).

### 7.2 Zero semantic privilege requirement

Whatever asset class, instrument, venue, or timeframe P0's first vertical slice happens to use MUST NOT
become an implicit default anywhere in the Deterministic Simulation Engine, Data Access Gateway, or
Validation Service. Concretely, this architecture requires:

- every market/timeframe/venue assumption used by the P0 slice is expressed through the explicit typed
  contracts named in §3.1 (`ExperimentDefinition`/`CampaignSpec`, execution/cost model — full schema is
  document 04's job per U-02/U-03), never as a hardcoded engine default;
- the conformance/test program (document 08's job to schedule) must add a second, materially different
  asset/timeframe/execution context before any P0 exit criterion can be marked satisfied, per Matrix row
  57's "progressively exercise heterogeneous" requirement and the Fixture Isolation Principle (Matrix
  §7.2, Constitution §2);
- this generalizes the anti-pattern found in Microsoft Qlib's CN-region-silent-default (per
  `docs/research/candidate-reports/PA-01-microsoft-qlib.md`, cited here as a negative precedent, not
  adopted code) and in Zipline-reloaded's hardcoded NYSE-session `minutes_per_day=390` default
  (`docs/research/prior-art/PA-10-reuse-decision-register.md` row PA-01-06) — both are cited as concrete
  evidence that "operationally first" defaults silently become semantic defaults if not architecturally
  blocked.

### 7.3 Exact P0/P1 acceptance thresholds

Deferred to document 08 (U-13). This document fixes the vertical-slice boundary and the zero-privilege
requirement; it does not fix pass/fail numeric thresholds.

---

## 8. Interfaces / API contracts between domains

Interfaces are expressed as artifact-typed contracts, not RPC signatures (which are document 04's job once
`StrategyIR` schema is fixed, per U-01). At the architecture level, each cross-domain boundary is a
one-directional, versioned handoff:

| From | To | Contract | Direction rule |
|---|---|---|---|
| Research Intelligence | Quant Laboratory | `ResearchRecord`/`ResearchHypothesis` referenced by a `CampaignSpec` | Quant Laboratory reads Research Intelligence's canonical records; never writes into them |
| Quant Laboratory (generator) | Quant Laboratory (simulation) | `StrategyIR Candidate` | Simulation reads the candidate; never mutates it |
| Quant Laboratory (simulation) | Quant Laboratory (validation) | `SimulationRun` | Validation reads runs; never re-executes or edits the ledger |
| Quant Laboratory (validation) | Promotion/Gate Service | `ValidationArtifact` | Gate service reads the artifact to decide promotion; never edits validation evidence |
| Promotion/Gate Service | Portfolio | `StrategyArtifact` | Portfolio reads immutable artifacts; never mutates them, only references them in a `PortfolioArtifact` |
| Portfolio / Quant Laboratory | Deployment & Monitoring | `PortfolioArtifact` / `StrategyArtifact` | Deployment reads and builds from artifacts; a `BuildArtifact` never rewrites its source artifact |
| Deployment & Monitoring | Research Intelligence / Quant Laboratory | `DecisionRecord`/`ResearchTrigger` | Feedback creates new downstream records; never mutates the historical artifacts that triggered it (Constitution §3.5) |
| Agent Tool Layer *(not P0)* | any domain | typed tool calls only, scoped per U-11's future permission matrix | No direct write path to any immutable artifact; agent output is always proposal material (Constitution §6) until compiled/validated |

---

## 9. Determinism and performance contract (language-agnostic)

Per U-08, the numerical-core implementation language (Python-only vs. a Rust hot path) is **PROVISIONAL —
unvalidated, owner: Danny**, resolved once the benchmark named in Matrix row 54/U-08 runs comparing
candidate implementations against the contract below. This architecture does not choose the language. It
requires, of whichever language(s) are chosen:

1. **Deterministic replay contract** (Constitution §7.1): under a declared deterministic mode, identical
   versioned input tuple + identical engine version + identical environment must reproduce an identical
   event ledger and metrics. This is a correctness requirement on the implementation, not a suggestion.
2. **Seedable stochastic contract** (Constitution §7.2): any non-deterministic-but-seedable path (search,
   bootstrap) must record its seed and full environment alongside an immutable input snapshot; a single run
   is never sufficient evidence — replicated or statistical evidence across runs is required before
   promotion.
3. **Non-replayable exclusion** (Constitution §7.3): live LLM inference and live market-data feeds cannot
   satisfy either contract above and MUST NOT be certified as numerical evidence regardless of seed
   recording.
4. **No external engine as truth store** (Constitution §8): whichever language/runtime is chosen, and
   whatever external engines are used for conformance comparison (e.g. NautilusTrader per
   `docs/research/candidate-reports/PA-01-nautilustrader.md`, REFERENCE + candidate conformance comparator
   only, pending LGPL legal review — not adopted as a dependency here), none may become a coequal or
   alternate source of numerical truth.

**Design precedent (reference only, not adopted code):** NautilusTrader's Deterministic Simulation Testing
pattern (seed-controlled runtime; identical observable behavior for identical seed+binary+config+platform)
and its shared-kernel backtest/live parity pattern (one code path serving both backtest and live, not two
reimplementations kept in sync) are cited as architecture patterns worth following for the Deterministic
Simulation Engine's own internal design, independent of the U-08 language decision and independent of any
decision to depend on NautilusTrader itself.

---

## 10. Venue/broker/data-vendor adapter isolation (U-03 boundary)

Full broker/venue normalization semantics (calendars, futures roll, session policy, currency) are document
04's job (U-03). This document fixes the isolation boundary those semantics must sit behind:

- Venue-specific and broker-specific code MUST be isolated behind an adapter interface with zero
  venue-specific logic in the Deterministic Simulation Engine's core, per Constitution §2 ("Market-specific
  and venue-specific behavior belongs in explicit typed contracts and metadata, never as an implicit engine
  default").
- **Design precedent (reference only, not adopted code):** vn.py's `BaseGateway` abstract-base-class
  pattern — an abstract adapter interface with zero venue-specific code in the shared core, validated
  across roughly 90 peer venue-adapter repositories per
  `docs/research/candidate-reports/PA-01-vnpy.md` — is cited as the strongest clean venue-adapter-isolation
  precedent found in this program's prior-art research (`docs/research/prior-art/PA-10-reuse-decision-register.md`
  row PA-01-08). vn.py's GUI-coupled core and thin core test coverage disqualify it from ADOPT/WRAP; only
  the isolation *pattern* is cited, not its code.
- MT5, and any other target/broker platform, is a deployment/conformance adapter only. It MUST NOT define
  Signal Current research semantics, data semantics, execution ontology, or `StrategyIR` (Constitution §8;
  Matrix row 44).

---

## 11. Validation-layer contract boundary (protecting against library-ontology leakage)

Per this sprint's explicit constraint and Matrix row 44's generalization (Constitution §10): no external
library's internal ontology may silently become Signal Current's domain model. This is architecturally
relevant now because `docs/research/prior-art/PA-10-reuse-decision-register.md` (row PA-05-01) lists
skfolio as an **ADOPT CANDIDATE** (not a final ADOPT) for CPCV/HRP/NCO/CVaR/CDaR, and explicitly flags:
"Adopting CPCV directly would silently import skfolio/scikit-learn's estimator, model-selection and split
ontology as Signal Current's implicit ValidationPlan model — that must be an explicit decision, not a side
effect."

This document fixes the boundary that decision must respect, without pre-deciding skfolio's ADOPT status
(out of this sprint's scope per the Requirements doc's "Out of Scope" section):

- The Validation & Statistical Controls Service (§5) exposes a Signal-Current-owned `ValidationPlan`
  contract (full schema is document 05's job). Any adopted third-party validation library — skfolio or
  otherwise — sits entirely behind this contract as an internal implementation detail, never exposed
  through the service's own interface.
- If skfolio's CPCV/HRP/NCO/CVaR/CDaR modules are adopted following the PA-10 register's own stated
  acceptance gates, they are wrapped, not surfaced — per PA-10's own recommendation ("place every adopted
  module including CPCV behind Signal-Current-owned contracts").
- The Portfolio Service's `PortfolioArtifact` contract (§3.1) is likewise insulated: any adopted allocator
  library's estimator/optimizer types never appear in the `PortfolioArtifact` schema itself.

---

## 12. Patterns and anti-patterns

| Pattern | Where used | Rationale / anchor |
|---|---|---|
| Typed artifact contracts between bounded domains, no shared mutable state | All cross-domain boundaries (§8) | Matrix row 3; prevents ontology leakage across domains |
| Append-only audit event log for every state transition | Audit/Event Log, Promotion/Gate Service | Constitution §9.1; Matrix row 53 |
| Single mediated data-access gateway enforcing zone boundaries | Data Access Gateway (§4) | Constitution §4; Matrix rows 26, 36 |
| Pluggable generator / fixed downstream evaluation path | Campaign/Generator Service (§5) | Matrix row 17 — search algorithm is not architecturally privileged |
| Adapter isolation for venue/broker/target-platform code | §10 | Constitution §2, §8; Matrix row 44; vn.py precedent (PA-10 row PA-01-08) |
| Wrap, don't surface, third-party validation/portfolio libraries | §11 | Matrix row 44 generalized (Constitution §10); PA-10 row PA-05-01 |
| Deterministic-replay-first design; seedable-stochastic as a distinct, lesser evidentiary tier | §9 | Constitution §7 |

**Anti-patterns (explicitly rejected):**

- Region/venue/timeframe silent defaults anywhere in core engine code — rejected per Constitution §2 and
  the Qlib CN-region and Zipline-reloaded NYSE-session precedents cited in §7.2.
- Treating an external engine's parity/regression pass as equivalent to Signal Current's own numerical
  evidence ("PARITY ORACLE"-style conflation) — rejected per Constitution §8; any such label previously
  found in PA-10 was already corrected against Matrix rows 1/16. No PARITY ORACLE label survives as an
  active disposition in `docs/research/prior-art/PA-10-reuse-decision-register.md` or any candidate
  report — that specific claim is verified true (zero occurrences in the PA-10 register and in
  `docs/research/candidate-reports/`). References to it remaining in this sprint's own scaffolding
  (`NORTH-STAR.md`, `INTAKE.md`, `01-REQUIREMENTS.md`) and in
  `Signal_Current_Specification_Set/CLAUDE_CODE_GREENFIELD_KICKOFF.md` (a historical research-input
  document, not authority, which as of this writing still lists PARITY ORACLE as an allowed disposition in
  its "Prior-art research program" section's proposed-disposition list) are historical references to a prior state, not live labels,
  and were not carried into this architecture.
- Autonomous agent-driven promotion of any material-risk-bearing artifact — rejected per Constitution §6
  and Matrix row 49; see TradingAgents' Portfolio-Manager-as-LLM-approval pattern
  (`docs/research/candidate-reports/PA-08-tradingagents.md`), cited here as a concrete negative precedent
  this architecture's Promotion/Gate Service (§5) must not replicate.
- Building a UI or dashboard ahead of the typed contracts it would sit on top of — rejected per Matrix row
  51 ("UI is an operator surface over contracts. No UI-first implementation... P0 proves numerical/semantic
  spine first"); this is also why `03-UI-SPEC.md` for this sprint is a stub, not a design.

---

## 13. Dependencies

No new third-party runtime dependency is promoted by this document. Per §11 and the sprint's own
constraint, no ADOPT decision is made here for skfolio or any other library — that decision belongs to
PA-10's own register acceptance gates (out of scope for this sprint per the Requirements document). Where
this document names external systems, it does so only as REFERENCE-only design precedent (NautilusTrader
§9, vn.py §10, Qlib/Zipline anti-patterns §7.2, TradingAgents anti-pattern §12) or as a future ADOPT
CANDIDATE whose gates have not yet run (skfolio, §11) — none of these are adopted as dependencies by this
architecture document.

---

## 14. PROVISIONAL items and resolution paths

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| U-08: numerical-core language (Python-only vs. Rust hot path) | PROVISIONAL — unvalidated | Danny | Resolved once the benchmark comparing candidate implementations against §9's determinism/performance contract runs (document 08's job to schedule) |
| U-09: worker queue/job granularity (specific product/technology) | PROVISIONAL — unvalidated | Danny | Resolved once an operational-evidence benchmark (not yet run) compares candidate queue/worker technologies against the durability/idempotency/content-addressing requirements this document assumes exist but does not name a product for |
| U-10: artifact-store product (local filesystem vs. S3-compatible) | PROVISIONAL — unvalidated | Danny | Resolved once a deployment-context decision (single-workstation vs. distributed-worker mode, per Matrix row 50) is made — this document requires only that whichever product is chosen present a content-addressed, immutable interface |
| U-11: agent harness activation gate and exact permission matrix | Deferred, not PROVISIONAL — explicitly out of P0 scope | Document 07 owner | Resolved when document 07 (Agent & Orchestration Layer) is drafted, per Matrix row 56's stated criterion: "activate after core APIs/artifacts/gates are stable enough to constrain agents" |
| U-01/U-02/U-03 (StrategyIR schema, execution semantics, broker/venue normalization) | Deferred, not PROVISIONAL — explicitly document 04's scope | Document 04 owner | Resolved when document 04 (Data Architecture & Strategy IR) is drafted; this document fixes only the boundary contracts in §3.1, §9, §10 |

No numeric constant is adopted as a Signal Current setting anywhere in this document. The only numeric
literals present are an external config-default value cited from prior-art research (§7.2's Zipline-reloaded
`minutes_per_day=390`) and a repo-count observation cited from prior-art research (§10's vn.py "roughly 90 peer
venue-adapter repositories"); neither is adopted as a Signal Current setting. Every deferred decision above is either a product/technology choice explicitly out of scope per
the Reconciliation Matrix (U-08/U-09/U-10/U-11) or a schema/semantics decision explicitly scoped to a later
document (U-01/U-02/U-03) — none is guessed here.

---

## 15. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Data/StrategyIR,
Validation, Portfolio/Deployment, and Agent specification work (Constitution §11). After freeze, amendment
requires an explicit ADR and version change.
