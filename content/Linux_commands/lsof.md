---
title: "lsof"
date: 2020-04-01 02:28
---
[toc]





# lsof 

lsof 是 linux 下的一个非常实用的系统级的监控、诊断工具。

它的意思是 List Open Files，很容易你就记住了它是 “ls + of”的组合~

它可以用来列出被各种进程打开的文件信息，记住：**linux 下 “一切皆文件”**，

包括但不限于 pipes, sockets, directories, devices, 等等。

因此，使用 lsof，你可以获取任何被打开文件的各种信息。



只需输入 lsof 就可以生成大量的信息，因为 lsof 需要访问核心内存和各种文件，所以必须以 root 用户的身份运行它才能够充分地发挥其功能。





# Hello World

```
# lsof
COMMAND     PID   TID       USER   FD      TYPE     DEVICE SIZE/OFF       NODE NAME
systemd       1             root  cwd       DIR        8,6     4096          2 /
systemd       1             root  rtd       DIR        8,6     4096          2 /
systemd       1             root  txt       REG        8,6  2273340    1834909 /usr/lib/systemd/systemd
systemd       1             root  mem       REG        8,6   210473    1700647 /lib/libnss_files-2.15.s
...
```













# 常用操作



## 监控打开的文件、设备

```
# lsof /dev/tty1
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND    PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
systemd-l  780 root   21u   CHR    4,1      0t0 1044 /dev/tty1
gdm-x-ses 1132  gdm    0u   CHR    4,1      0t0 1044 /dev/tty1
Xorg      1134  gdm    0u   CHR    4,1      0t0 1044 /dev/tty1
Xorg      1134  gdm   11u   CHR    4,1      0t0 1044 /dev/tty1
```





## 监控文件系统

```
# lsof /var/log/auth.log
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF  NODE NAME
rsyslogd 735 syslog    9w   REG  252,1     1256 59780 /var/log/auth.log
```





列出某个目录（挂载点 如 /home 也行）下被打开的文件

```
# lsof +D /var/log
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND     PID     USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
rsyslogd    735   syslog    7w   REG  252,1   262710      71 /var/log/syslog
rsyslogd    735   syslog    8w   REG  252,1    50344   59756 /var/log/kern.log
rsyslogd    735   syslog    9w   REG  252,1     1256   59780 /var/log/auth.log
rabbitmq-   840 rabbitmq    1w   REG  252,1      362  266813 /var/log/rabbitmq/startup_log
rabbitmq-   840 rabbitmq    2w   REG  252,1       61  266822 /var/log/rabbitmq/startup_err
unattende   847     root    3w   REG  252,1        0 1294312 /var/log/unattended-upgrades/unattended-upgrades-shutdown.log
mpd         848      mpd    1w   REG  252,1       60 1929927 /var/log/mpd/mpd.log
mpd         848      mpd    2w   REG  252,1       60 1929927 /var/log/mpd/mpd.log
beam.smp    860 rabbitmq    1w   REG  252,1      362  266813 /var/log/rabbitmq/startup_log
beam.smp    860 rabbitmq    2w   REG  252,1       61  266822 /var/log/rabbitmq/startup_err
beam.smp    860 rabbitmq   14w   REG  252,1        0  256924 /var/log/rabbitmq/rabbit@host1595041234.log
beam.smp    860 rabbitmq   15w   REG  252,1        0  266854 /var/log/rabbitmq/rabbit@host1595041234-sasl.log
xrdp-sesm   874     root    3w   REG  252,1     6256   40671 /var/log/xrdp-sesman.log
xrdp        929     xrdp    3w   REG  252,1 10140686   41104 /var/log/xrdp.log
erl_child  1625 rabbitmq    1w   REG  252,1      362  266813 /var/log/rabbitmq/startup_log
erl_child  1625 rabbitmq    2w   REG  252,1       61  266822 /var/log/rabbitmq/startup_err
inet_geth  2015 rabbitmq    2w   REG  252,1       61  266822 /var/log/rabbitmq/startup_err
inet_geth  2016 rabbitmq    2w   REG  252,1       61  266822 /var/log/rabbitmq/startup_err
cupsd     16857     root   11u   REG  252,1      224  769551 /var/log/cups/access_log
```



