# quant-platform

This repository owns the architecture, governance, standards, ADRs, shared prompts, and agent guidance for the quantitative trading platform. It coordinates independent repositories; it does not contain their runtime implementations. Use [repositories.yaml](repositories.yaml) as the machine-readable ownership map.

## Repository map

| Repository | Owns | Must not do |
| --- | --- | --- |
| [quant-data](https://github.com/Caraway-Labs/quant-data) | Point-in-time ingestion, quality, lineage, and Snowflake publication | Make trading decisions or hold broker credentials |
| [quant-research](https://github.com/Caraway-Labs/quant-research) | Features, backtests, models, evaluation, and approval | Hold live broker credentials or submit live orders |
| [quant-execution](https://github.com/Caraway-Labs/quant-execution) | Portfolio construction, risk veto, Alpaca integration, reconciliation, and audit | Bypass deterministic risk controls |
| quant-platform | Cross-repository rules and contracts | Implement child repository runtime code |

Each repository has its own Git history, issues, tests, and release process. A typical local checkout is:

```text
quant-platform/
  quant-data/          # independent clone, ignored by parent Git
  quant-research/      # independent clone, ignored by parent Git
  quant-execution/     # independent clone, ignored by parent Git
  repositories.yaml
  AGENTS.md
```

## Architecture

```text
External data → quant-data → Snowflake → quant-research
                                      ↓ approved strategy/model outputs
                              quant-execution → Alpaca
                                      ↓ orders, fills, positions
                                Snowflake audit trail
```

Snowflake is the preferred cross-repository system of record and contract layer where practical. Producers publish versioned, lineage-aware contracts; consumers validate the contract they read. Research outputs require approval before execution consumes them. Execution applies deterministic risk controls that can veto every strategy or model output. Paper trading is the default; live trading needs explicit promotion and configuration.

## Working here

Read the governing issue, identify the owning repository and dependencies, then create a branch in that repository. Implement only the issue scope, run relevant checks, review the diff against acceptance criteria, and open a PR with evidence and unresolved risks. Start with [AGENTS.md](AGENTS.md) for agent rules and [Git workflow](standards/git-workflow.md) for the common process. Do not edit a nested clone while carrying out a parent governance issue.

For a new repository, update [repositories.yaml](repositories.yaml), this map, [architecture docs](docs/architecture/README.md), and routing guidance in [AGENTS.md](AGENTS.md); record a new [ADR](docs/adr/README.md) when the boundary changes.

## Guidance

Start with the [system architecture](docs/architecture/system-architecture.md), [Snowflake contracts](docs/architecture/snowflake-contracts.md), [security boundaries](docs/architecture/security-boundaries.md), [strategy lifecycle](docs/architecture/strategy-lifecycle.md), and [observability and audit standard](docs/operations/observability-and-audit.md) for EPIC #2 implementation boundaries.

- [Documentation](docs/README.md), [architecture](docs/architecture/README.md), [development](docs/development/README.md), [operations](docs/operations/README.md)
- [Engineering standards](standards/README.md)
- [Shared agent skills](skills/README.md) and [reusable prompts](prompts/README.md)
- [Architecture decisions](docs/adr/README.md)
