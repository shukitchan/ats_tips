For ATS compiled with `-g` or with debug symbols installed, we can trace or debug with ebpf/bcc

Installation instructions - https://github.com/iovisor/bcc/blob/master/INSTALL.md#alpine---binary

e.g. 
1. Put ATS docker image on a host (e.g. rhel 8.5)
```
docker pull ghcr.io/shukitchan/ats-alpine-devel:latest
sudo docker run --rm -it --privileged \
  -v /lib/modules:/lib/modules:ro \
  -v /sys:/sys:ro \
  -v /usr/src:/usr/src:ro \
  ghcr.io/shukitchan/ats-alpine-devel
```

2. Install bcc
```
sudo apk add bcc-tools bcc-doc
```

3. Turn on xdebug plugin inside `plugin.config` and restart ATS
```
echo xdebug.so >> /opt/ats/etc/trafficserver/plugin.config
DISTRIB_ID=gentoo /opt/ats/bin/trafficserver restart
```

4. Open a separate terminal, go into the docker instance and trace the call for retrieving milestone
```
sudo docker exec -it <docker container id> /bin/bash
/usr/share/bcc/tools/trace ':/opt/ats/bin/traffic_server:TSHttpTxnMilestoneGet "Function called"'
```

5. Back to the original terminal, trigger the call to the ATS server and see the traces of the call for retrieving milesotne in the above terminal window
```
curl -v -H 'X-Debug: X-Milestones' 'http://localhost:8080/'
```
