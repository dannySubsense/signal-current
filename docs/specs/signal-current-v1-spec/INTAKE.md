# Intake: signal-current-v1-spec

**Status**: APPROVED

**Date**: 2026-09-05
**Author**: Vane (drafted for Danny's review/approval — not self-approved)

---

## Problem Statement

Signal Current needs a canonical, frozen v1.0 specification set before any product implementation begins. Per `Signal_Current_Specification_Set/CLAUDE_CODE_GREENFIELD_KICKOFF.md` and `docs/specs/00-source-inventory-reconciliation.md` §8, that set is eight documents produced as one package: Constitution → System Architecture → Research Methodology → Data Architecture & Strategy IR → Validation & Statistical Controls → Portfolio/Deployment & Monitoring → Agent & Orchestration Layer → Implementation Roadmap. No implementation may begin until this set is complete, independently reviewed, and frozen.

A first, hand-authored draft of document 01 (`docs/specs/01-constitution.md`) was already produced this session, outside the proper spec-agent framework — self-checked with a blanket traceability claim ("every clause traces to §6 or §7.1") that Sol's independent cold review (`git log` commit `7926fac`) found false: several clauses trace to other reconciliation-matrix sections, and one clause was invented with no source at all. Sol also found the accompanying prior-art research (31 candidates, `docs/research/prior-art/PA-10-reuse-decision-register.md`) systematically overstates its own underlying reports, mislabels several REFERENCE-tier findings as "PARITY ORACLE" in direct tension with the reconciliation matrix's own rule that external engines are never alternate truth stores, and advances an ADOPT decision (skfolio) ahead of the register's own stated acceptance gates.

This sprint exists to redo document 01 properly through the spec-agent framework — and to carry the full 8-document sequence forward — so that authorship, traceability, and Frank's binding gate all apply as designed, rather than continuing ad hoc.

## Context

- Prior-art research (PA-01 through PA-08, 31 candidates, 29 candidate reports) is committed and pushed to `main` as of commit `0e51f7c` — this is a completed input to this sprint, not something this sprint redoes from scratch.
- `docs/specs/00-source-inventory-reconciliation.md` (the 58-row Reconciliation Matrix, "Working draft v0.2") is the authoritative synthesis of all prior chats/research inputs and is the primary source this sprint's documents must trace to.
- A first Constitution draft exists at `docs/specs/01-constitution.md` (commit `7926fac`) but per Sol's review is not lock-ready — it will need re-drafting through `@architect`/`@requirements-analyst` rather than hand-editing, per this repo's "Always Redispatch" orchestration discipline.
- Danny directed (2026-09-05, mid-session): a new standing rule that the `benchmark` subagent runs before QC in every spec/forge sprint, no exception unless obviously unneeded, no permission asked — this applies to every document in this sprint that introduces a numeric constant/threshold.
- Danny also reiterated (2026-09-05, sharing a department-os transcript): a `PROVISIONAL — unvalidated` tag on a numeric constant is not a compliant resting state — it must still resolve to a real citation or deletion. This applies directly to the 13 unresolved decisions (U-01..U-13) in the Reconciliation Matrix §7, several of which are exactly this kind of number (validation thresholds, CPCV applicability, multiple-testing diagnostic defaults).

## Capability Gaps This Sprint Closes

- No properly-authored, traceable Constitution exists yet — the current draft fails Sol's cold review on its own blanket traceability claim.
- The prior-art register's overstatement problem (Sol findings 4-8) has no correction mechanism yet — this sprint's `@spec-reviewer`/Frank gate steps are where that gets caught before Architecture work builds on it.
- No formal Intake/Interview/North-Star scaffolding exists around the Signal Current v1.0 spec package at all — this sprint creates it.

## Constraints

- Sequence must be the full spec-agent sequence (Intake → Interview → North Star → Requirements → Architecture → UI-Spec → Roadmap → Review → Frank binding gate → human approval), not `--lite` — this is Signal Current's core product specification, not bounded internal tooling.
- `03-UI-SPEC.md` may end up thin/non-applicable for this sprint (Signal Current's P0 is UI-last per its own Reconciliation Matrix row 51 — "No UI-first implementation... P0 proves numerical/semantic spine first") — `@ui-spec-writer` should say so explicitly rather than fabricate screens, if that's what the requirements actually call for.
- The orchestrator (this session) must not hand-author any of the 01-05 documents directly — every one is delegated to its named subagent, per this repo's "Always Redispatch" rule, which this sprint exists partly to restore compliance with.
- Numeric constants entering any document in this sprint go through the benchmark agent before QC, per Danny's standing directive — no exception without explicit justification.
- PROVISIONAL tags on any constant are not acceptable as a final state in a locked document — they must carry a concrete path to resolution (a named owner and what they're waiting on), per Danny's sharpened standard.

## Open Questions — resolved by Danny 2026-09-05

- **Constitution draft disposition: KEEP as input, not discarded.** Handed to `@architect` alongside Sol's findings as the fix list. Sol's blocking finding was the false traceability claim, not the substance — most clauses map to real matrix rows, just uncited/under-anchored. The major findings (dropped human-authorization gate, determinism/nondeterminism conflation) are additive fixes, not grounds for a rewrite.
- **PA-10 register correction: IN SCOPE for this sprint.** `@architect` reads that register directly in Step 4 (System Architecture); an uncorrected register would propagate every overstatement Sol found (premature skfolio ADOPT, self-contradictory PARITY ORACLE labels, dropped caveats) into Architecture. Cheaper to fix upstream now than to redo Architecture after a later correction.
- `docs/NORTHSTAR.md` confirmed: exists, `Status: ACTIVE` (non-DRAFT) — Frank's Step 8 Layer 2 check will not need a PROVISIONAL stamp.

---

## Approval

Danny's approval of this document (Status line above set to `APPROVED`) is what gates `spec-start` Step 0. Anything else — missing file, `DRAFT`, `REJECTED`, or the Status line absent entirely — is a HALT before any downstream doc generation.
