# Candidate Report — Valeyre, "Optimal trend following portfolios" (2022)

**Stream:** PA-02 (Numerical & Statistical Methods), also relevant to PA-05 (Portfolio/Risk)
**Status:** Sixth candidate report for PA-02.
**Researched:** 2026-09-05, by directly reading the local PDF (`/home/d-tuned/life/resources/quant-research-papers/valeyre-2022-optimal-trend-following-portfolios.pdf`, 33 pages: title/abstract/intro, methodology derivation, empirical section, data table, conclusion, full reference list) plus corroborating web sources — not general knowledge recall.

## 1. Exact citation

Sébastien Valeyre, "Optimal trend following portfolios," **arXiv:2201.06635** (submitted 17 Jan 2022, q-fin.PM), preprint submitted to Elsevier. Verified directly from the PDF title page and header (author affiliation: Valeyre Research, Cannes, France). A later journal version appears to exist — *Journal of Investment Strategies*, 2024, vol. 12(3), per web search — this venue's page was not independently opened; **treat the journal citation as signpost, the arXiv version as pillar** (directly read).

## 2. Core claim

Derives a closed-form "optimal" trend-following portfolio from a Gaussian autocorrelation model of asset returns (extending Grebenkov & Serror 2015) that decomposes into four basic component portfolios — naive Markowitz, risk parity (RP), agnostic risk parity (ARP, from Benichou et al. 2017), and trend-on-risk-parity (ToRP) — and claims a specific weighted combination (ARP 19.5% / RP 51% / ToRP 30%) beats each component individually in backtest, reaching Sharpe 1.37 vs. RP alone at 1.24 (read directly, p.17).

## 3. Coverage

47 futures instruments — 24 stock-index futures, 14 bond-index futures, 9 FX pairs (Table 1, p.15, read directly). Data from 8 May 1985–31 Dec 2018; Sharpe/backtest stats computed 1 Jan 1993–27 Aug 2020.

## 4. Methodology precision

Precisely specified mathematically (explicit EMA signal formula, EMA decay rate η=1/100, covariance-matrix estimation via weekly-return EMA with η′=1/750, correlation cleaning via a random-matrix "rotational invariant estimator") — the math is reproducible in principle by a quant with the arXiv text. **However, it is not directly replicable without the author's private futures dataset** (no public data/code release found on arXiv or elsewhere). The paper itself concedes: "the implemented portfolio can be different from the theoretical formula" because practical constraints (liquidity, market impact) are not in the optimization (p.13).

## 5. Transaction costs / slippage — explicitly not modeled

Direct quote (p.13): **"We do not take into account transactions cost and market impact."** The author separately flags that ToRP's higher turnover "is expected to be smaller when including the market impact" (p.16) — i.e., the paper's own author concedes the reported ToRP/ARP-mix Sharpe ratios are optimistic before costs.

## 6. Multiple-testing / data-mining bias

Not addressed with any formal correction (no bootstrap, no out-of-sample holdout, no multiple-comparison adjustment across the 4-5 portfolio variants tested). The paper does candidly note the optimal ARP/ToRP mixture weights are "not so robust and are very sensitive to the estimation of the Sharpe ratio of each strategy that can change from period to period" (p.16) — an in-paper acknowledgment of instability, but not a statistical robustness test.

## 7. Known follow-ups / critiques

A 2025 paper "Breaking the Trend: How to Avoid Cherry-Picked Signals" (arXiv:2504.10914) cites Valeyre's later work directly and argues, in the same spirit as this paper's own framing, against complex multi-indicator signal cherry-picking in favor of a single well-tuned EMA — this is a continuation/validation of the approach rather than a refutation. A Substack post (harbourfrontquant) characterizes the model as depending on historical data with robustness "not tested." **No formal replication study or statistical critique analogous to Huang/Li/Wang/Zhou (2020) on Moskowitz et al. was found** — this appears to be a much less-cited, less-scrutinized paper than the Time Series Momentum paper reported on separately in `PA-02-time-series-momentum.md`. Search coverage of citing literature was not exhaustive (no citation-count check performed), so absence of a major critique is not proof none exists.

## 8. What Signal Current could reuse

The decomposition framework itself (RP / ARP / ToRP / Markowitz as basis portfolios, expressing any allocation as a linear combination of them) is a useful conceptual taxonomy for portfolio-construction research — as a reference lens, not as validated performance numbers.

## 9. What Signal Current should not do

Do not treat any Sharpe ratio in this paper (1.13–1.37 range) as an expectation for live or even honest backtest performance — costs/impact are explicitly excluded, the combination weights are self-described as fragile, and no multiple-testing correction was applied across the several portfolio variants compared.

## 10. Proposed disposition

**REFERENCE** (as a conceptual/theoretical framework for portfolio-construction taxonomy) — **not worth independently reproducing** as a performance claim, since the required proprietary futures dataset and the exact cleaning/RMT-based covariance procedure would need a full replication effort for a paper with a materially weaker external-validation footprint than Moskowitz et al.

## 11. Confidence level

**MEDIUM.** High confidence in what the paper itself states — read directly from the source PDF across all key sections. Lower confidence on the external critique landscape — search coverage of citing literature was not exhaustive.

## 12. Unresolved questions

- Whether the 2024 *Journal of Investment Strategies* version differs materially from the 2022 arXiv preprint — not independently verified.
- Whether any formal statistical-significance/bootstrap critique of this specific paper exists in journals not surfaced by search.
- Whether the underlying 47-instrument dataset or code is available anywhere for independent reproduction — none found.

## Sources

- https://arxiv.org/abs/2201.06635 (paper, read in full from local PDF)
- https://arxiv.org/html/2504.10914v1 ("Breaking the Trend: How to Avoid Cherry-Picked Signals")
- https://harbourfrontquant.substack.com/p/covariance-matrix-of-trends-and-risk
- Local file: `/home/d-tuned/life/resources/quant-research-papers/valeyre-2022-optimal-trend-following-portfolios.pdf`
