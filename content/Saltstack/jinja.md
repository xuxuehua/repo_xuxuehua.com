---
title: "jinja 模块"
date: 2019-06-19 16:24
---
[TOC]

# jinja 模块

文件的同步我们可以实现了，但不一定需要改动的内容都一样，比如web中我们需要监听对应minion端自己的一个网卡IP、某个端口，就不可以直接配置文件指定IP，**需要涉及到一些变量进行获取后更改操作。现在Saltstack使用Jinja模板进行文件管理**，在jinja中使用grains、pillar等标识并加上一些变量，就可以实现上述操作，同时可以使得文件管理更灵活，使用jinja可以减少人为操作，提升工作效率。



## 修改source配置文件

我们找到port和bind这两行，我们给他设置成变量，以后sls文件里边获取到的信息就会将这里批量替换修改。

```
# Accept connections on the specified port, default is 6379.# If port 0 is specified Redis will not listen on a TCP socket.port {{ PORT }} 

# If you want you can bind a single interface, if the bind option is not# specified all the interfaces will listen for incoming connections.#
bind {{ HOST }}
```



## 修改redis.sls，设置启用jinja模板

我这里设置一个jinja模板，PORT 可以设置成需要的端口。 HOST这里就比较灵活了，只要可以获取对应信息的方式都可以，比如通过直接指定、grains获取客户端信息、pillar获取客户端信息等。这里以grains组件获取为例。
另外 {{  grains[‘fqdn_ip4’][0] }}也可以直接写入source的目标文件中，但这种不直观，不建议。**所有的操作建议都是从sls文件中实现，统一、直观。**

```
redis-service:
  pkg.installed:
    - name: redis
    - require_in:
      - file: redis-service
  file.managed:
    - name: /etc/redis.conf
    - source: salt://files/redis.conf
    - template: jinja
    - defaults:
      PORT: 6379
      HOST: {{  grains['fqdn_ip4'][0] }}
  service.running:
    - name: redis
    - enable: True
    - watch:
      - file: redis-service
```



