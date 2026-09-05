# Candidate Report — Kronos

**Stream:** PA-04 (Search/Optimization/ML)
**Status:** Second candidate report for PA-04. Independently re-verifies claims from the local homelab capture `/home/d-tuned/life/resources/AlgoTradingIdeas/kronos.md`.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, README, direct source read of `model/kronos.py` and `tests/test_kronos_regression.py`, arXiv abstract page, AAAI OJS proceedings record) — not general knowledge recall.

## Note on this report's purpose

This program has repeatedly found homelab-captured claims to be wrong or stale on re-check (a repo misattributed to the wrong author entirely in `PA-02-ml-finance-codes.md`; audit claims found stale in `PA-04-finrl.md`). This report set out to apply the same scrutiny to `kronos.md`'s three specific factual claims (AAAI 2026 venue, MIT license, arXiv 2508.02739). **All three held up.** This is itself worth recording explicitly — the absence of a defect is a data point, not an oversight in the check.

## 1. Repository / paper / publication venue

Canonical repo: `github.com/shiyu-coder/Kronos`, "Kronos: A Foundation Model for the Language of Financial Markets." Paper of the same title, authors Yu Shi, Zongliang Fu, Shuo Chen, Bohan Zhao, Wei Xu, Changshui Zhang, Jian Li, **arXiv 2508.02739** (q-fin.ST), submitted 2 Aug 2025 — confirmed matching title/authors between the GitHub repo and the arXiv abstract page.

**Publication venue verified independently, not just from the repo's own README claim**: *Proceedings of the AAAI Conference on Artificial Intelligence*, Vol. 40 No. 30 (AAAI-26 Technical Tracks), pp. 25366–25373, published 2026-03-14, DOI 10.1609/aaai.v40i30.39730 — found directly on the AAAI OJS proceedings site. The GitHub README's own news feed independently corroborates: "🚩 [2025.11.10] Kronos has been accpeted by AAAI 2026" (sic, typo in original).

## 2. Version reviewed

