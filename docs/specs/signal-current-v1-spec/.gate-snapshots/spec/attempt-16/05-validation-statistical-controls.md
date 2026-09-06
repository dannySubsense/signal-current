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
of body text/doc-comments into this header line. @architect, 2026-09-05, per Frank's spec-gate attempt-4 findings F1 (overreach)/fix items
4/5 — §2.1 item 2 now also checks the session-initiation event's human `actorId` for distinctness when either
actor is an agent, not `orchestrationSessionId` inequality alone (document 04 §5.4's session-initiation-
authority rule makes this checkable); §2.1 item 3 is rescoped to state precisely what the derivation rule
rules out versus what it still defers to document 06 §16's widened U-12 PROVISIONAL item; re-ran §13 against
the now-further-amended documents 02/04/07. **HALT-flagged, not blocking:** whether two sessions initiated by
the same human principal should, by themselves, count as non-distinct for row 59's purposes is a genuine
open question about what "distinct" means at the human level (a legitimately different validator and
reproducer could share one human-operated account) — flagged to Danny in this pass rather than silently
resolved; see §2.1 item 2's closing note. @architect, 2026-09-05, per Frank's spec-gate attempt-5 fixes 3/5 — added a sentence to §2.1 stating an `IndependentReproductionRecord` cannot satisfy the gate until document 06 §16's widened U-12 item's actor/session-verification sub-items resolve, mirroring the match-tolerance item's existing treatment; added a real §14 table row for the human-actorId-distinctness judgment call §2.1 item 2 raises (previously an orphaned §13 cross-reference); added `08-implementation-roadmap.md` to §13's consistency-check list and re-ran that check against document 08 §3's Phase R1 sequencing. @architect, 2026-09-05, per Frank's spec-gate attempt-6 Carried Condition 2 — added a "see §14" cross-reference to §2.1 item 2's closing note, so the sentence that raises the human-actorId-distinctness judgment call now points directly at the §14 table row tracking it, instead of that pointer existing only in this header line and in §13. @architect,
2026-09-05, per Sol's cold review (target SHA 99d3673) — closed a gap items 1-2 left open: item 1 (two
human `AuditActor`s) treated different `actorId`s as *always* distinct with no check for one natural person
operating under two accounts, and item 3 (human/agent pair) checked only the rubber-stamp case, not whether
the human is the same person who controls the agent's session. Rewrote items 1 and 3 to require distinct
`controllingPrincipalId` (document 04 §5.4, added this pass), widened the §2.1 "Flagged open question"
paragraph and the §14 human-distinctness table row to cover all three items (not item 2 alone), and re-ran
§13 against the now-further-amended documents 04/06/07/08. @architect, 2026-09-06, per Frank's spec-gate attempt-10 finding — closed the fail-open loophole Cold Frank found: document 04 declared `controllingPrincipalId` optional with no stated absence treatment while this document's §2.1 items 1 and 3 stated an unconditional MUST, so an absent field fell back to bare `actorId` comparison. Added an explicit fail-closed clause to items 1 and 3 (absence is non-distinct, never a fallback), reworded the §2.1 definition sentence so items 1-3 read as the sufficient conditions, deleted the "(ordinarily its own `actorId`)" default language from item 3, extended the §2.1 closing Phase R1 blocking paragraph to name `controllingPrincipalId` resolution, and re-ran §13 against document 04's matching fail-closed fix. @architect, 2026-09-06, per Frank's spec-gate attempt-11 finding — closed two verified gaps: item 2 (agent vs. agent) now carries the same `controllingPrincipalId` requirement and fail-closed absence clause items 1 and 3 already had, matching the §2.1 definition sentence's existing (previously unimplemented) claim that all three items require it; reworded item 3 so the agent side resolves to the session-initiating human's own `controllingPrincipalId`, not that human's `actorId`, matching document 04 §5.4's redefinition in the same pass; re-ran §13 naming this specific item-2/definition-sentence contradiction as what the re-run closes. @architect, 2026-09-06, per Frank's spec-gate attempt-13 finding — the §2.1 "Flagged open question" paragraph (attempt-4/5-era text predating the whole controllingPrincipalId fix sequence) still named `AuditActor.actorId` as what the interim distinct-identity default and its open judgment call depend on; read §2.1 and document 04 §5.4 in full end to end (not a keyword sweep against prior verdicts) and corrected every live sentence found to name `controllingPrincipalId` instead, re-ran §13, and identified document 08's remaining occurrences for `@planner` to fix under the same full-read instruction.

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
not merely a different string, and not merely a different role label. A different `actorId` is necessary
but not sufficient (Frank spec-gate attempt-10): items 1-3 below state the actual sufficient conditions,
each of which additionally requires a different `controllingPrincipalId` (document 04 §5.4), resolved and
compared fail-closed per that document's absence rule — never satisfied by `actorId`/`orchestrationSessionId`
inequality alone, and never satisfied when `controllingPrincipalId` is absent on either side. Concretely:

1. Two `AuditActor` entries with `actorType: 'human'` and different `actorId`s are distinct only if they
   also resolve to different `controllingPrincipalId`s (document 04 §5.4, added per Sol's cold review,
   target SHA 99d3673). Different `actorId` alone is not sufficient: the same natural person operating
   under two different accounts or sets of credentials — for example, filing an original validation under
   one login and its own reproduction under another — has one `controllingPrincipalId` and therefore does
   **NOT** satisfy Matrix row 59, regardless of the two `actorId`s being lexically different. This closes
   the human-side analog of item 2's "one orchestrator, one session" loophole: account-label inequality is
   not identity inequality. **Fail-closed (Frank spec-gate attempt-10):** if either side's
   `controllingPrincipalId` is absent, this item does NOT pass on `actorId` inequality alone — an absent
   `controllingPrincipalId` is treated as non-distinct, per document 04 §5.4's absence rule, never as a
   fallback to bare `actorId` comparison. Exactly how `controllingPrincipalId` is resolved for a human
   `actorId` is PROVISIONAL — see item 2's closing note and §14.
2. Two `AuditActor` entries with `actorType: 'agent'` are distinct **only if** they carry different
   `orchestrationSessionId`s AND also resolve to different `controllingPrincipalId`s (document 04 §5.4)
   (Frank spec-gate attempt-11 finding: the §2.1 definition sentence above already claimed items 1-3 all
   require a distinct `controllingPrincipalId`, but this item was never actually amended to check it — this
   closes that contradiction). An agent rerun of an agent-authored validation **within the same
   orchestration session** as the original — even when driven by a different tool/role name (e.g. a
   "validator" role and a separately-named "reproducer" role both invoked by one orchestrator in one
   session) — shares the same `orchestrationSessionId` and therefore does **NOT** satisfy Matrix row 59,
   regardless of the two role names being lexically different. This closes the "two agent roles, one
   orchestrator, one session = one well" loophole: role-name inequality is not identity
   inequality, and the promotion gate MUST check `orchestrationSessionId`, not role labels, when either
   actor is `actorType: 'agent'`. **Fail-closed (Frank spec-gate attempt-10, extended to this item per
   attempt-11):** if either side's `controllingPrincipalId` is absent, this item does NOT pass on
   `orchestrationSessionId` inequality alone — an absent `controllingPrincipalId` is treated as
   non-distinct, per document 04 §5.4's absence rule, never as a fallback to bare `orchestrationSessionId`
   comparison.

   **Extended check (Frank spec-gate attempt-4 fix 4):** `orchestrationSessionId` inequality alone is
   necessary but not sufficient. Per document 04 §5.4's session-initiation-authority rule, every
   `orchestrationSessionId` traces back to exactly one session-initiation `AuditLineageEvent` whose `actor`
   is the human principal who started it, and whose own `controllingPrincipalId` (document 04 §5.4, as
   redefined per Frank spec-gate attempt-11) is what this item's `controllingPrincipalId` comparison above
   actually resolves to for the agent side. When either `originalAuthor` or `reproducedBy` resolves to an
   `AuditActor` with `actorType: 'agent'`, the promotion gate MUST also resolve each side's session-initiation
   event and compare the human's `controllingPrincipalId` on each — not merely the `orchestrationSessionId`s
   or the human `actorId`s themselves. Two distinct `orchestrationSessionId`s initiated by humans who share
   the same `controllingPrincipalId` do NOT, by themselves, satisfy this item; this is what actually closes
   the residual "one orchestrator quietly spans two sessions," and the further "one natural person holds two
   session-initiating accounts," loopholes a fresh-session-per-role pattern would otherwise leave open, since
   fresh sessions alone no longer suffice.

   **Flagged open question, not silently resolved (per this pass's explicit instruction):** whether this is
   the right disposition is a genuine judgment call this document does not have standing to settle
   unilaterally. Requiring distinct human initiators could produce a false positive against a legitimate
   configuration — e.g., two different individual validators who each independently drive a reproduction
   under one team's shared human-operated service account, which is a real distinct-identity case at the
   level Matrix row 59 actually cares about ("someone other than the original author"), even though both
   sessions trace to the same `controllingPrincipalId`. This document does not pick between "same human
   `controllingPrincipalId` disqualifies" and "same `controllingPrincipalId` is fine if the underlying
   operator is provably different" — that choice depends on how `controllingPrincipalId` for humans is
   actually resolved (one `controllingPrincipalId` per natural person vs. per shared account), which is
   exactly the identity-provider/token-mechanism question document 06 §16's widened U-12 item (cross-
   referenced from document 04 §8 and document 07 §12) already defers. Until U-12 resolves, this document
   adopts the stricter reading above (same human `controllingPrincipalId` never satisfies distinctness when
   either side is an agent) as the safer default, and flags it to Danny as requiring confirmation once U-12's
   identity model is chosen — not as a settled design decision; see §14.

   **Widened, not a new question (Sol's cold review, target SHA 99d3673):** the same open judgment call
   applies identically to item 1's pure human-vs-human comparison and item 3's human/agent-controller
   comparison, not only to this item's agent-vs-agent case — all three items now resolve `controllingPrincipalId`
   the same way, and all three inherit the same interim stricter reading (a shared `controllingPrincipalId`
   never satisfies distinctness) pending the same U-12 identity-model resolution. This document does not
   introduce a second, differently-scoped judgment call for items 1-3; §14's table row is widened to name
   all three items rather than item 2 alone.
3. A human actor and an agent actor are distinct only if the human's `controllingPrincipalId`
   differs from the agent's `controllingPrincipalId` — which, for the agent side, resolves to the
   `controllingPrincipalId` (never the `actorId`) of the human who holds session-initiation authority for
   its `orchestrationSessionId` (document 04 §5.4, added per Sol's cold review, target SHA 99d3673;
   redefined in one namespace for both `actorType`s per Frank spec-gate attempt-11, closing the gap where
   resolving the agent side to that human's `actorId` instead let this item pass even when the same
   natural person controlled both sides). **Fail-closed (Frank spec-gate attempt-10):** if either
   side's `controllingPrincipalId` is absent, this item does NOT pass — an absent `controllingPrincipalId`
   is treated as non-distinct, never as a fallback to bare `actorId`/`actorType` inequality. This is in
   addition to, not a replacement for, the existing
   rubber-stamp caveat below: a human who is the same natural person who initiated the orchestration session
   driving an agent-authored original (or its reproduction) is not a distinct identity merely because
   `actorType` differs across the two sides — that human controls both. Subject also to items 1-2 still
   applying if the "human" side is itself a human merely rubber-stamping the same agent session's own output
   without independently driving a separate `orchestrationSessionId`'s reproduction run. With `reproducedBy`'s
   derivation now mechanical (§2 above), a human-authorization entry recorded as `reproducedBy` must
   itself be a real `AuditActor.actorId` resolved the same way — from the `AuditLineageEvent` that
   actually recorded production of the reproduction's own `SimulationRun`/`ValidationArtifact`.

   **Rescoped precisely (Frank spec-gate attempt-4 fix 5 — attempt-3's wording overreached, claiming this
   rule "structurally rules out a rubber-stamp entry" outright; that was true only once document 04 §5.4's
   `actor`-enforcement point existed, which it did not until this fix pass):** what this derivation rule now
   mechanically rules out is a `reproducedBy` (or `originalAuthor`) value with no actual production event
   behind it at all — a bare free-text name or a self-reported identity with nothing in the Audit/Event Log
   to back it. That is enforced now that document 04 §5.4 names the recording component as the sole
   populator of `actor` and states that a caller-supplied `actor` is malformed and rejected. What this rule
   still defers, and does not claim to close, is authenticity at the identity-provider level: whether the
   authenticated caller principal document 04 §5.4 refers to is really who it claims to be, and whether a
   human reviewer's authorization was backed by genuine scrutiny of the reproduction's output before
   recording it. Both of those are deferred to document 06 §16's widened U-12 PROVISIONAL item (cross-
   referenced from document 04 §8 and document 07 §12) and, for the scrutiny question specifically, remain a
   process question for how lockbox/reproduction requests are authorized (§11.1) that no schema field can
   fully police.

