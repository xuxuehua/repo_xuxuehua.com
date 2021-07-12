---
title: "pgpool"
date: 2021-07-05 16:18
---



[toc]



# pgpool-II

Pgpool-II is a middleware that works between PostgreSQL servers and a PostgreSQL database client. It is distributed under a license similar to BSD and MIT. It provides the following features.



## Advantages

**Connection Pooling**

Pgpool-II saves connections to the PostgreSQL servers, and reuse them whenever a new connection with the same properties (i.e. username, database, protocol version) comes in. It reduces connection overhead, and improves system's overall throughput.



**Replication**

Pgpool-II can manage multiple PostgreSQL servers. Using the replication function enables creating a realtime backup on 2 or more physical disks, so that the service can continue without stopping servers in case of a disk failure.





**Query Load Balancing**

If a database is replicated, executing a SELECT query on any server will return the same result. Pgpool-II takes an advantage of the replication feature to reduce the load on each PostgreSQL server by distributing SELECT queries among multiple servers, improving system's overall throughput. At best, performance improves proportionally to the number of PostgreSQL servers. Load balance works best in a situation where there are a lot of users executing many queries at the same time.

Pgpool-II's load balancing works best in a scenario where there are a lot of users executing many read-only queries at the same time







**Limiting Exceeding Connections**

There is a limit on the maximum number of concurrent connections with PostgreSQL, and connections are rejected after this many connections. Setting the maximum number of connections, however, increases resource consumption and affect system performance. pgpool-II also has a limit on the maximum number of connections, **but extra connections will be queued instead of returning an error immediately**.





**Watchdog**

Watchdog can coordinate multiple Pgpool-II, create a robust cluster system and avoid the single point of failure or split brain. Watchdog can perform lifecheck against other pgpool-II nodes, to detect a fault of Pgpoll-II. If active Pgpool-II goes down, standby Pgpool-II can be promoted to active, and take over Virtual IP.



**In Memory Query Cache**

In memory query cache allows to save a pair of SELECT statement and its result. If an identical SELECTs comes in, Pgpool-II returns the value from cache. Since no SQL parsing nor access to PostgreSQL are involved, using in memory cache is extremely fast. On the other hand, it might be slower than the normal path in some cases, because it adds some overhead of storing cache data.





# load balancing mode

Pgpool-II has two load balancing modes:

- [Session level load balancing](https://www.pgpool.net/docs/latest/en/html/runtime-config-load-balancing.html) (default)
- [Statement level load balancing](https://www.pgpool.net/docs/latest/en/html/runtime-config-load-balancing.html#GUC-STATEMENT-LEVEL-LOAD-BALANCE)

Pgpool-II randomly selects a **load balancing node** from all available PostgreSQL servers according to [backend_weight](https://www.pgpool.net/docs/latest/en/html/runtime-config-backend-settings.html#GUC-BACKEND-WEIGHT) and sends READ queries to that load balancing node.
The difference between [Session level load balancing](https://www.pgpool.net/docs/latest/en/html/runtime-config-load-balancing.html) and [Statement level load balancing](https://www.pgpool.net/docs/latest/en/html/runtime-config-load-balancing.html#GUC-STATEMENT-LEVEL-LOAD-BALANCE) is the timing to select the **load balancing node**. 



## session level load balancing (default)



By default, load balancing mode is "**session level**" which means the load balancing node is determined when a client connects to Pgpool-II. Once the load balancing node is selected, it will not change until the client closes the session.

 

[![img](/Users/rxu/coding/github/repo_xuxuehua.com/content/Postgresql/pgpool.assets/session_mode.jpg)









# installation



## Amazon linux2

```
yum groupinstall -y "Development Tools"
sudo amazon-linux-extras install epel -y 
sudo yum-config-manager --enable epel
yum install -y http://mirror.centos.org/centos/7/extras/x86_64/Packages/centos-release-scl-rh-2-3.el7.centos.noarch.rpm
yum install -y llvm-toolset-7-clang
yum install -y postgresql12-devel
yum install -y https://www.pgpool.net/yum/rpms/4.2/redhat/rhel-7-x86_64/pgpool-II-pg12-4.2.3-1pgdg.rhel7.x86_64.rpm
```









# Configuring pgpool

Most of the pgpool configuration is done in `/usr/local/etc/pgpool.conf`, but for yum installed version, please search it manually.

The following table shows the important settings.

| **Setting**                    | **Value**                                  | **Notes**                                                  |
| ------------------------------ | ------------------------------------------ | ---------------------------------------------------------- |
| **listen_addresses**           | ‘*’                                        | Allow incoming connections on all interfaces.              |
| **backend_hostname0**          | The Amazon Aurora cluster endpoint         |                                                            |
| **backend_port0**              | 3306                                       | Amazon Aurora in PostgreSQL mode uses port 3306.           |
| **backend_flag0**              | ALWAYS_MASTER                              | Don’t let pgpool try to fail over.                         |
| **backend_hostname1**          | The Amazon Aurora reader endpoint          |                                                            |
| **backend_port1**              | 3306                                       | Amazon Aurora in PostgreSQL mode uses port 3306.           |
| **enable_pool_hba**            | On                                         | Amazon Aurora requires this for authentication.            |
| **pool_passwd**                | ‘pool_passwd’                              | Set location of authentication file.                       |
| **Ssl**                        | On                                         | Amazon Aurora uses Secure Sockets Layer (SSL) connections. |
| **replication_mode**           | Off                                        |                                                            |
| **load_balance_mode**          | On                                         |                                                            |
| **master_slave_mode**          | On                                         |                                                            |
| **master_slave_sub_mode**      | Stream                                     |                                                            |
| **sr_check_period**            | 0                                          |                                                            |
| **health_check_\***            | Configure with master account credentials. |                                                            |
| **fail_over_on_backend_error** | Off                                        |                                                            |



## generate md5 hash value

```
/usr/local/bin/pg_md5 -m -u ${DatabaseUser} ${DatabasePassword}
```

The corresponding user and md5 will insert into **pool_passwd** file



## setup md5 authentication

configure MD5 authentication in **pool_hba.conf**

```
# TYPE  DATABASE    USER        CIDR-ADDRESS          METHOD
host    all         all         0.0.0.0/0               md5
host		all					rxu					source_ip_of_10.23.1.0/24	md5 
```



## enable hba

```
vim 
enable_pool_hba = on
```





## enable Load balance

To enable load balancing in Pgpool-II, turn on [load_balance_mode](https://www.pgpool.net/docs/latest/en/html/runtime-config-load-balancing.html#GUC-LOAD-BALANCE-MODE)

```
load_balance_mode = on
```





# Appendix

https://aws.amazon.com/blogs/database/a-single-pgpool-endpoint-for-reads-and-writes-with-amazon-aurora-postgresql/

https://www.pgpool.net/mediawiki/index.php/Main_Page

https://b-peng.blogspot.com/2020/12/load-balancing-in-pgpool.html
