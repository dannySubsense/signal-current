# Candidate Report — quantmuse

**Stream:** PA-01 (Systems & Engine Survey), also relevant to PA-04 (LLM/NLP layer)
**Status:** Twelfth PA-01-area report. **Corrects a mischaracterization from the original homelab survey: this is not Danny's own prior work.**
**Researched:** 2026-09-05, via direct local filesystem inspection (`Read`/`Glob`/`Grep`, `git log`, `git remote -v` on `/home/d-tuned/life/resources/AlgoTradingIdeas/quantmuse/`) — not GitHub research, since this is a local clone.

## Critical correction: this is not Danny's own work — it is a third-party clone

The earlier homelab survey characterized quantmuse as "Danny's own prior side-project," flagged to receive the same rigor as external candidates. **Direct inspection shows it is not Danny's work at all — it is a git clone of a third-party public GitHub repo:**

- `git remote -v` → `origin git@github.com:0xemmkty/QuantMuse.git`.
- All 9 commits are authored by `EKMM`/`prxr`, both using email `2733224120@qq.com` (a Tencent QQ mailbox, consistent with a Chinese author) — no Danny commits anywhere in history.
- The two Chinese-language files (`README_Quantitative_Strategies.md`, `README_Web_Interface.md`) are **not translations** of the English docs — they are original-language documents (different content structure, Chinese section headers, Chinese code comments) consistent with the repo's actual primary author writing in Chinese natively. This confirms genuine Chinese origin, not a fork of a Chinese project by an English-speaking author.

**This local folder is simply a clone that landed in Danny's homelab collection** — how or why is unresolved (§ unresolved questions). The prior survey's "own prior codebase" framing should be corrected wherever it's referenced.

## Additional finding: README.md itself contains unverifiable, possibly fabricated third-party praise

`README.md` ends with: "**Logged:** August 25, 2026 / **Source:** GitHub repo + Bright Coding review + web search," citing unverifiable third-party "reviews" ("Bright Coding Deep-Dive," "SourcePulse," an "X/Twitter announcement by @quantscience_"). **This reads as a prior research/summarization pass that uncritically repeated promotional claims about the repo — exactly the "unsourced claim passes as fact" failure mode this project's global rules exist to catch.** None of those claims were verified true or false this pass (no web check performed) — flagged as unverified, not confirmed false, but should not be repeated as evidence of quality.

## 1. Repository / canonical URL / author

`0xemmkty/QuantMuse` (GitHub), cloned locally. Actual author: GitHub handle `0xemmkty`/commit authors `EKMM`/`prxr`.

## 2. Version / commit reviewed

Not independently checked against the live GitHub repo this pass (only the local clone's git history was inspected) — 9 commits total in local history.

## 3. License — MIT, generic placeholder copyright

An MIT LICENSE file exists, copyright "Quantitative Trading System" (a generic placeholder name, not the actual author's name) — 2024. **MIT-licensed, but attribution to the actual author/project must be preserved per MIT terms if any code is ever reused** — this is third-party code requiring standard attribution, not Danny's own IP to license as he sees fit.

## 4. Maintenance / activity

Not assessed against the live GitHub repo this pass — only the local clone's static state was reviewed.

## 5. Architecture summary — aspirational scaffold, not a working system

`Trading_Engine_Architecture.md` is a bare-bones aspirational outline — section headers only ("Threading Model," "Error Handling," "Performance Considerations") with no implementation detail, no diagrams, no citations to actual code. **It reads as a planning doc, not documentation of a built system.**

## 6. Relevant modules

C++ backend (aspirational per §5), Python `data_service`, FastAPI/Streamlit dashboard, an LLM/NLP layer (`demo_llm_nlp_simple.py`).

## 7. Tests and test quality — illusory C++ coverage, real but narrow Python coverage

`test.cpp` is literally `int main(){ std::cout << "Hello, C++ is working!"; }` — a 106-byte compiler-check stub, unrelated to any trading logic. There is a real `tests/` directory with 4 pytest files (`test_integration.py`, `test_binance_fetcher.py`, `test_data_processor.py`, `test_llm_integration.py`) plus `.pyc` cache showing they've actually been run under pytest 8.3.4/Python 3.13 — a real, executed (if narrow) Python test suite, but **the C++ backend has effectively zero test coverage.**

## 8. Deterministic / reproducibility properties

Not assessed — out of scope given the disposition below.

## 9. Asset / timeframe / venue assumptions

`config.example.json` implies dependencies on Binance, OpenAI (GPT-4), Alpha Vantage, and Yahoo Finance, plus Postgres/Redis/SMTP/Slack/Telegram integration points — all placeholder values, no real credentials present. A fairly standard quant-stack config shape, not proprietary/exotic.

## 10-11. Hidden defaults / performance

Not assessed — out of scope given the disposition below.

## 12. What Signal Current could reuse

At most, the file-layout/config-surface shape as a vocabulary reference for "what a full-stack quant platform's directory structure looks like" (data_service, dashboard, LLM layer, C++ engine) — not code, not architecture detail (§5 shows there isn't any beyond headers).

## 13. What Signal Current should not inherit

No code should be lifted without explicit MIT attribution to the actual upstream repo (`0xemmkty/QuantMuse`) — treat as external third-party code, full stop, regardless of how it landed in Danny's local folder. **Do not cite the promotional claims in README.md** ("production-ready," "institutional-grade," "Bright Coding review") as evidence of quality — they are unverified and possibly fabricated/synthesized, and should not be laundered into Signal Current's own prior-art record. The C++ trading engine is aspirational scaffold only — don't treat the architecture doc as validated design.

## 14. Integration / coupling risks

License/attribution risk if any code is reused directly without crediting the actual author.

## 15. Required parity / golden tests

Not applicable given the disposition below.

## 16. Proposed disposition

**REJECT as prior art; REFERENCE at most for architecture-vocabulary/config-shape ideas only, with mandatory attribution if any code is ever copied.** This is not Danny's IP, was mischaracterized by the earlier survey as "his own prior work," and the one document that summarizes it (README.md) itself contains unsourced/unverifiable third-party praise that should not be repeated as fact. Fine as a reference for "what a full-stack quant platform's file layout looks like," but should not be cited in any Signal Current doc as Danny's own prior engineering.

## 17. Confidence level

**HIGH** on origin/authorship/license/test findings — all directly verified from git history and file contents. **MEDIUM-LOW** on the "why is this local copy here" question, and the README.md's third-party citations remain unverified either way (not confirmed true or false).

## 18. Unresolved questions

- Who/what created this local copy and why (manual clone by Danny vs. an automated/agent action) — worth asking Danny directly.
- Whether the "Bright Coding," "SourcePulse," and "@quantscience_" citations in README.md are real or fabricated — needs an actual web check before anyone treats them as evidence of the repo's quality.
- Whether the earlier survey's "Danny's own prior work" framing originated from a misread of the local file path (personal folder) rather than actual authorship — **this should be corrected wherever that framing was referenced.**

## Sources

- Direct filesystem inspection: `/home/d-tuned/life/resources/AlgoTradingIdeas/quantmuse/` (README.md and other top-level docs, `Trading_Engine_Architecture.md`, `test.cpp`, `tests/`, `config.example.json`, `.git` history via `git log`/`git remote -v`)
