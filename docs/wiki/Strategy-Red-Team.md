# Strategy Red Team

**Purpose:** Honest pressure test of the ten planned research candidates against (a) the deep-research findings, (b) platform constraints (long-only, daily/weekly, paper-first), and (c) personal IRA capital (~$2k + ~$200/month).  
**Tone:** Critical by design. None of these strategies are validated. Keep/reject belongs to future experiments.

## Executive judgment

The architecture and lifecycle thinking are strong. The **near-term strategy slate is ambitious in the wrong order**.

| Judgment | Detail |
| --- | --- |
| Best fit for capital + learning | #2 ETF trend (simple first), then #7 quality/value or plain momentum as stock baselines |
| Most over-indexed early | #1 LightGBM cross-sectional ranking as a first research story |
| Highest cost risk at this capital | #3 mean reversion, #4 breakouts, #9 opening range, #10 options |
| Structurally mismatched to long-only v1 | #5 classic pairs (needs short leg) |
| Worth keeping as later research | #6 PEAD, #8 macro sector rotation—after PIT fundamentals/macro exist |

Deep research recommendation in one line: **prove the research factory with simple factor and ETF-trend baselines before paying the overfitting tax of tree models.**

## Cross-cutting weaknesses (apply to almost everything)

1. **Capital vs book construction.** At ~$2k, a diversified 20–50 stock long-only book is fantasy in practice. Either trade a handful of liquid names/ETFs or accept extreme concentration. Many “institutional” cross-sectional designs silently assume larger capital.  
2. **Costs dominate weak edges.** Spreads, slippage, and turnover destroy short-horizon strategies faster than notebooks admit. Free Alpaca market data being IEX-only / not institutional PIT is a first-class risk to any price-based edge.  
3. **Multiple testing.** Ten candidates plus feature fishing will produce lucky Sharpes. Without pre-registration, deflated Sharpe / haircut rules, and a hard stop on endless tuning, the platform will “discover” noise.  
4. **Implementation gap.** Governance docs exist; runtime data/research/execution still do not. Strategy opinions without a null-test harness (random labels → no alpha) are premature.  
5. **IRA constraints.** Strategies needing short borrow, margin, or options APIs are not just “later”—they may be permanently wrong for this account wrapper.

## Candidate-by-candidate critique

### 1. Cross-sectional LightGBM stock ranking — research #14

**What’s good**

- Clear product story: rank relative attractiveness, compare to a momentum baseline, weekly rebalance.  
- Forces the platform to grow features, walk-forward, costs, and approval artifacts.  
- Nonlinear interactions are a legitimate research question *after* linear/factor baselines.

**What’s weak**

- Deep research: **defer**—trees overfit noisy equity panels easily; published daily/weekly CS alpha after costs is scarce.  
- Needs a high-quality historical universe (delistings, ticker changes). Without that, LightGBM will “learn” survivorship.  
- At $2k, you cannot run the diversified book the model wants; capacity and concentration make backtest→paper translation weak.  
- Opportunity cost: building LightGBM infra before a working PIT loader and costed baseline is the wrong dependency order.

**Verdict:** Keep as a **challenger**, not the first experiment. Gate it behind a momentum/quality baseline that fails to explain the same signal.

### 2. Regime-aware ETF trend allocation — research #15

**What’s good**

- Best **capital fit**: few tickers, liquid ETFs, natural cash sleeve, monthly/weekly horizon.  
- Strong economic intuition (trend + vol scaling). Easy to explain and audit.  
- Deep research: **investigate now** for the *simple* trend variant; regime ML later.  
- Ideal first paper-trading candidate once execution exists.

**What’s weak**

- “Regime” labels are easy to leak (future-informed bull/bear tags). Must infer regimes causally.  
- Trend following can sit in cash for long stretches—psychologically hard, and IRA contributions need an explicit cash policy.  
- Equity-only trend often underperforms buy-and-hold in long bull markets; evaluation must include utility, not only Sharpe vs SPY without matching risk.  
- Adding a fancy regime classifier too early recreates the LightGBM problem on a smaller sample.

**Verdict:** **Promote to first research pair** (simple trend + vol targeting). Treat ML regime as optional challenger.

### 3. ML-filtered short-term mean reversion — research #16

**What’s good**

- Clean experimental shape: rule generates candidates, ML filters. Teaches meta-labeling instincts.  
- Short horizon produces many samples (statistically tempting).

**What’s weak**

- Deep research: **defer / de-prioritize**. Long-only reversal after a crash often means catching knives; gap risk and earnings days dominate.  
- Daily turnover vs $2k: costs and minimum notional kill the edge unless the gross edge is unrealistically large.  
- Classification accuracy ≠ PnL; easy to fool yourself with balanced accuracy on tiny moves.  
- Crowded retail narrative; any remnant edge is fragile.

**Verdict:** Optional **null experiment** only after costed backtester exists. Do not staff as a core path.

### 4. Volatility-compression breakout — research #17

**What’s good**

- Simple baseline (bands/ATR compression) is easy to specify.  
- Can be limited to liquid names or ETFs to reduce microstructure pain.

