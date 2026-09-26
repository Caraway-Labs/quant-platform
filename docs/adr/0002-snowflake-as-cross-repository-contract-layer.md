# ADR 0002: Snowflake as cross-repository contract layer

## Status

Accepted

## Context

Data, research, and execution need discoverable, reproducible exchange without undocumented imports or direct access to each other's internal stores.

## Decision

Use Snowflake as the primary cross-repository system of record and versioned contract layer where practical. Each interface identifies producer, consumer, schema, grain, time semantics, lineage, access, and compatibility policy. Exceptions require documentation and review.

## Consequences

Producers must preserve stable contract semantics; consumers validate versions and access. This adds migration discipline and does not imply every transient runtime message must use Snowflake.

## Alternatives considered

Direct repository imports and private tables are quicker initially but create hidden coupling and unclear ownership.
