---
title: "systemd"
date: 2018-07-30 18:13
---

[TOC]



# systemd 

![img](https://cdn.pbrd.co/images/I0KO5bP.png)



## 特点

Systemd 的优点是功能强大，使用方便，缺点是体系庞大，非常复杂。事实上，现在还有很多人反对使用 Systemd，理由就是它过于复杂，与操作系统的其他部分强耦合，违反"keep simple, keep stupid"的Unix 哲学



## Commands



### systemctl

```
# 显示某个 Unit 是否正在运行
$ systemctl is-active application.service

# 显示某个 Unit 是否处于启动失败状态
$ systemctl is-failed application.service

# 显示某个 Unit 服务是否建立了启动链接
$ systemctl is-enabled application.service

# 立即启动一个服务
$ sudo systemctl start apache.service

# 立即停止一个服务
$ sudo systemctl stop apache.service

# 重启一个服务
$ sudo systemctl restart apache.service

# 杀死一个服务的所有子进程
$ sudo systemctl kill apache.service

# 重新加载一个服务的配置文件
$ sudo systemctl reload apache.service

# 重载所有修改过的配置文件
$ sudo systemctl daemon-reload

# 显示某个 Unit 的所有底层参数
$ systemctl show httpd.service

# 显示某个 Unit 的指定属性的值
$ systemctl show -p CPUShares httpd.service

# 设置某个 Unit 的指定属性
$ sudo systemctl set-property httpd.service CPUShares=500
```



#### List-units 列出unit

```
# 列出正在运行的 Unit
$ systemctl list-units

# 列出所有Unit，包括没有找到配置文件的或者启动失败的
$ systemctl list-units --all

# 列出所有没有运行的 Unit
$ systemctl list-units --all --state=inactive

# 列出所有加载失败的 Unit
$ systemctl list-units --failed

# 列出所有正在运行的、类型为 service 的 Unit
$ systemctl list-units --type=service
```



### systemd-analyze 耗时分析

命令用于查看启动耗时。

```
# 查看启动耗时
$ systemd-analyze                                                                                       

# 查看每个服务的启动耗时
$ systemd-analyze blame

# 显示瀑布状的启动过程流
$ systemd-analyze critical-chain

# 显示指定服务的启动流
$ systemd-analyze critical-chain atd.service
```



### hostnamectl 主机信息

```
# 显示当前主机的信息
$ hostnamectl

# 设置主机名。
$ sudo hostnamectl set-hostname rhel7
```



### localectl 本地设置

```
# 查看本地化设置
$ localectl

# 设置本地化参数。
$ sudo localectl set-locale LANG=en_GB.utf8
$ sudo localectl set-keymap en_GB
```



### timedatectl 时区设置

命令用于查看当前时区设置。

```
# 查看当前时区设置
$ timedatectl

# 显示所有可用的时区
$ timedatectl list-timezones                                                                                   

# 设置当前时区
$ sudo timedatectl set-timezone America/New_York
$ sudo timedatectl set-time YYYY-MM-DD
$ sudo timedatectl set-time HH:MM:SS
```



### loginctl 登陆信息

```
# 列出当前session
$ loginctl list-sessions

# 列出当前登录用户
$ loginctl list-users

# 列出显示指定用户的信息
$ loginctl show-user rick
```



### journalctl 日志

Systemd 统一管理所有 Unit 的启动日志。带来的好处就是，可以只用`journalctl`一个命令，查看所有日志（内核日志和应用日志）。日志的配置文件是`/etc/systemd/journald.conf`。

```
# 查看所有日志（默认情况下 ，只保存本次启动的日志）
$ sudo journalctl

# 查看内核日志（不显示应用日志）
$ sudo journalctl -k

# 查看系统本次启动的日志
$ sudo journalctl -b
$ sudo journalctl -b -0

# 查看上一次启动的日志（需更改设置）
$ sudo journalctl -b -1

# 查看指定时间的日志
$ sudo journalctl --since="2012-10-30 18:17:16"
$ sudo journalctl --since "20 min ago"
$ sudo journalctl --since yesterday
$ sudo journalctl --since "2015-01-10" --until "2015-01-11 03:00"
$ sudo journalctl --since 09:00 --until "1 hour ago"

# 显示尾部的最新10行日志
$ sudo journalctl -n

# 显示尾部指定行数的日志
$ sudo journalctl -n 20

# 实时滚动显示最新日志
$ sudo journalctl -f

# 查看指定服务的日志
$ sudo journalctl /usr/lib/systemd/systemd

# 查看指定进程的日志
$ sudo journalctl _PID=1

# 查看某个路径的脚本的日志
$ sudo journalctl /usr/bin/bash

# 查看指定用户的日志
$ sudo journalctl _UID=33 --since today

# 查看某个 Unit 的日志
$ sudo journalctl -u nginx.service
$ sudo journalctl -u nginx.service --since today

# 实时滚动显示某个 Unit 的最新日志
$ sudo journalctl -u nginx.service -f

# 合并显示多个 Unit 的日志
$ journalctl -u nginx.service -u php-fpm.service --since today

# 查看指定优先级（及其以上级别）的日志，共有8级
# 0: emerg
# 1: alert
# 2: crit
# 3: err
# 4: warning
# 5: notice
# 6: info
# 7: debug
$ sudo journalctl -p err -b

# 日志默认分页输出，--no-pager 改为正常的标准输出
$ sudo journalctl --no-pager

# 以 JSON 格式（单行）输出
$ sudo journalctl -b -u nginx.service -o json

# 以 JSON 格式（多行）输出，可读性更好
$ sudo journalctl -b -u nginx.serviceqq
 -o json-pretty

# 显示日志占据的硬盘空间
$ sudo journalctl --disk-usage

# 指定日志文件占据的最大空间
$ sudo journalctl --vacuum-size=1G

# 指定日志文件保存多久
$ sudo journalctl --vacuum-time=1years
```







## [Unit]

### Description

A meaningful description of the unit. This text is displayed for example in the output of the systemctl status command.



### Documentation

Provides a list of URIs referencing documentation for the unit.



### After

Defines the order in which units are started. The unit starts only after the units specified in After are active. Unlike Requires, After does not explicitly activate the specified units. The Before option has the opposite functionality to After.



### Requires

Configures dependencies on other units. The units listed in Requires are activated together with the unit. If any of the required units fail to start, the unit is not activated.



### Wants

Configures weaker dependencies than Requires. If any of the listed units does not start successfully, it has no impact on the unit activation. This is the recommended way to establish custom unit dependencies.



### Conflicts

Configures negative dependencies, an opposite to Requires.





## [Service]

### Type

Configures the unit process startup type that affects the functionality of ExecStart and related options. One of: 

- simple – The default value. The process started with ExecStart is the main process of the service. 
- forking – The process started with ExecStart spawns a child process that becomes the main process of the service. The parent process exits when the startup is complete. 
- oneshot – This type is similar to simple, but the process exits before starting consequent units. 
- dbus – This type is similar to simple, but consequent units are started only after the main process gains a D-Bus name. 
- notify – This type is similar to simple, but consequent units are started only after a notification message is sent via the sd_notify() function. 
- idle – similar to simple, the actual execution of the service binary is delayed until all jobs are finished, which avoids mixing the status output with shell output of services. 



### ExecStart

Specifies commands or scripts to be executed when the unit is started. ExecStartPre and ExecStartPost specify custom commands to be executed before and after ExecStart. Type=oneshot enables specifying multiple custom commands that are then executed sequentially.



### ExecStop

Specifies commands or scripts to be executed when the unit is stopped.



### ExecReload

Specifies commands or scripts to be executed when the unit is reloaded.



### Restart

With this option enabled, the service is restarted after its process exits, with the exception of a clean stop by the systemctl command.

可能的值包括`always`（总是重启）、`on-success`、`on-failure`、`on-abnormal`、`on-abort`、`on-watchdog`

### RemainAfterExit

If set to True, the service is considered active even when all its processes exited. Default value is False. This option is especially useful if Type=oneshot is configured.



## [Install]

启动计算机的时候，需要启动大量的 Unit。如果每一次启动，都要一一写明本次启动需要哪些 Unit，显然非常不方便。Systemd 的解决方案就是 Target。

简单说，Target 就是一个 Unit 组，包含许多相关的 Unit 。启动某个 Target 的时候，Systemd 就会启动里面所有的 Unit。从这个意义上说，Target 这个概念类似于"状态点"，启动某个 Target 就好比启动到某种状态。



### Target 与 Runlevel

```
Traditional runlevel      New target name     Symbolically linked to...

Runlevel 0           |    runlevel0.target -> poweroff.target
Runlevel 1           |    runlevel1.target -> rescue.target
Runlevel 2           |    runlevel2.target -> multi-user.target
Runlevel 3           |    runlevel3.target -> multi-user.target
Runlevel 4           |    runlevel4.target -> multi-user.target
Runlevel 5           |    runlevel5.target -> graphical.target
Runlevel 6           |    runlevel6.target -> reboot.target
```



### init 进程

```
（1）默认的 RunLevel（在/etc/inittab文件设置）现在被默认的 Target 取代，位置是/etc/systemd/system/default.target，通常符号链接到graphical.target（图形界面）或者multi-user.target（多用户命令行）。

（2）启动脚本的位置，以前是/etc/init.d目录，符号链接到不同的 RunLevel 目录 （比如/etc/rc3.d、/etc/rc5.d等），现在则存放在/lib/systemd/system和/etc/systemd/system目录。

（3）配置文件的位置，以前init进程的配置文件是/etc/inittab，各种服务的配置文件存放在/etc/sysconfig目录。现在的配置文件主要存放在/lib/systemd目录，在/etc/systemd目录里面的修改可以覆盖原始设置。
```



### Alias

Provides a space-separated list of additional names for the unit. Most systemctl commands, excluding systemctl enable, can use aliases instead of the actual unit name.



### RequiredBy

A list of units that depend on the unit. When this unit is enabled, the units listed in RequiredBy gain a Require dependency on the unit.

它的值是一个或多个 Target，当前 Unit 激活时，符号链接会放入`/etc/systemd/system`目录下面以 Target 名 + `.required`后缀构成的子目录中



### WantedBy

A list of units that weakly depend on the unit. When this unit is enabled, the units listed in WantedBy gain a Want dependency on the unit.

它的值是一个或多个 Target，当前 Unit 激活时（enable）符号链接会放入`/etc/systemd/system`目录下面以 Target 名 + `.wants`后缀构成的子目录中



### Also

Specifies a list of units to be installed or uninstalled along with the unit.



### DefaultInstance

Limited to instantiated units, this option specifies the default instance for which the unit is enabled.





## [Timer] 定时器

新建 Service 非常简单，就是在/usr/lib/systemd/system目录里面新建一个文件，比如mytimer.service文件，你可以写入下面的内容。

```
[Unit]
Description=MyTimer

[Service]
ExecStart=/bin/bash /path/to/mail.sh
```



`/usr/lib/systemd/system`目录里面，新建一个`mytimer.timer`文件。

```
[Unit]
Description=Runs mytimer every hour

[Timer]
OnUnitActiveSec=1h
Unit=mytimer.service

[Install]
WantedBy=multi-user.target
```



```
启动定时器
sudo systemctl start mytimer.timer

查看这个定时器的状态。
$ systemctl status mytimer.timer

查看所有正在运行的定时器。
$ systemctl list-timers

关闭这个定时器。
$ sudo systemctl stop myscript.timer

下次开机，自动运行这个定时器。
$ sudo systemctl enable myscript.timer

关闭定时器的开机自启动。
$ sudo systemctl disable myscript.timer

```



### Timer参数

```
OnActiveSec：定时器生效后，多少时间开始执行任务
OnBootSec：系统启动后，多少时间开始执行任务
OnStartupSec：Systemd 进程启动后，多少时间开始执行任务
OnUnitActiveSec：该单元上次执行后，等多少时间再次执行
OnUnitInactiveSec： 定时器上次关闭后多少时间，再次执行
OnCalendar：基于绝对时间，而不是相对时间执行
AccuracySec：如果因为各种原因，任务必须推迟执行，推迟的最大秒数，默认是60秒
Unit：真正要执行的任务，默认是同名的带有.service后缀的单元
Persistent：如果设置了该字段，即使定时器到时没有启动，也会自动执行相应的单元
WakeSystem：如果系统休眠，是否自动唤醒系统
```



### 时间参数

```
OnUnitActiveSec=1h表示一小时执行一次任务。
```



#### 可识别时间符号 Parsing Time Spans 

When parsing, systemd will accept the same time span syntax. Separating spaces may be omitted. The following time units are understood:

- usec, us, µs
- msec, ms
- seconds, second, sec, s
- minutes, minute, min, m
- hours, hour, hr, h
- days, day, d
- weeks, week, w
- months, month, M (defined as 30.44 days)
- years, year, y (defined as 365.25 days)

If no time unit is specified, generally seconds are assumed, but some exceptions exist and are marked as such. In a few cases "`ns`", "`nsec`" is accepted too, where the granularity of the time span permits this. Parsing is generally locale-independent, non-English names for the time units are not accepted.

Examples for valid time span specifications:

```
2 h
2hours
48hr
1y 12month
55s500ms
300ms20s 5day
```



#### valid timestamps and their normalized form

```
  Fri 2012-11-23 11:12:13 → Fri 2012-11-23 11:12:13
      2012-11-23 11:12:13 → Fri 2012-11-23 11:12:13
  2012-11-23 11:12:13 UTC → Fri 2012-11-23 19:12:13
               2012-11-23 → Fri 2012-11-23 00:00:00
                 12-11-23 → Fri 2012-11-23 00:00:00
                 11:12:13 → Fri 2012-11-23 11:12:13
                    11:12 → Fri 2012-11-23 11:12:00
                      now → Fri 2012-11-23 18:15:22
                    today → Fri 2012-11-23 00:00:00
                today UTC → Fri 2012-11-23 16:00:00
                yesterday → Fri 2012-11-22 00:00:00
                 tomorrow → Fri 2012-11-24 00:00:00
tomorrow Pacific/Auckland → Thu 2012-11-23 19:00:00
                 +3h30min → Fri 2012-11-23 21:45:22
                      -5s → Fri 2012-11-23 18:15:17
                11min ago → Fri 2012-11-23 18:04:22
              @1395716396 → Tue 2014-03-25 03:59:56
```

Timestamps may also be specified with microsecond granularity. The sub-second remainder is expected separated by a full stop from the seconds component. Example:

```
2014-03-25 03:59:56.654563
```



The following special expressions may be used as shorthands for longer normalized forms:

```
    minutely → *-*-* *:*:00
      hourly → *-*-* *:00:00
       daily → *-*-* 00:00:00
     monthly → *-*-01 00:00:00
      weekly → Mon *-*-* 00:00:00
      yearly → *-01-01 00:00:00
   quarterly → *-01,04,07,10-01 00:00:00
semiannually → *-01,07-01 00:00:00
```





Examples for valid timestamps and their normalized form:

```
  Sat,Thu,Mon..Wed,Sat..Sun → Mon..Thu,Sat,Sun *-*-* 00:00:00
      Mon,Sun 12-*-* 2,1:23 → Mon,Sun 2012-*-* 01,02:23:00
                    Wed *-1 → Wed *-*-01 00:00:00
           Wed..Wed,Wed *-1 → Wed *-*-01 00:00:00
                 Wed, 17:48 → Wed *-*-* 17:48:00
Wed..Sat,Tue 12-10-15 1:2:3 → Tue..Sat 2012-10-15 01:02:03
                *-*-7 0:0:0 → *-*-07 00:00:00
                      10-15 → *-10-15 00:00:00
        monday *-12-* 17:00 → Mon *-12-* 17:00:00
  Mon,Fri *-*-3,1,2 *:30:45 → Mon,Fri *-*-01,02,03 *:30:45
       12,14,13,12:20,10,30 → *-*-* 12,13,14:10,20,30:00
            12..14:10,20,30 → *-*-* 12..14:10,20,30:00
  mon,fri *-1/2-1,3 *:30:45 → Mon,Fri *-01/2-01,03 *:30:45
             03-05 08:05:40 → *-03-05 08:05:40
                   08:05:40 → *-*-* 08:05:40
                      05:40 → *-*-* 05:40:00
     Sat,Sun 12-05 08:05:40 → Sat,Sun *-12-05 08:05:40
           Sat,Sun 08:05:40 → Sat,Sun *-*-* 08:05:40
           2003-03-05 05:40 → 2003-03-05 05:40:00
 05:40:23.4200004/3.1700005 → *-*-* 05:40:23.420000/3.170001
             2003-02..04-05 → 2003-02..04-05 00:00:00
       2003-03-05 05:40 UTC → 2003-03-05 05:40:00 UTC
                 2003-03-05 → 2003-03-05 00:00:00
                      03-05 → *-03-05 00:00:00
                     hourly → *-*-* *:00:00
                      daily → *-*-* 00:00:00
                  daily UTC → *-*-* 00:00:00 UTC
                    monthly → *-*-01 00:00:00
                     weekly → Mon *-*-* 00:00:00
    weekly Pacific/Auckland → Mon *-*-* 00:00:00 Pacific/Auckland
                     yearly → *-01-01 00:00:00
                   annually → *-01-01 00:00:00
                      *:2/3 → *-*-* *:02/3:00
```





### 日志

```

# 查看整个日志
$ sudo journalctl

# 查看 mytimer.timer 的日志
$ sudo journalctl -u mytimer.timer

# 查看 mytimer.timer 和 mytimer.service 的日志
$ sudo journalctl -u mytimer

# 从结尾开始查看最新日志
$ sudo journalctl -f

# 从结尾开始查看 mytimer.timer 的日志
$ journalctl -f -u timer.timer
```





# exmaple

## ipa_list      

```
[Unit]
Description=ipa_list
[Service]
Type=simple
ExecStart=/root/.pyenv/versions/env3.5.3/bin/python /root/rtmbot_test/ipa_list.py
WorkingDirectory=/root/rtmbot_test
Restart=on-failure
LimitCORE=infinity
[Install]
WantedBy=multi-user.target
```



## rpcbind

```
!1004 $ cat /etc/sysconfig/rpcbind
#
# Optional arguments passed to rpcbind. See rpcbind(8)
RPCBIND_ARGS=""
```



```
[Unit]
Description=RPC bind service
Requires=rpcbind.socket
After=systemd-tmpfiles-setup.service

[Service]
Type=forking
EnvironmentFile=/etc/sysconfig/rpcbind
ExecStart=/sbin/rpcbind -w $RPCBIND_ARGS

[Install]
Also=rpcbind.socket
```



## postfix

```
/usr/lib/systemd/system/postifix.service
```

```
[Unit]
Description=Postfix Mail Transport Agent
After=syslog.target network.target
Conflicts=sendmail.service exim.service

[Service]
Type=forking
PIDFile=/var/spool/postfix/pid/master.pid
EnvironmentFile=-/etc/sysconfig/network
ExecStartPre=-/usr/libexec/postfix/aliasesdb
ExecStartPre=-/usr/libexec/postfix/chroot-update
ExecStart=/usr/sbin/postfix start
ExecReload=/usr/sbin/postfix reload
ExecStop=/usr/sbin/postfix stop

[Install]
WantedBy=multi-user.target
```





## Emacs

```
touch /etc/systemd/system/emacs.service
chmod 664 /etc/systemd/system/emacs.service
```

```
[Unit]
Description=Emacs: the extensible, self-documenting text editor
           
[Service]
Type=forking
ExecStart=/usr/bin/emacs --daemon
ExecStop=/usr/bin/emacsclient --eval "(kill-emacs)"
Environment=SSH_AUTH_SOCK=%t/keyring/ssh
Restart=always
           
[Install]
WantedBy=default.target
```



## Second sshd

```
cp /etc/ssh/sshd{,-second}_config
```

```
Port 22220
PidFile /var/run/sshd-second.pid
```

```
[Unit]
Description=OpenSSH server second instance daemon
After=syslog.target network.target auditd.service sshd.service

[Service]
EnvironmentFile=/etc/sysconfig/sshd
ExecStart=/usr/sbin/sshd -D -f /etc/ssh/sshd-second_config $OPTIONS
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
```

```
cp /usr/lib/systemd/system/sshd.service /etc/systemd/system/sshd-second.service
```

If using SELinux, add the port for the second instance of sshd to SSH ports, otherwise the second instance of sshd will be rejected to bind to the port:

```
semanage port -a -t ssh_port_t -p tcp 22220
```



## ss

```
vim /lib/systemd/system/ss-local.service
```



```
[Unit]
Description=Shadowsocks-Libev Custom Client Service for %I
Documentation=man:ss-local(1)
After=network.target

[Service]
Type=simple
# CapabilityBoundingSet=CAP_NET_BIND_SERVICE
User=nobody
Group=nogroup
LimitNOFILE=32768
ExecStart=/usr/bin/ss-local -c /etc/shadowsocks-libev/config.json
# ExecStart=/usr/bin/ss-local -c $CONFFILE $DAEMON_ARGS


[Install]
WantedBy=multi-user.target
```



## Python-configparser

可通过configparser 生成systemd配置文件

