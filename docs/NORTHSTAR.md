# Northstar - signal-current

**Status**: ACTIVE

**Established:** 2026-09-05 · **Last reviewed:** 2026-09-05

## Purpose

Signal Current finds trading strategy ideas, tests them, and combines the good ones into
portfolios. It also verifies and monitors strategies once they trade live. It does not produce
market data, that is market-data's job, and it does not interpret dilution or float, that is
gap-lens's job.

## Thesis

A strategy only counts as validated if someone other than its author can rerun the same test and
get the same result.

## Non-goals

Signal Current does not source or certify market data. It does not interpret dilution or float.
It does not place live trades automatically yet, live trading starts as observation and paper
trading only. It does not use a threshold, lookback window, or cutoff without a source, an owner
if it is still provisional, or removal.

## Drift check

This project has drifted if it starts producing its own market data, if it starts interpreting
dilution or float itself, if it starts placing real trades without a separate decision to allow
that, or if a strategy gets called validated without someone other than its author rerunning the
test.
