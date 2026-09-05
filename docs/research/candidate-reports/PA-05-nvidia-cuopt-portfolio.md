# Candidate Report — NVIDIA Quantitative Portfolio Optimization Blueprint (cuOpt-based)

**Stream:** PA-05 (Portfolio/Risk)
**Status:** Second candidate report for PA-05.
**Researched:** 2026-09-05, via WebFetch/WebSearch against the GitHub repo, a raw LICENSE fetch, and NVIDIA's own blueprint/blog pages. **Lower confidence than most reports in this program** — several findings rest on LLM-summarized page fetches rather than raw file reads or `gh api` calls; this is flagged explicitly per finding below rather than smoothed over.

## 1. Repository / project / nature

`github.com/NVIDIA-AI-Blueprints/quantitative-portfolio-optimization` — a real, NVIDIA-owned open-source example repo ("developer blueprint"), built on top of NVIDIA's own solver `github.com/NVIDIA/cuopt` (a separate repo, the actual GPU LP/QP/MIP/VRP engine).

**A genuine, unresolved ambiguity found during this research**: GitHub search also surfaced `github.com/NVIDIA-AI-Blueprints/cuFOLIO`, described as "GPU-accelerated portfolio optimization toolkit... with NVIDIA cuOpt." Whether this is a rename, a fork, or a distinct successor project of `quantitative-portfolio-optimization` was **not resolved this pass** — needs a direct visit to both repo pages before either is cited as canonical.

## 2. Version / commit reviewed — unresolved

The repo has no GitHub Releases (confirmed via direct fetch of the `/tags` page: "There aren't any releases here"). The local capture's "Version: 25.10" does not correspond to any tagged release found; it may refer to an NVIDIA NGC container tag (`nvcr.io/nvidia/pytorch:25.10-py3`) rather than a repo version — a container/date tag, not a software version. This distinction was not verified further. A commit-count figure (229 commits on main) came from a summarized fetch, not a raw `git log` — lower confidence.

## 3. License — the one claim verified against a raw primary source

Fetched `https://raw.githubusercontent.com/NVIDIA-AI-Blueprints/quantitative-portfolio-optimization/main/LICENSE` directly: full, unmodified **Apache License 2.0** text, copyright NVIDIA Corporation. No NVIDIA-specific EULA clauses present in that file. **This covers the blueprint repo's own source code only** — it does not by itself cover the `cuopt-cu12`/`cuopt-cu13` PyPI packages, a separate distribution channel (see §13).

## 4. Maintenance / activity — medium confidence, summary-sourced

Reported (via summarized fetch, not independently re-verified with `gh api`): 485 stars, 107 forks, 7 watchers, 229 commits, CI badges present. `NVIDIA/cuopt` itself shows much larger activity (1,000+ stars, 1,118 commits, nightly builds) — consistent with cuOpt being the mature engine and this blueprint being a thin example layer on top.

## 5. Architecture summary — what "Mean-CVaR + cuOpt" actually means

