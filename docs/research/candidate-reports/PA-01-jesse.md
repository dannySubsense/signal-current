# Candidate Report — Jesse

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Ninth candidate report for the prior-art research program.
**Researched:** 2026-09-05, via primary-source fetches (GitHub API/`gh api`, LICENSE file, commit/contributor history, jesse.trade pricing page) — not general knowledge recall.

## 1. Repository / canonical URL

`jesse-ai/jesse` — https://github.com/jesse-ai/jesse. Org-owned (not single-maintainer's personal repo), created 2018-11-09.

## 2. Version / commit reviewed

HEAD of `master`, commit `44a0ed43` (2026-09-04T17:03:16Z), tagged `v3.1.1`. Steady release cadence confirmed back through v3.1.0, v3.0.8, v3.0.0…v2.2.1.

## 3. License — clean, no drift

MIT, read directly from the `LICENSE` blob: copyright "(c) 2020 Jesse.Trade," standard MIT text, no additional clauses (no Commons Clause, no field-of-use restriction). Checked history on the file: only two commits ever touched it — the 2020-04-07 initial release and a 2020-11-25 "update license" commit. No drift toward a restrictive/dual license as seen in NautilusTrader/vectorbt in this program. Confirmed clean MIT, no license-drift risk found.

## 4. Maintenance / activity

Very active — most recent commit is from the day of this research (2026-09-04), multiple commits/day cadence visible in the log. 51 total contributors, but concentration is heavy: founder `saleh-mir` has 2,177 commits vs. the next-highest human contributor at 185 — a classic founder-led-project distribution, not abandoned, but a real bus-factor risk. 16 open issues (low, suggesting active triage or aggressive closing).

**Commercial tier confirmed and material.** jesse.trade sells hosted/licensed plans (Base $899, Popular $999, Professional $1,599), and an FAQ page titled "Why do I have to pay for live? I thought it's open source" exists (fetch blocked 403, but the URL/title corroborates the pattern independent of body text). This is the vectorbt/vectorbt.pro pattern confirmed for Jesse: **OSS = backtest/research, paid = live execution license.** Commit velocity is still high and current, so OSS does not appear starved by the commercial split — but the live-trading gate is a real constraint for anyone assuming full parity from the OSS repo alone.

## 5. Architecture summary

Strategy base class (`jesse/strategies/Strategy.py`, ABC) with lifecycle hooks (`before`, `after`, `should_long`, `go_long`, etc.), order/position/route model classes (`jesse/models/`), a central event-loop-style `store` (`jesse/store/`), broker/order services (`jesse/services/broker.py`, `order_service.py`), and a `jesse/modes/` package split into `backtest_mode.py`, `optimize_mode/`, `monte_carlo_mode/`, `significance_test_mode/`, `import_candles_mode/`. Live and paper trading exist per README claims (multi-account, DEX support, notifiers), but the OSS repo's live-mode code path is gated by the licensing service referenced above — this was not fully enumerated at the source level this pass (see §18).

## 6. Relevant modules

