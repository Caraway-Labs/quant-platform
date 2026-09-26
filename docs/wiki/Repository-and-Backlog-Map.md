# Repository and Backlog Map

Snapshot of GitHub issues reviewed for this wiki pass. Issue state changes over time; treat this as orientation, not a live dashboard.

## Repository ownership

| Repository | Owns | Must not |
| --- | --- | --- |
| [quant-platform](https://github.com/Caraway-Labs/quant-platform) | Architecture, governance, ADRs, shared agent guidance, cross-repo contracts | Child runtime code or broker authority |
| [quant-data](https://github.com/Caraway-Labs/quant-data) | Ingestion, identity, PIT validation, lineage, RAW/CORE publication | Trading decisions or broker credentials |
| [quant-research](https://github.com/Caraway-Labs/quant-research) | Features, experiments, models, evaluation, approvals, ML outputs | Broker credentials or order submission |
| [quant-execution](https://github.com/Caraway-Labs/quant-execution) | Targets, risk veto, Alpaca, order lifecycle, reconciliation, EXECUTION audit | Override risk controls or silently go live |

## quant-platform — completed foundation

All closed (EPIC #1 scaffolding; EPIC #2 architecture):

- #1 Platform repository scaffolding and agentic SDLC governance  
- #2 Architecture, contracts, and governance  
- Stories #3–#12: README/map, AGENTS.md, skills/docs skeleton, ADRs, architecture, Snowflake contracts, security, lifecycle, observability/audit  

## quant-data — open (16)

| # | Title | Role |
| --- | --- | --- |
| 1 | Quant Data Repository Scaffolding | EPIC |
| 2 | Point-in-Time Market Data Foundation | EPIC |
| 3 | Fundamental, Earnings & Macro Data Expansion | EPIC |
| 4–7 | Package/CI, README/AGENTS, skills/docs, adapter + Snowflake interfaces | Stories (EPIC 1) |
| 8–12 | Security master/universe, Alpaca daily bars, corporate actions, market context, PIT/lineage/DQ | Stories (EPIC 2) |
| 13–16 | SEC EDGAR/XBRL, FRED/ALFRED vintages, earnings calendar/actuals, vendor evaluation | Stories (EPIC 3) |

## quant-research — open (23)

| # | Title | Role |
| --- | --- | --- |
| 1 | Research repository scaffolding | EPIC |
| 2 | Reproducible backtesting, evaluation & model lifecycle | EPIC |
| 3 | Core daily/weekly strategy research | EPIC |
| 4 | Advanced strategy & alternative data research | EPIC |
| 5–8 | Package/tests, README/AGENTS, skills, shared interfaces | Stories (EPIC 1) |
| 9–13 | PIT loader, walk-forward engine, costs/portfolio accounting, metrics/robustness, registry/approvals | Stories (EPIC 2) |
| 14 | Cross-sectional LightGBM stock ranking | Core strategy |
| 15 | Regime-aware ETF trend allocation | Core strategy |
| 16 | ML-filtered short-term mean reversion | Core strategy |
| 17 | Volatility-compression breakout | Core strategy |
| 18 | Adaptive pairs trading | Advanced |
| 19 | Post-earnings surprise continuation | Advanced |
| 20 | Fundamental quality/value ranking | Advanced (research report argues this should come earlier) |
| 21 | Macro-conditioned sector rotation | Advanced |
| 22 | Intraday opening-range continuation | Advanced |
| 23 | Selective long-volatility options | Advanced |

## quant-execution — open (18)

| # | Title | Role |
| --- | --- | --- |
| 1 | Execution repository scaffolding | EPIC |
| 2 | Paper trading execution, risk & reconciliation | EPIC |
| 3 | DigitalOcean scheduling, deployment & live-readiness | EPIC |
| 4–7 | Service/Docker/tests, README/AGENTS, safety docs, config/secrets | Stories (EPIC 1) |
| 8–13 | Approved-signal reader, portfolio/risk engine, Alpaca paper adapter, idempotent orders, reconciliation/audit, kill switch | Stories (EPIC 2) |
| 14–18 | Calendar jobs, container publish, DigitalOcean deploy, observability/runbooks, paper→live gate | Stories (EPIC 3) |

## Known documentation mismatch

Research story #9 refers to feature datasets “from quant-data,” while committed contracts assign published `FEATURES` to `quant-research`. Align the story to the contract before implementing the loader.

## Suggested critical path

```text
data #1 → data #2 (PIT prices/identity)
        → research #1 → research #2 (walk-forward + costs)
        → research baselines (momentum / quality / ETF trend)
        → research #14/#15 only after baselines exist
        → execution #1 → #2 (paper) → #3 (deploy); live remains gated
```