Per the summarized README: a three-stage pipeline — (a) cuML-based scenario generation (KDE-based return-scenario sampling on GPU), (b) **cuOpt used as the optimization solver** for the scenario-based Mean-CVaR problem (CVaR formulated as a linear program over sampled scenarios — a standard Rockafellar-Uryasev LP formulation, solved by cuOpt's LP capability), (c) a backtesting layer. **This is a wrapper/example application built on top of cuOpt's LP solver, not a new solver** — cuOpt itself is domain-agnostic (LP/QP/MIP/VRP); the blueprint supplies the finance-specific scenario generation, CVaR-LP formulation, and backtest harness.

## 6. Relevant modules / functions — gap, not independently inspected

Only repo-level structure was surfaced (a `tests/` directory reported to exist; notebooks `cvar_basic.ipynb`, `efficient_frontier.ipynb`, `rebalancing_strategies.ipynb` per the local capture). **No source file was actually opened or read this pass.** Anyone adopting this should clone and read the actual solver-invocation code directly, not rely on this summary.

## 7. Tests and test quality — insufficient verification

A `tests/` directory's presence was reported by a summarized fetch only. No test file content, count, or CI pass/fail history was actually read. Cannot assess test quality.

## 8. Deterministic / reproducibility properties — unaddressed anywhere found

Neither the local file nor the fetched pages mention GPU floating-point non-determinism, seed control for the KDE/scenario sampling, or CVaR-LP solver determinism. This is a real open question for a project (Signal Current) whose stated value is reproducible backtests — treat as unresolved, not handled.

## 9. Asset / timeframe / venue assumptions

Data integration uses `yfinance` (included), with Bloomberg/custom feeds as options; no venue/broker is built in ("Live Trading: ❌ No," consistent across the local file and the fetched comparison table). No timeframe (daily/intraday) constraint confirmed from primary source.

## 10. Hidden defaults or semantic coupling — real hardware coupling found

The CUDA compute-capability floor (≥7.0 minimum, ≥9.0 "recommended" for H100) is a real hardware coupling baked into what the solver can practically be used for — exactly the "engineering default promoted into a scientific/architectural constraint" pattern this project's global rules warn about: if Signal Current's own hardware sits below CC 9.0, the "160x" performance number does not transfer, and no source found gives a CC-7.0-vs-9.0 performance breakdown.

## 11. Performance characteristics — vendor-reported only, not independently verifiable

All performance figures (100x scenario generation, 160x Mean-CVaR optimization, RTX 4090/3090/3060 speedup tiers) trace to NVIDIA's own blueprint page and blog post. **No third-party benchmark, academic paper, or independent reproduction of these numbers was found.** They are purely vendor-reported, compared against an unspecified "CPU baseline" (solver, problem size, and hardware for the CPU side were not disclosed in anything fetched). **Per this project's own data-integrity rule, these are unsourced-beyond-vendor numbers and must not be cited as fact** — they are marketing claims, not verified benchmarks.

## 12. What Signal Current could reuse

The CVaR-as-scenario-LP formulation pattern (well-established, Rockafellar-Uryasev — not novel to NVIDIA) as a reference formulation to reimplement independently; the general three-stage architecture shape (scenario generation → optimization → backtest) as a design pattern, decoupled from any specific vendor solver.

## 13. What Signal Current should not inherit

- **Hardware lock-in — confirmed real**: cuML/cuOpt require an NVIDIA GPU with CUDA CC≥7.0; the blueprint explicitly states no AMD/CPU-only path exists.
- **A live, unresolved license ambiguity**: the local capture describes `cuopt-cu12`/`cuopt-cu13` PyPI packages as "NVIDIA proprietary packages from PyPI (nvidia index)," while a separately-fetched summary described cuOpt's own GitHub repo as "fully open source under Apache-2.0... no proprietary or EULA components." **These two characterizations are in tension and were not resolved this pass.** Do not assume the Apache-2.0 source license automatically covers the compiled PyPI wheels without checking the wheel's own metadata/LICENSE directly — this is exactly the "NVIDIA SDK EULA distinct from code license" risk pattern this report was tasked to check for, and it remains open.
- The unverified vendor speedup multipliers (§11) as planning inputs.

## 14. Integration / coupling risks

Tight coupling to RAPIDS (cuDF/cuML) + cuOpt + specific CUDA/driver versions (13.0, driver ≥580.65.06) + Linux-only + an NGC container base image — a substantial infrastructure dependency chain for a system that otherwise relies on deterministic, portable tooling.

## 15. Required parity / golden tests if used as reference

A CPU-only reference implementation of the same Mean-CVaR scenario-LP (e.g., via `cvxpy` + an open solver) run on a fixed, hashed scenario set, with results checked against cuOpt's output within a defined tolerance — necessary precisely because determinism (§8) is unaddressed and performance claims (§11) are unverified.

## 16. Proposed disposition

**REFERENCE** — not ADOPT/FORK/WRAP. The underlying math (Mean-CVaR scenario LP) is standard and reimplementable without vendor lock-in; the hardware/package coupling is disqualifying for ADOPT/FORK/WRAP if Signal Current aims for portability (which its "no privileged... deployment target" invariant implies); but the architecture shape and NVIDIA's own worked notebooks are legitimate design references to consult while writing an independent, hardware-agnostic spec.

## 17. Confidence level

**MEDIUM.** HIGH confidence on the license text (raw file fetch) and the CUDA/GPU hardware requirement (corroborated across multiple sources). MEDIUM-LOW confidence on version/commit identity, test quality, star/commit counts, and the cuFOLIO-vs-quantitative-portfolio-optimization relationship — these rest on LLM-summarized WebFetch output, not raw file reads or `gh api` calls. **A follow-up pass should re-run with `gh api repos/.../releases`, an actual `git clone`, and `ls tests/` to convert these from summary-sourced to raw-verified before any code-level reliance.**

## 18. Unresolved questions

- Is `cuFOLIO` the successor/rename of `quantitative-portfolio-optimization`, or a distinct project?
- What license actually governs the `cuopt-cu12`/`cuopt-cu13` PyPI wheels specifically, versus the cuOpt source repo?
- What CPU baseline (hardware, solver, problem size) underlies the 100x/160x claims?
- Does the KDE scenario generation or cuOpt's LP solve have any documented non-determinism across GPU runs?
- What repo state does "Version: 25.10" in the local capture actually refer to, given no GitHub releases exist?

## 19. Did the local capture's specific claims hold up?

**Apache-2.0 license: held up** for the blueprint repo's own source (independently confirmed via raw LICENSE fetch), **but the local file's characterization of `cuopt-cu12`/`cuopt-cu13` PyPI packages as "NVIDIA proprietary" sits in direct tension with a separately-fetched summary describing cuOpt's own GitHub repo as "fully open source... no proprietary or EULA components."** This is a live contradiction between two sources fetched this session, not yet resolved — check the actual PyPI package page before anyone relies on either characterization. **GPU-accelerated Mean-CVaR blueprint description: held up** as an accurate description of what the repo does. **Performance numbers (100x/160x): not independently verifiable** — confirmed only as NVIDIA's own vendor claim, exactly the caution the local file itself and this report's brief both anticipated.

## Sources

- https://github.com/NVIDIA-AI-Blueprints/quantitative-portfolio-optimization
- https://raw.githubusercontent.com/NVIDIA-AI-Blueprints/quantitative-portfolio-optimization/main/LICENSE (fetched directly)
- https://github.com/NVIDIA-AI-Blueprints/cuFOLIO
- https://build.nvidia.com/nvidia/quantitative-portfolio-optimization
- https://github.com/NVIDIA/cuopt
- https://github.com/NVIDIA-AI-Blueprints/quantitative-portfolio-optimization/tags (confirmed: no releases)
- Local file: `/home/d-tuned/life/resources/AlgoTradingIdeas/nvidia-quant-portfolio-opt.md` (treated throughout as signpost, not settled fact)
