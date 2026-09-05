# Candidate Report — mlfinlab (hudson-and-thames/mlfinlab)

**Stream:** PA-02 (Numerical & Statistical Methods)
**Status:** Third candidate report for PA-02, following up on `PA-02-ml-finance-codes.md`'s finding that the homelab capture's "López de Prado reference code" was misattributed.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, `LICENSE.txt`, README, Snyk package health page) — not general knowledge recall.

## 1. Repository / canonical URL / relationship to López de Prado

`hudson-and-thames/mlfinlab` — https://github.com/hudson-and-thames/mlfinlab. Org-owned, public, description "MlFinLab helps portfolio managers and traders who want to leverage the power of machine learning." **This is a third-party reimplementation** of methods from López de Prado's *Advances in Financial Machine Learning*, built by Hudson & Thames Quantitative Research (started as a WorldQuant University research project). López de Prado has given a public testimonial ("compiles important algorithms that every quant should know and use"), but there is **no evidence of authorship, code contribution, or ownership stake** — he does not appear in the contributors list (`gh api .../contributors`: top contributors are `PanPip`, `vlrie`, `Jackal08`). **Treat as endorsed-by, not authored-by.**

## 2. Version / commit reviewed

Default branch `master`, HEAD commit `79dcc7120ec84110578f75b025a75850eb72fc73`, committer date **2021-12-01T08:04:50Z** ("Readme update" merge, PR #511) — the most recent real commit on master. Repo `pushed_at` shows 2023-10-02, but the only branches besides `master` are three Dependabot pip-bump branches (`joblib-1.2.0`, `numpy-1.22.0`, `tensorflow-2.11.1`) — the "recent activity" implied by `pushed_at` is automated dependency-bump noise, not feature development on master.

## 3. License — the central finding: this is not open-source

Read directly from `LICENSE.txt`: **not an OSI license.** GitHub's own `license.key` field returns `"other"` / `spdx_id: "NOASSERTION"`. The file is titled "Copyright Protection Notice and Licensing Agreement," last updated November 2021, and is a proprietary end-user agreement referencing "Business or Enterprise License Agreement," subscription-based licensing, and — a notable sloppiness signal — an "ArbitrageLab Business License Agreement" template reused verbatim for mlfinlab (a different Hudson & Thames product's license text left in place). README.md states explicitly: *"This project is licensed under an all rights reserved licence... Licensing options: Business / Enterprise"* and *"With the purchase of the library, our clients get access to the Hudson & Thames Slack community."*

**Confirmed: mlfinlab has shifted to a commercial/paid, all-rights-reserved model. It is not free/open-source today, regardless of what any earlier PyPI release implied.**

## 4. Maintenance / activity — public repo is a decoy front-end

- Last PyPI release: **0.4.1, 2019-09-04** — no releases since, per Snyk's package health page, which flags the project "Inactive."
- Last substantive commit to public master: 2021-12-01.
- README itself states: *"This repo is public facing and exists for the sole purpose of providing users with an easy way to raise bugs, feature requests, and other issues"* — **the public GitHub repo is explicitly an issue-tracker front end; real development has moved to a private, paid distribution channel** (`portal.hudsonthames.org`).
- Consistent with this: the public repo's module tree contains **no visible `tests/` directory** — only a stray `backtest_statistics` module — suggesting the test suite was stripped from what's shown publicly, or was never public.

## 5. Architecture summary / 6. Modules present

