---
{"tags":["Database/Clickhouse","Database/Clickhouse/Compression"],"projects":["EventStore"],"url":null,"type":"Note","Description":null,"Areas":null,"publish":true,"PassFrontmatter":true,"created":"2025-01-08T11:21:04.177+05:30","updated":"2025-01-09T21:07:38.799+05:30"}
---

# Environment details
## Schema
```sql
CREATE TABLE otel.otel_traces
(
    `Timestamp` DateTime64(9) CODEC(Delta(8), ZSTD(1)),
    `TraceId` String CODEC(ZSTD(1)),
    `SpanId` String CODEC(ZSTD(1)),
    `ParentSpanId` String CODEC(ZSTD(1)),
    `TraceState` String CODEC(ZSTD(1)),
    `SpanName` LowCardinality(String) CODEC(ZSTD(1)),
    `SpanKind` LowCardinality(String) CODEC(ZSTD(1)),
    `ServiceName` LowCardinality(String) CODEC(ZSTD(1)),
    `ResourceAttributes` Map(LowCardinality(String), String) CODEC(ZSTD(1)),
    `ScopeName` String CODEC(ZSTD(1)),
    `ScopeVersion` String CODEC(ZSTD(1)),
    `SpanAttributes` Map(LowCardinality(String), String) CODEC(ZSTD(1)),
    `Duration` UInt64 CODEC(ZSTD(1)),
    `StatusCode` LowCardinality(String) CODEC(ZSTD(1)),
    `StatusMessage` String CODEC(ZSTD(1)),
    `Events.Timestamp` Array(DateTime64(9)) CODEC(ZSTD(1)),
    `Events.Name` Array(LowCardinality(String)) CODEC(ZSTD(1)),
    `Events.Attributes` Array(Map(LowCardinality(String), String)) CODEC(ZSTD(1)),
    `Links.TraceId` Array(String) CODEC(ZSTD(1)),
    `Links.SpanId` Array(String) CODEC(ZSTD(1)),
    `Links.TraceState` Array(String) CODEC(ZSTD(1)),
    `Links.Attributes` Array(Map(LowCardinality(String), String)) CODEC(ZSTD(1)),
    INDEX idx_trace_id TraceId TYPE bloom_filter(0.001) GRANULARITY 1,
    INDEX idx_res_attr_key mapKeys(ResourceAttributes) TYPE bloom_filter(0.01) GRANULARITY 1,
    INDEX idx_res_attr_value mapValues(ResourceAttributes) TYPE bloom_filter(0.01) GRANULARITY 1,
    INDEX idx_span_attr_key mapKeys(SpanAttributes) TYPE bloom_filter(0.01) GRANULARITY 1,
    INDEX idx_span_attr_value mapValues(SpanAttributes) TYPE bloom_filter(0.01) GRANULARITY 1,
    INDEX idx_duration Duration TYPE minmax GRANULARITY 1
)
ENGINE = MergeTree
PARTITION BY toStartOfInterval(Timestamp, toIntervalHour(1))
ORDER BY (ServiceName, SpanName, toDateTime(Timestamp))
SETTINGS index_granularity = 8192, ttl_only_drop_parts = 1
```

## Topology
```
Single node cluster
CPU - 40
RAM - 240GB
Disk - 5TB Standard EBS
```

# Compression observations
## Table level
```
┌─compressed_size─┬─uncompressed_size─┬─ratio─┐
1. │ 2.05 TiB        │ 6.15 TiB          │  3.01 
└─────────────────┴───────────────────┴───────┘
```

## Column level
```
    ┌─name───────────────┬─compressed_size─┬─uncompressed_size─┬───ratio─┐
 1. │ SpanId             │ 63.22 GiB       │ 122.73 GiB        │    1.94 │
 2. │ TraceId            │ 117.23 GiB      │ 238.25 GiB        │    2.03 │
 3. │ SpanName           │ 20.28 MiB       │ 7.25 GiB          │  366.04 │
 4. │ SpanKind           │ 31.78 MiB       │ 7.28 GiB          │  234.51 │
 5. │ Duration           │ 28.81 GiB       │ 57.76 GiB         │       2 │
 6. │ Links.TraceId      │ 30.41 MiB       │ 57.76 GiB         │ 1944.82 │
 7. │ SpanAttributes     │ 1.74 TiB        │ 4.71 TiB          │     2.7 │
 8. │ Links.SpanId       │ 0.00 B          │ 0.00 B            │     nan │
 9. │ Links.Attributes   │ 12.84 KiB       │ 2.45 KiB          │    0.19 │
10. │ ScopeVersion       │ 18.93 MiB       │ 28.88 GiB         │ 1562.57 │
11. │ ServiceName        │ 20.20 MiB       │ 7.25 GiB          │  367.59 │
12. │ ScopeName          │ 34.29 MiB       │ 57.76 GiB         │ 1724.64 │
13. │ Events.Name        │ 19.66 MiB       │ 737.87 MiB        │   37.54 │
14. │ ParentSpanId       │ 58.23 GiB       │ 113.43 GiB        │    1.95 │
15. │ TraceState         │ 4.89 MiB        │ 33.15 MiB         │    6.78 │
16. │ Events.Timestamp   │ 3.90 GiB        │ 63.07 GiB         │   16.19 │
17. │ StatusMessage      │ 4.89 MiB        │ 33.15 MiB         │    6.78 │
18. │ Links.TraceState   │ 0.00 B          │ 0.00 B            │     nan │
19. │ StatusCode         │ 31.78 MiB       │ 7.28 GiB          │  234.51 │
20. │ Timestamp          │ 36.09 GiB       │ 57.76 GiB         │     1.6 │
21. │ ResourceAttributes │ 3.59 GiB        │ 620.95 GiB        │  173.02 │
22. │ Events.Attributes  │ 229.75 MiB      │ 32.58 GiB         │  145.22 │
    └────────────────────┴─────────────────┴───────────────────┴─────────┘
```


