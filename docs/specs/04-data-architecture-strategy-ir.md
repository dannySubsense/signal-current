# Signal Current — Data Architecture & Strategy IR Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, `docs/specs/02-system-architecture.md`, and
`docs/specs/03-research-methodology.md`. Not yet independently reviewed. Frozen only after the full
eight-document set clears Frank's binding spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gap G8. @architect, 2026-09-05, per Frank's spec-gate attempt-2 finding F1/F2 — added the audit/lineage event
principal contract (§5.4), so `IndependentReproductionRecord.originalAuthor`/`reproducedBy` (document 05 §2)
have a real, bound actor reference instead of free strings. @architect, 2026-09-05, per Frank's spec-gate
attempt-3 findings F2/minor — named the specific `AuditLineageEvent`s `originalAuthor`/`reproducedBy` are
each resolved from (§5.4 closing paragraph), made `orchestrationSessionId` required (non-undefined) for
`actorType: 'agent'` and stated it is assigned by the Agent Tool Layer/harness at session start, never
self-declared (§5.4), repaired the broken first-paragraph sentence and restructured §5 into proper
§5.1-§5.3 subsections ahead of §5.4 rather than renumbering a cross-referenced anchor, and removed
process-narration language from body text. @architect, 2026-09-05, per Frank's spec-gate attempt-4
findings F1/F2 — named the `AuditLineageEvent.actor` enforcement point mirroring Architecture §4.1's
lockbox pattern, stated session-initiation authority (human-only, no nested minting) (§5.4), and added
a cross-referenced PROVISIONAL row for actor/session authenticity to `06-portfolio-deployment-monitoring.md`
§16's widened U-12 entry (§8). @architect, 2026-09-05, per Frank's spec-gate
attempt-5 fix 2 — split the U-12 (extended) row's resolution condition (§8) so the actor/session-
verification sub-item resolves in Phase R1 rather than being lumped with the deployment-target-dependent
sub-item; matched the identical wording into document 06 §16 and document 07 §12. @architect, 2026-09-05,
per Sol's cold review (target SHA 99d3673) — closed a distinct-identity gap in the human-side and
human/agent-pair comparisons: §5.4's session-initiation-authority rule supplied a distinctness hook only
where an agent was on one side of a comparison; it defined no equivalent hook for a human `actorId` operating
under multiple accounts, or for the human who controls both an original run and its own agent-driven
reproduction. Added `AuditActor.controllingPrincipalId` and the "Controller/natural-person equivalence"
paragraph (§5.4), widened the U-12 (extended) PROVISIONAL row (§8) to cover controller/natural-person
resolution alongside the existing actor/session-authenticity sub-item, and re-ran §9's consistency check. @architect, 2026-09-05, per Frank's spec-gate attempt-10 finding — closed the fail-open loophole Cold Frank found in `controllingPrincipalId`: made the field fail-closed (an absent value is treated as non-distinct, never as license to fall back to bare `actorId` comparison), named the recording component as its sole populator (mirroring `orchestrationSessionId`'s existing treatment), deleted the "whenever populated" clause, extended §8's U-12 (extended) row and §5.4's Phase R1 dependency note to cover `controllingPrincipalId` resolution itself, and re-ran §9's consistency check.

**Primary question this document answers:** What are the canonical data, artifact, schema, lineage and
executable strategy contracts?

**Traceability convention:** every substantive clause cites a Reconciliation Matrix row, a Constitution
clause, or an Architecture/Research-Methodology section. Where this document generalizes beyond a single
directly-matching source, it is flagged inline as synthesis, consistent with the precedent in Constitution
§10 and Architecture §2. Design precedent from prior-art research is cited REFERENCE-only.

## 1. Scope and non-goals — read this before anything else

Per the Reconciliation Matrix §7's explicit statement that U-01 "requires deliberate design and golden
examples" and must not be guessed during drafting, and per this sprint's explicit orchestration
instruction: **this document defines the CONTRACT the StrategyIR schema, fill/execution semantics, and
broker/venue normalization fields must satisfy once designed. It does NOT invent:**

- a field-by-field StrategyIR JSON/binary schema;
- a specific canonical serialization format;
- a specific content-addressing hash algorithm;
- specific fill/intrabar-collision resolution rules or cost-model defaults;
- a specific broker/venue normalization field list.

Each of the above is tagged **PROVISIONAL — unvalidated**, owner **Danny**, with a named resolution
condition, in §8. This is the correct and required output for U-01/U-02/U-03 at this stage — an honest,
well-scoped PROVISIONAL section, not a defect to be patched by inventing plausible-sounding specifics.

This document also does NOT re-decide:

- the artifact-store product (local filesystem vs. S3-compatible) — that is U-10, already deferred to
  document 08 in Architecture §14; this document only fixes the store's required properties (§5);
- validation statistical defaults (U-04/U-05/U-06) — document 05's job;
- deployment parity tolerance (U-07) — document 06's job;
- agent permission matrix (U-11) — document 07's job.

## 2. StrategyIR contract — what it must BE, not its exact schema

Per Constitution §3 item 2 ("StrategyIR must be executable and semantically complete — no ambiguity
permitted") and Matrix row 11 ("StrategyIR must support composable, typed primitives and explicit semantic
roles rather than opaque code blobs... one or more synchronized input streams/timeframes with explicit
alignment and availability semantics"), a valid StrategyIR Candidate MUST satisfy the following properties.

### 2.1 Composable, typed primitives with explicit semantic roles

1. A StrategyIR is a composition of primitives, each carrying an explicit, versioned semantic role drawn
   from at minimum: **regime, detector, trigger, entry, exit, stop, sizing** (Matrix row 11's verbatim
   list). A primitive with no declared role is not valid IR — an "opaque code blob" is exactly what row 11
   prohibits.
2. Each primitive's role determines what it may read (e.g., a sizing primitive may read realized
   position/account state; a detector primitive may not write orders) and this role/capability boundary is
   itself part of the contract the eventual schema must encode — the exact typed representation is document
   04's own schema job (i.e., PROVISIONAL here, per §8), but the requirement that roles are enforced, not
   advisory, is fixed now.
3. Per Research Methodology §5 (Matrix row 12), entry, exit, sizing, regime, and risk/stop logic are
   independently addressable — the schema must allow a StrategyIR to hold one dimension fixed/inherited
   from a prior `StrategyArtifact` while varying another, per that document's `CampaignSpec` dimension
   bookkeeping. StrategyIR itself is always fully resolved for every dimension it needs to execute; only the
   *campaign/generation process* upstream may vary dimensions independently.
4. **Motivating example (cited per this sprint's explicit instruction), not adopted code:** TA-Lib's
   Wilder-smoothing-vs-EMA RSI convention ambiguity (`docs/research/candidate-reports/PA-02-talib.md`) is
   the clearest illustration in this whole research program of why an indicator name alone is not a
   semantic contract. Two RSI implementations using different smoothing conventions are not
   "the same primitive with different parameters" — they are two distinct, separately-versioned primitive
   definitions. The eventual StrategyIR schema (§8, PROVISIONAL) MUST make convention (e.g., smoothing
   method, warm-up handling, price source) a first-class, versioned, explicit part of a primitive's
   identity — never an implicit default inherited from whichever library happens to compute it. This
   requirement is fixed now; the exact field(s) that encode it are the deferred schema design's job.

### 2.2 Synchronized input streams and timeframes

1. A StrategyIR MUST support one or more synchronized input data streams and/or timeframes (Matrix row 11).
   "Synchronized" means the schema must express, explicitly and per-stream: what data is available at a
   given evaluation point (availability/point-in-time semantics), how streams of differing native
   resolution align to a common evaluation clock, and what happens when one stream has no new bar/tick at
   an evaluation point a co-input stream does.
2. No single timeframe or resolution is privileged as the implicit alignment clock (Architecture §7.2,
   Constitution §2). A strategy consuming, say, an hourly regime filter and a 1-minute entry trigger must
   express that relationship as an explicit alignment contract within the IR, not as an artifact of
   execution order in code.
3. Exact field-level representation of streams/alignment/availability is PROVISIONAL (§8) — the requirement
   that it be explicit and versioned, never implicit, is fixed now.

### 2.3 Executable and semantically complete — no ambiguity permitted

Per Constitution §3 item 2 and Research Methodology §1's spine diagram: a `ResearchRecord` may carry
ambiguity; a StrategyIR Candidate may not. Concretely:

1. Every primitive, parameter, and alignment rule in a StrategyIR Candidate must resolve to one concrete
   value or one concrete typed reference (e.g., a versioned indicator-convention identifier per §2.1 item
   4) — no "TBD," no free-text parameter, no unresolved branch.
2. Where Research Methodology §3 required an ambiguous source to be preserved as multiple versioned
   branches at the `ResearchRecord`/`CampaignSpec` stage, each such branch that reaches candidate generation
   produces its own fully-resolved, unambiguous StrategyIR Candidate — the ambiguity is resolved by
   branching into distinct candidates, never by a single candidate carrying an unresolved choice.
3. A StrategyIR Candidate that fails this completeness check does not exist as a valid candidate — it is
   rejected before entering the `SimulationRun` stage (Architecture §3.1), and that rejection is itself
   recorded in the `CampaignSpec`'s rejection-count bookkeeping (Research Methodology §9 item 4).

### 2.4 Immutability and content-addressed identity

Per Architecture §3.1 ("a candidate is never mutated after creation, only superseded by a new candidate")
and Matrix row 23 ("content-addressed artifacts"):

1. Once a StrategyIR Candidate is created (emitted by a generator against a `CampaignSpec`), it is
   immutable. Any change to a primitive, parameter, or alignment rule produces a new candidate with a new
   identity, never an in-place edit.
2. A StrategyIR Candidate's identity MUST be content-addressed: its identity is derived deterministically
   from its own resolved content (primitives, parameters, roles, alignment rules, and the versioned
   convention identifiers named in §2.1 item 4), not from an externally-assigned sequence number alone. This
   is what makes two independently-generated candidates with identical resolved content recognizably the
   same artifact rather than accidentally-duplicated lineage entries.
3. The exact serialization format that content addressing hashes over, and the exact hash algorithm, are
   PROVISIONAL (§8) — the requirement that identity be content-derived and immutable is fixed now.
4. A `StrategyArtifact` (Architecture §3.1) that is later promoted from a candidate carries that candidate's
   content-addressed identity forward as part of its own provenance record (Constitution §9.1) — promotion
   never re-derives or reassigns identity.

## 3. Fill/execution semantics contract — what must be explicit and versioned, not the exact rules

Per Matrix row 21 (verbatim): "All order timing, stop/target collision, gaps, spreads, slippage, fees,
financing, tick/lot sizes, sessions and margin assumptions must be explicit and versioned." Per Matrix
§7, U-02 requires "an exact semantics ADR" and must not be guessed here.

### 3.1 What the execution-semantics contract must cover

An `ExecutionContext` (the typed contract governing how a `SimulationRun` interprets a StrategyIR
Candidate's orders against market data) MUST make each of the following an explicit, versioned,
inspectable field or field-group — never an implicit engine default:

1. **Order timing** — when, relative to bar/tick availability, an order generated by a trigger/entry/exit
   primitive is considered "seen" by the simulated market (e.g., next-bar-open vs. same-bar-close vs.
   tick-level timing) — the exact rule is PROVISIONAL (§8); that a rule must be declared and versioned is
   fixed now.
2. **Stop/target collision priority** — when both a stop and a target could plausibly fill within the same
   evaluation window (e.g., an intrabar gap through both levels), which one is deemed to have filled first,
   and under what assumption about intrabar path — exact rule PROVISIONAL (§8); the requirement that this
   is an explicit, named, versioned assumption (never silently resolved by execution-engine implementation
   order) is fixed now.
3. **Gaps** — how a simulated fill is determined when price gaps through an order's trigger level between
   evaluation points.
4. **Spreads** — bid/ask (or equivalent) spread modeling, whether static, time-varying, or venue-sourced.
5. **Slippage** — the model class used (e.g., fixed, volume-scaled, volatility-scaled) and that "zero
   slippage" is itself an explicit, justified, versioned choice, never a silent default.
6. **Fees** — commission/fee schedule, explicit and versioned per instrument/venue where it varies.
7. **Financing** — overnight/swap/funding-rate cost modeling where the instrument class carries it.
8. **Tick/lot sizes** — minimum price increment and minimum tradeable size, explicit per instrument.
9. **Sessions** — trading-session/calendar model governing when an order may be considered live (detailed
   under §4, since session/calendar policy is shared with broker/venue normalization).
10. **Margin assumptions** — how margin/leverage is modeled for instruments that require it.

Per Constitution §5 item 6 and Matrix row 34: no promotion may proceed on a zero-cost assumption unless the
instrument truly has no modeled cost and this is explicitly justified and recorded — this applies to every
item above, not only fees.

### 3.2 What this document fixes vs. defers

This document fixes only that:

- every item in §3.1 is a named, versioned field or field-group in the `ExecutionContext` and/or
  `CostModel` contract — never a hardcoded constant inside the Deterministic Simulation Engine's core code;
- a `SimulationRun` (Architecture §3.1) records which exact `ExecutionContext`/`CostModel` version it used,
  as part of its own versioned-input-tuple identity (Constitution §3 item 1, Architecture §9 determinism
  contract);
- changing any item in §3.1 for an existing strategy produces a new `SimulationRun` against the new
  context, never a silent re-interpretation of a historical run's recorded ledger (Constitution §3 item 5's
  append-only/no-rewrite principle, generalized here from live data to execution-context versioning).

The exact collision-resolution rule, exact cost-model default values, and exact fill-timing rule are
PROVISIONAL — see §8, owner Danny, resolution condition "dedicated execution-semantics ADR per Matrix row
21's own stated need."

## 4. Broker/venue normalization contract — what must be encoded, not the exact field list

Per Matrix row 25 (verbatim): "DataSnapshot and ExecutionContext must explicitly encode venue/broker/
calendar/session/roll/contract/currency assumptions." Per Matrix §7, U-03 requires resolution in this
document at the *contract* level; the exact field list remains open pending dedicated design (§8).

### 4.1 What must be explicit

A `DataSnapshot` (the typed contract describing a specific slice of market/feature data consumed by a
`CampaignSpec` or `SimulationRun`) and an `ExecutionContext` (§3) MUST, together, explicitly encode:

1. **Venue/broker identity** — which venue or broker's data/execution conventions this snapshot/context
   represents, as an explicit typed reference, never inferred from file naming or directory location.
2. **Calendar/session model** — trading calendar and session boundaries (open/close, holidays, half-days)
   for the relevant venue, as an explicit versioned reference — never a hardcoded assumption baked into
   core simulation code.
3. **Futures roll policy** (where the instrument class requires it) — roll trigger rule and adjustment
   method (e.g., back-adjusted vs. unadjusted continuation), explicit and versioned per contract series.
4. **Contract specification** — tick size, contract size, expiry structure, and other instrument-defining
   fields, explicit per instrument.
5. **Currency** — the settlement/quote currency of the instrument and, where a strategy or portfolio spans
   multiple currencies, an explicit conversion/normalization policy — never a single implicit
   `stake_currency`-style global baked in above the adapter boundary.

### 4.2 Architectural isolation requirement (binding now, not deferred)

Per Architecture §10 and Constitution §2: venue/broker-specific logic MUST be isolated behind an adapter
interface with zero venue-specific logic in the Deterministic Simulation Engine's core. This document
extends that isolation boundary to the data layer specifically:

1. No StrategyIR primitive, no core simulation code path, and no validation code path may read venue/
   broker/calendar/currency identity directly — they read only through the `DataSnapshot`/`ExecutionContext`
   contract's explicit fields (§4.1), which are themselves populated by an adapter, per Architecture §10's
   isolation boundary.
2. **Positive precedent (REFERENCE only, not adopted code):** vn.py's `BaseGateway` abstract-adapter
   pattern (`docs/research/candidate-reports/PA-01-vnpy.md`) — an abstract adapter interface with zero
   venue-specific code in the shared core, validated across roughly 90 peer venue-adapter repositories — is
   the positive precedent this isolation requirement follows. Only the isolation pattern is cited; vn.py's
   GUI coupling and thin core test coverage disqualify its code from adoption (per PA-10's own register).
3. **Cautionary precedent — what this contract must structurally prevent (REFERENCE only):**
   - Freqtrade's central finding (`docs/research/candidate-reports/PA-01-freqtrade.md`): a BASE/QUOTE pair
     format and a global `stake_currency` baked in above the adapter boundary. No currency or pair-format
     assumption may leak above the adapter layer into StrategyIR or core execution semantics — this is
     exactly the failure §4.1 item 5's explicit-currency-field requirement exists to block.
   - Zipline-reloaded's `minutes_per_day=390` NYSE-session hardcoding and its live crypto-ingestion uint32
     dtype bug (`docs/research/candidate-reports/PA-01-zipline-reloaded.md`): concrete evidence of what
     happens when a data-layer default (a session length, a dtype chosen for one asset class) is silently
     promoted without being explicit, versioned, and re-justified for a different asset class. This is the
     direct precedent for why §4.1 item 2's calendar/session model must be an explicit versioned reference,
     never a hardcoded constant.
   - Microsoft Qlib's silently-defaulted `qlib.init()` CN-region behavior
     (`docs/research/candidate-reports/PA-01-microsoft-qlib.md`): the sharpest available example of a
     data-layer default becoming an unversioned, unexamined scientific parameter. This is cited directly as
     the anti-pattern the entire §4 contract exists to prevent — every field named in §4.1 must be explicit
     specifically so that no region/venue/session default can ever again enter unversioned and unexamined.

### 4.3 What this document defers

The exact field-by-field schema (data types, required-vs-optional fields, exact roll-adjustment method
names, exact calendar representation format) is PROVISIONAL — see §8, owner Danny, resolution condition
"dedicated StrategyIR/data-schema design session with golden-example worked strategies spanning at least
two materially different venues/asset classes, per Architecture §7.2's heterogeneity requirement."

## 5. Data store architecture

Per Matrix row 23 (verbatim): "Canonical architecture: immutable columnar market/feature snapshots;
analytical scans separate from transactional metadata/lineage; content-addressed artifacts. Exact products
remain implementation decisions where not semantically important." This section is organized into four
subsections (§5.1-§5.4), each fixing one required property of the store per that Matrix row, followed by
the product-choice deferral.

### 5.1 Market/feature data is immutable columnar

Market/feature data is stored as immutable columnar snapshots. A `DataSnapshot` (§4.1), once referenced by
any `CampaignSpec` or `SimulationRun`, is never mutated in place — a corrected or updated data source
produces a new, separately-versioned `DataSnapshot`, never an in-place rewrite (Constitution §9.1, Matrix
row 5).

### 5.2 Analytical scans are architecturally separate from transactional metadata/lineage

Analytical scans (bulk read access for simulation/feature computation across a `DataSnapshot`) are
architecturally separate from transactional metadata/lineage (artifact identity, provenance, promotion-gate
audit events, correlation/causation IDs per Architecture §3.2 and Constitution §9.1) — the two are not
stored in, or served from, the same table/store role, because they have different access patterns,
different mutability profiles (lineage/audit is append-only event data; market data is immutable bulk
columnar data), and different consumers (Deterministic Simulation Engine vs. Promotion/Gate Service and
Audit/Event Log per Architecture §5).

### 5.3 Artifacts are content-addressed

Artifacts are content-addressed (Matrix row 23; §2.4 above for StrategyIR specifically). This applies
uniformly to every immutable artifact in the spine (Architecture §3) that this document's contracts touch:
`SourceSnapshot`, `DataSnapshot`, `StrategyIR Candidate`, `SimulationRun` event ledgers, and
`ValidationArtifact` records are each identified by content-derived hash, not solely by a sequential or
externally-assigned ID.

**Which specific database/object-store products** implement §5.1-§5.3 above (e.g., a specific columnar
analytical engine, a specific object store, a specific metadata database) is U-10, already deferred to
document 08 in Architecture §14 — this document does not re-decide it. This document fixes only the store's
required properties: immutability of market/feature data (§5.1), separation of analytical-scan access from
transactional lineage access (§5.2), and content-addressed identity for every artifact class named above
(§5.3).

### 5.4 Audit/lineage event actor contract

Per Architecture §4.2's naming of this document as the audit-log schema owner ("the exact audit-log schema
is also document 04's job") and Constitution §9.1 / Matrix row 53 ("state transitions and consequential
actions produce versioned audit events with actor, correlation/causation ID, and artifact references"): the
transactional-metadata/lineage store (§5.2) MUST carry a principal/actor field on every audit/lineage
event it records. This document fixes the actor contract itself, which was previously absent despite this
document being the schema owner named for it.

```typescript
/**
 * A distinct, resolvable identity — a human account or a specific, individually-identified agent
 * invocation/session — never an unbound free-text label. Per Constitution §9.1/Matrix row 53, every
 * audit/lineage event MUST reference one of these, not a string an emitter is free to author.
 */
interface AuditActor {
  /** Content-addressed or otherwise stable, unique identifier for this principal. */
  actorId: string;
  /** 'human' or 'agent' — never conflated; an agent invocation is not a human account. */
  actorType: 'human' | 'agent';
  /**
   * For actorType 'agent': the specific orchestration-session/invocation identity that produced this
   * event — not merely a role name. MUST be present (non-undefined) whenever actorType is 'agent'; a
   * record with actorType 'agent' and no orchestrationSessionId is malformed and MUST be rejected. Absent
   * only for actorType 'human', where no orchestration session exists. This ID is assigned by the Agent
   * Tool Layer/harness at session start — never self-declared by the agent itself, nor by whatever
   * orchestrates it. Two agent role labels (e.g. "validator" and "reproducer") driven by the same
   * orchestration session MUST share the same orchestrationSessionId, per document 05 §2.1's "distinct
   * identity" definition (which this field exists to make checkable, not merely asserted), and per
   * document 07 §3.1 item 3/§3.2 (which states every agent run carries the orchestrationSessionId of the
   * session that spawned it).
   */
  orchestrationSessionId?: string;
  /**
   * The natural person deemed to control this action, independent of which account or session
   * produced it. For `actorType: 'human'`, this is the natural-person identity `actorId` resolves to
   * under the identity-provider's account model — which may differ from `actorId` itself if that model
   * permits one person to hold multiple accounts/credentials. For `actorType: 'agent'`, this is the
   * `actorId` of the human who holds session-initiation authority for `orchestrationSessionId` (per the
   * session-initiation-authority paragraph below). Populated the same way `actor` itself is populated
   * (Enforcement point below): by the recording component, resolved from an identity-provider lookup at
   * the moment it records the event — never caller-supplied, never self-declared. Remains optional only
   * because the identity-provider lookup this field depends on is not yet chosen (U-12, §8); it is NOT
   * optional in the sense of "may be omitted from a well-formed event once that lookup exists." Document
   * 05 §2.1's distinctness test (items 1 and 3) operates on `controllingPrincipalId`, per the
   * "Controller/natural-person equivalence" paragraph below (added per Sol's cold review, target SHA
   * 99d3673) — and, per that same paragraph, a record with this field absent is treated as fail-closed
   * (non-distinct), never as license to fall back to bare `actorId` comparison.
   */
  controllingPrincipalId?: string;
}

/**
 * The audit/lineage event envelope this document owns, per Architecture §4.2. Every event in the
 * transactional-metadata/lineage store (§5.2) is shaped as at least this envelope; specific event
 * kinds (promotion-gate transitions, lockbox access, independent-reproduction runs, etc.) extend it with
 * kind-specific fields, per Architecture §3.2 and document 05 §11.2.
 */
interface AuditLineageEvent {
  /** Content-addressed identity of this event itself. */
  id: string;
  /** The principal responsible for this event — see AuditActor above. Never a free string. */
  actor: AuditActor;
  /** Correlation/causation IDs per Constitution §9.1 / Matrix row 53. */
  correlationId: string;
  causationId?: string;
  /** Content-addressed references to the artifact(s) this event concerns. */
  artifactRefs: string[];
  timestamp: string;
  /** Event-kind-specific payload; exact per-kind shapes are this document's and document 05's job as
   *  specific event kinds are defined (e.g. document 05 §11.2's lockbox-access event, §2's
   *  reproduction-record event). */
  eventKind: string;
}
```

**Enforcement point (mirrors Architecture §4.1's lockbox pattern):** `actor` above is never a parameter
accepted in any typed tool call or API request that produces a `SimulationRun`, a `ValidationArtifact`, a
lockbox-access event, or any other audit/lineage event. The component *recording* the event — the
Deterministic Simulation Engine for `SimulationRun` production, the Validation & Statistical Controls
Service for `ValidationArtifact` production, the Data Access Gateway for lockbox events (Architecture §4.1),
and the Audit/Event Log for every other event kind generally — MUST itself populate `actor` from the
authenticated caller principal at the moment it records the event, never from a caller-supplied field. Per
Architecture §4.1's own wording for the lockbox ("the gateway itself performs the check — not caller-side
discipline"), the same discipline applies here: a typed request that attempts to supply its own `actor`
value is malformed and MUST be rejected by the recording component, not merely discouraged by convention.
Exactly which authentication mechanism establishes "the authenticated caller principal" in the first place —
the identity provider, token format, or session-verification technology — is not fixed here; that is the new
PROVISIONAL item in §8 below, which is deliberately the terminal boundary: below it, authentication-mechanism
selection is U-12's job (document 06 §16), not this document's.

**Session-initiation authority:** an orchestration session (the source of `orchestrationSessionId` above) is
initiated only by a human principal (`actorType: 'human'`). Session initiation is itself recorded as an
`AuditLineageEvent` whose `actor` is that human and whose payload carries the newly-minted
`orchestrationSessionId`. An agent run — including any orchestrator role, however named — cannot itself
initiate a new session; every run spawned from within an existing session inherits that session's
`orchestrationSessionId`, and no nested or fresh minting is permitted mid-session. This closes the residual
gap Frank's spec-gate attempt-4 named: without this sentence, an orchestrator could request a fresh session
between driving a validator and driving a reproducer, satisfying document 05 §2.1 item 2's distinctness
check on paper while remaining one well in fact. Document 07 §3.1 item 3/§3.2 restate this same rule from the
agent-permission side and must not be read as permitting one orchestrator to legitimately span several
sessions.

**Controller/natural-person equivalence (Sol's cold review, target SHA 99d3673; fail-closed per Frank's
spec-gate attempt-10):** the session-initiation-authority rule above supplies a distinctness hook only where
an agent is on one side of a comparison — via `orchestrationSessionId` resolving back to its
session-initiating human. It supplies no equivalent hook for a pure human-vs-human comparison, and by itself
does not prevent one natural person from appearing distinct merely by holding two different human
`actorId`s (e.g., two accounts or sets of credentials), nor does it check whether the human `actorId`
recording a reproduction is the same natural person who, as session initiator, controls an agent-authored
original (or vice versa). To close this, `AuditActor` carries `controllingPrincipalId` (defined above):
for `actorType: 'human'`, the natural-person identity `actorId` resolves to under the identity-provider's
account model; for `actorType: 'agent'`, the `actorId` of the human who holds session-initiation authority
for its `orchestrationSessionId`. Populated the same way `actor` itself is populated (Enforcement point
above): by the recording component, resolved from an identity-provider lookup, never caller-supplied.

`controllingPrincipalId` remains typed optional only because the identity-provider lookup it depends on is
not yet chosen (U-12, §8) — not because a well-formed event may omit it once that lookup exists. Document
05 §2.1's distinctness test (items 1 and 3) operates on `controllingPrincipalId` unconditionally, never
falling back to bare `actorId`/`orchestrationSessionId` comparison. **A record in which
`controllingPrincipalId` is absent is treated as fail-closed: the distinctness test MUST NOT pass on that
record, and the record MUST NOT be read as satisfying items 1 or 3 by virtue of `actorId` or
`orchestrationSessionId` inequality alone.** Absence of `controllingPrincipalId` is never license to fall
back to the pre-Sol-review check this field exists to replace — that is the exact loophole Frank's
spec-gate attempt-10 named. Exactly how a human `actorId` resolves to `controllingPrincipalId` — one
`actorId` per natural person by construction, versus an identity-provider lookup that distinguishes shared
or delegated accounts from the person actually operating them — is not fixed here. **PROVISIONAL —
unvalidated; owner Danny.** This is the same identity-provider/account-model question §8's widened U-12
item already names for agent-side actor/session authenticity, now further widened to cover human-side
controller resolution; resolved in Phase R1 alongside U-12's other actor/session-verification sub-items
(§8), since this equally gates the first `StrategyArtifact` promotion (document 05 §2.1). Until U-12
resolves and the recording component can populate this field on every event, the independent-reproduction
gate (document 05 §2.1) cannot be satisfied at all — this is the same Phase R1 blocking dependency §14 and
document 05 §2's closing paragraphs already state for the match-tolerance and actor/session-authenticity
sub-items, now extended to cover `controllingPrincipalId` resolution itself.

This is the binding target for document 05 §2's `IndependentReproductionRecord.originalAuthor` and
`reproducedBy` fields, and both are derived by the Validation Service, never supplied by the caller.
`originalAuthor` is resolved from the `AuditLineageEvent` recording production of the original
`SimulationRun`/`ValidationArtifact` (the run/artifact referenced by `ValidationPlan.simulationRunRefs`);
`reproducedBy` is resolved from the distinct `AuditLineageEvent` recording production of the reproduction's
own `SimulationRun`/`ValidationArtifact` (the run/artifact referenced by
`IndependentReproductionRecord.reproductionRunRefs`/`reproductionValidationArtifactRef`). Neither field is an
independently authored string a `ValidationPlan` emitter is free to populate, per Constitution §9.1 and
Matrix row 53.

## 6. No privileged market dimension, applied at the data layer

Restating Constitution §2 in data-architecture-specific terms, per this sprint's explicit instruction:

1. No asset class, instrument, venue, timeframe, resolution, session model, currency, contract type, or
   execution platform may receive a hardcoded default anywhere in `DataSnapshot`, `ExecutionContext`,
   `CostModel`, or the Deterministic Simulation Engine's core code path. Every such assumption must be an
   explicit, versioned, checked parameter carried by the typed contracts in §2-§4 above.
2. This is not a restatement of intent alone — §4.2's adapter-isolation requirement and §5's
   content-addressed, separated store architecture are the concrete architectural mechanisms that make this
   enforceable rather than aspirational, in the same way Architecture §4's Data Access Gateway makes the
   Exploration/Validation/Lockbox boundary a service-layer enforcement point rather than a convention.
3. **Direct tie to cited cautionary precedent (§4.2 item 3):** Qlib's CN-region default, Zipline-reloaded's
   NYSE-session default, and Freqtrade's BASE/QUOTE-and-stake-currency default are three independently-
   sourced, concrete instances of exactly the failure this clause exists to prevent — an engineering
   convenience for one context silently becoming an unexamined scientific parameter for every other context.
   Every field this document requires to be explicit (§3.1, §4.1) is explicit specifically because one of
   these three precedents shows what happens when it is not.

## 7. Simulation tiers (Matrix row 20)

Per Matrix row 20 (verbatim): "Preserve tiered simulation, but fidelity and resolution MUST be selected
through explicit market/execution contracts. No timeframe or bar resolution, including M1, is canonical."

1. The Deterministic Simulation Engine (Architecture §5) MAY offer more than one simulation fidelity tier
   (e.g., a fast/lower-fidelity tier for broad search, a high-fidelity tier for finalist verification), per
   Architecture §5's "supports tiered fidelity, selected via explicit contract, never a hardcoded default
   resolution."
2. Tier selection is itself an explicit field of the `ExperimentDefinition`/`CampaignSpec` or the
   `ExecutionContext` (§3) — never an implicit property of which asset class or timeframe a candidate
   happens to use. A candidate tested at "fast-tier" fidelity and later promoted to finalist status must
   explicitly record which tier(s) it was evaluated under as part of its `SimulationRun`/`ValidationArtifact`
   provenance (Constitution §3 item 1).
3. No specific bar resolution (e.g., M1) is privileged as the canonical or default high-fidelity tier. Per
   Architecture §7.2 and Matrix row 57, the conformance program must progressively exercise heterogeneous
   timeframes; this document's contract structurally supports that by keeping tier/resolution an explicit
   parameter rather than an assumption embedded in engine code.
4. Exact tier definitions (how many tiers, what fidelity difference each represents) are not fixed by this
   document — that is an engine-implementation and roadmap-conformance-coverage question (documents 02 §9's
   determinism contract and document 08's P0/P1 acceptance thresholds, U-13), not a data-architecture schema
   question. This document fixes only that tier/resolution selection is explicit and contract-driven.

## 8. PROVISIONAL items and resolution paths

Per this sprint's explicit constraint on U-01/U-02/U-03: these are expected to be more numerous and more
central in this document than in documents 01-03, because the Reconciliation Matrix itself flags all three
as requiring dedicated design work this drafting pass must not substitute for.

| Item | Tag | Owner | Concrete resolution condition |
|---|---|---|---|
| **U-01a** — Exact StrategyIR field-by-field schema (primitive representation, role typing, parameter types, alignment/availability field structure) | PROVISIONAL — unvalidated | Danny | Dedicated StrategyIR schema design session with golden-example worked strategies (at least one single-timeframe, single-asset example and one multi-timeframe/multi-stream example), informed by U-01's explicit Matrix requirement that it "requires deliberate design and golden examples" |
| **U-01b** — Exact canonical serialization format (e.g., specific JSON/binary/CBOR encoding) | PROVISIONAL — unvalidated | Danny | Resolved alongside U-01a, once schema shape is fixed enough to choose a serialization that preserves content-addressing stability (§2.4) |
| **U-01c** — Exact content-addressing hash algorithm and canonicalization rule | PROVISIONAL — unvalidated | Danny | Resolved alongside U-01a/U-01b; must be chosen together with serialization so that semantically-identical candidates hash identically regardless of field ordering or generator |
| **U-02a** — Exact order-timing rule (e.g., next-bar-open vs. tick-level) | PROVISIONAL — unvalidated | Danny | Dedicated execution-semantics ADR per Matrix row 21's own stated need for "an exact semantics ADR" |
| **U-02b** — Exact stop/target intrabar collision-resolution rule | PROVISIONAL — unvalidated | Danny | Resolved in the same execution-semantics ADR as U-02a; must be justified against at least one documented worst-case gap/collision scenario, not asserted without example |
| **U-02c** — Exact default cost-model values (slippage model class defaults, fee schedule defaults) | PROVISIONAL — unvalidated | Danny | Resolved in the execution-semantics ADR; per Constitution §5 item 6, any zero-cost default requires explicit justification, not silent adoption |
| **U-03** — Exact broker/venue normalization field schema (calendar representation format, roll-adjustment method taxonomy, currency-conversion policy fields) | PROVISIONAL — unvalidated | Danny | Dedicated StrategyIR/data-schema design session with golden-example worked strategies spanning at least two materially different venues/asset classes, per Architecture §7.2's heterogeneity requirement (may be combined with U-01a's design session) |
| **U-12 (extended, further widened per Sol's cold review, target SHA 99d3673)** — exact mechanism verifying `AuditActor.actorId` authenticity, minting/verifying `orchestrationSessionId` (§5.4), and resolving `AuditActor.controllingPrincipalId` — i.e., whether two human `actorId`s (or a human and an agent's session-initiating human) resolve to the same natural person (§5.4's "Controller/natural-person equivalence" paragraph) | PROVISIONAL — unvalidated | Danny | Cross-referenced, not duplicated: this is the same PROVISIONAL item as document 06 §16's widened U-12 row. **Sequencing (Frank spec-gate attempt-5 fix 2; widened per Sol's cold review):** this sub-item — `AuditActor.actorId` authenticity, `orchestrationSessionId` minting/verification, and `controllingPrincipalId` resolution — resolves in Phase R1, alongside the independent-reproduction match tolerance (document 05 §14), since all three gate the same first `StrategyArtifact` promotion (document 05 §2.1) and a `StrategyArtifact` cannot be promoted before they resolve; this does NOT depend on a deployment target or user model being chosen. (The separate `HumanAuthorizationRecord` identity-provider/protocol sub-item resolves in Phase R3 once a deployment target/user model are chosen — see document 06 §16.) This is the terminal boundary: below it, the spec set correctly stops (authentication mechanism selection is not a spec-time decision, per U-12's existing treatment). |

No numeric constant is adopted as a Signal Current setting anywhere in this document. The only numeric
literals present are an external config-default value cited from prior-art research (§4.2's Zipline-reloaded
`minutes_per_day=390`) and a repo-count observation cited from prior-art research (§4.2's vn.py "roughly 90 peer
venue-adapter repositories"); neither is adopted as a Signal Current setting. Every PROVISIONAL item above is a schema/semantics/algorithm design
decision explicitly flagged by the Reconciliation Matrix as requiring dedicated design work (U-01/U-02/U-03),
not a number this document declines to source — none is guessed here.

## 9. Consistency check against Constitution, Architecture, and Research Methodology

This document was checked for contradiction against `01-constitution.md`, `02-system-architecture.md`, and
`03-research-methodology.md` in full. No conflict was found: every clause above elaborates a boundary or
contract those three documents already establish (Constitution §§2-3, Architecture §§3, 7, 9, 10, 11,
Research Methodology §§4-5, 9) at the data/schema level, without contradicting or silently re-deciding any
of their fixed clauses. No HALT condition applies.

**Re-run 2026-09-05, per Sol's cold review (target SHA 99d3673):** this document's new
`controllingPrincipalId` field and "Controller/natural-person equivalence" paragraph (§5.4), and the widened
U-12 (extended) row (§8), were checked against document 05 §2.1/§14 (which now consumes
`controllingPrincipalId` in items 1 and 3), document 06 §16, document 07 §3.1/§12, and document 08 §3's
Phase R1 sequencing — all amended in the same pass with matching, non-duplicated wording. No new
contradiction was found; no HALT condition applies.

**Re-run 2026-09-05, per Frank's spec-gate attempt-10 finding:** the prior pass's `controllingPrincipalId`
fix left the field fail-open — declared optional at §5's field-list with no stated treatment for absence,
while document 05 §2.1's items 1 and 3 stated an unconditional MUST that this document's "whenever populated"
clause (former §5.4 wording) silently contradicted. This pass makes §5.4's field and paragraph fail-closed
(an absent `controllingPrincipalId` is non-distinct, never a fallback to bare `actorId`), deletes the
"whenever populated" clause, and names the recording component as the field's sole populator — the same
treatment §5.4 already gives `orchestrationSessionId`. Checked against the matching fixes in document 05
§§2.1/14 — both documents now state the same fail-closed rule in the same terms, and
§8's U-12 (extended) row and the Phase R1 dependency note in §5.4 both now name `controllingPrincipalId`
resolution as blocking, not merely `actorId`/`orchestrationSessionId` authenticity. No new contradiction was
found; no HALT condition applies.

## 10. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Validation,
Portfolio/Deployment, and Agent specification work (Constitution §11), and MUST be revised once the
dedicated design sessions named in §8 resolve U-01/U-02/U-03 — at that point this document is amended (not
silently superseded) to replace the PROVISIONAL tags with cited, versioned schema/semantics decisions. After
freeze, amendment requires an explicit ADR and version change.
