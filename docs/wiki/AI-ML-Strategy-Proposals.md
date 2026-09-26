# AI/ML Strategy Proposals

**Purpose:** Propose up to five *additional* AI/ML-oriented strategies beyond the ten backlog candidates, with required systems and an objective comparison to the current plan.  
**Constraint lens:** personal IRA (~$2k + ~$200/month), long-only, daily/weekly preferred, paper-first.

These are research hypotheses, not endorsements. Each should still face a simple non-ML baseline and a pre-registered keep/reject rule.

## Comparison rubric

| Criterion | Question |
| --- | --- |
| Capital fit | Can a ~$2k IRA express the book with liquid ETFs/few names and low turnover? |
| Data readiness | Does it mostly need prices already in EPIC data #2, or heavy new pipelines? |
| Overfit risk | How easy is it to p-hack? |
| Incremental value vs plan | Does it teach something the current ten do not? |
| Ops burden | Solo-operator complexity for paper/live |

Score legend: **H** high / good, **M** medium, **L** low / poor fit.

## Proposal A — Meta-labeled ETF trend filter

### Strategy

Start from a transparent trend rule on a small ETF set (for example SPY, QQQ, IWM, TLT, a commodities proxy). The baseline decides direction/size with moving-average or time-series momentum and volatility targeting. An ML **meta-labeler** (binary classifier) predicts whether the next trend trade will be profitable *after costs*, using features available at decision time (trend strength, realized vol, cross-asset correlation, distance from high-water mark, calendar/macro surprise flags when ready). If the meta-label is negative, hold cash or reduce size.

This is Lopez-de-Prado-style meta-labeling applied to the platform’s best capital-fit sleeve—not a replacement for the baseline.

### AI/ML systems required

- Feature store for ETF panels (returns, vol, correlations) with PIT stamps  
- Walk-forward training with purging/embargo around label horizons  
- Probabilistic classifier (logistic / gradient boosted trees) with calibration  
- Position sizer that consumes `p_success × baseline_size` under hard risk caps  
- Experiment registry linking baseline version + meta-model version + cost assumptions

### Assessment vs current plan

| Vs | Assessment |
| --- | --- |
| #15 ETF trend | Direct upgrade path: keeps #15 baseline, adds ML only where it must beat a dumb filter |
| #1 LightGBM stock rank | Lower capital stress, fewer names, clearer baseline; still ML discipline |
| #3/#4 short-horizon stock filters | Same meta-label idea, but on friendlier horizon and instruments |
| Capital fit | **H** |
| Data readiness | **H** (prices first) |
| Overfit risk | **M** (must freeze baseline before meta-model search) |
| Incremental value | **H** — teaches the right ML pattern for this shop |

**Priority suggestion:** After simple #15 works end-to-end.

## Proposal B — Liquid ETF cross-section with shrinkage / Bayesian ranking

### Strategy

Instead of ranking hundreds of stocks, rank a **liquid ETF universe** (sector, factor, country, duration). Predict next-period relative returns with a regularized model (elastic net, Bayesian hierarchical shrinkage, or small MLP with heavy regularization). Hold top-K ETFs with volatility parity and a maximum single-name weight. Rebalance monthly.

This preserves the cross-sectional ML ambition of #14 while matching IRA capacity.

### AI/ML systems required

- ETF universe master with expense ratios and liquidity screens  
- Hierarchical prior or shrinkage toward sector/factor means  
- Turnover-penalized objective (not raw MSE)  
- Risk model: simple factor betas (market, rates, USD) for exposure reports  
- Capacity simulation at $2k / $5k / $10k notionals

### Assessment vs current plan

| Vs | Assessment |
| --- | --- |
| #1 stock LightGBM | Same family, better capital fit; should **precede** single-stock LightGBM |
| #8 macro sector rotation | Complementary: this is price/factor cross-section; #8 adds macro later |
| #7 quality/value | Can include quality/value *factor ETFs* as features or competitors |
| Capital fit | **H** |
| Data readiness | **H–M** |
| Overfit risk | **M** (small cross-section → fewer bets, easier to overfit narratives) |
| Incremental value | **H** — missing middle ground between stock ML and simple trend |

**Priority suggestion:** Core EPIC candidate; consider inserting before #14.

## Proposal C — Realized-vol / distribution forecast for vol-targeted allocation

### Strategy

Do not predict returns first. Predict **volatility and tail risk** for a small ETF set; set exposures so portfolio ex-ante vol hits a target (for example 8–10% annualized), with a hard drawdown brake. Models: HAR-RV, GARCH family, and a gradient-boosted model on realized measures / options-implied proxies only when available. Optional second stage: predict probability of a large downside week to cut risk.

This is ML for **risk**, which matches a personal IRA utility function better than alpha theater.

### AI/ML systems required

- High-quality daily bars; later optional intraday RV  
- Vol model zoo with proper scoring (QLIKE, interval coverage)—not accuracy  
- Portfolio vol attribution and kill-switch hooks in execution  
- Stress library (rate shock, equity −20%) separate from the ML model

### Assessment vs current plan

