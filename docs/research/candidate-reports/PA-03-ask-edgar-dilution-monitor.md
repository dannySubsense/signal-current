# Candidate Report — Ask Edgar Dilution Monitor (jasontange)

**Stream:** PA-03 (Data/Time/Instrument Semantics)
**Status:** First candidate report for PA-03. **REJECT as an ingestion/architecture reference.**
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, README, direct source read of `das_monitor.py`) — not general knowledge recall. **Security note**: per an earlier finding in this program, the local capture file `AlgoTradingIdeas/ask-edgar/summary.md` contains what looks like a live plaintext API key. This report and its research agent deliberately did not read, quote, or transmit that credential anywhere — only the public repo's architecture was evaluated.

## Critical finding: the public repo contains no SEC-filing ingestion or dilution-scoring logic at all

Repo: `jasontange/Ask-Edgar-Dilution-Monitor-Public` — https://github.com/jasontange/Ask-Edgar-Dilution-Monitor-Public. Author confirmed via `gh api` as GitHub user `jasontange`, matching the local capture. Owning product: "Ask Edgar" (askedgar.io).

**The repo is not a dilution-risk analysis engine and does not touch SEC EDGAR at all.** It is an 803-line single-file Python/tkinter **desktop overlay GUI** (`das_monitor.py`) that: (a) polls Windows window titles every 1 second via `win32gui` to detect the active ticker in DAS Trader Pro / thinkorswim, (b) on ticker change, makes plain `requests.get` HTTP calls to five hosted REST endpoints under `eapi.askedgar.io` (`dilution-rating`, `float-outstanding`, `news`, `dilution-data`, `screener`), and (c) renders the JSON response as color-coded risk badges/cards. **All SEC-filing ingestion, parsing, scoring methodology, and the underlying data pipeline live server-side behind Ask Edgar's paid API and are not published anywhere in this repo.**

## 1. Repository / canonical URL / author

`jasontange/Ask-Edgar-Dilution-Monitor-Public` — https://github.com/jasontange/Ask-Edgar-Dilution-Monitor-Public. GitHub user `jasontange` (account id 153547888). A "Jason Tang" is separately named as co-founder of the Ask Edgar product per its blog — whether this is the identical person as GitHub handle `jasontange` was not independently reconciled this pass, flagged as unresolved.

## 2. Version / commit reviewed

Single commit only: `d85f30d` (2026-03-29T20:06:02Z), "Ask Edgar Dilution Monitor – original public release." Confirmed exactly one commit exists via the GitHub API's pagination headers.

## 3. License — claimed in prose, not formally granted

