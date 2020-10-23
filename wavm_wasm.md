Setting up ATS Development Environment for WASM plugin
======================================================

* Follow instructions in [here](vagrant.md) first to setup ATS development environment
* Install cmake 3.12.3

```
wget https://cmake.org/files/v3.12/cmake-3.12.3.tar.gz
tar zxvf cmake-3.12.3.tar.gz 
cd cmake-3.12.3/
./bootstrap --prefix=/usr/local
make -j4
sudo make install
cmake --version
 ```

* Install LLVM 9.0.0

```
wget https://apt.llvm.org/llvm.sh
sudo chmod +x llvm.sh 
sudo ./llvm.sh 9
```

* Follow instructions [here](https://github.com/jplevyak/trafficserver/blob/wasm/plugins/experimental/wasm/README.md) to emscripten and WAVM
* Get all the changes from this [branch](https://github.com/jplevyak/trafficserver/commits/wasm/plugins/experimental/wasm) to the latest ATS code. Tested against latest master at [this time](https://github.com/apache/trafficserver/commit/457261c836dc7d8a406dbdcc73afd2fdacbf4b57) 
* Now you can recompile the ATS project with experimental plugins options and get the WASM plugin after installation
* With this setup, you can also compile C++ program into wasm module. We can use the examples provided [here](https://github.com/jplevyak/trafficserver/tree/wasm/plugins/experimental/wasm/example/http)

```
make DOCKER_SDK=../.. -f Makefile.docker_cpp_builder
sudo cp http.wasm /usr/local/var/wasm/http.wasm
sudo cat "wasm.so /usr/local/var/wasm/http.wasm" >> /usr/local/etc/trafficserver/plugin.config
sudo /usr/local/bin/trafficserver restart
```
