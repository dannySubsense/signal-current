# Signal Current — Research Methodology Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, and `docs/specs/02-system-architecture.md`. Not yet
independently reviewed. Frozen only after the full eight-document set clears Frank's binding spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gap G10.

**Primary question this document answers:** How does an idea become a legitimate experiment?

**Traceability convention:** every substantive clause cites a Reconciliation Matrix row, a Constitution
clause, or an Architecture (`02-system-architecture.md`) section. Where this document generalizes beyond a
single directly-matching source, it is flagged inline as synthesis, not presented as a 1:1 citation,
consistent with the precedent set in Constitution §10 and Architecture §2. Design precedent drawn from
prior-art research is cited as REFERENCE only — illustrating a failure mode to guard against or a pattern
worth following — never as an adopted dependency or a criterion this document invents on its own authority.

## 1. Scope and non-goals

This document defines the workflow, gates, and typed-artifact obligations that govern the front half of
the canonical artifact spine (Architecture §3):

```
SourceSnapshot -> ResearchRecord/ResearchHypothesis -> ExperimentDefinition/CampaignSpec -> StrategyIR Candidate
```

It does NOT define:

- exact `SourceSnapshot`/`ResearchRecord`/`CampaignSpec`/`StrategyIR` field-level schema, serialization, or
  hashing (deferred to document 04, per Architecture U-01 and this sprint's Requirements doc);
- exact statistical thresholds for multiple-testing diagnostics, CPCV applicability, or search-budget
  numeric limits (deferred to document 05 — Validation & Statistical Controls);
- the agent permission matrix or which tool calls an agent may make at each stage (deferred to document 07);
- specific search-budget numbers, significance thresholds, or any other numeric constant (out of scope for
  this document entirely — see §9).

Where this document must reference an undecided item to describe a methodology boundary, it states the
contract the eventual decision must satisfy and points to the owning document, rather than inventing a
number or product choice.

## 2. The research workflow: source to hypothesis

### 2.1 Source acquisition and preservation

Any external source — an academic paper, blog post, GitHub repository, public strategy description, or
prior-art report — is acquired by the Ingestion Service and immediately becomes an immutable
`SourceSnapshot`: a capture of that source at one point in time, with capture metadata and hash where
possible. *(Matrix row 4, row 5; Architecture §3.1, §5 "Ingestion Service.")*

Nothing downstream — extraction, hypothesis formation, campaign design — may read or reason from a source
except through its `SourceSnapshot`. This is the methodology-level anchor for reproducibility: a claim
traced back only to "the paper" or "the repo" without a versioned snapshot reference is not yet a
`ResearchRecord`.

### 2.2 Extraction into ResearchRecord/ResearchHypothesis

The Extraction/Hypothesis Service reads one or more `SourceSnapshot`s and produces a `ResearchRecord` /
`ResearchHypothesis`: a provenance-carrying research claim that may hold ambiguity, citation, and alternate
interpretation, and that is explicitly not executable. *(Constitution §3.2; Matrix row 9; Architecture
§3.1.)*

A `ResearchRecord` is versioned, not overwritten — a superseding record creates a new version and the prior
version remains inspectable. *(Architecture §3.1.)*

### 2.3 What makes a hypothesis legitimate enough to promote

Per Matrix row 9 and Constitution §3.2, the distinction between `ResearchRecord`/`ResearchHypothesis` and
`StrategyIR` is the load-bearing boundary this whole section protects: research evidence is not execution
semantics, and promotion from one to the other is explicit and auditable. Concretely, a `ResearchRecord` is
eligible to become the basis of an `ExperimentDefinition`/`CampaignSpec` when:

1. it references at least one `SourceSnapshot` (§2.1) — a hypothesis with no snapshot reference is not
   research-grounded and cannot be promoted;
2. any ambiguity, conflicting rule, or missing information the source contains has been preserved
   explicitly per §3 below, not silently resolved;
3. it states which experiment dimensions (§5) it addresses and which it leaves open — a hypothesis about an
   entry signal that says nothing about exit, sizing, regime, or risk is legitimate as an entry-only
   hypothesis, provided it is scoped as such rather than silently assumed complete.

This is the full legitimacy gate this document specifies. It deliberately does not add a numeric
"confidence score" or "evidence strength" threshold — no such number is sourced in the Matrix, and
inventing one here would violate the no-fabricated-constants discipline this program follows (see this
repo's `CLAUDE.md`, Decision Discipline). If a future document needs a scored/ranked intake gate, that is
document 05's or document 07's job to source and justify, not this document's to guess.

### 2.4 Promotion into ExperimentDefinition/CampaignSpec

An `ExperimentDefinition`/`CampaignSpec` is a typed, replayable definition of a search/experiment: search
space, budget, seeds, generator configuration. *(Architecture §3.1; Matrix rows 3, 17, 19.)* Promotion from
`ResearchRecord` to `CampaignSpec` is itself a promotion-gate event per Architecture §3.2: it is a versioned
audit event, one-directional, and requires the evidence named in §2.3 above.

Once a `CampaignSpec` exists and an experiment run references it, the `CampaignSpec` becomes immutable
(Architecture §3.1) — later refinements to the underlying hypothesis create a new `CampaignSpec`, not a
silent edit of the one already in use, preserving the audit trail Constitution §9.1 requires.

## 3. Preserving source ambiguity (Matrix row 15)

Per Matrix row 15, a source's internal ambiguity, prose/code discrepancy, or apparently-impossible reported
statistic must never be silently "corrected." This methodology requires:

1. The Extraction/Hypothesis Service records every plausible interpretation it identifies as a distinct,
   versioned branch/assumption set within (or referenced from) the `ResearchRecord` — not a single
   "resolved" reading with the alternatives discarded.
2. Each such branch, if promoted, becomes its own `CampaignSpec`/`StrategyIR Candidate` lineage. Two
   candidates descending from the same ambiguous source are related-but-distinct lineage entries, not one
   candidate silently picking a winner at extraction time.
3. The original source's reported results (e.g., an author-claimed Sharpe ratio) are preserved as
   provenance metadata on the `ResearchRecord`. They are never treated as ground truth to reconcile the
   ambiguity against — see §6 (public-strategy-as-fixture) for why.

**Design precedent (illustrative, not adopted code):** TA-Lib's Wilder-vs-EMA RSI convention ambiguity
(`docs/research/candidate-reports/PA-02-talib.md`) is a concrete instance of exactly this failure mode at
the primitive level — an indicator with two legitimate conventions that a careless extraction would
silently pick one of. This methodology requires the ambiguity be preserved as a versioned branch (e.g., two
distinct primitive-parameterization candidates) rather than resolved by extractor judgment, consistent with
Matrix row 11's requirement (document 04's schema job) that `StrategyIR` primitives carry explicit semantic
roles rather than opaque, convention-ambiguous blobs.

## 4. Behavioral family vs. named strategy (Matrix row 10)

Signal Current researches **behavioral families and primitives**, not merely named strategies; a named
strategy is a source instance of some underlying behavioral family, not itself the unit of discovery.
*(Matrix row 10.)*

Consequences for this methodology:

1. When a `ResearchRecord` is extracted from a source describing a "named" strategy (e.g., a specific
   named breakout system), the Extraction/Hypothesis Service records both the named-strategy-as-source-
   instance metadata (author, name, source citation) and, separately, the underlying behavioral-family
   classification the strategy instantiates (e.g., "volatility-breakout entry conditioned on session
   range"), where that classification can be stated without over-claiming precision.
2. A `CampaignSpec` targeting a behavioral family that has already been explored under a different named
   source is not automatically a duplicate — but the campaign's provenance record must state the prior
   related lineage, so a reviewer (human or downstream diagnostic) can see that two `StrategyArtifact`s
   share a latent family even though they descend from differently-named sources. This is what makes
   Constitution §5.9's behavioral-diversification requirement (portfolio stage) possible later — it depends
   on family lineage being recorded here, at the research stage, not reconstructed after the fact.
3. "New discovery" is scoped to the family level for reporting/decision purposes: restating an already-
   researched behavioral family under a new named source is not treated as a novel finding, though it may
   still be a legitimate `CampaignSpec` (e.g., to test the family against a materially different market or
   timeframe context per Architecture §7.2's zero-semantic-privilege requirement).

## 5. Entry, exit, sizing, regime, and risk as independent experiment dimensions (Matrix row 12)

Entry, exit, sizing, regime, and risk logic are independently addressable experiment dimensions and
lineage components — they are not required to be researched or varied together. *(Matrix row 12.)*

This methodology requires:

1. A `ResearchRecord` may legitimately address only one dimension (e.g., an entry-signal hypothesis with no
   claim about exit or sizing). It must state explicitly which dimensions it addresses (§2.3 item 3) rather
   than leave the scope implicit.
2. A `CampaignSpec` may hold one or more dimensions fixed (inherited from a prior `StrategyArtifact` or a
   fixture, per §6) while varying only the dimension(s) under test. The `CampaignSpec` records which
   dimensions are fixed-and-inherited versus actively searched, so that a resulting `StrategyIR Candidate`'s
   lineage is traceable to which specific dimension produced any observed effect.
3. This independence is a methodology-level requirement on how experiments are scoped and recorded; the
   exact typed representation of "entry primitive," "exit primitive," etc. within `StrategyIR` is document
   04's schema job (Matrix row 11, tagged "Yes — exact schema/hashing" as unresolved).

## 6. Regime conditioning: evidence boundary (Matrix row 13)

Regime definitions and regime-detection models are first-class experimental artifacts, and must obey the
same evidence boundary and leakage rules as strategies themselves. *(Matrix row 13.)*

Concretely:

1. A regime definition (e.g., a volatility-state classifier, a session-based regime split) cannot be
   discovered or fit using confirmation-zone or lockbox-zone data (Matrix row 26; Architecture §4). A regime model
   developed by inspecting data that a `StrategyIR Candidate` will later be validated or sealed against is
   leaked evidence, exactly as if the strategy's parameters themselves had been fit on that data.
2. A regime definition, once used as a conditioning input to a `CampaignSpec` or `ValidationPlan`, is
   version-tracked with the same rigor as a `StrategyIR Candidate`: which data zone it was fit on, what
   `SourceSnapshot`/`ResearchRecord` motivated it, and which experiments consumed it.
3. Regime-conditioned research is not exempt from §3's ambiguity-preservation requirement: if a source
   proposes a regime split ambiguously (e.g., "high volatility" without a precise definition), that
   ambiguity is preserved as a versioned branch, not silently resolved into one arbitrary threshold — and no
   such threshold is invented in this document (see §9).

## 7. Public strategies as engineering fixtures, never performance targets (Matrix row 14)

Public/named-strategy sources are engineering fixtures and hypotheses, not performance targets. Reported
author results are provenance only, never a target to reproduce. *(Matrix row 14.)*

This methodology requires:

1. A `ResearchRecord` extracted from a public strategy source records the author's reported performance
   figures (Sharpe, CAGR, drawdown, etc.) purely as citation/provenance metadata — the same field class as
   "source URL" or "publication date," not as a target field any later validation gate checks against.
2. No promotion gate in the spine (Architecture §3.2) may use "does the `StrategyArtifact` match or exceed
   the source's reported performance" as a pass criterion. The only performance evidence that counts is
   what Signal Current's own deterministic engine and Validation & Statistical Controls Service produce
   (Constitution §1, §8).
3. Public strategies are useful specifically as parity/plumbing fixtures — proving the pipeline can
   ingest, extract, and compile a known structure into valid `StrategyIR` — and as a source of behavioral-
   family hypotheses (§4), not as a scoreboard.

**Design precedent (illustrative, cautionary, not adopted method):** the Time Series Momentum critique and
the Valeyre (2022) re-examination (`docs/research/candidate-reports/PA-02-time-series-momentum.md`,
`docs/research/candidate-reports/PA-02-valeyre-2022.md`) both show a headline backtest result that did not
survive independent statistical re-testing once bootstrap resampling and a vol-scaling-overlay confound
were accounted for. This is the concrete failure mode item 2 above exists to block: treating a source's
reported number as the bar to clear, rather than treating the source purely as provenance and letting
Signal Current's own Validation Service (document 05) independently determine whether any edge survives
scrutiny.

## 8. Search and generation are pluggable (Matrix row 17); LLM-generated signals are proposal-only (Matrix row 18)

### 8.1 Pluggable generators

Every generator — manual, GA, evolutionary, Bayesian/surrogate, or agent-proposed — must emit valid
`StrategyIR` candidates through the identical downstream deterministic simulation/validation path.
*(Matrix row 17; Architecture §5 "Campaign/Generator Service," §12 pattern table.)* No generator is
architecturally privileged: the methodology does not treat a GA-produced candidate differently from a
manually-authored one once both are valid `StrategyIR`. What differs between generators is recorded in the
`CampaignSpec` (generator identity/configuration, per Architecture §3.1) so that search-budget accounting
(§9 below) can attribute candidates to the generator that produced them.

### 8.2 LLM-generated signals: proposal boundary

LLM output is always an unvalidated proposal artifact. It receives no evidentiary weight until compiled to
valid IR and evaluated by deterministic tools. *(Matrix row 18; Constitution §6, §7.3.)* At the methodology
level this means:

1. An LLM (whether generating a hypothesis, a candidate `StrategyIR` sketch, or a regime definition
   proposal) is treated exactly as one more generator input under §8.1 — its output must be compiled into
   valid `StrategyIR` and pass through the same `CampaignSpec` → `SimulationRun` → `ValidationArtifact` path
   as any other generator's output before it carries any evidentiary weight.
2. An LLM's own stated confidence, reasoning, or explanation is never itself evidence and is never recorded
   as if it were a validation result. It may be recorded as provenance/rationale metadata on the
   `ResearchRecord` or `CampaignSpec`, exactly as a human researcher's rationale would be.
3. No promotion gate treats "an agent/LLM recommended this" as sufficient authorization for any
   material-risk-bearing action — that authority boundary is Constitution §6 and Architecture §3.2 item 4,
   not restated differently here.

**Design precedent — legitimate pattern to take, rejected pattern to avoid (both REFERENCE only):**
TradingAgents' (`docs/research/candidate-reports/PA-08-tradingagents.md`) role-decomposition — an
analyst proposing a claim, then a bull/bear researcher pair adversarially examining it before a trader
role acts — is a legitimate structural pattern this methodology's own "hypothesis → adversarial
examination via ambiguity-preservation (§3) and independent-dimension scoping (§5) → promotion gate (§2.4)"
sequence is consistent with, and may inform how an eventual Agent Tool Layer (Architecture §6, deferred to
document 07) structures multi-step hypothesis review. TradingAgents' Portfolio-Manager-as-LLM-approval-gate
is explicitly rejected: it would let an LLM serve as the human-authorization step Constitution §6 requires,
which this methodology (and Architecture §3.2 item 4, §12 anti-patterns) does not permit at any stage,
including the ResearchRecord→CampaignSpec promotion gate defined in §2.4.

## 9. Search-budget and hypothesis-lineage recording (Matrix row 19; Constitution §5 item 5)

Every campaign records search space, budget, seeds, candidate count, rejection count, selection process,
and hypothesis lineage. *(Matrix row 19; Constitution §5 item 5.)* This is what makes the multiple-testing
diagnostics document 05 defines in detail possible — that document specifies how the diagnostics are
computed and what thresholds apply; this document specifies what data the research/campaign stage must
produce so those diagnostics have something to operate on.

Concretely, an `ExperimentDefinition`/`CampaignSpec` (Architecture §3.1) must record, for methodology
purposes:

1. **Search space** — the bounded region of `StrategyIR` composition the campaign is permitted to explore
   (which primitives, which dimensions per §5, which parameter ranges), traceable back to the
   `ResearchRecord`(s) that motivated the scope.
2. **Budget** — the maximum number of candidates/trials/generations the campaign is authorized to run.
   (The specific numeric ceiling for any given campaign is a per-campaign operational parameter set by
   whoever authorizes the campaign, not a global constant this document invents.)
3. **Seeds** — for any seedable-stochastic generator (Constitution §7.2), the seed(s) used, satisfying the
   deterministic-replay/seedable-stochastic contract already fixed in Architecture §9.
4. **Candidate count and rejection count** — how many `StrategyIR Candidate`s the campaign actually
   produced, and how many were rejected before reaching `ValidationArtifact`, and at what stage (e.g.,
   failed to compile to valid IR, failed a pre-simulation sanity check, rejected by an intermediate
   fitness/surrogate filter).
5. **Selection process** — the explicit rule or model (if any) used to decide which candidates advance
   from a large search to full simulation/validation, recorded machine-readably, not just as prose.
6. **Hypothesis lineage** — the chain from originating `SourceSnapshot`/`ResearchRecord` through
   `CampaignSpec` to each resulting `StrategyIR Candidate`, including which behavioral family (§4) and
   which experiment dimension(s) (§5) each candidate addresses.

No specific numeric search-budget ceiling, candidate-count threshold, or rejection-rate threshold is
defined in this document. Per this sprint's explicit constraint and this repo's no-fabricated-constants
discipline, any such number belongs to document 05 (Validation & Statistical Controls), where it can be
justified against a stated statistical method (e.g., a specific multiple-testing correction), not invented
here as an unsourced default.

## 10. Promotion gate: ResearchHypothesis to ExperimentDefinition/CampaignSpec

Synthesizing §2.3, §2.4, §3-§9 above into a single checklist, a `ResearchRecord`/`ResearchHypothesis` is
promotable to an `ExperimentDefinition`/`CampaignSpec` when all of the following hold. This checklist is a
direct application of Matrix row 9 and Architecture §3.2's general promotion-gate contract to this specific
transition — it does not introduce a new gate mechanism beyond what Architecture §3.2 already establishes.

1. It references at least one `SourceSnapshot` (§2.1).
2. Any source ambiguity has been preserved as explicit versioned branches, not silently resolved (§3).
3. Its behavioral-family classification is recorded, distinct from any named-strategy source label (§4).
4. It states which experiment dimensions (entry/exit/sizing/regime/risk) it addresses and which are
   inherited/fixed from elsewhere (§5).
5. If it involves regime conditioning, the regime definition's data-zone provenance is recorded and
   does not draw on confirmation/lockbox-zone data (§6).
6. If it derives from a public/named-strategy source, reported performance figures are recorded as
   provenance metadata only, never as a target field (§7).
7. If any part of the hypothesis or candidate-generation step involved an LLM, that output is marked as
   proposal-only pending compilation to valid `StrategyIR` and deterministic evaluation (§8.2).
8. The resulting `CampaignSpec` is prepared to record search space, budget, seeds, candidate/rejection
   counts, selection process, and hypothesis lineage from the moment the campaign begins running (§9) —
   this is a readiness requirement on the `CampaignSpec`'s design, not a claim that data exists before any
   candidate has run.

This transition is, per Architecture §3.2, a versioned audit event, one-directional, and requires the
evidence named above; it does not additionally require human authorization (Constitution §6's human-
authorization requirement is scoped to material-risk-bearing promotions, i.e., later gates in the spine —
this transition produces only a bounded, not-yet-evaluated search definition, carrying no risk exposure).

Exact per-gate acceptance thresholds beyond this checklist (e.g., how many ambiguity branches are "too
many," what counts as a sufficiently precise regime definition) are not specified here — no such threshold
is sourced in the Matrix, and this document does not fabricate one. Where a future document needs such a
threshold, it must source or provisionally-tag it there, following the Constitution's own discipline.

## 11. Consistency check against Constitution and Architecture

This document was checked for contradiction against `01-constitution.md` and `02-system-architecture.md`
in full. No conflict was found: every clause above cites a Matrix row, Constitution clause, or Architecture
section already establishing the underlying rule; this document only elaborates the research-stage workflow
those rules imply. No HALT condition applies.

## 12. PROVISIONAL items and resolution paths

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| Exact `SourceSnapshot`/`ResearchRecord`/`CampaignSpec`/`StrategyIR` schema fields | Deferred, not PROVISIONAL — document 04's scope | Document 04 owner | Resolved when document 04 (Data Architecture & Strategy IR) is drafted |
| Numeric search-budget ceilings, rejection-rate thresholds, multiple-testing diagnostic defaults | Deferred, not PROVISIONAL — document 05's scope | Document 05 owner | Resolved when document 05 (Validation & Statistical Controls) is drafted, sourced against a named statistical method |
| Regime-definition precision/threshold criteria | Deferred, not PROVISIONAL — document 05's scope | Document 05 owner | Resolved alongside CPCV/regime applicability matrix (Matrix row 28, U-06) |
| Agent tool-call permissions at each stage of this workflow | Deferred, not PROVISIONAL — document 07's scope | Document 07 owner | Resolved when document 07 (Agent & Orchestration Layer) is drafted, per Architecture §6 |

No numeric constant (search-budget size, ambiguity-branch limit, rejection-rate threshold, regime-precision
cutoff) is introduced anywhere in this document. Each is either explicitly deferred to a later document per
this sprint's constraint, or is a per-campaign operational parameter set at authorization time rather than
a global constant this specification would fix.

## 13. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Data/StrategyIR,
Validation, Portfolio/Deployment, and Agent specification work (Constitution §11). After freeze, amendment
requires an explicit ADR and version change.
