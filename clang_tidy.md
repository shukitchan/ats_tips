clang_tidy can report problems while writing ATS plugins

To install clang_tidy
```
apk add clang-extra-tools
```

E.g. run
```
clang-tidy -checks=*,-fuchsia-default-arguments-calls,-readability-redundant-string-init,-*decay,-llvmlibc*,-modernize-use-trailing-return-type,-*vararg /work/plugins/experimental/wasm/wasm_main.cc -- -I ./include/ -I ./plugins/experimental/wasm/lib/ -I ./lib/yamlcpp/include/ -x c++ -std=c++17
```
