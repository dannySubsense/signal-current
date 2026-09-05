# Candidate Report — MASTER: Market-Guided Stock Transformer

**Stream:** PA-04 (Search/Optimization/ML)
**Status:** Third candidate report for PA-04. Resolves a duplicate-capture flag raised by the original homelab survey.
**Researched:** 2026-09-05, via WebSearch/WebFetch against AAAI's proceedings page, arXiv, and the official GitHub repo — not general knowledge recall.

## 1. Exact citation

Li, T., Liu, Z., Shen, Y., Wang, X., Chen, H., & Huang, S. (2024). "MASTER: Market-Guided Stock Transformer for Stock Price Forecasting." *Proceedings of the AAAI Conference on Artificial Intelligence*, 38(1), 162–170. DOI: 10.1609/aaai.v38i1.27767. Also on arXiv as 2312.15235 (submitted 2023-12-23). Both local capture files' citations match this exactly.

## 2. Duplicate-capture resolution

**The two local captures are not strict duplicates — they differ meaningfully and should be merged, not blindly deleted:**

- `/home/d-tuned/life/resources/quant-research-papers/master-market-guided-stock-transformer.md` (added 2026-03-20) is deeper and technical: reproduces the full architecture (equations for gating, intra-/inter-stock attention, temporal aggregation, complexity table), appears to be a direct read of the PDF, and has its own "Relevance to Global Market World View Project" section tied to a different project.
- `/home/d-tuned/life/projects/quant-strat-lab/papers/li2024-master-market-guided-stock-transformer.md` (added 2026-03-09, earlier) is a lighter summary with working links (AAAI page, DOI) and a "Research Applications" section cross-referencing Danny's other projects (Gap-Lens, AskEdgar, ML Pipeline).

Neither is a strict subset of the other. **Recommendation for Danny's homelab collection (not a signal-current task): keep the resources-folder file as canonical (it has the real substance), fold in the DOI/arXiv links and the "Research Applications" cross-references from the projects-folder file, then delete the duplicate projects-folder stub.** This is a genuine one-source-two-captures documentation issue, not a data-corruption one — flagged for Danny's own housekeeping, not actioned by this report.

## 3. Core claim / contribution

MASTER models stock correlations that are (a) **momentary** — occurring at specific time steps rather than uniformly — and (b) **cross-time** — correlations between stock u at time i and stock v at time j, not just time-aligned. It adds a market-guided gating mechanism that reweights input features based on current market index/volume statistics before the transformer encodes them. Architecture alternates intra-stock (temporal, per-stock) and inter-stock (cross-sectional, per-timestep) attention layers, then does a temporal pooling attention over the resulting embeddings to produce one prediction per stock.

## 4. Code released — real, but with a disclosed, directly-relevant reproducibility gap

Yes — official repo `SJTU-DMTai/MASTER` (https://github.com/SJTU-DMTai/MASTER), MIT license. Real and usable (pretrained models, performance spreadsheet, Qlib integration), but: **no automated test suite**, and **a 2025-06-26 repo notice discloses the authors themselves found the released Qlib validation pipeline used `learn_processor` instead of `infer_processor` on the validation set** — a train/val processing inconsistency — and that hyperparameters were untuned against the newer open-sourced data, which "covers a different timespan" than the paper's original (partly proprietary) experiments. **This is a real, first-party-disclosed reproducibility gap, straight from the repo, not a rumor or third-party critique.**

## 5. Markets / timeframe

Chinese A-share universes: CSI300 (~300 stocks) and CSI800 (~800 stocks, CSI300 is a subset). Exact original experiment timeframe not confirmed (proprietary data); the open-sourced replacement data covers a different span than the paper's original experiments, per the repo's own disclosure.

## 6. Look-ahead bias / transaction costs / OOS robustness — unresolved

Not confirmable from what was fetched (abstract-level pages, not the full paper text). The repo reports IC/RankIC (return-prediction correlation), not portfolio-level backtests with costs — a separate Qlib integration reportedly can produce AR/IR portfolio metrics, but this claim was not independently verified against Qlib docs. Whether the paper itself discusses look-ahead-bias controls or true out-of-sample splits could not be confirmed without reading the full PDF text — flagged as genuinely unresolved rather than asserted either way.

## 7. Known critiques / replications / follow-ups

No independent replication study or critique paper found via search. The most substantive finding is the authors' own self-disclosed data-processing bug noted above (2025-06-26 repo update) — a first-party correction, not a third-party critique. No adversarial replication attempt located.

## 8. What Signal Current could reuse

Methodology only, as REFERENCE: the intra-/inter-stock alternating attention pattern and market-regime gating idea are conceptually reusable for feature-selection/ML search design — genuinely documented in both local files and the paper. Code reuse is possible in principle (MIT license, real repo) but should not be treated as validated infrastructure given the disclosed validation-pipeline bug and lack of tests — if ever adopted, Signal Current would need to independently re-verify the train/val split logic itself, not inherit the repo's claims.

## 9. What Signal Current should not do

Do not cite MASTER's reported performance numbers as a validated baseline without re-running the repo against Signal Current's own data and split logic — the repo's own maintainers have flagged a validation-set processing bug. Do not assume transaction-cost or look-ahead-bias handling exists in the paper without reading the full paper text — this could not be confirmed either way this pass. Do not treat CSI300/CSI800 Chinese-market results as transferable to Signal Current's target markets without separate justification.

## 10. Proposed disposition

**REFERENCE** (architectural/methodology idea only). Not ADOPT/ADAPT-code at this time — the disclosed validation-pipeline bug and absence of tests mean the released code needs independent verification before any reuse, out of scope for this research pass.

## 11. Confidence level

**MEDIUM.** High confidence on citation, authors, venue, DOI, repo existence/license — directly fetched from AAAI and GitHub. Lower confidence on dataset/timeframe detail and especially on look-ahead-bias/transaction-cost claims, since the full paper PDF text was not fetched/parsed — only abstract-level pages.

## 12. Unresolved questions

- Does the full paper text address look-ahead bias, walk-forward validation, or transaction costs anywhere in its experiments section? Requires reading the actual PDF.
- What exactly was the impact of the disclosed validation-processor bug on reported metrics — the repo claims "no significant impact," but this is a self-assessment, not independently verified.
- Original (pre-open-source) experiment timeframe is still unspecified.

## Sources

- https://ojs.aaai.org/index.php/AAAI/article/view/27767
- https://ojs.aaai.org/index.php/AAAI/article/view/27767/27575
- https://arxiv.org/abs/2312.15235
- https://github.com/SJTU-DMTai/MASTER
- Local: `/home/d-tuned/life/resources/quant-research-papers/master-market-guided-stock-transformer.md`
- Local: `/home/d-tuned/life/projects/quant-strat-lab/papers/li2024-master-market-guided-stock-transformer.md`
