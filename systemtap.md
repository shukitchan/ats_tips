For ATS compiled with systemtap support - https://docs.trafficserver.apache.org/en/latest/developer-guide/debugging/debug-builds.en.html#systemtap-and-dtrace-support , we can write debug script 

e.g. ats_plugin_mutex.stp - 
```
#!/usr/bin/env stap
#
# ats_plugin_mutex.stp - debug mutex usage in plugin
#
# Print the mutex pointer being locked and unlocked by Apache Traffic
# Server plugins.
#
# Tested with systemtap 4.3
#
# For more sytemtap example on ATS, please check out -
#  https://github.com/wikimedia/puppet/tree/production/modules/trafficserver/files
#
probe process("/usr/local/bin/traffic_server").function("TSMutexLock") {
    printf("TSMutexLock called: %d\n", $mutexp);
}

probe process("/usr/local/bin/traffic_server").function("TSMutexUnlock") {
    printf("TSMutextUnlock called: %d\n", $mutexp);
}
```
