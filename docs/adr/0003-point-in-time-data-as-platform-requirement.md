# ADR 0003: Point-in-time data as platform requirement

## Status

Accepted

## Context

Backtests and live decisions become invalid when revised or late data is treated as though it was known earlier.

## Decision

`quant-data` must preserve source, observation, and availability timing with revision lineage. Research and execution must select data available at the decision time and document their timing assumptions. Missing or unknown values remain distinct from valid zero.

## Consequences

Data contracts and tests must cover late arrivals, revisions, and replay. Some apparently complete historical datasets cannot support defensible point-in-time evaluation.

## Alternatives considered

Latest-value snapshots are simpler but allow look-ahead bias.
