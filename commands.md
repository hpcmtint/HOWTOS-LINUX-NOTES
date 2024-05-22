```bash
% uptime
% grep 'model name' /proc/cpuinfo | wc -l
% uptime
% stress --cpu 1 --timeout 600
# the `-d` parameter indicates highlighting the changed areas
% watch -d uptime
# `-P ALL` indicates monitoring all CPUs, and the number `5` after it indicates that a set of data will be output every 5 seconds.
% mpstat -P ALL 5
# output a set of data every 5 seconds
% pidstat -u 5 1
% stress -i 1 --timeout 600
% watch -d uptime
# display metrics for all CPUs and output a set of data every 5 seconds
% mpstat -P ALL 5 1
# output a set of data every 5 seconds, using the `-u` flag to display CPU metrics
% pidstat -u 5 1
% stress -c 8 --timeout 600
% uptime
# output a set of data every 5 seconds
% pidstat -u 5 1
```
