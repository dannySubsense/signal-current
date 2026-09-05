# Signal Current — Canonical Specification Set

**Status:** Specification phase / pre-v1.0 freeze  
**Current authority:** Working drafts only. No document becomes normative until independent review and v1.0 freeze.

## Deliverable Structure

This directory is the actual Signal Current specification artifact. The chat is the review and decision surface; the Markdown files are the durable work product.

| # | Document | Status |
|---:|---|---|
| 00 | Source Inventory & Reconciliation Matrix | **Working draft v0.2 — revised after architecture challenge** |
| 00A | Prior Art & Reuse Research Program | **Active — PA-01 through PA-10 created** |
| 01 | Signal Current Constitution | Not started |
| 02 | System Architecture Specification | Not started |
| 03 | Research Methodology Specification | Not started |
| 04 | Data Architecture & Strategy IR | Not started |
| 05 | Validation & Statistical Controls | Not started |
| 06 | Portfolio, Deployment & Monitoring | Not started |
| 07 | Agent & Orchestration Layer | Not started |
| 08 | Implementation Roadmap | Not started |
| 09 | Independent Review Record | Not started |
| 10 | v1.0 Freeze Manifest | Not started |

## Working Sequence

Reconciliation + Reuse Research → Constitution → Architecture → Methodology → Schemas/Contracts → Validation → Portfolio/Deployment/Monitoring → Agent Layer → Roadmap → Independent Review → Repair → Freeze v1.0 → Build

## Authority Rules

1. Prior chats and research artifacts remain evidence, not authority.
2. Working drafts may be revised as contradictions are discovered.
3. No implementation decision may silently override a frozen specification.
4. v1.0 is frozen only after independent review closes all blocking contradictions.
5. Post-freeze changes require an explicit amendment/ADR and version change.


## Prior Art Research Freeze Dependency

The Prior Art & Reuse Research Program (`prior_art_reuse/`) is a formal specification input.

The Constitution may proceed in parallel. Architecture, StrategyIR/Data, Validation, Portfolio/Deployment, and Agent specifications must incorporate the relevant prior-art findings before freeze.
