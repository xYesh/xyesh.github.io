---
{"aliases":null,"Description":"Master doc for Clickhouse","tags":["Database/Clickhouse","MasterDoc"],"date created":"2024-12-02T19:15","date modified":"2025-01-15T21:41","projects":["EventStore"],"publish":true,"type":"Note","PassFrontmatter":true,"created":"2025-01-14T15:25:43.451+05:30","updated":"2025-01-15T21:41:20.605+05:30"}
---

## Details

Installing ClickHouse locally to play around - [Installing clickhouse on macos](<https://www.notion.so/Installing-clickhouse-on-macos-121079ed9ace80be9a34ee8cf223e19a?pvs=21>

### Misc
- [https://clickhouse.com/docs/en/sql-reference/statements/kill](https://clickhouse.com/docs/en/sql-reference/statements/kill) kill running queries
- [https://clickhouse.com/docs/en/sql-reference/statements/explain](https://clickhouse.com/docs/en/sql-reference/statements/explain) explain query plan
- [https://clickhouse.com/docs/en/sql-reference/statements/optimize](https://clickhouse.com/docs/en/sql-reference/statements/optimize) force merge of contents for better queries
- You can partition based on a column which is not an index!!
- [https://clickhouse.com/docs/knowledgebase/maximum_number_of_tables_and_databases](https://clickhouse.com/docs/knowledgebase/maximum_number_of_tables_and_databases)
    - Recommendations for maximum number of columns, tables, partitions or parts.
- [https://clickhouse.com/docs/en/optimize/asynchronous-inserts](https://clickhouse.com/docs/en/optimize/asynchronous-inserts)

### Database Engines
- [https://clickhouse.com/docs/en/engines/database-engines/atomic](https://clickhouse.com/docs/en/engines/database-engines/atomic) default engine[^1]
- [https://clickhouse.com/docs/en/engines/database-engines/lazy](https://clickhouse.com/docs/en/engines/database-engines/lazy)
    - This is like a cold store, Loads contents into memory only when it’s queried.
- There are some hacky ways to alert the table and change database engines - [https://stackoverflow.com/questions/68716267/is-it-possible-to-change-a-table-engine-of-an-existed-clickhouse-table/68716746#68716746](https://stackoverflow.com/questions/68716267/is-it-possible-to-change-a-table-engine-of-an-existed-clickhouse-table/68716746#68716746)
    - You can also move data from one table to another quite easily using materialized views or the partitions directly
    - [Clickhouse data lifecycle](https://www.notion.so/Clickhouse-data-lifecycle-12a079ed9ace800e93a1c51a702f2367?pvs=21)

# Materialized View Details
- Moving the cost of compute intensive queries from query time to insertion time.

## Performance
- Materialized views in ClickHouse are updated in real time as data flows into the table they are based on, functioning more like continually updating indexes. This is in contrast to other databases where materialized views are typically static snapshots of a query that must be refreshed (similar to ClickHouse [refreshable materialized views](https://clickhouse.com/docs/en/sql-reference/statements/create/view#refreshable-materialized-view))
- Benchmaking [https://double.cloud/blog/posts/2022/12/performance-impact-of-materialized-views-in-clickhouse/](https://double.cloud/blog/posts/2022/12/performance-impact-of-materialized-views-in-clickhouse/)
    - Peak insert performance slows down significantly when you add materialized views.
    - Insert time degrades linearly to the number of chained MV
        - This is **not just due to disk operations**, even with a NULL engine, we see degradations.
    - for parallel MVs, we see the same linear degradation.

# Projections
- Projections are like [materialized views](https://clickhouse.com/docs/en/sql-reference/statements/create/view#materialized) but defined in part-level. It provides consistency guarantees along with automatic usage in queries.
- Projections are an appealing feature for new users as they are automatically maintained as data is inserted. Furthermore, queries can just be sent to a single table where the projections are exploited where possible to speed up the response time.

### When to Use Projections

We recommend using projections when:

- A complete reordering of the data is required. While the expression in the projection can, in theory, use a **`GROUP BY,`** materialized views are more effective for maintaining aggregates. The query optimizer is also more likely to exploit projections that use a simple reordering, i.e., **`SELECT * ORDER BY x`**. Users can select a subset of columns in this expression to reduce storage footprint.
- Users are comfortable with the associated increase in storage footprint and overhead of writing data twice. Test the impact on insertion speed and [evaluate the storage overhead](https://clickhouse.com/docs/en/data-compression/compression-in-clickhouse).

> [!Comparison]
> # Projection vs Materialized view

[https://clickhouse.com/docs/en/migrations/postgresql/data-modeling-techniques](https://clickhouse.com/docs/en/migrations/postgresql/data-modeling-techniques)

Projections are an appealing feature for new users as they are automatically maintained as data is inserted. Furthermore, queries can just be sent to a single table where the projections are exploited where possible to speed up the response time.

This is in contrast to materialized views, where the user has to select the appropriate optimized target table or rewrite their query, depending on the filters. This places greater emphasis on user applications and increases client-side complexity.

### Dis-advantages of Projections

- ~~Lightweight updates and deletes are not supported for tables with projections.~~
- Materialized views can be chained: the target table of one materialized view can be the source table of another materialized view, and so on. This is not possible with projections.
- Projections don't support filters (WHERE clause); materialized views do.
- Materialized views are more effective for maintaining aggregates.
- Users should be comfortable with the associated increase in storage footprint and overhead of writing data twice.

> [!Important]
> # General guides on how to write indexes

[https://clickhouse.com/docs/en/optimize/sparse-primary-indexes#an-index-design-for-massive-data-scales](https://clickhouse.com/docs/en/optimize/sparse-primary-indexes#an-index-design-for-massive-data-scales)

When a query is filtering on at least one column that is part of a compound key, and is the first key column, [then ClickHouse is running the binary search algorithm over the key column's index marks](https://clickhouse.com/docs/en/optimize/sparse-primary-indexes#the-primary-index-is-used-for-selecting-granules).

When a query is filtering (only) on a column that is part of a compound key, but is not the first key column, [then ClickHouse is using the generic exclusion search algorithm over the key column's index marks](https://clickhouse.com/docs/en/optimize/sparse-primary-indexes#secondary-key-columns-can-not-be-inefficient).

For the second case the ordering of the key columns in the compound primary key is significant for the effectiveness of the [generic exclusion search algorithm](https://github.com/ClickHouse/ClickHouse/blob/22.3/src/Storages/MergeTree/MergeTreeDataSelectExecutor.cpp#L1444).

### **The Primary Index Has One Entry per granule[](https://clickhouse.com/docs/en/optimize/sparse-primary-indexes#the-primary-index-has-one-entry-per-granule)**

![Screenshot 2024-12-02 at 7.17.59 PM.png](../../../03-Projects/EventStore/Notes/attachments/Screenshot%202024-12-02%20at%207.17.59%20PM.png)

### Compression of Index

- If we order based on cardinality. i.e, lower cardinality first, we also save a lot of space as the compression algorithm will work more efficiently.
    - This is because the index contents are sorted and if the lower cardinality PK is sorted, the compound index keys have a higher chance of being sorted properly.
    - If the PK has high cardinality, the following keys will not be sorted that efficiently as it’s sorted based on the first key and then the second key.

---

> [!Important]
> # Hardware Sizing / Scale recommendations

- [https://clickhouse.com/docs/en/guides/sizing-and-hardware-recommendations](https://clickhouse.com/docs/en/guides/sizing-and-hardware-recommendations)
- [https://kb.altinity.com/altinity-kb-schema-design/how-much-is-too-much/](https://kb.altinity.com/altinity-kb-schema-design/how-much-is-too-much/)
- [https://clickhouse.com/docs/knowledgebase/maximum_number_of_tables_and_databases](https://clickhouse.com/docs/knowledgebase/maximum_number_of_tables_and_databases)

## Hardware Recommendations

### CPU

- 8:1 memory to CPU core ratio

### Disk

- SSD EBS storage
- You can also implement a tiered storage using SSDs and HDDs in a [hot/warm/cold architecture](https://clickhouse.com/docs/en/guides/developer/ttl#implementing-a-hotwarmcold-architecture)


> [!NOTE] Disk space calculation
> Size per span = ~ 1 - 2kB

| Ingestion rate | Space per Hour | Space per day |
| -------------- | -------------- | ------------- |
| 100k/s         | 360 gb / hour  | 8.6 TB        |

### Memory

- For use cases with long retention periods for your data or with high data volumes, we recommend a 1:100 to 1:130 memory to storage ratio. For example, 100GB of RAM per replica if you are storing 10TB of data.

---
## Number of Tables

- Replicated merge tree
    - It’s not recommended to have more than a few dozens with active insertions

## **Number Of Inserts per seconds**

- For usual (non async) inserts - dozen is enough. Every insert creates a part, if you will create parts too often, ClickHouse will not be able to merge them and you will be getting ’too many parts’.

## **Number Of Parts / Partitions (system-wide, Across All databases)**

[https://github.com/ClickHouse/ClickHouse/issues/10087](https://github.com/ClickHouse/ClickHouse/issues/10087)

---

# Performance Tuning
- [https://blog.cloudflare.com/http-analytics-for-6m-requests-per-second-using-clickhouse/?utm_source=linkedin&utm_medium=social&utm_campaign=blog/#clickhouse-performance-tuning](https://blog.cloudflare.com/http-analytics-for-6m-requests-per-second-using-clickhouse/?utm_source=linkedin&utm_medium=social&utm_campaign=blog/#clickhouse-performance-tuning)

# System Parameters
- [https://medium.com/@ShivIyer/how-to-configure-clickhouse-server-for-high-performance-5d7dbb37da47](https://medium.com/@ShivIyer/how-to-configure-clickhouse-server-for-high-performance-5d7dbb37da47)


> [!NOTE] Bechmarking design
> [[03-Projects/EventStore/Notes/Clickhouse Benchmarking design\|Clickhouse Benchmarking design]]
---
# Footnotes
# Footnotes
[^1]: This is what is used in the majority of usecases. 