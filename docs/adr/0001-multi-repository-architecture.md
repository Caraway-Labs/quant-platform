# ADR 0001: Multi-repository architecture

## Status

Accepted

## Context

Data, research, and execution have different release cycles and security privileges. A single codebase would blur ownership and broker-access boundaries.

## Decision

Keep `quant-platform`, `quant-data`, `quant-research`, and `quant-execution` independently versioned. The parent owns shared governance and repository routing; each child owns its runtime and local tests. The [catalog](../../repositories.yaml) records boundaries.

## Consequences

Cross-repository work needs explicit contracts and coordinated PRs. The parent ignores local child clones and never vendors them as content.

## Alternatives considered

A monorepo simplifies atomic changes but weakens independent privilege and release boundaries.