列出被指定进程名打开的文件

```
# lsof -c ssh -c init
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND   PID   USER   FD   TYPE             DEVICE SIZE/OFF   NODE NAME
sshd      889   root  cwd    DIR              252,1     4096      2 /
sshd      889   root  rtd    DIR              252,1     4096      2 /
sshd      889   root  txt    REG              252,1   786856  41482 /usr/sbin/sshd
sshd      889   root  mem    REG              252,1    47568  64052 /lib/x86_64-linux-gnu/libnss_files-2.27.so
sshd      889   root  mem    REG              252,1    47576  64054 /lib/x86_64-linux-gnu/libnss_nis-2.27.so
sshd      889   root  mem    REG              252,1    39744  64050 /lib/x86_64-linux-gnu/libnss_compat-2.27.so
sshd      889   root  mem    REG              252,1    84032   1136 /lib/x86_64-linux-gnu/libgpg-error.so.0.22.0
sshd      889   root  mem    REG              252,1    97072  64058 /lib/x86_64-linux-gnu/libresolv-2.27.so
sshd      889   root  mem    REG              252,1    14256  35582 /lib/x86_64-linux-gnu/libkeyutils.so.1.5
sshd      889   root  mem    REG              252,1    43616  35601 /usr/lib/x86_64-linux-gnu/libkrb5support.so.0.1
sshd      889   root  mem    REG              252,1   199104  35557 /usr/lib/x86_64-linux-gnu/libk5crypto.so.3.1
sshd      889   root  mem    REG              252,1   144976  64057 /lib/x86_64-linux-gnu/libpthread-2.27.so
sshd      889   root  mem    REG              252,1  1159864   2653 /lib/x86_64-linux-gnu/libgcrypt.so.20.2.1
sshd      889   root  mem    REG              252,1   112672   1153 /usr/lib/x86_64-linux-gnu/liblz4.so.1.7.1
sshd      889   root  mem    REG              252,1   153984   1747 /lib/x86_64-linux-gnu/liblzma.so.5.2.2
sshd      889   root  mem    REG              252,1    31680  64059 /lib/x86_64-linux-gnu/librt-2.27.so
sshd      889   root  mem    REG              252,1   464824   1926 /lib/x86_64-linux-gnu/libpcre.so.3.13.3
sshd      889   root  mem    REG              252,1    14560   8593 /lib/x86_64-linux-gnu/libdl-2.27.so
sshd      889   root  mem    REG              252,1    18712    344 /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
sshd      889   root  mem    REG              252,1    97176  63391 /lib/x86_64-linux-gnu/libnsl-2.27.so
sshd      889   root  mem    REG              252,1  2030928   8589 /lib/x86_64-linux-gnu/libc-2.27.so
sshd      889   root  mem    REG              252,1    14248  33986 /lib/x86_64-linux-gnu/libcom_err.so.2.1
sshd      889   root  mem    REG              252,1   877056  35615 /usr/lib/x86_64-linux-gnu/libkrb5.so.3.3
sshd      889   root  mem    REG              252,1   305456  35525 /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2.2
sshd      889   root  mem    REG              252,1    39208   8592 /lib/x86_64-linux-gnu/libcrypt-2.27.so
sshd      889   root  mem    REG              252,1   116960   3495 /lib/x86_64-linux-gnu/libz.so.1.2.11
sshd      889   root  mem    REG              252,1    10592  64061 /lib/x86_64-linux-gnu/libutil-2.27.so
sshd      889   root  mem    REG              252,1  2361984    102 /usr/lib/x86_64-linux-gnu/libcrypto.so.1.0.0
sshd      889   root  mem    REG              252,1   536648   5024 /lib/x86_64-linux-gnu/libsystemd.so.0.21.0
sshd      889   root  mem    REG              252,1   154832   1965 /lib/x86_64-linux-gnu/libselinux.so.1
sshd      889   root  mem    REG              252,1    55848    421 /lib/x86_64-linux-gnu/libpam.so.0.83.1
sshd      889   root  mem    REG              252,1   124848  41121 /lib/x86_64-linux-gnu/libaudit.so.1.0.0
sshd      889   root  mem    REG              252,1    39784  37423 /lib/x86_64-linux-gnu/libwrap.so.0.7.6
sshd      889   root  mem    REG              252,1   179152   8579 /lib/x86_64-linux-gnu/ld-2.27.so
sshd      889   root    0r   CHR                1,3      0t0   1028 /dev/null
sshd      889   root    1u  unix 0xffff8f19590c7400      0t0  26509 type=STREAM
sshd      889   root    2u  unix 0xffff8f19590c7400      0t0  26509 type=STREAM
sshd      889   root    3u  IPv4              27679      0t0    TCP *:54321 (LISTEN)
sshd      889   root    4u  IPv6              27683      0t0    TCP *:54321 (LISTEN)
sshd    25157   root  cwd    DIR              252,1     4096      2 /
sshd    25157   root  rtd    DIR              252,1     4096      2 /
sshd    25157   root  txt    REG              252,1   786856  41482 /usr/sbin/sshd
sshd    25157   root  mem    REG              252,1   253944   8143 /lib/x86_64-linux-gnu/libnss_systemd.so.2
sshd    25157   root  mem    REG              252,1    42920  69906 /lib/x86_64-linux-gnu/security/pam_gnome_keyring.so
sshd    25157   root  mem    REG              252,1    14464    498 /lib/x86_64-linux-gnu/security/pam_env.so
sshd    25157   root  mem    REG              252,1    22872    508 /lib/x86_64-linux-gnu/security/pam_limits.so
sshd    25157   root  mem    REG              252,1    10312    512 /lib/x86_64-linux-gnu/security/pam_mail.so
sshd    25157   root  mem    REG              252,1    10336    514 /lib/x86_64-linux-gnu/security/pam_motd.so
sshd    25157   root  mem    REG              252,1    14576    448 /lib/x86_64-linux-gnu/libpam_misc.so.0.82.0
sshd    25157   root  mem    REG              252,1   258040   6976 /lib/x86_64-linux-gnu/security/pam_systemd.so
sshd    25157   root  mem    REG              252,1    10376    535 /lib/x86_64-linux-gnu/security/pam_umask.so
sshd    25157   root  mem    REG              252,1    10280    506 /lib/x86_64-linux-gnu/security/pam_keyinit.so
sshd    25157   root  mem    REG              252,1    10336    511 /lib/x86_64-linux-gnu/security/pam_loginuid.so
sshd    25157   root  mem    REG              252,1    18736    522 /lib/x86_64-linux-gnu/security/pam_selinux.so
sshd    25157   root  mem    REG              252,1    10264    516 /lib/x86_64-linux-gnu/security/pam_nologin.so
sshd    25157   root  mem    REG              252,1    22768   4356 /lib/x86_64-linux-gnu/libcap.so.2.25
sshd    25157   root  mem    REG              252,1    10080   8127 /lib/x86_64-linux-gnu/security/pam_cap.so
sshd    25157   root  mem    REG              252,1     6104    517 /lib/x86_64-linux-gnu/security/pam_permit.so
sshd    25157   root  mem    REG              252,1     5776    496 /lib/x86_64-linux-gnu/security/pam_deny.so
sshd    25157   root  mem    REG              252,1    60272    536 /lib/x86_64-linux-gnu/security/pam_unix.so
sshd    25157   root  mem    REG              252,1    47568  64052 /lib/x86_64-linux-gnu/libnss_files-2.27.so
sshd    25157   root  mem    REG              252,1    47576  64054 /lib/x86_64-linux-gnu/libnss_nis-2.27.so
sshd    25157   root  mem    REG              252,1    39744  64050 /lib/x86_64-linux-gnu/libnss_compat-2.27.so
sshd    25157   root  mem    REG              252,1    84032   1136 /lib/x86_64-linux-gnu/libgpg-error.so.0.22.0
sshd    25157   root  mem    REG              252,1    97072  64058 /lib/x86_64-linux-gnu/libresolv-2.27.so
sshd    25157   root  mem    REG              252,1    14256  35582 /lib/x86_64-linux-gnu/libkeyutils.so.1.5
sshd    25157   root  mem    REG              252,1    43616  35601 /usr/lib/x86_64-linux-gnu/libkrb5support.so.0.1
sshd    25157   root  mem    REG              252,1   199104  35557 /usr/lib/x86_64-linux-gnu/libk5crypto.so.3.1
sshd    25157   root  mem    REG              252,1   144976  64057 /lib/x86_64-linux-gnu/libpthread-2.27.so
sshd    25157   root  mem    REG              252,1  1159864   2653 /lib/x86_64-linux-gnu/libgcrypt.so.20.2.1
sshd    25157   root  mem    REG              252,1   112672   1153 /usr/lib/x86_64-linux-gnu/liblz4.so.1.7.1
sshd    25157   root  mem    REG              252,1   153984   1747 /lib/x86_64-linux-gnu/liblzma.so.5.2.2
sshd    25157   root  mem    REG              252,1    31680  64059 /lib/x86_64-linux-gnu/librt-2.27.so
sshd    25157   root  mem    REG              252,1   464824   1926 /lib/x86_64-linux-gnu/libpcre.so.3.13.3
sshd    25157   root  mem    REG              252,1    14560   8593 /lib/x86_64-linux-gnu/libdl-2.27.so
sshd    25157   root  mem    REG              252,1    18712    344 /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
sshd    25157   root  mem    REG              252,1    97176  63391 /lib/x86_64-linux-gnu/libnsl-2.27.so
sshd    25157   root  mem    REG              252,1  2030928   8589 /lib/x86_64-linux-gnu/libc-2.27.so
sshd    25157   root  mem    REG              252,1    14248  33986 /lib/x86_64-linux-gnu/libcom_err.so.2.1
sshd    25157   root  mem    REG              252,1   877056  35615 /usr/lib/x86_64-linux-gnu/libkrb5.so.3.3
sshd    25157   root  mem    REG              252,1   305456  35525 /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2.2
sshd    25157   root  mem    REG              252,1    39208   8592 /lib/x86_64-linux-gnu/libcrypt-2.27.so
sshd    25157   root  mem    REG              252,1   116960   3495 /lib/x86_64-linux-gnu/libz.so.1.2.11
sshd    25157   root  mem    REG              252,1    10592  64061 /lib/x86_64-linux-gnu/libutil-2.27.so
sshd    25157   root  mem    REG              252,1  2361984    102 /usr/lib/x86_64-linux-gnu/libcrypto.so.1.0.0
sshd    25157   root  mem    REG              252,1   536648   5024 /lib/x86_64-linux-gnu/libsystemd.so.0.21.0
sshd    25157   root  mem    REG              252,1   154832   1965 /lib/x86_64-linux-gnu/libselinux.so.1
sshd    25157   root  mem    REG              252,1    55848    421 /lib/x86_64-linux-gnu/libpam.so.0.83.1
sshd    25157   root  mem    REG              252,1   124848  41121 /lib/x86_64-linux-gnu/libaudit.so.1.0.0
sshd    25157   root  mem    REG              252,1    39784  37423 /lib/x86_64-linux-gnu/libwrap.so.0.7.6
sshd    25157   root  mem    REG              252,1   179152   8579 /lib/x86_64-linux-gnu/ld-2.27.so
sshd    25157   root    0r   CHR                1,3      0t0   1028 /dev/null
sshd    25157   root    1u   CHR                1,3      0t0   1028 /dev/null
sshd    25157   root    2u   CHR                1,3      0t0   1028 /dev/null
sshd    25157   root    3u  IPv4             893062      0t0    TCP host1595041234:54321->4.35.65.3:35551 (ESTABLISHED)
sshd    25157   root    4u  unix 0xffff8f19580c0000      0t0 894031 type=DGRAM
sshd    25157   root    5u   CHR                5,2      0t0  13408 /dev/ptmx
sshd    25157   root    6u  unix 0xffff8f1870db9800      0t0 893384 type=STREAM
sshd    25157   root    7w  FIFO               0,24      0t0 893318 /run/systemd/sessions/29.ref
sshd    25256 ubuntu  cwd    DIR              252,1     4096      2 /
sshd    25256 ubuntu  rtd    DIR              252,1     4096      2 /
sshd    25256 ubuntu  txt    REG              252,1   786856  41482 /usr/sbin/sshd
sshd    25256 ubuntu  mem    REG              252,1   253944   8143 /lib/x86_64-linux-gnu/libnss_systemd.so.2
sshd    25256 ubuntu  mem    REG              252,1    42920  69906 /lib/x86_64-linux-gnu/security/pam_gnome_keyring.so
sshd    25256 ubuntu  mem    REG              252,1    14464    498 /lib/x86_64-linux-gnu/security/pam_env.so
sshd    25256 ubuntu  mem    REG              252,1    22872    508 /lib/x86_64-linux-gnu/security/pam_limits.so
sshd    25256 ubuntu  mem    REG              252,1    10312    512 /lib/x86_64-linux-gnu/security/pam_mail.so
sshd    25256 ubuntu  mem    REG              252,1    10336    514 /lib/x86_64-linux-gnu/security/pam_motd.so
sshd    25256 ubuntu  mem    REG              252,1    14576    448 /lib/x86_64-linux-gnu/libpam_misc.so.0.82.0
sshd    25256 ubuntu  mem    REG              252,1   258040   6976 /lib/x86_64-linux-gnu/security/pam_systemd.so
sshd    25256 ubuntu  mem    REG              252,1    10376    535 /lib/x86_64-linux-gnu/security/pam_umask.so
sshd    25256 ubuntu  mem    REG              252,1    10280    506 /lib/x86_64-linux-gnu/security/pam_keyinit.so
sshd    25256 ubuntu  mem    REG              252,1    10336    511 /lib/x86_64-linux-gnu/security/pam_loginuid.so
sshd    25256 ubuntu  mem    REG              252,1    18736    522 /lib/x86_64-linux-gnu/security/pam_selinux.so
sshd    25256 ubuntu  mem    REG              252,1    10264    516 /lib/x86_64-linux-gnu/security/pam_nologin.so
sshd    25256 ubuntu  mem    REG              252,1    22768   4356 /lib/x86_64-linux-gnu/libcap.so.2.25
sshd    25256 ubuntu  mem    REG              252,1    10080   8127 /lib/x86_64-linux-gnu/security/pam_cap.so
sshd    25256 ubuntu  mem    REG              252,1     6104    517 /lib/x86_64-linux-gnu/security/pam_permit.so
sshd    25256 ubuntu  mem    REG              252,1     5776    496 /lib/x86_64-linux-gnu/security/pam_deny.so
sshd    25256 ubuntu  mem    REG              252,1    60272    536 /lib/x86_64-linux-gnu/security/pam_unix.so
sshd    25256 ubuntu  mem    REG              252,1    47568  64052 /lib/x86_64-linux-gnu/libnss_files-2.27.so
sshd    25256 ubuntu  mem    REG              252,1    47576  64054 /lib/x86_64-linux-gnu/libnss_nis-2.27.so
sshd    25256 ubuntu  mem    REG              252,1    39744  64050 /lib/x86_64-linux-gnu/libnss_compat-2.27.so
sshd    25256 ubuntu  mem    REG              252,1    84032   1136 /lib/x86_64-linux-gnu/libgpg-error.so.0.22.0
sshd    25256 ubuntu  mem    REG              252,1    97072  64058 /lib/x86_64-linux-gnu/libresolv-2.27.so
sshd    25256 ubuntu  mem    REG              252,1    14256  35582 /lib/x86_64-linux-gnu/libkeyutils.so.1.5
sshd    25256 ubuntu  mem    REG              252,1    43616  35601 /usr/lib/x86_64-linux-gnu/libkrb5support.so.0.1
sshd    25256 ubuntu  mem    REG              252,1   199104  35557 /usr/lib/x86_64-linux-gnu/libk5crypto.so.3.1
sshd    25256 ubuntu  mem    REG              252,1   144976  64057 /lib/x86_64-linux-gnu/libpthread-2.27.so
sshd    25256 ubuntu  mem    REG              252,1  1159864   2653 /lib/x86_64-linux-gnu/libgcrypt.so.20.2.1
sshd    25256 ubuntu  mem    REG              252,1   112672   1153 /usr/lib/x86_64-linux-gnu/liblz4.so.1.7.1
sshd    25256 ubuntu  mem    REG              252,1   153984   1747 /lib/x86_64-linux-gnu/liblzma.so.5.2.2
sshd    25256 ubuntu  mem    REG              252,1    31680  64059 /lib/x86_64-linux-gnu/librt-2.27.so
sshd    25256 ubuntu  mem    REG              252,1   464824   1926 /lib/x86_64-linux-gnu/libpcre.so.3.13.3
sshd    25256 ubuntu  mem    REG              252,1    14560   8593 /lib/x86_64-linux-gnu/libdl-2.27.so
sshd    25256 ubuntu  mem    REG              252,1    18712    344 /lib/x86_64-linux-gnu/libcap-ng.so.0.0.0
sshd    25256 ubuntu  mem    REG              252,1    97176  63391 /lib/x86_64-linux-gnu/libnsl-2.27.so
sshd    25256 ubuntu  mem    REG              252,1  2030928   8589 /lib/x86_64-linux-gnu/libc-2.27.so
sshd    25256 ubuntu  mem    REG              252,1    14248  33986 /lib/x86_64-linux-gnu/libcom_err.so.2.1
sshd    25256 ubuntu  mem    REG              252,1   877056  35615 /usr/lib/x86_64-linux-gnu/libkrb5.so.3.3
sshd    25256 ubuntu  mem    REG              252,1   305456  35525 /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2.2
sshd    25256 ubuntu  mem    REG              252,1    39208   8592 /lib/x86_64-linux-gnu/libcrypt-2.27.so
sshd    25256 ubuntu  mem    REG              252,1   116960   3495 /lib/x86_64-linux-gnu/libz.so.1.2.11
sshd    25256 ubuntu  mem    REG              252,1    10592  64061 /lib/x86_64-linux-gnu/libutil-2.27.so
sshd    25256 ubuntu  mem    REG              252,1  2361984    102 /usr/lib/x86_64-linux-gnu/libcrypto.so.1.0.0
sshd    25256 ubuntu  mem    REG              252,1   536648   5024 /lib/x86_64-linux-gnu/libsystemd.so.0.21.0
sshd    25256 ubuntu  mem    REG              252,1   154832   1965 /lib/x86_64-linux-gnu/libselinux.so.1
sshd    25256 ubuntu  mem    REG              252,1    55848    421 /lib/x86_64-linux-gnu/libpam.so.0.83.1
sshd    25256 ubuntu  mem    REG              252,1   124848  41121 /lib/x86_64-linux-gnu/libaudit.so.1.0.0
sshd    25256 ubuntu  mem    REG              252,1    39784  37423 /lib/x86_64-linux-gnu/libwrap.so.0.7.6
sshd    25256 ubuntu  mem    REG              252,1   179152   8579 /lib/x86_64-linux-gnu/ld-2.27.so
sshd    25256 ubuntu    0u   CHR                1,3      0t0   1028 /dev/null
sshd    25256 ubuntu    1u   CHR                1,3      0t0   1028 /dev/null
sshd    25256 ubuntu    2u   CHR                1,3      0t0   1028 /dev/null
sshd    25256 ubuntu    3u  IPv4             893062      0t0    TCP host1595041234:54321->4.35.65.3:35551 (ESTABLISHED)
sshd    25256 ubuntu    4u  unix 0xffff8f19580c0000      0t0 894031 type=DGRAM
sshd    25256 ubuntu    5u  unix 0xffff8f1870dbac00      0t0 893383 type=STREAM
sshd    25256 ubuntu    6r  FIFO               0,13      0t0 894146 pipe
sshd    25256 ubuntu    7w  FIFO               0,24      0t0 893318 /run/systemd/sessions/29.ref
sshd    25256 ubuntu    8w  FIFO               0,13      0t0 894146 pipe
sshd    25256 ubuntu    9u   CHR                5,2      0t0  13408 /dev/ptmx
sshd    25256 ubuntu   11u   CHR                5,2      0t0  13408 /dev/ptmx
sshd    25256 ubuntu   12u   CHR                5,2      0t0  13408 /dev/ptmx
```





