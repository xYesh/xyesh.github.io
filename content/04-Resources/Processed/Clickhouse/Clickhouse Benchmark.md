---
{"tags":["Database/Clickhouse","Benchmark"],"projects":["EventStore"],"url":"https://clickhouse.com/docs/en/operations/utilities/clickhouse-benchmark","type":"Article","Description":"clickhouse read path benchmarking tool","Areas":null,"publish":true,"PassFrontmatter":true,"created":"2024-12-04T09:21:25.440+05:30","updated":"2024-12-26T09:11:27.261+05:30"}
---

# Cli command
```Shell
clickhouse-benchmark [keys] < queries_file;
```

# Parameters
- `--query=QUERY` — Query to execute. If this parameter is not passed, `clickhouse-benchmark` will read queries from standard input.
- `-c N`, `--concurrency=N` — Number of queries that `clickhouse-benchmark` sends simultaneously. Default value: 1.
- `-d N`, `--delay=N` — Interval in seconds between intermediate reports (to disable reports set 0). Default value: 1.
- `-h HOST`, `--host=HOST` — Server host. Default value: `localhost`. For the [comparison mode](https://clickhouse.com/docs/en/operations/utilities/clickhouse-benchmark#clickhouse-benchmark-comparison-mode) you can use multiple `-h` keys.
- `-i N`, `--iterations=N` — Total number of queries. Default value: 0 (repeat forever).
- `-r`, `--randomize` — Random order of queries execution if there is more than one input query.
- `-s`, `--secure` — Using `TLS` connection.
- `-t N`, `--timelimit=N` — Time limit in seconds. `clickhouse-benchmark` stops sending queries when the specified time limit is reached. Default value: 0 (time limit disabled).
- `--port=N` — Server port. Default value: 9000. For the [comparison mode](https://clickhouse.com/docs/en/operations/utilities/clickhouse-benchmark#clickhouse-benchmark-comparison-mode) you can use multiple `--port` keys.
- `--confidence=N` — Level of confidence for T-test. Possible values: 0 (80%), 1 (90%), 2 (95%), 3 (98%), 4 (99%), 5 (99.5%). Default value: 5. In the [comparison mode](https://clickhouse.com/docs/en/operations/utilities/clickhouse-benchmark#clickhouse-benchmark-comparison-mode) `clickhouse-benchmark` performs the [Independent two-sample Student’s t-test](https://en.wikipedia.org/wiki/Student%27s_t-test#Independent_two-sample_t-test) to determine whether the two distributions aren’t different with the selected level of confidence.
- `--cumulative` — Printing cumulative data instead of data per interval.
- `--database=DATABASE_NAME` — ClickHouse database name. Default value: `default`.
- `--user=USERNAME` — ClickHouse user name. Default value: `default`.
- `--password=PSWD` — ClickHouse user password. Default value: empty string.
- `--stacktrace` — Stack traces output. When the key is set, `clickhouse-bencmark` outputs stack traces of exceptions.
- `--stage=WORD` — Query processing stage at server. ClickHouse stops query processing and returns an answer to `clickhouse-benchmark` at the specified stage. Possible values: `complete`, `fetch_columns`, `with_mergeable_state`. Default value: `complete`.
- `--help` — Shows the help message.

If you want to apply some [settings](https://clickhouse.com/docs/en/operations/settings) for queries, pass them as a key `--<session setting name>= SETTING_VALUE`. For example, `--max_memory_usage=1048576`.

# Report details
In the report you can find:
- Number of queries in the `Queries executed:` field.
    
- Status string containing (in order):
    - Endpoint of ClickHouse server.
    - Number of processed queries.
    - QPS: How many queries the server performed per second during a period specified in the `--delay` argument.
    - RPS: How many rows the server reads per second during a period specified in the `--delay` argument.
    - MiB/s: How many mebibytes the server reads per second during a period specified in the `--delay` argument.
    - result RPS: How many rows placed by the server to the result of a query per second during a period specified in the `--delay` argument.
    - result MiB/s. How many mebibytes placed by the server to the result of a query per second during a period specified in the `--delay` argument.
- Percentiles of queries execution time.

