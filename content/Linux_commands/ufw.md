---
title: "ufw"
date: 2021-01-11 19:07
---
[toc]



# udw

## nat

此方法在 Debian & Ubuntu 下较为简便（Ubuntu 18.04 默认使用 UFW）。

首先要修改 `/etc/sysctl.conf` 文件：

```
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

修改 `/etc/default/ufw` ：

```
sudo vim /etc/default/ufw
/etc/default/ufwDEFAULT_FORWARD_POLICY="ACCEPT"
```

修改 `/etc/ufw/before.rules` ：

```
sudo vim /etc/ufw/before.rules
```

在 `*filter` 之前添加：

```
/etc/ufw/before.rules# nat table rules
*nat
:PREROUTING ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]

# port forwarding
-A PREROUTING -p tcp --dport 本机端口号 -j DNAT --to-destination 目标地址:目标端口号
-A PREROUTING -p udp --dport 本机端口号 -j DNAT --to-destination 目标地址:目标端口号
-A POSTROUTING -p tcp -d 目标地址 --dport 目标端口号 -j SNAT --to-source 本机内网地址
-A POSTROUTING -p udp -d 目标地址 --dport 目标端口号 -j SNAT --to-source 本机内网地址

# commit to apply changes
COMMIT
```

其中，`目标地址` 为目标服务器的 IP 地址，`本机内网地址` 为本机在内部局域网的 IP 地址。

重启 UFW：

```
sudo ufw disable && sudo ufw enable
```