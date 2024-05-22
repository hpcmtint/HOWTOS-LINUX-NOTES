Whenever we notice the system slowing down, our typical first step is to execute the `top` or `uptime` command to understand the system’s load. For example, as shown below, I entered the `uptime` command in the command line, and the system promptly provided the result.

```
<span id="4478" data-selectable-paragraph=""><span>$ </span><span><span>uptime</span></span><br>02:34:03 up 2 days, 20:14,  1 user,  load average: 0.63, 0.83, 0.88</span>
```

But do you really know the meaning of each column in the output?

I believe you are familiar with the first few columns, which represent the current time, system uptime, and the number of logged-in users.

```
<span id="ec6e" data-selectable-paragraph=""><span>02</span>:<span>34</span>:<span>03</span>              <br>up <span>2</span> days, <span>20</span>:<span>14</span>      <br><span>1</span> user                </span>
```

The last three numbers represent the average load over the last 1 minute, 5 minutes, and 15 minutes respectively.

Average load? This term may be familiar yet unfamiliar to many people. We mention it in our daily work, but do you really understand its meaning? If an intern in your team asks you to explain what average load is, could you do it?

Today, I’ll teach you how to observe and understand this most common and important system metric.

You might think average load is just the CPU usage rate per unit time, right? For example, a load average of 0.63 means a CPU usage rate of 63%. Actually, it’s not like that. If you have time, you can use the command `man uptime` to learn more about the detailed explanation of average load.

In simple terms, average load refers to the average number of processes in the runnable or uninterruptible state over a unit of time. This is also known as the average number of active processes, and it’s not directly related to CPU usage. Let me explain the terms “runnable” and “uninterruptible” states.

Runnable processes are those that are either using the CPU or waiting for CPU time. These are the processes you typically see with the `ps` command, in the R state (Running or Runnable).

Uninterruptible processes are those in a critical kernel process and cannot be interrupted. The most common example is processes waiting for I/O responses from hardware devices, which are seen in the D state (Uninterruptible Sleep or Disk Sleep) with the `ps` command.

For example, when a process is reading or writing data to a disk, it cannot be interrupted until it receives a response from the disk to ensure data consistency. If the process were to be interrupted during this time, it could lead to inconsistent data between the disk and the process.

Therefore, the uninterruptible state is actually a protection mechanism for processes and hardware devices by the system.

In essence, average load is the average number of active processes. It’s the average number of active processes over a unit of time, but it’s actually an exponentially decaying average of active processes. The detailed meaning of “exponentially decaying average” isn’t important for your understanding; it’s just a faster way for the system to calculate. You can simply think of it as the average number of active processes.

Since the average is the number of active processes, the ideal scenario would be to have exactly one process running on each CPU, so that each CPU is fully utilized. For example, what does it mean when the average load is 2?

-   On a system with only 2 CPUs, it means that all CPUs are fully occupied.
-   On a system with 4 CPUs, it means that there is 50% idle CPU capacity.
-   And on a system with only 1 CPU, it means that half of the processes are competing for CPU time.

## What is a reasonable average load?

After explaining what average load is, let’s return to the example we started with. Can you judge from the output of the `uptime` command what range of average load values would indicate high system load, or conversely, very low system load?

We know that the ideal situation for average load is when it equals the number of CPUs. So, when evaluating average load, the first thing you need to know is how many CPUs the system has, which can be obtained from the `top` command or by reading the file `/proc/cpuinfo`, for example:

```
<span id="0909" data-selectable-paragraph=""><span>$ </span><span>grep <span>'model name'</span> /proc/cpuinfo | <span>wc</span> -l</span><br>2</span>
```

Once we have the number of CPUs, we can determine that when the average load exceeds the number of CPUs, the system is overloaded.

However, there’s another question: which of the three average load values should we reference?

In fact, we should consider all of them. The three different time interval averages provide us with data sources to analyze the trend of system load, allowing us to more comprehensively and three-dimensionally understand the current load situation.

-   If the values for 1 minute, 5 minutes, and 15 minutes are similar or not significantly different, it indicates that the system load is stable.
-   If the 1-minute value is much lower than the 15-minute value, it indicates that the load in the last minute has decreased, while there has been a significant load over the past 15 minutes.
-   Conversely, if the 1-minute value is much higher than the 15-minute value, it indicates that the load in the last minute has increased. This increase may be temporary or may continue to increase, so continuous observation is necessary. Once the 1-minute average load approaches or exceeds the number of CPUs, it means that the system is experiencing overload. At this point, the cause of the problem needs to be analyzed and optimized.

Here’s another example: suppose we see average loads of 1.73, 0.60, and 7.98 on a single CPU system. This indicates that the system was overloaded by 73% in the past minute and by 698% in the past 15 minutes. However, the overall trend shows a decrease in system load.

In a real production environment, at what level of average load should we pay attention?

