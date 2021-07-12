---
title: "yum"
date: 2020-02-27 16:48
---
[toc]



# Yum





## clean 清理缓存



## check-update

检查是否有可用的更新



## deplist 依赖

显示yum 软件包的所有依赖





## makecache



# local repo

```
mkdir /var/iso
## CentOS-7-x86_64-DVD-1611.iso 所在 home/hadoop01 目录下
mount -o loop /home/hadoop01/CentOS-7-x86_64-DVD-1611.iso /var/iso
```



Local.repo

```
[local]
name=CentOS-Local
baseurl=file:///var/iso
gpgcheck=1
enabled=1   #很重要，1才启用
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```



# Remote.repo

```
[remote]
name=CentOS-Local
baseurl=http://192.168.81.61/CentOS-7
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```



```
yum clean all
yum repolist
yum install -y httpd
```



# createrepo 管理配置repo

This software bundles seve`l **.rpm** files together into a **repomd** repository.

```
sudo yum install createrepo yum-utils -y 
```



 create a directory for an HTTP repository using:

```output
sudo mkdir –p /var/www/html/repos/{base,centosplus,extras,updates}
```

Alternaticreate an FTP directory by typing the following:

```output
sudo mkdir –p /var/ftp/repos
```



Download a local copy of the official **CentOS repositories** to your server. This allows systems on the same network to install updates more efficiently.

To download the repositories, use the commands:

```output
sudo reposync -g -l -d -m --repoid=base --newest-only --download-metadata --download_path=/var/www/html/repos/
sudo reposync -g -l -d -m --repoid=centosplus --newest-only --download-metadata --download_path=/var/www/html/repos/
sudo reposync -g -l -d -m --repoid=extras --newest-only --download-metadata --download_path=/var/www/html/repos/
sudo reposync -g -l -d -m --repoid=updates --newest-only --download-metadata --download_path=/var/www/html/repos/
```

The system should reach out and download copies of the official repositories.

In the previous commands, the options are as follows:



- **–g** – lets you [remove or uninstall packages on CentOS](https://phoenixnap.com/kb/centos-uninstall-remove-package) that fail a GPG check
- **–l** – yum plugin support
- **–d** – lets you delete local packages that no longer exist in the repository
- **–m** – lets you download comps.xml files, useful for bundling groups of packages by function
- **––repoid** – specify repository ID
- **––newest-only** – only download the latest package version, helps manage the size of the repository
- **––download-metadata** – download non-default metadata
- **––download-path** – specifies the location to save the packages



use the **createrepo utility** to create a repository.

To create the repository for HTTP use the command:

```output
sudo createrepo /var/www/html
```



OR

a **repository** is different from a **rpm package**. A repository is a directory containing multiple rpms. So if you just want to install your rpm; you can just

```
yum install /path/to/package.rpm
```

if you want to start hosting your own **repository**; then you need to look into `createrepo`. For example a local directory can be turned into a repository like this:

```
mkdir /myrepo
cp package.rpm /myrepo
cd /myrepo
createrepo .
```

now you can add this directory to `yum`:

```
yum-config-manager --add-repo file:///myrepo
```

now you can also keep adding rpms to this directory (don't forget to run `createrepo` each time).



## reposync

```
reposync --gpgcheck -l --repoid=rhel-6-server-rpms --download_path=/var/www/html
```

> 将repo id为rhel-6-server-rpms 下载到本地/var/www/html





## sync repo data

```
# local linux with public network
yum install yum-plugin-downloadonly yum-utils createrepo
PKG=nginx # NAME OF THE PACKAGE TO INSTALL ON OFFLINE MACHINE
yum install --downloadonly --installroot=/tmp/$PKG-installroot --releasever=7 --downloaddir=/tmp/$PKG $PKG

cp -R /tmp/nginx* leftServer
[root@MiWiFi-R4A-srv nginx-installroot]# tree


# leftServer
yum clean all
cp nginx.rpm 
cp above to /var/www/html/centos7
createrepo --database /var/www/html/centos7
```





## yumdownloader



### --resolve

```
yumdownloader --resovle --destdir=/root/ httpd
```

> default is current directory





## repotrack & repoquery

```
repoquery -R --resolve --recursive glibc | xargs -r yumdownloader
```

repoquery can get package name

```
repoquery mysql-comm*
```





using repotrack to download rpm package and all dependencies

```
repotarck mysql-community-devel-0:5.7.34-1.el7.x86_64
```



```
repotrack -p /repos/Packages [packages]
```





# Package management

https://packagecloud.io

example

https://xiaoheidiannao.com/28356.html







# public rpm website

http://rpm.pbone.net

https://centos.pkgs.org

https://rpmfind.net

https://cbs.centos.org







