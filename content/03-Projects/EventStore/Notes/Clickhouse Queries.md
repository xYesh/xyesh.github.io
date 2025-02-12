---
{"aliases":["Clickhouse Queries"],"tags":["Database/Clickhouse","Database/Clickhouse/SQL"],"publish":true,"date created":"2024-12-06T11:24","date modified":"2025-02-07T13:18","Description":"Some useful internal queries of clickhouse","projects":["EventStore"],"type":"Note","PassFrontmatter":true,"created":"2025-01-14T15:25:42.559+05:30","updated":"2025-02-07T13:18:12.697+05:30"}
---


**Table of contents**
- [Table Creation Command](#Table%20Creation%20Command)
- [Inserting Some Data into a Table](#Inserting%20Some%20Data%20into%20a%20Table)
- [Get Table Schema](#Get%20Table%20Schema)
- [Compression Ratio / Sizes](#Compression%20Ratio%20/%20Sizes)
	- [Overall Table](#Overall%20Table)
	- [Column Details](#Column%20Details)
	- [Index](#Index)
	- [Average Bytes per Row for a Table](#Average%20Bytes%20per%20Row%20for%20a%20Table)
	- [Get Partition Data](#Get%20Partition%20Data)
- [Alter Table](#Alter%20Table)
	- [Alert Table to Create Materialized `Nested` Column From a `Map`](#Alert%20Table%20to%20Create%20Materialized%20%60Nested%60%20Column%20From%20a%20%60Map%60)
	- [Alter Tables to Add TTL](#Alter%20Tables%20to%20Add%20TTL)
- [Debugging Queries](#Debugging%20Queries)
	- [Check if Read Queries Are Spilling Out of RAM into Disk](#Check%20if%20Read%20Queries%20Are%20Spilling%20Out%20of%20RAM%20into%20Disk)
- [Random useful queries](#Random%20useful%20queries)
	- [Min and max of Duration](#Min%20and%20max%20of%20Duration)


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

#### Compression Ratio / Sizes
##### Overall Table
```sql
SELECT formatReadableSize(sum(data_compressed_bytes)) AS compressed_size,  
formatReadableSize(sum(data_uncompressed_bytes)) AS uncompressed_size,  
round(sum(data_uncompressed_bytes) / sum(data_compressed_bytes), 2) AS ratio  
FROM system.columns  
WHERE table = 'otel_traces'
```
##### Column Details
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

##### Average Bytes per Row for a Table
```sql
SELECT 
    (SELECT sum(bytes_on_disk) FROM system.parts WHERE database = 'otel' AND table = 'otel_traces') 
    / 
    (SELECT count() FROM otel.otel_traces) AS approx_row_size_bytes;
```
##### Get Partition Data
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

#### Alter Table
##### Alert Table to Create Materialized `Nested` Column From a `Map`
```sql
ALTER TABLE otel_traces
MODIFY COLUMN nested_SpanAttributes.keys Array(LowCardinality(String)) MATERIALIZED mapKeys(SpanAttributes) CODEC(ZSTD(1))
ALTER TABLE otel_traces
MODIFY COLUMN nested_SpanAttributes.values Array(String) MATERIALIZED mapValues(SpanAttributes) CODEC(ZSTD(1))



```

##### Alter Tables to Add TTL
```sql

```
#### Debugging Queries
##### Check if Read Queries Are Spilling Out of RAM into Disk
```sql
SELECT 
    query_id,
    query,
    memory_usage AS max_memory_usage,
    read_bytes,
    written_bytes
FROM system.query_log
WHERE type = 'QueryFinish'
  AND (read_bytes > 0 OR written_bytes > 0)
  AND NOT (query LIKE 'INSERT%' OR query LIKE 'UPDATE%' OR query LIKE 'DELETE%')
ORDER BY event_time DESC
LIMIT 10;
```

#### Random Useful Queries
##### Min and Max of Duration
```sql
SELECT 
    min(Duration) AS min_duration, 
    max(Duration) AS max_duration
FROM otel_traces
WHERE Timestamp >= now() - INTERVAL 3 HOUR;
```
