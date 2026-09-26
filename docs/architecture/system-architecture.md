# System architecture and data flow

This is the logical design for [EPIC #2](https://github.com/Caraway-Labs/quant-platform/issues/2). It specifies boundaries, not deployed services. Each child repository owns its implementation and release.

```text
Market data providers; later SEC, macro, alternative sources
  -> quant-data: acquire, normalize, validate, retain revisions
  -> Snowflake RAW -> CORE -> FEATURES
  -> quant-research: PIT research, backtests, training, approval, scheduled signals
  -> Snowflake ML: immutable version/approval/prediction evidence
  -> quant-execution: eligibility -> portfolio construction -> deterministic risk veto
  -> Alpaca (paper by default): orders -> fills, positions, account state
  -> quant-execution: reconcile -> Snowflake EXECUTION audit
```

## Components and trust boundaries

| Component | Responsibility and boundary |
| --- | --- |
| External market data, later SEC and macro sources | Untrusted input; availability, revisions, identity and units need validation. Later sources require their own contracts before use. |
| `quant-data` | Sole owner of ingestion, canonical validation, source lineage and RAW/CORE publication. It never chooses trades. |
| Snowflake | Preferred versioned cross-repository system of record. RAW preserves source evidence; CORE contains validated PIT facts; FEATURES contains published research inputs; ML holds research output and approval; EXECUTION holds execution evidence. See [contracts](snowflake-contracts.md). |
| `quant-research` | Research-only boundary: PIT queries, features, experiments, training, evaluation and approval. It publishes versioned signals, never submits orders. |
| `quant-execution` | Sole broker boundary: independently checks approved inputs, freshness, portfolio and deterministic risk; may reject any signal. Owns order/fill reconciliation and audit. |
| Alpaca | External broker. Its acknowledgements and state are independently reconciled; a request timeout is not proof of rejection. |
| Scheduler/deployment environment | Triggers bounded backfills, incremental jobs, research, signal and rebalance runs. Each run has an id, configured mode and credentials; no scheduler grants live authority. Deployment choice is implementation-specific. |
| Observability/alerting | Consumes structured events, metrics and audit references per [operational standard](../operations/observability-and-audit.md); does not authorize trades. |

Broker credentials exist only in the controlled `quant-execution` runtime. Eventual live-money authority requires human promotion and separate live configuration and credentials. The ML contract boundary transfers approved *inputs*, not order authority.

## Scheduled and batch patterns

1. `quant-data` backfills historical source snapshots with source timestamps, original availability and revision lineage; incremental jobs publish only validated updates. A backfill never retroactively changes what was known at an earlier decision time.
2. Scheduled feature publication selects eligible CORE revisions by availability cutoff and records input manifests, code version and run id. `quant-research` trains and backtests on the same PIT rule, records experiment/model versions and review evidence, and publishes approval separately from metrics.
3. Scheduled signal generation pins approved strategy/model and data versions, a decision timestamp and prediction expiry. Execution reads only compatible approved ML contracts, applies portfolio and risk policy, records intent and risk decision, then sends to Alpaca only if permitted. Scheduled rebalancing follows the same path; a schedule alone is never authorization.
4. Broker events update order and fill evidence. Execution reconciles broker orders, positions and account state with internal state before the next unsafe action. End-of-day audit records run outcomes, data/model versions, risk decisions, order lifecycle, fills, snapshots and reconciliation results in EXECUTION.

## Failure boundary

| Condition | Required response |
| --- | --- |
| Stale, failed-quality or unavailable source data | Do not publish eligible features/signals from it; execution rejects affected signals pending fresh validated evidence. |
| Snowflake unavailable or contract incompatible | Stop dependent publication/consumption and new order creation; preserve durable local/operational incident evidence, then reconcile before resuming. No guessed latest values. |
| Stale prediction or unapproved model/version | Reject signal and record reason; do not substitute another model silently. |
| Portfolio/risk validation fails | Deterministic veto; preserve risk decision and never submit the intent. |
| Broker API fails | Stop retries that could duplicate orders; record failure and use broker state to resolve. |
| Submission outcome uncertain | Halt affected order flow, query broker by durable client order id, reconcile before retry; never assume failed submission. |
| Broker and internal positions diverge | Halt affected execution, preserve both snapshots, investigate and reconcile before resuming. |

## Extension points

Intraday strategies, options, alternative data/news, additional brokers, model-serving patterns and advanced orchestration may be proposed later. Each needs explicit data time semantics, permissions, risk controls, contracts and audit behavior. None is required by this architecture.
