# Signal Current — Portfolio, Deployment & Monitoring Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, `docs/specs/02-system-architecture.md`,
`docs/specs/04-data-architecture-strategy-ir.md`, and `docs/specs/05-validation-statistical-controls.md`.
Not yet independently reviewed. Frozen only after the full eight-document set clears Frank's binding
spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gaps G2/G11/G13. @architect, 2026-09-05, per Frank's spec-gate attempt-4 fix 2 — widened the U-12 PROVISIONAL row (§16) to also cover `AuditActor.actorId` authenticity and `orchestrationSessionId` minting/verification (document 04 §5.4), cross-referenced from document 04 §8 and document 07 §12. @architect, 2026-09-05, per Frank's spec-gate attempt-5 fix 2 — split the widened U-12 row's resolution condition (§16) so the actor/session-verification sub-items resolve in Phase R1 (not gated on a deployment target/user model) while the HumanAuthorizationRecord identity-provider/protocol sub-item resolves in Phase R3; matched the identical wording into document 04 §8 and document 07 §12. @architect, 2026-09-05,
per Sol's cold review (target SHA 99d3673) — widened the U-12 row (§16) a second dimension: it previously
covered only agent-side actor/session authenticity (`AuditActor.actorId`, `orchestrationSessionId`); it now
also covers resolving `AuditActor.controllingPrincipalId` (document 04 §5.4), the mechanism that lets
document 05 §2.1 items 1 and 3 (not only item 2) check whether two human `actorId`s, or a human and an
agent's session-initiating human, are the same natural person. Re-ran §15's consistency check.

**Primary question this document answers:** How do validated strategies become portfolios, builds,
deployments, and monitored live systems?

**Traceability convention:** every substantive clause cites a Reconciliation Matrix row, a Constitution
clause, or an Architecture/Data-Architecture/Validation section. Where this document generalizes beyond a
single directly-matching source, it is flagged inline as synthesis, consistent with the precedent in
Constitution §10 and Architecture §2. Design precedent from prior-art research is cited REFERENCE-only,
never as an adopted dependency.

## 1. Scope and non-goals — read this before anything else

Per Matrix §7's explicit framing of U-07 ("exact equality may be impossible across execution engines") and
U-12 ("required for risk-bearing deployment"), and per this sprint's explicit orchestration instruction:
**this document defines the CONTRACT — what a `PortfolioPlan` must record, what a target-platform parity
discrepancy taxonomy must structurally cover, what the incubation lifecycle and human-authorization gate
must operationally mean — that the eventual exact tolerance values and exact auth/authz protocol must
satisfy once dedicated design work produces them. It does NOT invent:**

- a specific parity tolerance percentage or numeric discrepancy threshold for any difference class (U-07);
- a complete, final discrepancy taxonomy (the structural categories are fixed here; the exhaustive
  enumeration and per-category tolerance values are not);
- a specific authentication/authorization protocol, identity provider, or technical mechanism (U-12);
- specific incubation-stage durations, capital thresholds, or graduation criteria numbers.

Each of the above is tagged **PROVISIONAL — unvalidated**, owner **Danny**, with a named resolution
condition, in §16. Per this repo's `CLAUDE.md` Research Data Integrity rules, an unsourced number in a
research/data/deployment path is a hypothesis, not a setting; none is smuggled in here as a setting.

This document also does NOT re-decide:

- `StrategyIR`/`ExecutionContext`/`CostModel` field-level schema (document 04's scope; this document
  consumes those contracts as given);
- validation statistical defaults or the `ValidationPlan` contract itself (document 05's scope; this
  document requires a cleared `ValidationPlan` as a precondition, per §2 below);