`jesse/strategies/Strategy.py` (base class, read directly — imports candle pipelines, broker, metrics, ML loader inline), `jesse/exchanges/` (`exchange.py` + `sandbox/` subdir), `jesse/services/exchange_service.py`, `jesse/indicators/` (300+ indicators per README, partly Rust-backed per README's "Rust-Powered Indicators" claim — not independently verified in source), `jesse/modes/optimize_mode/` (Optuna + Ray per README), `jesse/research/` (Research API/Jupyter surface, includes `ml.py` referenced in `Strategy.py`).

## 7. Tests and test quality

Substantial: `tests/` has 40+ top-level test files (`test_backtest.py`, `test_exchange.py`, `test_metrics.py`, `test_simulator_parity.py`, `test_rule_significance_testing.py`, `test_e2e_database.py`, etc.), plus `jesse/strategies/` contains ~140 dedicated `Test*` fixture-strategy directories used as backtest fixtures for specific behaviors (leverage, liquidation, spot mode, partial fills, DNA/optimization). Codecov badge present — coverage tracked, not independently pulled this pass. This is materially more thorough test coverage than typical for this category among candidates examined so far.

## 8. Deterministic / reproducibility properties

README explicitly claims backtesting "without look-ahead bias," and dedicated `test_isolated_backtest.py` / `test_research_optimization_isolation.py` files exist, suggesting isolation is a designed, tested property rather than merely asserted. Not independently reproduced this pass — would require running the test suite.

## 9. Crypto-exchange coupling depth — the central finding

Core semantics are crypto-native by design, **not** adapter-isolated at the edges only — a deeper coupling than vn.py's clean adapter isolation found in the previous candidate report. `Strategy.py` and models directly import `FuturesExchange`, `SpotExchange` classes; leverage/liquidation/margin logic is baked into base Position/Order models, evidenced by multiple dedicated tests (`TestLiquidationInCrossModeForShortTrade`, `TestFuturesExchangeAvailableMargin`, `TestBalanceAndFeeReductionWorksCorrectlyInSpotModeInBothBuyAndSellOrders`). Spot vs. futures vs. leverage vs. liquidation are first-class concepts throughout the core, not confined to an exchange-driver layer. **Answering the same-class question asked of Freqtrade and vn.py: Jesse is not equity/traditional-asset-portable without meaningful rework** — margin/liquidation/leverage semantics are load-bearing in the core order/position model, not bolted on.

## 10. Hidden defaults or semantic coupling

One artifact worth flagging for this project's own number-provenance discipline: `Strategy.py` has `LIVE_CHART_MAX_POINTS_PER_LINE = 1_000`, a rolling cap for chart-line arrays "during live sessions" — exactly the class of engineering-default constant the global CLAUDE.md rule flags as needing a citation or PROVISIONAL tag. It appears to be a UI/memory guard, not obviously a research-path number, but if Signal Current ever reuses code touching chart/metrics data for research conclusions, this constant should be re-justified or explicitly excluded — the same PROMOTED DEFAULT pattern found in Qlib's CN-region config, at a smaller scale.

## 11. Performance characteristics

README claims Rust-native indicator implementations for speed ("substantially faster") — unverified/unbenchmarked this pass; treat as vendor claim only.

## 12. What Signal Current could reuse

The Strategy lifecycle-hook pattern (`should_long`/`go_long`/`before`/`after`), the Rule Significance Testing feature (bootstrap-based entry-rule significance testing — directly relevant to Signal Current's own statistical-checks goal), and the Monte Carlo trade-shuffling approach are strong conceptual reference points, MIT-licensed and clean to draw from as REFERENCE material.

## 13. What Signal Current should not inherit

The core Position/Order/Exchange model — spot/futures/leverage/liquidation semantics are too deeply crypto-specific to serve as Signal Current's execution model if multi-asset-class support is a goal (it is, per this project's "no privileged market dimension" invariant). Also should not assume feature parity with the paid tier when evaluating "live trading support" — that capability is license-gated, not purely open.

## 14. Integration / coupling risks

Founder bus-factor (2,177 vs. 185 commits) is the single-vendor-dependency risk. The MCP-server feature (`jesse/mcp/`) is new and AI-facing — interesting but immature, worth checking version history if ever adopted.

## 15. Required parity / golden tests if used as reference

Would need independent reproduction of `test_simulator_parity.py` and `test_isolated_backtest.py` results, plus a manual audit distinguishing OSS-only code paths from paid-license-gated live-mode code, before citing "look-ahead-bias-free" as a validated property rather than a vendor claim.

## 16. Proposed disposition

**REFERENCE** — not ADOPT/FORK/ADAPT/WRAP. MIT license is genuinely clean, maintenance is genuinely active (contra a naive "check the badge" approach — verified via direct commit history), but core semantics are crypto-asset-coupled deep enough that adopting the engine itself would import unwanted domain assumptions into Signal Current, and the live-trading capability is commercially gated — "adopting" it for a live path means adopting a paid dependency, not pure OSS. Best used as a design reference for the Rule Significance Testing / Monte Carlo statistical-validation pattern and the Strategy lifecycle-hook API shape.

## 17. Confidence level

**MEDIUM-HIGH.** High confidence on license text, commit recency, contributor distribution, and directory-level architecture — all read directly from primary source. Medium confidence on the paid-tier FAQ content (site fetch returned 403; conclusion rests on URL title + separately-confirmed pricing page, not the FAQ's actual body text) and on Rust-indicator performance claims (unverified, vendor-stated only).

## 18. Unresolved questions

- Exact FAQ text on what's gated vs. free — fetch blocked, needs an authenticated/alternate fetch.
- Actual test coverage percentage from Codecov (badge seen, number not pulled).
- Whether `jesse/modes/` live-trading code path is present-but-license-locked in the OSS repo, or genuinely absent and shipped only in a private/paid distribution — this materially affects the WRAP-vs-REFERENCE calculus and should be checked by reading `jesse/modes/` live-mode source directly, not inferred from README.

## Sources

- https://github.com/jesse-ai/jesse
- https://api.github.com/repos/jesse-ai/jesse
- https://raw.githubusercontent.com/jesse-ai/jesse/master/LICENSE (fetched via `gh api` contents)
- https://api.github.com/repos/jesse-ai/jesse/commits?path=LICENSE
- https://api.github.com/repos/jesse-ai/jesse/contributors
- https://api.github.com/repos/jesse-ai/jesse/tags
- https://jesse.trade/pricing
- https://jesse.trade/help/faq/why-do-i-have-to-pay-for-live-i-thought-its-open-source (title/existence only, body fetch 403)
