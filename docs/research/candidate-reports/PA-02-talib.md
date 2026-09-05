# Candidate Report — TA-Lib (C core + Python wrapper)

**Stream:** PA-02 (Numerical & Statistical Methods), directly relevant to Data Architecture & Strategy IR's indicator-convention needs.
**Status:** First candidate report for PA-02.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE files at both repos, README, direct source read of `ta_RSI.c`, commit history on both repos) — not general knowledge recall.

## 1. Repositories / canonical URLs / relationship

C core: `github.com/TA-Lib/ta-lib` ("Official TA-Lib Core," created 2016, mirrors the legacy SourceForge ta-lib.org project). Python wrapper: `github.com/TA-Lib/ta-lib-python` (created 2012, Cython bindings around the C library, not a pure-Python reimplementation). The Python package requires the C library to be compiled/installed separately (confirmed via README: "Cannot find ta-lib library, installation may fail" warning). Both live under the same `TA-Lib` GitHub org.

## 2. Version / commit reviewed

Latest tagged release both repos: `v0.7.1` (ta-lib-python published 2026-07-16; ta-lib C published 2026-07-03). The C repo has continued past v0.7.1 with untagged commits through 2026-09-05 (`972c5cc`).

## 3. License — historical ambiguity confirmed, now resolved

ta-lib-python `LICENSE` = BSD-2-Clause (verified via GitHub API SPDX field and direct file fetch). ta-lib (C) `LICENSE` = BSD-3-Clause, copyright "Mario Fortier," year bumped to 2026 in commit `2b4d8c7` (2026-07-20).

**Real historical finding, matching the "ambiguous license" recollection**: the C repo's commit history shows `fefc40b` "Add BSD 3 license file" dated **2024-10-02** — i.e., a machine-readable, SPDX-recognized LICENSE file did not exist in this repo until October 2024. Before that, licensing text lived in a differently-formatted `LICENSE.TXT`/source-header form that GitHub's license detector could not classify — consistent with long-standing community reports (FOSSA/ClearlyDefined-style flags) of non-standard license text on the classic ta-lib.org distribution. ta-lib-python similarly normalized its LICENSE file wording in commit `9d78f10` (2025-08-25, "changed the LICENSE text to a standardized format and added license badge"), implying the pre-2025-08 text was non-standard even though SPDX-classified as BSD-2.

**Conclusion: currently BSD-2 (Python) / BSD-3 (C), both permissive and compatible — but this is a recent (2024–2025) cleanup, not the long-standing state.** Treat "TA-Lib license" as version-dependent metadata to re-check against a pinned commit/tag, not a fixed fact.

## 4. Maintenance / activity — actively maintained, overturns "old/dormant" assumption

C repo: commits as recent as the day of this research (2026-09-05), including brand-new indicators merged same-day (Williams Fractal, Heikin-Ashi, RVI; Vortex Indicator; Elder Ray Index; Kaufman Efficiency Ratio; TSI/KDJ) — active PR-driven feature development, 23 contributors, 15 open issues. Python wrapper: last commit 2026-08-29, 6 releases in the last ~14 months, 30 contributors, 137 open issues, 12.2k stars/2k forks. **Both repos are healthy, not legacy/abandoned** — the age-based skepticism this report was tasked to apply came back negative (i.e., the project is fine).

## 5. Architecture summary

C core implements each indicator once in `src/ta_func/*.c`; language bindings (Python via Cython, historically SWIG-based for other languages) wrap the C ABI. Three Python-facing APIs per README: Function API (raw NumPy arrays), Abstract API (uniform metadata/DataFrame-friendly), and an experimental Streaming API computing only the latest value for live/streaming use. Batch and incremental modes both exist, but streaming is explicitly labeled experimental — do not treat it as production-grade.

## 6. Coverage

Per README: Overlap Studies (17), Momentum (28, incl. RSI/MACD/Stochastic), Volume (3), Cycle/Hilbert Transform (5), Price Transform (4), Volatility (3), Pattern Recognition (61 candlestick patterns) — plus new indicators actively being added upstream in the C library (Vortex, TSI, KDJ, Elder Ray, RVI, Williams Fractal, Efficiency Ratio, as of September 2026) not yet necessarily exposed in the Python wrapper (unresolved, §18).

## 7. Tests and test quality — gap, not a claim

Directory structure confirmed (`src/ta_func`, `ta_abstract`, `ta_common`, `tools` in the C repo); test file contents were not directly enumerated this session. Flagged as a gap.

## 8. Deterministic / reproducibility properties

Pure numeric C functions operating on arrays — deterministic given identical inputs/compiler/optimization flags. A byte-identical-output test across platforms/versions was not run this pass (recommended in §15).

## 9. Asset / timeframe / venue assumptions

No timeframe/venue-specific logic found in the modules inspected — indicators are asset/timeframe-agnostic array transforms (function signatures take raw price arrays + period params only).

## 10. Hidden defaults or semantic coupling — the central finding: real, verified convention ambiguity

