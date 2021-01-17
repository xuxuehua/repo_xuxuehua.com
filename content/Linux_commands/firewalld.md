---
title: "firewalld"
date: 2021-01-11 19:05
---
[toc]



# firewalld



## nat

### 目标机的设置

此处示例的目标机为 Debian & Ubuntu 系统，安装并启用了 UFW：

如需同时允许 TCP 和 UDP 入站：

```
sudo ufw allow from 中转机地址 to any port 端口号
```

如需只允许 TCP 入站：

```
sudo ufw allow proto tcp from 中转机地址 to any port 端口号
```

如需只允许 UDP 入站：

```
sudo ufw allow proto udp from 中转机地址 to any port 端口号
```

以上命令中，`中转机地址` 为 NAT VPS 的公网 IP 地址。



检查 firewalld 运行状态，输出应为 `running`：

```
firewall-cmd --state
```

接下来设置端口转发：

```
sudo firewall-cmd --zone=public --permanent --add-port 本机端口号/tcp
sudo firewall-cmd --zone=public --permanent --add-port 本机端口号/udp
sudo firewall-cmd --zone=public --permanent --add-forward-port=port=本机端口号:proto=tcp:toport=目标端口号:toaddr=目标地址
sudo firewall-cmd --zone=public --permanent --add-forward-port=port=本机端口号:proto=udp:toport=目标端口号:toaddr=目标地址
sudo firewall-cmd --zone=public --permanent --add-masquerade
sudo firewall-cmd --reload
```

其中 `目标地址` 为目标服务器的 IP 地址。

```
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
sysctl -p
firewall-cmd --permanent --add-masquerade

firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --permanent --add-port=8080/udp

firewall-cmd --permanent --add-forward-port=port=8080:proto=tcp:toaddr=1.1.1.1:toport=2670
firewall-cmd --permanent --add-forward-port=port=8080:proto=udp:toaddr=1.1.1.1:toport=2670
firewall-cmd --reload
```

