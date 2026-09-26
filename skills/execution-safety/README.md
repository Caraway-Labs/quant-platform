# Execution safety review

Use for `quant-execution` portfolio, risk, broker, order, and reconciliation work. Confirm paper mode, explicit live promotion, isolated broker credentials, deterministic risk veto before submission, idempotent order handling, and durable order/fill/position audit. Test rejection and failure paths as well as success paths. Implementation belongs in `quant-execution`.
