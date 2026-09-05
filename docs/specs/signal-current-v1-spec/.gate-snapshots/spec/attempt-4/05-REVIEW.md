# Spec Review: signal-current-v1-spec

**Status**: Complete — all 14 gaps re-verified CLOSED against live file content (see §9, re-review
2026-09-05 post-fix-loop). Four new MINOR findings (N1–N4) recorded; none blocking. Ready for
Frank's Step 8 binding gate.
**Author**: @spec-reviewer
**Date**: 2026-09-05 (original review); 2026-09-05 (re-review, §9)

**This is not a gate.** Frank's Step 8 verdict is the binding one. This document exists so an
editorial fix loop can run first, and Frank is not the first check to catch something an editorial
pass should have caught.

**Method note (for the record, not as a claim of sufficiency):** all thirteen artifacts named in the
review brief were read in full and directly, including `00-source-inventory-reconciliation.md` and
`PA-10-reuse-decision-register.md`. Clause citations were checked against the matrix's actual text,
not against each document's own claim about its citations. Numeric constants were found by grepping
the eight canonical files directly, not by trusting each document's "no numeric constant is
introduced" statement.

---

## 1. Gaps table

Severity: **BLOCKER** (implementation/freeze impossible) / **MAJOR** (a reader is actively
misdirected, or a stated obligation is unmet) / **MINOR** (accuracy/hygiene).

