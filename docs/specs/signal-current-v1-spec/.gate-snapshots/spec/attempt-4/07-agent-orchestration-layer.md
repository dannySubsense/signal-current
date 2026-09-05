# Signal Current — Agent & Orchestration Layer Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, `docs/specs/02-system-architecture.md`,
`docs/specs/03-research-methodology.md`, and `docs/specs/06-portfolio-deployment-monitoring.md`. Not yet
independently reviewed. Frozen only after the full eight-document set clears Frank's binding spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gap G7. @architect, 2026-09-05, per
Frank's spec-gate attempt-3 finding F2 — reconciled §3.1 item 3/§3.2's "agent-run identity and iteration
index" vocabulary with document 04 §5.4's `orchestrationSessionId` (every agent run carries the session id
of whatever spawned it; runs from one orchestrator in one session share it), added document 04 to §11's
consistency-check list, and re-ran that check.

**Primary question this document answers:** What may agents do, with which tools, under which permissions
and gates?

**Traceability convention:** every substantive clause cites a Reconciliation Matrix row, a Constitution
clause, or an Architecture/Research-Methodology/Portfolio-Deployment section. Where this document
generalizes beyond a single directly-matching source, it is flagged inline as synthesis, consistent with
the precedent in Constitution §10 and Architecture §2. Design precedent from prior-art research is cited
REFERENCE-only, never as an adopted dependency or framework choice.

## 0. This layer is not on the P0 critical path

