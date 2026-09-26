# Quant Trading Platform Wiki

**Audience:** personal operator (Caraway Labs)  
**Capital context:** ~$2,000 initial IRA funding, ~$200/month contributions, long-only U.S. equities/ETFs, paper-first  
**Status:** governance and architecture are documented in `quant-platform`; data, research, and execution runtimes are still backlog

This wiki turns the product brief, deep-research report, and GitHub backlog into operator-facing guidance. Architecture rules of record remain in `docs/architecture/` inside this repository.

## Start here

| Page | Purpose |
| --- | --- |
| [Personal Investor Operating Context](Personal-Investor-Operating-Context) | What we are building, for whom, and what “success” means at this capital size |
| [Repository & Backlog Map](Repository-and-Backlog-Map) | Four-repo ownership and the open epic/story backlog |
| [Strategy Red Team](Strategy-Red-Team) | Honest pressure test of the ten planned research candidates |
| [AI/ML Strategy Proposals](AI-ML-Strategy-Proposals) | Up to five additional AI/ML strategies and how they compare |

## One-sentence architecture

Research-first platform: `quant-data` publishes point-in-time facts to Snowflake → `quant-research` produces versioned features, models, and approvals → `quant-execution` applies deterministic risk controls and talks to Alpaca (paper default) → every decision is reconstructable in audit records.

## Immediate objective

Credible data and reproducible research—not automated live trading. The first meaningful milestone is a keep/reject decision for a strategy with historically valid inputs, realistic costs, and a simple baseline comparison.

## Capital-aware default path

At ~$2k + $200/month, prefer **few liquid ETFs, low turnover, monthly/weekly decisions**. High-turnover single-stock strategies and options are research curiosities until capital and cost assumptions say otherwise.

Recommended first experiments (aligned with deep research and this capital size):

1. Simple ETF trend / volatility-scaled allocation  
2. Simple long-only momentum or quality/value baseline on a liquid universe  
3. Only then challenge those baselines with ML (for example LightGBM ranking)

## Source materials (2026-09)

- Product brief / architectural statement (uploaded planning doc)  
- ChatGPT deep-research report on planned strategies  
- GitHub issues across `quant-platform`, `quant-data`, `quant-research`, `quant-execution`  
- Verified architecture baseline commit `f821529` on `quant-platform/main`
