# Development Workflow

Adapted from `department-os`'s `docs/development-workflow.md` (Ledger, 2026-09-05) for Signal
Current's research/validation pipeline. Sections carried over near-verbatim are marked; sections
changed to fit a research pipeline (rather than a product with a UI) are noted inline.

## Roles

- **Danny** — product, requirements, architecture, scope, and final merge authority. All merges
  require his explicit approval.
- **Vane** — the primary builder. Implements features, research tooling, and documentation per
  approved specs.
- **Codex** — an independent engineering auditor and QC reviewer. Inspects the complete diff
  against the approved specification, adds or runs tests, and identifies confirmed issues for
  focused correction commits — independently of Vane, not as a rubber stamp. Onboarding to this
  repo's PR workflow is pending; not yet active here as of this document.
- **Sol** (ChatGPT), or another independent reviewer if Sol is unavailable — active in the GitHub
  PR workflow under the same independent-review discipline Codex will eventually use. Different
  tooling, same standard: verified against live files and primary sources, never handed the
  author's method.
- **Frank** — the binding judgment gate. Evaluates specifications before implementation greenlight,
  and evaluates completed (and reviewer-corrected) implementation before merge. Frank's verdict is
  PASS/FAIL/HALT — binding, no manual override.

Codex/Sol and Frank are complementary, not interchangeable: Codex and Sol are diff-level
engineering audits; Frank is a binding go/no-go judgment gate applied to the specification and to
the final implementation state. Multiple independent reviewers are additive, not redundant, as
long as each reads from primary sources rather than a prior reviewer's summary — see Independent
Review Discipline below. Doer and checker are deliberately different agents reading
independently, not the same agent reviewing its own output — see the Research Data Integrity
rules in Danny's global CLAUDE.md, which apply with extra force here since Signal Current's core
product IS research evidence.

## How work moves

1. **Intake** (mandatory) — the requirement or sprint is documented and approved.
2. **Interview** — a standalone stage, run inline in-session by default. It always produces
   `INTERVIEW.md` before specification begins, even when it finds zero gaps.
3. **Specification** — requirements, architecture, and roadmap are written for the slice
   (`01-REQUIREMENTS` → `02-ARCHITECTURE` → `04-ROADMAP` → `05-REVIEW`, per this repo's
   `CLAUDE.md` Project Workflow Pattern).
4. **Frank's binding specification gate** — the spec must pass before implementation begins.
5. **Human implementation greenlight** — Danny explicitly authorizes implementation to start.
6. **Forge / slice-by-slice implementation** — Vane implements a complete vertical slice against
   the spec.
7. **Independent review** — each PR records its own active independent reviewer(s) from the Roles
   roster above; they review the complete diff against the approved specification, independently
   of Vane's reasoning path (see below).
8. **Focused correction commits** — confirmed findings are fixed through focused, traceable
   commits, not folded silently into the original change. This must happen *before* step 9, so the
   final gate evaluates the code that will actually be merged.
9. **Frank's binding final forge gate** — evaluates the corrected, final branch state.
10. **Human merge approval** — Danny merges.

## Independent Review Discipline

**Give the unbiased auditor the map, not the path.**

An independent reviewer (Codex, Sol, Frank, or any future auditor role) should receive:

- The objective.
- The approved requirements.
- The architecture boundaries.
- The acceptance criteria.
- The relevant diff and live repository state.

The reviewer should **not** be handed the author's full reasoning path, debugging method,
preferred diagnosis, or a checklist designed to reproduce the author's own assumptions — a method
handed over is a lens handed over, and it caps the reviewer's ceiling at the author's.

Independence means more than different agent identities. Reviewers using the same corrupted
source or inherited assumptions as the author are not independent — N reviewers sharing one input
is one review, not N. This applies to Signal Current with extra force: a backtest, a validation
result, and a reviewer that all read the same truncated or mislabeled dataset produce unanimous
agreement that proves nothing. The author may provide factual orientation and repository context,
but must not constrain the auditor to the author's method. Findings must be verified against live
files, current git state, primary sources, raw data, and reproducible tests — not trusted because
a prior pass reported them.

## Git and Review Workflow

