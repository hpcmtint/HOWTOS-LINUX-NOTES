<pre>
1. Introduction
Insufficient system resources such as storage, memory, and CPU (Central Processing Unit) can greatly affect an application’s performance. Hence, monitoring these components is crucial.

Unlike disk and memory, monitoring the CPU usage on a Linux system isn’t as straightforward. In this article, we’ll look at how to interpret CPU metrics and display them in a human-readable format.

2. CPU Load vs. CPU Usage
Even though CPU load and CPU usage sound similar, they are not interchangeable. CPU load is defined as the number of processes using or waiting to use one core at a single point in time.

Let’s say we have a single-core system, and our CPU load average is always below 0.6. This indicates that every process that needs to use the CPU can use it instantly without waiting. If the CPU load average is above 1, this indicates that there are processes that need to use the CPU but cannot at the moment due to the unavailability of the CPU.

However, the load average above 1 in a multi-processor system won’t be an issue since more cores are readily available.

The uptime command gives us a view of the load average at 1, 5, and 15-minute intervals:

[root@localhost ~]# uptime
 12:40:05 up  2:29,  1 user,  load average: 0.37, 0.08, 0.03
Interpreting load average can’t be done without knowing the number of cores of a system:

[root@localhost ~]# cat /proc/cpuinfo |grep core
core id		: 0
cpu cores	: 1
 process non-idle tasks. CPU Usage can only be measured over a specified interval of time. We can determine the CPU usage by taking the percentage of time spent idling and subtracting it from 100.
3. Calculating CPU Usage
3.1. Getting CPU Usage Using vmstat
The vmstat command displays CPU activity in near-real time:

[root@localhost ~]# vmstat 3 4
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 4  0      0 1347080   6120 941464    0    0    68    11   72  137  1  2 97  0  0
 1  0      0 1347080   6120 941464    0    0     0     0   84  157  1  2 97  0  0
 1  0      0 1347080   6120 941464    0    0     0     0   59  107  1  1 98  0  0
 1  0      0 1347080   6120 941464    0    0     0     1   59  104  1  1 98  0  0
The columns under CPU provide an overview of where the processor time is spent:

us — time spent running non-kernel code
sy — time spent running kernel code
id — time spent idle
wa — time spent waiting for I/O
st — time is stolen from a virtual machine
The id column is what we’re interested in. With the delay of a second, we calculate the CPU usage using vmstat:

[root@localhost ~]# echo "CPU Usage: "$[100-$(vmstat 1 2|tail -1|awk '{print $15}')]"%"
CPU Usage: 2%
The vmstat command without any arguments provided will give CPU times since boot. This will not provide an accurate CPU usage percentage. Hence, the arguments can only be 1 and 2 where we take the metrics calculated after a second:

vmstat 1 2
3.2. Getting CPU Usage Using /proc/stat
CPU activity can also be extracted from the /proc/stat file. The file contains various metrics about the system since boot:

[root@localhost ~]# cat /proc/stat 
cpu  3020 28 1863 22404 35 432 47 0 0 0
cpu0 3020 28 1863 22404 35 432 47 0 0 0
intr 96468 28 100 0 0 0 0 0 0 1 0 0 0 1263 0 0 0 3696 0 153 928 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 207 0 41 14600 0 0 0 0 0 0 0 0 0 0 0 0 0 0 343 97 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
ctxt 340950
btime 1628404433
processes 3276
procs_running 2
procs_blocked 0
softirq 112867 1 16857 56 269 510 0 261 0 0 94913
The first line, ‘cpu’ is an aggregate of the metrics of all cores of the system. On a system with 4 cores, there would be 4 cpu lines — cpu0, cpu1, cpu2, and cpu3. The columns in the ‘cpu‘ row represent times spent processing different tasks:

user — time spent in user mode
nice — time spent processing nice processes in user mode
system — time spent executing kernel code
idle — time spent idle
iowait — time spent waiting for I/O
irq — time spent servicing interrupts
softirq — time spent servicing software interrupts
steal — time stolen from a virtual machine
guest — time spent running a virtual CPU for a guest operating system
guest_nice — time spent running a virtual CPU for a “niced” guest operating system
We’re going to use these metrics to calculate the average idle percentage. Subsequently, we’ll use the calculated value to work out the CPU usage. It’s important to note that older distributions of Linux don’t calculate the steal, guest, or guest_nice metrics. If we were using an older system, we’d omit those metrics in our calculation:

Average idle time (%) = (idle * 100) / (user + nice + system + idle + iowait + irq + softirq + steal + guest + guest_nice)

cat /proc/stat |grep cpu |tail -1|awk '{print ($5*100)/($2+$3+$4+$5+$6+$7+$8+$9+$10)}'|awk '{print "CPU Usage: " 100-$1}'
CPU Usage: 2.4219
Since we’re working on a single-core system, the ‘cpu’ line will be the same as ‘cpu1‘. Hence, the use of tail -1 is to retrieve only one of the lines. Yet, we’d use the ‘cpu‘ line on a multiprocessor system since it’s an aggregate of metrics on all cores.

3.3. Getting CPU Usage Using top
Generally, the top command is usually utilized to display active processes on a system and how much resources the processes are consuming. Nevertheless, we can use this command to measure the state of the CPU:

[root@localhost ~]# top
top - 07:08:31 up  2:41,  1 user,  load average: 0.00, 0.00, 0.00
Tasks: 322 total,   2 running, 320 sleeping,   0 stopped,   0 zombie
%Cpu(s): 10.0 us, 15.0 sy,  0.0 ni, 97.8 id,  0.0 wa,  5.0 hi,  0.0 si,  0.0 st
MiB Mem :   3709.4 total,   1483.1 free,   1402.0 used,    824.4 buff/cache
MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   2053.4 avail Mem 
Furthermore, it’s important to note that the top command displays the CPU percentage of a single core. In a multiprocessor system, it’s possible to have the CPU percentage exceed 100%. For example, if 4 cores are at 75%, the top command will show CPU as being 300%.

We need to obtain the value of the time spent idling so that we can subtract it from 100 to get the usage:

[root@localhost ~]# top -bn2 | grep '%Cpu' | tail -1 | grep -P '(....|...) id,'|awk '{print "CPU Usage: " 100-$8 "%"}'
CPU Usage: 2.2%
The -n option is the number of iterations the top command should use before ending. We avoid using the first loop because the metrics we retrieve will be values since boot. Hence, we’ve taken the second iteration.

Alternatively, in a multiprocessor system, we’d have to divide the given ‘id’ value by the number of cores and subtract the value from 100. For example, if we were operating on a quad-core system and the ‘id‘ value was 304%, we’d calculate our CPU usage as:

CPU Usage % = 100 — (304/4)

[root@localhost ~]# top -bn2 | grep '%Cpu' | tail -1 | grep -P '(....|...) id,'|awk '{print "CPU Usage: " 100-($8/4) "%"}'
4. Conclusion
In this article, we discussed the difference between CPU usage and CPU load. Many use these two concepts interchangeably, which is incorrect. After that, we delved into the various methods used to retrieve CPU utilization metrics.

We’ve also pointed out what to avoid for better accuracy.
</pre>
