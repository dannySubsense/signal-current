# Signal Current — Implementation Roadmap

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, `docs/specs/02-system-architecture.md`,
`docs/specs/03-research-methodology.md`, `docs/specs/04-data-architecture-strategy-ir.md`,
`docs/specs/05-validation-statistical-controls.md`, `docs/specs/06-portfolio-deployment-monitoring.md`, and
`docs/specs/07-agent-orchestration-layer.md`. This is the eighth and final document of the canonical v1.0
spec set. Not yet independently reviewed. Frozen only after the full eight-document set clears Frank's
binding spec-gate.

**Provenance:** @planner.
**Editorial corrections:** @planner, 2026-09-05, per `05-REVIEW.md` gaps G3/G4.

**Primary question this document answers:** In what sequence do we build and prove the system without
violating the specification?

**Traceability convention:** every substantive clause cites a Reconciliation Matrix row, a Constitution
clause, or a document 02-07 section. Where this document generalizes beyond a single directly-matching
source, it is flagged inline as synthesis, consistent with the precedent in Constitution §10 and
Architecture §2. Design precedent from prior-art research is cited REFERENCE-only, cited here as evidence
for *sequencing logic*, never as a technology choice.

## 1. Scope and non-goals — read this before anything else

Per Matrix §7's explicit framing of U-08 ("need benchmarks before choosing"), U-09 ("operational choice,
not yet evidence-driven"), U-10 ("local filesystem vs S3-compatible is deployment-dependent"), and U-13
("must derive from frozen semantics and required heterogeneity coverage"), and per this sprint's explicit
orchestration instruction: **this document defines the SEQUENCE of decisions and gates — what benchmark
must run before U-08 can be decided, what precondition must exist before U-09/U-10 can be decided, what P0's
own completion criteria require before U-13's thresholds can be set. It does NOT invent:**

- the numerical-core implementation language (Python-only vs. a Rust hot path) — U-08;
- the worker queue/job-granularity technology — U-09;
- the artifact-store product (local filesystem vs. S3-compatible) — U-10;
- specific P0/P1 acceptance-threshold numbers, or the specific asset/timeframe combinations the
  conformance program will exercise beyond "at least one, then a second materially different one" — U-13.

Each of the above is tagged **PROVISIONAL — unvalidated**, owner **Danny**, with a named resolution
condition, in §8. This document also collects and sequences — without resolving — every PROVISIONAL item
already tagged in documents 04, 05, 06, and 07 (U-01a/b/c, U-02a/b/c, U-03, U-04, U-05, U-06, the
adverse-cost-stress magnitude, the robustness-family thresholds, U-07a/b, U-12, the diversification/
contribution thresholds, the incubation-stage durations, the `StrategyArtifactPassport.complexity` field
schema, and the agent-activation readiness checklist/permission matrix). §3 is the sequencing table for all
of these; §8 restates only this document's own four new items (U-08/U-09/U-10/U-13) in the standard
PROVISIONAL format.

