# ADR 0005: Paper-first execution and live promotion

## Status

Accepted

## Context

Live orders carry financial risk and cannot be inferred from a passing backtest or a successful paper run.

## Decision

Paper mode is the default. Live trading requires explicit human promotion and separate configuration controls in `quant-execution`; no implicit environment fallback may activate it. Risk enforcement and audit remain active in both modes.

## Consequences

Promotion must be reviewable and reversible, with distinct credentials and evidence. Operational details and exact gates belong in the execution repository before live use.

## Alternatives considered

Automatic live activation after tests is rejected because tests alone do not grant trading authority.