- agent permission matrix (U-11, document 07's job);
- whether/when a specific target platform (e.g. MT5) is implemented — that is a Roadmap (document 08)
  decision, per Matrix row 44 ("Whether it is implemented early is a roadmap decision, not an architectural
  privilege").

**The one exception to "no provisional principle" in this document:** the requirement that a human, never
an agent, authorizes material-risk promotion (§7) is Constitution-locked (Constitution §6, §1 item on agent
authority boundary) and is NOT provisional. Only its technical implementation protocol is provisional.

## 2. Precondition: a StrategyArtifact must have cleared ValidationPlan

Per Constitution §3 item 3 ("only a candidate that completes required validation and promotion gates
becomes an immutable StrategyArtifact") and Validation §2 (`ValidationPlan.status` reaching
`'promotion-gated'`): no artifact enters this document's scope — Portfolio qualification, Build,
Deployment, Incubation — unless it is already an immutable `StrategyArtifact` whose `ValidationPlan` has
reached `status: 'promotion-gated'` (Validation §2). This document does not re-run or second-guess that
gate; it consumes its outcome as a precondition, per Architecture §8's "Promotion/Gate Service reads the
artifact to decide promotion; never edits validation evidence."

## 3. StrategyArtifact registry — the passport (Matrix rows 37-38)

Per Matrix row 37: "Vault" is a UX name only, not a core domain requirement — the underlying requirement is
an **immutable StrategyArtifact registry/passport**. Per Matrix row 38 and Architecture §3.1, every promoted
`StrategyArtifact` carries provenance, IR, data zones, campaign/search budget, costs, validation evidence,
complexity, regimes, compatibility, and decision history.

```typescript
/**
 * The registry entry ("passport") for one immutable StrategyArtifact, as consumed by this document's
 * Portfolio/Deployment/Monitoring domain. Field-level types for irRef/validationPlanRef/etc. are owned by
 * documents 04/05; this document fixes only that every field below MUST be present and readable before
 * the artifact is eligible for Portfolio qualification (§4) or Build (§5).
 */
interface StrategyArtifactPassport {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  /** Reference to the immutable StrategyIR Candidate this artifact promotes, per Data Architecture §2.4. */
  irRef: string;
  /** Full provenance chain: SourceSnapshot -> ResearchRecord -> CampaignSpec -> ... per Architecture §3. */
  provenanceChain: string[];
  /** The DataSnapshot zone(s) this artifact was developed/validated against (exploration/validation/
   *  lockbox), per Architecture §4 and Validation §11. */
  dataZonesUsed: Array<'exploration' | 'validation' | 'lockbox'>;
  /** CampaignSpec search-budget bookkeeping this artifact descends from, per Research Methodology §9. */
  campaignBudgetRef: string;
  /** Versioned CostModel/ExecutionContext reference this artifact was validated against, per Data
   *  Architecture §3 and Validation §8. */
  costModelRef: string;
  /** Reference to the cleared ValidationPlan (status: 'promotion-gated'), per Validation §2. */
  validationPlanRef: string;
  /** Structural complexity descriptor (e.g., primitive count, dimension count) — exact schema is a
   *  future amendment; this document fixes only that complexity is a recorded, inspectable field, not
   *  an unrecorded property of the IR. Exact schema is **PROVISIONAL — unvalidated, owner Danny** (§16),
   *  resolution condition tied to whichever schema-design session addresses `StrategyArtifactPassport`'s
   *  `complexity` field — most naturally the same U-01a schema-design session document 08 §3 Phase R1
   *  sequences for `StrategyIR`, since complexity descriptors are read off the same IR shape. */
  complexity: Record<string, unknown>;
  /** Regime coverage this artifact's validation evidence actually spans, per Validation §9's regime-stress
   *  robustness family where applicable. */
  regimeCoverage: Record<string, unknown>;
  /** What this artifact declares itself compatible with (asset classes, timeframes, execution contexts it
   *  has been validated against) — never an implicit assumption, per Architecture §7.2. */
  compatibility: Record<string, unknown>;
  /** Append-only history of decisions concerning this artifact (promotions, portfolio inclusions, build
   *  requests, deployment/rollback events), each a DecisionRecord reference per Architecture §3.1. */
  decisionHistory: string[];
  /** Immutable; retraining creates a new lineage child artifact with its own passport, never a mutation
   *  of this one (Constitution §3.6; Matrix row 48). */
  supersededBy?: string;
}
```

This registry is a domain requirement regardless of what UX name (if any) exposes it to a human operator.
No UI-first implementation of this registry is implied or required by this document (Matrix row 51;
Architecture §12).

## 4. Portfolio qualification (Matrix rows 39-42)

The Portfolio Service (Architecture §5) combines one or more `StrategyArtifact`s into an immutable
`PortfolioArtifact` (Architecture §3.1) only after the qualification process below produces a
`PortfolioPlan` recording every decision as evidence, not as a bare pass/fail boolean — the same structural
discipline Validation §2 already establishes for `ValidationPlan`.

```typescript
interface PortfolioPlan {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  /** The StrategyArtifact passports this plan considers for inclusion. */
  candidateArtifactRefs: string[];
  /** Version of this plan's own decision-rule set — never silently redefined in place. */
  planVersion: string;

  behavioralDiversification: BehavioralDiversificationDecision;
  sharedCapitalSimulation: SharedCapitalSimulationDecision;
  allocation: AllocationDecision;
  contributionTesting: ContributionTestingDecision;

  /** Every decision above must resolve before this plan is eligible to gate PortfolioArtifact promotion. */
  status: 'draft' | 'complete' | 'promotion-gated';
}
```

### 4.1 Behavioral diversification (Matrix row 39)

Per Matrix row 39: "Portfolio qualification must measure signal, return, exposure, drawdown, regime and
latent-family overlap. Parameter/timeframe diversity alone does not count as edge diversity." Per
Constitution §5 item 9: "HRP is a first-class portfolio method. Portfolio decisions additionally require
behavioral-diversification and shared-account evidence — parameter/timeframe diversity alone is not edge
diversity."

```typescript
interface BehavioralDiversificationDecision {
  /** Each named overlap dimension MUST be measured — a plan that measures only parameter/timeframe
   *  diversity and omits the rest does not satisfy Matrix row 39. */
  overlapMeasures: {
    signalOverlap: unknown;
    returnOverlap: unknown;
    exposureOverlap: unknown;
    drawdownOverlap: unknown;
    regimeOverlap: unknown;
    latentFamilyOverlap: unknown;
  };
  /** Parameter/timeframe diversity alone is recorded for context but MUST NOT, by itself, satisfy this
   *  decision — fixed now, per Matrix row 39 and Constitution §5 item 9. */
  parameterTimeframeDiversityContext?: Record<string, unknown>;
  /** Whether the candidate set is judged sufficiently diversified given the measures above. Exact
   *  thresholds are PROVISIONAL — §16. */
  sufficientlyDiversified: boolean;
  justification: string;
}
```

This document fixes the decision structure — six named overlap dimensions must each be measured and
recorded, and parameter/timeframe diversity is explicitly insufficient on its own — without fixing the
numeric threshold that separates "sufficiently diversified" from "not," which is PROVISIONAL (§16),
consistent with this program's no-fabricated-constants discipline.

### 4.2 HRP as first-class, not-only allocator (Matrix row 40)

Per Matrix row 40 and Constitution §5 item 9: "HRP is a first-class allocation method and benchmark, not
the only permissible allocator. Alternatives must be compared under common risk and shared-account
simulation."

```typescript
interface AllocationDecision {
  /** HRP MUST be run as a benchmark comparator for every PortfolioPlan, whether or not it is the method
   *  ultimately selected — fixed now, per Constitution §5 item 9's "first-class... not the only
   *  permissible allocator." */
  hrpBenchmarkResult: unknown;
  /** Alternative allocators considered (e.g., mean-variance, risk-parity, CVaR-based), each compared under
   *  the same SharedCapitalSimulationDecision (§4.3) — never compared under a different simulation basis
   *  than HRP's own comparison. */
  alternativesConsidered: Array<{ method: string; result: unknown }>;
  /** The method actually selected for this PortfolioArtifact, and why. */
  selectedMethod: string;
  selectionJustification: string;
}
```

**skfolio reference implementation, not adopted default (REFERENCE only):**
`docs/research/candidate-reports/PA-05-skfolio.md` is cited as the ADOPT CANDIDATE reference for
HRP/NCO/CVaR/CDaR implementations, per the Prior-Art Register (PA-10, row PA-05-01) status: **ADOPT
CANDIDATE, not final ADOPT** — its test suite was never executed per Sol's finding reflected in the
corrected PA-10 register. Per Architecture §11 and Validation §12's same contract-boundary discipline: if
skfolio (or any allocator library) is adopted following PA-10's own acceptance gates, it is wrapped entirely
behind this document's own `PortfolioPlan`/`AllocationDecision` interface — no field of `AllocationDecision`
may be, or be shaped like, a skfolio-native estimator/optimizer object; every field is a Signal-Current-owned
primitive or nested interface, per the same discipline Architecture §11 and Validation §12 already fix for
the Validation Service.

### 4.3 Shared-capital / shared-account simulation (Matrix row 41)

Per Matrix row 41: "Portfolio promotion requires shared-capital/event simulation with common calendar/
currency, aggregate margin, exposure and risk constraints." A simple sum-of-equity-curves is insufficient.

```typescript
interface SharedCapitalSimulationDecision {
  /** Common calendar reference all member StrategyArtifacts are simulated against jointly — never each
   *  member's own native calendar summed independently. Per Data Architecture §4.1 item 2. */
  commonCalendarRef: string;
  /** Common settlement/quote currency, or an explicit conversion/normalization policy where members span
   *  multiple currencies, per Data Architecture §4.1 item 5. */
  currencyPolicyRef: string;
  /** Aggregate margin/exposure/risk constraints modeled jointly across all members, not per-member. */
  aggregateConstraints: Record<string, unknown>;
  /** The joint event-simulated result. A field explicitly present so that a reviewer can verify this is
   *  NOT a sum-of-independent-equity-curves computation. */
  jointSimulationResult: unknown;
  /** Explicit confirmation this was NOT computed as sum-of-equity-curves — required, non-optional, per
   *  Matrix row 41's own stated insufficiency of that shortcut. */
  sumOfEquityCurvesUsed: false;
}
```

The `sumOfEquityCurvesUsed: false` literal type is deliberate: this document fixes that a
`SharedCapitalSimulationDecision` claiming the sum-of-equity-curves shortcut was used is not a valid
decision record at all — it is structurally excluded, not merely discouraged.

### 4.4 Contribution testing (Matrix row 42)

Per Matrix row 42: "Portfolio evaluation includes leave-one-out contribution, drawdown overlap, tail/
correlation stress, concentration, regime coverage and capital utilization."

```typescript
interface ContributionTestingDecision {
  /** Per-member leave-one-out result: the joint portfolio result with this member excluded, so a reviewer
   *  can see whether the member is genuinely contributive or redundant. */
  leaveOneOutResults: Array<{ excludedArtifactRef: string; portfolioResultWithoutMember: unknown }>;
  drawdownOverlap: unknown;
  tailCorrelationStress: unknown;
  concentration: unknown;
  regimeCoverage: unknown;
  capitalUtilization: unknown;
  /** Whether the full member set, after all measures above, is judged non-redundant. Exact thresholds
   *  PROVISIONAL — §16. */
  nonRedundant: boolean;
  justification: string;
}
```

Per Matrix row 42's own stated risk — "Optimized weights can hide redundant members" — an
`AllocationDecision` (§4.2) alone, however sophisticated the allocator, never substitutes for the explicit
leave-one-out and overlap/concentration measures above.

## 5. Deployment as controlled artifact promotion (Matrix row 43)

Per Matrix row 43 and Constitution §3 item 4 ("a backtest is not a deployment. Build, target-platform
conformance, incubation, risk approval, telemetry, and health monitoring are separate promotion stages"):
export is never treated as the endpoint. The Build/Conformance Service (Architecture §5) mints an immutable
`BuildArtifact`, and the Promotion/Gate Service (Architecture §5) subsequently mints an immutable
`DeploymentArtifact`, only through the staged pipeline below.

```typescript
interface BuildArtifact {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  /** The StrategyArtifact or PortfolioArtifact this build compiles from. A BuildArtifact never rewrites
   *  its source artifact (Architecture §8). */
  sourceArtifactRef: string;
  /** The target platform this build compiles toward — an explicit typed reference, never inferred. */
  targetPlatformRef: string;
  /** Compile/build metadata: build-tool version, compile timestamp, build configuration. */
  buildMetadata: Record<string, unknown>;
  /** Result of the semantic-conformance check against the discrepancy taxonomy (§6). */
  conformanceResult: ConformanceResult;
  /** Result of the target-compatibility check (instrument/venue/session support on the target platform). */
  targetCompatibilityResult: unknown;
  status: 'built' | 'conformance-checked' | 'rejected';
}

interface DeploymentArtifact {
  id: string;
  buildArtifactRef: string;
  /** Explicit human approval record, per §7 — never present without it for any material-risk-bearing
   *  deployment. */
  approvalRef: string;
  /** Current incubation stage, per §8. */
  incubationStage: 'paper' | 'shadow' | 'small-risk' | 'graduated';
  /** Rollback creates a new state-transition event, never a silent edit (Architecture §3.1). */
  rollbackHistory: string[];
  auditRefs: string[];
}
```

This document fixes that `BuildArtifact` and `DeploymentArtifact` are each distinct, immutable, audited
artifacts in the lineage spine (Architecture §3), and that a `BuildArtifact` alone — however clean its
compile — never constitutes a `DeploymentArtifact`. Promotion from `BuildArtifact` to `DeploymentArtifact`
requires conformance (§6), incubation progression (§8), and explicit human authorization (§7) as separate,
recorded gates, per Constitution §3 item 4.

## 6. Target-platform role (Matrix row 44)

Per Matrix row 44 and Constitution §8: any target platform — MT5 or otherwise — is a deployment/conformance
adapter only. It MUST NOT define Signal Current research semantics, data semantics, execution ontology, or
`StrategyIR`. This document requires:

1. The Build/Conformance Service (Architecture §5) reads a `StrategyArtifact`'s IR and cost/execution
   contracts (documents 04/05) as the sole semantic source it compiles from. No target-platform-specific
   concept (e.g., a platform-native order type, a platform-native indicator library convention) may be
   introduced upstream of the Build step — it is confined to the adapter code that performs compilation and
   conformance checking, per Architecture §10's adapter-isolation boundary, extended here from data/venue
   normalization to the build/deployment layer specifically.
2. Whether, and when, a specific target platform (e.g. MT5) is actually implemented is a Roadmap (document
   08) decision, not an architectural privilege granted by this document — this document fixes only the
   adapter-isolation contract any target platform integration must satisfy, per Matrix row 44's explicit
   "Whether it is implemented early is a roadmap decision, not an architectural privilege" clause.

## 7. Human authorization gate for live-risk promotion (Matrix row 49, U-12, Constitution §6)

This is the most consequential section in this document.

### 7.1 The principle is Constitution-locked, not provisional

Per Constitution §6: "Agents may NOT... serve as, substitute for, or impersonate the human authorization
required to promote any material-risk-bearing artifact, regardless of how confident or well-tested the
agent's judgment is. Promotion of any material-risk-bearing artifact requires explicit HUMAN authorization;
no agent-defined or agent-only authorization path ever satisfies this gate." This principle is fixed now
and is NOT PROVISIONAL. Only the technical protocol implementing it (§7.4) is PROVISIONAL.

### 7.2 What "explicit human authorization" means operationally

```typescript
interface HumanAuthorizationRecord {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  /** The DeploymentArtifact (or incubation-stage graduation event, per §8) this authorization concerns. */
  deploymentArtifactRef: string;
  /** Identity of the specific named human who authorized this promotion — never a role placeholder, never
   *  an agent identity, never a service account. Exact identity/auth mechanism is PROVISIONAL (§16, U-12);
   *  the requirement that it resolve to one accountable, named human is fixed now. */
  authorizingHumanId: string;
  /** The evidence this human reviewed before authorizing — required, non-optional. At minimum: the
   *  StrategyArtifact passport(s) (§3), the cleared ValidationPlan (Validation §2), the PortfolioPlan if
   *  applicable (§4), the BuildArtifact's conformanceResult (§5), and the current incubation stage's
   *  evidence (§8). */
  evidenceReviewedRefs: string[];
  /** What the human is explicitly attesting to — not a vague "approved," but a recorded attestation that
   *  they reviewed the evidence above and accept the material risk of this specific promotion. */
  attestationStatement: string;
  timestamp: string;
  /** Distinct, versioned audit event per Constitution §9.1 and Matrix row 53. */
  auditEventRef: string;
}
```

Operationally, this document fixes:

1. **Who:** a specific, named, accountable human — never a role placeholder, a committee alias without an
   individual attestor, or any agent/service identity. The exact identity/access-control mechanism that
   verifies this human's identity is PROVISIONAL (§16, U-12).
2. **What they review:** the full evidence chain named in `evidenceReviewedRefs` above — the human
   authorization is not valid if it references an incomplete evidence set (e.g., a `DeploymentArtifact`
   whose `BuildArtifact.conformanceResult` is missing from `evidenceReviewedRefs`).
3. **What they attest to:** an explicit, recorded statement that they reviewed the evidence and accept the
   material risk of this specific promotion — not a bare boolean `approved: true`, per the same "no bare
   boolean" discipline Validation §2 already establishes for `ValidationPlan` decisions.

### 7.3 No agent — however well-reviewed — may constitute or substitute for this gate

**Rejected anti-pattern, cited directly per this sprint's instruction (REFERENCE only, not adopted code):**
TradingAgents' Portfolio-Manager-as-LLM-approval-gate
(`docs/research/candidate-reports/PA-08-tradingagents.md`) is the clearest rejected anti-pattern for this
section. TradingAgents structures a multi-agent debate/review pipeline that culminates in an LLM-role
"Portfolio Manager" agent issuing an approve/reject decision on a trade. However well-designed the upstream
debate/review process feeding into that decision, an LLM's approve/reject output is not, and cannot become,
a substitute for explicit human authorization under this document's model. This architecture's
Promotion/Gate Service (Architecture §5) and the `HumanAuthorizationRecord` above are structurally
incompatible with any design where an agent role — regardless of how many review/debate stages precede
it — is the final actor whose output is treated as the authorization event. No number of agent review
stages, no confidence score, no agent-generated "recommendation approved" event ever populates
`authorizingHumanId` or satisfies this gate. Agents may populate `evidenceReviewedRefs`' *contents* (by
preparing evidence) but may never themselves produce the `HumanAuthorizationRecord` itself.

### 7.4 What is PROVISIONAL vs. fixed

The exact auth/authorization **protocol** — e.g., a specific identity provider, a specific technical
mechanism such as a signed attestation, multi-factor confirmation, or a specific access-control system — is
**PROVISIONAL — unvalidated, owner Danny**, resolution condition: "dedicated auth/authz design pass once a
deployment target and user model are chosen (depends on Roadmap sequencing)." The **principle** (a human,
not an agent, authorizes) is NOT provisional, per §7.1.

## 8. Incubation and risk graduation (Matrix row 46)

Per Matrix row 46: "Preserve as mandatory deployment lifecycle before material risk. Graduation requires
evidence and explicit authorization."

```typescript
type IncubationStage = 'paper' | 'shadow' | 'small-risk' | 'graduated';

interface GraduationEvent {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  deploymentArtifactRef: string;
  fromStage: IncubationStage;
  toStage: IncubationStage;
  /** Evidence supporting this graduation — e.g., a LiveObservationSet/HealthAssessment reference (§9)
   *  showing the prior stage's behavior matched expectations. Exact evidence-sufficiency criteria and
   *  stage durations are PROVISIONAL — §16. */
  evidenceRefs: string[];
  /** Required whenever toStage represents material risk exposure (i.e., 'small-risk' or 'graduated') —
   *  per Constitution §6 and §7 above, this MUST be a HumanAuthorizationRecord reference, never absent,
   *  never an agent-generated approval. */
  humanAuthorizationRef?: string;
}
```

This document fixes the mandatory three-stage-minimum progression (`paper` → `shadow` → `small-risk`, with
`graduated` as a terminal state representing full material-risk operation) as a lifecycle no
`DeploymentArtifact` may skip on the way to material risk, and that any transition into a
material-risk-bearing stage requires a `HumanAuthorizationRecord` per §7. Exact stage durations, exact
evidence-sufficiency thresholds, and exact capital thresholds distinguishing "small-risk" from "graduated"
are **PROVISIONAL — unvalidated, owner Danny** (§16).

## 9. Target-platform parity contract (Matrix row 45, U-07)

Per Matrix row 45: "Require event/decision conformance within explicit tolerance and explainable difference
classes. Target-platform real-tick verification is finalist evidence, not a replacement for reference
simulation." Per Matrix §7, U-07 states "exact equality may be impossible across execution engines" and
must not be guessed here.

### 9.1 What this document fixes: the structural discrepancy taxonomy

A `ConformanceResult` (referenced by `BuildArtifact.conformanceResult`, §5) MUST record discrepancies
against a structural taxonomy of difference *classes* — not a single pass/fail number — because different
kinds of divergence between reference simulation and target-platform execution have different causes and
different acceptable-tolerance shapes.

```typescript
type DiscrepancyClass =
  | 'fill-price-difference'
  | 'fill-timing-difference'
  | 'order-sequencing-difference'
  | 'rounding-tick-lot-difference'
  | 'session-calendar-difference'
  | 'cost-model-difference'
  | 'other-explained';

interface DiscrepancyRecord {
  discrepancyClass: DiscrepancyClass;
  /** The specific observed magnitude/instance of this discrepancy — units are class-specific. */
  observedMagnitude: unknown;
  /** Whether the observed magnitude falls within this class's tolerance. Exact tolerance values are
   *  PROVISIONAL — §16, U-07. */
  withinTolerance: boolean;
  /** Human-readable explanation of the discrepancy's mechanism — required whenever withinTolerance is
   *  false, and encouraged even when true, per Matrix row 45's "explainable difference classes." An
   *  unexplained discrepancy, regardless of magnitude, does not satisfy this contract. */
  explanation: string;
}

interface ConformanceResult {
  buildArtifactRef: string;
  discrepancies: DiscrepancyRecord[];
  /** Whether every discrepancy above is both within its class's tolerance AND explained. Exact
   *  per-class tolerance thresholds and the exhaustive taxonomy are PROVISIONAL — §16, U-07. */
  overallConformant: boolean;
  /** Distinguishes reference-simulation-only conformance checking from target-platform real-tick
   *  verification, per Matrix row 45's "finalist evidence, not a replacement" framing — a BuildArtifact
   *  MUST NOT rely on real-tick verification alone as a substitute for reference-simulation conformance. */
  verificationTier: 'reference-simulation-only' | 'target-platform-real-tick';
}
```

### 9.2 Structural template cited (REFERENCE only, not adopted code)

Freqtrade's documented backtest-vs-live divergence checklist
(`docs/research/candidate-reports/PA-01-freqtrade.md`) — covering fill assumptions, signal-priority
ordering, and dynamic-pairlist non-reproducibility — is cited directly as a genuinely useful, citable
structural template for what this taxonomy must cover. The `DiscrepancyClass` union above generalizes that
checklist's specific findings (fill-price/fill-timing map to Freqtrade's fill-assumption divergence;
order-sequencing-difference maps to its signal-priority-ordering finding; the taxonomy's extensibility via
`'other-explained'` accommodates a class this program has not yet encountered, such as a future analogue of
Freqtrade's dynamic-pairlist non-reproducibility finding). This is cited as structural precedent only — no
Freqtrade code, tolerance value, or product decision is adopted here.

### 9.3 What is deferred (U-07)

The exact tolerance value per `DiscrepancyClass` (e.g., what fill-price difference magnitude counts as
"within tolerance"), and the fully exhaustive enumeration of `DiscrepancyClass` beyond the structural
categories fixed above, are **PROVISIONAL — unvalidated, owner Danny**, resolution condition: "dedicated
target-platform parity ADR once at least one concrete target platform is selected in the Roadmap (document
08)." This document fixes only that: (a) discrepancies are classified into explainable difference classes,
never reported as a single undifferentiated pass/fail number; (b) every discrepancy, tolerated or not, is
explained; (c) reference-simulation conformance and target-platform real-tick verification are recorded as
distinct verification tiers, with real-tick verification never substituting for reference-simulation
conformance, per Matrix row 45's own explicit framing.

## 10. Live telemetry and retraining (Matrix rows 47-48)

Per Constitution §3 item 5 and Matrix row 47: "LiveObservation artifacts are append-only operational
evidence. They can trigger re-research/retraining but never mutate historical StrategyArtifacts or
validation results."

```typescript
interface LiveObservationSet {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  deploymentArtifactRef: string;
  /** Append-only: a new observation batch produces a new LiveObservationSet entry, never an edit to a
   *  prior one, per Constitution §3.5. */
  observations: unknown[];
  capturedAt: string;
}

interface HealthAssessment {
  id: string;
  liveObservationSetRefs: string[];
  /** Derived evaluation against expected behavior — immutable per assessment run, per Architecture §3.1. */
  assessmentResult: unknown;
  /** May emit a ResearchTrigger, per Architecture §3 spine and Matrix row 47's "can trigger
   *  re-research/retraining." */
  researchTriggerRef?: string;
}
```

This document fixes that the Telemetry/Health Service (Architecture §5) never writes to, or otherwise
mutates, any `StrategyArtifact`, `ValidationArtifact`, `PortfolioArtifact`, or their referenced evidence.
Per Matrix row 48 and Constitution §3 item 6: retraining or regeneration triggered by a `HealthAssessment`
produces a **new lineage child** `StrategyArtifact` — with its own passport (§3), its own
`ValidationPlan` (Validation §2), and, if it reaches deployment, its own `BuildArtifact`/`DeploymentArtifact`
and incubation progression (§8) from scratch. It never mutates, backdates, or supersedes the historical
artifact in place; the historical artifact's `supersededBy` field (§3) points forward to the new child, but
the historical artifact and its evidence remain immutable.

## 11. Fully autonomous live deployment is out of v1.0 scope (Matrix row 49)

Per Matrix row 49: "Fully autonomous risk-bearing deployment is outside v1.0. Agents may prepare and
recommend; risk promotion requires the defined human authorization gate." This document states plainly:
agents (Architecture §6's Agent & Orchestration layer, not P0, and not load-bearing for this document's own
contracts either) may prepare `BuildArtifact`s, draft `PortfolioPlan`/`ConformanceResult` evidence, propose
graduation events, and recommend authorization — but no agent output, however well-reviewed, ever itself
constitutes promotion to material risk (§7.3). This is not a temporary limitation awaiting a future
capability improvement; it is the fixed v1.0 boundary per Matrix row 49, reinforced by the
Constitution-locked principle in §7.1.

## 12. Cost realism carries through to deployment readiness (cautionary reference)

**Cautionary precedent (REFERENCE only, not adopted code):** je-suis-tm/quant-trading's own README
admission that its backtests are "frictionless" (no slippage/cost modeling,
`docs/research/candidate-reports/PA-01-je-suis-tm-quant-trading.md`) illustrates why cost-realism, already
required at the Validation stage (Validation §8, Constitution §5 item 6), is not a check this document may
silently drop once a candidate reaches Portfolio/Build/Deployment. `SharedCapitalSimulationDecision` (§4.3)
and `ConformanceResult` (§9.1) both reference the same versioned `CostModel`/`ExecutionContext` (Data
Architecture §3) the artifact was validated against — a `BuildArtifact` or `PortfolioArtifact` MUST NOT
silently substitute a zero-cost or lower-cost assumption at this later stage than the one its
`ValidationPlan.costModel` (Validation §8) already recorded and justified.

## 13. Patterns and anti-patterns

| Pattern | Where used | Rationale / anchor |
|---|---|---|
| Immutable, content-addressed StrategyArtifact passport registry | §3 | Matrix rows 37-38 |
| Six-dimension behavioral-diversification measurement, parameter/timeframe diversity explicitly insufficient | §4.1 | Matrix row 39; Constitution §5.9 |
| HRP as mandatory benchmark comparator, never the only allocator | §4.2 | Matrix row 40; Constitution §5.9 |
| Joint shared-capital event simulation, sum-of-equity-curves structurally excluded | §4.3 | Matrix row 41 |
| Leave-one-out contribution testing alongside allocator weights | §4.4 | Matrix row 42 |
| Staged artifact promotion: Build → Conformance → Incubation → Human Authorization → Deployment | §5, §7, §8 | Constitution §3.4; Matrix row 43 |
| Target platform as adapter only, isolated from research/data/execution semantics | §6 | Constitution §8; Matrix row 44 |
| Discrepancy-class taxonomy, never a single undifferentiated parity number | §9 | Matrix row 45 |
| Named, evidence-backed, attested human authorization record | §7 | Constitution §6; Matrix row 49 |
| Append-only telemetry; retraining as new lineage child, never mutation | §10 | Constitution §3.5-3.6; Matrix rows 47-48 |
| Wrap, don't surface, third-party allocator libraries | §4.2 | Constitution §10; Architecture §11 |

**Anti-patterns (explicitly rejected):**

- LLM/agent-as-final-approval-gate for risk promotion — rejected per Constitution §6 and Matrix row 49; see
  TradingAgents' Portfolio-Manager-as-LLM-approval pattern (§7.3), cited as the concrete negative precedent.
- Treating export/build as the deployment endpoint — rejected per Matrix row 43 and Constitution §3 item 4.
- A single undifferentiated parity pass/fail number standing in for a discrepancy taxonomy — rejected per
  Matrix row 45.
- Sum-of-equity-curves standing in for joint shared-capital simulation — rejected per Matrix row 41; §4.3's
  `sumOfEquityCurvesUsed: false` literal type structurally excludes it.
- Optimized allocator weights alone standing in for redundancy/contribution evidence — rejected per Matrix
  row 42.
- Silent zero-cost or reduced-cost assumption reappearing at Build/Portfolio stage after Validation already
  recorded and justified a different `CostModel` — rejected per §12 and Constitution §5 item 6.
- Real-tick target-platform verification substituting for reference-simulation conformance — rejected per
  Matrix row 45's "finalist evidence, not a replacement" framing (§9.1's `verificationTier` field exists
  specifically to make this distinction inspectable).

