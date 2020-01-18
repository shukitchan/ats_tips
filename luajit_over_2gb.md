Luajit with over 2GB
====================

* Use at least luajit-2.1.0 beta3 - https://github.com/LuaJIT/LuaJIT/releases/tag/v2.1.0-beta3
* Edit src/Makefile and uncomment line 114 - "#XCFLAGS+= -DLUAJIT_ENABLE_GC64"
* Run make
* Sample lua program to use over 2GB memory

```
cd trafficserver
vagrant up trusty_64
vagrant ssh trusty_64
```
