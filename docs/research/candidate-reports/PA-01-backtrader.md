# Candidate Report — Backtrader

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Fifth candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, PyPI release JSON, live repo WebFetch) — not general knowledge recall.

## 1. Repository / canonical URL

`github.com/mementum/backtrader`, homepage `www.backtrader.com`.

## 2. Version / commit reviewed

Latest commit across all branches (`development`, `fix-compression`, `master`, `merge_memento_backtrader`, `numpylines`): `b853d7c9`, "Version 1.9.78.123", dated **2023-04-19T14:13:08Z**. PyPI's latest published release is also `1.9.78.123`, uploaded 2023-04-19T14:13:18Z — matches. GitHub has **zero** entries in `/releases`; tagging is git-tags-only. One out-of-sequence tag, `1.94.15.104`, exists and is unexplained — possibly an unofficial/community re-tag, not confirmed as upstream. Flagged, not resolved.

## 3. License

`GNU GENERAL PUBLIC LICENSE Version 3, 29 June 2007` — read directly from the `LICENSE` file content. GitHub's own SPDX detection agrees (`gpl-3.0`/`GPL-3.0`) — no badge/file mismatch here, unlike the vectorbt case in this program.

## 4. Maintenance / activity — the critical finding: effectively abandoned

- Repo metadata: `has_issues: false`, `has_projects: false`, `has_wiki: false`, `has_discussions: false` — the maintainer has **actively disabled** the Issues tracker (confirmed via both the API field and a live render of the repo page: no Issues tab, only "Pull requests (63)").
- Last actual commit to any branch: **2023-04-19** — over 2 years stale as of this research (2026-09-05).
- The repo's `pushed_at` field shows 2024-08-19T17:47:36Z, but this exact timestamp appears identically on multiple unrelated forks (`pgeofrey026/backtrader`, `UmeshNareddy/backtrader`, `pspetri/backtrader`), indicating it reflects a GitHub fork-network metadata event, not a new commit to mementum's own tree. The commit API confirms no commit newer than 2023-04-19 exists — `pushed_at` alone would have been misleading if trusted without cross-checking commit history directly.
- README carries a dated note from 2018-11-16 about data-download reliability and explicitly redirects all support to an external "Community" forum (community.backtrader.com), stating the GitHub ticket system was "abused" for support requests — evidence the maintainer withdrew from GitHub-based support before disabling Issues outright.
- 63 open PRs, none mergeable through any visible active review process.

**Verdict: effectively abandoned**, not merely "maintenance-only" — no commits, no releases, no issue-triage capability, for over two years, by deliberate maintainer choice (tracker disabled) rather than passive neglect alone.

## 5. Architecture summary — README/homepage level, not source-verified

Cerebro is the central orchestrator (owns data feeds, strategies, brokers, observers, analyzers, and drives the event loop). Class model: `Strategy`, `Indicator`, `Analyzer`, `Observer`, `Sizer`, `Broker`, `DataFeed`, built on a custom `lines` metaclass system for time-series data. Clock model is bar-driven (next-bar processing), with resampling/replaying to multiple timeframes and multiple simultaneous data feeds per Cerebro instance. This description is drawn from README/homepage content — not verified line-by-line against source in this pass, and given the maintenance findings, a deeper source read is lower priority than it would be for an actively-maintained candidate.

## 6. Relevant modules (presence inferred from repo structure, not individually opened)

`brokers/` (simulated broker with commission schemes), `feeds/` (CSV, Pandas, and various live/data-vendor feeds), `indicators/` (large built-in library), `analyzers/` (Sharpe, drawdown, SQN, etc.), `sizers/` (position sizing).

## 7. Tests and test quality — unresolved, known weak point

Not independently verified this pass. Needs a follow-up that actually opens `tests/` and runs it before any ADOPT/CONFORMANCE-COMPARATOR decision — do not assume coverage from README claims. Given the abandonment finding, this is lower-priority to chase further.

## 8. Deterministic / reproducibility properties — unresolved

Not verified against source (e.g. `cerebro.py`, `broker.py`) this pass.

## 9. Asset / timeframe / venue assumptions — unresolved

Not verified against source this pass.

## 10. Hidden defaults or semantic coupling — unresolved

Not verified against source this pass.

## 11. Performance characteristics

Pure Python; no documented performance ceiling or benchmark found in primary sources fetched this session.

## 12. What Signal Current could reuse

The Cerebro-as-orchestrator / Strategy-Indicator-Analyzer-Observer-Sizer-Broker class separation is a reasonable architectural reference point for how to decompose a backtest engine's responsibilities — read-only design inspiration, not a dependency.

## 13. What Signal Current should not inherit

Nothing at the code level — see disposition below. No dependency relationship is safe to establish with an abandoned, unpatchable GPL-3.0 codebase.

## 14. Integration / coupling risks

GPL-3.0 copyleft plus zero commits since 2023-04-19, a disabled issue tracker, and zero GitHub Releases together mean there is no realistic path to upstream fixes, security patches, or license renegotiation. This combination is disqualifying for any dependency relationship (ADOPT, WRAP, or FORK-with-upstream-sync).

## 15. Required parity / golden tests if used as reference

Not applicable — not proposed as a conformance comparator given the abandonment finding.

## 16. Proposed disposition

**REJECT for ADOPT/FORK/WRAP; REFERENCE only** (architecture reading for design ideas, no code dependency). A hard fork is technically possible (the code is GPL and forkable) but would inherit GPL-3.0 obligations and a stale codebase with no upstream feedback loop — not worth it when NautilusTrader, LEAN, and Freqtrade all offer actively-maintained alternatives already surveyed in this program.

## 17. Confidence level

**HIGH** on license text and maintenance/activity findings — both read directly from primary sources (LICENSE file, commit API, releases API, PyPI JSON, repo metadata, live repo render). **MEDIUM-LOW** on architecture/test-quality/performance claims (§5–11) — drawn from README/homepage description, not independently confirmed against source or a test run. Given the abandonment verdict, that gap is not worth closing further unless Signal Current later wants specific architecture ideas from Cerebro.

## 18. Unresolved questions

- Does `tests/` exist and pass under a current Python version? (Would require actually cloning and running, not reading claims.)
- Is `community.backtrader.com` itself still live/active? Not checked this pass — relevant to whether "Community" is a real support channel or another dead end.
- The out-of-sequence git tag `1.94.15.104` is unexplained.
- No source-level check yet of Cerebro's event loop, look-ahead-bias safeguards, or broker fill-simulation assumptions — would only be worth doing if Signal Current wants specific architecture ideas, not for any dependency decision.

## Sources

- https://api.github.com/repos/mementum/backtrader (repo metadata: `has_issues`, `pushed_at`, license, open PRs)
- https://api.github.com/repos/mementum/backtrader/commits (latest commit 2023-04-19)
- https://api.github.com/repos/mementum/backtrader/releases (empty — no GitHub Releases)
- https://api.github.com/repos/mementum/backtrader/contents/LICENSE (GPL-3.0 text)
- https://pypi.org/pypi/backtrader/json (release history, last upload 2023-04-19)
- https://github.com/mementum/backtrader (live render: no Issues tab, README 2018 note, community-forum redirect)
