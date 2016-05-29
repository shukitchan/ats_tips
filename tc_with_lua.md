Traffic Control with ATS and Lua
================================

Here is an example of simple traffic control using ATS, Lua and tc.

First, lets start fresh by removing all qdisc

```
-bash-4.1$ sudo tc qdisc del dev eth0 root
```

We will then add a priority qdisc

```
-bash-4.1$ sudo tc qdisc add dev eth0 root handle 1: prio
```

By default, there are 3 bands to this PRIO class. Lets add the network emulator class to the 3rd band. It will add a
200ms delay

```
-bash-4.1$ sudo tc qdisc add dev eth0 parent 1:3 handle 30: netem  delay 200ms 
```

We now add filter so that any request with IP TOS equal to 0x38 will be sent to the 3rd band

```
-bash-4.1$ sudo tc filter add dev eth0 protocol ip parent 1:0 prio 3 u32 match ip tos 0x38 0xff flowid 1:3
```

We also want to make sure all TCP traffic (i.e. protocol 6) is sent to the first band

```
-bash-4.1$ sudo tc filter add dev eth0 protocol ip parent 1:0 prio 4 u32 match ip protocol 6 0xff flowid 1:1 
```

Next we need to make sure we have a lua script to set the IP TOS

```
-- script is /usr/local/var/lua/tos.lua
function do_remap()
    ts.http.client_packet_tos_set(56) -- 56 is 0x38
    ts.http.server_packet_tos_set(56) -- 56 is 0x38
    return 0
end
```

We make sure that this script is used in the rules we want in remap.config

```
map http://test1.com/ http://test.com/ @plugin=tslua.so @pparam=/usr/local/var/lua/tos.lua
map http://test2.com/ http://test.com/
```

Now requests for test1.com will have a 400ms (200ms for client and 200ms for origin) delay while requests for test2.com
will not be affected.

Finally here are some links for reference
* [Linux Advanced Routing and Traffic Control](http://lartc.org/) – general info on tc
* [netem](http://www.linuxfoundation.org/collaborate/workgroups/networking/netem) – general info on network emulator
* [Using TCPDUMP to filter on DSCP](http://mccltd.net/blog/?p=1199) – general info on debugging IP tos/dscp
* [ATS Traffic Shaping](https://cwiki.apache.org/confluence/display/TS/Traffic+Shaping) – general info on traffic shaping for ATS
