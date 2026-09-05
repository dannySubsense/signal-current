# Candidate Report — FinRL and FinRL-Trading ("FinRL-X")

**Stream:** PA-04 (Search/Optimization/ML)
**Status:** First candidate report for PA-04. Independently re-verifies two pre-existing local audit reports (`/home/d-tuned/life/resources/AlgoTradingIdeas/finrl-audit-report.md`, `finrl-x-audit-report.md`, both by "Major Tom," dated 2026-03-24) against live repo state.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE files, `pyproject.toml`/`setup.py`, direct source/test-directory listings, PyPI) — not general knowledge recall, and not a repeat of the local audits' claims without checking.

## 1. Names / URLs / relationship — clarified

Two distinct projects, both under the AI4Finance-Foundation org:

- **FinRL** — https://github.com/AI4Finance-Foundation/FinRL — the original 2020 open-source financial RL framework (Gym-style envs + DRL agents).
- **"FinRL-X" is not a separate repo** — it is the marketing/project name for the repo whose GitHub slug is `FinRL-Trading`: https://github.com/AI4Finance-Foundation/FinRL-Trading. Confirmed via `gh api repos/AI4Finance-Foundation/FinRL-Trading` → description "FinRL-X: An AI-Native Modular Infrastructure for Quantitative Trading." The local audit's URL for "FinRL-X" is correct; the name is a rebrand-in-description, not a repo rename. It is a ground-up rewrite, not a fork of FinRL (created 2020-07-26, independent history, `"fork": false`).

## 2. Version / commit reviewed

- FinRL: latest tag `v0.3.8` (released 2026-03-20); HEAD commit `2334a5fe` (2026-07-12). `pyproject.toml` still declares `version = "0.3.8"` with a description string referencing "Version 0.3.5 notes" — the version-mismatch the local audit flagged **is still present today**.
- FinRL-Trading/"FinRL-X": latest git tag is `v1.0.0`, but `setup.py` declares `version="2.0.2"` — a real, currently-live tag/setup.py mismatch, confirmed directly. HEAD commit `e65d6f04` (2026-05-02).

## 3. License — both local audit claims hold

Verified via GitHub API license field and by reading actual `LICENSE` file content:
- FinRL: **MIT** (API `license.spdx_id == "MIT"`; `pyproject.toml` also declares `license = "MIT"`).
- FinRL-Trading: **Apache-2.0** (API confirms; `LICENSE` file content read directly begins "Apache License, Version 2.0"). No license-history changes found in either repo.

## 4. Maintenance / activity — audit's framing partially wrong

FinRL: 30 contributors, 16,220 stars, 310 open issues, last push 2026-07-13, last release 2026-03-20 — active. FinRL-Trading: 14 contributors, 3,664 stars, 56 open issues, last push 2026-05-02, one release (v1.0.0, 2026-03-25) — active but noticeably smaller/younger. **Both are active as of today, contradicting the local audit's framing of FinRL as merely "maintenance mode."** If anything, the audit's comparison table has the two projects' relative activity levels reversed: FinRL's most recent commit (2026-07-12) is materially more recent than FinRL-Trading's (2026-05-02).

## 5. Architecture summary

FinRL: layered `finrl/meta/` (env_stock_trading, env_portfolio_allocation, env_cryptocurrency_trading, data_processors) + `finrl/agents/` (elegantrl, stablebaselines3, rllib, portfolio_optimization) — confirms the Gym-style env wrapper + pluggable-RL-backend design the local audit described. FinRL-Trading/FinRL-X: `src/{config,data,backtest,strategies,trading,utils,web}` — Pydantic config, `bt` backtesting library, multi-strategy (ML + an `rl_model.py` in strategies).

## 6. Relevant modules

FinRL: `finrl/meta/env_stock_trading/env_stocktrading.py` (core StockTradingEnv), `env_stocktrading_cashpenalty.py`, `env_stocktrading_stoploss.py`, `agents/stablebaselines3/models.py`. FinRL-Trading: `src/strategies/base_strategy.py`, `rl_model.py`, `adaptive_rotation/`, `backtest/backtest_engine.py`.

## 7. Tests and test quality — audit's implied claim was too generous for FinRL-Trading

FinRL: `unit_tests/{downloaders,environments,preprocessors,test_core.py}` — real, present, narrow. **FinRL-Trading: no dedicated test directory exists in the repo tree as of today** (root listing has no `tests/`; a code search for test files only matched `backtest_engine.py`, a substring false-positive) — despite `pytest`/`pytest-cov` being listed as dev dependencies in `requirements.txt`. This is worse than the local audit's "Test Framework: pytest with coverage ✅" line implied — that line reflected tooling configuration, not actual test files.

## 8. Deterministic / reproducibility properties — a real gap in both audits

Neither local audit addressed RL reproducibility/seeding directly, and no centralized global-seed utility was found in either repo within API-browsable scope. FinRL's Stable-Baselines3/ElegantRL/RLlib backends each have their own seeding semantics, and FinRL's own code doesn't appear to unify them. **This is a genuine gap and a real risk for Signal Current** — RL agent training is stochastic by default in all three supported backends; verify per-backend before building any expectation of run-to-run determinism. Not verified either way with high confidence — flagged unresolved.

## 9. Asset / timeframe / venue assumptions

FinRL: daily-bar equities/crypto is the dominant path (Yahoo Finance, Alpaca, Binance, CCXT, JQData, WRDS, Baostock, Akshare connectors confirmed present as separate `data_processors` submodule entries). FinRL-Trading: equities-only, Alpaca-only broker, Yahoo/FMP/WRDS data sources.

## 10. Hidden defaults or semantic coupling — new finding, not in either local audit