- Bootstrap commits may already exist directly on `main` — that's expected for initial repository
  setup (this repo's `chore: project bootstrap` and the specification-set rename/scrub commits).
- New implementation work uses focused branches and pull requests.
- Direct implementation pushes to `main` are not allowed after bootstrap, unless Danny explicitly
  authorizes an exception.
- Every pull request is tied to an approved intake, specification, or recorded decision — not
  opened speculatively.
- Vane authors the implementation on its branch.
- Each PR records its own active independent reviewer(s) from the Roles roster above; they review
  the complete diff independently against the approved specification (see Independent Review
  Discipline above).
- Confirmed findings are fixed through focused, traceable commits on the same branch.
- Frank's final binding forge gate evaluates the corrected final branch state — not the
  pre-correction diff.
- Required checks and tests must pass before merge.
- Danny provides final merge approval. Push policy is manual-push-only: nothing is pushed to a
  shared/reviewed branch without Danny's explicit authorization, and no force-pushing such a
  branch without his explicit direction.
- Merge strategy (squash / merge commit / rebase) remains human-controlled until separately
  decided and recorded in a DDR.

This updates this repo's `CLAUDE.md` Git Workflow placeholders: **Branching** is PR / feature-branch
flow (not direct-to-mainline) for anything past the bootstrap commits above; **Push policy** is
manual-push-only.

## Judgment Gate Protocol (binding, every gate)

Carried over verbatim from `department-os` — this is general judgment-gate discipline, not
product-specific, and applies exactly as written to Signal Current's validation and forge gates.

Frank's gate is dispatched this way **every time**, without being requested. It is a mechanism, not
a favour. Established 2026-08-16 (department-os) after a briefed gate returned PASS on an artifact
the Composer then failed on three reachable defects.

1. **The gate reviews a frozen commit in an ISOLATED CHECKOUT — never the live working tree.**
   Create a detached worktree at the exact SHA under review and point the gate at that path. The
   live tree is disqualifying: a cold gate pointed at a repo root has previously found defects by
   reading uncommitted fix comments — an answer key, not a review. If the gate can see the fixes,
   the gate is not testing anything.
2. **The brief is generated, not authored.** Only these fields, no free prose from the
   orchestrator: repository path (the isolated checkout), the SHA under review and its parent, the
   verdict required, and an explicit statement that nothing else is supplied deliberately. No
   artifact list, no summary of what the change does, no statement of what it claims, no list of
   binding documents, no prior review results, no test outcomes, no scope boundaries. If the
   Producer is typing sentences into a gate brief, the Producer is setting the gate's ceiling at
   their own.
3. **The whole package is in scope by default.** Implementation, any data pipeline it produces,
   tests and what they actually assert, the specifications said to govern them, dependencies relied
   on, status and gate records, and the review process that produced it. The gate chooses its own
   targets and needs no permission for any line of inquiry.
4. **Operational facts are allowlisted; conclusions are never supplied.** A fact may be given only
   if withholding it would waste the gate's time or manufacture a false finding. Anything that
   shapes what the gate expects to find is a conclusion, not a fact, and is withheld.
5. **Exhaustiveness is obligatory.** Where an invariant must hold, the gate enumerates every site it
   must hold at and checks each — it does not confirm the invariant holds somewhere. A verdict
   carries its own coverage claim: how many sites, and how many checked.
6. **The gate record names the exact commit gated.** A verdict against a SHA does not transfer to a
   later SHA containing fixes. Re-gate the commit that actually contains them.
7. **Prefer a different model from the one that produced the work.** This buys independence on the
   code axis only — a gate that reads the same specs, repo, and data as the producer shares their
   source-axis blind spots, and no model difference repairs that. For Signal Current specifically,
   see Danny's global CLAUDE.md Research Data Integrity doctrine — the source axis is exactly
   where a shared corrupted dataset would slip past every reviewer at once.
8. **The independent test suite is QC-only and must be STRUCTURALLY out of the implementer's
   reach.** Implementers work from the gated specification and may write and run their own
   spec-derived tests; they must not read, edit, or run the independent suite. This applies during
   corrections as much as during first implementation. Keep it in a QC-only branch or worktree so
   the separation is mechanical. When a test and the code disagree, that disagreement is escalated
   — neither side silently conforms to the other.

---

## Architecture and scope decisions

Decisions that change architecture, choose a runtime, or expand scope beyond a current sprint are
recorded as a DDR in `docs/specs/signal-current-ddrs/`, not just discussed in passing.

## No parallel status cache

`PROGRESS.md` (per sprint) is the single live status record. Do not maintain a second copy of
current state anywhere else — not in a root status card, not in a separate tracking file, not in
prose at the top of another doc. `department-os` removed a root `NOW.md` on 2026-08-14 after it
went stale, claiming a slice "fully complete" after a later-discovered defect disproved it, while
`PROGRESS.md` recorded the correction. One record, updated in place.

If an agent instruction file (including an untracked local one such as `CLAUDE.md`) tells you to
maintain a status file that no tracked document describes, treat that as a defect in the
instruction and raise it — an artifact committed to the repository whose governing rule is not in
the repository cannot be verified, reviewed, or inherited by a fresh clone.
