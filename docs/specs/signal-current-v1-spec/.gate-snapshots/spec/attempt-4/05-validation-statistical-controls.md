# Signal Current — Validation & Statistical Controls Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, `docs/specs/02-system-architecture.md`,
`docs/specs/03-research-methodology.md`, and `docs/specs/04-data-architecture-strategy-ir.md`. Not yet
independently reviewed. Frozen only after the full eight-document set clears Frank's binding spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gap G1. @architect, 2026-09-05,
per Frank's spec-gate attempt-1 fix item 3 — added `IndependentReproductionRecord` (§2, §2.1) and its
match-tolerance PROVISIONAL item (§14), per Matrix row 59 / Reconciliation Matrix §6 item 14. @architect, 2026-09-05, per Frank's spec-gate attempt-2
findings F1 (required), F3 (required), F4 (required) — redefined `IndependentReproductionRecord` to bind
to content-addressed reproduction outputs and audit-log actor references rather than free assertions
(§2), defined "distinct identity" precisely and closed the one-session/one-orchestrator loophole (§2.1),
resolved the lockbox-confirmation question (§2.1, §11.3), and re-ran the §13 consistency check against the
now-amended documents 02/04. @architect, 2026-09-05, per Frank's spec-gate attempt-3 findings F1/F2 —
added the missing `reproducedBy` derivation sentence and tightened `originalAuthor`'s wording to match
(§2), tightened §2.1 item 3's human-rubber-stamp clause now that derivation is mechanical, re-ran the §13
consistency check against the now-further-amended documents 02/04/07, and moved gate-narration language out
of body text/doc-comments into this header line.

**Primary question this document answers:** What evidence is required before promotion, and how is
self-deception constrained?

**Traceability convention:** every substantive clause cites a Reconciliation Matrix row, a Constitution
clause, or an Architecture/Research-Methodology/Data-Architecture section. Where this document generalizes
beyond a single directly-matching source, it is flagged inline as synthesis, consistent with the precedent
in Constitution §10 and Architecture §2. Design precedent from prior-art research is cited REFERENCE-only,
never as an adopted dependency.

## 1. Scope and non-goals — read this before anything else

Per Reconciliation Matrix §7's explicit statement that U-04 "cannot be universal without research design,"
U-05 "DSR/reality-check/bootstrap methods have different applicability," and U-06 "must depend on
sampling/event structure," and per this sprint's explicit orchestration instruction: **this document
defines the CONTRACT — what a `ValidationPlan` must record, what evidence must exist, what the decision
criteria structure looks like — that the eventual statistical defaults must satisfy once dedicated
statistical/research design work produces them. It does NOT invent:**

- a specific minimum-sample-size number for any asset/timeframe/event-structure combination (U-04);
- a specific p-value, significance, or Deflated Sharpe Ratio (DSR) cutoff, or a universal choice among
  DSR/reality-check/bootstrap-style diagnostics (U-05);
- a specific CPCV applicability matrix, fold count, or purge/embargo window length (U-06);
- a specific cost-stress percentage or adverse-cost scenario magnitude.

Each of the above is tagged **PROVISIONAL — unvalidated**, owner **Danny**, with a named resolution
condition, in §14. This is the correct and required output for U-04/U-05/U-06 at this stage — an honest,
well-scoped PROVISIONAL section, not a defect to be patched by inventing plausible-sounding specifics. Per
this repo's `CLAUDE.md` Research Data Integrity rules, an unsourced number in a research/data path is a
hypothesis, not a setting; none is smuggled in here as a setting.

This document also does NOT re-decide:

- `StrategyIR`/`ExecutionContext`/`CostModel` field-level schema (document 04's scope; this document
  consumes those contracts as given);
