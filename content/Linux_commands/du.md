---
title: "du"
date: 2018-07-16 20:51
---

[TOC]

# du



## --exclude



```
!42 $ sudo du -sh --exclude=/{proc,export,boot,mnt} /*
0	/bin
0	/dev
109M	/etc
364K	/home
0	/lib
0	/lib64
16K	/lost+found
4.0K	/media
159M	/opt
416K	/root
42M	/run
0	/sbin
4.0K	/srv
0	/sys
144K	/tmp
2.6G	/usr
251G	/var
```

