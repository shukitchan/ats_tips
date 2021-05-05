Tips on Generating GDB Stack Trace for cores from running ATS Lua script
========================================================================
* Tips on purposefully generating segfault with luajit - https://codegolf.stackexchange.com/questions/4399/shortest-code-that-raises-a-sigsegv?page=2&tab=votes
```
function do_remap()
    ts.debug('TEST')
    f=require"ffi"f.cdef"int puts()"f.C.puts()
end
```
* Turn off JIT for your lua script (I think this will help)
```
jit.off(true, true)

function do_remap()
    ts.debug('TEST')
    f=require"ffi"f.cdef"int puts()"f.C.puts()
end
```
* In ATS 8.0.x and above, Luajit is not part of the compiled binary. Make sure the luajit shared object is compiled with "-g" option. i.e. https://github.com/LuaJIT/LuaJIT/blob/v2.0/src/Makefile#L60-L62
* When using GDB, you can use "info sharedlibrary" to make sure all share libraries are loaded correctly. 