| # | Document | Gap | Severity |
|---|---|---|---|
| G1 | 05-validation | PROVISIONAL table is **§14**, but §1, §3, §4, §5, §6, §7, §8 and §9 all direct the reader to **"§9"** (~10 occurrences: "PROVISIONAL — §9", "§9 U-04/U-06", "PROVISIONAL alongside U-04/U-05 (§9)"). §9 is *Robustness / perturbation families*. §9 even points at itself. Every deferral pointer in the document lands on the wrong section. | MAJOR |
| G2 | 06-portfolio | Same defect class: PROVISIONAL table is **§16**, but §1, §4.1, §4.4, §7.2, §7.4, §8, §9.1 and §9.3 direct the reader to **"§10"** (~8 occurrences, incl. "§10, U-07", "§10, U-12"). §10 is *Live telemetry and retraining*. | MAJOR |
| G3 | sprint scaffolding | **`PROGRESS.md` does not exist.** The repo's `CLAUDE.md` Directory Structure lists it as a per-sprint artifact and Decision Discipline calls it "the ground truth for sprint state." Worse, `08-implementation-roadmap.md` §1 asserts as fact that "that sprint's own completion is tracked in `docs/specs/signal-current-v1-spec/PROGRESS.md`" — a citation to a file that isn't there. | MAJOR |
| G4 | 08-roadmap vs 07-agent | 08 §1 claims it "collects and sequences — without resolving — every PROVISIONAL item already tagged in documents 04, 05, 06, and 07." Two items from 07 §12 — *specific agent framework/SDK/orchestration product choice* and *specific tool-permission enforcement technology* — name their owner as "**Document 08** / implementation owner." Neither appears anywhere in document 08. Orphaned: assigned to a document that never picks them up. | MAJOR |
| G5 | 01-constitution | §9 item 2 cites *"(Reconciliation §6 item — SourceSnapshot requirement; Matrix row 5.)"*. Matrix §6 has thirteen numbered items and **none** concerns SourceSnapshot. This is an unfinished placeholder citation pointing at a nonexistent anchor — the precise defect class Sol's cold review blocked this document for. (The `Matrix row 5` half is correct and sufficient on its own.) | MAJOR |
| G6 | 01-constitution | §7 item 1 (deterministic replay) cites *"Reconciliation §6 item 6 cross-reference"*. Matrix §6 item 6 is temporal OOS / walk-forward, an unrelated rule. The correct anchor is Matrix **§7.1** ("deterministic replay where the mode is declared deterministic"), which the document does not cite here. | MAJOR |
| G7 | 07-agent | §7.2 item 3 defers to **"§7.4"**. §7 contains only §7.1–§7.3; there is no §7.4. Separately, §1 routes both the activation scorecard and the permission matrix to "PROVISIONAL, §5", but §5 is the concept section — the actual PROVISIONAL entries are in §7.3 and §12. | MAJOR |
| G8 | 02-architecture, 04-data | Both close with "**No numeric constant … is introduced anywhere in this document**". Literally false as written: 02 §7.2 and §10 contain `minutes_per_day=390` and "roughly 90 peer venue-adapter repositories"; 04 §4.2 contains both. The values are substantively fine — external, cited to candidate reports, explicitly *not* adopted — but an unqualified blanket claim contradicted by the document's own body text is the same failure mode as the Constitution's original "every clause traces to §6 or §7.1". Fix by scoping the claim (e.g. "no numeric constant is adopted as a Signal Current setting"), not by deleting the citations. | MINOR |
| G9 | INTAKE, 01-REQUIREMENTS | Both pin the Reconciliation Matrix as **"Working draft v0.2"**. That string does not appear in `00-source-inventory-reconciliation.md`, whose Status line reads "Specification-phase working baseline" (2026-09-04). 01-REQUIREMENTS' *Assumes* clause is version-pinned to a label that does not exist, so its "if it is revised mid-sprint, re-check" trigger cannot actually be evaluated. | MINOR |
| G10 | 03-research | US-10 names three required coverages: row 26 (Exploration/Validation/Sealed-Lockbox zones), row 19, row 9. Rows 19 and 9 are cited explicitly and repeatedly. **Row 26 is never cited** — §6 reaches the zones only indirectly via "Architecture §4". Content is present; the traceability anchor US-6/US-10 requires is not. | MINOR |
| G11 | 02-architecture, 06-portfolio | Stale wording. 02 §12 says any PARITY ORACLE label "found in PA-10 is corrected against Matrix rows 1/16" — present tense, implying the label survives; a repo-wide grep returns **zero** occurrences of "PARITY ORACLE", so the correction already landed and the sentence describes a state that no longer exists. 06 §14 refers to itself as "this **architecture** document" (copy-paste from 02 §13). | MINOR |
| G12 | sprint scaffolding | No sprint-level `02-ARCHITECTURE.md` or `04-ROADMAP.md`, both listed in the repo's mandated per-sprint Directory Structure. Canonical `02-system-architecture.md` and `08-implementation-roadmap.md` evidently serve those roles — a defensible substitution for a sprint whose deliverable *is* an architecture and a roadmap, but no artifact records that this substitution was made or approved. | MINOR |
| G13 | 06-portfolio | §3's `complexity` field carries "exact schema is a future amendment" — an open item with no PROVISIONAL tag, no owner, and no resolution condition. It appears neither in 06 §16 nor in 08 §3's sequencing. The smallest orphan in the set, but it is one. | MINOR |
| G14 | all eight canonical docs | North Star criterion 1 requires each document be "produced by its named subagent (never hand-authored by the orchestrator)". Documents 01–08 carry **no Author field** (only `03-UI-SPEC.md` names `@ui-spec-writer`). The criterion is therefore not checkable from the artifacts themselves — it can only be asserted. Given that this sprint exists *because* an authorship/self-certification failure occurred, the absence of an authorship record is a notable omission. | MINOR |

**No BLOCKER found.** No gap prevents implementation or makes the set internally incoherent.
G1–G7 are mechanical and fixable in a single editorial pass.

---

## 2. What was checked and found sound

Recorded explicitly, because a review that reports only defects hides what its own coverage was.

**Cross-document consistency (brief item 1) — no contradiction found.**