In my opinion, when the average load exceeds 70% of the number of CPUs, you should analyze and investigate the high load issues. High load can cause process responses to slow down, thereby affecting the normal functioning of services.

However, the 70% figure is not absolute. The recommended approach is to monitor the average system load and judge the trend of the load based on more historical data. When you notice a significant increase in load, such as a doubling of the load, that’s the time to analyze and investigate.

## Average load vs. CPU usage

In real-world scenarios, we often easily confuse average load and CPU usage, so here, I’ll make a distinction.

You might wonder, since average load represents the number of active processes, doesn’t a high average load imply high CPU usage?

Let’s revisit the definition of average load. It refers to the number of processes in the runnable and uninterruptible state per unit of time. Therefore, it includes not only processes actively using the CPU but also those waiting for the CPU and waiting for I/O.

CPU usage, on the other hand, is a statistic of how busy the CPU is during a unit of time and doesn’t necessarily correspond directly to the average load. For example:

-   CPU-intensive processes that use a lot of CPU will increase the average load, in which case the two metrics are aligned.
-   I/O-bound processes that are waiting for I/O can increase the average load, but the CPU usage may not be very high.
-   A large number of processes waiting for CPU scheduling can also increase the average load, and in this case, the CPU usage will also be relatively high.

## Case study: understanding average load

Here, we’ll look at three examples to understand these three situations, and we’ll use tools like `iostat`, `mpstat`, `pidstat`, etc., to identify the root causes of the increased average load.

Because the case studies are based on operations on the machine, it’s best not just to listen and watch, but also to follow along and perform the actual operations with me.

The cases below are all based on Ubuntu 18.04 but are also applicable to other Linux systems. The environment I’m using for the cases is as follows:

-   Machine configuration: 2 CPUs, 8GB RAM.
-   Pre-installed packages: stress and sysstat, installed using `apt install stress sysstat`.

Here, I’ll briefly introduce stress and sysstat.

stress is a Linux system stress testing tool that we use here to simulate a scenario where the average load increases due to abnormal processes.

sysstat contains commonly used Linux performance tools for monitoring and analyzing system performance. We will use two commands from this package: mpstat and pidstat.

-   mpstat is a common tool for analyzing multi-core CPU performance, used to view real-time performance metrics for each CPU and the average metrics for all CPUs.
-   pidstat is a common tool for analyzing process performance, used to view real-time performance metrics for CPU, memory, I/O, and context switches for processes.

Additionally, each scenario requires you to open three terminals and log in to the same Linux machine.

Before starting the experiment, make sure you have prepared as described above. If there are any issues with package installation, you can try to solve them by Googling first. If you still can’t solve them, leave a message for me in the comment section, as this should not be difficult.

Also, please note that all the commands below are assumed to be run as the root user. So, if you are logged in as a regular user, be sure to first run the command `sudo su root` to switch to the root user.

If you have completed the above requirements, you can start by using the `uptime` command to see the average load before testing:

```
<span id="1c0e" data-selectable-paragraph=""><span>$ </span><span><span>uptime</span></span><br>...,  load average: 0.11, 0.15, 0.09</span>
```

## Scenario one: CPU-intensive process

First, we run the `stress` command in the first terminal to simulate a scenario where the CPU usage is 100%:

```
<span id="56d9" data-selectable-paragraph=""><span>$ </span><span>stress --cpu 1 --<span>timeout</span> 600</span></span>
```

Next, in the second terminal, run `uptime` to view the changes in average load:

```
<span id="be2f" data-selectable-paragraph=""><br>$ watch -d <span>uptime</span><br>...,  load average: 1.00, 0.75, 0.39</span>
```

Finally, in the third terminal, run `mpstat` to observe the changes in CPU usage:

```
<span id="498b" data-selectable-paragraph=""><br>$ mpstat -P ALL 5<br>Linux 4.15.0 (ubuntu) 09/22/18 _x86_64_ (2 CPU)<br>13:30:06     CPU    %usr   %<span>nice</span>    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle<br>13:30:11     all   50.05    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00   49.95<br>13:30:11       0    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00<br>13:30:11       1  100.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00</span>
```

From terminal two, you can see that the average load increases slowly to 1.00 over one minute. From terminal three, you can see that one CPU is indeed at 100% usage, but its iowait is 0. This indicates that the increase in average load is due to the CPU being at 100% usage.

So, which process is causing the CPU usage to be 100%? You can use pidstat to find out:

```
<span id="c815" data-selectable-paragraph=""><br>$ pidstat -u 5 1<br>13:37:07      UID       PID    %usr %system  %guest   %<span>wait</span>    %CPU   CPU  Command<br>13:37:12        0      2962  100.00    0.00    0.00    0.00  100.00     1  stress</span>
```

From here, it is clear that the `stress` process has a CPU usage of 100%.

## Scenario two: I/O-intensive process