**Directly verified for RSI** by fetching `ta_RSI.c` from the C repo: TA-Lib's RSI uses **Wilder's smoothing**, explicit in-code comment: "Subsequent prevLoss and prevGain are smoothed using the previous values (Wilder's approach): multiply previous by (period-1), add today's value, divide by period" — i.e., `prevLoss *= (period-1); prevLoss /= period`, seeded by a simple arithmetic mean over the first period. **This is not a standard EMA** (`α = 2/(N+1)`); Wilder's implicit smoothing constant is `α = 1/N`. This is exactly the ambiguity class this report was tasked to check — libraries/authors that assume "EMA-style RSI" will get numerically different results than TA-Lib's Wilder-based RSI, and ATR has the same historical Wilder-vs-EMA distinction (not independently re-verified for ATR this pass, see §18). Also confirmed via README: STOCHRSI is explicitly **not** equivalent to STOCH(RSI(...)) — a documented gotcha, not a bug, but another convention trap.

**This is directly load-bearing for Signal Current's own Reconciliation Matrix row 11 (StrategyIR must support composable, typed primitives with explicit semantic roles) — any indicator convention Signal Current adopts must be versioned and cited exactly this precisely, or downstream strategies will silently diverge based on which "RSI" implementation was assumed.**

## 11. Performance characteristics

README claims the Cython/NumPy binding is "2-4x faster than the SWIG interface" — self-reported, not independently benchmarked this session.

## 12. What Signal Current could reuse

The C core's battle-tested numeric kernels (especially the Wilder-family and overlap studies) as a computation backend or as a golden-reference oracle; the Abstract API's per-function metadata model as a pattern for a versioned indicator-definition schema.

## 13. What Signal Current should not inherit

Silent trust in "BSD, it's fine" without pinning the exact commit/tag — the license is version-dependent metadata, not a fixed fact, per §3. Implicit Wilder-vs-EMA conventions without an explicit, documented, versioned convention doc of Signal Current's own. Do not treat the Streaming API as production-grade — README itself labels it experimental.

## 14. Integration / coupling risks

Native C compilation dependency (a separate install step per OS) is an operational burden. The Python wrapper trails the C core's newest indicators — the September 2026 C additions are likely unexposed in the wrapper yet (unresolved, needs a direct check against the current wrapper's function-list file). Single maintainer for the reference implementation's formula documentation.

## 15. Required parity / golden tests if adopted

- Golden-value tests comparing TA-Lib's RSI/ATR/ADX (all Wilder-smoothed) against (a) the original Wilder 1978 published formula and (b) any EMA-based alternative library Signal Current might also use, across a range of periods including edge cases (period=1, insufficient lookback/"unstable period" — note commit `f69d740`, "Clarify RSI lookback and unstable-period defaults in the docs," itself evidence this is a known footgun upstream).
- A STOCHRSI-vs-STOCH(RSI) divergence test.
- A compiled-binary determinism test: same inputs/same TA-Lib version/build → byte-identical outputs across platforms.

## 16. Proposed disposition

**PARITY ORACLE** (primary) + **WRAP** (secondary, if operationally acceptable). Use pinned-version TA-Lib C output as the versioned reference/ground-truth oracle for Wilder-family indicators specifically because its convention is explicit, documented, and widely deployed — but do not adopt it as Signal Current's sole runtime indicator engine without isolating the native-dependency risk, and **do not conflate "matches TA-Lib" with "matches the original published formula"** — test against both.

## 17. Confidence level

**MEDIUM-HIGH.** License, release/commit history, the RSI smoothing formula, and README architecture claims were each verified against primary sources. Test-suite quality and current wrapper-vs-core feature parity were not independently verified this session — genuine gaps, not assumptions.

## 18. Unresolved questions

- Does the current Python wrapper expose the brand-new C-side indicators added in the last 48 hours (Vortex, TSI, KDJ, Elder Ray, RVI)? Not checked.
- What exactly did the pre-Oct-2024 `LICENSE.TXT` text say — was it truly non-standard/restrictive, or just unrecognized formatting? Not directly read, only inferred from commit messages.
- Actual test coverage/quality in `src/ta_func` — not inspected.
- ATR's Wilder-smoothing implementation was not directly fetched this pass (only RSI was) — should be verified with the same rigor before Signal Current relies on it for any golden test.

## Sources

- https://github.com/TA-Lib/ta-lib-python
- https://github.com/TA-Lib/ta-lib
- https://api.github.com/repos/TA-Lib/ta-lib-python/contents/LICENSE (BSD-2-Clause)
- https://api.github.com/repos/TA-Lib/ta-lib/contents/LICENSE (BSD-3-Clause)
- https://github.com/TA-Lib/ta-lib/commit/fefc40b ("Add BSD 3 license file," 2024-10-02)
- https://github.com/TA-Lib/ta-lib-python/commit/9d78f10 ("changed the LICENSE text to a standardized format," 2025-08-25)
- https://raw.githubusercontent.com/TA-Lib/ta-lib/main/src/ta_func/ta_RSI.c (Wilder's smoothing code/comment)
- https://github.com/TA-Lib/ta-lib-python/blob/master/README.md
- Recent C-repo commits: `8c0fedb`, `c7cfdc0`, `8a7b83b`, `404165a`, `e3737b3` (2026-09-05)
