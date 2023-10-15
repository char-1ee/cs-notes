# Linux Load Averages

Linux load averages, aka System load average, is a set of 3 numbers that provides information about system's workload over a specific period of time. In details, 3 numbers represent the _average number of processes in the run queue_ (waiting for CPU time) over the last **1min, 5mins, 15mins** time intervals.&#x20;

```sh
$ uptime
 00:17:25 up 55 min,  0 users,  load average: 1.07, 2.05, 1.53
```

* A higher load average indicates the system is under heavier load (more processes are competing for CPU time).
* If load average == 0.0, meaning the system is idle.
* If 1min load average higher than 5mins/15mins load average, the workload is increasing.
* If 1min load average lower than 5mins/15mins load average, the workload is decreasing.
* If load average is consistently higher than the number of CPU cores on the system, tyhere might be a resource bottleneck.&#x20;
