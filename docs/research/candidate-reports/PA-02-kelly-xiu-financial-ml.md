# Candidate Report — "Financial Machine Learning" (Kelly & Xiu, BFI WP 2023-100)

**Stream:** PA-02 (Numerical & Statistical Methods), also relevant to Validation & Statistical Controls
**Status:** Seventh candidate report for PA-02. Recovered from an orphaned file in a stale `.openclaw/workspace` mirror; the strongest single find among the recovered orphaned files.
**Researched:** 2026-09-05, via WebSearch/WebFetch against the Becker Friedman Institute and NBER — not general knowledge recall.

## 1. Exact citation — confirmed real, high-authority

"Financial Machine Learning" by **Bryan T. Kelly** (Yale School of Management / AQR Capital Management) and **Dacheng Xiu** (University of Chicago Booth School of Business) — published as Becker Friedman Institute Working Paper No. 2023-100, cross-published as NBER Working Paper No. 31502, and in *Foundations and Trends in Finance*. Title, authors, and PDF URL all verified directly via BFI and NBER.

## 2. Core claim

A comprehensive, well-cited survey of machine learning in empirical asset pricing: return prediction, conditional factor models (IPCA), portfolio optimization, and trading-cost-aware implementation. A recurring, directly relevant theme: **reported Sharpe ratios collapse roughly 4x once trading costs are included.**

## 3. Relevance — high and directly on-topic

This is a legitimate, heavily-cited academic survey by two of the most prominent names in empirical asset pricing/ML finance, covering exactly the concerns Signal Current needs: regularization, conditional factor models, cost-aware portfolio construction, walk-forward validation, and data-snooping warnings. Its own "trading cost reality check" theme echoes this project's own skepticism about backtested Sharpe ratios (see this program's earlier `PA-02-time-series-momentum.md` and `PA-02-valeyre-2022.md` reports, both of which found real critique literature on exactly this pattern).

## 4. What Signal Current could reuse

The survey's framing of ML-in-finance methodology (regularization approaches for high-dimensional factor models, IPCA-style conditional factor models, cost-aware portfolio optimization) as a methodology reference for the Validation & Statistical Controls spec. Its explicit "costs collapse Sharpe ~4x" finding is directly citable evidence supporting Signal Current's own Cost Model requirement (Reconciliation Matrix row 34).

## 5. What Signal Current should not do

Do not treat this as a source of specific tradeable signals — it is a survey/synthesis paper, not a strategy proposal. Do not skip independently verifying its cited sub-results before relying on any specific numeric claim from within it — this report verified the paper's existence and authorship, not every cited figure inside it (see unresolved questions).

## 6. Proposed disposition

**REFERENCE.** A strong, citable methodology source for Signal Current's Research Methodology and Validation & Statistical Controls specs.

## 7. Confidence level

**HIGH** on citation, authorship, and venue — directly verified via BFI's own PDF hosting and NBER's paper page, both independent, authoritative sources agreeing.

## 8. Unresolved questions

- Individual sub-claims/figures within the paper (e.g., the exact "4x" cost-collapse figure's derivation) were not independently re-verified against the paper's own methodology section — recommend a deeper read before citing a specific number in a spec doc.

## Sources

- https://bfi.uchicago.edu/wp-content/uploads/2023/07/BFI_WP_2023-100.pdf
- https://www.nber.org/papers/w31502
- Recovered from orphaned local file: `/home/d-tuned/.openclaw/workspace/life/resources/AlgoTradingIdeas/BFI_WP_2023-100_ML_Finance.md`
