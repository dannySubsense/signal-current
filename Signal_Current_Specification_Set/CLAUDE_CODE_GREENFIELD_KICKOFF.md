# Signal Current — Greenfield Claude Code Kickoff

You are beginning a greenfield repository for **Signal Current**.

Signal Current is an auditable quantitative research, strategy engineering, portfolio, deployment, and lifecycle system with a research-intelligence front end and a deterministic numerical core.

## Authority and phase boundary

The files provided with this prompt are the current working specification artifacts.

Treat all older chats, product research, prior Build Alpha / Quant Alpha Explorer / Algory ideas, external repositories, and public implementations as **research inputs**, not authoritative specifications.

We are in:

**SPECIFICATION + PRIOR-ART RESEARCH PHASE**

We are **not yet in product implementation**.

Do not build the Signal Current numerical engine, simulator, UI, agent runtime, trading adapters, or production services until the canonical specification set has:

1. been completed;
2. undergone independent review;
3. had blocking contradictions resolved;
4. been frozen as **Signal Current v1.0**.

Repository scaffolding, research tooling, documentation automation, schemas used only to clarify/specify contracts, tiny non-product research spikes, and test fixtures used to evaluate prior art are allowed. Any spike must be clearly isolated under research/spikes and must not silently become production code.

## Constitutional working principle

The deterministic quant engine will be the source of numerical research truth.

Agents and LLMs may operate the laboratory, discover sources, propose hypotheses, plan experiments, operate typed tools, compare evidence, and explain results.

They may not:
- invent numerical evidence;
- alter immutable evidence;
- bypass validation gates;
- access sealed confirmation/lockbox data through unauthorized paths;
- promote risk-bearing artifacts outside the defined authorization path.

Signal Current has **no privileged asset, market, venue, timeframe, bar resolution, strategy family, broker, execution platform, or deployment target**.

Markets, instruments, one or more timeframes/data streams, indicators/features, strategy primitives, execution contexts, validation methods, portfolio methods, and deployment adapters are typed compositional dimensions.

Scientific invariants such as no-look-ahead, point-in-time correctness, provenance, deterministic replay, evidence separation, multiple-testing controls, and agent authority boundaries are **not menu choices**.

## Source files you have been given

Read these first, in this order:

1. `README.md`
2. `00_Source_Inventory_and_Reconciliation_Matrix.md`
3. `00A_Existing_Systems_and_Reuse_Research.md`
4. `prior_art_reuse/README.md`
5. `prior_art_reuse/PA-01_Systems_and_Engine_Survey.md`
6. `prior_art_reuse/PA-02_Numerical_and_Statistical_Methods.md`
7. `prior_art_reuse/PA-03_Data_Time_and_Instrument_Semantics.md`
8. `prior_art_reuse/PA-04_Search_Optimization_and_ML.md`
9. `prior_art_reuse/PA-05_Portfolio_and_Risk.md`
10. `prior_art_reuse/PA-06_Execution_Brokerage_and_Deployment.md`
11. `prior_art_reuse/PA-07_Provenance_Experiment_and_Workflow_Infrastructure.md`
12. `prior_art_reuse/PA-08_Agent_and_Research_Automation.md`
13. `prior_art_reuse/PA-09_Licensing_and_Reuse_Policy.md`
14. `prior_art_reuse/PA-10_Reuse_Decision_Register.md`

Do not treat wording in these working files as frozen if a contradiction is discovered. Record and resolve contradictions explicitly.

## Greenfield repository structure

Create a clean repository structure along these lines. Adjust only where there is a concrete reason, and document deviations.

