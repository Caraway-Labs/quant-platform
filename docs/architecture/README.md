# Architecture

The [repository catalog](../../repositories.yaml) is the ownership inventory. External data enters through `quant-data`; versioned, point-in-time data is published to Snowflake. `quant-research` consumes those contracts and produces approved strategy/model outputs. `quant-execution` consumes approved outputs, applies deterministic risk controls, interacts with Alpaca, and records orders, fills, positions, and audit evidence. See the [ADRs](../adr/README.md) for binding decisions.

Cross-repository interfaces need named owners, schema/version, timing semantics, lineage, access policy, and compatibility rules. A child repository documents its concrete tables, jobs, and runtime procedures.
