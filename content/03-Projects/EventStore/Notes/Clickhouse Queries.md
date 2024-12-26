---
{"aliases":null,"tags":["Database/Clickhouse"],"publish":true,"date created":"Friday, December 6th 2024, 11:24:10 am","date modified":"Wednesday, December 18th 2024, 10:25:24 pm","Description":"Some useful internal queries of clickhouse","PassFrontmatter":true,"created":"2024-12-06T11:24:10.884+05:30","updated":"2024-12-26T09:09:20.831+05:30"}
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
