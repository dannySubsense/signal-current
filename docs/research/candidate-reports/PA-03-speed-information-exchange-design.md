# Candidate Report — "Speed, Information, and Optimal Exchange Design" (Semenas)

**Stream:** PA-03 (Data/Time/Instrument Semantics), directly relevant to Reconciliation Matrix row 21 / unresolved decision U-02 (fill/intrabar collision/execution semantics).
**Status:** Second candidate report for PA-03. **REJECT — the paper appears unverifiable and possibly does not exist as a distributable, citable document.**
**Researched:** 2026-09-05, via WebSearch across arXiv, SSRN, ResearchGate, Google Scholar, and the named author's own website — not general knowledge recall.

## Central finding: this could not be located anywhere in the public record

The local capture file names the paper "Speed, Information, and Optimal Exchange Design: A Dynamic Mechanism Approach to Latency Arbitrage," author "Jackson Semenas," affiliation "JS Financials," dated March 8, 2026, with **"Target Venue: Quantitative Finance / Journal of Economic Dynamics and Control."** Note this field says *target*, not *published in*.

**This paper could not be found on arXiv, SSRN, ResearchGate, Google Scholar, or on the author's own site's live research page.** Jackson Semenas is a real, identifiable person — a self-described solo quant trader/undergraduate ("Honours in Finance, Economics, and Statistics at ANU, commencing 2027") who runs "JS Financials" (jsfinancials.com.au) and posts self-published working papers there. His site's research page lists exactly two working papers as of this check — a Bitcoin binary-options pricing paper (July 2026) and an Australian immigration/inflation policy proposal (October 2025, submitted to Treasury, not peer-reviewed) — **neither is this paper**; no exchange-design/latency-arbitrage paper is linked there at all. No DOI, no journal record, no preprint exists anywhere located.

## 1. Exact citation

Cannot be independently confirmed. The only source for this citation is the local capture file itself.

## 2-4. Core claim / paper type / mechanism studied — cannot be independently verified

The local file describes Myerson mechanism design, an HJB dynamic extension, and a claim that batch auctions dominate continuous limit-order-book trading and speed bumps, explicitly framed as "extending Budish, Cramton & Shim 2015." **Per this project's own rule that content must be independently verifiable, this content is not restated as verified findings here — it cannot be pointed to anywhere except the local file itself, which is exactly the single-well-citation problem this project's global rules exist to catch.**

## 5. Critiques / follow-up / caveats

Cannot be assessed — there is nothing in the public record to check against.

## 6. What Signal Current could reuse — nothing, and here's why

Whether or not the underlying math is sound, this is not currently a citable, peer-reviewed, or even publicly-postable artifact. **It cannot serve as prior art or precedent for U-02 (intrabar collision/execution semantics) because this project's own global rule is explicit: "an unsourced number/claim does not pass" — a citable, reproducible source, an explicit PROVISIONAL tag with a named owner, or deletion. This item satisfies none of the three.**

Even the genuinely-published, real, well-known paper it claims to extend — Budish, Cramton & Shim (2015), *"The High-Frequency Trading Arms Race: Frequent Batch Auctions as a Market Design Response,"* *Quarterly Journal of Economics* — is itself a theoretical/policy paper about a *proposed* mechanism (frequent batch auctions), not a description of how any real exchange Signal Current will connect to (e.g., Coinbase, IBKR, Alpaca, live CLOB venues) actually behaves. So even in the best case where the Semenas paper is legitimate, it would be a second-order theoretical extension of a policy proposal, several steps removed from implementable, connectable execution semantics.

## 7. What Signal Current should not do

Do not treat this local capture as prior art for U-02 or any execution-semantics decision. Do not treat a theoretical/hypothetical market-design proposal — even the real Budish/Cramton/Shim paper it claims to extend — as descriptive of how real exchanges Signal Current will actually connect to behave.

## 8. Proposed disposition

**REJECT — do not cite, do not use as prior art.** Justification: (a) unverifiable — no independent record of the paper exists anywhere searched; (b) if genuine, it is self-published by a named individual on his own promotional site, with no peer review, no preprint-server listing, and not even present on that site's own research page; (c) the local file's own metadata ("Target Venue," not "Published in") signals it was never accepted anywhere; (d) even if genuine, it is a game-theoretic/policy paper about a hypothetical optimal-mechanism design, not a description of real exchange behavior — using it to justify Signal Current's fill/collision semantics would be exactly the "promoted default" failure pattern this project's global rules were written to prevent. **Recommend instead going to the real, verifiable literature this paper claims to extend**: Budish, Cramton & Shim (2015, QJE) for the batch-auction argument, and empirical market-microstructure papers (e.g., work by Menkveld, or Baldauf & Mollner) for anything meant to be descriptive of real venue behavior.

## 9. Confidence level

**HIGH** that this specific paper is unverified/unverifiable in the public record as of this research date, and that its named author is a real but non-peer-reviewed, self-publishing individual. **MEDIUM-LOW** on whether the paper simply hasn't been indexed yet vs. never having existed as a finished, distributable document — no PDF/preprint was fetched, only search-engine summaries of the author's bio and site.

## 10. Unresolved questions

- Does a PDF/docx of this paper exist anywhere retrievable — did the person who added it to the homelab collection obtain it directly from the author, bypassing any public posting?
- Is the JEL classification/keyword list in the local capture file the author's own, or fabricated by whatever pipeline generated the markdown summary?
- Should Signal Current instead directly source Budish/Cramton/Shim (2015) and any of Menkveld's or Baldauf/Mollner's peer-reviewed work for the actual U-02 decision, since those are independently verifiable? **This report's recommendation: yes.**

## Sources

- https://jsfinancials.com.au/
- https://jsfinancials.com.au/research
- https://arxiv.org/abs/2202.00127 ("On Market Design and Latency Arbitrage" — real, verifiable adjacent literature, not the paper in question)
- https://link.springer.com/article/10.1186/s40854-023-00491-5 ("Latency arbitrage and the synchronized placement of orders" — real, verifiable adjacent literature, not the paper in question)
- Local file reviewed: `/home/d-tuned/life/resources/quant-research-papers/speed-information-optimal-exchange-design.md`
