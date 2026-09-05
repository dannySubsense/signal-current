# Requirements: Signal Current v1.0 Canonical Specification Set

## Summary

This sprint produces eight canonical specification documents (Constitution, System Architecture,
Research Methodology, Data Architecture & Strategy IR, Validation & Statistical Controls,
Portfolio/Deployment & Monitoring, Agent & Orchestration Layer, Implementation Roadmap) through the
proper spec-agent pipeline, each traceable to `docs/specs/00-source-inventory-reconciliation.md`
(the 58-row Reconciliation Matrix), correcting the defects Sol's cold review found in the
hand-authored Constitution draft and the PA-10 prior-art register, so no implementation begins on
an unreviewed, self-certified foundation.

## User Stories

**US-1** As the Composer (Danny), I want a Constitution document whose every clause cites a real
Reconciliation Matrix anchor (a section number or row ID), so that I can trust no invariant was
invented by an agent claiming otherwise.

**US-2** As a future implementer, I want the Constitution to state explicitly that material-risk
promotion requires human authorization and that no agent may serve as or impersonate that
authority, so that autonomous risk-bearing deployment cannot creep in through later specs.

**US-3** As a future implementer, I want the Constitution to distinguish deterministic replay,
seedable stochastic computation, and inherently non-replayable computation (LLM/live-feed output),
with different evidentiary treatment for each, so that non-deterministic methods cannot quietly
acquire the evidentiary weight of the deterministic engine.

**US-4** As a future implementer, I want the Constitution to state explicitly that external
engines/libraries (including skfolio and any adopted library) are conformance systems and never
alternate truth stores, so that no future document can silently promote a library's internals into
Signal Current's domain model.

**US-5** As the orchestrator (Vane), I want the PA-10 prior-art register corrected before
`@architect` reads it in Step 4, so Architecture does not inherit overstated claims, mislabeled
"PARITY ORACLE" evidence tiers, or a premature skfolio ADOPT decision.

**US-6** As Frank (the binding gate), I want each of the eight documents to carry per-clause
traceability to the Reconciliation Matrix (not a single blanket claim covering the whole document),
so I can independently verify sourcing rather than trust an assertion.

**US-7** As the Composer, I want every numeric constant/threshold introduced by any of the eight
documents to have gone through the `benchmark` subagent before that document's QC step, so no
document ships an unsourced number.

**US-8** As the Composer, I want any constant that cannot yet be benchmarked to carry a
`PROVISIONAL — unvalidated` tag with a named human owner and a concrete path to resolution (what
they are waiting on), not a bare tag with no resolution path, so PROVISIONAL cannot become a
permanent resting state.

**US-9** As a future implementer, I want the System Architecture document to define Signal
Current's bounded domains (Research Intelligence, Quant Laboratory), the canonical artifact spine
(SourceSnapshot → ... → DecisionRecord/ResearchTrigger per Reconciliation Matrix §5), and the
components/interfaces/states/invariants for each, so implementation has an unambiguous structural
map.

**US-10** As a future implementer, I want the Research Methodology document to define how an idea
becomes a legitimate experiment — including the Exploration/Validation/Sealed-Lockbox evidence
zones (row 26), search-budget/hypothesis-lineage recording (row 19), and the ResearchRecord vs
StrategyIR distinction (row 9) — so that no research idea skips the promotion path.

**US-11** As a future implementer, I want the Data Architecture & Strategy IR document to resolve
or explicitly scope U-01 (StrategyIR schema/serialization/hashing), U-02 (fill/execution
semantics), and U-03 (broker/venue normalization), so the canonical data contracts are unambiguous
before Architecture builds on them.

**US-12** As a future implementer, I want the Validation & Statistical Controls document to resolve
or explicitly scope U-04 (validation defaults by asset/timeframe), U-05 (multiple-testing
diagnostic defaults), and U-06 (CPCV applicability matrix), each number benchmarked or PROVISIONAL
with a named owner, so validation gates are not guessed.