- **Human-authorization gate.** Constitution §6 is the source text. `06 §7.1` quotes it *verbatim*
  ("serve as, substitute for, or impersonate … no agent-defined or agent-only authorization path
  ever satisfies this gate"). `07 §2.3 item 5` restates it from the agent-permission side and adds
  the role-name-immunity clause ("no matter how the role is named") without weakening anything.
  `06 §11` and `07 §9` both re-reject the same TradingAgents pattern in the same terms. **No drift.**
- **P0 scope (brief item 5).** `02 §2` (domain table: Agent & Orchestration "Explicitly not P0"),
  `02 §6`, `02 §7.1` (P0 stops at `StrategyArtifact`), `07 §0`, `08 §2.3`, `08 §4`, and
  `03-UI-SPEC §1` all agree: spine to `StrategyArtifact`; Portfolio/Deployment/Monitoring and
  Agent & Orchestration out of P0; no P0 component depends on agents. **Consistent, no exceptions.**
- **skfolio status.** `02 §11`, `05 §4`, `05 §12`, `06 §4.2`, `08 §5` all state ADOPT CANDIDATE,
  never final ADOPT, all tied to PA-10 row PA-05-01 and the un-executed test suite. **Consistent.**
- **Section-number arbitration for G1/G2.** Document 08 §3 cites "Validation **§14**" and
  "Portfolio/Deployment **§16**" — correctly. This is what establishes that the defect is the
  *internal* pointers in 05/06, not their PROVISIONAL tables' numbering.

**No fabricated numbers (brief item 4) — verified independently, not trusted.**
Direct grep across all eight canonical files. Every numeric literal found is one of: an external
value quoted from a cited candidate report and explicitly rejected as a Signal Current setting
(`minutes_per_day=390`, "roughly 90 repositories", cuOpt's "100x/160x", TA-Lib/Zipline artifacts);
a section reference; or a structural count sourced to a matrix row (06 §8's three incubation stages
→ row 46's "paper → shadow → small-risk"). **No threshold, budget, tolerance, cutoff, fold count,
sample minimum or cost default is set anywhere in the eight documents.** Two instances of positive
discipline deserve note: `05 §8` cites Kelly & Xiu's qualitative principle while *explicitly
refusing* its unverified "~4x" figure, and `06 §14` cites cuOpt's formulation concept while
explicitly refusing its vendor-reported benchmark. That is the standard being applied correctly.
The only issue here is the over-broad wording of the blanket claims (G8), not the substance.

**PROVISIONAL sequencing (brief item 3) — near-complete.**
Every PROVISIONAL item in 02 §14, 04 §8, 05 §14, 06 §16 and 07 §12 traces to a Phase in 08 §3,
with matching owners (Danny throughout) and resolution conditions quoted from the originating
document rather than paraphrased. Reverse direction also clean: 08 §8's four items (U-08/U-09/
U-10/U-13) are the matrix's own. **The two exceptions are G4 and G13.**

**Reconciliation Matrix §8 coverage (brief item 6) — all eight answer their own primary question.**

| Doc | §8 primary question | Verdict |
|---|---|---|
| 01 | What can never be violated? | ✅ §§1–11 are prohibitions/invariants, not guidance |
| 02 | Bounded domains, components, interfaces, states, invariants? | ✅ §2 domains, §3 state machine, §5 components, §8 interfaces |
| 03 | How does an idea become a legitimate experiment? | ✅ §2 workflow, §10 explicit promotion checklist — answers directly, not adjacently |
| 04 | Canonical data/artifact/schema/lineage/IR contracts? | ✅ contract-level, with §1 stating plainly why field-level is deferred |
| 05 | What evidence before promotion; how is self-deception constrained? | ✅ `ValidationPlan` + §11 lockbox mechanism |
| 06 | Validated strategies → portfolios/builds/deployments/monitoring? | ✅ §§3–10 staged promotion |
| 07 | What may agents do, with which tools, under which permissions/gates? | ✅ §2 boundary, §5 scoping, §7 activation |
| 08 | In what sequence do we build without violating the spec? | ✅ §2 P0, §3 Phases R1–R4, §5 R-Prior-Art |

**PA-10 corrections (US-5) — landed.** Status vocabulary (TRIAGED/PARTIAL/RESEARCH COMPLETE/
DECIDED) added with per-row assignment; "No row is DECIDED yet" stated; the Rule explicitly
demotes ADOPT CANDIDATE to a proposal; **"PARITY ORACLE" returns zero hits repo-wide.**

---

## 3. North Star success criteria (brief item 7)

| # | Criterion | Verdict |
|---|---|---|
| 1 | Eight documents exist, each by its named subagent, per-clause traceable with correct anchors | ⚠️ Documents exist and per-clause traceability is genuinely present throughout — but two anchors are wrong (G5, G6), one required anchor is absent (G10), and authorship is unrecorded and therefore uncheckable (G14) |
| 2 | Constitution corrected, Sol's blocking/major findings resolved | ✅ Blanket claim replaced by inline per-clause citations; human-authorization gate restored (§6); determinism/nondeterminism split into three tiers (§7); the one unsourced clause (§10) now carries an explicit self-flagged synthesis note rather than a false citation. G5/G6 are *new* miscitations, not the original findings recurring |
| 3 | PA-10 corrected before @architect reads it | ✅ See §2 above |
| 4 | `docs/CADENCE.md` and `docs/INVARIANTS.md` exist | ✅ Both present, alongside `docs/NORTHSTAR.md` |
| 5 | Benchmark subagent runs before QC on every doc introducing a numeric constant | ⚠️ Vacuously satisfied — **no document introduces one** (independently grep-verified). But no run record exists, so "it ran" and "there was nothing to run it on" are indistinguishable from the artifacts. Recommend stating the latter explicitly somewhere durable |
| 6 | Frank's binding spec-gate reaches PASS on the full set | ⏳ Pending Step 8 — outside this review's scope |

*(Criterion 1's verdict is superseded by §9 below: G5, G6, G10 and G14 are now closed.)*

---

## 4. Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| G1/G2's broken pointers survive to freeze; a future implementer follows "§9"/"§10", finds unrelated content, and concludes a threshold was never deferred at all — then supplies one | M | H | Fix in the editorial loop. These are the highest-consequence findings despite being mechanical: the pointers guard exactly the numbers this program most needs kept unset |
| Every document's own "consistency check" section (03 §11, 04 §9, 05 §13, 06 §15, 07 §11, 08 §9) is **self-assessed by the document's own author**, and every one reports "No conflict was found" | H | M | This review is the first non-self check. Note it is still a *single* additional read of the same source — this review and the eight self-checks share the reconciliation matrix as one well. Frank's Step 8 gate is the independent axis; do not treat six self-checks plus this one as seven reviews |
| G14: authorship unrecorded in a sprint convened specifically because of an authorship failure | H | M | Add an Author line to each canonical document, or record the delegation trail in the sprint folder |
| PROVISIONAL is the correct state today but has no expiry; the set could freeze at v1.0 with ~25 items open and no forcing function beyond 08 §3's phase ordering | M | M | Real and accepted by design (Intake and 01-REQUIREMENTS both state PROVISIONAL-with-owner is this sprint's required output). Worth Danny's explicit acknowledgement at approval rather than silent inheritance |
| Doc 04 defers U-01 wholesale; every downstream schema in 05/06 (`ValidationPlan`, `PortfolioPlan`, `StrategyArtifactPassport`) is typed against contracts that do not yet exist | M | M | Already sequenced (08 Phase R1 first, with 04 §10 and 05 §15 committing to amendment-not-supersession). Structurally sound; flagged for visibility |

---

## 5. Assumptions made in this review

| Assumption | Impact if wrong |
|---|---|
| `00-source-inventory-reconciliation.md` as committed is the version every document was drafted against | G9's version-label mismatch would become substantive rather than cosmetic; every traceability check here would need redoing against the true source |
| Canonical `02-system-architecture.md` and `08-implementation-roadmap.md` legitimately stand in for the sprint's missing `02-ARCHITECTURE.md` / `04-ROADMAP.md` | G12 would escalate from MINOR to a missing-deliverable finding |
| The 29 underlying candidate reports are sound; I checked the eight documents' *citations against PA-10 and the matrix*, not the candidate reports against their own primary sources | An overstatement one layer below PA-10 would pass through this review untouched. 01-REQUIREMENTS' own edge-case table anticipates exactly this ("correcting only the register would leave a corrupted well one layer down") |
| Documents 01–08 were in fact produced by their named subagents | North Star criterion 1 fails; the sprint's founding defect would have recurred undetected (see G14) |

---

## 6. Open questions for Danny

| # | Question | Status |
|---|---|---|
| Q1 | Should `PROGRESS.md` be created retroactively for this sprint (G3), or is the sprint-state obligation waived for a spec-only sprint? Document 08 §1 currently cites it as existing either way | **Answered by the fix loop** — 08 §1 now states no `PROGRESS.md` exists for a spec-only sprint and that sprint-state tracking begins at `/forge-start`. Danny to confirm he accepts that disposition |
| Q2 | Is the 02/08-substitute-for-sprint-ARCHITECTURE/ROADMAP arrangement (G12) approved? If so it should be recorded in the sprint folder rather than inferred | **Recorded** in INTAKE.md as an explicit substitution note; still needs Danny's approval |
| Q3 | Who owns document 07's two implementation-technology items (G4) — document 08 as 07 §12 states, or a later build-phase decision outside this spec set? | **Answered by the fix loop** — 08 §4.1 picks both up and sequences them after Phase R4's activation gate. Danny to confirm |
| Q4 | Should each canonical document carry an Author/delegation record (G14)? | **Answered** — all eight now carry Provenance + Editorial-corrections lines |
| Q5 | Freezing v1.0 with ~25 PROVISIONAL items open is this sprint's stated intended output. Confirming that explicitly at approval, rather than leaving it implicit | Open |

---

## 7. Recommended fix loop before Step 8

Ordered by consequence. All are editorial; none requires re-drafting a document.
**All nine items below were executed and verified — see §9.**

1. **05-validation** — repoint all "§9" PROVISIONAL references to **§14** (G1).
2. **06-portfolio** — repoint all "§10" PROVISIONAL references to **§16** (G2).
3. **01-constitution** — repair §9 item 2's placeholder citation and §7 item 1's §6-item-6 miscitation (G5, G6).
4. **07-agent** — fix the "§7.4" dangling reference, §1's "§5" routing, and §5.1's list starting at "2." (G7).
5. **08-roadmap** — pick up or explicitly reassign 07 §12's two orphaned items (G4); resolve the `PROGRESS.md` reference (G3).
6. **02 / 04** — scope the "no numeric constant" blanket claims to what is actually true (G8).
7. **03-research** — add the explicit Matrix row 26 anchor (G10).
8. **INTAKE / 01-REQUIREMENTS** — correct the "Working draft v0.2" label (G9).
9. **02 §12 / 06 §14** — stale-wording cleanup (G11).

---

## 8. Approval checklist (for Danny, after the fix loop)

### Sprint scaffolding
- [ ] `01-REQUIREMENTS.md` reviewed — US-1..US-17 acceptance criteria are testable
- [ ] `03-UI-SPEC.md` reviewed — the no-UI-yet stub is the right call, and §3's pre-UI inspectability floor is accepted as a live P0 requirement
- [ ] Q1–Q5 (§6 above) answered

### Canonical set 01–08
- [ ] All fourteen gaps dispositioned (fixed, or accepted-with-reason)
- [ ] Constitution read in raw form — it is an identity-tier artifact; per repo discipline it is not self-approved by any agent
- [ ] The ~25 PROVISIONAL items, their owners (all Danny), and 08 §3's Phase R1–R4 sequencing accepted as the intended v1.0 resting state
- [ ] Authorship/delegation trail satisfactory (G14)
- [ ] N1–N4 (§9 below) dispositioned — N4 in particular, which is the same defect class as G8

### Overall
- [ ] Frank's Step 8 binding spec-gate returns PASS
- [ ] Independent review per Matrix §9's Freeze Rule complete
- [ ] v1.0 freeze authorized — no implementation begins before this box is ticked (08 §7)

---

**Reviewer's verdict:** the set is substantively strong and internally consistent on every axis the
brief named — the human-authorization gate holds verbatim across three documents, P0 scope agrees
across five, and not one fabricated number entered eight documents. The defects are concentrated in
mechanical cross-referencing, and G1/G2 are worth fixing carefully rather than quickly, because the
pointers that broke are precisely the ones guarding the unset numbers.

---

## 9. Re-review verification (2026-09-05, post-fix-loop)

**Method.** Every claim below was re-derived from current file content read directly this session.
No prior report — including §1 of this document — was treated as still accurate. Section numbers,
citation anchors and numeric literals were re-grepped rather than trusted. Where a fix asserted a
matrix anchor, the matrix's own text at that anchor was opened and read.

### 9.1 Gap-by-gap disposition

| # | Verdict | Evidence verified this session |
|---|---|---|
| G1 | **CLOSED** | All ten former "§9" deferral pointers in `05-validation-statistical-controls.md` now read "§14" (lines 36, 98, 108, 130, 165, 174, 198, 214, 243, 258, 276, 285, 323). §14 is confirmed to be *PROVISIONAL items and resolution paths* (line 474). **Not overcorrected:** the surviving "§9" strings are all legitimate — four cross-document references to *Research Methodology* §9 (lines 185, 397, 458, 469), the §14 table's own genuine self-reference to §9 *Robustness / perturbation families* (line 482), and Constitution §9 citations (lines 380, 387). |
| G2 | **CLOSED** | All eight former "§10" pointers in `06-portfolio-deployment-monitoring.md` now read "§16" (lines 37, 98, 172, 180, 260, 354, 375, 423, 437, 468, 480). §16 confirmed at line 643. **Not overcorrected:** surviving "§10" strings are all legitimate — Constitution §10 (lines 18, 587), Architecture §10 (lines 326, 628), and the anti-pattern table's genuine internal pointer to this document's own §10 *Live telemetry and retraining* (line 586, which is exactly the row about telemetry/retraining). |
| G3 | **CLOSED** | `08-implementation-roadmap.md` §1 (lines 70–78) no longer cites `PROGRESS.md` as existing. It now states plainly that this spec-only sprint "does not have its own `PROGRESS.md`" because it produces eight canonical documents rather than tracked slices, and that sprint-state tracking begins when `/forge-start` runs against the frozen set. Accurate as written — `PROGRESS.md` is confirmed absent from the sprint folder. |
| G4 | **CLOSED** | New `08 §4.1` ("Two implementation-technology items deferred from document 07 §12", lines 313–336) picks up both items explicitly, quoting 07 §12's own resolution conditions verbatim, and sequences both strictly after Phase R4's activation gate. Ownership is consistent in both directions: `07 §12` (lines 372–373) names "Document 08 / implementation owner" and tags them "Deferred, not PROVISIONAL"; `08 §1` (lines 47–60) and `§4.1` adopt exactly that framing rather than silently promoting them to PROVISIONAL. `08 §10` (line 449) also now names them as amendment triggers. |
| G5 | **CLOSED** | `01-constitution.md` §9 item 2 (line 77) now reads "*(Matrix row 5.)*" — the nonexistent "Reconciliation §6 item — SourceSnapshot requirement" placeholder is gone. Verified against the matrix's own text: row 5 is "Raw source preservation … Every acquired source must produce an immutable **SourceSnapshot** or equivalent evidence reference with capture metadata and hash where possible" (`00-source-inventory-reconciliation.md` line 71). Citation is now real and correct. |
| G6 | **CLOSED** | `01-constitution.md` §7 item 1 (line 64) now reads "*(Reconciliation §7.1; Matrix row 22.)*". Both anchors verified against the matrix directly: §7.1's "Scientific invariants are not menu choices" list contains "deterministic replay where the mode is declared deterministic" (line 200); row 22 is "Deterministic replay … Deterministic modes must reproduce the same event ledger and metrics for an identical versioned input tuple" (line 88). The false §6-item-6 anchor is gone; §6 item 6 (temporal OOS) remains correctly cited at §5 item 3 (line 39) where it belongs. |
| G7 | **CLOSED** | Three separate fixes verified in `07-agent-orchestration-layer.md`: (a) the dangling "§7.4" in §7.2 item 3 now reads "(§7.3)" (line 290), and §7 correctly contains only §7.1–§7.3; (b) §1's routing now reads "PROVISIONAL, §7.3/§12" for both the activation scorecard and the permission matrix (lines 41–42), matching where the entries actually live; (c) §5.1's list now runs 1., 2. (lines 237, 244). Internally consistent. |
| G8 | **CLOSED** | Both blanket claims are now scoped and, re-grepped, true as written. `02` line 409: "No numeric constant is **adopted as a Signal Current setting** anywhere in this document," followed by an explicit naming of the two literals that *are* present (§7.2's `minutes_per_day=390`, §10's "roughly 90 peer venue-adapter repositories") as external quoted-and-rejected values. `04` line 329 uses the same scoped construction and explicitly points at §4.2. Independent re-grep of both files found no numeric literal outside those already named. See **N3** for a wording infelicity in the replacement text. |
| G9 | **CLOSED** | `INTAKE.md` line 21 and `01-REQUIREMENTS.md` lines 237–238 both now read "Specification-phase working baseline", dated 2026-09-04. Verified against `00-source-inventory-reconciliation.md` line 3, whose Status line is exactly that string. The string "Working draft v0.2" now returns zero hits across `docs/specs/` outside this review document's own historical G9 entry. |
| G10 | **CLOSED** | `03-research-methodology.md` §6 line 171 now cites "(Matrix row 26; Architecture §4)" directly on the zone-leakage clause, rather than reaching the zones only indirectly via Architecture §4. The US-6/US-10 traceability anchor is now present in the document itself. |
| G11 | **CLOSED** | Both halves fixed. `02` §12 (lines 373–376) now reads in the past tense — "any such label **previously** found in PA-10 **was already corrected** against Matrix rows 1/16 (repo-wide grep confirms zero remaining occurrences) and was not carried into this architecture" — which matches the actual repo state. `06` §14 (lines 608–613) no longer calls itself "this architecture document"; it now says "this document" and "this portfolio/deployment document". |
| G12 | **CLOSED** | `INTAKE.md` lines 42–52 now record the substitution explicitly: that this sprint has no `02-ARCHITECTURE.md` or `04-ROADMAP.md`, that the canonical `02-system-architecture.md` and `08-implementation-roadmap.md` serve those roles, and the stated rationale (a separate planning document about a plan already held in the Reconciliation Matrix would be redundant). The decision is now an artifact rather than an inference. Danny's approval is still outstanding (Q2). |
| G13 | **CLOSED, and consistent across both documents.** | `06 §3` (line 98) now tags the field "**PROVISIONAL — unvalidated, owner Danny** (§16)". `06 §16` (line 653) carries a full row: owner **Danny**, resolution condition "Resolved in whichever schema-design session addresses `StrategyArtifactPassport` — most naturally the same U-01a `StrategyIR` schema-design session document 08 §3 Phase R1 sequences." `06 §17` (line 667) names it as an amendment trigger. `08 §1` (line 42) lists it among the collected items; `08 §3` Phase R1 item 6 (lines 182–186) sequences it with the *same* rationale (read off the same IR shape U-01a's golden examples fix); the Phase R1–R4 summary table (line 277) lists it under R1 with source "Portfolio/Deployment §16". Same owner, same resolution condition, present in both places, no drift. |
| G14 | **CLOSED** (with one attribution defect — see **N2**) | All eight canonical documents now carry a **Provenance** line: `01` (line 4, and notably honest — "Initial draft: orchestrator (pre-dating this sprint's redispatch discipline); corrected: @architect"), `02` (7), `03` (7), `04` (8), `05` (8), `06` (9), `07` (8), `08` (11). All eight also carry a dated **Editorial corrections** line naming the agent and the gap IDs. Cross-checked against what actually changed in each file: 01→G5/G6 ✅, 02→G8/G11 ✅, 03→G10 ✅, 04→G8 ✅, 05→G1 ✅, 06→G2/G11 ⚠️ (see N2), 07→G7 ✅, 08→G3/G4 ⚠️ (see N2). |

**Result: 14 of 14 gaps CLOSED. Zero STILL OPEN.**

### 9.2 Parallel-dispatch integrity check

`06-portfolio-deployment-monitoring.md` (touched by three dispatches) and
`08-implementation-roadmap.md` (touched by two) were each re-read **in full**, not sampled, on the
explicit hypothesis that one dispatch clobbered or stale-read another.

- **No clobbering found.** In `06`, all three dispatches' edits coexist intact: the G2 repointing
  (§16 throughout), the G11 self-description fix (§14), the G13 `complexity` PROVISIONAL tagging
  (§3/§16/§17), and the G14 header lines. In `08`, the G3 `PROGRESS.md` rewrite (§1), the G4 §4.1
  addition plus its §1 lead-in and §10 amendment-trigger update, and the G13 Phase R1 sequencing
  (§3 item 6 and the summary table) all coexist.
- **No duplicate content.** No section, table row, or paragraph appears twice in either file. `06`
  §16 has seven distinct rows with no repetition; `08` §3's Phase R1 list runs 1–6 with no repeats.
- **No partially-applied edit.** Every cross-reference introduced by one dispatch resolves against
  content another dispatch owns: `08 §3` item 6's reference to "document 06 §3, §16" resolves (both
  exist and both carry the item); `06 §16`'s reference to "document 08 §3 Phase R1" resolves; `08
  §4.1`'s quotations of 07 §12 match 07 §12's live text verbatim.
- **No new numeric constant.** Re-grepped all eight files. The literal inventory is unchanged from
  the original review: `minutes_per_day=390`, "roughly 90 peer venue-adapter repositories", cuOpt's
  explicitly-refused 100x/160x, Kelly & Xiu's explicitly-refused ~4x. The fix loop introduced none.
- **No fabricated citation.** Every citation added by the fix loop was opened at its target:
  Matrix row 5, Matrix §7.1, Matrix row 22, Matrix row 26, 07 §12's two rows, 06 §16's complexity
  row. All resolve to real text saying what the citing document claims.
- **No PROVISIONAL-item inconsistency.** Owner is Danny on every PROVISIONAL item across 02 §14,
  04 §8, 05 §14, 06 §16, 07 §12 and 08 §8. The two 07 §12 items remain "Deferred, not PROVISIONAL"
  in both 07 and 08 — the fix loop resisted the tempting error of promoting them to PROVISIONAL to
  make the collection tidier.

### 9.3 New findings introduced or left by the fix loop

| # | Document | Finding | Severity |
|---|---|---|---|
| N1 | 08-roadmap | §1 line 57 refers to "**§3's** 'collects and sequences… documents 04, 05, 06, and 07' claim" — but that claim is made in **§1 itself** (lines 39–40), not in §3. A self-reference introduced by the G4 fix that points at the wrong section. Same defect class as G1/G2, one occurrence, low consequence (the reader is one paragraph away from the real text). | MINOR |
| N2 | 06-portfolio, 08-roadmap | Both files' "Editorial corrections" lines are **incomplete**: `06` claims "gaps G2/G11" but also received the G13 `complexity` PROVISIONAL fix (§3, §16, §17); `08` claims "gaps G3/G4" but also received the G13 Phase R1 sequencing (§3 item 6, summary table). The G13 work is present and correct in both — only the attribution line omits it. This matters slightly more than usual because G14's entire purpose was to make the change trail checkable from the artifact; an attribution line that under-reports its own file's changes partially defeats that. | MINOR |
| N3 | 02-architecture, 04-data | The G8 replacement wording is accurate in substance but confusing in its parenthetical. `02` line 410 / `04` line 330 read "The only numeric literals present (threshold, budget, tolerance, sample-size minimum…)" — the parenthetical enumerates *categories none of the actual literals belong to*: `390` is a session-length hardcoding and `90` is a repository count, neither a threshold, budget, tolerance nor sample minimum. The claim being made is correct; the gloss describing it is not. Suggest replacing the parenthetical with the literals themselves, which the very next clause already names. | MINOR |
| N4 | 08-roadmap | **Same defect class as G8, in a file G8 did not name.** `08 §8` line 415 closes with an unqualified "No numeric constant … is introduced anywhere in this document" — while `08 §2.1` item 3 (lines 113–116) contains "roughly 90 peer venue-adapter repositories" and "not all 90 at once". The value is fine (external, cited to `PA-01-vnpy.md`, used as sequencing evidence, not adopted). But per this repo's own discipline — *"one unsourced number found → sweep the whole doc set, don't fix it in isolation"* — the sweep should have carried G8's scoping fix into 08 as well. `03 §333`, `05 §484`, `06 §655` and `07 §375` were each re-checked and are fine: their claims are parenthetically enumerated and no contradicting literal exists in those files. **08 is the only remaining instance.** | MINOR |

None of N1–N4 is a blocker, and none affects the substance of any spec decision. N4 is the one
worth fixing before freeze rather than after, because it is precisely the failure mode this sprint
was convened to eliminate — a blanket claim contradicted by the document's own body text — and
leaving one instance standing after explicitly correcting two others is worse than never having
looked, since it implies the sweep was complete when it was not.

### 9.4 Re-review verdict

All fourteen gaps are closed and independently verified against live file content. The four
parallel dispatches did not clobber one another; every edit from every dispatch is present and
mutually consistent. No new numeric constant, fabricated citation, or PROVISIONAL inconsistency
entered the set during the fix loop.

**Recommendation:** proceed to Frank's Step 8 binding gate. N1–N4 may be dispositioned in the same
pass or carried to Danny as accepted-with-reason; only N4 warrants a fix before freeze.

**A standing caution on this verification, stated because the repo's own discipline requires it:**
this re-review and the original review share one reader and one set of source documents. Its
agreement with itself is not independent confirmation. Frank's Step 8 gate remains the independent
axis, and nothing in §9 should be read as reducing what that gate needs to examine.