- deployment parity tolerance (U-07, document 06's job);
- agent permission matrix (U-11, document 07's job).

## 2. The ValidationArtifact this service produces

Per Architecture §3.1 and §5, the Validation & Statistical Controls Service consumes one or more
`SimulationRun`s and produces an immutable `ValidationArtifact` that "records applicability decisions, not
just outcomes." This document defines the `ValidationPlan` — the typed, versioned decision record every
`ValidationArtifact` must carry — and the evidence each of its decisions must be backed by.

```typescript
interface ValidationPlan {
  /** Content-addressed identity, per Data Architecture §2.4/§5 pattern. */
  id: string;
  /** The StrategyIR Candidate(s) / SimulationRun(s) this plan validates. */
  simulationRunRefs: string[];
  /** Version of this ValidationPlan's own decision-rule set — never silently redefined in place. */
  planVersion: string;

  purgeEmbargo: PurgeEmbargoDecision;
  cpcv: CPCVDecision;
  temporalEvaluation: TemporalEvaluationDecision;
  multipleTestingDiagnostics: MultipleTestingDecision;
  costModel: CostRealismDecision;
  robustness: RobustnessDecision[];
  labelingWorkflow: LabelingWorkflowDecision;
  sampleAdequacy: SampleAdequacyDecision;
  /**
   * Per Matrix row 59 / Reconciliation Matrix §6 item 14 and NORTHSTAR.md's Thesis: a strategy is
   * not validated by its own author's rerun, however deterministic the replay. This record is the
   * gate — it MUST resolve (rerun happened, matched) before `status` may become 'promotion-gated'.
   */
  independentReproduction: IndependentReproductionRecord;

  /** Every decision above must resolve before this plan is eligible to gate promotion. */
  status: 'draft' | 'complete' | 'promotion-gated';
}

/**
 * Per Matrix row 59 / Reconciliation Matrix §6 item 14: "Promotion to StrategyArtifact requires that
 * someone other than the original author (or authoring agent) independently reruns the validation
 * against the same content-addressed inputs and obtains the same result." This is additional to, not
 * a substitute for, the deterministic-replay and multiple-testing-diagnostics decisions above.
 *
 * This record is NOT an assertion a caller populates.
 * Every field below is either (a) a reference to an actor bound to the Audit/Event Log (document 04
 * §5.4's `AuditActor`, per Constitution §9.1 / Matrix row 53), or (b) a content-addressed reference to
 * the reproducer's own `SimulationRun`/`ValidationArtifact` outputs, or (c) `matched`, which is computed
 * by the Validation Service from an actual comparison of those two content-addressed outputs — never
 * populated by direct assertion. A record where every field is merely a free string or a self-reported
 * boolean does not satisfy Matrix row 59 and MUST NOT be accepted by the promotion gate.
 */
interface IndependentReproductionRecord {
  /**
   * Reference to the `AuditActor.actorId` (document 04 §5.4) of whoever ran the original validation this
   * record reproduces — not a free-text name. Resolved by the Validation Service from the
   * `AuditLineageEvent` whose `artifactRefs` match `ValidationPlan.simulationRunRefs` (§2 above) or the
   * equivalent original-run reference — i.e., the event that recorded production of the original
   * `SimulationRun`/`ValidationArtifact` — never caller-supplied. A record whose `originalAuthor` does not
   * equal that derived actor is malformed and MUST be rejected.
   */
  originalAuthor: string;
  /**
   * Reference to the `AuditActor.actorId` (document 04 §5.4) of whoever reran it. Resolved by the
   * Validation Service from the `AuditLineageEvent` whose `artifactRefs` contain `reproductionRunRefs`
   * (below) — i.e., the event that recorded production of the reproduction's own
   * `SimulationRun`/`ValidationArtifact` — never caller-supplied. A record whose `reproducedBy` does not
   * equal that derived actor is malformed and MUST be rejected. MUST also be a distinct principal from
   * `originalAuthor` per §2.1's "distinct identity" definition below — not merely a different string, and
   * not merely a different role name driven by the same orchestration session (see §2.1).
   */
  reproducedBy: string;
  /** Content-addressed input references the rerun was executed against — must match the original run's. */
  contentAddressedInputRefs: string[];
  /**
   * Content-addressed reference(s) to the reproducer's own `SimulationRun`(s) — distinct from, and
   * produced independently of, the original author's `SimulationRun`(s) referenced by
   * `ValidationPlan.simulationRunRefs` (§2 above). Without this, there is nothing for the Validation
   * Service to compare `matched` against, and the gate degenerates to the self-certification failure
   * identified in review.
   */
  reproductionRunRefs: string[];
  /**
   * Content-addressed reference to the `ValidationArtifact` produced from the reproduction run(s) above —
   * the reproducer's own independent validation output, not a restatement of the original artifact.
   */
  reproductionValidationArtifactRef: string;
  /** When the independent rerun was executed. */
  reproducedAt: string;
  /**
   * Whether the reproduced run's event ledger/metrics matched the original, within whatever tolerance
   * this field's companion PROVISIONAL item (§14) resolves. `matched` is never populated by direct
   * assertion; it is the Validation Service's own computed comparison result, derived by the service
   * itself diffing the original run's content-addressed output (`ValidationPlan.simulationRunRefs`)
   * against `reproductionRunRefs`/`reproductionValidationArtifactRef` above. Until the tolerance item in
   * §14 resolves, the service cannot evaluate this field against a numeric bar and the record cannot
   * satisfy the gate.
   */
  matched: boolean;
  /**
   * Structured note, written by the Validation Service alongside its own computation of `matched` (not by
   * either author), on what was compared and how (event ledger diff, metric-by-metric, etc.) — a record
   * of the service's method, not a claim either party asserts about their own work.
   */
  comparisonMethod: string;
}
```

Each decision sub-record below shares one structural requirement (the pattern this document fixes for
every row 27/28/32/33 "applicability decision" the Matrix calls out): **the record states the inputs that
determined applicability, the decision reached, and the justification — never a bare boolean.** An
applicability decision that says only `applicable: true` with no recorded rationale does not satisfy
Constitution §5 items 1-4 or Matrix rows 27/28/32/33's "applicability decision is machine-recorded"
requirement.

## 2.1 Independent reproduction gate (Matrix row 59; Reconciliation Matrix §6 item 14)

Per NORTHSTAR.md's Thesis and Matrix row 59: reproducibility-in-principle (the deterministic replay
this document already requires via §7.1's determinism controls, and the multiple-testing diagnostics
of §6) is not the same claim as reproduction-in-fact by a distinct identity. `ValidationPlan.status`
MUST NOT transition to `'promotion-gated'` unless the Validation Service has itself computed
`independentReproduction.matched === true` (per §2's redefinition — never a directly-asserted boolean)
AND `independentReproduction.reproducedBy` resolves to a **distinct identity**, per the definition below,
from `independentReproduction.originalAuthor`. This is a structural precondition on promotion to
`StrategyArtifact` (Architecture §5), not a statistical decision like §§3-10 above — it constrains *who*
checks a result, not *how* the method is applied.

**Distinct identity, defined precisely:** Matrix row 59 requires
"someone other than the original author (or authoring agent)." This document fixes that requirement as:
`reproducedBy` and `originalAuthor` MUST resolve to a different `AuditActor.actorId` (document 04 §5.4) —
not merely a different string, and not merely a different role label. Concretely:

1. Two `AuditActor` entries with `actorType: 'human'` and different `actorId`s are always distinct.
2. Two `AuditActor` entries with `actorType: 'agent'` are distinct **only if** they carry different
   `orchestrationSessionId`s. An agent rerun of an agent-authored validation **within the same
   orchestration session** as the original — even when driven by a different tool/role name (e.g. a
   "validator" role and a separately-named "reproducer" role both invoked by one orchestrator in one
   session) — shares the same `orchestrationSessionId` and therefore does **NOT** satisfy Matrix row 59,
   regardless of the two role names being lexically different. This closes the "two agent roles, one
   orchestrator, one session = one well" loophole: role-name inequality is not identity
   inequality, and the promotion gate MUST check `orchestrationSessionId`, not role labels, when either
   actor is `actorType: 'agent'`.
3. A human actor and an agent actor are always distinct, subject to items 1-2 still applying if the
   "human" side is itself a human merely rubber-stamping the same agent session's own output without
   independently driving a separate `orchestrationSessionId`'s reproduction run. With `reproducedBy`'s
   derivation now mechanical (§2 above), a human-authorization entry recorded as `reproducedBy` must
   itself be a real `AuditActor.actorId` resolved the same way — from the `AuditLineageEvent` that
   actually recorded production of the reproduction's own `SimulationRun`/`ValidationArtifact` — which
   structurally rules out a rubber-stamp entry with no corresponding production event behind it. This
   does not eliminate every residual risk: a human reviewer can still choose to authorize a reproduction
   run without meaningfully scrutinizing its output before recording it, and no schema field can compel
   genuine scrutiny. That residual is a process question for how lockbox/reproduction requests are
   authorized (§11.1), not something this field alone can fully police, and reviewers must treat it as
   such.

**Lockbox confirmation:** if the original validation this record
reproduces included Sealed Lockbox confirmation (§11), the independent rerun MUST also include lockbox
confirmation, obtained through a separate, independently-audited lockbox access request per §11.1-§11.2 —
per the Thesis's own wording, "the same test." A reproduction that omits lockbox confirmation the original
included is not a reproduction of "the same test" and MUST NOT be accepted as satisfying this gate. See
§11.3 for how this is distinguished from lockbox contamination.

The exact tolerance for "matched" (exact byte-for-byte event-ledger match vs. some numeric tolerance
band on derived metrics) is a new PROVISIONAL item — see §14.

## 3. Purge and embargo applicability (Matrix row 27)

Per Constitution §5 item 1 and Matrix row 27: purging and embargo are required whenever training/validation
observations or labels can overlap or leak through temporal adjacency; applicability is machine-recorded,
and exceptions require justification.

```typescript
interface PurgeEmbargoDecision {
  /** What in this candidate's data/label structure could cause temporal-adjacency leakage. */
  overlapRisk: 'labels-span-intervals' | 'rolling-feature-windows' | 'none-identified';
  applicable: boolean;
  /** Required whenever applicable is false but overlapRisk is not 'none-identified'. */
  exceptionJustification?: string;
  /** Purge window and embargo period, if applied. Exact sizing rule is PROVISIONAL — see §14 U-04/U-06. */
  purgeWindow?: unknown;
  embargoPeriod?: unknown;
}
```

This document fixes the DECISION STRUCTURE: applicability is derived from the candidate's own label/feature
structure (does a label span an interval that could overlap a neighboring observation's evaluation window;
does a feature use a rolling window that could leak future information into a nominally-past evaluation
point), not asserted as a blanket "always on" or "always off" rule. The exact purge-window/embargo-period
sizing formula is PROVISIONAL (§14).

## 4. CPCV applicability (Matrix row 28, U-06)

Per Constitution §5 item 2: "CPCV is supported and required when the ValidationPlan determines it is
applicable... applicability matrix itself remains open per Matrix Unresolved U-06." Per Matrix row 28: "CPCV
is a first-class validation method where sampling/event structure supports it, not a universal ritual."

```typescript
interface CPCVDecision {
  /** The candidate's sampling/event structure — the inputs the eventual applicability matrix must
   *  key off of, per U-06. This document fixes that these are the relevant input dimensions; it does
   *  NOT fix the decision function mapping them to an applicable/not-applicable verdict. */
  samplingStructure: {
    eventBased: boolean;
    overlappingObservations: boolean;
    pathDependentSizing: boolean;
  };
  applicable: boolean;
  /** Required whenever samplingStructure suggests overlap/path-dependence but applicable is false,
   *  or vice-versa when a simple non-overlapping fixed-interval structure is nonetheless run through CPCV. */
  justification: string;
  /** Number of groups/folds and purge/embargo parameters used, if applicable. PROVISIONAL sizing — §14. */
  foldConfig?: unknown;
}
```

**Decision structure this document fixes (not the thresholds):** CPCV applicability is a function of the
candidate's sampling/event structure — specifically whether observations are event-based rather than
fixed-interval, whether observations can overlap in time, and whether position sizing is path-dependent
(so that split-order matters, which is exactly what CPCV's combinatorial grouping is designed to test).
These are the input dimensions the eventual applicability matrix (U-06) must be built against. This
document does not fix the decision function itself, the exact fold count, or the exact purge/embargo
parameters CPCV uses when applicable — those require the dedicated research design work named in §14.

**skfolio reference implementation, not adopted default:** `docs/research/candidate-reports/PA-05-skfolio.md`
documents `CombinatorialPurgedCV` as a real, citable purge/embargo/CPCV implementation, citing López de
Prado, and self-documents its own deviations from the source paper's defaults (e.g., Ward linkage vs.
single-linkage clustering). It is cited here as a concrete reference implementation candidate for this
section and for §5.3's lockbox mechanism, per the Prior-Art Register (PA-10, row PA-05-01) status: **ADOPT
CANDIDATE, not final ADOPT** — its test suite was never executed per Sol's finding reflected in the
corrected PA-10 register, and direct adoption risks importing scikit-learn/CVXPY's split ontology as Signal
Current's own implicit `ValidationPlan` model unless wrapped behind Signal-Current-owned contracts. §8 below
defines that contract boundary explicitly.

## 5. Temporal out-of-sample evaluation (Matrix row 32)

Per Constitution §5 item 3 and Matrix row 32: temporal OOS evaluation is mandatory; walk-forward is the
default for adaptive/parameterized/model workflows, and otherwise required unless the `ValidationPlan`
records why another temporal design is superior.

```typescript
interface TemporalEvaluationDecision {
  method: 'walk-forward' | 'alternative';
  /** Required whenever method is 'alternative' — walk-forward is the default and must be affirmatively
   *  displaced, not silently skipped. */
  alternativeJustification?: string;
  /** Window sizing (train/test split lengths, step size, number of folds). PROVISIONAL — §14 (U-04). */
  windowConfig?: unknown;
}
```

This document fixes the default-and-displace structure: walk-forward is presumed, and any non-walk-forward
temporal design (e.g., a genuinely one-shot historical strategy per Matrix row 32's own carve-out) requires
an explicit, recorded justification on the `ValidationPlan`, not an implicit omission. Exact window sizing
(train length, test length, step size, number of folds) is a minimum-sample-adequacy question — PROVISIONAL,
deferred to §14's U-04 resolution alongside §7 below.

## 6. Multiple-testing diagnostics (Matrix row 33, U-05)

Per Constitution §5 item 4 and Matrix row 33: multiple-testing and selection-bias diagnostics are mandatory
after any broad search; raw Sharpe is never sufficient evidence; DSR and/or reality-check-style methods are
available where mathematically applicable.

```typescript
interface MultipleTestingDecision {
  /** Search-budget/trial-count context this diagnostic must account for — consumed directly from the
   *  CampaignSpec bookkeeping Research Methodology §9 requires the research stage to produce. */
  trialContext: {
    candidateCount: number;
    rejectionCount: number;
    selectionProcessRef: string;
  };
  /** Which diagnostic family/families were applied. More than one may be recorded; this document does
   *  not mandate a single universal method (see rationale below). */
  methodsApplied: Array<'deflated-sharpe-ratio' | 'whites-reality-check' | 'bootstrap-resampling' | 'other'>;
  /** Why the chosen method(s) are mathematically applicable to this candidate's trial structure —
   *  required, not optional, because applicability differs by method (per U-05's own stated reason). */
  applicabilityJustification: string;
  /** Numeric outcome of each applied method (e.g., a DSR value, a reality-check p-value). The
   *  PASS/FAIL cutoff each numeric outcome is compared against is PROVISIONAL — §14. */
  results: Record<string, unknown>;
  /** Raw Sharpe, reported for context only — never itself a pass criterion (Matrix row 33). */
  rawSharpeContext?: number;
}
```

**Decision structure this document fixes:** raw Sharpe is never a sufficient pass criterion under any
circumstance — this is fixed now, unconditionally, per Constitution §5 item 4 and does not wait on U-05's
resolution. What is deferred to U-05 is *which* trial-aware diagnostic(s) (Deflated Sharpe Ratio, White's
Reality Check, bootstrap resampling, or another named method) apply to a given trial structure, and at what
numeric threshold each is judged to pass. The Matrix's own stated reason for leaving this open — "DSR/
reality-check/bootstrap methods have different applicability" — is itself the reason this document defines
a `methodsApplied` array plus a required `applicabilityJustification`, rather than picking one method as a
universal default. A future resolution of U-05 may still conclude one method is the practical default for
most trial structures encountered in this program; that conclusion requires the dedicated statistical
design work named in §14, not invention here.

**Motivating case (cited per this sprint's explicit instruction):** the Time Series Momentum critique
(`docs/research/candidate-reports/PA-02-time-series-momentum.md`) — Huang, Li, Wang & Zhou (2020) found the
original paper's pooled-regression result was not robust to bootstrap critical values, and performance was
statistically indistinguishable from a naive historical-mean strategy — is the single clearest illustration
in this program of why Matrix row 33 exists at all: a headline Sharpe/regression result that looked
sufficient on its face did not survive a trial-aware/bootstrap re-examination. This is cited as the direct
motivating case for why `methodsApplied` is mandatory and `rawSharpeContext` alone can never gate promotion.

## 7. Minimum sample requirements (U-04)

Per Matrix §7: U-04 ("validation defaults by asset/timeframe and minimum sample requirements") "cannot be
universal without research design." This document fixes the CONTRACT, not the numbers.

```typescript
interface SampleAdequacyDecision {
  /** The asset/timeframe/event-structure combination this candidate was validated under — the
   *  dimensions the eventual minimum-sample research must be keyed against, per Architecture §7.2's
   *  zero-semantic-privilege requirement (no single asset/timeframe gets an implicit default). */
  context: {
    assetClass: string;
    timeframe: string;
    eventStructure: 'fixed-interval' | 'event-based';
  };
  /** Observed sample count(s) relevant to statistical power for this context (e.g., number of
   *  non-overlapping trades, number of independent walk-forward folds). */
  observedSampleCounts: Record<string, number>;
  /** Whether the observed counts meet the minimum required for this context. The minimum itself is
   *  PROVISIONAL — §14. */
  adequate: boolean;
  inadequacyJustification?: string;
}
```

**Why this cannot be a single universal number (fixed now, not deferred):** a minimum-sample requirement
that is adequate for a liquid, high-frequency intraday asset/timeframe combination is not automatically
adequate — or even meaningful in the same units — for a low-frequency, long-horizon, event-based strategy.
Per Architecture §7.2's "zero semantic privilege" requirement (no asset class, timeframe, venue, or
resolution may receive a hardcoded default anywhere in the core), a single global minimum-sample constant
would itself be exactly the kind of silently-promoted engineering default this program's own Research Data
Integrity discipline (this repo's `CLAUDE.md`) and the Qlib/Zipline-reloaded cautionary precedents already
cited in documents 02 and 04 (§7.2, §4.2/§6) warn against. This document therefore fixes only that
`SampleAdequacyDecision.context` must be recorded per candidate, and that the minimum threshold is a
function of that context, not a constant. The function itself is PROVISIONAL (§14, U-04).

## 8. Cost realism (Matrix row 34)

Per Constitution §5 item 6 and Matrix row 34: `CostModel` is a versioned required input; validation includes
baseline costs plus adverse-cost stress; no promotion on a zero-cost assumption unless the instrument truly
has no modeled cost and this is justified.

```typescript
interface CostRealismDecision {
  /** Reference to the versioned CostModel/ExecutionContext this ValidationPlan evaluated against,
   *  per Data Architecture §3's ExecutionContext contract. */
  costModelRef: string;
  /** Whether any component of the referenced CostModel is a zero-cost assumption. */
  zeroCostComponents: string[];
  /** Required whenever zeroCostComponents is non-empty, per Constitution §5 item 6. */
  zeroCostJustification?: string;
  /** Baseline-cost result plus at least one adverse-cost-stress scenario result. The exact stress
   *  magnitude/percentage is PROVISIONAL — see §14. */
  baselineResult: unknown;
  adverseStressResults: unknown[];
}
```

This document fixes that baseline-cost evaluation is never sufficient alone — at least one adverse-cost
stress scenario must also be recorded — and that any zero-cost component requires explicit justification.
The exact adverse-cost stress magnitude (e.g., what multiple of baseline slippage/fees constitutes
"adverse") is PROVISIONAL (§14), consistent with Data Architecture §3.2/§8's own deferral of exact
cost-model default values (U-02c) to a dedicated execution-semantics ADR — this document does not
re-invent that number either.

**Supporting citation (qualitative principle only, not a specific figure):** Kelly & Xiu, "Financial
Machine Learning" (`docs/research/candidate-reports/PA-02-kelly-xiu-financial-ml.md`), is a high-authority
academic source directly supporting the qualitative principle that transaction costs materially erode
apparent edge. This document cites that principle only — it does NOT cite the report's specific "~4x"
figure, which the report itself flags as unverified and not independently checked. No specific cost-erosion
multiplier is adopted here or anywhere in this document.

## 9. Robustness / perturbation families (Matrix row 35)

Per Matrix row 35: separate robustness families exist — parameter sensitivity, execution/cost stress,
trade/path resampling, data perturbation, regime stress — because "different tools use 'Monte Carlo' for
different procedures." Each run records the actual method rather than a generic "Monte Carlo passed."

```typescript
type RobustnessFamily =
  | 'parameter-sensitivity'
  | 'execution-cost-stress'
  | 'trade-path-resampling'
  | 'data-perturbation'
  | 'regime-stress';

interface RobustnessDecision {
  family: RobustnessFamily;
  /** The specific, named method used within this family (e.g., "grid perturbation of entry
   *  threshold ±N steps", "block bootstrap of trade sequence") — never a bare "Monte Carlo" label. */
  methodName: string;
  methodParameters: Record<string, unknown>;
  result: unknown;
}
```

`ValidationPlan.robustness` is an array because more than one family may apply to a single candidate. This
document fixes the requirement that each entry names its specific family and method; it does not fix which
families are mandatory for which candidate types, nor any pass/fail threshold for a given family's result —
those are PROVISIONAL alongside U-04/U-05 (§14), because family applicability depends on the same
asset/timeframe/event-structure context §7 already established cannot be universally defaulted.

**Motivating case (self-admitted instability, cited per this sprint's instruction):** Valeyre (2022)
(`docs/research/candidate-reports/PA-02-valeyre-2022.md`) — the paper's own author admits its combination-
weight optimum is "not so robust and very sensitive to the estimation period." This is a direct illustration
of why a single point-estimate backtest is insufficient and why `parameter-sensitivity` robustness testing
is a first-class, separately-named family here, not folded into an undifferentiated "run it again a few
times" step.

## 10. Triple-barrier, meta-labeling, and sample-uniqueness weighting (Matrix rows 29-31)

Per Constitution §5 item 8 (already stated, not re-decided here) and Matrix rows 29-31: these are first-class
capabilities for applicable ML/event workflows, not imposed on deterministic rule strategies.

```typescript
interface LabelingWorkflowDecision {
  workflowType: 'deterministic-rule' | 'event-label-ml';
  /** Present only when workflowType is 'event-label-ml'. */
  tripleBarrier?: { applied: boolean; parametersRef?: unknown };
  metaLabeling?: { applied: boolean; primaryStrategyRef: string; secondaryModelRef?: unknown };
  sampleUniquenessWeighting?: { applied: boolean; method?: unknown };
}
```

This document's contribution beyond Constitution §5 item 8 is the `ValidationPlan`-level contract for *when*
these apply: a candidate's `workflowType` is recorded explicitly (deterministic-rule vs. event-label-ml),
and the triple-barrier/meta-labeling/uniqueness-weighting sub-decisions are only populated — and only
required — when `workflowType` is `'event-label-ml'`. A deterministic rule strategy's `ValidationPlan` is not
penalized or blocked for lacking labels it structurally does not use, satisfying Matrix rows 29-31's "not
imposed on deterministic rule strategies that don't need them." Per Matrix row 30, meta-labeling keeps the
primary `StrategyIR` side/intent separate from a secondary ML acceptance/sizing model — `primaryStrategyRef`
and `secondaryModelRef` are recorded as distinct fields specifically to preserve that separation, never
merged into one opaque model reference.

## 11. Sealed Lockbox enforcement (Matrix row 36)

Per Constitution §4 and Matrix row 36: "Lockbox must be protected at the data/service permission layer, not
merely hidden in UI." Architecture §4 already establishes the Data Access Gateway as the sole mediated
access path and the general zone-boundary mechanism; this document specifies the operational mechanism
specific to lockbox access as it bears on the Validation & Statistical Controls Service.

### 11.1 Who/what can request lockbox access

1. Only the Validation & Statistical Controls Service, acting on a `StrategyIR Candidate` that has already
   produced a `ValidationArtifact` recording that it passed every required validation-zone gate named in
   §§3-10 above, may request lockbox-zone data through the Data Access Gateway (Architecture §4.1).
2. No Campaign/Generator Service, no search/tuning code path, and no agent tool call may request lockbox
   access directly, under any circumstance — this restates Constitution §6's "may NOT... access sealed
   confirmation/lockbox data through an unauthorized path" as a concrete request-authorization rule scoped
   to this service.
3. A lockbox access request is itself a distinct, typed request object, not a parameterized ordinary
   `DataSnapshot` read — this is what lets the gateway (Architecture §4.1) apply a materially different,
   audited code path rather than relying on caller discipline.

### 11.2 What gets audited

Per Constitution §9.1 and Architecture §4.1, every lockbox access request produces a versioned audit event
in the Audit/Event Log (Architecture §5) distinct from ordinary validation-zone reads, recording at minimum:

1. the requesting `ValidationPlan`/`StrategyIR Candidate` identity (content-addressed, per Data Architecture
   §2.4);
2. the `ValidationArtifact` reference showing the candidate already passed validation-zone gates, as the
   precondition for the request being honored at all;
3. actor and correlation/causation ID (Constitution §9.1, Matrix row 53);
4. timestamp and outcome (granted/denied).

### 11.3 What "may invalidate a research cycle" means operationally

Per Constitution §4: lockbox access "may invalidate a research cycle." Operationally, this document fixes:

1. A second lockbox access request against a materially-modified candidate (i.e., a new content-addressed
   `StrategyIR Candidate` identity descending from the same `CampaignSpec`/research lineage as a prior
   lockbox-accessed candidate) is itself logged as a distinct audited event, cross-referenced to the prior
   request via the lineage chain Research Methodology §9 item 6 already requires the `CampaignSpec` to
   record.
2. This cross-reference is what lets a human reviewer (Promotion/Gate Service consumer, per Architecture §5)
   identify a "peek and re-tune" pattern — repeated lockbox requests against successively modified
   candidates from the same lineage — and treat the earlier cycle's lockbox-informed promotion as void. The
   exact review procedure/threshold for when a reviewer must void a cycle is a human-judgment gate, not a
   mechanical rule this document invents; this document fixes only that the audit trail makes the pattern
   visible, per Matrix row 36's own framing that "human curiosity can contaminate it even if code is
   blocked."
3. This mechanism does not, by itself, retroactively delete or mutate the prior `ValidationArtifact` or
   `StrategyArtifact` (Constitution §3.6's no-silent-mutation principle) — voiding is recorded as a new
   `DecisionRecord` (Architecture §3.1), never a silent rewrite of history.
4. **Distinguishing a legitimate audited independent reproduction from "peek and re-tune" contamination:**
   a lockbox access request made to satisfy §2.1's
   independent-reproduction gate is a *repeat* request against the *same* content-addressed
   `StrategyIR Candidate`/`ValidationPlan` identity, by a *distinct* actor (§2.1), producing a *matching*
   result — this is structurally different from the contamination pattern above, which is a repeat request
   against a *materially-modified* candidate descending from the same lineage. The audit trail (§11.2)
   already distinguishes these: a reproduction-gate request's `ValidationArtifact` reference (§11.2 item 2)
   points to the same candidate identity as the original request, whereas a "peek and re-tune" request's
   reference points to a new, modified candidate identity in the same lineage chain. A reviewer (or the
   Promotion/Gate Service, mechanically) checking whether two lockbox requests share one candidate identity
   (legitimate reproduction) versus descend from one lineage but differ in candidate identity (contamination
   pattern) is how this document's own audit mechanism tells the two apart — it is not a new mechanism, only
   this document's explicit statement of how the existing one applies to §2.1's gate.

### 11.4 Design precedent — why this must be a permission-layer mechanism, not UI convention

**Cautionary precedent (REFERENCE only):** the MASTER stock transformer's self-disclosed validation-pipeline
bug (`docs/research/candidate-reports/PA-04-master-stock-transformer.md`) — a train/val processing
inconsistency discovered only post-publication — is cited as a concrete illustration of why the Sealed
Lockbox must be protected at the data/service permission layer rather than hidden in UI or relied upon as
researcher discipline: even well-intentioned, published research can leak validation data without anyone
noticing until after the fact. This is the direct motivating case for §11.1's request-authorization rule and
§11.2's mandatory audit trail — a UI affordance that merely discourages lockbox access would not have caught
this class of bug; a permission-layer gate that only the Validation Service can invoke, and that logs every
invocation, is structurally different.

## 12. skfolio contract boundary — ValidationPlan interface independent of implementation library

Per Architecture §11 (already establishing the general principle) and Sol's finding reflected in the
corrected PA-10 register (row PA-05-01): skfolio is an ADOPT CANDIDATE, not a final ADOPT, and "adopting
CPCV directly would silently import skfolio/scikit-learn's estimator, model-selection and split ontology as
Signal Current's implicit ValidationPlan model — that must be an explicit decision, not a side effect."

This document fixes the boundary specific to the Validation & Statistical Controls Service, extending
Architecture §11's general statement to this document's own `ValidationPlan` interface defined in §§2-10
above:

1. The `ValidationPlan` interface (§2) and its sub-decision types (§§3-10) are the only surface the
   Validation & Statistical Controls Service exposes to the Promotion/Gate Service and to any other domain.
   No field of `ValidationPlan` may be, or be shaped like, a scikit-learn/CVXPY estimator object, a
   scikit-learn `cross_validate`/split-generator return type, or any other library-native type — every field
   is either a Signal-Current-owned primitive (string, number, boolean) or a Signal-Current-defined nested
   interface.
2. If skfolio's `CombinatorialPurgedCV` (or HRP/NCO/CVaR/CDaR modules) is adopted following PA-10's own
   stated acceptance gates (out of this sprint's scope), it is wrapped entirely inside the implementation of
   `CPCVDecision`'s (§4) internal computation — e.g., an internal adapter function that accepts this
   document's own candidate/sampling-structure inputs and returns this document's own `foldConfig`/`results`
   shape, never skfolio's native fold-index or estimator objects surfacing through the `ValidationPlan`
   itself.
3. This wrapping requirement applies equally to any alternative CPCV/multiple-testing/robustness library
   Signal Current might adopt instead of or alongside skfolio — the contract boundary is the `ValidationPlan`
   interface defined in this document, not any specific library's ontology, per Constitution §10's general
   "no external library's internal ontology may silently become Signal Current's domain model."
4. This does not pre-decide skfolio's ADOPT status. That decision remains PA-10's register's own job, gated
   on skfolio's test suite actually being executed (per Sol's finding) — out of this sprint's scope, per the
   Requirements document's "Out of Scope" section.

## 13. Consistency check against Constitution, Architecture, Research Methodology, Data Architecture, and Agent & Orchestration Layer

**Re-run 2026-09-05** — the prior version of this section predated documents 02 §3.1/§3.2/§5, 04 §5.4,
and 07 §3.1/§3.2's further amendments (this fix pass) and was stale. This document was re-checked for
contradiction against the current text of `01-constitution.md`, `02-system-architecture.md` (as amended by
this fix pass), `03-research-methodology.md`, `04-data-architecture-strategy-ir.md` (as amended by this fix
pass), and `07-agent-orchestration-layer.md` (as amended by this fix pass), in full. No conflict was found:
every clause above elaborates a boundary or contract those five documents already establish (Constitution
§5, §4, §9, §3 item 7; Architecture §3, §4, §5, §11; Research Methodology §9; Data Architecture §2.4, §3,
§5, §5.4; Agent & Orchestration §3.1, §3.2, §11) at the validation-decision level, without contradicting or
silently re-deciding any of their fixed clauses. In particular:

- this document does not re-decide the Exploration/Validation/Lockbox zone mechanism itself (Architecture
  §4 already fixes the Data Access Gateway as the sole enforcement point) — it specifies the request/audit
  behavior specific to lockbox access as consumed by this service (§11);
- this document does not re-decide `CostModel`/`ExecutionContext` field schema (Data Architecture §3,
  U-02c) — it consumes those fields by reference (§8) and defers their exact default values to the same
  execution-semantics ADR Data Architecture §8 already names;
- this document does not re-decide search-budget/hypothesis-lineage recording mechanics (Research
  Methodology §9) — it consumes that bookkeeping as an input to `MultipleTestingDecision.trialContext`
  (§6);
- this document's §2 `originalAuthor`/`reproducedBy` derivation sentences (this fix pass) now name the
  exact `AuditLineageEvent` each field is resolved from — the event recording production of the original
  `SimulationRun`/`ValidationArtifact`, and the event recording production of the reproduction's own
  `SimulationRun`/`ValidationArtifact`, respectively — which is consistent with, and consumes without
  re-deciding, document 04 §5.4's `AuditLineageEvent.artifactRefs` field and its statement (also amended
  this fix pass) that the Validation Service, not the caller, derives both actor fields;
- this document's §2.1 item 2 "distinct identity" rule for agent actors is now consistent with document 04
  §5.4's `orchestrationSessionId` being required (not optional) when `actorType === 'agent'`, and with
  document 07 §3.1 item 3/§3.2's reconciling statement that every agent run carries the
  `orchestrationSessionId` of the session that spawned it — this section consumes both as the mechanism by
  which agent-actor distinctness is checked, it does not invent a second identity model, and the "two agent
  roles, one orchestrator, one session" loophole closed at §2.1 item 2 is now closed at its source (document
  07) as well as consumed here;
- §2.1 item 3's human-rubber-stamp clause (tightened this fix pass) is consistent with document 04 §5.4's
  actor-derivation rule: a human authorization recorded as `reproducedBy` must resolve to a real
  `AuditActor.actorId` from the event that actually produced the reproduction, the same mechanical
  derivation §2 now states for every `reproducedBy` value, agent or human.

## 14. PROVISIONAL items and resolution paths

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| **U-04** — Minimum-sample-size numbers, and validation-default parameters (window sizing for walk-forward, purge-window/embargo-period sizing), per asset/timeframe/event-structure combination | PROVISIONAL — unvalidated | Danny | Dedicated research-design pass deriving minimum-sample requirements per the heterogeneous asset/timeframe/event-structure coverage the conformance program (Matrix row 57) will actually exercise |
| **U-05** — Which multiple-testing diagnostic(s) (DSR / White's Reality Check / bootstrap resampling / other) apply by default to which trial structures, and the numeric pass/fail cutoff for each | PROVISIONAL — unvalidated | Danny | Dedicated statistical-methods design pass evaluating DSR/reality-check/bootstrap-style methods against this program's actual campaign/trial structures, justified against a named statistical method per this repo's no-fabricated-constants discipline |
| **U-06** — CPCV applicability decision function (mapping `CPCVDecision.samplingStructure` to an applicable/not-applicable verdict) and CPCV fold count / purge-embargo sizing when applicable | PROVISIONAL — unvalidated | Danny | Dedicated research-design pass defining the applicability matrix, informed by whichever CPCV reference implementation (e.g., skfolio's `CombinatorialPurgedCV`, pending its own PA-10 acceptance gates) is used for comparison, per Matrix row 28's "applicability/default matrix" designation |
| Adverse-cost-stress magnitude/percentage (§8) | PROVISIONAL — unvalidated | Danny | Resolved in the same execution-semantics ADR Data Architecture §8 already names for U-02c's cost-model default values — not a separate, second-guessed number |
| Robustness-family (§9) applicability-by-candidate-type rules and pass/fail thresholds per family | PROVISIONAL — unvalidated | Danny | Resolved alongside U-04, since family applicability depends on the same asset/timeframe/event-structure context U-04's research design must characterize |
| **Independent-reproduction match tolerance** (§2.1) — whether `IndependentReproductionRecord.matched` requires exact event-ledger match or some numeric tolerance band on derived metrics, and if the latter, the tolerance value itself | PROVISIONAL — unvalidated | Danny | Resolved alongside U-01's schema design session (Roadmap §3 Phase R1), since the exact reproduction bar depends on the same content-addressing/hashing mechanism (Data Architecture §2.4/§5, U-01c) that determines what "same content-addressed inputs" precisely means |

No numeric constant (p-value, DSR cutoff, minimum sample size, CPCV fold count, cost-stress percentage,
purge-window/embargo-period length, independent-reproduction match tolerance) is introduced anywhere in
this document. Every PROVISIONAL item above is a statistical/research-design decision explicitly flagged by
the Reconciliation Matrix (U-04/U-05/U-06), an execution-semantics decision already deferred by Data
Architecture §8, or the new independent-reproduction tolerance surfaced by Matrix row 59 — none is guessed
here.

## 15. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Portfolio/
Deployment and Agent specification work (Constitution §11), and MUST be revised once the dedicated design
work named in §14 resolves U-04/U-05/U-06 — at that point this document is amended (not silently
superseded) to replace the PROVISIONAL tags with cited, versioned thresholds. After freeze, amendment
requires an explicit ADR and version change.