## 监控进程

```
# lsof -p 2
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND  PID USER   FD      TYPE DEVICE SIZE/OFF NODE NAME
kthreadd   2 root  cwd       DIR  252,1     4096    2 /
kthreadd   2 root  rtd       DIR  252,1     4096    2 /
kthreadd   2 root  txt   unknown                      /proc/2/exe
```



当你想要杀掉某个用户所有打开的文件、设备，可以这样

```
kill -9 `lsof -t -u www-data`
```

> -t 的作用是单独的列出 进程 id 这一列







## 监控网络

```
# lsof -i:80
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2 18050     root    4u  IPv6 949046      0t0  TCP *:http (LISTEN)
apache2 18628 www-data    4u  IPv6 949046      0t0  TCP *:http (LISTEN)
```



列出被某个进程打开所有的网络文件

```
# lsof -i -a -p 18050
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
apache2 18050 root    4u  IPv6 949046      0t0  TCP *:http (LISTEN)
```



```
# lsof -i -a -c ssh
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND   PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd      889   root    3u  IPv4  27679      0t0  TCP *:54321 (LISTEN)
sshd      889   root    4u  IPv6  27683      0t0  TCP *:54321 (LISTEN)
sshd    29446   root    3u  IPv4 925105      0t0  TCP 1.1.1.1:54321->2.2.2.2:24497 (ESTABLISHED)
sshd    29541 ubuntu    3u  IPv4 925105      0t0  TCP 1.1.1.1:54321->2.2.2.2:24497 (ESTABLISHED)
```



