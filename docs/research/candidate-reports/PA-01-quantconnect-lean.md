# Candidate Report — QuantConnect LEAN

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Second candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (live repo, LICENSE file, README, GitHub API/`gh api`) — not general knowledge recall.

**This report is partial by design, not by oversight.** License, repo metadata, and commit history are fully verified against primary sources. Architecture, determinism, asset-neutrality, and hidden-coupling claims (§5, §8, §9, §10, §11) were **not** verified against actual source code this pass — only inferred from README prose and directory names — and are marked LOW confidence accordingly. A follow-up pass reading `Engine/`, `Common/`, and `Data/` source is required before any Signal Current spec doc cites LEAN's architecture as precedent.

## 1. Repository / canonical URL

`QuantConnect/Lean` — https://github.com/QuantConnect/Lean (confirmed via `gh api repos/QuantConnect/Lean`).

## 2. Version / commit reviewed

**No recent tagged release exists.** `gh api repos/QuantConnect/Lean/releases/latest` returns tag `v2.4.0.1`, published 2017-08-08; the tags list shows the same stale 2017-or-earlier set. LEAN has not used GitHub Releases/tags since ~2017 despite continuous active development — versioning is effectively rolling-on-`master`, not semver-tagged. Reviewed **`master` HEAD, commit `23b735d99a357807dc0df9f4c51d30f05fe0d277`, committed 2026-09-04T14:52:27Z**.

## 3. License — verified, one open item

`LICENSE` at master (raw-fetched) is the full **Apache License 2.0** text; GitHub's own SPDX detection agrees (`license.spdx_id: "Apache-2.0"`). Checked commit history on the LICENSE file specifically: only two commits ever touch it — the 2015-01-12 initial commit and a 2015-07-10 "Update LICENSE" commit. **The before/after content of that 2015-07-10 commit was not diffed** (pre-commit blob not fetched), so a license-family change at that single point cannot be fully ruled out — but current state is unambiguously Apache-2.0 and has been stable since at least mid-2015. No GPL/LGPL history found anywhere in the record (unlike NautilusTrader's GPLv3→LGPLv3 2020 change, see `PA-01-nautilustrader.md`).

## 4. Maintenance / activity

