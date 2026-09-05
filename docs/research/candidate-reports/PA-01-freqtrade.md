# Candidate Report — Freqtrade

**Stream:** PA-01 (Systems & Engine Survey)
**Status:** Fourth candidate report for the prior-art research program. Originally surfaced via the homelab resource survey (`docs/research/prior-art/PA-10-reuse-decision-register.md`, sourced 2026-09-05).
**Researched:** 2026-09-05, via primary-source fetches (live repo, LICENSE file, docs, tests directory, GitHub API/`gh api`) — not general knowledge recall.

## 1. Repository / canonical URL

`freqtrade/freqtrade` — https://github.com/freqtrade/freqtrade.

## 2. Version / commit reviewed

`develop` branch HEAD, commit `29186a9a0e62af7e52a0f0d386c8b02a0d4b2206` (2026-09-05T07:17:51Z, matches `pushed_at`). Latest tagged release: `2026.8` on `stable`, published 2026-08-31 — Freqtrade uses monthly CalVer releases. `requirements.txt` at develop pins `ccxt==4.5.76`.

## 3. License — clean history, no drift found

**GPL-3.0.** Confirmed by decoding the actual `LICENSE` blob (text begins "GNU GENERAL PUBLIC LICENSE Version 3, 29 June 2007"). Checked history on the file specifically: exactly **one** commit has ever touched `LICENSE` — the 2017-05-17 initial commit. No license change has ever occurred here, unlike the NautilusTrader and vectorbt cases in this program. GitHub's SPDX field (`gpl-3.0`) agrees with the direct file read — no discrepancy.

## 4. Maintenance / activity

Very active: 54,043 stars, 11,210 forks, 776 subscribers. 242 commits in the last 30 days. Contributors: pagination indicates 400+ distinct contributors. 30 open issues; a small, well-triaged open-PR backlog (~6). Monthly CalVer releases confirm a real, sustained cadence.

## 5. Architecture summary

Strategy interface centers on `populate_indicators()` / `populate_entry_trend()` / `populate_exit_trend()` as the core signal-generation contract, plus an extensive set of lifecycle callbacks (`custom_stoploss`, `custom_roi`, `adjust_trade_position`, `leverage`, `confirm_trade_entry/exit`). Exchange access is mediated through a `self.dp` (DataProvider) object inside strategies — strategy code never calls ccxt directly. Exchange adapters wrap ccxt (100+ exchanges), with per-exchange hardcoded quirks (Kraken rate-limit semantics, Binance/Kucoin native-fee-token blacklisting, OKX/Kucoin passphrase fields). Backtest simulates against OHLCV candles with documented assumptions: entries fill at candle open (no slippage) unless a custom-price callback is set; exit signals fill at the next candle's open; an explicit priority order governs simultaneous signals (Exit-signal → Stoploss → ROI → Trailing stoploss).

## 6. Relevant modules

`freqtrade/freqai/` is a mature, separate adaptive-ML subsystem (`data_kitchen.py`, `data_drawer.py`, `freqai_interface.py`, `base_models/`, `prediction_models/`, `RL/`, `torch/`, `tensorboard/`). Test directory has dedicated subdirectories: `exchange/`, `exchange_online/`, `freqai/`, `freqtradebot/`, `leverage/`, `optimize/`, `persistence/`, `plugins/`, `rpc/`, `strategy/`, plus `lookahead-analysis` and `recursive-analysis` CLI subcommands — tooling that exists specifically to catch backtest-vs-live signal leakage, a real design-maturity signal.

## 7. Tests and test quality

Codecov badge wired to `develop` (actual coverage percentage not independently verified — would require hitting codecov.io directly, not done this session). Test suite is structurally comprehensive: separate online vs. offline exchange tests, dedicated freqai and leverage suites. Exact pass/fail counts not run this session.

## 8. Deterministic / reproducibility properties — self-admitted limitation, documented not inferred

`docs/backtesting.md` states verbatim: **"Reproducibility of backtesting-results cannot be guaranteed"** when using dynamic pairlists, since current market conditions don't reflect historical pairlist membership. This is a documented, self-admitted non-determinism risk, not something this research inferred.

## 9. Exchange/asset coupling depth — the central finding

Exchange is **adapter-isolated at the strategy-interface layer, but not fully abstracted at the semantic layer.** Strategy base-class methods (`populate_*`, custom callbacks) never reference ccxt or exchange objects directly — access goes through `self.dp`. But core trading semantics remain crypto/exchange-native by design:

- Pair format is hardcoded to `BASE/QUOTE` (e.g. `BTC/EUR`).
- A single global `stake_currency` config concept is baked into the config schema.
- "Leverage" and "futures vs. spot mode" are first-class strategy-callback concepts — `leverage()` is a method on the strategy interface itself.
- Backtest signal-priority logic (stoploss/ROI/trailing-stoploss) assumes crypto-perpetual-style mechanics.

