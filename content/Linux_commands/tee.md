---
title: "tee"
date: 2020-03-17 22:24
---
[toc]



# tee



## Direct to two files

```
[root@ip-10-23-12-86 rxu_test]# echo "test5" | tee -a rxu.txt >>rxu_test.txt
[root@ip-10-23-12-86 rxu_test]# cat rxu.txt
test
test2
test4
test5
[root@ip-10-23-12-86 rxu_test]# cat rxu_test.txt
test2
test4
test5
```