**What’s weak**

- Deep research: **low priority**—thin systematic evidence in equities; false breakouts and regime dependence.  
- Pattern definition invites curve-fitting (lookback, band width, hold period).  
- Many simultaneous breakouts → either concentration or turnover.  
- ML filter on sparse events overfits quickly.

**Verdict:** Run a **one-weekend baseline** on liquid ETFs; if after-cost edge is absent, close the story. Do not escalate to ML by default.

### 5. Adaptive pairs trading — research #18

**What’s good**

- Rich research literature; Kalman hedge ratios are a good engineering exercise.  
- Forces two-leg execution, borrow, and relationship-break diagnostics—valuable later.

**What’s weak**

- Classic pairs are **long-short**. Long-only “pairs” is a different, usually weaker strategy.  
- Borrow availability/cost for retail/IRA is a hard gate.  
- Cointegration relationships break; monitoring is operationally heavy for a solo operator.

**Verdict:** **Omit for v1.** Reopen only if shorting (or ETF spread substitutes) becomes an explicit scope change.

### 6. Post-earnings surprise continuation (PEAD) — research #19

**What’s good**

- One of the most documented anomalies; clear economic story.  
- Lower turnover than daily reversal if held weeks–months.  
- Excellent stress test of PIT fundamentals, announcement timestamps, and consensus.

**What’s weak**

- Deep research: **later**—data pipeline is the gate, not modeling cleverness.  
- Surprise definition without historically valid consensus is fake PEAD.  
- Event concentration and liquidity around small caps; $2k book may be forced into a few names.  
- Effect has decayed in many modern samples; must not assume textbook magnitude.

**Verdict:** Strong **phase-2** candidate after earnings + estimate PIT data exists. Not a day-one model.

### 7. Fundamental quality/value ranking — research #20

**What’s good**

- Deep research: **investigate now**—clear rationale, monthly/quarterly turnover, anchors the fundamental pipeline.  
- Teaches restatement handling, sector exposure, and delisting bias.  
- Better capital fit than daily stock trading if implemented as a small, liquid, low-turnover book (or quality ETF proxies while single-name capacity is tiny).

**What’s weak**

- Currently parked under “advanced” EPIC #4 while weaker technical ideas sit in core EPIC #3—**backlog priority is inverted relative to evidence**.  
- Raw factor premia are modest and cyclical; easy to declare failure in the wrong decade without risk adjustment.  
- Needs serious PIT fundamentals (EDGAR/XBRL or vendor)—non-trivial data work.

**Verdict:** **Pull forward** as an early baseline alongside ETF trend. Prefer simple composite scores before ML weighting.

### 8. Macro-conditioned sector rotation — research #21

**What’s good**

- ETF implementation fits capital. Interpretable if feature set stays small.  
- Forces ALFRED vintages (true macro PIT)—high platform value.

**What’s weak**

- Deep research: **later**. Macro timing is hard; release lags and revisions dominate.  
- Easy to overfit a handful of famous indicators (yield curve, PMI).  
- Overlaps #2; build trend first, then ask whether macro adds incremental value.

**Verdict:** Natural extension of ETF allocation **after** FRED/ALFRED and a working trend baseline.

### 9. Opening-range continuation — research #22

**What’s good**

- Well-scoped intraday experiment someday; good for testing live data latency assumptions.

**What’s weak**

- Deep research: **de-prioritize**. Out of daily/weekly scope; needs aligned intraday history, spreads, and operational attention a solo IRA trader should not subsidize yet.

**Verdict:** Keep parked until the daily platform is boringly reliable.

### 10. Selective long-volatility options — research #23

**What’s good**

- Explicitly long-vol / no naked short-vol is the right safety instinct.  
- Distributional forecasting is intellectually interesting.

**What’s weak**

- Deep research: **omit for now**. Vol risk premium usually punishes long vol; chains, bid/ask, and IRA options permissions are heavy.  
- Broker API/options support and executable assumptions are unverified gates.  
- Worst capital match: options sizing and decay overwhelm a $2k account.

**Verdict:** Research fantasy until equities/ETF paper trading is proven. Do not let it block the roadmap.

## Net recommendation for the backlog

| Priority | Action |
| --- | --- |
| Now | Simple ETF trend + vol scaling (#15 baseline); simple momentum and/or quality-value (#7 / momentum baseline) |
| Next | Costed walk-forward harness + null tests; only then LightGBM (#14) as challenger |
| Later | PEAD (#6), macro rotation (#8) once data epics land |
| Park / omit | Mean reversion (#3), breakout ML (#4), pairs (#5), opening range (#9), long-vol options (#10) for this capital and scope |

## What would change these conclusions

- Credible after-cost walk-forward where a short-horizon strategy still clears a pre-registered hurdle at realistic IRA notionals  
- Point-in-time vendor data that removes Alpaca free-tier limitations for research  
- Explicit scope change allowing shorts, larger capital, or options in the IRA  

Until then, optimize for **learning speed and capital realism**, not model theater.