## 14. Dependencies

No new third-party runtime dependency is promoted by this document. Per Architecture §11/§13 and Validation
§12's same discipline, no ADOPT decision is made here for skfolio or any other allocator/conformance
library — that decision belongs to PA-10's own register acceptance gates (out of this sprint's scope). Where
this document names external systems, it does so only as REFERENCE-only design precedent (skfolio §4.2,
Freqtrade §9.2, TradingAgents anti-pattern §7.3, je-suis-tm/quant-trading cautionary reference §12, NVIDIA
cuOpt below) — none of these are adopted as dependencies by this portfolio/deployment document.

**NVIDIA cuOpt / Quant Portfolio Optimization blueprint** (`docs/research/candidate-reports/PA-05-nvidia-cuopt-portfolio.md`):
cited REFERENCE-only for the Mean-CVaR scenario-LP formulation *concept* as one possible allocator method a
future `AllocationDecision.alternativesConsidered` entry might record. Explicitly NOT cited for, and NOT
relied upon for, its vendor-reported and unverified 100x/160x performance claims (per this repo's
CLAUDE.md unsourced-number discipline — a vendor benchmark claim that has not been independently reproduced
is not a citable source), and NOT for its GPU/CUDA hardware coupling, which would violate Constitution §2's
"no privileged... execution platform" and this program's "no privileged deployment target" requirement.

