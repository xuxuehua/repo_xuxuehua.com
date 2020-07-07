---
title: "apt-get"
date: 2020-06-18 11:16
---
[toc]





# apt-get



## search 

search the packages that installed via apt-get

```
root@04825fb60008:/usr/share/spark-2.4.5-bin-hadoop2.7/python/lib# apt-cache search python3.7-dev
python3.7-dev - Header files and a static library for Python (v3.7)
libpython3.7-dev - Header files and a static library for Python (v3.7)
root@04825fb60008:/usr/share/spark-2.4.5-bin-hadoop2.7/python/lib#
root@04825fb60008:/usr/share/spark-2.4.5-bin-hadoop2.7/python/lib# dpkg -L python3.7-dev
/.
/usr
/usr/bin
/usr/share
/usr/share/doc
/usr/share/doc/python3.7
/usr/share/doc/python3.7/HISTORY.gz
/usr/share/doc/python3.7/README.maintainers
/usr/share/doc/python3.7/README.valgrind.gz
/usr/share/doc/python3.7/gdbinit.gz
/usr/share/doc/python3.7/pybench.log
/usr/share/doc/python3.7/test_results.gz
/usr/share/man
/usr/share/man/man1
/usr/bin/python3.7-config
/usr/bin/python3.7m-config
/usr/share/doc/python3.7-dev
/usr/share/man/man1/python3.7-config.1.gz
/usr/share/man/man1/python3.7m-config.1.gz
```





