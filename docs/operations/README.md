# Operations

[Observability, audit and runbook standard](observability-and-audit.md) defines common identifiers, events, escalation and retention expectations.

Required implementation runbooks, owned by the affected child repository, cover stale data (`quant-data` with execution halt), Snowflake outage (all consumers), stale or unapproved predictions (`quant-research` and `quant-execution`), Alpaca outage, uncertain order submission, reconciliation mismatch, kill-switch activation, accidental credential exposure, and rollback from live to paper or disabled (`quant-execution`). Each must use the incident/recovery interface in the standard before operational enablement.

This repository records cross-repository operational policy; runbooks and deployment commands belong to the owning child repository. Treat paper trading as the default. Any live promotion requires explicit human approval, configuration, credential isolation in execution, and a rollback path. Execution must retain risk-veto and reconciliation evidence. An incident involving data correctness or trading authority should identify the owning repository, affected contract versions, time window, and audit records before remediation. See [security](../../standards/security.md) and [ADR 0005](../adr/0005-paper-first-execution-and-live-promotion.md).