**US-13** As a future implementer, I want the Portfolio, Deployment & Monitoring document to
resolve or explicitly scope U-07 (target-platform parity tolerance) and U-12 (authentication/
authorization model before live-risk operation), and to encode the mandatory incubation/graduation
lifecycle (row 46) and the human-authorization risk gate (row 49), so deployment cannot bypass
required gates.

**US-14** As a future implementer, I want the Agent & Orchestration Layer document to resolve or
explicitly scope U-11 (agent harness activation gate and permission matrix), encoding the
constitutional prohibition on agents manufacturing numerical evidence or bypassing gates
(Reconciliation Matrix §6 item 13, §7.1), so the agent layer cannot be built ahead of stable typed
tools.

**US-15** As a future implementer, I want the Implementation Roadmap document to resolve or
explicitly scope U-08 (numerical core language), U-09 (worker queue/job granularity), U-10
(artifact-store product), and U-13 (P0/P1 acceptance thresholds), each flagged for benchmark before
being fixed, so the roadmap does not silently promote an engineering convenience into an
architectural decision.

**US-16** As the Composer, I want `@ui-spec-writer`'s output for this sprint to be an explicit
stub stating "no UI needed yet" with the Reconciliation Matrix row 51 rationale, rather than
fabricated screens, so the artifact set stays complete without inventing UI that P0 does not need.

**US-17** As Frank, I want a single binding spec-gate verdict (PASS/FAIL/HALT) over the full
eight-document set plus the corrected PA-10 register, with the gate loop continuing past a nominal
attempt-3 ceiling rather than auto-halting, so the freeze decision is not made on an unreviewed or
prematurely abandoned set.

## Acceptance Criteria

**US-1**
- [ ] Given the redrafted Constitution, when any clause is checked against
  `docs/specs/00-source-inventory-reconciliation.md`, then every clause cites a specific section
  number or row ID (not a blanket "traces to §6 or §7.1" statement).
- [ ] Given a clause with no traceable source, when the redraft is reviewed, then that clause is
  either removed or the underlying invariant is first added to the Reconciliation Matrix and then
  imported with a citation.

**US-2**
- [ ] Given the redrafted Constitution, when the risk-authorization clause is read, then it states
  that material-risk promotion requires explicit human authorization (Reconciliation Matrix row 49)
  and that an agent may neither serve as nor impersonate that authority.

**US-3**
- [ ] Given the redrafted Constitution, when the determinism section is read, then it names three
  distinct categories (deterministic replay, seedable stochastic computation, inherently
  non-replayable computation) with different evidentiary treatment for each.
- [ ] Given an approved stochastic method, when its output is used as evidence, then the
  Constitution requires an immutable input snapshot plus replicated/statistical evidence.
- [ ] Given LLM or live-feed output, when it is classified, then the Constitution states it is
  proposal material only, never numerical evidence.

**US-4**
- [ ] Given the redrafted Constitution, when the external-library clause is read, then it states
  explicitly that external engines/libraries are conformance systems, never alternate truth stores,
  independent of and not implicit to the Reconciliation Matrix.

**US-5**
- [ ] Given PA-10 after correction, when any row is checked against its underlying candidate
  report, then no row states a claim stronger than that report.
- [ ] Given PA-10 after correction, when evidence-level fields are checked, then every row carries
  a status ∈ {TRIAGED, PARTIAL, RESEARCH COMPLETE, DECIDED}.
- [ ] Given PA-10 after correction, when any "PARITY ORACLE" label is checked, then it is resolved
  against Reconciliation Matrix row 1/16 (external engines are never alternate truth stores) —
  either relabeled or removed.
- [ ] Given the skfolio decision, when PA-10 is checked, then it is stated as ADOPT CANDIDATE, not
  ADOPT, pending its own register acceptance gates.