Enumerated directly from the repo's file tree (`mlfinlab/<module>/*.py`): `data_structures` (standard/time/imbalance/run bars — tick/volume/dollar/imbalance bars), `labeling` (`labeling.py`, `fixed_time_horizon.py`, `trend_scanning.py`, `tail_sets.py` — triple-barrier and meta-labeling live in `labeling.py`/`bet_sizing`), `bet_sizing` (`ch10_snippets.py`, `ef3m.py` — book-chapter-numbered filenames, consistent with direct book-chapter mapping), `sampling` (`concurrent.py`, `bootstrapping.py` — sample-uniqueness weighting), `sample_weights/attribution.py`, `cross_validation/combinatorial.py` (CPCV), `clustering/hierarchical_clustering.py` + `onc.py` (HRP-adjacent, ONC), `backtest_statistics/` (PBO-type overfitting stats), `structural_breaks` (CUSUM/Chow/SADF), `microstructural_features`, `codependence`, `networks` (MST/ALMST/PMFG), `features/fracdiff.py`.

**No explicit standalone `hrp.py` or `nco.py` file was found in the top-level tree pulled this pass** — HRP/NCO likely live under `clustering` or a `portfolio_optimization` submodule not fully captured. **Flagged unresolved, not asserted** — a second, deeper tree pull is needed before Signal Current cites specific HRP/NCO file paths.

## 7. Tests and test quality — unresolved

No test files surfaced in the public tree. Given the license shift, the real test suite is plausibly gated behind the paid subscription. **Do not assume the public repo is test-covered without direct verification.**

## 8-11. Reproducibility / asset assumptions / hidden defaults / performance — not verified this pass

Not verifiable from what was fetched — would require pulling actual module source, a second research pass. Flagged as open rather than guessed.

## 12. What Signal Current could reuse

Conceptual reference only — the chapter-to-module mapping is a useful map of "what López de Prado's published methods look like as code," useful for orienting a read of the original book chapters, not as a code source.

## 13. What Signal Current should not inherit

The license (proprietary, all-rights-reserved, no redistribution) — any code copied from this repo post-November-2021-license-change is a legal risk. Also do not inherit its "public repo ≠ real source" pattern as a model for anything.

## 14. Integration / coupling risks

HIGH if anyone imports the stale pip package (`0.4.1`, 2019) — pre-dates the license change, uncertain if it's even legally usable now.

## 15. Required parity / golden tests if used as reference

If used as a conformance comparator at all, Signal Current would need to independently re-derive formulas from the original book itself (not from this repo) to avoid the "shared well" problem this project's global rules explicitly warn against — this repo cannot certify itself against a source it may not even faithfully implement anymore, and its current state (license-gated, real tests possibly hidden) means it can't be independently audited as a ground truth.

## 16. Proposed disposition

**REJECT as a dependency; REFERENCE only** (read-only, for module-naming/architecture ideas), with a mandatory legal check before any code is copied verbatim, if that is ever considered.

## 17. Confidence level

**MEDIUM.** High confidence on license status, PyPI staleness, and master's last-commit date — all directly read from primary sources (GitHub API, `LICENSE.txt`, README, Snyk). Lower confidence on exact HRP/NCO file locations and test-suite existence/quality — unresolved, needs a follow-up fetch of the full source tree before Signal Current writes anything assuming specific functions exist here.

## 18. Unresolved questions

- Exact HRP/NCO file locations within `clustering/` or a possible `portfolio_optimization` submodule — not confirmed this pass.
- Whether any test suite exists at all (public or paid-tier).
- What repo, if any, is López de Prado's own personally-maintained canonical code (separate from this third-party reimplementation) — not established. It's plausible no such single canonical repo exists; his published books/papers themselves may be the only true canonical source, meaning any code-level parity check should derive formulas from the book text directly rather than trusting any third-party implementation, including this one and skfolio's.

## Sources

- https://github.com/hudson-and-thames/mlfinlab
- https://raw.githubusercontent.com/hudson-and-thames/mlfinlab/master/LICENSE.txt
- https://snyk.io/advisor/python/mlfinlab
- https://hudsonthames.org/a-laboratory-for-machine-learning-in-finance/
- https://pypi.org/project/mlfinlab/ (fetch blocked by client-side JS wall; version/date corroborated via Snyk instead, secondary not primary confirmation)