**Lockbox confirmation:** if the original validation this record
reproduces included Sealed Lockbox confirmation (§11), the independent rerun MUST also include lockbox
confirmation, obtained through a separate, independently-audited lockbox access request per §11.1-§11.2 —
per the Thesis's own wording, "the same test." A reproduction that omits lockbox confirmation the original
included is not a reproduction of "the same test" and MUST NOT be accepted as satisfying this gate. See
§11.3 for how this is distinguished from lockbox contamination.

The exact tolerance for "matched" (exact byte-for-byte event-ledger match vs. some numeric tolerance
band on derived metrics) is a new PROVISIONAL item — see §14. Mirroring that same treatment
(Frank spec-gate attempt-5 fix 3, extended per Frank spec-gate attempt-10): until document 06 §16's widened
U-12 item's actor/session-verification sub-items — `AuditActor.actorId` authenticity,
`orchestrationSessionId` minting/verification, AND `controllingPrincipalId` resolution (document 04 §5.4) —
resolve, an `IndependentReproductionRecord` cannot satisfy this gate either — the same Phase R1 dependency
that blocks the match-tolerance item above also blocks this one, since all three are load-bearing for the
first `StrategyArtifact` promotion. This matches document 04 §8/§9, document 06 §16, document 07 §12, and
document 08 §3, all of which now enumerate `controllingPrincipalId` resolution alongside actor/session
authenticity as a single Phase R1 sub-resolution, not two.

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

