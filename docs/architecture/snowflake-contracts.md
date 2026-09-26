# Snowflake logical contracts and ownership

This catalog defines interfaces, not deployed database or table names. The producing repository owns the logical schema, writes and migrations. Consumers receive read access only; no child imports another child's runtime Python package for an interface that belongs here. Physical DDL, roles and retention periods require separately reviewed implementation in the owning repository.

## Domain policy

| Domain | Owner / producer | Consumers | Purpose, mutability, PIT, versioning and retention |
| --- | --- | --- | --- |
| RAW | `quant-data` ingestion | `quant-data`; restricted audit | Source-native payload and capture evidence. Append captures/corrections; keep event, source availability and ingestion times. Version capture format; retain enough history for replay and source audit. |
| CORE | `quant-data` validation | `quant-research`, authorized `quant-execution` | Canonical entities and observations. Revisions are explicit; PIT readers choose versions available at decision time. Version semantics and preserve superseded rows for replay. |
| FEATURES | `quant-research` feature publication | `quant-research`, authorized `quant-execution` where needed | Versioned derived features with input manifest and calculation cutoff. Recompute as a new version, never silently rewrite prior evidence. Retain versions used by backtests or execution. |
| ML | `quant-research` training, evaluation, approval and signal publication | `quant-research`, read-only `quant-execution` | Immutable model/experiment/approval/prediction evidence. Approval changes are new events; prediction has decision time and expiry. Retain every version referenced by execution. |
| EXECUTION | `quant-execution` runs, controls and broker reconciliation | `quant-execution`, restricted audit | Append-only decisions, order lifecycle, broker observations and snapshots. Corrections are linked events. Retain enough to reconstruct every execution and incident. |

## Dataset catalog

Each row names a logical dataset, owner, grain and required semantics. Domain policy supplies its producer, consumers, mutation, version and retention rules.

| Contract | Owner | Grain and semantics |
| --- | --- | --- |
| RAW source capture | `quant-data` | Provider, source record and capture revision; original payload, received time and provenance. |
| CORE.SECURITY | `quant-data` | Stable internal security id and identifier namespace; never infer identity from ticker alone. |
| CORE.SECURITY_HISTORY | `quant-data` | Security id, attribute, valid interval and known-from revision; ticker/listing history is PIT. |
| CORE.DAILY_PRICE | `quant-data` | Security, session, provider and revision; price units, adjustment policy, event and available times. |
| CORE.INTRADAY_PRICE | `quant-data` | Security, interval, provider and revision; exchange timezone, interval closure and availability. |
| CORE.CORPORATE_ACTION | `quant-data` | Security, action identity and revision; announcement, effective and availability times separated. |
| CORE.FUNDAMENTAL_FACT | `quant-data` | Entity, metric, period, filing/source and revision; published/available time, units and restatements. |
| CORE.EARNINGS_EVENT | `quant-data` | Entity, event and revision; scheduled versus actual event times and announcement availability. |
| CORE.MACRO_OBSERVATION | `quant-data` | Series, observation period and vintage; release and revision availability. |
| FEATURES.STOCK_DAILY_FEATURES | `quant-research` | Security, decision date, feature-set version and input manifest; values use only eligible inputs. |
| FEATURES.STOCK_FUNDAMENTAL_FEATURES | `quant-research` | Security, decision time and feature-set version; filing availability is enforced. |
| FEATURES.MACRO_FEATURES | `quant-research` | Series/context, decision time and feature-set version; vintage is pinned. |
| FEATURES.EVENT_EARNINGS_FEATURES | `quant-research` | Entity/event, decision time and feature-set version; never leak later actuals. |
| ML.MODEL | `quant-research` | Stable model identity, strategy association and purpose. |
| ML.MODEL_VERSION | `quant-research` | Immutable artifact/code/config/data manifest identity and creation time. |
| ML.EXPERIMENT | `quant-research` | Hypothesis, owner, baseline and data dependency identity. |
| ML.TRAINING_RUN | `quant-research` | Run, model version, input manifest, time windows and outcome. |
| ML.BACKTEST | `quant-research` | Run, strategy/model version, PIT input manifest, cost assumptions and time window. |
| ML.BACKTEST_METRIC | `quant-research` | Backtest, metric name, unit, slice and method; metrics never imply approval. |
| ML.PREDICTION | `quant-research` | Strategy/model version, security, decision time and signal id; value/units, available time and expiry. |
| ML.STRATEGY_APPROVAL | `quant-research` | Append-only strategy/version state transition, reviewer, evidence and effective time. Live enablement also needs execution configuration. |
| EXECUTION.EXECUTION_RUN | `quant-execution` | Scheduled run, environment, mode, pinned input/config versions and outcome. |
| EXECUTION.TARGET_POSITION | `quant-execution` | Run, strategy, security and target; units, portfolio constraints and source signal. |
| EXECUTION.ORDER_INTENT | `quant-execution` | Stable intent/client id before submission; target, account/mode, approval and risk references. |
| EXECUTION.BROKER_ORDER | `quant-execution` | Broker order id and client id; append lifecycle observations, status and broker timestamps. |
| EXECUTION.FILL | `quant-execution` | Broker fill/execution id; quantity, price, fees, time and order reference. |
| EXECUTION.POSITION_SNAPSHOT | `quant-execution` | Account, security and as-of time; source and reconciliation run. |
| EXECUTION.ACCOUNT_SNAPSHOT | `quant-execution` | Account and as-of time; cash, equity, exposure units and source. |
| EXECUTION.RECONCILIATION_RESULT | `quant-execution` | Run, account, comparison time, discrepancy and disposition. |
| EXECUTION.RISK_DECISION | `quant-execution` | Intent/run, policy version, deterministic pass/veto reason and inputs. |

## Common envelope and time rule

Each published cross-repository record carries a stable record id, producer/source, `run_id`, contract name/version, `event_at`, `available_at`, `created_at` and provenance. `ingested_at` applies to acquired data; `as_of_at` to snapshots/decisions; `model_version_id`, `strategy_id`, `correlation_id`, `order_intent_id` and broker identifiers apply where relevant. Timestamps are UTC with explicit source timezone when meaningful. Null/unknown, absent, and valid zero are distinct. Units, currency, grain and revision relationships are contract fields. A dataset manifest references exact source/revision sets and code/config versions; a consumer cannot substitute current latest data for a pinned manifest.

`event_at` says when something happened or was observed; `available_at` says when the platform could know it; `ingested_at` says when it was captured. For a decision at time T, use only records with `available_at <= T` and a version available by T, resolving revisions under the declared selection policy. Late arrivals and material corrections get a new revision linked to the predecessor. A correction cannot rewrite past knowledge. Unknown availability makes a record ineligible for PIT use until resolved or explicitly constrained by a reviewed assumption.

## Compatibility rule

Use `major.minor` contract versions. An additive nullable field with stable meaning can increment minor; a removed/renamed field, changed unit, grain, identity, timing, null meaning or selection rule increments major. Producer publishes a migration note, sample, validation and overlap period before consumers switch; consumers pin and reject unsupported major versions. No silent reinterpretation or overwrite of auditable records. Contract changes are reviewed across affected repositories and documented here before dependent implementation. See [ADR 0007](../adr/0007-contract-versioning-and-revision-policy.md).
