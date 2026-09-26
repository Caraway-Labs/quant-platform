# ADR 0007: Contract versioning and revision policy

## Status

Accepted

## Context

ADR 0002 requires versioned Snowflake contracts and ADR 0003 requires PIT revisions, but consumers need a shared compatibility rule.

## Decision

Cross-repository logical contracts use `major.minor` versions. Additive nullable fields with unchanged semantics increment minor; changed grain, identity, units, time, null meaning or removed fields increment major. Producers own migration and publish overlap evidence; consumers pin supported major versions and reject incompatible data. Material corrections append linked revisions with their actual availability time. See the [contract catalog](../architecture/snowflake-contracts.md).

## Consequences

Historical decisions remain reconstructable. Cross-repository migrations need coordinated review and temporary overlap; no schema registry service is mandated.

## Alternatives considered

Unversioned in-place tables invite silent reinterpretation. A central registry service adds operational complexity before a concrete need exists.
