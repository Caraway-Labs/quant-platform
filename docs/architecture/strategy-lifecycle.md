# Strategy lifecycle and promotion gates

`quant-research` owns versioned research state and review evidence in ML. `quant-execution` owns mode enablement, risk policy and final veto. State applies to a specific strategy and model version; changing either requires renewed evidence. State changes are append-only approval events with actor, time, evidence and prior state.

| Transition | Required evidence and authority |
| --- | --- |
| EXPERIMENTAL → CANDIDATE | Documented hypothesis, data dependencies and baseline; leakage-safe target and PIT data; reproducible backtest configuration and reasonable transaction-cost assumptions. Research reviewer records candidate decision. |
| CANDIDATE → APPROVED | Chronological/walk-forward evaluation; no unresolved PIT/leakage defect; realistic cost/slippage; robustness/sensitivity and drawdown/risk analysis; known failure modes; reproducible data/model/config versions; explicit human research review. Metrics alone never approve. |
| APPROVED → PAPER | Executable versioned prediction contract with freshness/expiry; execution compatibility and configured risk policy; successful integration checks; paper-only broker account/credentials. Execution operator explicitly enables paper after reviewing evidence. |
| PAPER → LIVE | Sufficient documented paper operational history for the proposed strategy, successful reconciliation, no unresolved critical execution defects, validated kill switch, working alerts/runbooks, explicit human approval, separate live configuration and credentials, and rehearsed rollback to paper/disabled. No arbitrary profitability threshold or automatic metric-based promotion. |
| Any active state → DISABLED | Immediate protective stop for stale model/data, failed quality, incident, risk breach, unexplained divergence or reconciliation failure. Preserve current evidence; operator investigates and authorizes resumption through the applicable gate. |
| APPROVED/PAPER/LIVE → DEPRECATED | Reviewed planned retirement; disable new signals/orders, resolve open orders and retain lineage. A replacement is separately approved. |

No skipped forward transitions. `DISABLED` is a reversible safety state; `DEPRECATED` is retired and requires a new version and full gates to return. A failed gate leaves the prior state unchanged. For live rollback, halt new submissions, account for open orders and fills, reconcile, move to DISABLED or explicitly configured PAPER, and record human action. Approval and PAPER/LIVE state never override an execution risk veto, freshness failure, kill switch or broker uncertainty. See [security](security-boundaries.md) and [operations](../operations/observability-and-audit.md).
