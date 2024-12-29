---
{"aliases":null,"tags":["Database/Clickhouse","Benchmark","O11y/DistributedTracing","O11y/AutoIntrumentation","O11y/Attributes"],"projects":["EventStore","loadgen"],"type":"Note","Description":"Generating Write path traffic with honeycombio for DT auto instrumentation load","Areas":null,"publish":true,"date created":"Friday, December 6th 2024, 10:28:39 am","date modified":"Wednesday, December 18th 2024, 10:25:11 pm","PassFrontmatter":true,"created":"2024-12-06T10:28:39.070+05:30","updated":"2024-12-27T18:21:58.454+05:30"}
---



# Honeycomb Traces Generator evaluation

- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Commands\|Commands]]
	- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Commands\|Basic Script That Generates 100 Traces per Second for 10 Seconds]]
		- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Basic Script That Generates 100 Traces per Second for 10 Seconds\|Data in ClickHouse]]
	- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Commands\|Script with Custom spanAttributes Added to it]]
		- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Script with Custom spanAttributes Added to it\|Data in Clickhouse]]
- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Generator Details\|Generator Details]]
	- [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen#Generator Details\|Test Script with print the traces to local]]



[[03-Projects/EventStore/Notes/attachments/Diagram - loadgen-setup\|Diagram - loadgen-setup]]

### Commands
#### Basic Script That Generates 100 Traces per Second for 10 Seconds
```Shell
./loadgen --tps=100 --depth=10 --nspans=10 --extra=8 --runtime=10s --ramptime=5s --sender=otel --insecure --host=localhost:4317
```

##### Data in ClickHouse
```json
"Timestamp": "2024-12-09 02:59:01.119417000",
"TraceId": "eaabcb311030f2f513e49ceed5fd11c5",
"SpanId": "146e7331c23d2e3d",
"ParentSpanId": "f31d8bb11a464d72",
"TraceState": "",
"SpanName": "wasabi",
"SpanKind": "SPAN_KIND_INTERNAL",
"ServiceName": "generatorTest",
"ResourceAttributes": {"service.version":"unknown","telemetry.sdk.language":"go","telemetry.sdk.name":"otelconfig","telemetry.sdk.version":"1.17.0","host.name":"BLRETV-V72JHX3G.local","service.name":"generatorTest"},
"ScopeName": "loadgen",
"ScopeVersion": "dev",
"SpanAttributes": {"small-ship":"true","early-horn":"32","hard-leg":"10187.073664152671","low-needle":"false","process_id":"47819","clear-horn":"0.9111312770436745","free-comb":"38","clear-ticket":"0.9597385593732547","whole-drawer":"8321d0c2bc55a888"},
"Duration": "511857375",
"StatusCode": "STATUS_CODE_UNSET",
"StatusMessage": "",
"Events.Timestamp": ["2024-12-09 02:59:01.119443000"],
"Events.Name": ["exception"],
"Events.Attributes": [{"exception.type":"error","exception.message":"error message","exception.stacktrace":"stacktrace","exception.escaped":"false"}],
"Links.TraceId": [],
"Links.SpanId": [],
"Links.TraceState": [],
"Links.Attributes": []
```

#### Script with Custom spanAttributes Added to it
```shell
./loadgen --tps=100 --depth=10 --nspans=10 --extra=8 --runtime=10s --ramptime=5s --sender=otel --insecure --host=localhost:4317 --dataset=generatorTest product=/sa discount=/b20 price=/fg100,50
```

##### Data in Clickhouse
```json
"Timestamp": "2024-12-09 03:40:32.810734000",
"TraceId": "fba470055ed0e606bccdea7112ce1e24",
"SpanId": "baba30b516af0c72",
"ParentSpanId": "89e05f9f751e9f1a",
"TraceState": "",
"SpanName": "coriander",
"SpanKind": "SPAN_KIND_INTERNAL",
"ServiceName": "generatorTest",
"ResourceAttributes": {"host.name":"BLRETV-V72JHX3G.local","service.name":"generatorTest","service.version":"unknown","telemetry.sdk.language":"go","telemetry.sdk.name":"otelconfig","telemetry.sdk.version":"1.17.0"},
"ScopeName": "loadgen",
"ScopeVersion": "dev",
"SpanAttributes": {"early-horn":"79","price":"0","clear-ticket":"0.9673112665661086","process_id":"59917","free-comb":"77","small-ship":"true","whole-drawer":"5344d1da62db0c38","hard-leg":"9532.70196019242","clear-horn":"0.11460945733595683","low-needle":"true","product":"xzynghiozkgggnjj","discount":"false"},
"Duration": "27313709",
"StatusCode": "STATUS_CODE_UNSET",
"StatusMessage": "",
"Events.Timestamp": [],
"Events.Name": [],
"Events.Attributes": [],
"Links.TraceId": [],
"Links.SpanId": [],
"Links.TraceState": [],
"Links.Attributes": []
```

### Generator Details

[GitHub - honeycombio/loadgen: A flexible command-line load generator to create traces in OTel or Honeycomb formats](https://github.com/honeycombio/loadgen?tab=readme-ov-file#generators)

| type  | description                                              | p1                          | p2                           |
| ----- | -------------------------------------------------------- | --------------------------- | ---------------------------- |
| i, ir | rectangularly distributed integers                       | min (0)                     | max (100)                    |
| ig    | gaussian integers                                        | mean (100)                  | stddev (10)                  |
| f, fr | rectangularly distributed floats                         | min (0)                     | max (100)                    |
| fg    | gaussian floats                                          | mean (100)                  | stddev (10)                  |
| b     | boolean                                                  | percentage true (50)        |                              |
| s, sa | alphabetic string                                        | length in chars (16)        |                              |
| sw    | pronounceable words, rectangular distribution            | cardinality (16)            |                              |
| sq    | pronounceable words, quadratic distribution              | cardinality (16)            |                              |
| sx    | hexadecimal string                                       | length in chars (16)        |                              |
| k     | key fields used for testing intermittent key cardinality | cardinality (50)            | period (60)                  |
| u     | url-like (2 parts)                                       | cardinality of 1st part (3) | cardinality of 2nd part (10) |
| uq    | url with random query                                    | cardinality of 1st part (3) | cardinality of 2nd part (10) |
| st    | status code                                              | percentage of 400s          | percentage of 500s           |

#### Test Script with print the traces to local
```shell
./loadgen --tracecount=3 --depth=10 --nspans=10 --extra=8 --sender=print --dataset=generatorTest product=/sa discount=/b20 price=/fg100,50

./loadgen --tracecount=100 --depth=10 --nspans=20 --sender=print --dataset=prodRepl fk.page.type=/sq100000

./loadgen --tracecount=10 --depth=1 --nspans=1 --sender=print --dataset=prodRepl fk.page.type=/sq5
```
# Data shape

### ARB[^1] data

| Distributed Tracing                      |                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------- |
| Ingestion throughput                     | 1-2 M spans/sec (per DC) BAU<br><br>2-4 M spans/sec (per DC) BBD |
| Number of spans in a Trace               | ~ 100-200                                                        |
| # of attributes in a span                | ~ 30-50                                                          |
| Max Depth of Trace                       | 10-20                                                            |
| Span Payload size                        | 5KB avg (Max 50KB)                                               |
| Retention                                | 1 month                                                          |
| Read throughput                          | 15 rps avg (Max 100 rps)                                         |
| Data Freshness                           | ~ 1-2 min (apart from tail)                                      |
| Query Latency<br><br>(Primary queries)   | 10s                                                              |
| Query Latency<br><br>(Secondary queries) | ~ 1-2 min                                                        |
| # of call Graphs/flows in FK             | 10 critical user path flows                                      |
| # of services in a trace                 | lower double digits                                              |
| Max Cardinality of a span                | lower double digits                                              |
| Cardinality of span attributes           |                                                                  |

## Span / Custom attributes
#### References - 
- Asterix - [Jiro Bundles](http://jiro.fkcloud.in/admin/config/asterix/zone/in-hyderabad-1/bundles/20)
- RUM - [New Relic](https://one.newrelic.com/data-exploration/data-explorer/explorer?account=3700279&duration=1800000&state=06b1df31-cc06-595e-7ca2-dacb64cfb07d)
- DT - [UAR Traces](https://confluence.fkinternal.com/display/MTL/Attribute+Details+of+UAR+Traces)

| Source  | Attribute Name     | Attribute Type | Example                                                                                                     | Cardinality | Example data                                             |
| ------- | ------------------ | -------------- | ----------------------------------------------------------------------------------------------------------- | ----------- | -------------------------------------------------------- |
| UAR/DT  | status.code        | int            | 0                                                                                                           |             | 0                                                        |
| UAR/DT  | originZone         | String         | "in-hyderabad-1"                                                                                            | Low         | federal-wall                                             |
| UAR/DT  | net.peer.port      | int            | -1                                                                                                          |             | -1                                                       |
| UAR/DT  | net.peer.name      | ip             | 10.24.15.135                                                                                                |             | TODO: can't generate IPs                                 |
| UAR/DT  | http.status_code   | int            | 200                                                                                                         |             | 200                                                      |
| UAR/DT  | http.method        | String         | "POST"                                                                                                      | Low         | important-office                                         |
| UAR/DT  | flow               | url            | "/4/page/fetch/productPage"                                                                                 | Low         | https://example.com/pipe/bad-garden                      |
| UAR/DT  | fk.url             | url            | "/4/page/fetch"                                                                                             | Low         | https://example.com/leaf/right-dress                     |
| UAR/DT  | fk.page.uri        | url            | "/%20/p/%20?pid=TSHGFF84YKTPQN5E"                                                                           | High        | https://example.com/apple/good-curtain?extra=yzquuuvort  |
| UAR/DT  | fk.page.type       | String         | productPage                                                                                                 | Low         | sure-pocket                                              |
| UAR/DT  | apiMethod          | String         | "com.flipkart.astra.server.resource.CustomAstraResource.fetchWidget"                                        | Low         | best-mouth                                               |
| RUM     | sessionCrashed     | Boolean        | true                                                                                                        |             | false                                                    |
| RUM     | requestDomain      | url            | www.Flipkart.com                                                                                            | Low         | https://example.com/spring/har                           |
| RUM     | deviceUUID         | UUID           |                                                                                                             | High        | mdwgnagpfmoamsqg                                         |
| RUM     | deviceType         | String         | Mobile / Tablet / random string                                                                             | Low         | white-cloud                                              |
| RUM     | deviceModel        | String         |                                                                                                             | Low         | good-parcel                                              |
| RUM     | deviceManufacturer | String         |                                                                                                             | Low         | international-bath                                       |
| RUM     | appName            | String         | fkAndroid                                                                                                   |             | long-stem                                                |
| RUM     | Uptime             | int            | 300                                                                                                         |             | 2                                                        |
| RUM     | SessionInfo        | String         | UUID + Some string                                                                                          |             | lwrkasqreuvgtqus                                         |
| RUM     | SessionID          | UUID           |                                                                                                             | High        | xzdjzwfexerpxzwp                                         |
| RUM     | MobileOSVersion    | String         | 8.0.1                                                                                                       | Low         | national-net                                             |
| RUM     | JS_Crash_Message   | String         | WIDGET_RENDERING_ERROR_ATLAS_WIDGET_default_fk_hp_tab_marketplace_wo-grocery_1: undefined is not a function | Low         | clear-whistle                                            |
| RUM     | FULLY_PAINTED_TIME | int            | 1234                                                                                                        |             | 61                                                       |
| RUM     | EngagementTime     | int            | 2                                                                                                           |             | 510                                                      |
| RUM     | AppVersion         | String         | 8.13                                                                                                        | Low         | local-thread                                             |
| RUM     | ABInfo             | UUID           |                                                                                                             | High        | olnboqdxjwqqiagz                                         |
| Asterix | x-user-agent       | String         |                                                                                                             | Low         | military-cow                                             |
| Asterix | offerStatus        | String         |                                                                                                             | Low         | social-tongue                                            |
| Asterix | fk-client-ip       | ip             |                                                                                                             |             | <br>https://example.com/berry/clear?extra=nzuaavygnj<br> |
| Asterix | OrderId            | UUID           |                                                                                                             | High        | sxtxmskimjoxwlcf                                         |
| Asterix | EventId            | UUID           |                                                                                                             | High        | axbozvceuoelubnu                                         |
| Asterix | CustomerId         | UUID           |                                                                                                             | High        | sparjemvtpuvullb                                         |
| Asterix | CheckoutId         | UUID           |                                                                                                             | High        | thffmmjrqigzylvh                                         |

#### Loadgen script with above parameters
```shell
./loadgen --tracecount=1 --depth=10 --nspans=20 --sender=print --dataset=prodRepl net.peer.name=0.0.0.0 http.status_code=/st10,0.1 status.code=/i-1,1 net.peer.port=-1 fk.page.uri=/uq10,100000 fk.url=/u10,1000 flow=/u10,1000 originZone=/sw8 fk.page.type=/sq1000 apiMethod=/sw5000  http.method=/sq10 EngagementTime=/i0,1000 Uptime=/ir0,1000 FULLY_PAINTED_TIME=/ig0,1000 appName=/sw20 SessionInfo=/sa16 sessionCrashed=/b1 deviceUUID=/s16 SessionID=/s16 ABInfo=/s16 AppVersion=/sq50 requestDomain=/u10,1 JS_Crash_Message=/sw50 MobileOSVersion=/sw10 deviceManufacturer=/sw10 deviceModel=/sw100 deviceType=/sw50 fk-client-ip=/uq50,10 CheckoutId=/s16 OrderId=/s16 EventId=/s16 CustomerId=/s16 offerStatus=/sw100 x-user-agent=/sw100
```

##### Example data
```
bay - T:10e9f9 S:d701 P     start:12:58:52.740 end:12:58:53.051 map[ABInfo:olnboqdxjwqqiagz AppVersion:local-thread CheckoutId:thffmmjrqigzylvh CustomerId:sparjemvtpuvullb EngagementTime:510 EventId:axbozvceuoelubnu FULLY_PAINTED_TIME:-866 JS_Crash_Message:clear-whistle MobileOSVersion:national-net OrderId:sxtxmskimjoxwlcf SessionID:xzdjzwfexerpxzwp SessionInfo:lwrkasqreuvgtqus Uptime:2 apiMethod:best-mouth appName:long-stem count:1 deviceManufacturer:international-bath deviceModel:good-parcel deviceType:white-cloud deviceUUID:mdwgnagpfmoamsqg fk-client-ip:https://example.com/berry/clear?extra=nzuaavygnj fk.page.type:sure-pocket fk.page.uri:https://example.com/apple/good-curtain?extra=yzquuuvort fk.url:https://example.com/leaf/right-dress flow:https://example.com/pipe/bad-garden http.method:important-office http.status_code:200 net.peer.name:0.0.0.0 net.peer.port:-1 offerStatus:social-tongue originZone:federal-wall process_id:36584 requestDomain:https://example.com/spring/hard sessionCrashed:false status.code:0 x-user-agent:military-cow]
```

# Limitations of Honeycombio/Loadgen
- Does not have a way to generate the following details
	- IPs
	- UUIDs with cardinality
	- Combination of static content + Generator.


# DT Auto instrumented attributes

# Auto-Instrumentation Attributes in OpenTelemetry

|             |                              |                                                          |                                  |                    |                                          |
| ----------- | ---------------------------- | -------------------------------------------------------- | -------------------------------- | ------------------ | ---------------------------------------- |
| Type        | Attribute Name               | Example                                                  | Expected Cardinality             | Expected Data Type | Description                              |
| General     | otel.library.name            | "io.opentelemetry"                                       | Low                              | string             | Name of the instrumentation library.     |
| General     | otel.library.version         | "1.8.0"                                                  | Low                              | string             | Version of the instrumentation library.  |
| General     | otel.scope.name              | "http.server"                                            | Medium                           | string             | Name of the scope creating telemetry.    |
| General     | otel.scope.version           | "1.2.0"                                                  | Low                              | string             | Version of the scope creating telemetry. |
| HTTP        | http.method                  | "GET", "POST"                                            | Low (fixed set of methods)       | string             | The HTTP method used.                    |
| HTTP        | http.url                     | "[https://example.com/users](https://example.com/users)" | High (varies per request)        | string             | The full URL of the request.             |
| HTTP        | http.target                  | "/users?id=123"                                          | High (varies per request)        | string             | The path and query string of the URL.    |
| HTTP        | http.host                    | "[example.com](http://example.com):443"                  | Medium (depends on host setup)   | string             | The hostname and port of the URL.        |
| HTTP        | http.scheme                  | "https"                                                  | Low (fixed set of schemes)       | string             | The protocol scheme.                     |
| HTTP        | http.status_code             | 200, 404                                                 | Low (fixed set of status codes)  | integer            | The HTTP response status code.           |
| HTTP        | http.request_content_length  | 512                                                      | High                             | integer            | Size of the request payload in bytes.    |
| HTTP        | http.response_content_length | 2048                                                     | High                             | integer            | Size of the response payload in bytes.   |
| HTTP        | net.peer.ip                  | "192.168.1.1"                                            | High (varies per client/server)  | string             | IP address of the client/server.         |
| HTTP        | net.peer.port                | 443                                                      | Low (fixed set of ports)         | integer            | Port number of the client/server.        |
| DB          | db.system                    | "mysql", "postgresql"                                    | Low (fixed set of systems)       | string             | The database management system.          |
| DB          | db.user                      | "admin"                                                  | Medium (limited number of users) | string             | The database user.                       |
| DB          | db.name                      | "user_database"                                          | Medium (depends on setup)        | string             | The name of the database.                |
| DB          | db.statement                 | "SELECT * FROM users WHERE id = ?"                       | High                             | string             | The database query being executed.       |
| DB          | db.operation                 | "SELECT", "INSERT"                                       | Low (fixed set of operations)    | string             | The high-level operation being executed. |
| DB          | net.peer.name                | "[db.example.com](http://db.example.com)"                | Medium                           | string             | Hostname of the database server.         |
| DB          | net.peer.ip                  | "192.168.1.2"                                            | Medium                           | string             | IP address of the database server.       |
| DB          | net.peer.port                | 5432                                                     | Low (fixed set of ports)         | integer            | Port number of the database server.      |
| Messaging   | messaging.system             | "kafka", "rabbitmq"                                      | Low (fixed set of systems)       | string             | The type of messaging system.            |
| Messaging   | messaging.destination        | "user-updates"                                           | Medium                           | string             | The name of the message queue or topic.  |
| Messaging   | messaging.destination_kind   | "queue", "topic"                                         | Low (fixed set of types)         | string             | The type of destination.                 |
| Messaging   | messaging.message_id         | "abc123"                                                 | High                             | string             | A unique identifier for the message.     |
| Messaging   | messaging.operation          | "send", "receive", "process"                             | Low (fixed set of operations)    | string             | The operation being performed.           |
| CDN         | cache.hit                    | true, false                                              | Low                              | boolean            | Indicates if a cache hit occurred.       |
| CDN         | cache.key                    | "user:1234"                                              | High                             | string             | The key used in the cache operation.     |
| CDN         | cache.expiration             | "600" (seconds)                                          | Medium                           | integer            | Expiration time of the cache entry.      |
| Process     | process.runtime.name         | "java", "nodejs"                                         | Low                              | string             | Name of the runtime used.                |
| Process     | process.runtime.version      | "11.0.8", "14.17.0"                                      | Low                              | string             | Version of the runtime.                  |
| Process     | process.pid                  | 12345                                                    | High                             | integer            | Process ID of the application.           |
| File System | file.path                    | "/var/log/app.log"                                       | High                             | string             | Path of the file being accessed.         |
| File System | file.operation               | "read", "write"                                          | Low                              | string             | Type of file operation.                  |
| File System | file.size                    | 1024                                                     | High                             | integer            | Size of the file in bytes.               |
| Event       | event.name                   | "user-login", "error"                                    | High                             | string             | Name of the event.                       |
| Event       | event.domain                 | "authentication", "http"                                 | Medium                           | string             | Domain of the event.                     |
| Event       | event.time                   | "2024-12-24T12:00:00Z"                                   | High                             | string             | Timestamp of the event.                  |

[^1]: Architecture Review Board
