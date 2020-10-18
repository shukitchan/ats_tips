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
* Get all the changes from this [branch](https://github.com/jplevyak/trafficserver/commits/wasm/plugins/experimental/wasm) to the latest ATS code. 
* Now you can recompile the ATS project and get the WASM plugin as well.
