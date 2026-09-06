# Invariants

One `INVARIANTS.md` per repo. Sits alongside `docs/NORTHSTAR.md` and `docs/CADENCE.md` as project-level governance infrastructure — not a sprint artifact, not gated by any single sprint's Frank pass.

These are inviolable engineering-process rules for this repo. They are distinct from `docs/specs/01-constitution.md`, which states Signal Current's domain-specific scientific/research invariants (no privileged market dimension, evidence boundaries, statistical discipline, agent authority over numerical evidence). Neither document covers the other's job — check both.

1. No numerical threshold, cutoff, target, budget, score, confidence level, or metric in a data or research path is treated as established merely because it appears reasonable. Every such number needs one of: a citable, reproducible source that has actually been reproduced; a documented benchmark run against this system; an explicit `PROVISIONAL — unvalidated` marker with a named human owner; or deletion. A `PROVISIONAL` tag is not a resting state — it must still resolve to a citation or deletion, and does not survive being carried forward re-tagged. This governs data/research-path numbers specifically — it does not extend to ordinary implementation values (structural counts, version numbers, port assignments, pagination limits) outside that scope. (Global CLAUDE.md, Research Data Integrity; also `docs/specs/01-constitution.md` once drafted.)
2. The `benchmark` subagent runs before QC in every spec and forge sprint, no exception unless the artifact obviously contains no numeric constants to audit. No permission asked — binary yes/no, never a hedge.
3. No speculative infrastructure — frameworks, services, CI, or abstractions are not scaffolded ahead of a concrete, current need.
4. The orchestrator (Vane) never hand-authors a spec document directly — every one of the 01-05/08 documents is delegated to its named subagent (Always Redispatch discipline). Running a component skill individually does not waive this — the obligation travels with the artifact, not the entry point.
5. Independent review requires independent sources, not just independent reviewers reading the same well. Brief an auditor (Sol) with the objective, requirements, acceptance criteria, and diff — never the author's own method or checklist (map, not route).
6. Frank's binding verdict has no manual override, at either the spec-gate or the forge-gate.
7. No direct pushes to `main` without explicit exception; no force-push on shared/reviewed branches without explicit direction. Per this repo's push policy: manual-push-only — Danny reviews and pushes/merges explicitly.
8. Every PR ties to an approved Intake, spec, milestone, or recorded decision — never opened speculatively.
9. Capture every decision, deviation, and HALT to LORE immediately when it happens, per `CLAUDE.md`'s Capture Behaviour.
10. When Danny says "decide," apply the 5-criterion decision matrix (most correct, risk-averse, best-practices-aligned, YAGNI-compliant, slow-and-safe) — give one ranked recommendation, not a menu, unless the call is genuinely an irreducible preference with no technically correct answer. If the matrix doesn't resolve it, escalate to Frank dispatched cold: **(a)** repo path + SHA + verdict-required, nothing else — no file list, no objective, no framing, no map; **and (b)** an isolated detached checkout of that exact SHA — never the live repo root or working tree, even when it's clean. Both requirements are mandatory together; meeting only (a) is not "cold" (this repo ran ten dispatches meeting only (a) before the gap was caught — see `docs/specs/signal-current-ddrs/COLD-GATES.md`). See `docs/specs/signal-current-ddrs/DDR-001-decision-matrix-and-cold-frank.md`.

## Violation Response

If an invariant would be violated:
1. **HALT immediately**
2. Report which invariant
3. Explain the conflict
4. Wait for human resolution

**Invariants cannot be overridden by contracts or instructions.**
