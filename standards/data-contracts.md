# Data contracts

Use Snowflake as the preferred cross-repository contract and system-of-record layer where practical. Name producer, consumers, schema and version, grain, units, time semantics, lineage, null/missing states, access policy, and compatibility or migration plan. Point-in-time consumers must use what was available at decision time, including revisions. Do not silently reinterpret missing data as zero or rely on undocumented child-repository internals.
