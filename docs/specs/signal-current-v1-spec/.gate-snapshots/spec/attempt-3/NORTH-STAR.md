# Sprint North Star: signal-current-v1-spec

**Status**: Locked

**Date**: 2026-09-05

## Declared Intent

Produce Signal Current's canonical v1.0 specification set — Constitution, System Architecture, Research Methodology, Data Architecture & Strategy IR, Validation & Statistical Controls, Portfolio/Deployment & Monitoring, Agent & Orchestration Layer, Implementation Roadmap — through the proper spec-agent pipeline, correcting the defects Sol's independent cold review found in the first hand-authored Constitution draft and the prior-art reuse register, so that no implementation begins on an unreviewed, self-certified foundation.

## In Scope / Out of Scope

See `01-REQUIREMENTS.md` Out of Scope (once produced by `@requirements-analyst`).

## Success Criteria (Layer 1 — fidelity)

- All eight canonical documents exist, each produced by its named subagent (never hand-authored by the orchestrator), traceable to `docs/specs/00-source-inventory-reconciliation.md` with correct section/row anchors, not a blanket unverifiable claim.
- The existing `01-constitution.md` draft is corrected, not discarded — Sol's blocking/major findings (false traceability claim, dropped human-authorization gate, determinism/nondeterminism conflation) are resolved.
- `docs/research/prior-art/PA-10-reuse-decision-register.md` is corrected before `@architect` reads it in Step 4: no register row states a claim stronger than its underlying candidate report, evidence-level/status fields (TRIAGED/PARTIAL/RESEARCH COMPLETE/DECIDED) are added, and the self-contradictory "PARITY ORACLE" labels are resolved against the reconciliation matrix's own rule that external engines are never alternate truth stores.
- `docs/CADENCE.md` and `docs/INVARIANTS.md` exist as project-level governance docs (added mid-sprint, outside Frank's gate for this sprint, since they are not sprint artifacts).
- The benchmark subagent runs before QC on every document in this sprint that introduces a numeric constant/threshold — no exception without explicit justification.
- Frank's binding spec-gate (`LANE: spec-gate`, standard briefed contract) reaches PASS on the full eight-document set — the gate loop keeps running past a nominal attempt-3 ceiling rather than auto-halting, per Danny's explicit direction this sprint; escalation to Danny happens only if the orchestrator independently concludes the loop genuinely isn't converging.

## Traceability (Layer 2 input — Frank verifies independently, does not trust this field)

Project North Star bullet(s) this sprint serves: `docs/NORTHSTAR.md` Thesis ("A strategy only counts as validated if someone other than its author can rerun the same test and get the same result") and Non-goals ("does not use a threshold, lookback window, or cutoff without a source, an owner if it is still provisional, or removal") — this sprint exists specifically to correct a case where the orchestrator's own self-certification (not an independent rerun) was mistaken for validation, and to ensure the spec set that follows does not carry forward unsourced numeric claims.

Project North Star status at gate time: ACTIVE (non-DRAFT) — Layer 2 verdict is a normal binding PASS/FAIL, no PROVISIONAL tag.