First, we still run the `stress` command, but this time we simulate I/O pressure by continuously executing `sync`:

```
<span id="b5a8" data-selectable-paragraph=""><span>$ </span><span>stress -i 1 --<span>timeout</span> 600</span></span>
```

Again, in the second terminal, run `uptime` to observe the changes in the average load:

```
<span id="c8d5" data-selectable-paragraph=""><span>$ </span><span>watch -d <span>uptime</span></span><br>...,  load average: 1.06, 0.58, 0.37</span>
```

Then, in the third terminal, run `mpstat` to observe the changes in CPU usage:

```
<span id="197b" data-selectable-paragraph=""><br>$ mpstat -P ALL 5 1<br>Linux 4.15.0 (ubuntu)     09/22/18     _x86_64_    (2 CPU)<br>13:41:28     CPU    %usr   %<span>nice</span>    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle<br>13:41:33     all    0.21    0.00   12.07   32.67    0.00    0.21    0.00    0.00    0.00   54.84<br>13:41:33       0    0.43    0.00   23.87   67.53    0.00    0.43    0.00    0.00    0.00    7.74<br>13:41:33       1    0.00    0.00    0.81    0.20    0.00    0.00    0.00    0.00</span>
```

From here, you can see that the average load increases slowly to 1.06 over one minute. One CPU’s system CPU usage rises to 23.87%, and the iowait reaches 67.53%. This indicates that the increase in average load is due to the rise in iowait.

So, which process is causing such a high iowait? Let’s use pidstat to find out:

```
<span id="61da" data-selectable-paragraph=""><br>$ pidstat -u 5 1<br>Linux 4.15.0 (ubuntu)     09/22/18     _x86_64_    (2 CPU)<br>13:42:08      UID       PID    %usr %system  %guest   %<span>wait</span>    %CPU   CPU  Command<br>13:42:13        0       104    0.00    3.39    0.00    0.00    3.39     1  kworker/1:1H<br>13:42:13        0       109    0.00    0.40    0.00    0.00    0.40     0  kworker/0:1H<br>13:42:13        0      2997    2.00   35.53    0.00    3.99   37.52     1  stress<br>13:42:13        0      3057    0.00    0.40    0.00    0.00    0.40     0  pidstat</span>
```

It can be observed that it is still caused by the `stress` process.

## Scenario three: scenario with a large number of processes

When the number of running processes in a system exceeds the CPU’s processing capacity, processes waiting for the CPU will occur.

For example, we still use `stress`, but this time we simulate 8 processes:

```
<span id="6f56" data-selectable-paragraph=""><span>$ </span><span>stress -c 8 --<span>timeout</span> 600</span></span>
```

Since the system only has 2 CPUs, which is significantly less than the 8 processes, the system’s CPUs are severely overloaded, with an average load of 7.97:

```
<span id="a214" data-selectable-paragraph=""><span>$ </span><span><span>uptime</span></span><br>...,  load average: 7.97, 5.93, 3.02</span>
```

Then, run `pidstat` again to see the situation of the processes:

```
<span id="9971" data-selectable-paragraph=""><br>$ pidstat -u 5 1<br>14:23:25      UID       PID    %usr %system  %guest   %<span>wait</span>    %CPU   CPU  Command<br>14:23:30        0      3190   25.00    0.00    0.00   74.80   25.00     0  stress<br>14:23:30        0      3191   25.00    0.00    0.00   75.20   25.00     0  stress<br>14:23:30        0      3192   25.00    0.00    0.00   74.80   25.00     1  stress<br>14:23:30        0      3193   25.00    0.00    0.00   75.00   25.00     1  stress<br>14:23:30        0      3194   24.80    0.00    0.00   74.60   24.80     0  stress<br>14:23:30        0      3195   24.80    0.00    0.00   75.00   24.80     0  stress<br>14:23:30        0      3196   24.80    0.00    0.00   74.60   24.80     1  stress<br>14:23:30        0      3197   24.80    0.00    0.00   74.80   24.80     1  stress<br>14:23:30        0      3200    0.00    0.20    0.00    0.20    0.20     0  pidstat</span>
```

It can be seen that the 8 processes are contending for 2 CPUs, with each process waiting for the CPU for up to 75% of the time (indicated by the `%wait` column in the output). These processes, exceeding the CPU’s computational capacity, ultimately lead to CPU overload.

## Summary

After analyzing these three cases, let me summarize the understanding of average load.

Average load provides a quick way to assess the overall performance of a system, reflecting the overall load situation. However, just looking at the average load itself does not directly reveal where the bottleneck is. Therefore, when understanding the average load, it is also important to note:

-   A high average load is likely caused by CPU-intensive processes.
-   A high average load does not necessarily mean high CPU usage; it could also indicate increased I/O activity.
-   When you find a high load, you can use tools like mpstat, pidstat, etc., to assist in analyzing the source of the load.
