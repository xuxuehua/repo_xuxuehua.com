---
title: "gpg"
date: 2018-09-08 23:51
---


[TOC]


# gpg

如果你想要同时验证下载文件的可靠性（拥有者）和完整性（内容），你需要依赖于加密签名

GnuPG（GNU Privacy Guard）来检查文件的可靠性和完整性。

GPG除了可用于信息加密和解密外，还是一个很好的签名算法，能有效的校验文件的完整性



# 安装



## Ubuntu

在 Debian、Ubuntu 和其他 Debian 衍生版上：

```
sudo apt install gnupg pbuilder ubuntu-dev-tools apt-file
```



## CentOS

在 Fedora、CentOS 或者 RHEL 上:

```
sudo yum install gnupg
```





# 使用

## 生成一个键对

```
$ gpg --gen-key
...
...
pub   rsa2048 2018-09-08 [SC] [expires: 2020-09-07]
      8A943ACBD8268ED61FB3F51A1DA57D31E159DBD6
      8A943ACBD8268ED61FB3F51A1DA57D31E159DBD6
uid                      Rick Xu <rickxu1989@gmail.com>
sub   rsa2048 2018-09-08 [E] [expires: 2020-09-07]
```



一旦 key 生成完毕后，公钥和私钥会存储在 ~/.gnupg 目录

```
xhxu-mac:test-gpg xhxu$ ls -l ~/.gnupg/
total 32
srwx------  1 xhxu  254449427     0 Sep  9 00:05 S.gpg-agent
srwx------  1 xhxu  254449427     0 Sep  9 00:05 S.gpg-agent.browser
srwx------  1 xhxu  254449427     0 Sep  9 00:05 S.gpg-agent.extra
srwx------  1 xhxu  254449427     0 Sep  9 00:05 S.gpg-agent.ssh
drwx------  4 xhxu  254449427   128 Sep  9 00:05 openpgp-revocs.d
drwx------  6 xhxu  254449427   192 Sep  9 00:05 private-keys-v1.d
-rw-r--r--  1 xhxu  254449427  2888 Sep  9 00:05 pubring.kbx
-rw-------  1 xhxu  254449427  1320 Sep  9 00:05 trustdb.gpg
```



## 验证公钥的拥有者

记住key ID

```
gpg --import signing-key.asc
```

导入的公钥的指纹

```
gpg --fingerprint KEY_ID
```

> 看到 key 的指纹字符串。把这个和网站上显示的指纹做对比。如果匹配，你可以选择信任这个文件拥有者的公钥。

一旦你决定相信这个公钥(不是一定要选择相信才能继续)，你可以通过编辑 key 来设置信任级别：

```
gpg --edit-key KEY_ID
```



检查导入的 key 列表：

```
gpg --list-keys
```



## 验证文件的可靠性/完整性

```
gpg --verify FILE_KEY FILE_NAME
```

> 如果命令的输出包含了 “Good signature from <文件所属者>”，那么下载的 sha256 文件就被成功地认证和核实了。如果下载的文件的任何地方在签名后被篡改了，那么验证就会失败。



如果有 .asc 文件，即文件拥有者分别公布了一个文件和它的 asc 签名文件

```
gpg --verify file.ext.asc file.ext
```





## 导入本地密钥

```
gpg --sign-key KEY_ID
```

