# Candidate Report — "Harvard Algorithmic Trading with AI" (moondevonyt)

**Stream:** PA-08 (Agent & Orchestration Layer, Research Methodology precedent)
**Status:** Second candidate report for PA-08. **REJECT leaning — the "Harvard" affiliation implied by the local capture's filename/description does not hold up, and three of the local capture's characterizations were found stale or wrong.**
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, README, direct source reads of `bb_squeeze_adx.py`/`template.py`, owner account, linked homepage) — not general knowledge recall.

## Critical finding: no Harvard affiliation exists

Repo: `moondevonyt/Harvard-Algorithmic-Trading-with-AI` — https://github.com/moondevonyt/Harvard-Algorithmic-Trading-with-AI. Owner account `moondevonyt` is "Moon Dev," a pseudonymous YouTube algo-trading educator (~104k subscribers) who sells a paid course via `algotradecamp.com` — the repo's GitHub `homepage` field is literally `https://algotradecamp.com/?utm_source=github`, confirmed live.

**No verified affiliation with Harvard University exists.** The README never claims institutional Harvard sponsorship; the only real Harvard reference is CS50 ("Introduction to Computer Science," a genuine Harvard course) listed as a prerequisite. "Harvard" in the repo/title is the author's own informal branding riding on the CS50-prerequisite framing, not an institutional program. The local capture's own text was arguably accurate as written (a short description, no explicit institutional claim), but the filename and framing are misleading enough that a reader could reasonably infer Harvard sponsorship, which is false. **This is the same class of finding as `PA-02-ml-finance-codes.md`'s authorship misattribution — worth flagging in the local capture, not just this report.**

## 1. Repository / canonical URL / author / verified relationship

See above. Owner: `moondevonyt` (GitHub user id 115879186).

## 2. Version / commit reviewed

Default branch `main`, HEAD commit `092560ed` (2025-04-18T22:18:27Z, "Restore button-style video link badge").

## 3. License — none exists, contradicting the local capture's "Open-source" framing

GitHub API reports `"license": null` — **no LICENSE file exists in the repo** (confirmed via root directory listing: only `.env_example`, `.gitattributes`, `.gitignore`, `README.md`, `backtest/`, `implement/`, `research/`). Absent a LICENSE file, the code defaults to full copyright reservation under GitHub's terms — **it is not open-source-licensed for reuse**, despite the "Open-source" description in the local capture and the framing of the README. This is a materially important discrepancy from the local capture.

## 4. Maintenance / activity — stale, contradicting "Active development"

Repo created 2025-04-18; all 10 commits (full history) fall on that single day — a single-day commit burst, not ongoing development. `pushed_at` = 2025-04-18T22:18:28Z — **no code commits in ~17 months** as of this research, despite `updated_at` showing a more recent date (metadata/star activity only, not code pushes). Open issues: 1. Stars 458, forks 209. **The local capture's "Status: Active development" claim does not hold up against the primary source.**

## 5. RBI methodology as actually presented — loose, informal, not rigorous

README presents Research→Backtest→Implement as three prose paragraphs of general advice ("study proven strategies," "avoid survivorship bias," "code your strategy... deploy with careful monitoring"). **No formal decision criteria, no statistical thresholds, no falsifiability tests, no defined gate/checklist between phases.** `research/README.md` is a reading list (finance books, podcasts) plus 7 bullet "best practices" — not a specified process. The "based on Jim Simons' systematic approach" framing in the local capture traces to unsourced marketing language in the README itself ("built on the hypothesis shared by Jim Simons... the only way to trade effectively is with robots") — not a documented methodology derived from Renaissance Technologies practice.

## 6. Modules / code present

Real code exists, but thin: `backtest/` (README, `bb_squeeze_adx.py`, `data.py`, `template.py`, a `data/` subdir), `implement/` (`bot.py`, `nice_funcs.py`), `research/` (README only, no code). Closer to a single-author course-companion sample set than a course repo with per-lesson exercises — three strategy-adjacent scripts total.

## 7. Tests and test quality

None found. No `tests/` directory, no test framework references, no CI config. Scripts are runnable examples, not tested modules.

## 8. Deterministic / reproducibility properties — poor

`backtest/bb_squeeze_adx.py` and `template.py` (read directly) **hardcode an absolute local path**: `data_path = '/Users/md/Dropbox/dev/github/Harvard-Algorithmic-Trading-with-AI-/backtest/data/BTC-6h-1000wks-data.csv'` — will not run without editing on any other machine. No `requirements.txt`/lockfile in root listing. No seed-setting or determinism guarantees found in either script reviewed.