- Dense, current commit activity: last 30 commits span 2026-08-18 to 2026-09-04, near-daily.
- 21,488 stars, 5,220 forks, 490 subscribers, `open_issues_count` 259 (this GitHub field bundles issues+PRs together; a clean is:issue/is:pr breakdown via Search API 404'd this session — likely a token/scope limitation, unresolved).
- Contributor count ≈211 distinct logins via paginated `/contributors` — GitHub's contributors endpoint caps/samples, so treat as a lower-bound approximation, not exact.
- README references active "Build & Test Lean" and "Regression Tests" GitHub Actions workflows.

Overall: actively, continuously maintained, but **not tagged/released conventionally since 2017** — any dependency on LEAN must pin a commit SHA and periodically re-verify against a fast-moving `master`, not track a version number.

## 5. Architecture summary — LOW confidence, inferred only

README states: "LEAN is an event-driven, professional-caliber algorithmic trading platform... Out-of-the-box alternative data and live-trading support," and "LEAN is modular in design, with each component pluggable and customizable." Top-level repo structure confirms real module separation: `Algorithm`, `Algorithm.Framework`, `Algorithm.CSharp`, `Algorithm.Python`, `AlgorithmFactory`, `Engine`, `Brokerages`, `Data`, `Indicators`, `Common`, `Optimizer`, `Report`, `Research`, `Tests`, `ToolBox`, `Api`, `Queues`, `Messaging`. This confirms an event-driven engine with a pluggable algorithm-framework layer and dual C#/Python language bindings. **Source inside `Engine/` or `Common/` was not read this pass** — specific claims about event/time semantics, multi-timeframe consolidation, or backtest/live parity mechanics are inferred from directory naming and README marketing copy only, not verified.

## 6. Relevant modules (directory presence only, contents unread)

`Engine/`, `Brokerages/`, `Data/`, `Indicators/`, `Algorithm.Framework/`, `Optimizer/` + `Optimizer.Launcher/`, `Report/`, `ToolBox/` (data conversion utilities), `Tests/`.

## 7. Tests and test quality

README badges reference two distinct CI workflows ("Build & Test Lean" and a separate "Regression Tests" workflow), plus a top-level `Tests/` directory and `run_syntax_check.py` / `compare_benchmarks.py` at repo root. README states "All code submissions must include accompanying tests." **`Tests/` contents were not opened, no coverage was measured** — verified only at the directory/README-claim level.

## 8. Deterministic / reproducibility properties — unresolved

Not verified from primary source this pass. README makes no explicit determinism claim, and `Engine/` time-loop code was not read. Needs source-level investigation before any claim — do not assume from reputation.

## 9. Asset / timeframe / venue assumptions — unresolved, flagged

Not independently verified against source beyond README's "multiple financial markets" / "out-of-the-box alternative data" claim. Given LEAN's historical roots in equities/futures via QuantConnect's cloud platform, this needs direct inspection of `Common/`/`Data/` instrument definitions before any "asset-agnostic" claim is accepted. **Do not assume genuine asset-neutrality** — this is exactly the kind of reputation-based assumption this project's rules prohibit.

## 10. Hidden defaults or semantic coupling — unresolved

Not verified at source level. Worth flagging: README explicitly markets "Local-Cloud Hybrid Development" and QuantConnect's own CLI/Docker/cloud data workflow as the *recommended* path ("For most users we strongly recommend the LEAN CLI") — circumstantial evidence of documentation-level coupling to QuantConnect's commercial data/cloud services. Whether this is baked into open-source core code paths (vs. just recommended tooling) was not confirmed — needs a source read of `Data/` providers and `DownloaderDataProvider/`.

## 11. Performance characteristics — unresolved

Repo root contains `run_benchmarks.py` and `compare_benchmarks.py`, implying an internal benchmarking harness exists, but no published benchmark numbers were located or read this pass.

## 12. What Signal Current could reuse

The modular plug-in taxonomy itself — algorithm framework separated from engine, brokerages, data providers, indicators — is a reasonable architectural reference point even without code reuse: a proven separation-of-concerns shape, confirmed by real directory structure, not just marketing copy.

## 13. What Signal Current should not inherit

Cannot be stated definitively yet — asset-class/venue assumptions and cloud-service coupling are unverified (§9, §10). The honest finding here is: **don't inherit anything from LEAN's domain model until `Data/`, `Common/`, and `Engine/` are actually read.** This is the same failure mode this project's global rules exist to prevent — treating an unverified claim ("asset-agnostic") as settled.

## 14. Integration / coupling risks

Apache-2.0 is permissive and low-risk for adaptation/reference use (attribution + NOTICE preservation only) — the one claim in this report at full confidence. Absence of semver releases since 2017 means any dependency must pin a commit SHA and periodically re-verify against `master`.

## 15. Required parity / golden tests if used as a conformance comparator

Cannot be specified responsibly yet — would require first reading `Engine/`'s event-loop and `Data/`'s subscription/consolidator code to know what to hold as ground truth.

## 16. Proposed disposition

**REFERENCE only, pending deeper source-level investigation.** Not ADOPT/FORK/ADAPT/WRAP/CONFORMANCE COMPARATOR yet — those all require verifying §5, §8, §9, §10 against actual source, which this pass did not do.

## 17. Confidence level

**LOW-MEDIUM overall.** HIGH confidence on: license (Apache-2.0, verified via file + history + GitHub SPDX), canonical repo identity, commit-activity recency, absence of recent tagged releases. LOW confidence on all architecture/determinism/asset-neutrality/coupling claims (§5, §8, §9, §10, §11) — inferred from README prose and directory names only, not verified against source.

## 18. Unresolved questions

- Read `Engine/` (time loop, event ordering) to confirm or deny determinism and backtest-vs-live parity claims.
- Read `Common/`/`Data/` instrument and subscription model to check for equities/futures-centric assumptions leaking into "core" abstractions.
- Read `DownloaderDataProvider/` and data-provider interfaces to check for hardcoded coupling to QuantConnect's own cloud data formats/services.
- Confirm the actual content diff of the single 2015-07-10 "Update LICENSE" commit — existence confirmed, before/after blob not diffed.
- Get an accurate open-issue vs open-PR breakdown (Search API calls 404'd this session).
- Locate and read actual benchmark output from `run_benchmarks.py`/`compare_benchmarks.py` if published anywhere.

## Sources

- https://github.com/QuantConnect/Lean
- https://raw.githubusercontent.com/QuantConnect/Lean/master/LICENSE
- https://raw.githubusercontent.com/QuantConnect/Lean/master/readme.md
- `gh api repos/QuantConnect/Lean/commits/master`
- `gh api "repos/QuantConnect/Lean/commits?path=LICENSE"`
- `gh api repos/QuantConnect/Lean/releases/latest`, `/tags`
- `gh api repos/QuantConnect/Lean/contributors`
- `gh api repos/QuantConnect/Lean/contents`