```text
signal-current/
├─ README.md
├─ CLAUDE.md
├─ AGENTS.md
├─ .gitignore
│
├─ docs/
│  ├─ specs/
│  │  ├─ 00-source-inventory-reconciliation.md
│  │  ├─ 01-constitution.md
│  │  ├─ 02-system-architecture.md
│  │  ├─ 03-research-methodology.md
│  │  ├─ 04-data-architecture-strategy-ir.md
│  │  ├─ 05-validation-statistical-controls.md
│  │  ├─ 06-portfolio-deployment-monitoring.md
│  │  ├─ 07-agent-orchestration.md
│  │  ├─ 08-implementation-roadmap.md
│  │  ├─ 09-independent-review.md
│  │  └─ 10-v1-freeze-manifest.md
│  │
│  ├─ research/
│  │  ├─ prior-art/
│  │  │  ├─ README.md
│  │  │  ├─ PA-01-systems-engines.md
│  │  │  ├─ PA-02-numerical-statistical-methods.md
│  │  │  ├─ PA-03-data-time-instrument-semantics.md
│  │  │  ├─ PA-04-search-optimization-ml.md
│  │  │  ├─ PA-05-portfolio-risk.md
│  │  │  ├─ PA-06-execution-brokerage-deployment.md
│  │  │  ├─ PA-07-provenance-experiment-workflow.md
│  │  │  ├─ PA-08-agent-research-automation.md
│  │  │  ├─ PA-09-licensing-reuse-policy.md
│  │  │  └─ PA-10-reuse-decision-register.md
│  │  ├─ candidate-reports/
│  │  ├─ papers/
│  │  ├─ source-notes/
│  │  └─ spikes/
│  │
│  ├─ adr/
│  ├─ glossary/
│  └─ runbooks/
│
├─ schemas/
│  └─ drafts/
│
├─ fixtures/
│  └─ research/
│
├─ scripts/
│  └─ research/
│
└─ src/
   └─ README.md
```

`src/README.md` must state that production implementation is intentionally blocked until v1.0 freeze.

Do not scaffold a framework simply to make the repository look active.

## Repository governance

Create `CLAUDE.md` and `AGENTS.md` with these rules:

- Danny is final product/specification acceptance authority.
- Claude Code owns repository editing and research execution.
- Codex is the independent reviewer.
- Claude Code and Codex must never write concurrently.
- Codex should review diffs, specifications, contradictions, unsupported assumptions, licensing conclusions, and architectural contamination.
- Codex must not silently repair Claude Code's work while acting as reviewer.
- Research findings must be captured in repository artifacts, not left only in terminal/chat output.
- Every material architectural decision must be traceable to evidence, an explicit reasoning record, or an ADR.
- External repositories are prior art, not authority.
- Numerical algorithms adopted from libraries must be independently testable against the Signal Current contract.
- No external library's internal ontology may silently become Signal Current's domain model.
- No asset/timeframe/platform-specific assumption may become an implicit core default.

## Prior-art research program

Execute PA-01 through PA-10 as a genuine research program.

Do not merely collect repository links.

For each serious candidate, create a report under:

`docs/research/candidate-reports/`

Each report must include:

- repository/project/paper name;
- URL;
- version, release, or commit reviewed;
- exact license;
- maintenance/activity assessment;
- architecture summary;
- relevant modules;
- tests and test quality;
- deterministic/reproducibility properties;
- asset/timeframe/venue assumptions;
- hidden defaults or semantic coupling;
- performance characteristics where evidence exists;
- what Signal Current can reuse;
- what Signal Current should not inherit;
- integration/coupling risks;
- required parity/golden tests;
- proposed disposition:
  - ADOPT
  - FORK
  - ADAPT
  - WRAP
  - REFERENCE
  - PARITY ORACLE
  - REJECT
- specification/ADR implications;
- confidence level;
- unresolved questions.

Prefer primary sources:
- official repositories;
- exact license files;
- official documentation;
- papers;
- standards;
- authoritative technical documentation.

Capture secondary commentary only when useful and label it as secondary.

## Initial prior-art candidates

At minimum investigate:

### Engines / systems
- NautilusTrader
- QuantConnect LEAN
- vectorbt
- Qlib
- Backtrader
- Zipline-reloaded
- QSTrader
- vn.py
- Jesse
- other serious projects discovered during research

