Setting up OpenTelemetry Collector to test ATS OpenTelemetry Tracer Plugin
==========================================================================
* Either one of the following will expose local port 4318 for OTLP HTTP endpoint for sending data to the collector

## Setting up collector through docker
* Use the following configuration yaml file - config.yaml
```
exporters:
  logging:
    loglevel: DEBUG
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
        cors_allowed_origins:
        - '*'
service:
  pipelines:
    traces:
      receivers:
      - otlp
      exporters:
      - logging
    logs:
      receivers:
      - otlp
      exporters:
      - logging
```
* Run a docker container for the collector
```
docker run --rm -it -p 4317:4317 -p 4318:4318 -v $(pwd):/cfg otel/opentelemetry-collector:0.38.0 --config=/cfg/config.yaml
```

## Setting up collector + Jaeger through docker compose
* Use the following configuration yaml for the collection - collector-gateway.yaml
```
receivers:
 otlp:
   protocols:
     http:
       endpoint: 0.0.0.0:4318
     grpc:
       endpoint: 0.0.0.0:4317
processors:
 batch:
   timeout: 1s
 resource:
   attributes:
     - key: test.key
       value: "test-value"
       action: insert
exporters:
 logging:
   loglevel: info
 jaeger:
   endpoint: jaeger-all-in-one:14250
   insecure: true
service:
 pipelines:
   traces:
     receivers: [otlp]
     processors: [batch, resource]
     exporters: [logging, jaeger]
```
* Use the following Docker-compose.yaml
```
version: "2"
services:
 # Jaeger
 jaeger-all-in-one:
   image: jaegertracing/all-in-one:latest
   ports:
     - "16686:16686"
     - "14268"
     - "14250"
 # Collector
 collector-gateway:
   image: otel/opentelemetry-collector:0.29.0
   volumes:
     - ./collector-gateway.yaml:/etc/collector-gateway.yaml
   command: [ "--config=/etc/collector-gateway.yaml" ]
   ports:
     - "1888:1888"   # pprof extension
     - "13133:13133" # health_check extension
     - "4317:4317"        # OTLP gRPC receiver
     - "4318:4318"        # OTLP HTTP receiver
     - "55670:55679" # zpages extension
   depends_on:
     - jaeger-all-in-one
``` 
* Run docker compose
```
docker compose up
```
