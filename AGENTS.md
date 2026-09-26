# Agent instructions for quant-platform

Read the governing issue and [repository catalog](repositories.yaml) before editing. These rules apply to work in this parent repository; child repositories are independent checkouts with their own instructions.

## Route the work

- Ingestion, data quality, lineage, and point-in-time correctness: `quant-data`.
- Strategies, features, backtests, models, experiments, and model approval: `quant-research`.
- Broker connectivity, portfolio construction, risk, orders, fills, and reconciliation: `quant-execution`.
- Cross-repository governance, architecture, contract policy, standards, ADRs, and shared agent guidance: `quant-platform`.

Switch into the owning repository before repository-specific work. Never edit child clones merely because they are nested here. For cross-repository changes, define the contract and coordinate separately scoped issues/PRs. Prefer a documented Snowflake contract where practical over hidden runtime coupling.

## Spec-driven SDLC

1. Inspect Git status, branch, tracked files, and existing user changes. Preserve unrelated work.
2. Read the governing issue and relevant [docs](docs/README.md), [ADRs](docs/adr/README.md), [standards](standards/README.md), and [skills](skills/README.md). Identify acceptance criteria, dependencies, and the correct repository.
3. Plan a bounded implementation. Create a feature branch; **never work directly on `main`**.
4. Implement only the authorized scope. Run relevant tests and checks.
5. Review the complete diff against acceptance criteria, security boundaries, and accidental files. Update documentation where behavior or contracts change.
6. Open a PR describing scope, test evidence, migration or contract impact, and unresolved risks. Do not merge or promote without the required human review.

## Security and execution boundaries

- Never commit secrets. Keep live Alpaca credentials only in the controlled execution environment, never in this repository, `quant-data`, or `quant-research`.
- `quant-data` never makes trading decisions. Research code cannot bypass execution controls or submit live trades.
- Deterministic execution risk checks must be able to veto any strategy or model output.
- Paper trading is the default. Never enable live trading implicitly; require explicit promotion and configuration controls.

## Definition of done

Acceptance criteria are satisfied; relevant checks pass; docs and contracts are updated when applicable; architecture and security boundaries hold; the diff is reviewed; no child repository content, local artifacts, or secrets are accidentally included; and remaining risks are reported.
