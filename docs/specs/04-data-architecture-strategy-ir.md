# Signal Current — Data Architecture & Strategy IR Specification

**Status:** Draft — first pass, per `docs/specs/00-source-inventory-reconciliation.md` (the Reconciliation
Matrix), `docs/specs/01-constitution.md`, `docs/specs/02-system-architecture.md`, and
`docs/specs/03-research-methodology.md`. Not yet independently reviewed. Frozen only after the full
eight-document set clears Frank's binding spec-gate.

**Provenance:** @architect.
**Editorial corrections:** @architect, 2026-09-05, per `05-REVIEW.md` gap G8.

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
remain implementation decisions where not semantically important."

1. **Market/feature data** is stored as immutable columnar snapshots. A `DataSnapshot` (§4.1), once
   referenced by any `CampaignSpec` or `SimulationRun`, is never mutated in place — a corrected or updated
   data source produces a new, separately-versioned `DataSnapshot`, never an in-place rewrite (Constitution
   §9.1, Matrix row 5).
2. **Analytical scans** (bulk read access for simulation/feature computation across a `DataSnapshot`) are
   architecturally separate from **transactional metadata/lineage** (artifact identity, provenance,
   promotion-gate audit events, correlation/causation IDs per Architecture §3.2 and Constitution §9.1) — the
   two are not stored in, or served from, the same table/store role, because they have different access
   patterns, different mutability profiles (lineage/audit is append-only event data; market data is
   immutable bulk columnar data), and different consumers (Deterministic Simulation Engine vs.
   Promotion/Gate Service and Audit/Event Log per Architecture §5).
3. **Artifacts are content-addressed** (Matrix row 23; §2.4 above for StrategyIR specifically). This applies
   uniformly to every immutable artifact in the spine (Architecture §3) that this document's contracts
   touch: `SourceSnapshot`, `DataSnapshot`, `StrategyIR Candidate`, `SimulationRun` event ledgers, and
   `ValidationArtifact` records are each identified by content-derived hash, not solely by a sequential or
   externally-assigned ID.
4. **Which specific database/object-store products** implement the above (e.g., a specific columnar
   analytical engine, a specific object store, a specific metadata database) is U-10, already deferred to
   document 08 in Architecture §14 — this document does not re-decide it. This document fixes only the
   store's required properties: immutability of market/feature data, separation of analytical-scan access
   from transactional lineage access, and content-addressed identity for every artifact class named above.

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

No numeric constant is adopted as a Signal Current setting anywhere in this document. The only numeric
literals present (threshold, budget, tolerance, sample-size minimum, cost-model default value), including
those in §4.2, are external values quoted from cited candidate reports and explicitly rejected as settings,
not adopted defaults. Every PROVISIONAL item above is a schema/semantics/algorithm design
decision explicitly flagged by the Reconciliation Matrix as requiring dedicated design work (U-01/U-02/U-03),
not a number this document declines to source — none is guessed here.

## 9. Consistency check against Constitution, Architecture, and Research Methodology

This document was checked for contradiction against `01-constitution.md`, `02-system-architecture.md`, and
`03-research-methodology.md` in full. No conflict was found: every clause above elaborates a boundary or
contract those three documents already establish (Constitution §§2-3, Architecture §§3, 7, 9, 10, 11,
Research Methodology §§4-5, 9) at the data/schema level, without contradicting or silently re-deciding any
of their fixed clauses. No HALT condition applies.

## 10. Amendment

This document may be revised before v1.0 freeze as contradictions are discovered during Validation,
Portfolio/Deployment, and Agent specification work (Constitution §11), and MUST be revised once the
dedicated design sessions named in §8 resolve U-01/U-02/U-03 — at that point this document is amended (not
silently superseded) to replace the PROVISIONAL tags with cited, versioned schema/semantics decisions. After
freeze, amendment requires an explicit ADR and version change.
