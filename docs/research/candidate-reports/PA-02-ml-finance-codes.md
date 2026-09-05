# Candidate Report — "ML_Finance_Codes" (mfrdixon/ML_Finance_Codes)

**Stream:** PA-02 (Numerical & Statistical Methods)
**Status:** Second candidate report for PA-02. **REJECT — sourced from a misattributed homelab capture.**
**Researched:** 2026-09-05, via primary-source fetch of the actual GitHub repo referenced by `/home/d-tuned/life/resources/quant-research/ml-finance-codes/summary.md` and `items.json`.

## Critical finding: the local capture misattributes this repository

`/home/d-tuned/life/resources/quant-research/ml-finance-codes/summary.md` claims: "Author: Marcos López de Prado," "Official code repository for... 'Machine Learning for Asset Managers'," and lists "Key Implementations" including Distance Correlation, HRP, NCO, PBO, and bar-sampling methods.

**This is factually wrong, verified by fetching the actual repo.** The repo at `github.com/mfrdixon/ML_Finance_Codes` is the official companion repository to *Machine Learning in Finance: From Theory to Practice* (Springer, 2020) by **Matthew Dixon, Igor Halperin, and Paul Bilokon** — confirmed via the repo's own README/description: "Official Python source code repository accompanying the textbook... by Matthew Dixon, Igor Halperin and Paul Bilokon." The owner handle `mfrdixon` is Matthew F. R. Dixon, one of that book's authors — not a López de Prado account. López de Prado's *Machine Learning for Asset Managers* (Cambridge University Press) is a different book by a different author with its own separate repo tradition, unrelated to this one.

Content surfaced from the actual repo (chapter list: Probabilistic Modeling, Advanced NNs, Reinforcement Learning — Market Making/Market Impact, Inverse RL) is neural-network/RL/probabilistic-modeling material — not portfolio-construction or backtest-validation content. **There is no evidence in what was fetched of HRP, NCO, CPCV, purging/embargo, PBO, triple-barrier labeling, meta-labeling, or tick/volume/dollar bars** — all López de Prado hallmarks from *Advances in Financial Machine Learning*, not Dixon/Halperin/Bilokon topics. The local capture's "Key Implementations" table does not match the actual repo's structure.

**This is exactly the PROMOTED DEFAULT / unsourced-claim propagation pattern this project's own global rules exist to catch** — a claim was captured, treated as fact, and would have been silently carried into Signal Current's spec docs if not independently re-verified against the primary source before use.

## 1. Repository / canonical URL / authority

`github.com/mfrdixon/ML_Finance_Codes` — companion repo to Dixon/Halperin/Bilokon's *Machine Learning in Finance*, not López de Prado's code.

## 2. Version / commit reviewed

Not pinned this pass — the research agent reported ~249 commits via WebFetch summary but did not execute a direct `gh api` call to get an exact HEAD commit SHA/date. **Unresolved — do not cite a specific commit without that call**, though this is moot given the disposition below.

## 3. License

WebFetch summary reported MIT License, copyright 2020, Dixon/Halperin/Bilokon — sourced from the fetch tool's summary of the repo's LICENSE, not from independently opening the raw file or checking its history. **Treat as signpost, not verified pillar.** Moot given the disposition below, but flagged for completeness.

## 4-11. Architecture, modules, tests, determinism, asset assumptions, hidden defaults, performance

**Not assessed.** Given the identity misattribution and content mismatch, none of these fields are relevant to Signal Current's actual research question (verifying HRP/NCO/CPCV/PBO provenance) and were not pursued further.

## 12. What Signal Current could reuse

Nothing from this repo for the purpose it was captured for (HRP/NCO/CPCV/PBO reference). It may have unrelated value for neural-network/RL research if that becomes relevant later, but that was not this report's purpose and was not evaluated.

## 13. What Signal Current should not inherit

The local capture's claims should not be cited or relied upon anywhere in Signal Current's spec docs without independent re-verification. **Action item: correct or delete `/home/d-tuned/life/resources/quant-research/ml-finance-codes/summary.md` and `items.json`** — they misattribute authorship and list implementations not actually present in the linked repo.

## 14. Integration / coupling risks

None — not a candidate for integration.

## 15. Required parity / golden tests

Not applicable. **This repo provides no conformance-comparator value for Reconciliation Matrix rows 27 (purging/embargo), 28 (CPCV), 33 (multiple-testing/PBO), or 40 (HRP)** — it does not implement those methods.

## 16. Proposed disposition

**REJECT** for the stated purpose (HRP/NCO/CPCV/PBO reference). This repo is not López de Prado's code and does not implement the methods Signal Current needs. Skfolio's citations (see `PA-05-skfolio.md`) to López de Prado's work do **not** trace back to this repo — the actual canonical source for those methods remains unestablished (see §18) and needs its own follow-up candidate report, most likely targeting `hudson-and-thames/mlfinlab`.

## 17. Confidence level

**MEDIUM** on the identity correction (directly evidenced by fetched README/description text, cross-confirmed by two independent tool calls and search results referencing multiple chapter notebooks). **LOW** on license exactness and commit recency (not independently re-verified against raw file/API this pass) — moot given the disposition.

## 18. Unresolved questions

- Exact current HEAD commit SHA/date for `mfrdixon/ML_Finance_Codes` — not needed further given REJECT, but noted for completeness.
- Raw LICENSE file text/history — not needed further given REJECT.
- **Whether the local capture's chapter/technique table was hallucinated, copy-pasted from a different resource, or describes some other repo entirely** — worth checking the capture's timestamp (2026-03-12 per the file) for how this error entered the collection. This is a data-hygiene question for Danny's broader homelab collection, not a signal-current research question, but worth flagging.
- **What repo is actually López de Prado's canonical code for HRP/NCO/CPCV/PBO** — not established in this pass. The likely candidate is `hudson-and-thames/mlfinlab`, which needs its own primary-source candidate report before Signal Current's Validation & Statistical Controls spec cites any "original implementation" as a conformance comparator for skfolio.

## Sources

- https://github.com/mfrdixon/ML_Finance_Codes
- https://github.com/mfrdixon/ML_Finance_Codes/blob/master/README.md
- Local capture (found inaccurate): `/home/d-tuned/life/resources/quant-research/ml-finance-codes/summary.md`, `items.json`
