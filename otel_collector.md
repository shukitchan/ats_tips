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
## Setting up environment to compile ATS OpenTelemetry Tracer Plugin on alpine (3.20.3)
* Update apk repositories in `/etc/apk/repositories` and comment out all lines
* Run `apk update`
* Run the following lines
```
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/main abseil-cpp-dev
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/community libgpr=1.62.1-r1
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/community libgrpc=1.62.1-r1
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/main libprotobuf=24.4-r3
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/main libprotoc=24.4-r3
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/main protobuf-dev=24.4-r3
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/community grpc-cpp
apk add -X http://dl-cdn.alpinelinux.org/alpine/edge/testing opentelemetry-cpp-dev
```
* restore the file `/etc/apk/repositories` and rerun `apk update`


