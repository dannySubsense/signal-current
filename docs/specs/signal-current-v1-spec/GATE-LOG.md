# Gate Log: signal-current-v1-spec

Records every Frank binding-gate verdict for this sprint, by lane. Each attempt's verdict is appended verbatim, plus the orchestrator's own independent review on PASS (per the spec-start skill's requirement that a PASS is followed by genuine independent scrutiny, not a rubber-stamp).

---

## Spec Gate

### Attempt 1 — 2026-09-05 — FAIL

**Layer 1 (sprint North Star fidelity): PASS** (criteria 1-5 met; criterion 5 vacuously — no
numeric constant introduced anywhere in the 8 documents, so no benchmark was applicable; this
disposition previously lived only in 05-REVIEW.md §3, now recorded durably here per Frank's own
finding — see fix item 7 below).

**Layer 2 (project North Star relevance): FAIL.** docs/NORTHSTAR.md Status: ACTIVE — binding,
no PROVISIONAL stamp.

**Blocking finding (F1):** The Project North Star's Thesis — "A strategy only counts as validated
if someone other than its author can rerun the same test and get the same result" — is not encoded
anywhere in docs/specs/00-source-inventory-reconciliation.md (the Reconciliation Matrix, dated
2026-09-04) or in any of the 8 canonical documents (all of which trace faithfully to that Matrix).
The Matrix predates NORTHSTAR.md (2026-09-05) and was never back-filled. Every document's fidelity
to the Matrix is real and independently verified — the well itself is simply missing the project's
one falsifiable claim. Document 05 (Validation & Statistical Controls) answers "how is
self-deception constrained?" with deterministic-replay-plus-diagnostics run by whoever ran the
search — reproducible-in-principle, not the Thesis's actual bar of reproduced-by-someone-else.

**Minor findings (non-blocking but tracked):**
- F2: Constitution §7.2/§7.3/§9.3/§11 state requirements beyond what their cited Matrix rows
  actually contain, without the explicit synthesis-flag §10 already uses as precedent for this
  exact situation.
- F3: 02-system-architecture.md §12 claims "repo-wide grep confirms zero remaining occurrences"
  of PARITY ORACLE — actually 7 remain, all historical/meta references in sprint scaffolding
  (NORTH-STAR, INTAKE, 01-REQUIREMENTS), none a live label. Intent true, literal claim false.
- F4: 01-constitution.md's Status line still reads "not yet re-reviewed" after two @spec-reviewer
  passes and this gate — stale.

**What was checked and confirmed sound:** human-authorization gate verbatim-consistent across
Constitution §6, doc 06 §7.1-7.3/§8/§11, doc 07 §2.3/§5.1; Agent & Orchestration excluded from P0
everywhere referenced, no exceptions found; 28 PROVISIONAL items (not ~25), all owner Danny, all
sequenced in doc 08 §3/§4.1/§8, no third orphan found beyond the two the review process already
caught; PA-10 register status vocabulary correct, skfolio remains ADOPT CANDIDATE with its six
gates faithfully reproduced in doc 08 §5; 05-REVIEW's 14 CLOSED + 4 N-findings independently
re-verified against live files, commit c690132 confirmed to close N1/N2/N4.

**Fix routing (per Frank's Fix/Next-step list):**
1. Matrix amendment (independent-rerun requirement) — **route to Danny**, human decision, Matrix
   is source of truth.
2. Constitution clause citing the new Matrix anchor + NORTHSTAR Thesis — route to @architect,
   blocked on fix 1.
3. ValidationPlan independent-reproduction record (05 §2/§14) + Roadmap sequencing (08 §1/§3) —
   route to @architect (05), @planner (08), blocked on fix 1.
4. Constitution §7.2/§7.3/§9.3/§11 synthesis-flagging or new anchors — route to @architect.
5. 02 §12 "zero remaining occurrences" rescoping — route to @architect.
6. 01-constitution Status line staleness — route to @architect.
7. Record vacuous-benchmark disposition durably in this log — done, above.
8. Snapshot before re-delegation — done, `.gate-snapshots/spec/attempt-1/`.

Attempt counter: 1 of 3 (uncapped per Danny's "keep going" ruling on this sprint — the loop
continues past 3 rather than auto-halting; see docs/specs/signal-current-v1-spec/INTERVIEW.md).

### Attempt 2 — 2026-09-05 — FAIL (converging)

**Layer 1: PASS** (unchanged from attempt 1, re-verified). **Layer 2: FAIL.**

Attempt 1's blocking finding (the well omits NORTHSTAR's Thesis) is **closed** — Matrix row 59 /
§6 item 14 exist, Constitution §3 item 7 cites them and quotes the Thesis verbatim, Validation
§2/§2.1 add a structural gate, and that gate is genuinely load-bearing downstream (doc 06 §2
refuses promotion without it, doc 07 §2.3 item 3 makes a promotion request without it a rejected
malformed tool call). Frank confirmed this chain is real, not cosmetic.

**New blocking finding (F1):** `IndependentReproductionRecord` is assertion-shaped. `matched:
boolean` and free-text `comparisonMethod`, with no reference to the reproduction's own
content-addressed `SimulationRun`/`ValidationArtifact`, and `originalAuthor`/`reproducedBy` as
unbound free strings. Doc 04 (which doc 02 §4.2 names as owner of the audit-log schema) has no
actor/identity field at all. Net effect: the gate can be satisfied by the same author writing
`matched: true` and any different string into `reproducedBy` — exactly the self-certification
failure this whole program exists to prevent, one layer deeper than attempt 1 caught it.

**New blocking finding (F2):** doc 02 (System Architecture — the document that names the enforcing
component) was never amended. §3.1/§3.2/§5 still enumerate five evidence checks, not six; no
mention of independent reproduction anywhere in the document that defines what the Promotion/Gate
Service enforces. Doc 05 §13's "checked against 02, no conflict" is now stale — it predates §2.1.

**New required findings (non-blocking but must close before freeze):**
- F3: whether the independent rerun includes Sealed Lockbox confirmation is unspecified (05 §2.1
  vs §11) — if not, the most consequential evidence remains author-only; if so, §11.3's
  contamination-voiding logic needs a sentence distinguishing legitimate rerun from "peek and
  re-tune."
- F4: "distinct identity" (row 59's "author or authoring agent") is undefined — two agent roles
  driven by one orchestrator in one session are one well; string inequality doesn't capture that.

**Attempt-1 minor findings, re-verified:** F2 (synthesis flags) CLOSED — specific, content-bearing
citations at all four locations, not generic disclaimers. F4 (stale status line) CLOSED. F3 (02 §12
grep claim) narrowed but overshot again — the PA-10/candidate-report claim is now true, but a new
added sentence ("fully retired from research-program vocabulary") is contradicted by
`Signal_Current_Specification_Set/CLAUDE_CODE_GREENFIELD_KICKOFF.md:183`, which still lists PARITY
ORACLE as an allowed disposition (a research input, not authority, but still a literally-false
sentence in the same paragraph).

**Process finding:** `05-REVIEW.md` was discovered untracked (never committed) — fixed separately,
commit 9890e58.

**Fix routing (all to @architect, single focused pass on docs 02/04/05):**
1. 05 §2: redefine `IndependentReproductionRecord` with content-addressed refs
   (`reproductionRunRefs`, `reproductionValidationArtifactRef`); `matched` computed by the
   Validation Service from comparing content-addressed outputs, never assertion-populated;
   `originalAuthor`/`reproducedBy` bound to Audit/Event Log actor entries (Constitution §9.1,
   Matrix row 53), not free strings.
2. 04 §7: add an `actor`/principal field to the audit/lineage event contract, so `reproducedBy`
   has something real to bind to.
3. 02 §3.1/§3.2/§5: add independent reproduction (Constitution §3 item 7, Matrix row 59) to the
   enumerated gate evidence and to what the Promotion/Gate Service enforces.
4. 05 §13: re-run the consistency check against 02/04 after 1-3 land, rewrite accordingly.
5. 05 §2.1/§11: state whether the rerun includes lockbox confirmation (recommend: yes, per the
   Thesis's "the same test"); add a distinguishing sentence to §11.3.
6. 05 §2.1: define "distinct identity" — different principal in the audit actor model; state
   explicitly that an agent rerun within the same orchestration session as the original does not
   satisfy row 59.
7. 02 §12: delete the overshot "fully retired from research-program vocabulary" sentence; keep
   the claim scoped to PA-10/candidate-reports (true), add the kickoff doc to the historical-
   reference list.

Attempt counter: 2 of 3 (uncapped, continuing).

### Attempt 3 — 2026-09-05 — FAIL (SHRINKING — explicit convergence classification)

**Layer 1: PASS** (unchanged, re-verified). **Layer 2: FAIL**, narrowly.

Attempt 2's F2 (doc 02 unamended), F3 (lockbox inclusion), and the minor (02 §12 overshoot) are
**all CLOSED**, verified substantive not passing mentions. Attempt 2's F1 and F4 are each reduced
to a single unstated binding sentence — not new findings, strict residue of the same two.

**F1 (residual of attempt-2 F1):** `reproducedBy` is bound to a real actor but nothing requires it
equal THE actor who actually produced the reproduction run. `originalAuthor`'s doc-comment states
it's derived from the AuditLineageEvent that recorded the original artifact's production;
`reproducedBy`'s doc-comment has no equivalent derivation sentence — it's just "reference to
whoever reran it," leaving a caller-supplied path one notch narrower than attempt 2 caught.

**F2 (residual of attempt-2 F4):** `orchestrationSessionId` is optional and has no assignment
authority stated — doc 04 §5.4 never says who mints it or that it's required for agent actors;
doc 07 (which owns agent identity) uses different vocabulary entirely ("agent-run identity and
iteration index") with no mapping to "orchestration session," and doc 07's own consistency check
doesn't cover doc 04. If the orchestrator can self-declare the session id, the "two agent roles,
one session = one well" loophole is back.

**Loop classification: SHRINKING**, per Frank's own re-derivation from the two snapshot diffs —
attempt 1 touched 5 files fixing "Thesis absent from the well," attempt 2 touched 3 files fixing
"record assertion-shaped, doc 02 unamended, two things unspecified," attempt 3's remaining findings
are each a single missing sentence. No new territory opened at any attempt.

**Minor findings, same pass:** doc 04 §5.4 has a broken sentence (process narration, not spec
content) and a phantom "§5.4" sub-numbering (§5 has no §5.1-5.3 siblings) — same pointer-defect
class as G1/G2, not yet bitten but flagged before it recurs. Gate-log/Frank-attempt narration is
embedded in canonical doc-comments and body text across 04/05 — wrong artifact for it, GATE-LOG.md
already carries this record.

**Fix routing (all to @architect, now spanning docs 02/04/05/07):**
1. 05 §2: add the missing derivation sentence for `reproducedBy` (mirror `originalAuthor`'s) —
   resolved by the Validation Service from the AuditLineageEvent whose artifactRefs contain
   `reproductionRunRefs`, never caller-supplied; a record where it doesn't match is malformed and
   rejected. Apply the same tightening to `originalAuthor`'s wording.
2. 04 §5.4: name the specific events in the closing paragraph rather than "AuditLineageEvent
   entries" generically.
3. 04 §5.4: make `orchestrationSessionId` required (not optional) when `actorType === 'agent'`;
   state it's assigned by the Agent Tool Layer/harness at session start, never self-declared.
4. 07 §3.1 item 3 / §3.2: reconcile "agent-run identity" with doc 04's `orchestrationSessionId` —
   every agent run carries the session id of whatever spawned it; runs from one orchestrator in
   one session share it. Add doc 04 to §11's consistency-check list, re-run it.
5. 05 §2.1 item 3: once 1-4 land, tighten the human-rubber-stamp clause to the now-mechanical rule,
   dropping the hedge that this can't be fully policed at the schema level.
6. 05 §13: re-run against the re-amended 04 and 07, rewrite.
7. Minor: repair 04 §5.4's broken sentence and phantom sub-numbering; drop 02 §12's "at line ~183"
   in favor of the section-name reference; move Frank-attempt narration out of interface
   doc-comments/body text into Editorial-corrections header lines across 04/05.

Attempt counter: 3 of 3 nominal (uncapped per Danny's ruling — continuing past this rather than
halting; SHRINKING classification independently supports continuing rather than escalating).

### Attempt 4 — 2026-09-05 — FAIL (SHRINKING, terminal notch named by Frank)

**Layer 1: PASS** (re-verified, nothing regressed). **Layer 2: FAIL**, narrowly.

Attempt 3's F1 and F2 both **CLOSED substantively**: `reproducedBy` now has the same derivation
rule as `originalAuthor` (self-supplied identity mechanically impossible at the
`IndependentReproductionRecord` layer); `orchestrationSessionId` required for agent actors,
vocabulary reconciled between docs 04/07. No stale cross-references from the §5 restructure
(verified: every "§5 item N" hit elsewhere in the repo is a Constitution §5 citation, not doc 04's).

**New-notch F1 (terminal residue of the F1 line):** the derivation chain now bottoms out at
`AuditLineageEvent.actor`, but no document states who populates `actor` on the event or that a
caller-supplied `actor` in any typed tool call is malformed. The set already has this exact pattern
solved for the lockbox (02 §4.1: "the gateway itself performs the check — not caller-side
discipline") — the actor field, which the project's one falsifiable claim rides on, has no
equivalent sentence.

**New-notch F2 (terminal residue of the F2 line):** session-initiation authority is unspecified.
Doc 04 says sessions are assigned "at session start"; doc 07 says runs "within one session" share
an ID — implicitly permitting one orchestrator to span multiple sessions. Nothing stops an
orchestrator from requesting a fresh session between driving the validator and the reproducer,
satisfying distinctness on paper while remaining one well in fact.

**Overreach flagged:** 05 §2.1 item 3 now claims the derivation rule "structurally rules out a
rubber-stamp entry" — true only once F1 above closes; a claim ahead of its mechanism, same failure
shape as attempt 1's "zero remaining occurrences" overreach.

**Convergence classification: SHRINKING**, re-derived from all 4 snapshot diffs. Frank explicitly
named this the terminal notch: the next layer down is authentication itself (which identity
provider, which token mechanism) — already correctly PROVISIONAL under U-12, owner Danny. The spec
cannot and should not pick the auth product; it must only (a) name the enforcement point for the
actor field, the way it already does for the lockbox, and (b) fence session-minting so an agent
cannot manufacture its own distinctness. Frank's own stated stopping condition: if attempt 5 closes
these two sentences, the gap is closed to its legitimate PROVISIONAL boundary. If attempt 5
surfaces yet another notch on the same line instead, reclassify STATIC and escalate to Danny — that
would mean the architect can't see the enforcement-point pattern the set already uses for the
lockbox, a briefing problem, not a spec problem.

**Fix routing (all to @architect, docs 04/05/07, plus a new PROVISIONAL row in 04/06/08):**
1. 04 §5.4: name the enforcement point — the component recording any AuditLineageEvent (Simulation
   Engine, Validation Service, Data Access Gateway, Audit/Event Log) MUST populate actor from the
   authenticated caller principal; actor is never a parameter of any typed tool call/API; a call
   supplying one is malformed/rejected. Mirror 02 §4.1's wording.
2. 04 §8 (or 06 §16 / 08 §3 U-12 widened in place): new PROVISIONAL row, owner Danny — "exact
   mechanism verifying AuditActor.actorId and minting/verifying orchestrationSessionId" — resolved
   in the same auth/authz design pass as U-12. This is the terminal boundary; below it the spec
   legitimately stops.
3. 04 §5.4 and 07 §3.1 item 3/§3.2: state session-initiation authority — a session is initiated
   only by a human principal, recorded as an AuditLineageEvent whose actor is that human; an agent
   run (including any orchestrator role) cannot initiate a session; every run spawned within a
   session inherits its ID, no nested minting. Fix 07's "within one session" phrasing so it no
   longer implies one orchestrator can legitimately span several.
4. 05 §2.1 item 2: when either actor is an agent, check the session-initiation event's human
   actorId for distinctness, not just orchestrationSessionId inequality.
5. 05 §2.1 item 3: rescope the overreach — state what the rule does rule out (a reproducedBy with
   no production event behind it) and what it defers (actor authenticity, to the new PROVISIONAL
   item).
6. 05 §13 and 07 §11: re-run after 1-5, rewrite.

Attempt counter: 4, uncapped, continuing. Orchestrator instruction from Frank: hold attempt 5 to
this stated terminal condition; reclassify STATIC and escalate to Danny if it doesn't hold.
