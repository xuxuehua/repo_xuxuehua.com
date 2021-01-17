---
title: "dmesg"
date: 2021-01-15 17:39
---
[toc]



# dmesg



## -T/--ctime                

show human-readable timestamp (may be inaccurate!)

```
$ dmesg -T | grep influx
[Fri Jan 15 08:35:34 2021] [31117]   499 31117 72953617 14801701  113518     282        0             0 influxd
[Fri Jan 15 08:35:34 2021] Memory cgroup out of memory: Kill process 31117 (influxd) score 1137 or sacrifice child
[Fri Jan 15 08:35:34 2021] Killed process 31117 (influxd) total-vm:291814468kB, anon-rss:51956420kB, file-rss:7251624kB, shmem-rss:0kB
[Fri Jan 15 08:35:38 2021] oom_reaper: reaped process 31117 (influxd), now anon-rss:0kB, file-rss:0kB, shmem-rss:0kB
[Fri Jan 15 09:00:12 2021] influxd invoked oom-killer: gfp_mask=0x14200ca(GFP_HIGHUSER_MOVABLE), nodemask=(null),  order=0, oom_score_adj=0
[Fri Jan 15 09:00:12 2021] influxd cpuset=6bbf4ac705f9b92d928815f8367f8598215a20ebf985ae6aa4a98ac5f92d65cf mems_allowed=0
[Fri Jan 15 09:00:12 2021] CPU: 5 PID: 7266 Comm: influxd Not tainted 4.14.146-119.123.amzn2.x86_64 #1
[Fri Jan 15 09:00:12 2021] [ 7030]   499  7030 73002208 14803448  114042     282        0             0 influxd
[Fri Jan 15 09:00:12 2021] Memory cgroup out of memory: Kill process 7030 (influxd) score 1138 or sacrifice child
[Fri Jan 15 09:00:12 2021] Killed process 7030 (influxd) total-vm:292008832kB, anon-rss:51955292kB, file-rss:7259412kB, shmem-rss:0kB
[Fri Jan 15 09:00:16 2021] oom_reaper: reaped process 7030 (influxd), now anon-rss:0kB, file-rss:0kB, shmem-rss:0kB
```