## 15. Consistency check against Constitution, Architecture, Data Architecture, and Validation

This document was checked for contradiction against `01-constitution.md`, `02-system-architecture.md`,
`04-data-architecture-strategy-ir.md`, and `05-validation-statistical-controls.md` in full. No conflict was
found: every clause above elaborates a boundary or contract those four documents already establish
(Constitution §3, §5.9, §6, §8, §9; Architecture §3, §5, §8, §10, §11; Data Architecture §2.4, §3, §4, §5;
Validation §2, §12) at the portfolio/deployment/monitoring level, without contradicting or silently
re-deciding any of their fixed clauses. In particular:

- **Re-run 2026-09-05, per Sol's cold review (target SHA 99d3673):** this document's widened U-12 row (§16,
  `controllingPrincipalId` sub-item) was checked against document 04 §5.4/§8, document 05 §2.1 items 1 and 3
  / §14, document 07 §3.1/§12, and document 08 §3's Phase R1 sequencing (all amended the same pass with
  matching wording). No conflict was found.

- **Re-run 2026-09-06, per Frank's spec-gate attempt-12 finding:** this document's widened U-12 row (§16)
  was checked against post-`e6dfe73` documents 04 (§5.4/§8) and 05 (§2.1/§14). The gap attempt-12 found: §16's
  U-12 row still used pre-attempt-11 "items 1 and 3" wording (implying item 2 was excluded, though item 2
  now also consumes `controllingPrincipalId` per document 05 §2.1) and still described the agent side of the
  human/agent comparison as resolving to a session-initiating human's own `actorId` rather than that human's
  `controllingPrincipalId`, contradicting document 04 §5.4's attempt-11 redefinition. This pass corrects both:
  §16's U-12 row now reads "items 1-3" and resolves the agent side to the session-initiating human's own
  `controllingPrincipalId`, matching document 04 §5.4 and document 05 §2.1/§14 as amended in this same pass.
  No new contradiction was found; no HALT condition applies.