## 13. Consistency check against Constitution, Architecture, Research Methodology, Data Architecture, Agent
& Orchestration Layer, and Implementation Roadmap

**Re-run 2026-09-05, per Sol's cold review (target SHA 99d3673)** — this document's amended §2.1 items 1
and 3 (now consuming `controllingPrincipalId`), the widened §2.1 "Flagged open question" paragraph, and the
widened §14 human/controller-distinctness row were checked against document 04 §5.4 (source of
`controllingPrincipalId` and its "Controller/natural-person equivalence" paragraph, amended the same pass),
document 06 §16, document 07 §3.1/§12, and document 08 §3's Phase R1 sequencing (all amended in the same
pass with matching wording). No conflict was found; item 2's existing agent-side rules are unchanged by
this pass, per this sprint's explicit instruction not to weaken them. No HALT condition applies.

**Re-run 2026-09-05, Frank spec-gate attempt-5 fix 5** — added `08-implementation-roadmap.md` to this
section's consistency-check list (it was not previously covered, which is how attempt-5's U-12 sequencing
finding went uncaught). This document was re-checked for contradiction against the current text of
`01-constitution.md`, `02-system-architecture.md`, `03-research-methodology.md`,
`04-data-architecture-strategy-ir.md` (as amended by this fix pass), `06-portfolio-deployment-monitoring.md`
§16 (as amended by this fix pass), `07-agent-orchestration-layer.md` (as amended by this fix pass), and
`08-implementation-roadmap.md` §3, in full. No conflict was found: every clause above elaborates a boundary or
contract those seven documents already establish (Constitution §5, §4, §9, §3 item 7; Architecture §3, §4,
§5, §11; Research Methodology §9; Data Architecture §2.4, §3, §5, §5.4; Portfolio/Deployment §16; Agent &
Orchestration §3.1, §3.2, §11, §12; Implementation Roadmap §3) at the validation-decision level, without
contradicting or silently re-deciding any of their fixed clauses. In
particular:

- this document does not re-decide the Exploration/Validation/Lockbox zone mechanism itself (Architecture
  §4 already fixes the Data Access Gateway as the sole enforcement point) — it specifies the request/audit
  behavior specific to lockbox access as consumed by this service (§11);