README states "License: MIT – do whatever you want with it," but the repo's root directory listing (`.env.example, .gitignore, README.md, das_monitor.py, requirements.txt, run.bat, setup.bat`) contains **no LICENSE file**, and the GitHub API's own `license` field returns `null` (GitHub's license-detector found nothing to key off). **A license claim exists in prose only, not a formal grant** — a real gap if any code were ever vendored or forked.

## 4. Maintenance / activity

Created 2026-03-08, one release commit 2026-03-29, nothing since (~5 months stale as of this research). 7 stars, 6 forks, 0 open issues. Reads as a one-shot marketing/community artifact from the Ask Edgar product team, not an actively maintained tool.

## 5. Architecture summary

See critical finding above. This is a thin desktop UI client, not a data pipeline.

## 6. Relevant modules / functions

`fetch_dilution_data`, `fetch_float_data`, `fetch_news_and_grok`, `fetch_in_play_dilution`, `fetch_last_price` (thin HTTP wrappers only); `find_montage_windows`/`find_tos_tickers` (win32 window-title parsing, irrelevant to Signal Current); UI builder methods. No data-modeling, scoring, or ingestion code exists to review.

## 7. Tests and test quality

None. No test directory, no test files, no CI workflow found.

## 8. Deterministic / reproducibility properties

Not assessable from this repo — the only client-side "logic" is UI polling and JSON→widget rendering. All risk classification (GREEN/YELLOW/RED bucketing, dilution scoring) happens inside the closed-source hosted API, so reproducibility of the actual risk signal cannot be evaluated at all from public code.

## 9. Asset / timeframe / venue assumptions

Implied by the API's endpoint names and product marketing (US small-cap equities filing with SEC EDGAR) — inferred from context, not verified from any published schema, since the API's OpenAPI docs were explicitly excluded from this review per the security instruction (they live in the local `api-docs.json` alongside the flagged credential).

## 10. Hidden defaults or semantic coupling

`POLL_INTERVAL = 1.0` (hardcoded 1s poll), hardcoded endpoint URLs, hardcoded UI geometry — all cosmetic client concerns, not data-architecture concerns.

## 11. Performance characteristics

None documented beyond "polls every 1 second." No throughput/latency/accuracy claims for the underlying dilution-rating engine appear anywhere in the public repo.

## 12. What Signal Current could reuse

Very little, and **nothing on the provenance/audit axis** most relevant to Signal Current's Reconciliation Matrix / SourceSnapshot requirements. There is no visible SEC-filing ingestion code, no snapshot/versioning of filings, no audit trail, no provenance metadata in this repo — the entire data-provenance question is opaque, hidden inside Ask Edgar's proprietary backend. At most, the risk-bucket taxonomy names (offering risk / offering ability / offering frequency / dilution risk / compliance risk / cash runway, per Danny's own reverse-engineering notes in the local `summary.md`) could inform Signal Current's own category naming — but that taxonomy is Danny's own inference, not something this repo itself documents or verifies.

## 13. What Signal Current should not inherit

Do not adopt this as an ingestion reference — it has no ingestion logic to adopt. Do not adopt the "MIT license, do whatever you want" README framing as a green light to vendor/fork code, since no LICENSE file backs that claim (§3). Do not adopt the single-Python-file/no-tests/no-CI engineering pattern.

## 14. Integration / coupling risks

If Signal Current ever considered *calling* the Ask Edgar API directly (rather than treating this repo as prior art), that would be a hard external-vendor coupling: a single paid API key, generically-described rate limits and "some bugs still being worked out" per Danny's own notes, a single point of failure, no visible SLA, and a proprietary/undocumented risk methodology — inheriting an unauditable black-box signal, in direct tension with Signal Current's deterministic, evidence-based mandate. This is a structural risk independent of the specific credential, which per the security instruction was not read, quoted, or transmitted in this research.

## 15. Required parity / golden tests if used as reference

Not applicable — there is no algorithm here to build a conformance comparator against. If Signal Current later wants a conformance comparator for "what would Ask Edgar say about this ticker," it would need to be built against the hosted API's live JSON responses (with its own rate-limit/cost/determinism problems), not against this repo's code.

## 16. Proposed disposition

**REJECT** as an architecture/ingestion reference. Optionally **REFERENCE** at the taxonomy-naming level only (the six risk-category names) — a documentation convenience, not code reuse. Fails ADOPT/FORK/ADAPT/WRAP because there is no ingestion or scoring logic to take; fails CONFORMANCE COMPARATOR because the only comparator available would be a live paid third-party API with an already-flagged credential-hygiene problem, no license backing, and a black-box methodology — exactly the "unsourced number" anti-pattern this project's global rules warn against building on.

## 17. Confidence level

**HIGH** on "this repo contains no SEC-filing-ingestion/dilution-scoring implementation" — directly verified from the actual file (803 lines, all UI/HTTP-client code). **MEDIUM** on the `jasontange`/"Jason Tang" identity linkage (surfaced only via web search snippet, not independently reconciled).

## 18. Unresolved questions

- Whether the hosted API's methodology is disclosed anywhere worth reviewing structurally (would require reading `api-docs.json`'s OpenAPI spec while still avoiding the credential field — out of scope this pass, doable in a follow-up if wanted).
- Whether Danny's separate reverse-engineering doc (referenced in `summary.md` but not read here) contains independently-derived methodology worth citing as its own prior art, distinct from this GitHub repo.

## Sources

- https://github.com/jasontange/Ask-Edgar-Dilution-Monitor-Public
- https://github.com/jasontange/Ask-Edgar-Dilution-Monitor-Public/blob/master/README.md
- https://github.com/jasontange/Ask-Edgar-Dilution-Monitor-Public/blob/master/das_monitor.py
- GitHub REST API responses (`gh api repos/jasontange/Ask-Edgar-Dilution-Monitor-Public`, `.../commits`, `.../contents/`), queried directly 2026-09-05
- https://www.askedgar.io/blog/9 (surfaced via web search, not independently verified against the repo author)
- Local file (credential NOT read/quoted): `/home/d-tuned/life/resources/AlgoTradingIdeas/ask-edgar/summary.md`