FinRL-Trading's `requirements.txt` still pins `finnhub>=2.4.19` — **this is the wrong PyPI package name**. The real Finnhub client is published as `finnhub-python` (confirmed via PyPI search); no actively-maintained standalone `finnhub` package was found. The local audit flagged this same issue (as "Issue #81") months ago, framed as something that "should be fixed with P0 priority" — **it is still unfixed today, five-plus months later.** A concrete example of a homelab-captured finding that was correct at the time and has simply not been resolved upstream since.

## 11. Performance characteristics

No independently reproduced backtest numbers exist in either local audit or in what could be pulled live — both audits' performance grades (Sharpe/drawdown claims) are qualitative code-review assessments, not reproduced results. **Treat any specific performance number from either project or audit as unsourced until someone actually runs the pipeline.**

## 12. What Signal Current could reuse

FinRL's `env_stock_trading` Gym-env pattern and its multi-backend agent abstraction (`agents/{elegantrl,stablebaselines3,rllib}`) are a reasonable reference architecture for "if we ever add an RL strategy family" — reuse as a pattern reference, not as a dependency.

## 13. What Signal Current should not inherit

Neither repo has cross-validation/regime-aware splits, walk-forward analysis, slippage/market-impact modeling, or degenerate-policy/reward-hacking detection for RL agents — a well-documented failure class in RL trading research generally (both audits independently agree, and this matches known RL literature; the specific "no such controls exist in these repos" claim is medium confidence — not exhaustively verified file-by-file, but consistent with the absence of `walk_forward`/`cross_val`/`regime` modules in either module listing).

## 14. Integration / coupling risks

FinRL: `TA-lib` requires conda (not pip) — deployment friction, plausible and unchanged since the audit but not independently re-tested this pass. FinRL-Trading: `sys.path.insert()` anti-pattern claimed by the audit — not independently re-read line-by-line this session, treat as signpost not pillar. The finnhub package-name bug (§10) is a real, current, install-breaking risk if that dependency path is exercised.

## 15. Required parity / golden tests if used as reference

If Signal Current references FinRL's env logic: (a) golden-test the reward function and step/reset semantics against a hand-computed toy portfolio; (b) an explicit seed-and-rerun test per RL backend to characterize actual determinism, not assumed; (c) verify `env_stocktrading.py`'s transaction-cost and turbulence-index logic against a known market-stress period — the local audit already flagged the turbulence index as "not validated," and this needs closing before any downstream reliance.

## 16. Proposed disposition

**REFERENCE (both).** Neither is a fit for ADOPT/FORK/ADAPT/WRAP: FinRL's architecture is monolithic/config-as-code with unpinned deps in places; FinRL-Trading/FinRL-X is younger, smaller, has zero visible test files, and has a live unresolved dependency bug. Use both purely as prior-art architecture references (env-wrapper pattern, multi-backend agent abstraction, Pydantic-config pattern) — do not take a runtime dependency on either package.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on license, version, contributor/activity, and the finnhub bug — all directly verified via `gh api`/PyPI. Lower confidence on architecture-quality claims taken from the audits that were not independently re-read line-by-line this session (path manipulation, print-statement, credential-pattern claims) — those remain signpost, not re-verified.

## 18. Unresolved questions

- Actual RL-seeding/reproducibility behavior per backend — needs a hands-on test, not just a repo browse.
- Whether FinRL-Trading's `v1.0.0`/`setup.py:2.0.2` mismatch is a real release-process bug or a cosmetic leftover.
- No independent performance/backtest numbers exist for either — anyone citing a Sharpe ratio from either project needs to point at a specific run artifact, not the README.

## 19. Did the local audits' grades hold up?

- **License claims: HOLD** — FinRL MIT and FinRL-Trading Apache-2.0 both confirmed by reading live LICENSE files and the GitHub API license field.
- **Version claims: PARTIALLY STALE** — FinRL is now at commit `2334a5fe` (2026-07-12), five months past the audit's implicit "current" state, still tagged 0.3.8. FinRL-Trading's "2.0.2" is confirmed in `setup.py`, but the git tag is `v1.0.0` — a nuance the audit didn't surface.
- **Maintenance-activity claims: PARTIALLY WRONG** — the audit's "FinRL = maintenance mode" framing undersells it; FinRL had a commit as recently as 2026-07-12, materially more recent than FinRL-Trading's 2026-05-02 last push. The audit's relative activity ranking appears reversed from what's observed today.
- **"Missing `base_strategy.py`" claim (Issue #80): FALSIFIED.** The file exists today at `src/strategies/base_strategy.py`, alongside `rl_model.py` (also contradicting the audit's separate claim "No Deep RL strategies despite FinRL name" — an `rl_model.py` file is present, though its functional completeness was not verified).
- **The finnhub wrong-package-name claim (Issue #81): CONFIRMED STILL PRESENT**, independently re-verified against PyPI, not just repeated from the audit.
- **Net: license grades hold; several point-in-time code-state claims (missing file, maintenance-mode framing) are now stale or reversed** — a concrete demonstration of why homelab-captured claims need re-verification against live source before being used to write an architecture spec, exactly the caution this program's earlier PA-02 reports (ML_Finance_Codes misattribution) already surfaced.

## Sources

- https://github.com/AI4Finance-Foundation/FinRL
- https://github.com/AI4Finance-Foundation/FinRL-Trading
- https://pypi.org/project/finnhub-python/
- GitHub API calls against both repos (license, commits, tags, contents, releases), run live 2026-09-05
- Local audits (re-verified, not repeated as-is): `/home/d-tuned/life/resources/AlgoTradingIdeas/finrl-audit-report.md`, `finrl-x-audit-report.md`
