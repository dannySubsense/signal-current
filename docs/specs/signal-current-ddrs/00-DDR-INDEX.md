# DDR Index — Signal Current

**A DDR (Decision Record) states what was decided, when, and why — nothing else.** It is not a
feature spec, not a status tracker, and not a log of downstream sprint outcomes. Once a row is
ACCEPTED, its text does not track what happens next — deployment status, rollout progress, and
downstream hardening work belong in that sprint's own `PROGRESS.md`, which a row may link to but
must never restate or update inline. Test before adding to a row: if the sentence will need
updating again later as facts change, it does not belong here — that's a `PROGRESS.md` fact, not
a decision. (Full definition and lifecycle: `agent-rig/docs/specs/agent-rig-ddrs/DDR-DEFINITION.md`,
if accessible from this project; otherwise this paragraph is the authoritative summary.)

| # | Title | Status |
|---|-------|--------|
| 001 | [Decision Matrix and Cold Frank Dispatch Protocol](DDR-001-decision-matrix-and-cold-frank.md) | ACCEPTED |

## Reference documents (not DDRs — no decision to accept, nothing to number)

- [Cold Gates: Two Mandatory Requirements, Not One](COLD-GATES.md) — the isolated-detached-checkout
  amendment to DDR-001 §2.2's Cold Frank protocol, with the incident that surfaced the gap. Read
  alongside DDR-001 §2.2, not instead of it.