**US-6**
- [ ] Given each of the eight documents, when Frank's gate reviews it, then every constitutional or
  architectural clause carries a specific Reconciliation Matrix anchor, checkable independently.

**US-7**
- [ ] Given any document introducing a numeric constant/threshold, when that document reaches QC,
  then a benchmark-agent run for that constant precedes the QC step, with no exception taken
  without explicit written justification in the document.

**US-8**
- [ ] Given a constant tagged PROVISIONAL, when the tag is reviewed, then it includes a named human
  owner and a stated concrete condition/input the owner is waiting on to resolve it.
- [ ] Given a locked document, when it is checked for PROVISIONAL tags, then none exist without
  both an owner and a resolution path.

**US-9 through US-15** (per-document U-* resolution — pattern applies to each)
- [ ] Given the named document, when its corresponding U-* item(s) are checked, then each is either
  resolved with a cited value/method, or explicitly scoped as an open ADR with owner and trigger
  condition — never silently guessed.
- [ ] Given the named document, when checked against its Reconciliation Matrix source rows (as
  listed in each user story above), then all listed rows are represented.

**US-16**
- [ ] Given `03-UI-SPEC.md` (or equivalent stub) for this sprint, when read, then it states
  explicitly "no UI needed for P0" and cites Reconciliation Matrix row 51, without fabricated
  screens or components.

**US-17**
- [ ] Given the full eight-document set plus corrected PA-10, when Frank's spec-gate runs, then it
  returns one binding verdict (PASS/FAIL/HALT) with no conditional pass.
- [ ] Given a FAIL/HALT at attempt 3, when the orchestrator evaluates convergence, then the gate
  loop continues rather than auto-halting, with escalation to Danny occurring only on an
  independent conclusion that the loop is not converging.

## Edge Cases

| Case | Expected Behavior |
|------|-------------------|
| A Constitution clause partially matches a Reconciliation Matrix row but generalizes beyond it | Cite the row and flag the generalization explicitly as new; add it to the Reconciliation Matrix before import, or narrow the clause to match the row. |
| A numeric constant needed by a document has no feasible benchmark within this sprint's timeframe | Tag PROVISIONAL with a named owner and the specific blocking condition (e.g., "needs live tick data not yet acquired") — never silently invented, never left ownerless. |
| PA-10 correction reveals a candidate report itself is unreliable (not just overstated in the register) | Escalate — do not silently downgrade the register row; flag the underlying report for separate review, since correcting only the register would leave a corrupted well one layer down. |
| Two Reconciliation Matrix rows appear to conflict when cited by the same clause | HALT and document the conflict for Danny; do not pick one silently. |
| `@ui-spec-writer` finds a genuine UI need this sprint despite row 51 | Do not fabricate around the North-Star constraint — report the finding and let Danny decide whether row 51's "UI-last" framing needs revisiting; do not silently produce full screens. |
| Frank's gate reaches attempt 3 and beyond with STATIC/THRASHING pattern | Continue looping the gate-and-fix mechanism (per Interview Q6); do not isolate-and-skip individual documents and do not hard-halt on the count alone. |
| A U-* item's resolution in one document conflicts with how another document (drafted later) needs to reference it | The later document must cite the earlier resolution, not silently re-decide it; a genuine conflict is a HALT for Danny, not a silent overwrite. |
| A benchmark run for a numeric constant produces a value contradicting an existing hand-authored draft's number | The benchmarked value wins; the draft's number is corrected, not preserved for continuity. |

## Out of Scope

- NOT: Re-running or redoing the prior-art research itself (PA-01–PA-08, 29 candidate reports) —
  those are a completed, committed input; only PA-10 (the register synthesizing them) is corrected
  this sprint.
- NOT: Choosing a final ADOPT decision for skfolio or any other library — that requires the
  register's own stated acceptance gates to run, which is out of scope for this sprint.
