# Cold Gates: Two Mandatory Requirements, Not One

**Date:** 2026-09-06
**Origin:** signal-current-v1-spec sprint, spec-gate attempts 7-17
**Author:** Vane, for Danny — portable across repos

## The rule

A binding judgment gate (Frank, or any equivalent) run "cold" requires **both** of the following,
always together. Meeting one without the other is not compliant, even though each independently
produces real findings.

### 1. Unbriefed dispatch

Repo + SHA (+ parent SHA) + verdict-required only. No file list, no scope narration, no summary of
what changed, no prior-verdict content, no "here's what to look for."

A method or checklist handed to the gate is a lens handed over — it caps the gate's ceiling at the
dispatcher's own.

### 2. Isolated detached checkout

The gate reviews a frozen commit in its own detached worktree/checkout — never the live repository
root, never the working tree, **even when the working tree is clean at the SHA under review. There
is no clean-tree exception.**

A repo root can contain things outside the reviewed commit's own content that leak the answer:
uncommitted scratch files, stray comments, and — concretely, on any repo running an iterative
gate-and-fix loop — prior gate-snapshot directories sitting right next to the code under review,
which are a literal visible record of every prior finding and fix. A "cold" dispatch pointed at a
directory containing that history is not testing anything, even if it never explicitly reads those
files. Isolation exists to make the leak structurally impossible, not merely unlikely.

## Why this is written down

On signal-current, ten consecutive "Cold Frank" dispatches across one sprint correctly implemented
requirement 1 and never implemented requirement 2 — every one was pointed at the live repo root.
One dispatch additionally asserted a clean-tree exception ("because the tree was clean at HEAD, the
result stands") that does not exist in any governing protocol.

An independent cold review from a separate tool (Codex CLI / Sol, itself run cold — no shared
context with the gate history) caught this twice: once flagging the general gap, once specifically
rejecting the clean-tree reasoning.

The first dispatch run from an actual isolated detached worktree, with a minimal generated brief
(repo path, SHA, parent, verdict required, nothing else), passed cleanly. The underlying work was
sound; the gate process checking it was not.

## Related discipline, same incident

A gate's coverage claim ("swept N files, found only these M things") must actually be checkable
against the bytes, not asserted. The same loop produced a numeric-sweep claim that didn't reproduce
(named 2 disclaimed figures when the real count was higher). An unverified exhaustiveness claim is
the same class of defect as an unsourced number — spot-check it before treating it as established.

## How to apply, in any repo

Before dispatching a "cold" gate, check both halves explicitly:

- Is the prompt free of any file list, scope hint, or prior-finding narration?
- Is the gate pointed at an isolated detached checkout of the exact SHA — not the live repo, even
  if clean?

If either answer is no, the dispatch is not cold regardless of what it's labeled. Treat its verdict
as advisory at best until a genuinely compliant re-dispatch confirms it.
