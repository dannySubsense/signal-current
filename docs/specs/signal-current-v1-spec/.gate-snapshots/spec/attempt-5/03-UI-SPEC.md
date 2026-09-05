# UI Specification: signal-current-v1-spec

**Status**: Complete (explicit no-UI-yet stub)
**Author**: @ui-spec-writer
**Date**: 2026-09-05

---

## 1. Why this document specifies no screens, flows, or components

This sprint's own Interview (`docs/specs/signal-current-v1-spec/INTERVIEW.md`, seed question 5)
resolved that the UI-spec step is not skipped but produced as an **explicit stub** — "keeps the
artifact set complete and the reasoning documented" — rather than a silent omission. This document
is that stub. It is deliberately not a placeholder to be filled in later; its job is to state, with
reasoning, why no UI is being specified now, and to make the future trigger and the present
inspectability floor explicit so neither gets lost.

The controlling source is the Reconciliation Matrix, row 51 (`docs/specs/00-source-inventory-reconciliation.md`,
line 117):

> "UI is an operator surface over contracts. No UI-first implementation. Every long-running state
> must be inspectable, but P0 proves numerical/semantic spine first."

Document 02 (`docs/specs/02-system-architecture.md`) §6, "Agent & Orchestration layer is not on the
P0 critical path," restates and sharpens this for the same P0 slice a UI would sit in front of:

- "No P0 component listed in §5 has the Agent Tool Layer as a required dependency for its own
  function."
- The Quant Laboratory pipeline (compile → simulate → validate → gate → seal) "must all function
  correctly with zero agent involvement — a human or a script can drive every P0 workflow."
- The Agent Tool Layer (and, by the same argument, any UI-facing surface) is "not architected as a
  load-bearing P0 component."

Document 02 §12 (anti-patterns) states the same conclusion directly for UI specifically: "Building
a UI or dashboard ahead of the typed contracts it would sit on top of — rejected per Matrix row 51
... this is also why `03-UI-SPEC.md` for this sprint is a stub, not a design."

Document 08 (`docs/specs/08-implementation-roadmap.md`) sequences P0 as the deterministic
numerical/semantic spine (SourceSnapshot → ResearchRecord → StrategyIR → simulation → validation →
sealed StrategyArtifact) with no UI dependency anywhere in that path.

### The reasoning, stated plainly

