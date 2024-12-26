---
{"aliases":null,"tags":["O11y","Benchmark","Database/Clickhouse","Benchmark/k6"],"projects":["EventStore"],"type":null,"Description":null,"Areas":null,"publish":true,"date created":"Wednesday, December 4th 2024, 9:19:36 am","date modified":"Wednesday, December 18th 2024, 10:25:20 pm","PassFrontmatter":true,"created":"2024-12-04T09:19:36.042+05:30","updated":"2024-12-26T08:34:11.529+05:30"}
---


Official benchmarking docs - [ClickBench — a Benchmark For Analytical DBMS](https://benchmark.clickhouse.com/)  

- It has comparisons to Pinot too.
# Objective

Build a performance tool to simulate high volume read operations on trace data specifically targeting a QPS of  100.

---
# Functional Requirements

**Ability to configure test load scenarios, including:**
1. Target data(traces, logs, metrics)
2. Target  data store (ClickHouse, Pinot, Victoria Logs,etc)
3. Query types (e.g., primary key/value search, secondary key/value search, time-series queries, text search )
4. Load parameters (number of concurrent users, ramp-up time, duration)

**Executing Performance test plan:**
1. Execute predefined test scenarios.
2. Generate and send test traffic (queries) to the target hot store(CH/Pinot/VL/etc).
3. Monitor and record performance metrics (e.g., response time, throughput, error rate).

# Non - Functional Requirements
1. **Maintainability: P2**
2. Modular and well-structured codebase.
3. Easy to update and extend for new hot stores or test scenarios.
4. Generate target QPS of 100.

---
# Available Solutions

## Clickhouse-benchmark

doc - [https://clickhouse.com/docs/en/operations/utilities/clickhouse-benchmark](https://clickhouse.com/docs/en/operations/utilities/clickhouse-benchmark)

### Pros
- Official benchmark tool used by Clickhouse
- Provides out of the box statistics like
    - QPS, RPS (rows read per second), MiB/s, Results per second, Percentile of query execution times.
- Provides several parameters like 
    - Number of concurrent queries. 
    - Randomize the input queries. 
    - Number of iterations. 

### Cons
- Input is a file containing all the queries that need to be run. 
    - **Need to write a script that will generates the input file based on some parameters**
- Queries cannot contain placeholders that can be replaced during runtime. 
- cannot have categories of queries, just one large list. 

  

## Python Script to Query Directly Using Clickhouse-cli

doc - [https://github.com/ClickHouse/simple-logging-benchmark](https://github.com/ClickHouse/simple-logging-benchmark)

### Pros
- Custom written script, we can easily have placeholders and other items in the query list that can be replaced during runtime. 

### Cons
- **Custom written script.** 
- Need to ensure it is stable and need to calculate all the stats and ensure accuracy. 

  

## K6 Script - Query via the Clickhouse HTTP Port

doc - Clickhouse HTTP interface - [https://clickhouse.com/docs/en/interfaces/http](https://clickhouse.com/docs/en/interfaces/http)

### Pros
- K6 script is an industry standard and we have some familiarity with it. 
- We can store the queries as templates and easily generate queries based on config.
- **Script can be re-used for testing other Datastores.**

### Cons
- The scripts need to be written manually.
- **Prod servers might use the official plugin which uses the TCP port to communicate**

  

---

# Query Patterns For Read Path Load Generation

Reference doc - [Ingestion/Query Patterns](https://docs.google.com/spreadsheets/d/1MPzTAPiBSUpUDzpWkOFd7W5RidLgKzux-Gaei0gsWW4/edit?gid=0#gid=0) 

UAR App traces - [http://10.83.35.43/](http://10.83.35.43/)

Query patterns

|   |   |   |   |
|---|---|---|---|
||Types|Queries|Templatized Queries|
|1|Distinct Operation Name|SELECT DISTINCT operationName FROM {placeholder_for_table}|select distinct {column_name} from {placeholder_for_table}|
|2|Distinct Service Name|SELECT DISTINCT serviceName FROM {placeholder_for_table}|
|3|Distinct SpanID|SELECT DISTINCT spanID FROM {placeholder_for_table}|
|4|Filter by ServiceName|SELECT * FROM {placeholder_for_table} WHERE ServiceName = 'rome_in-chennai-2’|select * from {table_name} where {key}={value}|
|5|Filter by Operation Name|SELECT * FROM {placeholder_for_table}<br><br>WHERE operationName = 'com.flipkart.poseidon.core.PoseidonServlet:service:(Ljavax/servlet/http/HttpServletRequest;Ljavax/servlet/http/HttpServletResponse;)<br><br>'|
|6|filter by Duration Range|SELECT * FROM {placeholder_for_table}  WHERE duration BETWEEN 100 AND 1000;|SELECT * FROM {placeholder_for_table}  WHERE duration BETWEEN 100 AND 1000;|
|7|Filter by Time Range|SELECT * FROM {placeholder_for_table}  WHERE start_time BETWEEN 'start_time' AND 'end_time';|SELECT * FROM {placeholder_for_table}  WHERE start_time BETWEEN 'start_time' AND 'end_time';|
|8|Filter by Multiple Criteria|SELECT * FROM {placeholder_for_table} WHERE service_name = 'your_service_name' AND operation_name = 'your_operation_name' AND start_time > '2023-01-01';|SELECT * FROM {placeholder_for_table} WHERE {key1} = {value1} AND {key2} = {value2} AND start_time > {start_time};|
|9|Group by Service Name and Count Spans|SELECT serviceName, COUNT(*) AS span_count FROM {placeholder_for_table} GROUP BY serviceName;|SELECT {coulmn_name}, COUNT(*) AS span_count FROM {placeholder_for_table} GROUP BY {cloumn_name};|
|10|Group by Operation Name and Calculate Average Duration|SELECT operationName, AVG(duration) AS avg_duration FROM {placeholder_for_table} GROUP BY operationName;|SELECT {column_name}, AVG(duration) AS avg_duration FROM {placeholder_for_table} GROUP BY {column_name};|
|11|Filter By duration|WITH long_duration_spans AS ( SELECT * FROM {placeholder_for_table} WHERE duration > 1000 ) SELECT * FROM long_duration_spans;|WITH long_duration_spans AS ( SELECT * FROM {placeholder_for_table} WHERE duration > 1000 ) SELECT * FROM long_duration_spans;|
|12|Extract Tag Values|SELECT trace_id, tags->>'key' AS tag_value FROM {placeholder_for_table} ;|SELECT {trace_id_column}, tags->>'key' AS tag_value FROM {placeholder_for_table} ;|
|13|Filter by Tag Key-Value Pair|SELECT * FROM {placeholder_for_table} WHERE tags @> '{"http.status_code": "500"}';|SELECT * FROM {placeholder_for_table} WHERE tags @> '{"{key}": "{value}"}';|
|14|Filter By startTime|SELECT * FROM {placeholder_for_table}<br><br>WHERE startTime > '2023-08-10 08:00:02.415166048'<br><br>AND startTime < '2023-08-10 09:16:28.998186527'|SELECT * FROM {placeholder_for_table}<br><br>WHERE startTime > '2023-08-10 08:00:02.415166048'<br><br>AND ‘startTime < '2023-08-10 09:16:28.998186527'|
|15|Filter by Multiple Conditions and Group|SELECT serviceName, operationName, COUNT(*) AS span_count FROM {placeholder_for_table} WHERE start_time BETWEEN '2024-11-01' AND '2024-12-31' AND duration > 100 GROUP BY serviceName, operationName;|SELECT {coulmn_name_1}, {coulmn_name_2}, COUNT(*) AS span_count FROM {placeholder_for_table} WHERE start_time BETWEEN '2024-11-01' AND '2024-12-31' AND duration > 100 GROUP BY {coulmn_name_1}, {coulmn_name_2};|
|16|Group by Time Intervals and Calculate Metrics|SELECT DATE_TRUNC('hour', startTime) AS hour, COUNT(*) AS span_count, AVG(duration) AS avg_duration FROM {placeholder_for_table} GROUP BY hour ORDER BY hour;|SELECT DATE_TRUNC('hour', startTime) AS hour, COUNT(*) AS span_count, AVG(duration) AS avg_duration FROM {placeholder_for_table} GROUP BY hour ORDER BY hour;|
|17|Group by Service Name and Calculate Percentiles|SELECT serviceName, percentile_cont(0.95) WITHIN GROUP (ORDER BY duration) AS p95_duration, percentile_cont(0.99) WITHIN GROUP (ORDER BY duration) AS p99_duration FROM {placeholder_for_table} GROUP BY serviceName;|SELECT {column_name}, percentile_cont(0.95) WITHIN GROUP (ORDER BY duration) AS p95_duration, percentile_cont(0.99) WITHIN GROUP (ORDER BY duration) AS p99_duration FROM {placeholder_for_table} GROUP BY {column_name};|
|18|Using Join|SELECT t1.*, t2.duration AS previous_duration FROM {placeholder_for_table} t1 JOIN traces t2 ON t1.trace_id = t2.trace_id AND t1.span_id = t2.parent_span_id WHERE t1.duration > 2 * t2.duration;|SELECT t1.*, t2.duration AS previous_duration FROM {placeholder_for_table} t1 JOIN traces t2 ON t1.  <br>{trace_id_column} = t2.{trace_id_column} AND t1.{span_id_column} = t2.{parent_span_id_column} WHERE t1.duration > 2 * t2.duration;|
|19|Filter By Tag key-Value|SELECT * FROM {placeholder_for_table} WHERE tags ->> ‘http.status_code’ = ‘500’|SELECT * FROM {placeholder_for_table} WHERE tags ->> ‘http.status_code’ = ‘500’|
|20|Multiple filter Query|SELECT startTime,<br><br>TraceId,<br><br>SpanId,<br><br>ParentSpanId,<br><br>ServiceName,<br><br>Duration,<br><br>FROM {placeholder_for_table}<br><br>WHERE ServiceName = 'service_name' AND <br><br>tag->>’http.status_code’ = ‘500’<br><br>AND Timestamp >= NOW() - INTERVAL 1 HOUR<br><br>Limit 100;|SELECT startTime,<br><br>{TraceId_column},<br><br>{SpanId_column},<br><br>{ParentSpanId_coulmn},<br><br>{cloumn_name_1},<br><br>Duration,<br><br>FROM {placeholder_for_table}<br><br>WHERE {cloumn_name_1} = {column_value_1} AND <br><br>tag->>{key1} = {value}<br><br>AND Timestamp >= NOW() - INTERVAL 1 HOUR<br><br>Limit 100;|
|21|Complex Query with percentile, time operation and multiple filters|SELECT toStartOfInterval(toDateTime(Timestamp), INTERVAL 60 second) as time, ServiceName,<br><br>quantile(0.99)(Duration)/1000000 AS p99 FROM {placeholder_for_table} WHERE 1=1 AND Timestamp >= '1679994708'<br><br>AND Timestamp <= '1679998308' AND ( Timestamp >= toDateTime(intDiv(1679994708714,1000))<br><br>AND Timestamp <= toDateTime(intDiv(1679998308714,1000)) ) AND<br><br>ServiceName In ('accountingservice','adservice','cartservice','checkoutservice','currencyservice',<br><br>'emailservice','featureflagservice','frauddetectionservice','frontend','loadgenerator','paymentservice',<br><br>'recommendationservice','productcatalogservice','quoteservice','shippingservice','frontend-proxy','frontend-web')<br><br>AND ServiceName != 'loadgenerator' GROUP BY time, ServiceName ORDER BY time ASC LIMIT 100000|SELECT toStartOfInterval(toDateTime(Timestamp), INTERVAL 60 second) as time, {column_name_1},<br><br>quantile(0.99)(Duration)/1000000 AS p99 FROM {placeholder_for_table} WHERE 1=1 AND Timestamp >= '1679994708'<br><br>AND Timestamp <= '1679998308' AND ( Timestamp >= toDateTime(intDiv(1679994708714,1000))<br><br>AND Timestamp <= toDateTime(intDiv(1679998308714,1000)) ) AND<br><br>({column_name_1} In ({column_value_1},{column_value_2},{column_value_3},{column_value_4},{column_value_5}, {column_value_6},{column_value_7})<br><br>AND {column_name_1} != {column_value_8} GROUP BY time, {column_name_1} ORDER BY time ASC LIMIT 100000|