No formal GitHub releases or tags exist. Repo default branch `master`, latest commit at review time `67b630e67f6a18c9e9be918d9b4337c960db1e9a`, merged 2026-04-13 ("fix: preserve batch dimension in tokenizer and predictor training," PR #243). Pretrained model/tokenizer weights are pinned in the test suite by explicit Hugging Face revision hashes (`MODEL_REVISION`, `TOKENIZER_REVISION` in `tests/test_kronos_regression.py`), giving a de facto reproducible reference point even without repo tags.

## 3. License — confirmed clean, matches capture

Read directly from the `LICENSE` file (base64-decoded via GitHub Contents API): standard MIT text, copyright "(c) 2025 ShiYu." GitHub's license-detector API independently agrees (`spdx_id: MIT`). Only the current HEAD blob was checked; full commit history on the LICENSE file itself was not walked — flagged unresolved, though no drift is expected or suspected here.

## 4. Maintenance / activity — live and active, not a paper-dump

Created 2025-07-01, last push 2026-04-13, 38,482 stars, 6,423 forks, 270 open issues, 19 distinct commit contributors (author `shiyu-coder` plus 18 external contributors, including one merged external PR four months post-paper-acceptance). Materially more active than a typical one-shot academic drop.

## 5. Architecture summary

Two-stage design, confirmed via README and direct source read of `model/kronos.py`:
- **`KronosTokenizer`**: encoder/decoder Transformer blocks + a Binary Spherical Quantizer (BSQuantizer) producing hierarchical discrete tokens from continuous multi-dimensional OHLCV input (params include `s1_bits`/`s2_bits` for pre/post token splits, `beta/gamma0/gamma/zeta` BSQ hyperparameters, `group_size`).
- **`Kronos`**: a decoder-only autoregressive Transformer pretrained on the resulting token sequences.

"Foundation model" here means: pretrained once on a large multi-exchange OHLCV corpus, then usable zero-shot for forecasting or finetuned via the provided pipeline — not fundamentals/text, purely price-series K-lines.

## 6. Relevant modules

`model/kronos.py` (`KronosTokenizer`, `Kronos`, `KronosPredictor` with `.predict`/`.predict_batch`), `model/module.py` (building blocks), `finetune/{train_tokenizer.py, train_predictor.py, config.py, dataset.py, qlib_data_preprocess.py, qlib_test.py}`, `tests/test_kronos_regression.py`.

## 7. Tests and test quality

`tests/test_kronos_regression.py` is real, not a stub: pins exact HF model/tokenizer revisions, sets seeds, asserts numeric regression against a fixed CSV fixture (`rtol=1e-5`) and a hardcoded expected MSE (`MSE_EXPECTED = [0.008979, 0.003741]`, `tolerance=1e-6`) on CPU — a genuine golden-output regression test, but narrow (single fixture, CPU-only, small `pred_len`). Code search found only 3–4 test/test-like files total — no broader unit-test suite, no CI workflow file surfaced (not independently confirmed absent).

## 8. Deterministic / reproducibility properties

Pretrained weights ARE released on Hugging Face (`NeoQuasar/Kronos-mini/small/base`), confirmed via README links. **`Kronos-large` (499.2M params, presumably the best-performing variant) is explicitly closed/not released**, per both README and the homelab note. Training code IS released — this is not inference-only. However, finetuning depends on Microsoft Qlib and the authors' own A-share data pipeline, and the README carries an explicit disclaimer that the finetuning/backtest pipeline is "a simplified example... not a production-ready quantitative trading system." Full from-scratch pretraining reproducibility (the original 12B-record, 45-exchange corpus) is not verified — the raw training corpus itself does not appear to be released, only the resulting model weights.

## 9. Asset / timeframe / venue assumptions

Input must be OHLC(V) columns; `volume`/`amount` optional (zero-filled if absent). Max context length is architecture-fixed: 512 for Kronos-small/base, 2048 for Kronos-mini — **hard truncation enforced silently by `KronosPredictor`** per README ("will automatically handle truncation for longer contexts"). No explicit timeframe (1min/1D/etc.) is baked into the model itself — timestamps are passed in by the caller — but pretraining data provenance across "45 global exchanges" is asserted only in the README/paper abstract, not independently itemized or verified.

## 10. Hidden defaults or semantic coupling — directly relevant to this project's own rules

The silent truncation-on-overflow behavior in `KronosPredictor` is exactly the class of engineering default (a context-window guard) this project's own global rules flag as historically dangerous if silently inherited downstream. If Signal Current ever wraps this model, `max_context` truncation must be a first-class flagged column, never a silent drop. Sampling parameters (`T`, `top_p`, `top_k`, `sample_count`) materially change output with no single canonical default asserted anywhere as "correct" — each is closer to a hyperparameter than a validated constant.

## 11. Performance characteristics — unverified numbers, flagged explicitly

Third-party (secondary, not primary) summaries cite "93% RankIC improvement over leading TSFM," "87% over best non-pretrained baseline," "9% lower MAE in volatility forecasting," "22% improvement in generative fidelity." **These were not independently confirmed against the actual arXiv PDF's results tables this pass** — they are attributed to the paper by secondary blog posts, not directly quoted from primary text. No independent (non-author) benchmark reproduction was found. **Do not treat these specific percentages as confirmed — they need direct extraction from the PDF before use in any Signal Current architecture doc**, per this project's own "no unsourced number" rule.

## 12. What Signal Current could reuse

The discrete-tokenization concept for OHLCV (BSQuantizer approach) as an architectural reference point; the `KronosPredictor` API shape (normalize → tokenize → autoregress → denormalize) as a design pattern; the regression-test pattern (pinned model revision + seed + tight rtol) as a template for Signal Current's own golden-output tests if a third-party model is ever wrapped.

## 13. What Signal Current should not inherit

The finetuning/backtest example pipeline verbatim (the authors' own disclaimer: not production-grade — no portfolio optimization, risk neutralization, transaction costs, slippage); any unverified performance percentages from secondary sources; the silent-truncation behavior without making it explicit; any dependency on Qlib unless independently justified for Signal Current's own pipeline (see `PA-01-microsoft-qlib.md` for Qlib's own CN-region default risk).

## 14. Integration / coupling risks

Hard PyTorch + Hugging Face Hub dependency (network fetch of weights unless self-hosted); GPU recommended for batch mode; Python 3.10+ requirement. Kronos-large (biggest, presumably best-performing) is proprietary/closed — any reuse is capped at Kronos-base (102.3M params) at best. MIT license as-read imposes no reuse restriction beyond the standard permissive notice-preservation, but Kronos-large's closed status means "MIT" does not apply uniformly across the whole model family — only the open-sourced weights carry that license in practice (the LICENSE file governs the code repo; individual HF model-card licenses for the weights were not separately checked).

## 15. Required parity / golden tests if used as reference

Re-run `tests/test_kronos_regression.py` in Signal Current's own environment at the pinned revisions to confirm the numbers reproduce before trusting anything downstream; independently extract and re-derive at least one benchmark table from the actual PDF (not secondary summaries) before citing any percentage; add an explicit truncation-flag assertion (e.g. `assert len(input) <= max_context` or carry a `truncated=True` column) per this project's own data-integrity discipline if this model is ever wrapped.

## 16. Proposed disposition

**REFERENCE**, with PARITY ORACLE potential (its pinned regression test as an external checkpoint if Signal Current ever builds a comparable tokenizer). Not ADOPT/FORK/ADAPT — the finetuning pipeline is explicitly non-production per its own authors, and Signal Current's architecture should not be built directly on a self-described "simplified example." Not REJECT — this is a real, actively maintained, peer-reviewed (AAAI-26), genuinely open-weight project with real tests, worth studying as prior art for tokenization design.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on repo metadata, license, commit/activity data, AAAI venue (independently corroborated via the AAAI OJS proceedings page, not just the author's README), and code architecture — all read directly from primary sources. Medium confidence on the specific performance percentages (sourced from secondary summaries, not directly quoted from the primary PDF) and training-corpus reproducibility claims (45 exchanges, 12B records — asserted in README, not independently audited).

## 18. Unresolved questions

- The full arXiv PDF's results tables were not opened to quote verbatim — the 93%/87%/9%/22% figures are third-party-summarized, not primary-quoted.
- Full git history of the LICENSE file was not checked for changes over time, only current HEAD.
- Presence/absence of a CI workflow (`.github/workflows`) was not confirmed — test-quality assessment is based only on the one regression file found via code search.
- HF model-card license metadata for the individual weight repos was not verified separately from the code-repo LICENSE.

## 19. Verdict on the local capture's claims

- **AAAI 2026 venue — HELD UP**, confirmed independently via the AAAI OJS proceedings record (DOI 10.1609/aaai.v40i30.39730), not just the repo's self-reported claim.
- **MIT license — HELD UP**, confirmed by reading the actual LICENSE file content, standard MIT text, copyright ShiYu 2025.
- **arXiv 2508.02739 — HELD UP**, abstract page title/authors match exactly the GitHub repo and AAAI record.

**All three specific factual claims in `kronos.md` checked out against primary sources.**

## Sources

- https://github.com/shiyu-coder/Kronos
- https://arxiv.org/abs/2508.02739
- https://ojs.aaai.org/index.php/AAAI/article/view/39730
- https://github.com/shiyu-coder/Kronos/blob/master/LICENSE
- https://github.com/shiyu-coder/Kronos/blob/master/README.md
- https://github.com/shiyu-coder/Kronos/blob/master/tests/test_kronos_regression.py
- Local file: `/home/d-tuned/life/resources/AlgoTradingIdeas/kronos.md`