So: adapter isolation exists for exchange *connectivity*, but pair/quote-currency/leverage/futures semantics are baked into the strategy interface and config schema, not pushed down into an adapter layer. This claim rests on doc prose (`docs/strategy-callbacks.md`, `docs/exchanges.md`), not a direct source-code grep of `freqtrade/strategy/interface.py` — flagged medium confidence, not settled at the code level.

## 10. Hidden defaults or semantic coupling

Incomplete/forming candles are **silently dropped by default** (`docs/exchanges.md`) — a runtime default with real backtest-vs-live implications, discovered here as a risk, not assumed benign. Backtest fill assumes open-price execution unless a custom-price callback is defined — documented, not a bug, but any parity-oracle work must instrument around it explicitly.

## 11. Performance characteristics

No load/throughput benchmarks found in primary sources fetched this session — not claimed; a benchmarks page may exist elsewhere but was not located.

## 12. What Signal Current could reuse

The lookahead/recursive-bias analysis CLI concept (`lookahead-analysis`, `recursive-analysis`) is a strong, directly-relevant pattern for catching backtest self-deception — aligned with Signal Current's "hard to fool ourselves with" goal. The documented, explicit list of backtest-vs-live divergence points (fill assumptions, signal-priority ordering, dynamic-pairlist non-reproducibility) is a good checklist template for Signal Current's own parity documentation, regardless of whether any code is reused.

## 13. What Signal Current should not inherit — the central risk for this candidate

`BASE/QUOTE` pair format, a single global `stake_currency`, and `leverage()`-as-strategy-method are all crypto-native assumptions embedded **above** the adapter boundary. Reusing Freqtrade's strategy base class or config schema directly would import a privileged "quote currency" concept into Signal Current's asset-agnostic core — a direct violation of the project's "no privileged market dimension" invariant. This risk is structural, confirmed by reading the actual interface docs, not inferred from reputation.

## 14. Integration / coupling risks

GPL-3.0 is strong copyleft — any direct code reuse (not just architectural pattern-borrowing) in Signal Current would trigger GPL obligations on the combined work. This must be flagged to Danny before any code-level reuse is considered, not just noted here.

## 15. Required parity / golden tests if used as reference

If used only as a design reference: independently reproduce (a) open-price-fill-vs-actual-fill divergence, (b) signal-priority-order edge cases, (c) dynamic-universe/pairlist reproducibility failure mode — each with an asset-agnostic equivalent test, not a copy of Freqtrade's crypto-specific fixtures.

## 16. Proposed disposition

**REFERENCE only** — not ADOPT/FORK/WRAP. GPL-3.0 blocks code reuse without triggering copyleft; core strategy/config semantics are too crypto-native to WRAP cleanly without leaking pair/stake-currency/leverage concepts into an asset-agnostic core. Its documented backtest-vs-live divergence checklist and lookahead-bias-detection CLI pattern are legitimately valuable prior art for designing Signal Current's own parity-testing spec.

## 17. Confidence level

**MEDIUM-HIGH.** License history, activity metrics, and version/commit identification are HIGH confidence (direct API/file reads). Architecture and exchange-coupling-depth claims are MEDIUM — sourced from doc pages fetched directly, not from reading actual Python source (`freqtrade/strategy/interface.py`, `freqtrade/exchange/exchange.py` were not opened this session).

## 18. Unresolved questions

- Have not opened `freqtrade/strategy/interface.py` or `freqtrade/exchange/exchange.py` source directly to verify doc-derived coupling claims at the code level.
- Codecov actual coverage percentage not fetched.
- No performance/throughput benchmark data located — unknown if one exists elsewhere (e.g. freqtrade.io).
- Only one commit has ever touched LICENSE, so historical license-badge divergence is a low-risk item here, unlike the vectorbt case — but this was not exhaustively cross-checked beyond confirming current agreement between the SPDX field and the file.

## Sources

- https://github.com/freqtrade/freqtrade
- `gh api repos/freqtrade/freqtrade`
- `gh api repos/freqtrade/freqtrade/releases`
- `gh api "repos/freqtrade/freqtrade/commits?path=LICENSE"`
- `gh api repos/freqtrade/freqtrade/contents/LICENSE`
- `gh api "search/commits?q=repo:freqtrade/freqtrade+committer-date:>2026-08-06"`
- https://raw.githubusercontent.com/freqtrade/freqtrade/develop/docs/strategy-callbacks.md
- https://raw.githubusercontent.com/freqtrade/freqtrade/develop/docs/backtesting.md
- https://raw.githubusercontent.com/freqtrade/freqtrade/develop/docs/exchanges.md
- `gh api repos/freqtrade/freqtrade/contents/tests`
- `gh api repos/freqtrade/freqtrade/contents/requirements.txt`
- `gh api repos/freqtrade/freqtrade/contents/freqtrade/freqai`
- `gh api repos/freqtrade/freqtrade/contents/README.md`
