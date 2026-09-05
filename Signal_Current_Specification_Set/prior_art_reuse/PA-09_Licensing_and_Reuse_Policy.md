# PA-09 — Licensing & Reuse Policy

**Status:** Working policy for research and design decisions

## 1. Principle

License is part of architecture.

A technically excellent dependency may still be a poor Signal Current choice if its license constrains distribution, commercial use, modification, linking, hosted service use, or future product structure.

## 2. Reuse Postures

### Permissive licenses
Examples commonly include MIT, BSD-2-Clause, BSD-3-Clause, and Apache-2.0.

These generally give broad latitude to:
- use;
- modify;
- copy;
- redistribute;
- fork;
- include in commercial software,

subject to the exact license text and required notices/attribution.

Apache-2.0 also includes an explicit patent-license framework that can matter for dependency selection.

**Signal Current posture:** strongest candidates for ADOPT / FORK / ADAPT, assuming semantics and quality pass.

### Weak copyleft
Examples include LGPL-family licenses.

These can permit use in larger proprietary systems while imposing conditions around the covered library and modifications.

**Signal Current posture:** possible ADOPT / WRAP / ADAPT candidate, but architecture and distribution method must be reviewed before commitment.

### Strong copyleft
Examples commonly include GPL-family licenses.

Distribution and derivative-work obligations can materially affect product architecture.

**Signal Current posture:** usually REFERENCE / PARITY ORACLE unless deliberate product licensing decisions are made.

### Source-available / commercial / custom licenses
Public source code does not automatically mean open source.

Restrictions may apply to:
- commercial use;
- hosted services;
- redistribution;
- modification;
- competitive products;
- production deployment.

**Signal Current posture:** REFERENCE unless the exact license explicitly supports the desired reuse or a commercial license is obtained.

### No clear license / all rights reserved
Public visibility does not grant copying rights.

**Signal Current posture:** concepts, APIs, published behavior, mathematical ideas, tests we independently derive, and general architectural lessons may be studied where lawful; do not copy implementation code into Signal Current.

## 3. Copying vs Learning

Signal Current should distinguish:

**Allowed-by-design learning**
- algorithms described in papers/books;
- architectural patterns;
- public APIs and behavioral contracts;
- terminology;
- test ideas;
- benchmark methodology;
- edge cases discovered by reading implementations;
- independent reimplementation of public mathematical methods.

**Code reuse**
Requires license-compatible permission and preservation of all required notices/obligations.

**Independent implementation**
For REFERENCE-only projects:
- write from Signal Current’s own specification;
- use published algorithms/standards as semantic source;
- build our own golden fixtures;
- avoid copying protected implementation expression.

## 4. MIT / BSD / Apache Preference

Where two implementations are comparably strong, Signal Current SHOULD prefer the implementation with:
1. clearer semantics;
2. stronger tests;
3. healthier maintenance;
4. more permissive licensing;
5. lower coupling.

MIT/BSD/Apache implementations therefore often receive extra strategic value because they preserve optionality.

This preference does not override scientific correctness.

## 5. Dependency Record

Every dependency that can affect numerical evidence MUST record:
- exact package/repository;
- exact version or commit;
- license identifier;
- license text/notice location;
- hash or lockfile identity where practical;
- Signal Current adapter/contract;
- deterministic test coverage;
- upgrade policy.

## 6. Fork Policy

Fork only when at least one is true:
- upstream semantics are strong but Signal Current needs durable patches;
- release cadence creates unacceptable reproducibility risk;
- a small critical dependency must be pinned beyond upstream lifecycle;
- performance or determinism changes are strategically important;
- Signal Current requires auditable control of a core algorithm implementation.

Forking is not automatically better than wrapping. A fork creates maintenance responsibility.

## 7. Research Rule

Do not classify a repository as safe to copy or fork solely from the word “open source” in conversation, README prose, or community descriptions. The exact repository license and relevant dependency licenses must be recorded before reuse decisions are frozen.
