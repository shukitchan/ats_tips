Detecting Dead Code in Lua
==========================

We can run luacov (https://github.com/lunarmodules/luacov) in production in over a period of time and retrieve the code coverage information. 
This will then show the code that never gets run (i.e. dead code)
These are potential candidates to be removed as tech debt.

Installing luacov on Alpine Linux
```
apk add luarocks
apk add luarocks5.1
luarocks install luacov
```
We need to make changes to `/usr/local/share/lua/5.1/luacov/defaults.lua`
First we change the location of the stats and report file 
```
  --- Filename to store collected stats. Default: "luacov.stats.out".
  statsfile = "/tmp/luacov.stats.out",

  --- Filename to store report. Default: "luacov.report.out".
  reportfile = "/tmp/luacov.report.out",
```
We need to turn on `tick`
```
   tick = true,
```
We may also need to adjust `savestepsize` to determine how frequent the stats file is updated.

Example program
```
ts.add_package_path('/usr/local/share/lua/5.1/?.lua')
require("luacov")

function send_response()
    ts.client_response.header['Rhost'] = ts.ctx['rhost']
    return 0
end

function do_remap()
    local req_host = ts.client_request.header.Host
    ts.ctx['rhost'] = string.reverse(req_host)
    ts.hook(TS_LUA_HOOK_SEND_RESPONSE_HDR, send_response)
    return 0
end

```
We can then run `luacov` in `/tmp/` directory to retrieve the output and see which lines of code were never executed.

* However, this may be very inefficient for the code running in production and we may need a way to turn off luacov in runtime and only allow it to run in a very small percentage of the time. 
