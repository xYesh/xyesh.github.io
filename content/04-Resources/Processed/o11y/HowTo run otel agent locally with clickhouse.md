---
{"aliases":null,"tags":["Database/Clickhouse","otel","docker"],"projects":["EventStore"],"url":null,"type":"Guide","Description":"Otel agent with clickhouse","Areas":"HowTos","date created":"2024-12-06T16:48","date modified":"2025-01-21T12:14","publish":true,"PassFrontmatter":true,"created":"2025-01-14T15:25:43.285+05:30","updated":"2025-01-21T12:14:34.741+05:30"}
---

# Docker Compose File
```DockerFile
version: '3.8'
services:
  clickhouse:
    image: clickhouse/clickhouse-server:23.12
    ports:
      - "8123:8123"
      - "9000:9000"
    volumes:
      - ./clickhouse-data:/Users/yeshwanth.samardhi/otel/ch
    networks:
      - otel_network
    environment:
      - CLICKHOUSE_DB=otel
      - CLICKHOUSE_USER=otel
      - CLICKHOUSE_PASSWORD=otelpassword

  otel-collector:
    image: otel/opentelemetry-collector-contrib:0.91.0
    volumes:
      - ./otel-config.yaml:/etc/otelcol-contrib/config.yaml
    networks:
      - otel_network
    ports:
      - "4317:4317"  # OTLP gRPC receiver
      - "4318:4318"  # OTLP HTTP receiver
    depends_on:
      - clickhouse

networks:
  otel_network:
    driver: bridge
```

# otel-config.yml
```otel-config.yml
receivers:
  otlp:
    protocols:
      grpc:
      http:

processors:
  batch:

exporters:
  clickhouse:
    endpoint: tcp://host.docker.internal:9000?dial_timeout=10s
    database: otel
    username: otel
    password: otelpassword
    logs_table_name: logs
    traces_table_name: traces
    metrics_table_name: metrics

service:
  pipelines:
    logs:
      receivers: [otlp]
      processors: [batch]
      exporters: [clickhouse]
    traces:
      receivers: [otlp]
      processors: [batch]
      exporters: [clickhouse]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [clickhouse]
```

# Commands to Run
```Shell
docker-compose up -d
```

# Python Script to Send Some Traces to Test
```Python
import time
import random
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.sdk.resources import Resource, SERVICE_NAME

def setup_tracer():
    """
    Set up the OpenTelemetry tracer with OTLP exporter
    """
    # Define service resource
    resource = Resource(attributes={
        SERVICE_NAME: "demo-service"
    })

    # Create TracerProvider
    provider = TracerProvider(resource=resource)

    # Create OTLP Span Exporter
    otlp_exporter = OTLPSpanExporter(
        endpoint="localhost:4317",
        insecure=True  # Use for local development
    )

    # Add BatchSpanProcessor to the TracerProvider
    provider.add_span_processor(BatchSpanProcessor(otlp_exporter))

    # Set the global TracerProvider
    trace.set_tracer_provider(provider)

    # Create and return a tracer
    return trace.get_tracer(__name__)

def simulate_user_service(tracer):
    """
    Simulate a simple user service with tracing
    """
    # Start a parent span for the entire user service operation
    with tracer.start_as_current_span("user_service_operation") as parent_span:
        # Simulate user registration process
        with tracer.start_as_current_span("user_registration") as registration_span:
            # Simulate some work
            time.sleep(random.uniform(0.1, 0.5))
            registration_span.set_attribute("user.id", f"user_{random.randint(1000, 9999)}")
            registration_span.set_attribute("registration.method", "email")
			# Simulate user authentication
        with tracer.start_as_current_span("user_authentication") as auth_span:
            # Simulate authentication process
            time.sleep(random.uniform(0.1, 0.3))
            auth_status = random.choice(["success", "failure"])
            auth_span.set_attribute("auth.status", auth_status)
            auth_span.set_attribute("auth.method", "password")

            # Simulate a potential error condition
            if auth_status == "failure":
                auth_span.record_exception(Exception("Authentication failed"))

        # Simulate database interaction
        with tracer.start_as_current_span("database_operation") as db_span:
            # Simulate database query
            time.sleep(random.uniform(0.2, 0.6))
            db_span.set_attribute("db.system", "postgresql")
            db_span.set_attribute("db.operation", "insert")

def main():
    # Setup tracer
    tracer = setup_tracer()

    # Simulate traces for a certain duration
    print("Sending trace data to OpenTelemetry Collector…")
    try:
        # Run for 5 minutes
        end_time = time.time() + 300  # 5 minutes
        while time.time() < end_time:
            simulate_user_service(tracer)
            # Wait between trace generations
            time.sleep(random.uniform(1, 3))

    except KeyboardInterrupt:
        print("\nTrace generation stopped.")

if __name__ == "__main__":
    main()
```
