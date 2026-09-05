# Cadence

One `CADENCE.md` per repo. Sits alongside `docs/NORTHSTAR.md` and `docs/INVARIANTS.md` as project-level governance infrastructure — not a sprint artifact, not gated by any single sprint's Frank pass.

Full-weight features: Intake → Interview → Specification (`NORTH-STAR.md` + 01-05 docs) → benchmark agent (before each doc's QC, not just once at the end — proactively, for every numeric constant/threshold/budget/cap entering that doc) → Frank binding spec-gate (`LANE: spec-gate`) → human approval → Forge (slice-by-slice) → Sol (Codex CLI) advisory review, cold, map-not-route → focused correction commits → Frank binding forge-gate (`LANE: forge-gate`) → human merge approval.

Bounded internal tooling ("lite mode", `/spec-start --lite`): a single spec document at `docs/tooling/{tool-name}/SPEC.md` stands in for the full Intake/Interview/NORTH-STAR/Requirements/Roadmap set. Same binding-gate discipline throughout, no manual override at either Frank gate.

`PROGRESS.md` (or the lite-mode equivalent) is updated slice-by-slice, not at the end — it is ground truth for build state, read first on session resume and trusted over any session's own recollection of prior state.

## Roles

- **Danny** — final product/specification acceptance authority.
- **Vane (Claude Code)** — primary builder, owns implementation and the Intake → spec → forge delivery loop.
- **Sol (Codex CLI)** — independent diff-vs-spec auditor, advisory, cold review (repository source, target SHA, diff, tests — no LORE, no map, no prior memory before initial findings).
- **Frank** — binding judgment gate (PASS/FAIL/HALT, no manual override), applied at spec-gate and forge-gate.

## Checkpoints

| Phase | Checkpoint | Human Required? |
|-------|------------|-----------------|
| Spec | Intake approval | **Yes** |
| Spec | Each doc's benchmark pass | No (automatic, before that doc's QC) |
| Spec | Frank's spec-gate PASS | No (binding, not human, but drives next step) |
| Spec | Final approval (Step 9) | **Yes** |
| Forge | After each slice | No |
| Forge | Sol's advisory review | No (advisory — Danny decides whether to act on findings) |
| Forge | Frank's forge-gate PASS | No (binding) |
| Forge | Session end | Optional |
| Review | Merge | **Yes** |

## Recovery

### Stuck in Spec (Frank's spec-gate FAIL/HALT)
- Snapshot artifacts before re-delegation (`.gate-snapshots/spec/attempt-{N}/`)
- Route Fix/Next-step items to the named subagent per the failing document
- Keep looping the gate-and-fix cycle — the attempt counter does not impose a hard ceiling that halts the sprint; escalate to Danny only if the orchestrator independently concludes the loop genuinely isn't converging after sustained attempts, not on a fixed count.

### Stuck in Forge (Frank's forge-gate FAIL/HALT)
- Check test failures for root cause
- Review QC report and Sol's advisory findings for violations
- May need a spec amendment (back to Spec phase)

### Stuck in Review
- Address feedback in Forge phase
- Re-run QC and benchmark checks after changes
- Update PR

## Verifying gate findings (added 2026-09-05, Cold Frank attempt-8, signal-current-v1-spec)

Every Frank finding that asserts a repo-state fact (a file/directory exists or doesn't, a command
returns a specific result, a value is present or absent) is re-run by the orchestrator against the
live tree before any fix is authored on its basis. A Frank verdict is an input to verification, not
a substitute for it — doer≠checker still fails if the fixer adopts the checker's claim about the
world without independently opening the source. On signal-current, attempt 7's Cold Frank wrongly
claimed `.gate-snapshots/` didn't exist (checked the wrong path); the orchestrator wrote that
claim into the gate log three times, including one fabricated command-output citation, without
running `ls` on a directory it had been writing into all evening. Caught only by a second Cold
Frank dispatch.