Per Architecture §2 domain table ("Agent & Orchestration — Explicitly not P0") and §6 ("Agent & Orchestration
layer is not on the P0 critical path"), and per Matrix row 56 (U-11: "Agent harness is not required for P0
numerical spine. Activate after core APIs/artifacts/gates are stable enough to constrain agents"): this
document defines what agents may and may not do **once, and if, the activation gate in §5 is satisfied**. It
does not argue for accelerating agent activation, does not restate any urgency to build this layer early,
and does not itself activate any capability. Sequencing this layer relative to P0/P1 work is document 08's
(Implementation Roadmap's) job, not this document's. Every P0 component named in Architecture §5 must
function with zero agent involvement (Architecture §6) — nothing in this document creates a dependency on
agent existence for any P0 or P1 numerical-spine capability.

## 1. Scope and non-goals

This document defines the operational contract governing any future agent or LLM-driven orchestration
component interacting with Signal Current. It does NOT define:

- a specific agent framework, SDK, or orchestration product choice — that is an implementation decision, not
  a contract this document fixes;
- a specific token/cost/compute budget number for agent operations — any such number is an unsourced
  constant per this repo's `CLAUDE.md` Research Data Integrity rules and is not fabricated here;
- an exact agent-activation readiness scorecard or checklist — PROVISIONAL, §7.3/§12;
- an exact per-role permission matrix (which named role gets which named tool) — PROVISIONAL, §7.3/§12;
- educational/copilot agent UX design — noted only as a separate future capability, §6.

Where this document must reference an undecided item, it states the contract the eventual decision must
satisfy and tags the item PROVISIONAL with a named owner and resolution condition (§7), per the discipline
already established in documents 02/06.

## 2. Agent authority boundary in full operational detail

This section operationalizes Constitution §6 and Matrix row 2 ("Agents are operators over typed tools and
artifacts") in full detail, without restating it as a summary.

### 2.1 The only interface agents operate through

Per Matrix row 2 and Architecture §5/§8: an agent's sole interface to Signal Current is a **typed tool call**
against a component named in Architecture §5 (Ingestion Service, Extraction/Hypothesis Service, Campaign/
Generator Service, Data Access Gateway, Deterministic Simulation Engine, Validation & Statistical Controls
Service, Promotion/Gate Service, Portfolio Service, Build/Conformance Service, Incubation/Risk-Graduation
Service, Telemetry/Health Service, Audit/Event Log). There is no other path.

Concretely:

1. No agent has direct database, filesystem, or object-store mutation access to any canonical record
   (`SourceSnapshot`, `ResearchRecord`, `CampaignSpec`, `StrategyIR Candidate`, `SimulationRun`,
   `ValidationArtifact`, `StrategyArtifact`, `PortfolioArtifact`, `BuildArtifact`, `DeploymentArtifact`,
   `LiveObservationSet`, `HealthAssessment`, `DecisionRecord`/`ResearchTrigger` — Architecture §3.1) outside a
   typed tool call. This generalizes Architecture §2's "domains communicate only through typed artifact
   contracts... never through shared mutable state" to agents specifically, since an agent is not itself a
   bounded domain but an operator that must go through the same contracts a domain's own internal callers do
   — flagged here as an explicit synthesis, consistent with the precedent Architecture §2 itself sets for
   generalizing Matrix row 3.
2. A typed tool call is, at minimum, as constrained as the underlying component's own API contract (document
   04's schema job fixes the exact tool/RPC signatures once `StrategyIR` is fixed per U-01; this document
   fixes only that no tool call may exceed what the component's own typed contract already permits a
   non-agent caller to do — an agent gets no privileged shortcut a human-driven script would not also have
   through the same API).
3. Agent-authored artifacts (e.g., an LLM-proposed `ResearchHypothesis` sketch, an agent-proposed
   `CampaignSpec`) are created only by calling the same promotion-gate-respecting tool a human researcher or
   script would call — never by writing directly to a canonical store.

### 2.2 What agents MAY do (Matrix row 2, Matrix §3 thesis)

Per the Reconciliation Matrix's Proposed Canonical System Thesis (§3): agents may **discover, extract,
propose, organize, schedule, compare, explain, recommend**. Concretely, through typed tools, an agent may:

- discover and summarize `SourceSnapshot`/`ResearchRecord` content (read access via Research Intelligence
  tools);
- propose new `ResearchHypothesis` drafts and `CampaignSpec` drafts, subject to the same promotion-gate
  checklist Research Methodology §10 already fixes for any proposer (human or agent) — an agent proposal is
  evaluated against the identical gate, never a relaxed one;
- plan and schedule experiment execution (invoke the Campaign/Generator Service to run an already-authorized
  `CampaignSpec`, within the budget that `CampaignSpec` itself records per Research Methodology §9);
- compare `ValidationArtifact`s, `StrategyArtifact` passports, or `PortfolioPlan` evidence across candidates
  and summarize the comparison for a human reviewer;
- explain a result — e.g., narrate why a `ConformanceResult` discrepancy occurred, using the
  `DiscrepancyRecord.explanation` field (Portfolio/Deployment §9.1) as input, not as something the agent
  invents in place of the recorded explanation;
- recommend a next action (e.g., "this candidate is ready for human authorization review") — a recommendation
  is not an approval and carries no evidentiary or authorization weight (§2.3, §4).

### 2.3 What agents MAY NOT do (Constitution §6, restated operationally)

Per Constitution §6, restated here with the specific mechanism each prohibition maps to:

1. **Invent or mutate numerical evidence.** No agent tool call may write a `SimulationRun`, `ValidationArtifact`,
   or any field of a `StrategyArtifact` passport's evidentiary content by direct assertion — those artifacts
   are produced exclusively by the Deterministic Simulation Engine and Validation & Statistical Controls
   Service (Architecture §5), never by an agent asserting a number. An agent may request that the Deterministic
   Simulation Engine run a specific `StrategyIR Candidate`; it may never populate the resulting `SimulationRun`'s
   metrics itself.
2. **Alter immutable evidence.** No tool exists, or may be built, that lets an agent edit a `SourceSnapshot`,
   `StrategyIR Candidate`, `SimulationRun`, `ValidationArtifact`, or `StrategyArtifact` after creation — this
   mirrors the immutability column Architecture §3.1 already fixes for every artifact; an agent has no
   exception to it.
3. **Bypass validation or risk gates.** An agent may not call a tool that skips a required promotion-gate step
   named in Architecture §3.2 (e.g., requesting `StrategyArtifact` promotion for a candidate whose
   `ValidationPlan.status` has not reached `promotion-gated`, per Portfolio/Deployment §2, is a malformed tool
   call the Promotion/Gate Service must reject regardless of caller identity).
4. **Access sealed lockbox data through an unauthorized path.** The Data Access Gateway (Architecture §4) is
   the sole mediated path to market/feature data for every caller, agent or otherwise; an agent's tool
   permissions (§4 below) are scoped to the exploration/validation zones exactly as any other caller's would
   be, and lockbox access follows the identical audited-event contract Architecture §4.1 already fixes — an
   agent role gets no separate or relaxed lockbox-access path.
5. **Promote a risk-bearing artifact outside the defined human-authorization path.** Per Constitution §6 and
   Portfolio/Deployment §7 (`HumanAuthorizationRecord`, §7.2-7.3): no agent tool call ever populates
   `HumanAuthorizationRecord.authorizingHumanId`, and no agent output — however many review/debate stages
   produced it — ever itself constitutes the `HumanAuthorizationRecord` event. This restates Portfolio/
   Deployment §7.3's prohibition from the agent-permission side, per this sprint's instruction: an agent role
   must never be architected to constitute the human-authorization gate itself, no matter how the role is
   named (e.g., a "Portfolio Manager," "Risk Approver," or "Final Reviewer" agent role) or how much adversarial
   review feeds into it. This is Constitution-locked (Constitution §6; Portfolio/Deployment §7.1), not
   provisional.

## 3. Recursive research budgeting (Matrix row 8)

Per Matrix row 8: "Recursive research must be budgeted, logged, provenance-aware, and promotion-gated. It
may create ResearchHypothesis/CampaignSpec drafts, never validation outcomes." This section adds the
agent-specific budget-enforcement mechanism this Matrix row implies but Research Methodology §9 does not
itself specify (Research Methodology §9 fixes what a `CampaignSpec` must record about search-space/budget/
seeds/lineage; this section fixes what happens when an *autonomous agent run* — as opposed to a single
human-authorized campaign invocation — is the actor producing those drafts).

### 3.1 What "budgeted" requires for an agent run specifically

An autonomous or recursive agent run (i.e., an agent-driven loop that proposes, and potentially chains,
multiple `ResearchHypothesis`/`CampaignSpec` drafts without a human re-authorizing each individual step) must
record, in addition to Research Methodology §9's per-campaign requirements:

1. **A declared run-level budget ceiling** — a maximum number of proposal/tool-call iterations the run is
   authorized to execute before it must halt and return control to a human reviewer. The specific numeric
   ceiling is a per-run operational parameter set by whoever authorizes the run, not a global constant this
   document invents (consistent with Research Methodology §9's own treatment of per-campaign budget numbers).
2. **A hard halt, not a soft continuation, at the ceiling.** When an agent run reaches its declared budget
   ceiling, the run MUST halt and surface its state to a human reviewer — it must not silently continue,
   renegotiate its own budget, or request a larger budget from within the same unreviewed run. This is the
   agent-specific enforcement mechanism this section adds: Research Methodology §9 records budgets as data;
   this section requires the budget be a runtime-enforced stop condition for agent-driven (not
   human-driven-step-by-step) execution specifically, because an unbounded agent loop is the concrete risk
   Matrix row 8 names ("Unbounded agents risk runaway search, contamination, or self-confirmation").
3. **Provenance-awareness at the run level, not just the campaign level.** Every `ResearchHypothesis`/
   `CampaignSpec` draft an agent run produces carries a reference to the agent-run identity and iteration
   index that produced it, in addition to the `SourceSnapshot`/`ResearchRecord` provenance chain Research
   Methodology §2.1/§9 already requires — so a reviewer can distinguish "this campaign was proposed by
   iteration 4 of autonomous run X" from "this campaign was manually authored," without that distinction
   changing which promotion-gate checklist applies (§2.2 above: the same checklist applies regardless of
   proposer). **Reconciliation with document 04 §5.4's `AuditActor.orchestrationSessionId`:** the "agent-run
   identity" this item requires IS carried as that `orchestrationSessionId` — every agent run carries the
   `orchestrationSessionId` of the orchestration session that spawned it, per document 04 §5.4. Multiple runs
   spawned by one orchestrator within one session share the same `orchestrationSessionId`, regardless of how
   many different agent roles/personas are invoked within that session. This is what closes the "two agent
   roles, one orchestrator, one session = one well" loophole (Matrix row 59 / Constitution §3 item 7's
   independent-reproduction requirement, document 05 §2.1): role-name inequality within one session is not
   identity inequality, because both roles share one `orchestrationSessionId`. "Iteration index," by
   contrast, is this section's own addition beyond `orchestrationSessionId` — it distinguishes individual
   iterations within a single session/run, a finer grain than session identity, and is not itself part of
   document 04 §5.4's contract.
4. **Promotion-gated, never validation-outcome-producing.** Consistent with Matrix row 8's explicit
   limitation: an agent run's output is bounded to `ResearchHypothesis`/`CampaignSpec` drafts. It may request
   that an already-authorized `CampaignSpec` be executed (§2.2), but it may never itself produce a
   `ValidationArtifact`, `StrategyArtifact`, or any later-stage artifact by assertion — those remain
   exclusively the output of the Deterministic Simulation Engine, Validation & Statistical Controls Service,
   and Promotion/Gate Service (§2.3 item 1).

### 3.2 Logging

Every iteration of an agent run, and every tool call it makes, is a versioned audit event with actor
(the specific agent-run identity, not a generic "agent" label), correlation/causation ID, and artifact
references, per Constitution §9.1 and Matrix row 53 — no exception for agent-originated events; they use the
same Audit/Event Log component (Architecture §5) every other actor's events use. Per §3.1 item 3's
reconciliation above, that actor's `orchestrationSessionId` (document 04 §5.4) is the session that spawned
the run recording the event — never self-assigned by the agent run or its orchestrator, per document 04
§5.4's assignment rule (Agent Tool Layer/harness, at session start).

## 4. LLM output classification (Matrix row 18, Constitution §7 tier 3)

Per Matrix row 18 and Constitution §7.3: LLM output is always an unvalidated proposal artifact. It receives
no evidentiary weight until compiled to valid IR and evaluated by deterministic tools. This document states
plainly, for the agent layer specifically:

1. This classification applies **uniformly regardless of how sophisticated the agent architecture producing
   the output is.** A single-prompt LLM call and a multi-agent adversarial-debate pipeline (analyst proposes,
   bull/bear researchers examine, trader role acts — the TradingAgents role-decomposition pattern, cited as a
   legitimate *structural* pattern for organizing agent review in Research Methodology §8.2) receive the
   identical evidentiary treatment: zero evidentiary weight until the output is compiled to valid `StrategyIR`
   and passes through the Deterministic Simulation Engine and Validation & Statistical Controls Service
   (Architecture §5). No amount of internal agent review substitutes for, or discounts the need for,
   deterministic evaluation.
2. **Concrete illustration, cited directly:** TradingAgents' own project documentation
   (`docs/research/candidate-reports/PA-08-tradingagents.md`) candidly states that its results are not
   reproducible run-to-run, due to non-deterministic LLM sampling and live data drift. This is cited here as
   the concrete illustration of why Constitution §7's tier-3 (inherently non-replayable) classification
   applies to agent output categorically, not just to edge cases: a well-engineered multi-agent debate
   process does not, and structurally cannot, convert non-deterministic LLM inference into replayable
   evidence, because — per Constitution §7.3 — there is no fixed input tuple to replay against for live LLM
   inference. This is the same reasoning Architecture §9 item 3 already fixes at the numerical-core level;
   this document applies it to the agent layer specifically, since the agent layer is where LLM inference
   actually occurs in this system.
3. An agent's own stated confidence, reasoning chain, or explanation (Research Methodology §8.2 item 2) is
   never itself evidence and is never recorded as if it were a validation result — it may be recorded as
   provenance/rationale metadata, exactly as a human researcher's rationale would be, but it never populates
   any field a promotion gate reads as pass/fail evidence.
4. No promotion gate anywhere in the spine (Architecture §3.2) may treat "an agent/LLM recommended this," at
   any confidence level, as sufficient to satisfy that gate's required evidence — this restates Research
   Methodology §8.2 item 3 and Portfolio/Deployment §7.3/§11 from the agent-permission side, without
   introducing a new gate mechanism.

## 5. Tool-permission model (concept only — no enforcement technology chosen)

This section defines the **concept** of per-agent-role tool scoping. It does not choose a technical
permission-enforcement mechanism (e.g., a specific capability-token system, RBAC product, or sandboxing
technology) — that is an implementation decision deferred to whichever system builds the Agent Tool Layer
(Architecture §5), once activated (§6 below).

### 5.1 Role-category-to-tool-category scoping concept

Per Matrix row 2's "operators over typed tools" framing, and generalizing Architecture §5's component
boundary discipline to agent roles: an agent role is defined by **which category of tool it is scoped to
call**, not by what it is named. At minimum, this document distinguishes the following tool-access
categories a future permission matrix (§7, PROVISIONAL) must assign roles into:

| Tool-access category | Example components (Architecture §5) | Example permitted actions |
|---|---|---|
| Discovery / read-only | Discovery Index, Audit/Event Log (read) | Search, summarize, compare existing canonical records |
| Proposal-authoring | Extraction/Hypothesis Service, Campaign/Generator Service | Draft `ResearchHypothesis`/`CampaignSpec` records, subject to the promotion-gate checklist (§2.2) |
| Execution-requesting (bounded) | Deterministic Simulation Engine, Validation & Statistical Controls Service | Request execution of an already-authorized `CampaignSpec`/`StrategyIR Candidate` within its recorded budget (§3) — never invent or edit the resulting evidence |
| Evidence-comparison / explanation | Promotion/Gate Service (read), Portfolio Service (read), Telemetry/Health Service (read) | Compare `ValidationArtifact`s, `StrategyArtifact` passports, `PortfolioPlan`s, `HealthAssessment`s and narrate differences |
| **Never granted to any agent role** | Promotion/Gate Service (write path that mints `StrategyArtifact`/`PortfolioArtifact`/`BuildArtifact`/`DeploymentArtifact`), any tool that could populate `HumanAuthorizationRecord.authorizingHumanId` | N/A — structurally excluded, per §2.3 item 5 and Portfolio/Deployment §7.3 |

1. A given agent role is granted one or more of the first four categories, scoped to the minimum needed for
   its function — a research-proposing agent role gets discovery and proposal-authoring access; it never
   also gets a tool that can directly mint or promote a `StrategyArtifact`, `PortfolioArtifact`, or
   `DeploymentArtifact`, and it never gets any tool capable of writing a `HumanAuthorizationRecord`. This is
   the concept this document fixes: **no agent role's tool grant may span from a proposal-authoring category
   into the promotion/authorization category** — that boundary is structural, not a configuration choice a
   future permission matrix could relax.
2. The exact technical mechanism enforcing this scoping (capability tokens, per-tool ACL, a broker service
   checking role membership, etc.) is an implementation decision, not fixed here.

## 6. Educational/copilot agent modes are a separate, non-privileged future capability (Matrix row 52)

Per Matrix row 52: "Preserve as a future UX capability, separate from the privileged orchestration agent
layer. It receives read/limited action permissions through the same tool contracts." This document states:

1. Tutor/Navigator/Debugger-style educational or copilot agent modes are explicitly **not part of** the
   privileged orchestration agent layer this document otherwise specifies (§2-§5). They are a distinct future
   UX capability, out of this document's build scope and out of P0/P1 scope by the same Architecture §2/§6
   reasoning that excludes the orchestration layer generally.
2. When such a mode is eventually built, it receives only read/limited-action permissions through the
   **same** typed-tool contracts (§2.1, §5) any other agent role uses — it is not a special exception, does
   not get a separate access path to canonical data or the Data Access Gateway, and is bound by the identical
   prohibitions in §2.3 and the identical LLM-output classification in §4. Its "educational" framing does not
   loosen any constraint this document fixes for agents generally.

## 7. Agent activation gate (Matrix row 56, U-11)

### 7.1 Restated negative constraint (from Architecture §6)

The agent harness is **not required for P0**. It activates only after core APIs, artifacts, and gates are
stable enough to constrain agents (Matrix row 56). This document does not, and this sprint's instruction
explicitly directs it not to, argue for moving activation earlier. Sequencing whether/when activation happens
relative to P0/P1/later roadmap phases is document 08's (Implementation Roadmap's) job.

### 7.2 The structural contract "stable enough to constrain agents" must satisfy

Without inventing a specific readiness checklist or scorecard, this document fixes the structural
precondition any future activation decision must verify:

1. **Typed tool contracts must exist and be gate-enforced before any agent is given access to them.**
   Concretely: every component named in Architecture §5 that a prospective agent role would call (per the
   tool-access categories in §5.1) must already have its typed API contract defined (documents 04/05/06's
   schemas — `StrategyIR`, `CampaignSpec`, `ValidationPlan`, `PortfolioPlan`, `HumanAuthorizationRecord`, etc.)
   and its promotion gates (Architecture §3.2) already enforced for non-agent callers, before that same
   contract is opened to an agent caller. An agent must never be the first caller to exercise a tool contract
   whose gate-enforcement is unproven against a human or script caller.
2. **The prohibitions in §2.3 and the human-authorization boundary in §2.3 item 5 must already be
   structurally enforceable by the Promotion/Gate Service (Architecture §5) and the `HumanAuthorizationRecord`
   contract (Portfolio/Deployment §7.2) before any agent role is granted execution-requesting or
   proposal-authoring access (§5.1)** — i.e., the human-authorization gate must already exist and already be
   exercised by non-agent callers, not be built concurrently with, or after, agent access being granted.
3. This is a structural readiness contract, not a scorecard with named numeric thresholds (e.g., "N months of
   stable operation" or "N successful human-authorized promotions") — any such specific number is
   PROVISIONAL and explicitly not invented here (§7.3).

### 7.3 Exact activation criteria and permission matrix: PROVISIONAL

**PROVISIONAL — unvalidated, owner: Danny.** Resolution condition: defined once documents 04/05/06's typed
contracts are implementation-stable enough to constrain against, per Matrix row 56's own sequencing logic —
this is inherently a post-P0 decision, not a spec-time one. The exact readiness checklist/scorecard
satisfying §7.2's structural contract, and the exact per-role permission matrix populating §5.1's category
table with named roles, are both deferred under this same PROVISIONAL tag; neither is guessed here.

## 8. This document's restatement: no capability is activated by this document

This document defines what is permitted once, and if, the activation gate (§7) is satisfied. It does not
itself activate any agent capability, grant any tool access, or authorize building the Agent Tool Layer
(Architecture §5). Sequencing that decision within the broader build plan is document 08's (Implementation
Roadmap's) job, consistent with Architecture §6's own framing and this sprint's explicit scope instruction.

## 9. Patterns and anti-patterns

| Pattern | Where used | Rationale / anchor |
|---|---|---|
| Typed-tool-call-only interface, no direct mutation path | §2.1 | Matrix row 2; Architecture §2, §8 |
| Identical promotion-gate checklist regardless of proposer identity (human, script, or agent) | §2.2, §4 item 4 | Research Methodology §8.2, §10; Matrix row 18 |
| Hard run-level budget ceiling with mandatory halt, not soft continuation | §3.1 | Matrix row 8 |
| Uniform tier-3 (non-replayable) classification of LLM output regardless of agent architecture sophistication | §4 | Constitution §7.3; Matrix row 18 |
| Role-category tool scoping, promotion/authorization category structurally excluded from every role | §5 | Constitution §6; Matrix row 49; Portfolio/Deployment §7.3 |
| Educational/copilot modes bound by the same contracts as any other agent role, no special exception | §6 | Matrix row 52 |
| Typed contracts and human-authorization gate must precede, not accompany, agent access grant | §7.2 | Matrix row 56 (U-11) |

**Anti-patterns (explicitly rejected):**

- An agent role, however named ("Portfolio Manager," "Risk Approver," etc.) or however much adversarial
  review feeds into it, constituting or substituting for the `HumanAuthorizationRecord` event — rejected per
  Constitution §6 and Portfolio/Deployment §7.3; concrete negative precedent: TradingAgents'
  Portfolio-Manager-as-LLM-approval-gate (`docs/research/candidate-reports/PA-08-tradingagents.md`).
- Treating a sophisticated multi-agent debate pipeline's output as evidentiary because of its internal review
  structure, rather than because it passed deterministic compilation and evaluation — rejected per Constitution
  §7.3 and Matrix row 18; illustrated by TradingAgents' own non-reproducibility admission (§4 item 2).
- An unbounded or self-extending agent-run budget that continues past its declared ceiling without human
  re-authorization — rejected per Matrix row 8's "unbounded agents risk runaway search, contamination, or
  self-confirmation."
- Granting an agent role a tool spanning from proposal-authoring into the promotion/authorization category —
  rejected per §5.1's structural exclusion.
- Treating an educational/copilot agent mode as exempt from the prohibitions and classification rules that
  apply to the orchestration agent layer generally — rejected per Matrix row 52 ("not a special exception").
- Activating any agent tool access before the typed contracts and human-authorization gate it would call are
  already gate-enforced for non-agent callers — rejected per §7.2.

## 10. Dependencies

No third-party agent framework, SDK, or orchestration product is chosen, adopted, or promoted by this
document, per this sprint's explicit constraint. TradingAgents
(`docs/research/candidate-reports/PA-08-tradingagents.md`) is cited REFERENCE-only for its role-decomposition
structural pattern (§4 item 1) and as the concrete negative precedent for the approval-gate anti-pattern
(§2.3 item 5, §9) — its code is not adopted. No new runtime dependency is introduced by this document.

## 11. Consistency check against Constitution, Architecture, Data Architecture, Research Methodology, and
Portfolio/Deployment

This document was checked for contradiction against `01-constitution.md`, `02-system-architecture.md`,
`03-research-methodology.md`, `04-data-architecture-strategy-ir.md`, and
`06-portfolio-deployment-monitoring.md` in full. No conflict was found: every clause above operationalizes a
boundary those five documents already establish (Constitution §6, §7; Architecture §2, §5, §6, §8; Data
Architecture §5.4; Research Methodology §8.2, §9, §10; Portfolio/Deployment §7, §11) at the agent-permission
level, without contradicting or silently re-deciding any of their fixed clauses. In particular:

- this document does not relax or create an exception to Portfolio/Deployment §7.1's Constitution-locked
  principle that a human, never an agent, authorizes material-risk promotion (§2.3 item 5, §5.1 restate it
  from the agent-permission side only);
- this document does not re-decide `StrategyIR`/`CampaignSpec`/`ValidationPlan`/`PortfolioPlan`/
  `HumanAuthorizationRecord` schema (documents 04/05/06's scope) — it references those contracts by name;
- this document does not accelerate or argue for earlier activation of the Agent & Orchestration layer,
  consistent with Architecture §2's "Explicitly not P0" and §6's negative-constraint framing;
- §3.1 item 3/§3.2's "agent-run identity" vocabulary is now checked against, and reconciled with rather than
  duplicating, document 04 §5.4's `AuditActor.orchestrationSessionId` — this document does not define a
  second, competing session-identity model; it names document 04's field as the binding target and adds only
  "iteration index" as a finer-grained addition within a session, per §3.1 item 3 above. This reconciliation
  was checked this fix pass and found consistent, not merely asserted.

No HALT condition applies.

## 12. PROVISIONAL items and resolution paths

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| Exact agent-activation readiness checklist/scorecard satisfying §7.2's structural contract | PROVISIONAL — unvalidated | Danny | Defined once documents 04/05/06's typed contracts are implementation-stable enough to constrain against, per Matrix row 56's own sequencing logic — a post-P0 decision, not a spec-time one |
| Exact per-role permission matrix populating §5.1's tool-access categories with named agent roles | PROVISIONAL — unvalidated | Danny | Resolved alongside the activation readiness checklist above, once concrete agent roles are proposed against stable typed contracts |
| Agent-run budget ceiling numeric value(s) (§3.1) | Not a global constant — per-run operational parameter, consistent with Research Methodology §9's treatment of per-campaign budgets | Whoever authorizes a given agent run | Set at run-authorization time; no global default is fixed by this document |
| Specific agent framework/SDK/orchestration product choice | Deferred, not PROVISIONAL — implementation decision, out of this document's scope | Document 08 / implementation owner | Resolved during Implementation Roadmap or build-phase tooling selection, not spec-time |
| Specific tool-permission enforcement technology (capability tokens, RBAC product, sandboxing mechanism) | Deferred, not PROVISIONAL — implementation decision, out of this document's scope | Document 08 / implementation owner | Resolved during build-phase design of the Agent Tool Layer, once activation (§7) is authorized |

No numeric constant (budget ceiling, activation-readiness score, permission-matrix specifics) is introduced
anywhere in this document. Every item above is either explicitly deferred as an implementation decision out
of spec-time scope, or tagged PROVISIONAL with a named owner and resolution condition, per this program's
no-fabricated-constants discipline (this repo's `CLAUDE.md`, Decision Discipline; Constitution §11).

## 13. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Implementation
Roadmap work (document 08) or during independent review (Constitution §11). After freeze, amendment requires
an explicit ADR and version change.