### Indicators / numerical transforms
- TA-Lib
- other well-tested indicator libraries that materially differ

### Validation / financial ML
- skfolio
- mlfinpy
- MlFinLab as reference subject to licensing
- purged/CPCV implementations
- implementations or papers for PSR/DSR
- White Reality Check
- Hansen SPA
- block/stationary bootstrap approaches
- multiple-testing frameworks

### Portfolio / risk
- skfolio
- PyPortfolioOpt
- Riskfolio-Lib
- HRP/NCO implementations and references

### Search / optimization
- Optuna
- Nevergrad
- DEAP
- pymoo
- Ray Tune
- mature Bayesian/surrogate optimization libraries

### Data / time semantics
- Apache Arrow / Parquet
- DuckDB
- Polars
- exchange-calendars
- pandas-market-calendars
- mature corporate-action, futures-roll, and point-in-time patterns

### Provenance / orchestration
- MLflow
- DVC
- lakeFS
- OpenLineage
- Dagster
- Prefect
- Temporal
- Ray
- durable queue/event patterns

### Agentic research
- Qlib RD-Agent
- typed-tool/permission-oriented agent frameworks
- research automation systems that preserve deterministic tool boundaries

Do not stop at this list. Discover better candidates where warranted.

## Licensing posture

Be precise.

Permissive licenses such as MIT/BSD/Apache usually provide broad latitude, but always verify the exact repository license and dependency implications before recommending copy/fork/adoption.

LGPL/GPL/source-available/custom/no-license repositories require separate treatment.

Do not use the phrase “open source” as a substitute for license analysis.

Record exact license evidence in every serious candidate report.

## Specification work in parallel

While the prior-art research proceeds, draft:

`docs/specs/01-constitution.md`

The Constitution should be short and hard-edged. It should contain system invariants, authority boundaries, and prohibitions—not implementation libraries or arbitrary defaults.

Do not freeze System Architecture, Data/StrategyIR, Validation, Portfolio/Deployment, or Agent/Orchestration until their relevant prior-art research streams have materially informed them.

## Research philosophy

When a proven solution exists:

- do not rewrite it merely for ownership;
- do not adopt it merely for convenience;
- understand it;
- test it;
- identify its assumptions;
- decide whether to adopt, fork, adapt, wrap, use as reference, use as parity oracle, or reject it.

For critical numerical algorithms, the published mathematical or formal definition is the semantic reference whenever one exists.

Signal Current owns:
- the contract;
- the data semantics;
- the evidence model;
- the acceptance tests;
- the promotion rules;
- the provenance.

A library may own an implementation behind that boundary.

## First execution sequence

1. Initialize the greenfield repository.
2. Create repository governance files.
3. Ingest and relocate the supplied working artifacts without losing content.
4. Create the full docs/research/prior-art structure.
5. Create a source/research index.
6. Begin PA-01, PA-02, and PA-03 first because they most directly affect architecture and StrategyIR.
7. In parallel, draft `01-constitution.md`.
8. After the first meaningful research tranche, stop writing and run an independent Codex review.
9. Record Codex findings in the repository.
10. Reconcile findings before proceeding deeper into Architecture.

## First checkpoint

Do not report success merely because folders exist.

The first checkpoint is complete only when:

- repository structure exists;
- governance rules are committed;
- current Signal Current working artifacts are preserved;
- PA-01 through PA-10 are represented in-repo;
- candidate-report template exists;
- at least the first serious candidate reports are underway or completed;
- Constitution draft exists;
- Codex has independently reviewed the initial repo/spec/research structure;
- all blocking findings are recorded.

At that checkpoint, report:

1. repository tree;
2. files created/moved;
3. research completed;
4. candidate dispositions so far;
5. contradictions or risks discovered;
6. Codex findings;
7. exact next recommended action.

Do not begin product implementation without a later explicit v1.0 freeze.