列出所有 tcp、udp 连接

```
lsof -i tcp
lsof -i udp
```



## 监控用戶

```
# lsof -u www-data
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs
      Output information may be incomplete.
COMMAND   PID     USER   FD      TYPE DEVICE SIZE/OFF   NODE NAME
apache2 18628 www-data  cwd       DIR  252,1     4096      2 /
apache2 18628 www-data  rtd       DIR  252,1     4096      2 /
apache2 18628 www-data  txt       REG  252,1   671392   2895 /usr/sbin/apache2
```









## 恢复文件

```
$ rm /var/log/messages

$ lsof |grep /var/log/messages

rsyslogd   1737      root    1w      REG                8,2   5716123     652638 /var/log/messages (deleted)
```



```
$ cd /proc/1737/fd/

$ ll

lrwx------ 1 root root 64 Dec 23 13:00 0 -> socket:[11442]
l-wx------ 1 root root 64 Dec 23 13:00 1 -> /var/log/messages (deleted)
l-wx------ 1 root root 64 Dec 23 13:00 2 -> /var/log/secure
lr-x------ 1 root root 64 Dec 23 13:00 3 -> /proc/kmsg
l-wx------ 1 root root 64 Dec 23 13:00 4 -> /var/log/maillog
```



```
$ head -5 1

Nov 14 03:11:11 localhost kernel: imklog 5.8.10, log source = /proc/kmsg started.
Nov 14 03:11:11 localhost rsyslogd: [origin software="rsyslogd" swVersion="5.8.10" x-pid="1241" x-info="http://www.rsyslog.com"] start
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpuset
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpu
Nov 14 03:11:11 localhost kernel: Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013



$ head -5 /var/log/message_bac

Nov 14 03:11:11 localhost kernel: imklog 5.8.10, log source = /proc/kmsg started.
Nov 14 03:11:11 localhost rsyslogd: [origin software="rsyslogd" swVersion="5.8.10" x-pid="1241" x-info="http://www.rsyslog.com"] start
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpuset
Nov 14 03:11:11 localhost kernel: Initializing cgroup subsys cpu
Nov 14 03:11:11 localhost kernel: Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013


$ cat 1 > /var/log/messages
```







# FAQ

## lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1001/gvfs

`lsof` by default checks all mounted file systems including [FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) - file systems implemented in user space which have special access rights in Linux.

As you can see in this [answer on Ask Ubuntu](https://askubuntu.com/a/23199/67132) a mounted [GVFS](https://en.wikipedia.org/wiki/GVFS) file system (special case of FUSE) is normally accessible only to the user which mounted it (the owner of `gvfsd-fuse`). Even `root` cannot access it. To override this restriction it is possible to use mount options `allow_root` and `allow_other`. The option must be also enabled in the FUSE daemon which is described for example in this [answer](https://askubuntu.com/a/401509/67132) ...but in your case you do not need to (and should not) change the access rights.



# Appendix

https://unix.stackexchange.com/questions/171519/lsof-warning-cant-stat-fuse-gvfsd-fuse-file-system

