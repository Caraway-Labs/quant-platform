# ADR 0006: Model and strategy lifecycle with risk authority

## Status

Accepted

## Context

Research performance and approval are separate from permission to place an order under current portfolio and market conditions.

## Decision

`quant-research` records experiment identity, evaluation, and explicit approval state for strategy/model outputs. `quant-execution` consumes only approved, versioned outputs and applies deterministic portfolio and risk policy with final veto authority. Approval never overrides a risk rejection.

## Consequences

Outputs need traceable versions and approval status. Execution retains the consumed version, risk decision, and order/fill audit. Thresholds and implementation details remain in the owning repositories and require separate review.

## Alternatives considered

Letting an approved model submit orders directly is rejected because approval cannot account for every real-time risk condition.
