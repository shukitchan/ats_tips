Setting up OpenTelemetry Collector to test ATS OpenTelemetry Tracer Plugin
==========================================================================
* This will expose local port 4318 for OTLP HTTP endpoint for sending data to the collector

## Setting up collector through docker
* Use the following configuration yaml file - config.yaml
```
exporters:
  debug:
    verbosity: detailed

receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
        cors:
          allowed_origins:
            - http://*

service:
  pipelines:
    traces:
      receivers:
      - otlp
      exporters:
      - debug
    logs:
      receivers:
      - otlp
      exporters:
      - debug
```
* Run a docker container for the collector
```
docker run --rm -it -p 4317:4317 -p 4318:4318 -v $(pwd)/config.yaml:/etc/otelcol-contrib/config.yaml otel/opentelemetry-collector-contrib:0.113.0
```


