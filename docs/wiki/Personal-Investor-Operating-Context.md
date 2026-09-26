# Personal Investor Operating Context

## Who this serves

This platform is for a **personal investor**, not a fund or SaaS product. The operator deposits a couple thousand dollars into an IRA and plans to contribute about **$200 per month**. Roles (researcher, builder, operator) are the same person wearing different hats.

There is no customer-facing dashboard in initial scope. Working surfaces are Python packages, thin notebooks, scheduled jobs, evaluation artifacts, and audit records.

## Product purpose

Replace one-off experiments with a repeatable path:

1. Is there a defensible hypothesis?  
2. Does evidence survive realistic testing (point-in-time data, costs, executable timing)?  
3. Can we operate it safely (research separated from broker authority)?  
4. Can we explain every decision (data → model → approval → risk → order → fill)?

The product is **not** “AI predicts the market.” It is an evidence machine that makes bad ideas inexpensive to reject.

## Scope (v1)

| In scope | Out of initial scope |
| --- | --- |
| U.S. equities and ETFs | Short selling as a launch requirement |
| Daily / weekly decisions | Intraday / HFT |
| Long-only portfolios | Leverage and naked short-volatility |
| Paper trading before any live money | LLM choosing trades without risk gates |
| Structured numerical data first | Full options book as a day-one requirement |

## Capital constraints that change strategy choice

These are soft operating facts for research design—not approved risk limits:

| Constraint | Implication |
| --- | --- |
| ~$2,000 starting IRA capital | A 20-name equal-weight book is ~$100/name; diversification and round-lot friction matter |
| ~$200/month contributions | Dollar-cost averaging and cash drag are first-class; turnover burns compounding |
| IRA wrapper | Prefer liquid ETFs/stocks; avoid strategies that need margin, short borrow, or exotic instruments |
| Retail brokerage (Alpaca planned) | Spreads, partial fills, and free-data limitations (for example IEX-only free tiers) must be modeled honestly |
| Solo operator time | Complexity tax is real; prefer strategies that teach the pipeline |

**Rule of thumb:** if a strategy needs dozens of names, daily turnover, borrow, or options chains to “work” in a backtest, it is probably the wrong first live candidate for this account—even if it is a fine research story later.

## Architecture in one diagram

```text
External financial-data providers
        |
        v
quant-data: acquire, normalize, validate, preserve revisions
        |
        v
Snowflake RAW / CORE
        |
        v
quant-research: features, hypotheses, training, walk-forward evaluation
        |
        v
Snowflake FEATURES / ML (versioned models, approvals, expiring predictions)
        |
        v
quant-execution: eligibility → target portfolio → deterministic risk → durable order intent
        |
        v
Alpaca (paper first) → fills/positions → reconciliation → Snowflake EXECUTION audit
```

Ownership note: published `FEATURES` belong to **`quant-research`**, not `quant-data`. Canonical source facts belong to `quant-data`.

## Technology choices (status)

| Element | Role | Status |
| --- | --- | --- |
| Python | Pipelines, research, models, execution jobs | Specified; implementations ahead |
| Snowflake | System of record / contracts / audit | Logical contracts documented |
| Alpaca | Broker adapter + planned market observations | Named in backlog |
| DigitalOcean + containers | Scheduled execution | Planned in execution epic |
| GitHub + coding agents | Specs, issues, reviews, evidence | Governance committed in `quant-platform` |

## Strategy lifecycle (documented)

`EXPERIMENTAL → CANDIDATE → APPROVED → PAPER → LIVE`

Protective suspension: `DISABLED`. Planned retirement: `DEPRECATED`.

No forward gate is skipped. A prediction is not an order; a good backtest is not approval; approval is not permission to ignore risk controls. These are **requirements**, not yet proven implementations.

## What success means now

A reproducible **keep / reject / inconclusive** decision for a strategy, with:

- a simpler baseline  
- historically available inputs only  
- realistic transaction-cost and timing assumptions  
- an evaluation report someone else could re-run  

A well-supported rejection is a successful platform outcome.

## Delivery sequence (dependency order)

1. `quant-data` scaffolding + point-in-time market-data foundation  
2. `quant-research` scaffolding + evaluation framework  
3. Core strategy experiments with explicit keep/reject  
4. `quant-execution` scaffolding + paper path + deployment controls (live separately gated)

`quant-platform` EPIC #1 and #2 (governance + architecture) are already completed and merged.
