# Security and environment boundaries

Only `quant-execution` may possess broker credentials or submit orders. Credentials are never stored in Git, documentation, datasets, logs, artifacts or research environments. [ADR 0004](../adr/0004-execution-isolation-and-broker-security-boundary.md) and [ADR 0005](../adr/0005-paper-first-execution-and-live-promotion.md) govern this boundary.

| Environment | Permitted identity and capability |
| --- | --- |
| Local development | Synthetic/read-only fixtures by default; developer-scoped provider/Snowflake access via environment variables or local secret manager when needed. No live broker credential. |
| CI | Ephemeral, least-privilege test identity from CI secret store; no live broker credential or live order authority. Tests use fakes unless an explicitly scoped integration job needs a paper endpoint. |
| Research/development Snowflake | Separate non-production roles and data. `quant-data` writes RAW/CORE; `quant-research` reads permitted CORE, writes FEATURES/ML; `quant-execution` reads approved ML and permitted CORE/FEATURES, writes EXECUTION in its environment. |
| Paper execution | Controlled `quant-execution` runtime with paper-only Alpaca credential, approved read grants and EXECUTION write grant. Paper account and endpoint are explicit. |
| Eventual live execution | Separate controlled execution identity, live credential/endpoint and account, human-approved strategy and explicit live enablement. No reuse or automatic elevation of paper identity. |

`quant-platform` has governance-only access and no broker or runtime write identity. `quant-data` may read provider credentials and write only its Snowflake domains; it has no broker secret or order permission. `quant-research` reads appropriate CORE/FEATURES, writes its FEATURES/ML contracts, and has no broker credential or trade submission. `quant-execution` reads approved ML and necessary market data, writes EXECUTION, and is the sole broker client. Give each service a separate Snowflake role/identity; grant only named schema/table operations, avoid broad ownership or cross-domain writes, and separate development, paper and live accounts/roles. Human audit access is read-only and scoped.

## Minimum configuration and secret lifecycle

Execution configuration must specify `mode=paper|live`, broker endpoint/account identity, secret reference, Snowflake role, risk-policy version, kill-switch state and approved strategy/version allowlist. Missing, mismatched or invalid production configuration halts startup or new submissions. Default mode is paper. Never infer live mode from environment name, credential shape or endpoint; never fall back from paper to live. Live requires all of: reviewed human promotion record, independently enabled live setting in the controlled runtime, distinct live secret/endpoint/account, and passing startup policy checks. A live approval record alone does not activate live trading.

Local secrets may enter through environment variables or a local secret manager and must stay outside tracked files. CI uses its secret store; DigitalOcean or other runtime deployment uses its runtime secret store with scoped injection. Never print secret values. Rotate credentials on a documented schedule and after personnel/role changes, suspected exposure or provider revocation; validate replacement in the same environment, then revoke old credentials and audit the change. On accidental exposure: halt affected execution, revoke/rotate the credential, remove it from active systems, preserve incident evidence without the value, assess broker/Snowflake activity, and require explicit clearance before restart. Git history cleanup alone is insufficient.
