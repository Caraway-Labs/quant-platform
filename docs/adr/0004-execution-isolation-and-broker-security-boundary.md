# ADR 0004: Execution isolation and broker security boundary

## Status

Accepted

## Context

Research outputs are inputs to trading, but research and data workflows should not gain broker authority.

## Decision

Only `quant-execution` may hold broker credentials or submit orders. Live credentials remain in its controlled environment, never Git. `quant-data` makes no trading decisions; `quant-research` has no live broker authority. Execution validates approved inputs and applies deterministic risk controls before broker submission.

## Consequences

The execution boundary can reject any research output. Credential management, order audit, and broker failure handling are owned by `quant-execution`.

## Alternatives considered

Direct research-to-broker integration would shorten the path but remove independent risk veto and credential isolation.
