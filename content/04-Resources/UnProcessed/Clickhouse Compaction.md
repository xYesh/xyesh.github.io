---
{"aliases":null,"tags":["Database/Clickhouse","Database/Clickhouse/Compaction"],"projects":["EventStore"],"url":null,"type":"Note","Description":"Everything related to Clickhouse Compaction","Areas":null,"publish":true,"date created":"2025-01-15T17:04","date modified":"2025-01-15T18:42","PassFrontmatter":true,"created":"2025-01-15T19:15:18.031+05:30","updated":"2025-01-15T18:42:16.801+05:30"}
---

# How to Tell if Compaction is Happening on ClickHouse

> [!important]
> Check this [handy page](https://kb.altinity.com/altinity-kb-schema-design/how-much-is-too-much/) to find BAU numbers for most parameters.
## Max Parts per Partition
### PromQL
```PromQL
max(ClickHouseAsyncMetrics_MaxPartCountForPartition[1m])
```

![Screenshot 2025-01-15 at 5.16.06 PM.png](../../04-Resources/UnProcessed/attachments/Screenshot%202025-01-15%20at%205.16.06%20PM.png)

### SQL
```SQL
select * from system.asynchronous_metric_log where metric = 'MaxPartCountForPartition' order by event_time desc limit 1;
```

## Total Number of Parts

### PromQL
```PromQL
max(ClickHouseAsyncMetrics_TotalPartsOfMergeTreeTables[1m])
```

![Screenshot 2025-01-15 at 5.34.38 PM.png](../../04-Resources/UnProcessed/attachments/Screenshot%202025-01-15%20at%205.34.38%20PM.png)

### SQL
```sql
select * from system.asynchronous_metric_log where metric = 'TotalPartsOfMergeTreeTables' order by event_time desc limit 1;
```

## Number of Merges Running

### PromQL
```PromQL
max(ClickHouseMetrics_Merge[1m])
```

![Screenshot 2025-01-15 at 5.43.08 PM.png](../../04-Resources/UnProcessed/attachments/Screenshot%202025-01-15%20at%205.43.08%20PM.png)

### SQL
```sql
select event_time,CurrentMetric_Merge from system.metric_log limit 1;
```
