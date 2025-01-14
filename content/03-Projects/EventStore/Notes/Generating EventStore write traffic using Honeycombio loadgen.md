---
{"aliases":null,"tags":["Database/Clickhouse","Benchmark","O11y/DistributedTracing","O11y/AutoIntrumentation","O11y/Attributes"],"projects":["EventStore","loadgen"],"type":"Note","Description":"Generating Write path traffic with honeycombio for DT auto instrumentation load","Areas":null,"publish":true,"date created":"2024-12-06T10:28","date modified":"2025-01-14T17:30","PassFrontmatter":true,"created":"2025-01-14T15:25:43.341+05:30","updated":"2025-01-14T17:30:46.126+05:30"}
---


**Table of contents**
- [Honeycomb Traces Generator Evaluation](#Honeycomb%20Traces%20Generator%20Evaluation)
		- [Commands](#Commands)
			- [Basic Script That Generates 100 Traces per Second for 10 Seconds](#Basic%20Script%20That%20Generates%20100%20Traces%20per%20Second%20for%2010%20Seconds)
				- [Data in ClickHouse](#Data%20in%20ClickHouse)
			- [Script with Custom spanAttributes Added to it](#Script%20with%20Custom%20spanAttributes%20Added%20to%20it)
				- [Data in Clickhouse](#Data%20in%20Clickhouse)
		- [Generator Details](#Generator%20Details)
			- [Test Script with Print the Traces to Local](#Test%20Script%20with%20Print%20the%20Traces%20to%20Local)
- [Data Shape](#Data%20Shape)
		- [ARB[^1] Data](#ARB%5B%5E1%5D%20Data)
	- [Span / Custom Attributes](#Span%20/%20Custom%20Attributes)
			- [References -](#References%20-)
			- [Loadgen Script with Above Parameters](#Loadgen%20Script%20with%20Above%20Parameters)
				- [Example Data](#Example%20Data)
- [Limitations of Honeycombio/Loadgen](#Limitations%20of%20Honeycombio/Loadgen)
- [DT Auto Instrumented Attributes](#DT%20Auto%20Instrumented%20Attributes)
	- [Auto-Instrumentation Attributes in OpenTelemetry](#Auto-Instrumentation%20Attributes%20in%20OpenTelemetry)
- [Loadgen Format](#Loadgen%20Format)
	- [Example Loadgen Config](#Example%20Loadgen%20Config)
	- [Example data](#Example%20data)

# Honeycomb Traces Generator Evaluation

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

#### Test Script with Print the Traces to Local
```shell
./loadgen --tracecount=3 --depth=10 --nspans=10 --extra=8 --sender=print --dataset=generatorTest product=/sa discount=/b20 price=/fg100,50

./loadgen --tracecount=100 --depth=10 --nspans=20 --sender=print --dataset=prodRepl fk.page.type=/sq100000

./loadgen --tracecount=1 --depth=1 --nspans=1 --sender=print --dataset=prodRepl fk.page.type=/sq5

./loadgen --tracecount=1 --depth=1 --nspans=1 --sender=print --dataset=prodRepl fk.page.type=/sxc5,15
```
# Data Shape

### ARB[^1] Data

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

## Span / Custom Attributes
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
| UAR/DT  | flow               | url            | "/4/page/fetch/productPage"                                                                                 | Low         | <https://example.com/pipe/bad-garden>                      |
| UAR/DT  | fk.url             | url            | "/4/page/fetch"                                                                                             | Low         | <https://example.com/leaf/right-dress>                     |
| UAR/DT  | fk.page.uri        | url            | "/%20/p/%20?pid=TSHGFF84YKTPQN5E"                                                                           | High        | <https://example.com/apple/good-curtain?extra=yzquuuvort>  |
| UAR/DT  | fk.page.type       | String         | productPage                                                                                                 | Low         | sure-pocket                                              |
| UAR/DT  | apiMethod          | String         | "com.flipkart.astra.server.resource.CustomAstraResource.fetchWidget"                                        | Low         | best-mouth                                               |
| RUM     | sessionCrashed     | Boolean        | true                                                                                                        |             | false                                                    |
| RUM     | requestDomain      | url            | <www.Flipkart.com>                                                                                            | Low         | <https://example.com/spring/har>                           |
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
| Asterix | fk-client-ip       | ip             |                                                                                                             |             | <br><https://example.com/berry/clear?extra=nzuaavygnj><br> |
| Asterix | OrderId            | UUID           |                                                                                                             | High        | sxtxmskimjoxwlcf                                         |
| Asterix | EventId            | UUID           |                                                                                                             | High        | axbozvceuoelubnu                                         |
| Asterix | CustomerId         | UUID           |                                                                                                             | High        | sparjemvtpuvullb                                         |
| Asterix | CheckoutId         | UUID           |                                                                                                             | High        | thffmmjrqigzylvh                                         |

#### Loadgen Script with Above Parameters
```shell
./loadgen --tracecount=1 --depth=10 --nspans=20 --sender=print --dataset=prodRepl net.peer.name=0.0.0.0 http.status_code=/st10,0.1 status.code=/i-1,1 net.peer.port=-1 fk.page.uri=/uq10,100000 fk.url=/u10,1000 flow=/u10,1000 originZone=/sw8 fk.page.type=/sq1000 apiMethod=/sw5000  http.method=/sq10 EngagementTime=/i0,1000 Uptime=/ir0,1000 FULLY_PAINTED_TIME=/ig0,1000 appName=/sw20 SessionInfo=/sa16 sessionCrashed=/b1 deviceUUID=/s16 SessionID=/s16 ABInfo=/s16 AppVersion=/sq50 requestDomain=/u10,1 JS_Crash_Message=/sw50 MobileOSVersion=/sw10 deviceManufacturer=/sw10 deviceModel=/sw100 deviceType=/sw50 fk-client-ip=/uq50,10 CheckoutId=/s16 OrderId=/s16 EventId=/s16 CustomerId=/s16 offerStatus=/sw100 x-user-agent=/sw100
```

##### Example Data
```
bay - T:10e9f9 S:d701 P     start:12:58:52.740 end:12:58:53.051 map[ABInfo:olnboqdxjwqqiagz AppVersion:local-thread CheckoutId:thffmmjrqigzylvh CustomerId:sparjemvtpuvullb EngagementTime:510 EventId:axbozvceuoelubnu FULLY_PAINTED_TIME:-866 JS_Crash_Message:clear-whistle MobileOSVersion:national-net OrderId:sxtxmskimjoxwlcf SessionID:xzdjzwfexerpxzwp SessionInfo:lwrkasqreuvgtqus Uptime:2 apiMethod:best-mouth appName:long-stem count:1 deviceManufacturer:international-bath deviceModel:good-parcel deviceType:white-cloud deviceUUID:mdwgnagpfmoamsqg fk-client-ip:https://example.com/berry/clear?extra=nzuaavygnj fk.page.type:sure-pocket fk.page.uri:https://example.com/apple/good-curtain?extra=yzquuuvort fk.url:https://example.com/leaf/right-dress flow:https://example.com/pipe/bad-garden http.method:important-office http.status_code:200 net.peer.name:0.0.0.0 net.peer.port:-1 offerStatus:social-tongue originZone:federal-wall process_id:36584 requestDomain:https://example.com/spring/hard sessionCrashed:false status.code:0 x-user-agent:military-cow]
```

# Limitations of Honeycombio/Loadgen
- Does not have a way to generate the following details
	- IPs
	- UUIDs with cardinality
	- Combination of static content + Generator.


# DT Auto Instrumented Attributes
## Auto-Instrumentation Attributes in OpenTelemetry

| Type        | Attribute Name               | loadgen<br>config | Example                                                  | Expected Cardinality             | Expected Data Type | Description                              |
| ----------- | ---------------------------- | ----------------- | -------------------------------------------------------- | -------------------------------- | ------------------ | ---------------------------------------- |
| General     | otel.library.name            | io.opentelemetry  | "io.opentelemetry"                                       | Low                              | string             | Name of the instrumentation library.     |
| General     | otel.library.version         | /sq10             | "1.8.0"                                                  | Low                              | string             | Version of the instrumentation library.  |
| General     | otel.scope.name              | /sw500            | "http.server"                                            | Medium                           | string             | Name of the scope creating telemetry.    |
| General     | otel.scope.version           | /sq10             | "1.2.0"                                                  | Low                              | string             | Version of the scope creating telemetry. |
| HTTP        | http.method                  | /sq8              | "GET", "POST"                                            | Low (fixed set of methods)       | string             | The HTTP method used.                    |
| HTTP        | http.url                     | /u10,1000         | "[https://example.com/users](https://example.com/users)" | High (varies per request)        | string             | The full URL of the request.             |
| HTTP        | http.target                  | /uq10,1000        | "/users?id=123"                                          | High (varies per request)        | string             | The path and query string of the URL.    |
| HTTP        | http.host                    | /u                | "[example.com](http://example.com):443"                  | Medium (depends on host setup)   | string             | The hostname and port of the URL.        |
| HTTP        | http.scheme                  | /sq10             | "https"                                                  | Low (fixed set of schemes)       | string             | The protocol scheme.                     |
| HTTP        | http.status_code             | /st1,0.1          | 200, 404                                                 | Low (fixed set of status codes)  | integer            | The HTTP response status code.           |
| HTTP        | http.request_content_length  | /ir0,1000         | 512                                                      | High                             | integer            | Size of the request payload in bytes.    |
| HTTP        | http.response_content_length | /ir0,10000        | 2048                                                     | High                             | integer            | Size of the response payload in bytes.   |
| HTTP        | net.peer.ip                  | /ip2,2,10,256     | "192.168.1.1"                                            | High (varies per client/server)  | string             | IP address of the client/server.         |
| HTTP        | net.peer.port                | /i                | 443                                                      | Low (fixed set of ports)         | integer            | Port number of the client/server.        |
| DB          | db.system                    | /sw10             | "mysql", "postgresql"                                    | Low (fixed set of systems)       | string             | The database management system.          |
| DB          | db.user                      | /sw10000          | "admin"                                                  | Medium (limited number of users) | string             | The database user.                       |
| DB          | db.name                      | /sw10000          | "user_database"                                          | Medium (depends on setup)        | string             | The name of the database.                |
| DB          | db.statement                 | /uq10,10000       | "SELECT * FROM users WHERE id = ?"                       | High                             | string             | The database query being executed.       |
| DB          | db.operation                 | /sw10             | "SELECT", "INSERT"                                       | Low (fixed set of operations)    | string             | The high-level operation being executed. |
| DB          | net.peer.name                | /u                | "[db.example.com](http://db.example.com)"                | Medium                           | string             | Hostname of the database server.         |
| Messaging   | messaging.system             | /sw10             | "kafka", "rabbitmq"                                      | Low (fixed set of systems)       | string             | The type of messaging system.            |
| Messaging   | messaging.destination        | /sw1000           | "user-updates"                                           | Medium                           | string             | The name of the message queue or topic.  |
| Messaging   | messaging.destination_kind   | /sw10             | "queue", "topic"                                         | Low (fixed set of types)         | string             | The type of destination.                 |
| Messaging   | messaging.message_id         | /sxc10,1000       | "abc123"                                                 | High                             | string             | A unique identifier for the message.     |
| Messaging   | messaging.operation          | /sw10             | "send", "receive", "process"                             | Low (fixed set of operations)    | string             | The operation being performed.           |
| CDN         | cache.hit                    | /b                | true, false                                              | Low                              | boolean            | Indicates if a cache hit occurred.       |
| CDN         | cache.key                    | /sxc10,1000       | "user:1234"                                              | High                             | string             | The key used in the cache operation.     |
| CDN         | cache.expiration             | /ir600            | "600" (seconds)                                          | Medium                           | integer            | Expiration time of the cache entry.      |
| Process     | process.runtime.name         | /sw10             | "java", "nodejs"                                         | Low                              | string             | Name of the runtime used.                |
| Process     | process.runtime.version      | /sw50             | "11.0.8", "14.17.0"                                      | Low                              | string             | Version of the runtime.                  |
| Process     | process.pid                  | /i50000           | 12345                                                    | High                             | integer            | Process ID of the application.           |
| File System | file.path                    | /uq10,10000       | "/var/log/app.log"                                       | High                             | string             | Path of the file being accessed.         |
| File System | file.operation               | /sw10             | "read", "write"                                          | Low                              | string             | Type of file operation.                  |
| File System | file.size                    | /ig2048,50        | 1024                                                     | High                             | integer            | Size of the file in bytes.               |
| Event       | event.name                   | /sw10000          | "user-login", "error"                                    | High                             | string             | Name of the event.                       |
| Event       | event.domain                 | /sw1000           | "authentication", "http"                                 | Medium                           | string             | Domain of the event.                     |

# Loadgen Final Format for DT
## Example Loadgen Config
```yaml
telemetry:
    host: http://otel-collector-pulsar-ch.observability:4318/v1/traces
    insecure: true
    dataset: generatorTest

format:
    depth: 20
    nspans: 150
    extra: 50
    tracetime: 1s

quantity:
    tps: 100000
    runtime: 24h
    ramptime: 5s

output:
    sender: print
    protocol: grpc

global:
    loglevel: warn

fields:
    otel.library.name: io.opentelemetry
    otel.library.version: /sq10
    otel.scope.name: /sw500
    otel.scope.version: /sq10
    http.method: /sq8
    http.url: /u10,1000
    http.target: /uq10,1000
    http.host: /u
    http.scheme: /sq10
    http.status_code: /st1,0.1
    http.request_content_length: /ir0,1000
    http.response_content_length: /ir0,10000
    net.peer.ip: /ip2,2,10,256
    net.peer.port: /i
    db.system: /sw10
    db.user: /sw10000
    db.name: /sw10000
    db.statement: /uq10,10000
    db.operation: /sw10
    net.peer.name: /u
    messaging.system: /sw10
    messaging.destination: /sw1000
    messaging.destination_kind: /sw10
    messaging.message_id: /sxc10,1000
    messaging.operation: /sw10
    cache.hit: /b
    cache.key: /sxc10,1000
    cache.expiration: /ir600
    process.runtime.name: /sw10
    process.runtime.version: /sw50
    process.pid: /i50000
    file.path: /uq10,10000
    file.operation: /sw10
    file.size: /ig2048,50
    event.name: /sw10000
    event.domain: /sw1000
```

## Example Data
```
allspice - T:1f2d74 S:0281 P20e2 start:18:03:41.667 end:18:03:41.682 map[able-ticket:-96 best-cushion:d6df6de121c9a6f0 cache.expiration:496 cache.hit:false cache.key:f952eeda3e certain-sun:jj clear-store:-3 db.name:great-knot db.operation:only-town db.statement:https://example.com/ring/social-leaf?extra=xfwlydopdi db.system:late-chain db.user:easy-branch early-horn:hiir-a6262c20-fjgs early-spade:47 easy-comb:79 event.domain:clear-cheese event.name:only-ship federal-jewel:true federal-plane:jdlqbprigz file.operation:best-fish file.path:https://example.com/plate/national-ant?extra=xpwaaoztfg file.size:2085 free-ticket:80 full-nail:true full-pig:22 good-berry:56 good-knee:-68 good-line:0.7338457151522857 great-wire:uljneknhuh hard-berry:53 hard-orange:37 hard-throat:832.3887070377575 high-floor:2e757d7da40ecf59 high-net:0.8473846411319103 http.host:https://example.com/drop/large http.method:political-skirt http.request_content_length:119 http.response_content_length:1664 http.scheme:sure-finger http.status_code:200 http.target:https://example.com/eye/hard-plough?extra=kirbmrgdtj http.url:https://example.com/muscle/high-flag human-skirt:495.5855841694738 human-stick:730.5876208275826 important-boot:31 important-screw:0.28879545537594087 international-cat:40 local-collar:sdnt-f68086f4-ppye local-island:0.46699546883306065 local-rail:12121.810129287027 low-box:kp low-fowl:840.9503995599574 messaging.destination:federal-spoon messaging.destination_kind:clear-nail messaging.message_id:b91cf0687d messaging.operation:possible-star messaging.system:recent-cake military-army:false military-bed:-5 military-net:894.4957908877157 national-scissors:0.6722464985634898 net.peer.ip:0.1.9.90 net.peer.name:https://example.com/moon/special net.peer.port:88 new-wire:true otel.library.name:io.opentelemetry otel.library.version:important-hammer otel.scope.name:full-umbrella otel.scope.version:different-office other-hand:53 other-leaf:df6ff751aa87afc6 political-basket:64 political-potato:38 process.pid:48665 process.runtime.name:real-dress process.runtime.version:federal-wire process_id:52106 public-town:11218.97922437933 real-cheese:925.2928275230162 real-fish:8 real-island:24.0815123544348 small-pin:true social-spoon:true strong-plate:zidsx sure-floor:793.01014109755 white-eye:384.8473997771901 whole-spade:axwpqkomdb]
```

[^1]: Architecture Review Board
