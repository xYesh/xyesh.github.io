---
{"aliases":null,"tags":null,"date created":"Sunday, December 22nd 2024, 7:50:35 pm","date modified":"Thursday, December 26th 2024, 12:15:58 pm","publish":true,"PassFrontmatter":true,"created":"2024-12-26T12:14:55.260+05:30","updated":"2025-01-09T21:13:26.435+05:30"}
---

# Description
- Make some enhancements to the existing [honeycombio/loadgen](https://github.com/honeycombio/loadgen) for some popular usecases
	- P1
		- [x] Generate IPs for some particular subnets
		- [/] Given a list of strings, generate attribute values from that. #dropped
	- P2
		- [x] Control the length of string along with the cardinality
- [ ] Prep the repository for a PR to upstream. 
- [ ] Create the upstream PR and request for reviews.
## Details
Repository - [xYesh/loadgen: A flexible command-line load generator to create traces in OTel format](https://github.com/xYesh/loadgen)
PR - 

# Scratch Pad
```shell
./loadgen --tracecount=1 --depth=1 --nspans=1 --sender=print --dataset=prodRepl fk.page.type=/ip10,10,10,10


go run . --tracecount=1 --depth=10 --nspans=20 --sender=print --dataset=prodRepl net.peer.name=/ip10,10,10,256 http.status_code=/st10,0.1 status.code=/i-1,1 net.peer.port=-1 fk.page.uri=/uq10,100000 fk.url=/u10,1000 flow=/u10,1000 originZone=/sw8 fk.page.type=/sq1000 apiMethod=/sw5000  http.method=/sq10 EngagementTime=/i0,1000 Uptime=/ir0,1000 FULLY_PAINTED_TIME=/ig0,1000 appName=/sw20 SessionInfo=/sa16 sessionCrashed=/b1 deviceUUID=/sxc16,5000000 SessionID=/sxc16,1000000 ABInfo=/sxc16,100000 AppVersion=/sq50 requestDomain=/u10,1 JS_Crash_Message=/sw50 MobileOSVersion=/sw10 deviceManufacturer=/sw10 deviceModel=/sw100 deviceType=/sw50 fk-client-ip=/uq50,10 CheckoutId=/s16 OrderId=/s16 EventId=/s16 CustomerId=/sxc16,500000 offerStatus=/sw100 x-user-agent=/sw100


go run . --tracecount=10 --depth=1 --nspans=1 --sender=print --dataset=prodRepl net.peer.ip=/ip2,2,10,256

./loadgen --tracecount=10 --depth=1 --nspans=1 --sender=print --dataset=prodRepl net.peer.ip=/ip2,2,10,256
```

# Task Management
### Tasks
```tasks
not done
tag includes #loadgen
```

### Achievements
```tasks
done
tag includes #loadgen
```

# Note Management
| File                                                                                                                                                             | Description                                                                     | tags                                                                                                                                            | Date                         |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| [[03-Projects/EventStore/Notes/Generating EventStore write traffic using Honeycombio loadgen\|Generating EventStore write traffic using Honeycombio loadgen]] | Generating Write path traffic with honeycombio for DT auto instrumentation load | <ul><li>Database/Clickhouse</li><li>Benchmark</li><li>O11y/DistributedTracing</li><li>O11y/AutoIntrumentation</li><li>O11y/Attributes</li></ul> | 10:28 AM - December 06, 2024 |


# Resource Management
| File                                                                                  | url                                                                                                               | Description                                         | type  | tags                                                                     | Date                        |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- | ----- | ------------------------------------------------------------------------ | --------------------------- |
| [[04-Resources/Processed/GoLang/Get started with GoLang\|Get started with GoLang]] | <ul><li>https://www.youtube.com/watch?v=yyUHQIec83I</li><li>https://www.youtube.com/watch?v=XCZWyN9ZbEQ</li></ul> | A Crash course on how to start understanding GoLang | Video | <ul><li>go</li><li>ProgrammingLanguages/go</li><li>CrashCourse</li></ul> | 1:20 PM - December 26, 2024 |