- **Re-run 2026-09-06, per Frank's spec-gate attempt-13 finding:** document 05 §2.1's "Flagged open question"
  paragraph was found to still name `AuditActor.actorId` (attempt-4/5-era text predating the
  `controllingPrincipalId` fix sequence) as what the interim distinct-identity default and its open judgment
  call depend on, and was corrected to name `controllingPrincipalId` throughout. This document's own §16
  U-12 row already named `controllingPrincipalId` correctly (corrected in the attempt-12 re-run above) and
  required no further change. Checked against document 05's corrected §2.1/§14 in full; no new contradiction
  was found; no HALT condition applies.

- this document does not re-decide `ValidationPlan` structure or statistical defaults (Validation §§2-14) —
  it consumes a cleared `ValidationPlan` as a precondition (§2);
- this document does not re-decide `StrategyIR`/`ExecutionContext`/`CostModel` schema (Data Architecture
  §§2-4) — it references those contracts by ID (§4.3, §9, §12);
- this document does not re-decide the agent permission matrix (U-11, Architecture §14, document 07's
  scope) — it fixes only the negative constraint that no agent output ever constitutes the human
  authorization gate (§7.3), which is Constitution-locked independent of document 07's eventual permission
  matrix design.

No HALT condition applies.

## 16. PROVISIONAL items and resolution paths

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| **U-07a** — Exact per-discrepancy-class tolerance values (fill-price, fill-timing, order-sequencing, rounding/tick-lot, session/calendar, cost-model difference classes) | PROVISIONAL — unvalidated | Danny | Dedicated target-platform parity ADR once at least one concrete target platform is selected in the Roadmap (document 08) |
| **U-07b** — Exhaustive enumeration of `DiscrepancyClass` beyond the structural categories fixed in §9.1 | PROVISIONAL — unvalidated | Danny | Resolved in the same target-platform parity ADR as U-07a, informed by actual observed discrepancies once a target platform is integrated |
| **U-12** — Exact authentication/authorization protocol (identity provider, technical mechanism) for `HumanAuthorizationRecord.authorizingHumanId` verification. **Widened, Frank spec-gate attempt-4 fix 2:** this single item also covers the exact mechanism verifying `AuditActor.actorId` authenticity and the exact mechanism minting/verifying `orchestrationSessionId` (document 04 §5.4, document 07 §3.1/§3.2) — not a second PROVISIONAL item, since all three depend on the same identity-provider/token-mechanism choice. Cross-referenced, not duplicated, from document 04 §8 and document 07 §12. **Widened further, per Sol's cold review (target SHA 99d3673):** also covers resolving `AuditActor.controllingPrincipalId` (document 04 §5.4) — whether two human `actorId`s, or a human `actorId` and the `controllingPrincipalId` of the human who holds session-initiation authority for an agent's `orchestrationSessionId`, resolve to the same natural person, per document 05 §2.1 items 1-3. Still one PROVISIONAL item, not a fourth, since it depends on the same identity-provider account model as the other sub-items. This is the terminal boundary named by Frank's spec-gate attempt-4: below it, the spec set correctly stops — authentication mechanism selection is not a spec-time decision. **Sequencing split, Frank spec-gate attempt-5 fix 2:** although one PROVISIONAL item, its sub-items resolve on two different schedules — see the resolution condition column. | PROVISIONAL — unvalidated | Danny | **Split resolution, not one pass:** the `AuditActor.actorId` authenticity, `orchestrationSessionId` minting/verification, and `controllingPrincipalId` resolution sub-items (document 04 §5.4, document 07 §3.1/§3.2, document 05 §2.1 items 1-3) resolve in Phase R1, alongside the independent-reproduction match tolerance (document 05 §14) — all gate the same first `StrategyArtifact` promotion (document 05 §2.1's reproduction gate), so a `StrategyArtifact` cannot be promoted before they resolve. This does NOT depend on a deployment target or user model being chosen. The `HumanAuthorizationRecord.authorizingHumanId` identity-provider/protocol sub-item resolves separately, in Phase R3, once a deployment target and user model are chosen (depends on Roadmap sequencing). All sub-resolutions ultimately draw on the same identity-provider/token-mechanism choice, but land at different points in the sequence. |
| Behavioral-diversification "sufficiently diversified" threshold (§4.1) | PROVISIONAL — unvalidated | Danny | Dedicated portfolio-qualification research-design pass, informed by whichever allocator/diversification reference implementation (e.g., skfolio, pending its own PA-10 acceptance gates) is used for comparison |
| Contribution-testing "non-redundant" threshold (§4.4) | PROVISIONAL — unvalidated | Danny | Resolved alongside the behavioral-diversification threshold above, since both depend on the same portfolio-qualification research design |
| Incubation-stage durations, evidence-sufficiency criteria, and capital thresholds distinguishing `small-risk` from `graduated` (§8) | PROVISIONAL — unvalidated | Danny | Dedicated incubation/risk-graduation design pass, informed by whichever concrete deployment target and capital model the Roadmap selects |
| `StrategyArtifactPassport.complexity` (§3) — exact schema for the structural complexity descriptor (which fields, what "primitive count"/"dimension count" concretely means) | PROVISIONAL — unvalidated | Danny | Resolved in whichever schema-design session addresses `StrategyArtifactPassport` — most naturally the same U-01a `StrategyIR` schema-design session document 08 §3 Phase R1 sequences, since `complexity` is read off the same IR shape those golden examples fix |

No numeric constant (tolerance percentage, diversification threshold, redundancy threshold, incubation
duration, capital threshold, auth protocol specifics) is introduced anywhere in this document. Every
PROVISIONAL item above is a design decision explicitly flagged by the Reconciliation Matrix (U-07, U-12) or
a research-design/roadmap-dependent decision this document defers rather than guesses — none is fabricated
here. The single non-provisional principle in this document's most consequential section (§7) — that a
human, not an agent, authorizes material-risk promotion — is Constitution-locked (Constitution §6) and is
listed here only for completeness, not as an open item.

## 17. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Agent
specification work (document 07) and Roadmap sequencing work (document 08) (Constitution §11), and MUST be
revised once the dedicated design work named in §16 resolves U-07/U-12/the `complexity` field schema — at
that point this document is amended (not silently superseded) to replace the PROVISIONAL tags with cited,
versioned tolerance values/protocol decisions/schema fields. After freeze, amendment requires an explicit
ADR and version change.