**On document 07 §12's two implementation-technology items:** 07 §12 additionally lists two items —
the specific agent framework/SDK/orchestration product choice, and the specific tool-permission enforcement
technology — and names their owner as "Document 08 / implementation owner." These two are explicitly tagged
in 07 §12 itself as **"Deferred, not PROVISIONAL — implementation decision, out of [document 07's] scope,"**
not as PROVISIONAL items in the same sense as the list above. On review (05-REVIEW.md G4/Q3), this document
resolves the ambiguity as follows: these two items are genuinely this document's to sequence, since 07 §12
names Document 08 as owner and their resolution mechanism ("resolved during Implementation Roadmap or
build-phase tooling selection," "resolved during build-phase design of the Agent Tool Layer, once activation
(§7) is authorized") is a sequencing question, not a re-decision of document 07's permission-matrix content.
This document therefore picks up both items explicitly in §4 below, alongside Phase R4, rather than leaving
them uncollected. §3's "collects and sequences... documents 04, 05, 06, and 07" claim is read as including
these two 07 §12 items, sequenced in §4 rather than in the §3 phase table (because they are deferred
build-phase technology choices, not research-design-dependent PROVISIONAL items of the same shape as U-01
through U-13).

This document also does NOT re-decide:

- any StrategyIR/execution/data-normalization schema field (document 04's scope);
- any validation statistical default (document 05's scope);
- any portfolio/deployment tolerance, threshold, or auth protocol (document 06's scope);
- any agent permission-matrix content (document 07's scope) — it sequences *when* document 07's activation
  gate may be evaluated, not what that gate's content is.

**One clarification on this document's own identity, not to be confused with a related but distinct
artifact:** this is Signal Current's PRODUCT roadmap — what gets built, in what order, after the v1.0 spec
set freezes. It is not a roadmap for finishing this spec-drafting sprint itself. This spec sprint does not
have its own `PROGRESS.md`: it is a spec-only sprint producing the eight canonical documents (01-08) plus
the corrected PA-10 register, not a forge sprint implementing tracked slices against a frozen spec, so there
is no per-slice progress to track in the usual `PROGRESS.md` sense while this sprint is underway. Sprint-state
tracking in that sense begins once `/forge-start` runs against this frozen spec set, tracked in whatever
sprint folder that forge work opens — a separate document with a separate audience and separate success
criteria from this Roadmap.

## 2. P0 vertical-slice definition (Matrix row 57)

Per Architecture §7.1, P0 is architecturally satisfied when the following path executes end-to-end through
real (not stubbed) components, for at least one `StrategyIR Candidate`:

```
SourceSnapshot -> ResearchRecord -> CampaignSpec -> StrategyIR Candidate
  -> SimulationRun -> ValidationArtifact -> StrategyArtifact
```

This Roadmap adds the build-sequencing detail Architecture §7 intentionally left to this document.

### 2.1 What P0 proves and what it deliberately does not scope in advance

Per Matrix row 57: "P0 proves the general contracts through the smallest useful vertical slice. Any
operationally first test case has zero semantic privilege." Consequently:

1. **Whatever asset class, instrument, venue, or timeframe the P0 build happens to use first is an
   engineering starting point, not an architectural decision.** This Roadmap does not name that choice. Any
   illustrative example below is explicitly labeled as illustrative-only.

   *Illustrative-only, not a decision:* a build team might find it operationally convenient to start with a
   single liquid instrument on a single daily-bar timeframe, purely because it minimizes broker/venue
   normalization surface (document 04 §4) while the general contracts are first proven. This is cited only
   to make the "zero semantic privilege" instruction concrete — the actual choice belongs to whoever runs
   the build, not to this spec.

2. **The conformance program MUST progressively exercise heterogeneous assets, timeframes, multi-timeframe
   strategies, and execution contexts** after the first slice proves the spine (Matrix row 57). This is not
   optional follow-on work — per §2.2 below, no P0 exit criterion may be marked satisfied until a second,
   materially different asset/timeframe/execution context has also been exercised through the same spine.

3. **Precedent for scoping the first slice small, not exhaustive (REFERENCE only):** vn.py's `BaseGateway`
   adapter pattern was validated across roughly 90 peer venue-adapter repositories
   (`docs/research/candidate-reports/PA-01-vnpy.md`), but the pattern itself — an abstract adapter interface
   with zero venue-specific logic in the shared core — was provable with a small number of adapters, not all
   90 at once. This is cited as evidence for the general sequencing principle this Roadmap applies to P0: the
   adapter isolation already architected in document 04 §4.2/§10 can and should be proven with one or two
   heterogeneous examples first, then extended — not proven exhaustively before anything else is built.

### 2.2 P0 exit precondition (structural, not numeric)

P0 is not exit-eligible on the strength of one asset/timeframe combination alone. Per Matrix row 57 and
Architecture §7.2's zero-semantic-privilege requirement, a P0 exit claim requires, at minimum:

1. the end-to-end spine (§2 above) executing through real components for the first (operationally
   convenient) asset/timeframe/execution-context combination;
2. the same spine executing for at least one second, materially different asset/timeframe/execution-context
   combination — "materially different" meaning it exercises a different value along at least one of the
   dimensions Architecture §7.2 names (asset class, timeframe, venue, execution context), not a trivial
   parameter change within the same dimension;
3. no hardcoded default for either combination surviving anywhere in the Deterministic Simulation Engine,
   Data Access Gateway, or Validation Service (Architecture §7.2) — i.e., the second combination must not
   require special-casing engine code, only supplying different values through the typed contracts already
   fixed in documents 02/04.

The exact number of combinations beyond "at least two, materially different" and the exact pass/fail
numeric thresholds for what "executing" means at each step are U-13, sequenced in §3 and §8 below — this
section fixes the structural precondition, not the acceptance numbers.

### 2.3 Portfolio, Deployment, and Monitoring are out of P0 by construction

Per Architecture §7.1: P0 stops at `StrategyArtifact`. Portfolio, Deployment, and Monitoring domains require
≥1 `StrategyArtifact` as a precondition (Architecture §2 domain table) and are therefore necessarily
sequenced after P0's exit, never concurrently as a P0 dependency. This Roadmap does not compress that
ordering — a `PortfolioArtifact` cannot exist before at least one `StrategyArtifact` does, by definition
(Architecture §3).

## 3. Sequenced resolution of every PROVISIONAL item from documents 04-07

This is a dependency-ordered list, not a resolution. Each phase names what must exist or run before the
next phase's PROVISIONAL items can be meaningfully resolved — none is resolved here.

### Phase R1 — StrategyIR / execution-semantics / broker-venue schema design (U-01a/b/c, U-02a/b/c, U-03)

**Precondition:** none upstream within this list; this is the earliest schema-design phase, because every
later PROVISIONAL item in Phases R2-R4 either consumes a StrategyIR-shaped object or an
ExecutionContext/CostModel-shaped object as its own input.

1. **U-01a (StrategyIR field-by-field schema)** must resolve first among this phase's items, per Data
   Architecture §8's own stated resolution condition: "dedicated StrategyIR schema design session with
   golden-example worked strategies (at least one single-timeframe, single-asset example and one
   multi-timeframe/multi-stream example)." This session's golden examples should be drawn from, or aligned
   with, the same first-and-second P0 combinations named in §2.2 above, so the schema design and the P0
   vertical slice inform each other rather than running as two disconnected efforts.
2. **U-01b (serialization format)** and **U-01c (hash algorithm/canonicalization)** resolve immediately
   after U-01a, per Data Architecture §8's own stated ordering ("resolved alongside U-01a, once schema shape
   is fixed enough to choose a serialization that preserves content-addressing stability").
3. **U-02a (order-timing rule)** and **U-02b (stop/target collision-resolution rule)** resolve via the
   dedicated execution-semantics ADR Data Architecture §8 names, and may run concurrently with U-01a/b/c
   (different design surface — execution semantics vs. IR shape) but must both complete before Phase R2's
   cost-realism/validation items can be tested against real strategies, since a `SimulationRun`
   (Architecture §3.1) cannot execute without both a resolved StrategyIR shape and a resolved
   execution-semantics contract.
4. **U-02c (cost-model defaults)** resolves in the same execution-semantics ADR as U-02a/b, per Data
   Architecture §8 — and, per Validation §14's cross-reference, the adverse-cost-stress magnitude (Validation
   §8) is resolved in this same ADR, not as a separate second-guessed number.
5. **U-03 (broker/venue normalization field schema)** resolves via a dedicated design session that Data
   Architecture §8 explicitly permits combining with U-01a's session, "spanning at least two materially
   different venues/asset classes" — this is the same heterogeneity requirement §2.2 above already imposes
   on the P0 exit precondition, so U-03's design session and the P0 second-combination proof should draw on
   the same worked examples where practical.
6. **`StrategyArtifactPassport.complexity` field schema (document 06 §3, §16)** — the exact schema for the
   structural complexity descriptor Portfolio/Deployment §3 fixes only as "a recorded, inspectable field"
   without fixing its shape — resolves alongside U-01a in this same phase, since `complexity` is read off
   the same `StrategyIR` shape U-01a's golden examples fix; there is no reason to run this as a
   disconnected second schema-design effort once U-01a's session already has the worked examples in hand.

**Why this phase must complete before Phase R2:** Validation's `ValidationPlan` sub-decisions (Validation
§§3-10) each reference a `SimulationRun`, a `CostModel`, or an `ExecutionContext` by ID — none of Validation's
own PROVISIONAL items (U-04/U-05/U-06, adverse-cost-stress) can be meaningfully tested against real
strategies until Phase R1 produces a `StrategyIR` shape and execution-semantics contract those
`SimulationRun`s can actually be generated against.

### Phase R2 — Validation statistical-defaults research design (U-04, U-05, U-06, robustness-family thresholds)

**Precondition:** Phase R1 complete (a stable StrategyIR shape and execution-semantics contract must exist
so real `SimulationRun`s can be produced to test statistical methods against).

1. **U-04 (minimum-sample sizing, walk-forward window sizing, purge/embargo sizing)** resolves via "a
   dedicated research-design pass deriving minimum-sample requirements per the heterogeneous asset/
   timeframe/event-structure coverage the conformance program (Matrix row 57) will actually exercise"
   (Validation §14). This explicitly depends on the conformance-coverage plan this Roadmap's §2.2 fixes the
   *structure* of (at least two materially different combinations) — U-04's research design should be run
   against the actual combinations the P0/P1 conformance program selects, not a hypothetical general case.
2. **U-06 (CPCV applicability matrix, fold/purge-embargo sizing)** resolves via a dedicated research-design
   pass (Validation §14), informed by whichever CPCV reference implementation (e.g., skfolio's
   `CombinatorialPurgedCV`, pending its own PA-10 acceptance gates — see Phase R-Prior-Art below) is used for
   comparison. This item's dependency on the skfolio gate sequence is explicit: U-06 should not be finally
   resolved before Phase R-Prior-Art (below) determines skfolio's ADOPT status, though the research-design
   work itself (defining the applicability matrix's input dimensions) can begin in parallel.
3. **U-05 (multiple-testing diagnostic defaults and cutoffs)** resolves via a dedicated statistical-methods
   design pass "evaluating DSR/reality-check/bootstrap-style methods against this program's actual
   campaign/trial structures" (Validation §14) — this requires at least one real `CampaignSpec` with real
   trial-count bookkeeping (Research Methodology §9) to exist, meaning U-05's resolution is naturally
   sequenced after Phase R1 and alongside or after the P0 vertical slice has produced at least one real
   campaign to evaluate against, not purely as an abstract statistical exercise.
4. **Robustness-family applicability/thresholds (Validation §9/§14)** resolve "alongside U-04, since family
   applicability depends on the same asset/timeframe/event-structure context U-04's research design must
   characterize" (Validation §14) — sequenced together with U-04, not independently.

### Phase R3 — Portfolio/deployment thresholds and parity ADR (U-07a/b, diversification/contribution
thresholds, incubation-stage durations)

**Precondition:** Phase R2 substantially complete (at least U-04, since the diversification/contribution
thresholds and incubation-stage durations below explicitly depend on the same research-design work), AND at
least one `StrategyArtifact` must exist (Portfolio/Deployment §2's own stated precondition — this document
does not relax it) — meaning this phase cannot begin in earnest before the P0 vertical slice (§2 above) has
actually produced a promoted `StrategyArtifact`.

1. **Behavioral-diversification "sufficiently diversified" threshold and contribution-testing
   "non-redundant" threshold** (Portfolio/Deployment §16) resolve together via "a dedicated
   portfolio-qualification research-design pass, informed by whichever allocator/diversification reference
   implementation (e.g., skfolio) is used for comparison" — again dependent on Phase R-Prior-Art's skfolio
   gate outcome for its reference-implementation input, though not blocked on it if a non-skfolio comparison
   is used instead.
2. **Incubation-stage durations, evidence-sufficiency criteria, and small-risk/graduated capital thresholds**
   (Portfolio/Deployment §16) resolve via "a dedicated incubation/risk-graduation design pass, informed by
   whichever concrete deployment target and capital model the Roadmap selects" — this explicitly depends on
   a later phase of *this* Roadmap (§4 below, when/if a target platform is selected), since Portfolio/
   Deployment §1 already states "whether/when a specific target platform... is implemented is a Roadmap
   decision."
3. **U-07a/U-07b (target-platform parity tolerance values and exhaustive discrepancy-class enumeration)**
   resolve via "a dedicated target-platform parity ADR once at least one concrete target platform is
   selected in the Roadmap" (Portfolio/Deployment §16) — this is explicitly sequenced after a target-platform
   selection decision this Roadmap has not made (see §4 below); it cannot resolve before that selection
   happens, because the taxonomy's per-class tolerance values are inherently platform-specific.
4. **U-12 (authentication/authorization protocol)** resolves via "a dedicated auth/authz design pass once a
   deployment target and user model are chosen (depends on Roadmap sequencing)" (Portfolio/Deployment §16)
   — same dependency as U-07a/b: sequenced after a target-platform/deployment-model decision this Roadmap
   defers (§4 below).

### Phase R4 — Agent & Orchestration activation gate (U-11)

**Precondition:** per Architecture §6 and Agent-Orchestration §7.2's own stated structural contract, this
phase's precondition is that the typed contracts and promotion gates from documents 04/05/06 are
**already implementation-stable and already gate-enforced for non-agent (human or script) callers** —
not merely spec-drafted. Concretely: Phases R1-R3's PROVISIONAL items do not all need to be numerically
resolved, but the contracts they attach to (StrategyIR, ValidationPlan, PortfolioPlan,
HumanAuthorizationRecord) must already be built, and their promotion gates must already be exercised
successfully by non-agent callers, before this phase's readiness checklist can even be evaluated.

1. The exact agent-activation readiness checklist/scorecard (Agent-Orchestration §12) is defined "once
   documents 04/05/06's typed contracts are implementation-stable enough to constrain against" — this is
   inherently a post-P0, likely post-P1, decision. This Roadmap does not shorten that dependency chain.
2. The exact per-role permission matrix (Agent-Orchestration §12) resolves alongside the readiness checklist,
   "once concrete agent roles are proposed against stable typed contracts."

**This Roadmap's own contribution to Phase R4 beyond restating documents 02/07:** naming it explicitly as
Phase R4 — the *last* of the four resolution phases, strictly after Phases R1-R3's contract-stabilization
work, never folded into or run concurrently with P0/P1 build work as if it were a parallel workstream with
equal priority. See §5 below for the fuller statement of this sequencing rule.

### Phase R1-R4 summary table

| Phase | PROVISIONAL items resolved | Precondition | Resolution mechanism (already named in source doc) |
|---|---|---|---|
| R1 | U-01a, U-01b, U-01c, U-02a, U-02b, U-02c, U-03, `StrategyArtifactPassport.complexity` schema | None (earliest) | Dedicated schema/execution-semantics design sessions and ADR (Data Architecture §8; Portfolio/Deployment §16) |
| R2 | U-04, U-05, U-06, robustness-family thresholds | R1 complete | Dedicated statistical/research-design passes (Validation §14) |
| R3 | U-07a, U-07b, U-12, diversification/contribution thresholds, incubation-stage durations | R2 substantially complete + ≥1 StrategyArtifact exists + (for U-07a/b, U-12) a target platform selected (§4) | Dedicated portfolio-qualification/incubation-design passes and target-platform parity/auth ADRs (Portfolio/Deployment §16) |
| R4 | U-11 (activation checklist + permission matrix) | R1-R3's underlying contracts implementation-stable and gate-enforced for non-agent callers | Post-P0/P1 readiness evaluation (Agent-Orchestration §12) |

### U-08 sequencing relative to Phases R1-R4

Per Architecture §9 and this document's own §8 below: **U-08 (numerical-core language) should run its
benchmark against a fixed, small P0 contract surface, not the full eventual system.** Concretely, the U-08
benchmark should be run once Phase R1's StrategyIR shape and execution-semantics contract are stable enough
to define a fixed benchmark surface (a small number of representative StrategyIR candidates and
SimulationRun workloads), but it does not need to wait for Phases R2-R4 — language choice is a
determinism/performance question (Architecture §9), not a statistical or agent-permission question, so it
is sequenced immediately after Phase R1 rather than after R2-R4.

### U-13 sequencing relative to Phases R1-R4 and §2

Per Architecture §7.3 and this document's own §8 below: **U-13 (exact P0/P1 acceptance thresholds) can only
be set once P0's own scope (§2 above) and the heterogeneity-coverage requirement (Matrix row 57, §2.2 above)
are both fixed** — which this document does fix structurally in §2, without fixing the numeric thresholds
themselves. U-13's numeric thresholds are therefore sequenced after §2's structural definition is agreed
(which this document provides) but do not additionally wait on Phases R1-R4's schema/statistical/portfolio
resolution work, except insofar as U-13's thresholds for *validation* pass/fail criteria specifically will
naturally reuse whatever U-04/U-05/U-06 (Phase R2) research design produces, once available.

## 4. Agent & Orchestration Layer sequencing (Matrix row 56; Agent-Orchestration §0, §7)

Restating, not relaxing, Agent-Orchestration §7's own framing: the Agent & Orchestration layer activates
**strictly after** P0's typed contracts (StrategyIR, ValidationPlan, PortfolioPlan, HumanAuthorizationRecord)
are stable and already gate-enforced for non-agent callers. This Roadmap names this as **Phase R4 above** —
a distinct, later phase, never folded into P0 or run as a concurrent-priority workstream alongside it. No P0
or P1 milestone in this Roadmap depends on the Agent & Orchestration layer existing (Architecture §6); the
inverse dependency direction (Agent & Orchestration depends on P0/P1's contracts being stable) is the only
one this Roadmap recognizes, consistent with Matrix row 56 and Agent-Orchestration §7.1's explicit
instruction not to argue for earlier activation.

### 4.1 Two implementation-technology items deferred from document 07 §12

Agent-Orchestration §12 names two items as "Deferred, not PROVISIONAL — implementation decision, out of
[document 07's] scope," owned by "Document 08 / implementation owner":

1. **Specific agent framework/SDK/orchestration product choice** — per 07 §12, "resolved during
   Implementation Roadmap or build-phase tooling selection, not spec-time." This Roadmap sequences this
   choice as build-phase tooling selection work that follows Phase R4's activation gate (§4 above) — it is
   an implementation detail of *how* an already-authorized agent role is technically run, not a precondition
   for Phase R4's readiness checklist itself. It may be evaluated in parallel with the tail end of Phase R4
   once concrete agent roles are proposed (Phase R4 item 2), but no framework/SDK is selected, and no agent
   code is built against one, before Phase R4's activation gate (Agent-Orchestration §7.2's contract-stability
   precondition) is satisfied.
2. **Specific tool-permission enforcement technology (capability tokens, RBAC product, sandboxing
   mechanism)** — per 07 §12, "resolved during build-phase design of the Agent Tool Layer, once activation
   (§7) is authorized." This Roadmap sequences this strictly after Phase R4's activation gate, consistent
   with 07 §12's own stated condition — the enforcement *technology* is a build-phase choice that implements
   the per-role permission matrix (Phase R4 item 2) once that matrix itself is defined; it does not precede
   or substitute for defining the matrix's content.

Neither item is resolved by this document — consistent with §1's own PROVISIONAL/deferred-item convention,
this Roadmap sequences *when* each choice may be made without making it. Both are picked up explicitly here,
alongside Phase R4, because 07 §12 names Document 08 as their owner; this is the disposition this Roadmap
adopts for the review's Q3/G4 finding (05-REVIEW.md §6), rather than leaving the two items uncollected.

## 5. Prior-art dependency sequencing — skfolio (Phase R-Prior-Art)

Per Architecture §11, Validation §4/§12, and Portfolio/Deployment §4.2: skfolio is cited across three
documents as the ADOPT CANDIDATE reference implementation for CPCV/HRP/NCO/CVaR/CDaR, but its status per the
Prior-Art Register (PA-10, row PA-05-01) is **ADOPT CANDIDATE, not final ADOPT** — its test suite was never
executed per Sol's finding.

This Roadmap sequences skfolio's own register-stated acceptance gates as an explicit phase — **Phase
R-Prior-Art** — that must complete *before* any Validation or Portfolio work formally depends on skfolio
being adopted (as opposed to merely being cited as a design reference):

1. Pin the specific skfolio version under evaluation.
2. Execute skfolio's own test suite (the specific gap Sol's finding identifies — this has not yet happened).
3. Hand-compute fixtures independently and verify skfolio's CPCV/HRP/NCO/CVaR/CDaR outputs against those
   fixtures, not merely against skfolio's own internal tests.
4. Verify cited statistical claims (e.g., López de Prado sourcing for `CombinatorialPurgedCV`) against
   primary sources.
5. Audit license terms.
6. Wrap the adopted modules entirely behind Signal-Current-owned contracts (`ValidationPlan.cpcv`,
   `AllocationDecision`), per Architecture §11, Validation §12, and Portfolio/Deployment §4.2's identical
   wrap-don't-surface requirement.

**Precedent this six-step sequence follows (REFERENCE only, not a technology endorsement):**
`docs/research/candidate-reports/PA-05-skfolio.md` and the PA-10 register's own gate list for this exact
library are the direct source of this six-step sequence — this Roadmap does not invent a new gate list; it
sequences the one PA-10 already states as an explicit Roadmap phase, per this sprint's instruction to "cite
as a template for how ADOPT CANDIDATE → ADOPT sequencing should look."

**Where this phase sits relative to R1-R4:** Phase R-Prior-Art can begin at any point (it does not depend on
Signal Current's own schema work), but its *completion* is a precondition specifically for: Phase R2's U-06
final resolution (if skfolio is the chosen CPCV reference), and Phase R3's diversification/contribution
threshold resolution (if skfolio is the chosen allocator reference). If skfolio's acceptance gates are not
run, or skfolio fails them, U-06 and the Phase R3 threshold items must use an alternative reference
implementation or a from-scratch research design — this Roadmap does not make skfolio adoption mandatory,
only sequences its gate if it is pursued.

## 6. What does NOT block P0 (Matrix row 58)

Per Matrix row 58: "New source ingestion continues, but it cannot delay the specification/build gates. The
primary milestone is a trustworthy research laboratory, not corpus size." This Roadmap states explicitly, so
a future session does not silently treat this as a P0 blocker:

1. **Corpus growth (new `SourceSnapshot` ingestion, new `ResearchRecord` extraction) continues in parallel
   with P0/Phase R1-R4 build work, but is never a precondition for any P0 exit criterion (§2.2), any Phase
   R1-R4 resolution, or Frank's binding gate (§7 below).** A future session must not read "we need a larger
   research corpus" as a reason to hold P0's own build or any Phase R1-R4 item open.
2. This is consistent with Architecture §2's framing that Research Intelligence's P0 scope is "minimal —
   only enough to prove SourceSnapshot → ResearchRecord promotion, not full ingestion breadth." Corpus
   breadth is explicitly not a P0 or Phase R1-R4 success metric.
3. Symmetrically, the reverse also holds: P0/Phase R1-R4 build work never blocks ongoing source ingestion —
   the two proceed independently, per Matrix row 58's framing that ingestion "continues" rather than pauses.

## 7. Frank's binding gate on this document set (Reconciliation Matrix §9, Freeze Rule)

Restating the Reconciliation Matrix's own Freeze Rule (§9), unchanged and not loosened by anything in this
document:

> After the eight documents are drafted: **Independent review → contradiction/coverage repair → v1.0 freeze
> → implementation.** Until then, all decisions remain specification work.

This Roadmap document — including its P0 vertical-slice definition (§2), its Phase R1-R4 sequencing (§3),
its Agent-Orchestration sequencing (§4), and its skfolio gate sequencing (§5) — **does not itself authorize
implementation to begin.** Per this sprint's own North Star success criteria and the Requirements document's
US-17: the full eight-document set (01-08) plus the corrected PA-10 register must first pass Frank's single
binding spec-gate verdict (PASS/FAIL/HALT, no conditional pass, no manual override), and then clear Danny's
independent approval, before any Phase R1 (or earlier) build work is authorized to start. No document in this
set, including this one, self-certifies its own readiness for implementation.

## 8. PROVISIONAL items introduced by this document (U-08, U-09, U-10, U-13)

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| **U-08** — Numerical-core implementation language (Python-only vs. Rust hot path) | PROVISIONAL — unvalidated | Danny | Benchmark comparing candidate implementations against Architecture §9's determinism/performance contract, run against a fixed, small P0 contract surface (per §3's "U-08 sequencing" above) once Phase R1's StrategyIR shape and execution-semantics contract are stable — not against the full eventual system, and not before Phase R1 produces a fixed surface to benchmark against |
| **U-09** — Worker queue/job-granularity technology | PROVISIONAL — unvalidated | Danny | Operational-evidence benchmark comparing candidate queue/worker technologies against the durability/idempotency/content-addressing requirements Architecture §14 already assumes exist but does not name a product for; this benchmark has not yet run and this document does not schedule it ahead of Phase R1, since queue technology does not block schema design, but it must run before any distributed-worker deployment topology (Matrix row 50) is committed to |
| **U-10** — Artifact-store product (local filesystem vs. S3-compatible) | PROVISIONAL — unvalidated | Danny | A deployment-context decision (single-workstation vs. distributed-worker mode, per Matrix row 50) must be made first; this document does not make that decision, only notes that whichever product is chosen must present a content-addressed, immutable interface (Data Architecture §5) regardless of which mode is selected |
| **U-13** — Exact P0/P1 acceptance thresholds and benchmark/conformance coverage (exact numeric pass/fail criteria, exact number and identity of heterogeneous combinations beyond "at least two, materially different") | PROVISIONAL — unvalidated | Danny | Set once §2's structural P0 scope and heterogeneity-coverage requirement (fixed by this document) are agreed, and informed by whichever numeric research-design outputs Phase R2 (U-04/U-05/U-06) produces for validation-specific thresholds; this document fixes the structure those thresholds must satisfy (§2.2), not the numbers themselves |

No numeric constant (a specific benchmark result, a specific queue/store product name, a specific
threshold number, a specific count of conformance combinations beyond "at least two") is introduced anywhere
in this document. Each PROVISIONAL item above is a technology/threshold decision the Reconciliation Matrix
itself flags (U-08/U-09/U-10/U-13) as requiring benchmark or research-design work this document does not
substitute for — none is guessed here.

## 9. Consistency check against Constitution, Architecture, Research Methodology, Data Architecture,
Validation, Portfolio/Deployment, and Agent-Orchestration

This document was checked for contradiction against `01-constitution.md`, `02-system-architecture.md`,
`03-research-methodology.md`, `04-data-architecture-strategy-ir.md`,
`05-validation-statistical-controls.md`, `06-portfolio-deployment-monitoring.md`, and
`07-agent-orchestration-layer.md` in full. No conflict was found: every clause above sequences a resolution
path or exit precondition those seven documents already establish or explicitly defer to this document
(Architecture §7, §9, §14; Data Architecture §8; Validation §14; Portfolio/Deployment §1, §16;
Agent-Orchestration §0, §7, §12), without contradicting or silently re-deciding any of their fixed clauses.
In particular:

- this document does not re-decide any PROVISIONAL item's eventual value — it sequences the order of
  resolution only (§3);
- this document does not accelerate Agent & Orchestration activation ahead of P0/P1 contract stability,
  consistent with Architecture §6 and Agent-Orchestration §7.1's explicit instruction (§4 above);
- this document does not pre-decide skfolio's ADOPT status — it sequences the register's own stated
  acceptance gates as an explicit phase (§5), consistent with Architecture §11's, Validation §12's, and
  Portfolio/Deployment §4.2's shared "not this sprint's scope" framing;
- this document does not authorize implementation to begin — Frank's binding gate and Danny's approval
  remain required (§7), per the Reconciliation Matrix's own Freeze Rule (§9), unchanged.

No HALT condition applies.

## 10. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during independent review
(Constitution §11), and MUST be revised once any Phase R1-R4 item, U-08, U-09, U-10, U-13, or either of the
two document-07-§12 implementation-technology items (§4.1) is actually resolved — at that point this
document is amended (not silently superseded) to replace the relevant PROVISIONAL tag or sequencing entry
with a cited, versioned decision. After freeze, amendment requires an explicit ADR and version change.
