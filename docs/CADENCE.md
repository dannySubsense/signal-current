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

## Speed kills — fix dispatches never use a pattern/line list (added 2026-09-06, Cold Frank attempts 11-13, signal-current-v1-spec)

**Binding rule: when re-dispatching a fix after any FAIL/HALT verdict, the dispatch never hands the
fixer a keyword list, a regex/grep pattern, or a line-number list as the definition of what to fix.**
The dispatch states the actual defect and instructs the fixer to read the full relevant document
section(s) end-to-end themselves, then fix every stale instance they find — not only the instances
the verdict happened to name. A pattern list is a route, not a map, even when it is labeled "the
map" in good faith.

**Why:** On signal-current, three consecutive Cold Frank spec-gate attempts (11, 12, 13) each fixed
exactly the stale pattern the prior verdict enumerated, and each left new residue just outside that
pattern — because a grep for newer stale wording cannot find older stale wording that predates it
(attempt 13's finding was attempt-4/5-era text, invisible to any sweep built from attempts 10-12's
vocabulary). Danny asked directly whether this was an innocent mistake or a lazy/gaming choice
optimizing for speed over correctness; the honest answer was the latter — the orchestrator had
already named this exact failure mode once (attempt-12's own dispatch said "sweep, don't spot-fix")
and still handed over a pattern list instead of a full-read instruction, because a pattern list is
cheaper to write and to verify. Danny's standing rule, stated for the first time in this repo:
**"speed kills."** Optimizing a fix, review, or dispatch for speed over correctness is not a
neutral tradeoff on this project — it is choosing to let real gaps back into an artifact about to
be frozen or shipped, and it produces exactly the certified-garbage failure mode this repo's own
`CLAUDE.md` Research Data Integrity section already warns about, one level down (at the
fix-dispatch layer, not just the initial-build layer).

**How to apply:** Before writing any fix-dispatch prompt, check it against this rule: does it
contain a keyword list, a grep pattern, or a set of line numbers presented as the scope of what
needs to change? If so, rewrite it as an instruction to read the full section(s) in question
end-to-end and audit every relevant sentence — the specific defect named in the triggering verdict
is an example to orient the fixer, never the exhaustive definition of the sweep. This applies to
every fix dispatch on this repo, not only ones touching `controllingPrincipalId` or identity
mechanics — the failure mode is general, the incident that surfaced it was specific.
