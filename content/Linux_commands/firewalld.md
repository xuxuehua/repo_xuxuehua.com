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









##### **Add a Port for TCP or UDP**

You do have to specify TCP or UDP and to open a port for both. You will need to add rules for each protocol.

```
firewall-cmd --permanent --add-port=22/TCP`
`firewall-cmd --permanent --add-port=53/UDP
```

##### **Remove a Port for TCP or UDP**

Using a slight variation on the above structure, you can remove a currently open port, effectively closing off that port.

```
firewall-cmd --permanent --remove-port=444/tcp
```

##### **Add a Service**

These services assume the default ports configured within the **/etc/services** configuration file; if you wish to use a service on a non-standard port, you will have to open the specific port, as in the example above.

```
firewall-cmd --permanent --add-service=ssh`
`firewall-cmd --permanent --add-service=http
```

##### **Remove a Service**

As above, you specify the remove-service option, and you can close off the port that is defined for that service.

```
firewall-cmd --permanent --remove-service=mysql
```

##### **Whitelist an IP Address**

To whitelist or allow access from an IP or range of IPs, you can tell the firewall to add a trusted source.

```
firewall-cmd --permanent --add-source=192.168.1.100
```

You can also allow a range of IPs using what is called CIDR notation. CIDR is outside the scope of this article but is a shorthand that can be used for noting ranges of IP addresses.

```
firewall-cmd --permanent --add-source=192.168.1.0/24
```

##### **Remove a Whitelisted IP Address**

To remove a whitelisted IP or IP range, you can use the **–remove-source** option.

```
firewall-cmd --permanent --remove-source=192.168.1.100
```

##### **Block an IP Address**

As the firewall-cmd tool is mostly used for opening or allowing access, rich rules are needed to block an IP. Rich rules are similar in form to the way iptables rules are written.

```
firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='192.168.1.100' reject"
```

You can again use CIDR notation also block a range of IP addresses.

```
firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='192.168.1.0/24' reject"
```

##### **Whitelist an IP Address for a Specific Port (More Rich Rules)**

We have to reach back to iptables and create another rich rule; however, we are using the accept statement at the end to allow the IP access, rather than reject its access.

```
firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.100" port protocol="tcp" port="3306" accept'
```

##### **Removing a Rich Rule**

To remove a rich rule, use the option —**remove-rich-rule**, but you have to fully specify which rule is being removed, so it is best to copy and paste the full rule, rather than try to type it all out from memory.

```
firewall-cmd --permanent --remove-rich-rule='rule family="ipv4" source address="192.168.1.100" port protocol="tcp" port="3306" accept'
```

##### **Saving Firewall Rules**

After you have completed all the additions and subtraction of rules, you need to reload the firewall rules to make them active. To do this, you again use the **firewall-cmd** tool but using the option **–reload**.

```
firewall-cmd --reload
```

##### **Viewing Firewall Rules**

After reloading the rules, you can confirm if the new rules are in place correctly with the following.

```
firewall-cmd --list-all
```

Here is an example output from the **–list-all** option, you can see that this server has a number of ports, and services open in the firewall along with a rich rule (that forwards one port to another).

```
[root@centos-7 ~]# firewall-cmd --list-allpublic (default, active)interfaces: enp1s0sources: 192.168.1.0/24services: dhcpv6-client dns http https mysql nfs samba smtp sshports: 443/tcp 80/tcp 5900-5902/tcp 83/tcp 444/tcp 3260/tcpmasquerade: noforward-ports:icmp-blocks:rich rules:rule family="ipv4" source address="192.168.1.0/24" forward-port port="5423" protocol="tcp" to-port="80"
```