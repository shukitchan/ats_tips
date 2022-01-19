Tips on Generating JIT Trace from running ATS Lua script
========================================================
* Add the following to the top of the lua script file
```
require("jit.v").start('/tmp/jv.txt')
```
* And you can find JIT trace in /tmp/jv.txt
* The multi-threaded ATS process can cause the traces from multiple requests to overlap each other. In that case, we can change ATS to run on one thread while testing the script with the trace on. 