- NOT: Resolving U-01 through U-13 with final locked numeric values where no benchmark exists yet —
  this sprint requires each be resolved-with-citation or explicitly scoped as PROVISIONAL-with-owner
  or a named future ADR; it does not require inventing values to force premature closure.
- NOT: Building `docs/CADENCE.md` or `docs/INVARIANTS.md` content as part of this sprint's Frank
  gate — those already exist (added mid-session, project-level governance docs, not sprint
  artifacts) and are explicitly outside this sprint's binding gate.
- NOT: Any product implementation, prototyping, or code — this sprint is specification-only, per
  the reconciliation matrix's Freeze Rule (§9).
- NOT: A full UI design for `03-UI-SPEC.md` — only an explicit stub is required (see US-16).
- Deferred: Independent human review and v1.0 freeze itself — this sprint produces the reviewed,
  gated document set; the freeze decision is a separate downstream step per §9's stated sequence.
- Deferred: Resolving which specific external technology (queue product, artifact store, numerical
  core language) is chosen where the Reconciliation Matrix marks the choice as deferred to
  benchmark/ADR (U-08, U-09, U-10) — the Roadmap document scopes these as open ADRs, it does not
  close them without a benchmark.

## Constraints

- Must: Every one of the eight documents is delegated to its named subagent per this repo's
  "Always Redispatch" rule — the orchestrator (Vane) does not hand-author 01–08 directly.
- Must: Every clause in every document cites a specific Reconciliation Matrix section/row anchor,
  not a document-level blanket traceability claim.
- Must: The benchmark subagent runs before QC on every document introducing a numeric
  constant/threshold, no exception without explicit written justification.
- Must: Any PROVISIONAL tag on a locked document's constant carries a named human owner and a
  concrete resolution path — a bare PROVISIONAL tag with no owner/path is not acceptable in a
  locked document.
- Must: The existing `01-constitution.md` draft is corrected in place (not discarded, not
  rewritten from scratch) — Sol's findings are the fix list, handed to `@architect`.
- Must: PA-10 is corrected before `@architect` reads it in Step 4 (System Architecture).
- Must: `@ui-spec-writer` produces an explicit "not needed for P0" stub rather than skip the step
  or fabricate screens.
- Must: Frank's Step 8 gate for this sprint uses the standard briefed contract (map, not route) —
  not the unbriefed Cold Frank protocol, which is reserved for decision-matrix escalations.
- Must: Frank's verdict is binding PASS/FAIL/HALT with no manual override; the gate loop continues
  past a nominal attempt-3 ceiling rather than auto-halting per Danny's explicit direction this
  sprint.
- Must not: Any document assert a claim of traceability broader than what is individually cited
  per clause.
- Must not: Any agent role (including this requirements-analyst) make scope decisions on the
  13 unresolved decisions (U-01..U-13) — each downstream document must resolve-with-citation or
  explicitly scope as open, never silently guess a number to force closure.
- Must not: Treat a PARITY ORACLE-style label, or any external library's internal ontology, as
  Signal Current's domain model or ground truth, anywhere in the eight documents or the corrected
  PA-10 register.
- Assumes: The Reconciliation Matrix (`00-source-inventory-reconciliation.md`, "Working draft
  v0.2") remains the authoritative synthesis input for this sprint; if it is revised mid-sprint,
  affected documents must be re-checked against the new version before Frank's gate.
- Assumes: `docs/NORTHSTAR.md` remains `Status: ACTIVE` for the duration of this sprint, so Frank's
  Layer 2 check needs no PROVISIONAL stamp; if that status changes, Frank's gate treatment changes
  accordingly and must be re-verified, not assumed.
- Assumes: skfolio remains an ADOPT CANDIDATE (not final ADOPT) throughout this sprint's
  Architecture document — if the register's acceptance gates run and change this status mid-sprint,
  downstream documents referencing skfolio must be re-checked.
