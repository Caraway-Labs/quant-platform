# Security

Do not commit credentials, tokens, private keys, or local environment files. Live broker credentials belong only in the controlled `quant-execution` environment. Data and research have no live trade authority. Execution must enforce deterministic risk veto before submission, default to paper mode, require explicit live promotion, and retain audit and reconciliation records. Review access to Snowflake contracts by least privilege.
