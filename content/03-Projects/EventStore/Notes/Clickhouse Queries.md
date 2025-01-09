---
{"aliases":["Clickhouse Queries"],"tags":["Database/Clickhouse","Database/Clickhouse/SQL"],"publish":true,"date created":"Friday, December 6th 2024, 11:24:10 am","date modified":"Wednesday, December 18th 2024, 10:25:24 pm","Description":"Some useful internal queries of clickhouse","PassFrontmatter":true,"created":"2024-12-06T11:24:10.884+05:30","updated":"2025-01-09T21:00:50.278+05:30"}
---


#### Table Creation Command

```sql
CREATE TABLE helloworld.my_first_table
(
    user_id UInt32,
    message String,
    timestamp DateTime,
    metric Float32
)
ENGINE = MergeTree()
PRIMARY KEY (user_id, timestamp)
```

#### Inserting Some Data into a Table

```sql
INSERT INTO helloworld.my_first_table (user_id, message, timestamp, metric) VALUES
    (101, 'Hello, ClickHouse!',                                 now(),       -1.0    ),
    (102, 'Insert a lot of rows per batch',                     yesterday(), 1.41421 ),
    (102, 'Sort your data based on your commonly-used queries', today(),     2.718   ),
    (101, 'Granules are the smallest chunks of data read',      now() + 5,   3.14159 )
```

#### Get Table Schema
```sql
SHOW CREATE TABLE table_name;
```

#### Compression ratio 
##### Overall table
```sql
SELECT formatReadableSize(sum(data_compressed_bytes)) AS compressed_size,  
formatReadableSize(sum(data_uncompressed_bytes)) AS uncompressed_size,  
round(sum(data_uncompressed_bytes) / sum(data_compressed_bytes), 2) AS ratio  
FROM system.columns  
WHERE table = 'otel_traces'
```
##### Column details
```sql
SELECT
    name,
    formatReadableSize(sum(data_compressed_bytes)) AS compressed_size,
    formatReadableSize(sum(data_uncompressed_bytes)) AS uncompressed_size,
    round(sum(data_uncompressed_bytes) / sum(data_compressed_bytes), 2) AS ratio
FROM system.columns
WHERE table = 'otel_traces'
GROUP BY name order by compressed_size
```

##### Index
```sql

todo


```
#### Get partition data
```sql
SELECT
    partition_id,
    min_date,
    max_date,
    sum(rows) AS rows,
    sum(bytes_on_disk) AS size_on_disk
FROM system.parts
WHERE database = 'otel' AND table = 'otel_traces'
GROUP BY partition_id, min_date, max_date
ORDER BY min_date;
```

#### Alert table to create materialized `Nested` column from a `Map`
```sql
ALTER TABLE otel_traces
MODIFY COLUMN nested_SpanAttributes.keys Array(LowCardinality(String)) MATERIALIZED mapKeys(SpanAttributes) CODEC(ZSTD(1))
ALTER TABLE otel_traces
MODIFY COLUMN nested_SpanAttributes.values Array(String) MATERIALIZED mapValues(SpanAttributes) CODEC(ZSTD(1))
```