| Vs | Assessment |
| --- | --- |
| #15 regime/trend | Orthogonal and composable: trend picks sleeve, vol model sizes it |
| #10 long-vol options | Captures “care about volatility” without options until ready |
| #1 return-rank ML | Different target; usually more stable to estimate than expected return |
| Capital fit | **H** |
| Data readiness | **H** |
| Overfit risk | **L–M** if scored properly |
| Incremental value | **H** — fills a hole: planned strategies under-emphasize risk ML |

**Priority suggestion:** Parallel to #15 baseline; high personal-investor value.

## Proposal D — Event meta-model on earnings + NLP sentiment (PEAD upgrade)

### Strategy

Once earnings PIT data exists, build PEAD baseline (#6). Add an ML layer that combines SUE, first-day reaction, liquidity, and **transcript/8-K embeddings** (or cheaper headline sentiment) to estimate drift continuation probability. Trade only high-confidence long-only events in liquid names; hard cap on names and gross exposure.

### AI/ML systems required

- Earnings calendar, actuals, historically valid estimates  
- Text pipeline: filings/transcripts → embeddings → frozen encoder + small head  
- Leakage controls: text available timestamp must precede decision  
- Event study backtester (not only panel CV)  
- Name liquidity filters suitable for IRA notionals

### Assessment vs current plan

| Vs | Assessment |
| --- | --- |
| #6 PEAD | Strict superset; do not build NLP before numeric SUE works |
| #1 generic ranker | More causal/event structure; fewer daily bets |
| #19 backlog story | Aligns; this is how to make #19 modern without skipping gates |
| Capital fit | **M** (events cluster; concentration risk) |
| Data readiness | **L** today / **H** after data EPIC #3 |
| Overfit risk | **H** if text features are free-form without freezing |
| Incremental value | **M–H** — only after fundamentals exist |

**Priority suggestion:** Phase 2; numeric PEAD first.

## Proposal E — Representation learning for regime embedding (not trade picker)

### Strategy

Train an unsupervised or self-supervised model (autoencoder, contrastive TS model, or simple HMM/HSMM) on cross-asset returns to produce a **regime embedding**. Downstream policies stay simple: map embedding clusters to (risk-on ETF mix, defensive mix, cash). The ML system is a state estimator; the trading rule remains deterministic and reviewable.

### AI/ML systems required

- Multi-asset daily return panel (equity, rates, credit proxy, USD, vol proxy)  
- Unsupervised model with stability tests (cluster persistence, seed sensitivity)  
- Mapping table from state → target portfolio (human-approved)  
- Monitoring for embedding drift / novel states → force cash

### Assessment vs current plan

| Vs | Assessment |
| --- | --- |
| #15 regime-aware | Replaces fragile hand labels with learned state; keeps human portfolio map |
| #8 macro rotation | Can later concatenate macro vintages into the same embedding |
| Black-box return models | Safer ops story: ML does not pick tickers directly |
| Capital fit | **H** |
| Data readiness | **M** |
| Overfit risk | **M** (unsupervised can still be tortured into a backtest) |
| Incremental value | **H** — governance-friendly ML pattern |

**Priority suggestion:** After simple trend; strong cultural fit with approval/risk separation.

## Scoreboard vs the planned ten

| Idea | Capital | Data now | Overfit | vs best planned peers | Suggested order |
| --- | --- | --- | --- | --- | --- |
| A Meta-labeled ETF trend | H | H | M | Beats jumping to #1; upgrades #15 | 2 |
| B ETF cross-section shrinkage | H | H | M | Better #14 for this IRA | 3 |
| C Vol-forecast targeting | H | H | L–M | Complements #15; safer than #10 | 1–2 |
| D NLP/event PEAD | M | L | H | Extends #6/#19 | 5 |
| E Regime embedding | H | M | M | Disciplined version of #15/#8 regime | 4 |
| Planned #15 simple trend | H | H | L | Still the right **first** non-ML control | 0 (first) |
| Planned #7 quality/value | M–H | L | L–M | Still best early *fundamental* baseline | 1 |
| Planned #1 stock LightGBM | L–M | M | H | Demote behind A/B | later |
| Planned #3/#4/#9/#10 | L | varies | H | Remain poor early fits | park |

## Objective bottom line

1. **Do not add five more return-predicting stock models.** Add ML where this account lives: **ETF allocation, risk forecasting, and meta-labeling**.  
2. The highest-leverage new ideas are **C (vol targeting)** and **A (meta-labeled trend)**, then **B (ETF cross-section)** as the honest replacement for early single-stock LightGBM.  
3. **D** and **E** are valuable later: D needs data EPIC #3; E needs a working simple regime baseline so the embedding has something to beat.  
4. Every proposal still loses to a failed **costed baseline**. If simple trend and simple factor books cannot be researched cleanly, no AI/ML proposal here deserves implementation time.

## Suggested experiment tickets (for later issue filing)

1. Baseline ETF trend + vol target (non-ML) — keep/reject  
2. Vol model bake-off (HAR/GARCH vs boosted) — proper scoring only  
3. Meta-labeler on frozen trend baseline — must lift after-cost utility  
4. Liquid ETF monthly ranker with shrinkage — compare to equal-weight sectors  
5. Only then revisit stock-level LightGBM (#14) with the same evaluation contract
