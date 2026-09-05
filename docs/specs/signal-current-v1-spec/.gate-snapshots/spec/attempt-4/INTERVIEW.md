# Interview: signal-current-v1-spec

**Status**: Complete
**Mechanism**: Inline
**Date**: 2026-09-05

## Seed Questions (gap-diff)

| # | Category | Question | Answer | Assumed? |
|---|---|---|---|---|
| 1 | testing/rollback | Where does the new benchmark-before-QC rule plug into the 8-step sequence? | Before each document's QC individually, not once at the end. | no |
| 2 | downstream impact | Should the PA-10 register correction (per Sol's findings) happen as a dedicated fix pass before Step 3, or folded into @spec-reviewer's Step 7 loop? | Dedicated fix pass now, before Step 3 — Architecture (Step 4) would otherwise inherit the register's overstatements. | no |
| 3 | non-functional | Should Frank's Step 8 gate for this sprint be dispatched cold (unbriefed) or with the standard briefed contract this skill defines? | Standard briefed contract for this sprint's own scheduled gate. (Distinct from the general Cold Frank escalation protocol adopted separately as DDR-001 — that applies when Vane cannot resolve a decision via the decision matrix, not to a sprint's own scheduled Frank gate.) | no |
| 4 | non-functional | Is there a timeline/pace target for this spec sprint? | No fixed timeline — runs at whatever pace rigor demands. | no |
| 5 | downstream impact | Signal Current's own Reconciliation Matrix says P0 is UI-last. Should @ui-spec-writer produce an explicit "no UI needed yet" stub, or should the step be skipped entirely? | Explicit stub — keeps the artifact set complete and the reasoning documented. | no |
| 6 | edge case | If Frank's gate fails 3 times with STATIC/THRASHING, should the whole sprint pause, or should stuck documents be isolated while others proceed? | Neither as originally framed — "keep going" means keep looping the gate-and-fix mechanism itself past the nominal attempt-3 ceiling, not isolate-and-skip and not hard-halt. Escalate to Danny only if Vane independently concludes the loop truly isn't converging after sustained attempts, not on a fixed count. | no |

## Adaptive Follow-ups

| Triggered by | Question | Answer |
|---|---|---|
| Q1-6 discussion of governance | Does docs/specs/01-constitution.md overlap with docs/INVARIANTS.md? | No — different jobs. INVARIANTS.md is generic engineering-process hygiene (portable across projects); the Constitution is Signal Current's domain-specific scientific/research rules. Neither covers the other's job; cross-reference both. |
| INVARIANTS.md discussion | Does adding docs/INVARIANTS.md need Frank's spec-gate, or is it adopted directly? | Neither framing was right — it's project-level governance infrastructure (same tier as docs/NORTHSTAR.md), not a sprint artifact at all, so it's outside this sprint's Frank gate entirely. Added directly, alongside a newly-added docs/CADENCE.md (also missing, also project-level). |
| Repo-doc-structure correction | Repeated correction: exactly one CADENCE.md/INVARIANTS.md/NORTHSTAR.md per repo; each sprint gets its own separate NORTH-STAR.md. | Confirmed and corrected; both missing project-level docs added this session (commit 580f64d). Saved as a durable feedback memory so this does not need re-explaining again in this repo. |
| "use your decision matrix" | Did Danny actually hand Vane a decision matrix? | No — Vane had used the phrase loosely. Checked with wright (agent-rig) over Switchboard: a real one exists, agent-rig DDR-001 §2 ("Recommendation, not menu," 5 criteria including a same-day-added "slow and safe" criterion). Adopted into signal-current as its own DDR-001, cross-referenced in INVARIANTS.md #10. |
| Decision matrix follow-up | What does "Cold Frank" (escalation when the matrix doesn't resolve) actually mean? | Genuinely unbriefed: repo path + SHA + verdict-required only — no file list, no objective, no framing, no map. Distinct from and stronger than the existing Codex/Sol "map, not route" doctrine. Adopted as part of the same DDR-001. |

## Stopping Rationale

Six seed questions plus five adaptive follow-ups, all opening genuinely new resolved threads (no filler, no repeated non-generative exchanges) — well past the 5-7 soft anchor, but every exchange resolved a real gap rather than padding the count. Closed when Danny confirmed no further items to raise and directed the Interview to close.
