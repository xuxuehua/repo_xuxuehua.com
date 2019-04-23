---
title: "goaccess 图形化分析access日志"
date: 2018-12-31 22:26
---


[TOC]



# 图形化分析access日志

```
https://goaccess.io
```



## Installation

### Debian/Ubuntu

```
echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a && \ /etc/apt/sources.list.d/goaccess.list && \
wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add - && \
sudo apt-get update && \
sudo apt-get install goaccess
```



### CentOS

```
yum -y install goaccess
```



## Terminal Output

```
goaccess access.log -c
```



## Static HTML Output 

```
goaccess access.log -o report.html --log-format=COMBINED
```



## Real-Time HTML Output

```
goaccess access.log -o /var/www/html/report.html --log-format=COMBINED --real-time-html
```



### Ubuntu Linux example

nginx.conf

```
html {
    server {
        location /report.html {
            alias /usr/local/openresty/nginx/html/report.html;
        }
    }
}
```



cmd

```
goaccess logs/access.log  -o html/report.html --real-time-html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED
```



### systemd

#### Centos

```
/usr/lib/systemd/system/goaccess.service
```



```
[Unit]
Description= goaccess
After=network.target


[Service]
Type=simple
User=root
Group=root
ExecStart=/bin/goaccess /var/log/httpd/access_log  -o /var/www/html/report.html --real-time-html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED
ExecReload=/bin/kill -HUP ${MAINPID}
KillSignal=SIGINT
TimeoutSec=30
Restart=on-failure
RestartSec=1



[Install]
WantedBy = multi-user.target
```



#### Ubuntu

```
/lib/systemd/system/goaccess.service
```

```
[Unit]
Description= goaccess
After=network.target


[Service]
Type=simple
User=root
Group=root
ExecStart=/usr/bin/goaccess /usr/local/nginx/logs/access.log  -o /usr/local/nginx/html/report.html --real-time-html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED
ExecReload=/bin/kill -HUP ${MAINPID}
KillSignal=SIGINT
TimeoutSec=30
Restart=on-failure
RestartSec=1



[Install]
WantedBy = multi-user.target
```

