# DDR-001 — Decision Matrix and Cold Frank Dispatch Protocol

- **Status:** ACCEPTED
- **Author:** Vane
- **Date:** 2026-09-05
- **Sprint:** signal-current-v1-spec (Intake/Interview stage)
- **Supersedes:** —
- **GitHub issue:** —

---

## §1 Context

During this sprint's Interview stage, Danny used the phrase "your decision matrix" as an instruction ("decide, use your decision matrix"). No such formal artifact existed in signal-current, and Vane had used the phrase loosely to describe ad hoc reasoning. Danny caught this ("Did I give you a decision matrix?") and asked Vane to check with wright (agent-rig).

wright's first answer denied any formal matrix existed there either, then corrected: **agent-rig's `docs/specs/agent-rig-ddrs/DDR-001-ask-vs-act-decision-flow.md` §2 ("Recommendation, not menu") is exactly this** — a real, ACCEPTED, versioned artifact, not a loose phrase. Separately, ledger (department-os) relayed unprompted that the same session had also hardened a related, distinct rule — Cold Frank's dispatch protocol — after ledger over-narrowed Frank's scope across four gate rounds by naming files/areas to check, letting a fabricated citation sit just outside the boundary that kept getting drawn.

Danny confirmed live in this session: whenever he says "decide," apply this matrix; if it doesn't resolve, escalate to Cold Frank — and clarified precisely what "cold" means, because the failure mode ledger described (unintentional scope-narrowing) is exactly the trap this DDR exists to close for signal-current before it recurs here.

**Source design is agent-rig's, not re-derived here** — same author≠install-location pattern agent-rig's own DDR-013 (Frank) and DDR-001 already establish. Signal-current adopts the fixed source; any future change belongs in agent-rig's DDR-001 and is inherited, not diverged from silently.

## §2 Principle

### 2.1 The decision matrix (adopted verbatim from agent-rig DDR-001 §2)

Whenever Danny says "decide" — or, more generally, whenever a decision resolves to presenting a recommendation rather than acting silently — give **one ranked recommendation**, evaluated against:

1. **Most correct** — the best available answer given everything already established in-session, not merely one that survives scrutiny.
2. **Risk-averse** — among defensible options, the one with the smallest, most reversible blast radius.
3. **Aligned with best practices and industry standards** — for the domain in question; a novel or idiosyncratic approach needs a stated reason, it isn't the default.
4. **YAGNI-compliant** — no speculative generality, configuration, or abstraction beyond what the decision at hand actually needs.
5. **Slow and safe — speed is not a measure of progress; speed kills.** A recommendation that trades verification for velocity is not more correct for having arrived faster. Passing through N gate/fix rounds is never itself evidence that round N+1 deserves more trust than round 1.

A flat menu without a stated recommendation is appropriate only for an irreducible preference/values call with no technically correct answer — and even then, state a lean.

### 2.2 Cold Frank dispatch protocol (adopted from department-os's live-hardened doctrine, per ledger, 2026-09-05)

If the matrix in §2.1 does not resolve the decision — genuine ambiguity, not just difficulty — escalate to Frank, dispatched **cold**:

- Repo path + target SHA + "verdict required." Nothing else.
- **No** named file list, **no** stated objective, **no** "this is about X" framing, **no** map, **no** route, **no** checklist.
- Full rein: Frank decides what to look at, follows his own breadcrumbs, and decides what a verdict even means in this context.
- This is stronger than the Codex/Sol "map, not route" doctrine used elsewhere in this repo (which still supplies objective + architecture + what's-claimed) — Cold Frank gets none of that, not even the map.

**The specific failure this closes:** in department-os, an orchestrator narrowed Frank's scope across four gate rounds by naming files/areas to check as part of what felt like helpful framing — the real defect (a fabricated citation) sat just outside the boundary that kept getting drawn. Any scope hint at all, however well-intentioned, is leading the witness. Signal-current adopts the same protocol before that failure has a chance to recur here.

---

## §3 Decision

Signal Current adopts both the decision matrix (§2.1) and the Cold Frank dispatch protocol (§2.2) as binding practice, effective immediately, encoded in `docs/INVARIANTS.md`.

### 3.1 Scope

This DDR records the adoption decision and the fixed doctrine text. It does not re-derive the doctrine — agent-rig's DDR-001 and department-os's DDR-0002 remain the source-of-record for any future revision to the underlying criteria.

### 3.2 Not decided here

- Whether signal-current builds any mechanical tooling (a hook, a skill) around either protocol, versus relying on this DDR + `INVARIANTS.md` as a standing reminder — a future spec-time question if this proves insufficient in practice.
- Whether Frank's `LANE: spec-gate`/`LANE: forge-gate` contracts in this repo's `/spec-start`/`/forge-start` skills should be revised to always use the cold protocol by default, versus the standard briefed contract those skills currently define (Danny explicitly chose the standard briefed contract for the `signal-current-v1-spec` sprint's own Frank gate, per this sprint's Interview — the cold protocol in §2.2 applies when Vane cannot resolve a decision via §2.1 and escalates ad hoc, a distinct case from a sprint's own scheduled gate).

---

## §4 Risks

| Risk | Mitigation |
|---|---|
| Vane unintentionally leads Cold Frank the same way ledger did, through well-meant framing | §2.2 is written to name the exact failure mode; re-read before every Cold Frank dispatch, not just once at adoption |
| The matrix in §2.1 gets applied as a rote checklist recitation rather than genuine reasoning | Recommendations must show which criteria were actually load-bearing for that specific decision, not a boilerplate five-line recitation |
| Doctrine drifts from agent-rig's/department-os's own future revisions, since this DDR is a point-in-time adoption | Treat this DDR's text as adopted-then-fixed unless a future DDR here explicitly re-syncs it — do not assume automatic propagation from the source repos |

## §5 Open Questions

- Mechanical enforcement (§3.2) — deferred, not decided here.
- Whether `/spec-start`/`/forge-start`'s Frank contracts should default to cold — deferred, not decided here.
