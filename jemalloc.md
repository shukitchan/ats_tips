Using jemalloc with ATS without Re-compiling the Binary
=======================================================

First we need to make some changes to records.config

```
CONFIG proxy.config.bin_path STRING /usr/local/bin
CONFIG proxy.config.proxy_binary STRING start_ats.sh
```

Then we need to create a file in /usr/local/bin/start_ats.sh

```
#!/bin/sh
env LD_PRELOAD="/usr/local/lib/libjemalloc.so.1"
/home/y/bin64/traffic_server "$@"
```

You can in fact use this trick to pre-load any libraries you want for running traffic server