- this document does not re-decide `CostModel`/`ExecutionContext` field schema (Data Architecture §3,
  U-02c) — it consumes those fields by reference (§8) and defers their exact default values to the same
  execution-semantics ADR Data Architecture §8 already names;
- this document does not re-decide search-budget/hypothesis-lineage recording mechanics (Research
  Methodology §9) — it consumes that bookkeeping as an input to `MultipleTestingDecision.trialContext`
  (§6);
- this document's §2 `originalAuthor`/`reproducedBy` derivation sentences name the exact `AuditLineageEvent`
  each field is resolved from, consistent with document 04 §5.4's `AuditLineageEvent.artifactRefs` field and
  its statement that the Validation Service, not the caller, derives both actor fields — this section does
  not restate or duplicate document 04 §5.4's newly-added `actor`-enforcement-point sentence (Frank spec-gate
  attempt-4 fix 1), it simply consumes the fact that `actor` is never caller-supplied, which is what makes
  the derivation sentences here meaningful rather than aspirational;
- this document's §2.1 item 2 "distinct identity" rule for agent actors is consistent with document 04
  §5.4's `orchestrationSessionId` being required when `actorType === 'agent'` and with its newly-added
  session-initiation-authority paragraph (a session is initiated only by a human, recorded as its own
  `AuditLineageEvent`), and with document 07 §3.1 item 3/§3.2's matching restatement that a single continuous
  orchestration necessarily stays within the one session a human initiated it under — this section's
  extended check (comparing the session-initiation event's human `actorId`, not `orchestrationSessionId`
  alone) is the mechanism that actually depends on document 04's session-initiation-authority rule existing;
  before this fix pass, that rule did not exist in document 04, so this section's item 2 previously rested on
  an unstated assumption, now closed at its source;
- §2.1 item 3 is now rescoped (Frank spec-gate attempt-4 fix 5) to state precisely what the derivation rule
  rules out (a `reproducedBy`/`originalAuthor` with no production event behind it, mechanically enforced now
  that document 04 §5.4 names the enforcement point) versus what it defers (actor/session authenticity at the
  identity-provider level, to document 06 §16's widened U-12 item) — this matches, rather than overreaches
  beyond, what document 04 §5.4 and document 06 §16 actually establish;
- this document's new PROVISIONAL cross-reference (§2.1 item 2's flagged open question, pointing to document
  06 §16's widened U-12 item) is consistent with, and does not duplicate, the identical cross-references
  added this fix pass to document 04 §8 and document 07 §12 — all three name the same resolution condition.
- this document's §14 PROVISIONAL table now carries a real row for the human-actorId-distinctness judgment
  call §2.1 item 2 raises (Frank spec-gate attempt-5 fix 3) — previously this section claimed a "new
  PROVISIONAL cross-reference" that pointed at nothing in §14; that gap is closed;
- this document's §2.1 (both the match-tolerance and the newly-added actor/session-verification sentence)
  and §14's PROVISIONAL rows now name Phase R1 as the resolution point for the actor/session-verification
  sub-items, checked against document 08 §3's Phase R1 sequencing and found consistent — this is the same
  Phase R1 sequencing document 06 §16, document 04 §8, and document 07 §12 all name identically. No further
  sequencing inconsistency was found in document 08 as a result of this check.

**Re-run 2026-09-06, per Frank's spec-gate attempt-10 finding** — this document's §2.1 (definition sentence,
items 1-3, and the closing "load-bearing" paragraph) was checked against document 04 §5.4's newly fail-closed
`controllingPrincipalId` treatment. The gap attempt-10 found: document 04 declared `controllingPrincipalId`
optional with no stated treatment for absence, while this document's items 1 and 3 stated an unconditional
MUST — an absent field on either side fell straight back to bare `actorId` comparison, the exact loophole
Sol's cold review closed by name. This pass adds an explicit fail-closed clause to items 1 and 3 (absence is
non-distinct, never a fallback), rewords the definition sentence (§2.1) so items 1-3 read as the sufficient
conditions rather than restating `actorId` inequality as sufficient on its own, deletes the "(ordinarily its
own `actorId`)" default language from item 3, and extends the closing Phase R1 blocking paragraph to name
`controllingPrincipalId` resolution alongside `actorId`/`orchestrationSessionId` authenticity as a single
Phase R1 sub-resolution. Checked against document 04 §5.4/§8/§9 (amended the same pass with matching
fail-closed wording and the same recording-component populator statement) — both documents now state
identically that absence is non-distinct, never a fallback. No new contradiction was found; no HALT
condition applies.