There is nothing to specify screens, flows, layout, or component hierarchy *for* yet, because
there are no stable typed contracts for a UI to sit on top of. P0's job is to prove those contracts
— `SourceSnapshot`, `ResearchRecord`, `StrategyIR`, `StrategyArtifact`, the validation-gate outputs
— are real, deterministic, and sealed. A UI specified before that spine exists would be specifying
against a moving target: screens would be designed around data shapes and workflows that P0 itself
is still discovering and may change. This is precisely the risk Matrix row 51 names ("risk of
building dashboard before semantics") and precisely what document 02 §12 calls out as the
anti-pattern to reject.

This is a sequencing decision, not a permanent exclusion. UI is deferred, not cancelled.

---

## 2. What triggers a real UI-spec sprint in the future

A future `@ui-spec-writer` sprint becomes appropriate — and only becomes appropriate — when all of
the following are true, mirroring the same "activate after core is stable" criterion document 02
§6 applies to the Agent & Orchestration layer (Matrix row 56: "activate after core APIs/artifacts/
gates are stable enough to constrain agents"):

1. **P0 is architecturally satisfied.** Per document 02 §7.1, the end-to-end path through real
   (not stubbed) components — `SourceSnapshot` → `ResearchRecord` → `StrategyIR` → deterministic
   simulation → validation/statistical controls → sealed `StrategyArtifact` — executes and the P0
   exit criteria in document 02 §7.3 are met.
2. **The typed contracts are stable, not just present.** The schemas for `ResearchRecord`,
   `StrategyIR`, `StrategyArtifact`, and validation-gate outputs are frozen or changing only
   additively — an operator surface built against contracts still under active revision would be
   rebuilding itself in lockstep with the numerical core, defeating the sequencing purpose.
3. **There is a concrete operator need the CLI/API/logs surface cannot serve.** Per Matrix row 51,
   UI is "an operator surface over contracts" — it exists to serve a real operator workflow (e.g.
   monitoring a running validation batch, comparing sealed `StrategyArtifact`s side by side,
   triaging a `HealthAssessment` alert) that has become painful or error-prone to do through direct
   API/log inspection alone. The need must be named, not assumed.
4. **Portfolio/Deployment/Monitoring domains have progressed far enough to have long-running state
   worth an operator surface.** Document 02 §5's domain table marks Portfolio and Deployment &
   Monitoring "Out of P0" — a UI sprint gains little scoping a surface for domains with no running
   state yet to inspect.

When these hold, the next UI-spec sprint should follow the same Intake → Interview → Requirements →
Architecture → UI-Spec sequence as this one, and should specify screens, flows, and components
against the by-then-stable contracts — not retrofit this stub.

---

## 3. What inspectability requirement applies now, pre-UI

Matrix row 51 is explicit that the deferral is not a deferral of the underlying requirement: "Every
long-running state must be inspectable." This is a live P0-era requirement, not a UI requirement —
it is satisfied through structured logs and API/data responses, not through a user interface.

Concretely, for this sprint's P0 scope (document 02 §7.1's simulation/validation pipeline, and any
process document 02 or document 08 characterizes as long-running — e.g. a strategy-compilation run,
a simulation batch, a validation/statistical-control pass):

- **Every long-running process must expose its current state through a queryable, structured
  channel** (a status field on its artifact/job record, a log line with a stable schema, or an API
  response) — not only through terminal stdout that disappears when the process exits.
- **State exposed this way must be sufficient to answer, without a UI:** is this process running,
  succeeded, failed, or stalled; what stage is it in; and what artifact (if any) did it produce or
  fail to produce.
- **This is an operator-facing requirement enforced through the same typed-contract discipline as
  everything else in P0** — it is not satisfied by an agent's own internal reasoning trace, and it
  is not satisfied by a human having to read source code to infer state. A human (or script) using
  only the CLI/API/logs must be able to drive and observe every P0 workflow, per document 02 §6's
  "zero agent involvement" requirement extended to the UI axis: zero *UI* involvement, not zero
  *inspectability*.

This requirement belongs to whichever component owns each long-running process (the Quant
Laboratory pipeline components named in document 02 §5) to implement as part of its own contract —
it is not a separate deliverable this stub introduces, and it is not itself a UI. It is named here
so it is not lost in the gap between "no UI yet" and "no visibility at all," which Matrix row 51
draws a clear line between and this document is not permitted to blur.

---

## 4. Explicit non-deliverables of this document

Per this sprint's Intake constraints and the "Always Redispatch"/no-fabrication discipline this
repo runs under, this document does **not** contain, and should not be read as implying:

- Screen inventories, wireframes, or layout diagrams (none exist to specify).
- User flows through any UI (none exist to specify).
- Component hierarchies or state-visibility tables in the UI sense (Step 6/7/8 of the standard
  `ui-specification` skill process do not apply — there is no architecture-defined UI component
  set for them to map to).
- Any implicit commitment to a specific future UI technology, framework, or visual design — those
  are out of scope for a future UI-spec sprint too, per this repo's "no visual aesthetics" and "no
  code" boundaries for the `@ui-spec-writer` role.

---

## 5. Traceability

| Claim | Source |
|---|---|
| UI is an operator surface over contracts; no UI-first implementation; inspectability required regardless | `docs/specs/00-source-inventory-reconciliation.md`, row 51 (line 117) |
| Agent & Orchestration / UI-facing work not on P0 critical path; zero-agent-involvement standard | `docs/specs/02-system-architecture.md` §6 |
| Building UI ahead of typed contracts rejected as anti-pattern; this document is a stub not a design | `docs/specs/02-system-architecture.md` §12 |
| P0 exit criteria and end-to-end path with no UI dependency | `docs/specs/02-system-architecture.md` §7.1, §7.3 |
| P0 numerical/semantic spine sequencing, no UI dependency | `docs/specs/08-implementation-roadmap.md` |
| Explicit-stub decision for this step | `docs/specs/signal-current-v1-spec/INTERVIEW.md`, seed question 5 |
| Constraint that `03-UI-SPEC.md` may be thin/non-applicable, stated explicitly rather than fabricated | `docs/specs/signal-current-v1-spec/INTAKE.md`, Constraints |
| Activate-after-core-is-stable criterion (analogy applied to UI trigger) | `docs/specs/00-source-inventory-reconciliation.md`, row 56; `docs/specs/02-system-architecture.md` §6 |
