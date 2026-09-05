# Candidate Report — "Time Series Momentum" (Moskowitz, Ooi & Pedersen, 2012)

**Stream:** PA-02 (Numerical & Statistical Methods)
**Status:** Fifth candidate report for PA-02. Independently substantiates a return-inflation concern flagged in the local homelab capture `/home/d-tuned/life/resources/AlgoTradingIdeas/time-series-momentum.md`.
**Researched:** 2026-09-05, via WebSearch/WebFetch against ScienceDirect, SSRN, and follow-up critique papers — not general knowledge recall.

## 1. Exact citation

Moskowitz, T.J., Ooi, Y.H., Pedersen, L.H., "Time Series Momentum," *Journal of Financial Economics*, vol. 104, no. 2 (May 2012), pp. 228–250. DOI: 10.1016/j.jfineco.2011.11.003. (ScienceDirect: https://www.sciencedirect.com/science/article/pii/S0304405X11002613; SSRN preprint: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2089463; author PDF: https://w4.stern.nyu.edu/facdir/lpederse/papers/TimeSeriesMomentum.pdf). The local note's citation matches this exactly.

## 2. Core empirical claim

Past 1–12 month own-asset return sign predicts next-month return sign across 58 liquid futures instruments (equities, bonds, currencies, commodities); a volatility-scaled, diversified portfolio of these signals earns large risk-adjusted returns with modest correlation to standard risk factors, and returns partially reverse at 12+ month horizons.

## 3. Coverage

58 futures/forward contracts across equity indices, government bonds, currencies, and commodities, roughly 1965–2009 (varies by instrument's history), monthly frequency.

## 4. Methodology precision

Signal = sign of trailing 12-month excess return; position sized to target constant ex-ante volatility (40% annualized per instrument, scaled by realized vol). This is reasonably well specified and is the paper's most reusable methodological contribution. However, the critique literature below shows the specific *combination* of lookback/holding/vol-target choices was not robustness-tested against multiple-testing, leaving replication ambiguity about how sensitive results are to those exact parameters.

## 5. Transaction costs

The paper discusses transaction costs and argues returns survive them, but subsequent literature flags that cost estimates in this genre of research carry significant uncertainty and don't fully capture futures-roll costs or market-impact at scale — a documented open question, not resolved by the original paper.

## 6. Multiple-testing / data-mining

The original paper does not run a formal multiple-testing correction across its lookback/vol-target parameter choices. This gap is exactly what later critique papers targeted.

## 7. Is the local note's "return-inflation" concern substantiated? Yes — a real, documented critique

- Kim, Tse & Wald, "Time series momentum and volatility scaling," *Journal of Financial Markets* (2016) — found TSMOM profits are largely attributable to the volatility-scaling overlay itself, not to the momentum/trend signal — i.e., a volatility-managed buy-and-hold captures similar performance.
- **Huang, D., Li, J., Wang, L., Zhou, G., "Time Series Momentum: Is It There?", *Journal of Financial Economics* (2020)** — the most direct rebuttal: asset-by-asset regressions show weak/no evidence of TSM in- or out-of-sample; the pooled-regression t-stat that drives the original headline result is not statistically reliable against parametric/nonparametric bootstrap critical values; and the strategy's realized performance is statistically indistinguishable from a naive historical-mean strategy that requires no "momentum" predictability at all.
- Alpha Architect's synthesis of further robustness concerns (secondary source, not peer-reviewed): out-of-sample Sharpe ratios reported in some re-tests turn negative across most parameterizations, and statistical power calculations suggest ~250 years of monthly data would be needed to confidently distinguish TSMOM from buy-and-hold at 5–10 year horizons — i.e., the original in-sample Sharpe figures likely overstate what's achievable/detectable going forward.

**This directly confirms the homelab note's flag was not an unsubstantiated internal worry — it points at a real, citable academic dispute.**

## 8. Known follow-up / critique papers

- Kim, A., Tse, Y., Wald, J. (author list to re-confirm on ScienceDirect directly — fetch was blocked with a 403 this pass), "Time series momentum and volatility scaling," *Journal of Financial Markets* (2016). https://www.sciencedirect.com/science/article/abs/pii/S1386418116301379
- Huang, D., Li, J., Wang, L., Zhou, G., "Time Series Momentum: Is It There?", *Journal of Financial Economics* (2020). https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3165284, https://www.sciencedirect.com/science/article/abs/pii/S0304405X19301953
- Alpha Architect commentary (secondary, useful synthesis): https://alphaarchitect.com/are-trend-following-and-time-series-momentum-research-results-robust/, https://alphaarchitect.com/time-series-momentum-theory-and-evidence/
- AQR's own summary page (interested party — one of the original authors, Pedersen, is affiliated with AQR; read with that conflict in mind): https://www.aqr.com/Insights/Research/Journal-Article/Time-Series-Momentum

## 9. What Signal Current could reuse

The paper's precise, reusable signal definition (sign of trailing 12-month excess return) and its volatility-targeting position-sizing formula (scale position to a constant annualized vol target using realized vol) are legitimate, well-specified research-methodology references, independent of whether the headline alpha claim holds up.

## 10. What Signal Current should not do

Do not treat the paper's headline Sharpe ratio, or any "3500% vs 450% S&P 500"-style cumulative-return figure, as an expected/target performance number. Per Huang et al. (2020) and Kim et al. (2016), a material share of that performance is attributable to the volatility-scaling overlay rather than the trend signal itself, and out-of-sample replications show performance decay/negative Sharpe in many parameterizations. **Any backtest inside Signal Current that reproduces something resembling this paper's returns should be checked against a vol-scaled buy-and-hold benchmark before being credited to "momentum"** — this is directly actionable guidance for Signal Current's own Validation & Statistical Controls spec.

## 11. Proposed disposition

**REFERENCE for methodology** (signal definition, vol-scaling formula) **but explicitly NOT REFERENCE for the return numbers** without independent reproduction against the Huang et al. (2020) bootstrap-test standard. This is a "worth reproducing independently" flag, not a blanket citation.

## 12. Confidence level

**MEDIUM-HIGH** on the citation and existence of critiques (verified via ScienceDirect/SSRN records across multiple independent searches). **MEDIUM** on completeness of the critique literature — there may be additional rebuttals/rebuttals-to-rebuttals not surfaced by this search (e.g., a direct response from the original authors or AQR to Huang et al. 2020, not found this pass).

## 13. Unresolved questions

- Exact author list/venue confirmation for the Kim et al. 2016 paper — title/authors inferred from search snippets, not directly opened (ScienceDirect blocked the fetch with a 403).
- Whether AQR or the original authors published a direct rebuttal to Huang et al. (2020) — not found this pass.
- Whether Signal Current's own eventual out-of-sample backtest of a TSMOM-style signal would replicate the original paper's numbers or the critique papers' weaker findings — this can only be resolved by Signal Current running its own test, not by further literature review.

## Sources

- https://www.sciencedirect.com/science/article/pii/S0304405X11002613
- https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2089463
- https://w4.stern.nyu.edu/facdir/lpederse/papers/TimeSeriesMomentum.pdf
- https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3165284
- https://www.sciencedirect.com/science/article/abs/pii/S0304405X19301953
- https://www.sciencedirect.com/science/article/abs/pii/S1386418116301379
- https://alphaarchitect.com/are-trend-following-and-time-series-momentum-research-results-robust/
- https://alphaarchitect.com/time-series-momentum-theory-and-evidence/
- https://www.aqr.com/Insights/Research/Journal-Article/Time-Series-Momentum
- Local file: `/home/d-tuned/life/resources/AlgoTradingIdeas/time-series-momentum.md`