**Re-run 2026-09-06, per Frank's spec-gate attempt-11 finding:** this pass closes two related, verified
gaps attempt-11 named. First, document 04 §5.4's `controllingPrincipalId` definition previously resolved
to a different namespace on each `actorType` branch (a natural-person identity for humans, but the
session-initiating human's own `actorId` — not that human's `controllingPrincipalId` — for agents), so
item 3 (human vs. agent) here could pass even when the same natural person controlled both sides; document
04 §5.4 is amended this pass to resolve both branches to the same kind of value, and item 3 above is
reworded to match. Second, this document's own §2.1 definition sentence (line ~203) already asserted that
items 1-3 all require a distinct `controllingPrincipalId` and are never satisfied by `actorId`/
`orchestrationSessionId` inequality alone, but item 2 (agent vs. agent) was never actually amended to check
`controllingPrincipalId` — it checked only `orchestrationSessionId` and session-initiating human `actorId`
inequality, so two agents whose sessions were initiated by one person under two accounts could pass item 2
despite the definition sentence's claim to cover it. Item 2 above is amended this pass to add the same
`controllingPrincipalId` requirement and fail-closed absence clause items 1 and 3 already carry, closing
that contradiction rather than merely asserting it does not exist. Checked against document 04 §5.4's
matching redefinition (amended the same pass) and document 04 §9's re-run of the same finding — both
documents now resolve `controllingPrincipalId` in one namespace across `actorType`s, and all three of this
document's §2.1 items now consume it identically. This is the specific item-2/definition-sentence
contradiction this pass closes, not a general "no new contradiction was found" restatement. No further
contradiction was found; no HALT condition applies.

