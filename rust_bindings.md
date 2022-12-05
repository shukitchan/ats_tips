(Moved to [ATS wasm plugin](https://github.com/apache/trafficserver/tree/master/plugins/experimental/wasm)) Rust bindings for TrafficServer - https://github.com/netlify/trafficserver_rs

```
git clone https://github.com/netlify/trafficserver_rs
cd trafficserver_rs
cargo build 
cd examples/remap-example
cargo build 
sudo cp target/debug/libremap-example.so /usr/local/libexec/trafficserver/
```
