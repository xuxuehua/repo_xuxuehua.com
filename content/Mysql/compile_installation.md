---
title: "compile_installation"
date: 2019-01-04 13:34
---


[TOC]



# 编译安装MySQL

## cmake 

cmake是独立于源码(out of source)的编译功能，即编译工作可以在另一个指定的目录中而非源码目录中而非源码目录中进行，这可以保证源码目录不受任何一次编译的影响，因此在同一个源码树上可以进行多次不同的编译，比如针对不同平台编译



### Installation

```
yum -y install epel-release && \
yum -y install cmake && \
yum -y install ncurses-devel openssl-devel libevent-devel jemalloc-devel
```

 

### 编译配置

#### 与make对比

cmake指定编译选项的方式不同于make，实现方式对比

```
cmake .  <=>	./configure
cmake . -LH or ccmake .  <=> 	./configure --help
```



若清理编译所生成的文件

```
rm CMakeCache.txt	<=> 	make clean
```



#### 常用选项

指定安装文件的安装路径时常用的选项

```
-DCMAKE_INSTALL_PREFIX=/usr/local/mysql
-DMYSQL_DATADIR=/data/mysql
-DSYSCONFIG=/etc
```



其他常用选项

```
-DMYSQL_TCP_PORT=3306
-DMYSQL_UNIX_ADDR=/tmp/mysql.sock
-DENABLED_LOCAL_INFILE=1
-DEXTRA_CHARSETS=all
-DDEFAULT_CHARSET=utf8
-DDEFAULT_COLLATION=utf8_general_ci  #排序规则
-DWITH_DEBUG=0
-DENABLE_PROFILING=1
```



#### 存储引擎

默认编译的存储引擎包括csv, myisam, myisammrg, heap。 若安装其他存储引擎，可以使用下列选项

```
-DWITH_INNOBASE_STORAGE_ENGINE=1
-DWITH_ARCHIVE_STORAGE_ENGINE=1
-DWITCH_BLACKHOLE_STORAGE_ENGINE=1
-DWITCH_FEDERATED_STORAGE_ENGINE=1
```



指定不编译某个存储引擎

```
-DWITHOUT_<ENGINE>_STORAGE_ENGINE=1
```

```
-DWITHOUT_EXAMPLE_STORAGE_ENGINE=1
-DWITHOUT_FEDERATED_STORAGE_ENGINE=1
-DWITHOUT_PARTITION_STORAGE_ENGINE=1
```



#### 指定某个库

明确编译进行其他功能，如SSL等

```
-DWITH_READLINE=1
-DWITH_SSL=system
-DWITH_ZLIB=system
-DWITH_LIBWRAP=0
```



## Mariadb	

### Installation mariadb-5.5.44

```
yum -y groupinstall Development Tools

wget http://ftp.hosteurope.de/mirror/archive.mariadb.org/mariadb-5.5.44/source/mariadb-5.5.44.tar.gz
```



```
groupadd -r -g 306 mysql
useradd -r -g 306 -u 306 mysql
id mysql

mkdir -p /mydata/data
chown mysql.mysql /mydata/data



cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb-5.5.44 -DMYSQL_DATADIR=/mydata/data -DSYSCONFIG=/etc -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DWITH_SSL=system -DWITH_ZLIB=system -DWITH_LIBWRAP=0 -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci

make && make install

cd /usr/local/mariadb-5.5.44/
chown -R root.mysql ./*
cd ..
ln -sv mariadb-5.5.44 mysql

scripts/mysql_install_db --user=mysql --datadir=/mydata/data

mkdir /etc/mysql
cp support-files/my-large.cnf  /etc/mysql/my.cnf
```

vim /etc/mysql/my.cnf and append

```
[mysqld]
datadir = /mydata/data
innodb_file_per_table = ON
skip_name_resolve = ON
```

```
cp support-files/mysql.server  /etc/rc.d/init.d/mysqld
chmod +x /etc/rc.d/init.d/mysqld
chkconfig --add mysqld
rm -rf /etc/my.cnf /etc/my.cnf.d/
service mysqld start

/usr/local/mysql/bin/mysql_secure_installation
```





### Installation mariadb-5.5.62

```
yum -y groupinstall Development Tools
wget http://ftp.hosteurope.de/mirror/archive.mariadb.org/mariadb-5.5.62/source/mariadb-5.5.62.tar.gz

mkdir -p /data/mysql

tar -xf mariadb-5.5.62.tar.gz && cd mariadb-5.5.62

cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb-5.5.62 -DMYSQL_DATADIR=/data/mysql -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_ARIA_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DWITH_SSL=system -DWITH_ZLIB=system -DWITH_LIBWRAP=0 -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci && make && make install
```





```
cd /usr/local/
ln -sv mariadb-5.5.62 mysql

groupadd -r -g 306 mysql
useradd -r -g 306 -u 306 mysql
id mysql

mkdir -p /data/mysql
chown mysql.mysql /data/mysql
cp ~/mariadb-5.5.62/support-files/my-large.cnf /etc/my.cnf
```

```
vim /etc/my.cnf

[mysqld]
datadir = /data/mysql
```







```
mkdir -p /usr/lib/systemd/system/
vim /usr/lib/systemd/system/mariadb.service
```

```
[Unit]
Description=MariaDB database server
After=syslog.target
After=network.target

[Service]
Type=simple
User=mysql
Group=mysql

ExecStart=/usr/local/mysql/bin/mysqld_safe --basedir=/usr/local/mysql
TimeoutSec=300
#Place temp files in a secure directory, not /tmp
PrivateTmp=false  

[Install]
WantedBy=multi-user.target
```



```
cd /usr/local/mysql
chown -R root.mysql ./*
scripts/mysql_install_db --user=mysql --datadir=/data/mysql  # 必须使用含有scripts的相对路径

ln -sv /usr/local/mysql/bin/mysql /usr/bin/mysql

systemctl start mariadb && systemctl status mariadb

mysql
```