**Re-run 2026-09-06, per Frank's spec-gate attempt-12 finding** — this document's §2.1 (items 1-3, the
"items 1 or 3" wording at the close of the widened-scope note, and the §14 human/controller-distinctness
row) was checked against post-`e6dfe73` documents 04 (§5.4/§8/§9), 06 (§16), 07 (§3.1/§12/§15), and 08 (§3).
The gap attempt-12 found: three sibling documents (06, 07, 08) still cross-referenced this document's rule
using pre-attempt-11 "items 1 and 3"/"items 1 or 3" wording (implying item 2 was excluded), and two live
occurrences (this document's own §14 row and document 08's roadmap text) still described the agent side of
the comparison as resolving to a session-initiating human's own `actorId` rather than that human's
`controllingPrincipalId`, contradicting document 04 §5.4's attempt-11 redefinition. This pass corrects this
document's own §2.1 close and §14 row to match document 04 §5.4's single-namespace, three-item scope, and
document 06 §16/§14, document 07 §3.1/§15/§12 are corrected in the same pass with matching wording (see those
documents' own re-run entries). Document 08 is `@planner`'s document and is reported separately, not edited
here. No new contradiction was found; no HALT condition applies.

**Re-run 2026-09-06, per Frank's spec-gate attempt-13 finding** — this document's §2.1 "Flagged open
question" paragraph (lines ~251-265) still described the interim distinct-identity default in terms of
`AuditActor.actorId` equality/inequality, when the actual rule (established across attempts 10-11 and
confirmed correct by attempt 12) resolves distinctness via `controllingPrincipalId` instead. That paragraph
predates the whole fix sequence (attempt-4/5-era text) and had never been touched by any of the three prior
keyword sweeps, each of which searched only for the exact stale phrase the prior verdict had named and so
never found older stale wording using a different, earlier vocabulary. Per Frank's explicit instruction, this
pass read §2.1 (lines ~196-320) and document 04 §5.4 (lines ~338-437) in full, end to end, rather than
grepping for named phrases, and corrected every live sentence in the "Flagged open question" paragraph that
named `actorId` as the thing being compared or the thing the open question depends on: the false-positive
example ("even though both sessions trace to the same `actorId`" → `controllingPrincipalId`), the "does not
pick between..." framing (both options reworded to name `controllingPrincipalId`, not `actorId`), the claim
that the dependency is "how `AuditActor.actorId` for humans is actually assigned" (corrected to "how
`controllingPrincipalId` for humans is actually resolved"), and the closing stricter-reading parenthetical
("same human `actorId` never satisfies distinctness" → "same human `controllingPrincipalId` never satisfies
distinctness"). No other live sentence naming `actorId` (or `orchestrationSessionId`) as the thing determining
distinctness was found on this full read of §2.1 or document 04 §5.4 — items 1-3, the §14 table row, and the
attempt-10/11/12 re-run narration already used `controllingPrincipalId` correctly. Re-checked against document
04 §5.4 (unchanged this pass), document 06 §16, and document 07 §11/§12 (both re-run below); document 08's
remaining occurrences are reported separately for `@planner` to read and fix in full, per the same
full-read instruction, rather than being patched here by line number. No HALT condition applies.

## 14. PROVISIONAL items and resolution paths

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| **U-04** — Minimum-sample-size numbers, and validation-default parameters (window sizing for walk-forward, purge-window/embargo-period sizing), per asset/timeframe/event-structure combination | PROVISIONAL — unvalidated | Danny | Dedicated research-design pass deriving minimum-sample requirements per the heterogeneous asset/timeframe/event-structure coverage the conformance program (Matrix row 57) will actually exercise |
| **U-05** — Which multiple-testing diagnostic(s) (DSR / White's Reality Check / bootstrap resampling / other) apply by default to which trial structures, and the numeric pass/fail cutoff for each | PROVISIONAL — unvalidated | Danny | Dedicated statistical-methods design pass evaluating DSR/reality-check/bootstrap-style methods against this program's actual campaign/trial structures, justified against a named statistical method per this repo's no-fabricated-constants discipline |
| **U-06** — CPCV applicability decision function (mapping `CPCVDecision.samplingStructure` to an applicable/not-applicable verdict) and CPCV fold count / purge-embargo sizing when applicable | PROVISIONAL — unvalidated | Danny | Dedicated research-design pass defining the applicability matrix, informed by whichever CPCV reference implementation (e.g., skfolio's `CombinatorialPurgedCV`, pending its own PA-10 acceptance gates) is used for comparison, per Matrix row 28's "applicability/default matrix" designation |
| Adverse-cost-stress magnitude/percentage (§8) | PROVISIONAL — unvalidated | Danny | Resolved in the same execution-semantics ADR Data Architecture §8 already names for U-02c's cost-model default values — not a separate, second-guessed number |
| Robustness-family (§9) applicability-by-candidate-type rules and pass/fail thresholds per family | PROVISIONAL — unvalidated | Danny | Resolved alongside U-04, since family applicability depends on the same asset/timeframe/event-structure context U-04's research design must characterize |
| **Independent-reproduction match tolerance** (§2.1) — whether `IndependentReproductionRecord.matched` requires exact event-ledger match or some numeric tolerance band on derived metrics, and if the latter, the tolerance value itself | PROVISIONAL — unvalidated | Danny | Resolved alongside U-01's schema design session (Roadmap §3 Phase R1), since the exact reproduction bar depends on the same content-addressing/hashing mechanism (Data Architecture §2.4/§5, U-01c) that determines what "same content-addressed inputs" precisely means |
| **Human/controller-distinctness judgment call** (§2.1 items 1-3, widened from item 2 alone per Sol's cold review, target SHA 99d3673) — whether the same human `AuditActor.actorId`, or the same natural person behind two different human `actorId`s, or the same natural person behind a human `actorId` and the `controllingPrincipalId` of the human who holds session-initiation authority for an agent's `orchestrationSessionId`, can ever count as a distinct identity (e.g., two individuals sharing one team service account), or never counts as distinct once `controllingPrincipalId` (document 04 §5.4) resolves to the same person | PROVISIONAL — unvalidated | Danny | Interim disposition already implemented in §2.1 items 1-3: the stricter reading (same `controllingPrincipalId` never satisfies distinctness, whether both sides are human, both are agents, or one is human and one is an agent). Resolved once document 06 §16's widened U-12 item's identity model (per-person vs. per-shared-account `actorId`/`controllingPrincipalId` assignment) is chosen — the same Phase R1 sub-resolution the match-tolerance item above depends on. |

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
