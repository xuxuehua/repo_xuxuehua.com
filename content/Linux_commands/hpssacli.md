---
title: "hpssacli"
date: 2018-08-09 19:31
---

[TOC]

# hpssacli

HPE Smart Storage Administrator (HPE SSA) CLI (HPSSACLI, formerly HPACUCLI)



## ctrl

```
$ sudo hpssacli ctrl all show config

Smart Array P440 in Slot 2                


   Internal Drive Cage at Port 1I, Box 1, OK

   Internal Drive Cage at Port 1I, Box 1, OK
   array A (SAS, Unused Space: 0  MB)


      logicaldrive 1 (136.7 GB, RAID 1, OK)

      physicaldrive 1I:1:1 (port 1I:box 1:bay 1, SAS, 146 GB, OK)
      physicaldrive 1I:1:2 (port 1I:box 1:bay 2, SAS, 146 GB, OK)

   array B (Solid State SATA, Unused Space: 0  MB)


      logicaldrive 2 (1.6 TB, RAID 1+0, OK)

      physicaldrive 1I:1:3 (port 1I:box 1:bay 3, Solid State SATA, 600 GB, OK)
      physicaldrive 1I:1:4 (port 1I:box 1:bay 4, Solid State SATA, 600 GB, OK)
      physicaldrive 1I:1:5 (port 1I:box 1:bay 5, Solid State SATA, 600 GB, OK)
      physicaldrive 1I:1:6 (port 1I:box 1:bay 6, Solid State SATA, 600 GB, OK)
      physicaldrive 1I:1:7 (port 1I:box 1:bay 7, Solid State SATA, 600 GB, OK)
      physicaldrive 1I:1:8 (port 1I:box 1:bay 8, Solid State SATA, 600 GB, OK)
```

