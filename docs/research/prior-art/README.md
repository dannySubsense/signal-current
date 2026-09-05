# Signal Current — Prior Art & Reuse Research Program

**Status:** Active research leg  
**Role:** Pre-architecture and pre-implementation evidence program

## Mission

Before Signal Current invents an algorithm, subsystem, data model, orchestration pattern, simulator component, validation routine, portfolio method, or infrastructure primitive, determine whether the problem has already been solved well enough to adopt, adapt, fork, wrap, reference, benchmark, or independently reimplement.

This research leg exists to prevent two opposite failures:

1. **Reinventing solved work** simply to own every line of code.
2. **Importing someone else’s ontology or hidden assumptions** because a library is convenient.

The governing principle is:

> **Own Signal Current’s contracts, scientific semantics, evidence model, and acceptance tests. Reuse proven implementations where they satisfy those contracts.**

## Research Streams

| ID | Stream | Purpose |
|---|---|---|
| PA-01 | Systems & Engine Survey | Study complete trading/research platforms and engines |
| PA-02 | Numerical & Statistical Methods | Find proven implementations of validation, labeling, risk and research algorithms |
| PA-03 | Data / Time / Instrument Semantics | Study mature approaches to calendars, contracts, corporate actions, rolls, resampling and point-in-time data |
| PA-04 | Search / Optimization / ML | Study evolutionary, Bayesian, multi-objective, ML and experiment-search infrastructure |
| PA-05 | Portfolio / Risk | Study allocators, shared-risk simulation, HRP/NCO, optimizers and stress frameworks |
| PA-06 | Execution / Brokerage / Deployment | Study fill models, brokerage adapters, FIX/MT5/IBKR patterns, parity and live-risk controls |
| PA-07 | Provenance / Experiment / Workflow Infrastructure | Study immutable artifacts, lineage, experiment tracking, orchestration and durable jobs |
| PA-08 | Agent / Research Automation | Study agentic research systems and typed-tool permission models |
| PA-09 | Licensing & Reuse Policy | Define when Signal Current may copy, fork, modify, link, wrap, or only learn from a project |
| PA-10 | Reuse Decision Register | Record Adopt / Fork / Adapt / Wrap / Reference / Reject decisions with evidence |

## Required Output Per Candidate

Every serious candidate receives:

- project / repository / paper / standard;
- version / commit / release reviewed;
- license and reuse constraints;
- maintenance and ecosystem health;
- architecture summary;
- relevant modules / algorithms;
- assumptions and hidden defaults;
- asset/timeframe/venue coupling;
- deterministic/reproducibility characteristics;
- tests and reference fixtures;
- performance evidence;
- semantic fit with Signal Current;
- integration/coupling risk;
- independent-verification strategy;
- disposition;
- ADR/spec implications.

## Disposition Vocabulary

- **ADOPT** — use directly behind Signal Current contracts.
- **FORK** — maintain our own branch where the license and strategic value justify it.
- **ADAPT** — reuse substantial implementation while translating semantics through Signal Current interfaces.
- **WRAP** — preserve upstream behavior but isolate it behind an adapter.
- **REFERENCE** — learn from architecture/code/tests but independently implement.
- **CONFORMANCE COMPARATOR** — run a full external engine/library and diff its output against ours to surface disagreement; not a production dependency. Disagreement sends us back to the primary source; it is not evidence the external side is right.
- **EXTERNAL REGRESSION FIXTURE** — freeze a pinned-version/single-test external output as a change-detection fixture. Proves our behaviour did not drift; proves nothing about correctness.
- **REJECT** — do not depend on or model Signal Current after it.

A project may receive more than one role. Example: a package can be both **REFERENCE** and **CONFORMANCE COMPARATOR**.

## Freeze Relationship

The Constitution may proceed in parallel because its invariants should survive any library choice.

The following should not be frozen until their relevant prior-art streams have been reviewed:

- System Architecture
- Data Architecture & Strategy IR
- Validation & Statistical Controls
- Portfolio, Deployment & Monitoring
- Agent & Orchestration Layer

The Implementation Roadmap must explicitly cite reuse decisions rather than silently introducing third-party dependencies.