## 9. Asset / timeframe / venue assumptions

BTC only, 6-hour bars (per the hardcoded filename "BTC-6h-1000wks-data.csv"), via `yfinance` and `ccxt`/Hyperliquid for execution per README — crypto-perp-centric, not multi-asset or equities-production-grade.

## 10. Hidden defaults or semantic coupling — a real, verified example of an unsourced constant

Both reviewed strategy scripts hardcode take-profit/stop-loss as flat percentages (5%/3%) as class-level defaults with no cited justification, and `template.py`'s optimizer sweeps these same magic numbers (1–7%) with no stated rationale — **exactly the "promoted default" pattern this project's global rules flag**: unsourced numeric constants inside strategy logic. Any of these values pulled into Signal Current must not be reused without independent justification.

## 11. Performance claims

None substantiated — no backtest result tables, equity curves, or reported metrics were found in the reviewed files/README. The `bt.optimize(...)` call in `template.py` prints results at runtime only; no cached/committed output exists to audit.

## 12. What Signal Current could reuse (methodology only)

The local capture's framing — "reuse methodology only" — is directionally reasonable but overstates what's here: at most, the coarse three-stage vocabulary (Research → Backtest → Implement) as a naming convention/checklist prompt, and the reading-list/source-diversity heuristics ("cross-reference multiple sources," "identify key assumptions," "consider risk first"). **Nothing here is a specified, citable methodology worth adopting as a process definition.**

## 13. What Signal Current should not inherit

The unlicensed code itself (no LICENSE = do not copy/fork/vendor without contacting the author for explicit permission); the hardcoded TP/SL magic numbers; the absolute-path/no-lockfile non-reproducibility pattern; the "Harvard" branding/claim of institutional rigor; any implication that this is a peer-reviewed or academically vetted framework.

## 14. Integration / coupling risks

Legal risk from missing license if any code is copied verbatim. Reputational risk if Signal Current's own spec cites "Harvard" without the disaffiliation caveat. No API/dependency coupling risk since nothing here is packaged (no `setup.py`/`pyproject.toml` found) or would be imported as a library.

## 15. Required parity / golden tests if used as reference

Not applicable as a code dependency — treat this purely as a naming/vocabulary reference, no parity-test obligation triggered.

## 16. Proposed disposition

**REFERENCE** (read for the three-word framing/naming inspiration only), bordering on **REJECT** as prior art of substance. Do not ADOPT, FORK, ADAPT, WRAP, or use as a CONFORMANCE COMPARATOR / EXTERNAL REGRESSION FIXTURE — no rigorous methodology, no license to build on, no tests, no ongoing maintenance. If Signal Current wants a real "RBI"-labeled academic/institutional precedent, this is not it.

## 17. Confidence level

**HIGH** on the factual findings (license null, commit history, README content, Harvard non-affiliation, code snippets, hardcoded paths) — all pulled directly from the GitHub API/raw content. **MEDIUM** on "Moon Dev" identity specifics (pseudonymous, so underlying legal identity/credentials are unverifiable from public sources).

## 18. Unresolved questions

- Author's real identity and any formal trading/quant credentials remain unverified (pseudonymous).
- Whether the YouTube companion video series contains more rigorous methodology detail than the repo itself — out of scope, not primary-sourced this pass.

## 19. Verification verdict on the local capture's specific claims

- **"Harvard" affiliation**: Did **not** hold up. No institutional relationship with Harvard University exists.
- **RBI methodology characterization**: The local capture's implicit "loose" framing held up as accurate — it is indeed a loose, unrigorous framing, not a well-specified process.
- **"Active development" status**: Did **not** hold up (last code push April 2025, ~17 months stale as of this research).
- **"Open-source" description**: Did **not** hold up (no LICENSE file present).

**Three of four checked claims in the local capture were found stale or wrong** — the strongest single-file correction ratio found in this program so far. This local capture file (`AlgoTradingIdeas/harvard-algo-trading-ai.md`) should be corrected or annotated.

## Sources

- https://github.com/moondevonyt/Harvard-Algorithmic-Trading-with-AI (README, repo metadata, commit history, directory contents)
- https://github.com/moondevonyt (owner account)
- https://www.youtube.com/@moondevonyt (Moon Dev channel)
- https://algotradecamp.com/?utm_source=github (linked homepage in repo metadata)
- Local file: `/home/d-tuned/life/resources/AlgoTradingIdeas/harvard-algo-trading-ai.md`
