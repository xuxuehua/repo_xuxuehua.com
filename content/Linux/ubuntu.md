---
title: "ubuntu"
date: 2018-08-27 18:18
---

[TOC]

# Ubuntu



## Basic development tools

```
sudo apt-get install -y build-essential && \
sudo apt install -y libpcre3* && \ 
sudo apt install -y libzip4* && \ 
sudo apt install -y python3-dev && \
sudo apt install -y libpython3.7-dev && \
sudo apt install -y git vim curl wget screen proxychains
```



## apt/source.list for 18.04.1

```
# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://us.archive.ubuntu.com/ubuntu/ bionic main restricted
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://us.archive.ubuntu.com/ubuntu/ bionic-updates main restricted
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://us.archive.ubuntu.com/ubuntu/ bionic universe
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic universe
deb http://us.archive.ubuntu.com/ubuntu/ bionic-updates universe
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://us.archive.ubuntu.com/ubuntu/ bionic multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic multiverse
deb http://us.archive.ubuntu.com/ubuntu/ bionic-updates multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
# deb http://us.archive.ubuntu.com/ubuntu/ bionic-backports main restricted universe multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ bionic-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu bionic partner
# deb-src http://archive.canonical.com/ubuntu bionic partner

deb http://security.ubuntu.com/ubuntu bionic-security main restricted
# deb-src http://security.ubuntu.com/ubuntu bionic-security main restricted
deb http://security.ubuntu.com/ubuntu bionic-security universe
# deb-src http://security.ubuntu.com/ubuntu bionic-security universe
deb http://security.ubuntu.com/ubuntu bionic-security multiverse
# deb-src http://security.ubuntu.com/ubuntu bionic-security multiverse
```



### Update time

确保时间已更新

```
sudo timedatectl set-ntp off
sudo timedatectl set-ntp on
```



### aliyun

cp /etc/apt/sources.list /etc/apt/sources.list.bak

在/etc/apt/sources.list文件前面添加如下条目

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

sudo apt-get update
sudo apt-get upgrade



### 163

```
deb http://mirrors.163.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ bionic-backports main restricted universe multiverse
```







## Guest additions (copy&paste)

 install the package **virtualbox-guest-additions-iso** in the **host** Ubuntu.

```
sudo apt-get install virtualbox-guest-additions-iso
```

The .iso file with an image of the OSE edition of the guest additions CD will install in the host directory `/usr/share/virtualbox/VBoxGuestAdditions.iso`. Mount this .iso file as a CD in your virtual machine's settings. In the guest you will then have access to a CD-ROM with the installer.



In case the Guest Additions fail to build we may have to install the Linux kernel headers (see [How do I install kernel header files?](https://askubuntu.com/q/75709/88802)) or [**build-essential** ![Install build-essential](https://i.imgur.com/uRtxs.png)](https://apps.ubuntu.com/cat/applications/build-essential) tools in addition. It is also recommended to have [**dkms** ![Install dkms](https://i.imgur.com/uRtxs.png)](https://apps.ubuntu.com/cat/applications/dkms) installed (see below - Note 4). You can run this command in a terminal to install both:

```
sudo apt install build-essential dkms
```

Selecting *Devices -> Install Guest Additions* (or press Host+D from the Virtual Box Manager) the Guest Additions CD .iso will be loaded but **not installed** in your guest OS. To install we need to run the installer script `VBoxLinuxAdditions.run` as root or from the Autorun Prompt (see below).





## Ubuntu Chinese Setup

This page describes how to install Chinese features in non-Chinese versions of Ubuntu 18.04. The preview version of this new GNOME-based interface,[ Ubuntu 17.10,](https://www.pinyinjoe.com/faq/ubuntu-1710-ibus-fcitx.htm) required its own FAQ page. If you need an earlier version, see the setup pages for [Ubuntu 12.04-17.04](https://www.pinyinjoe.com/linux/ubuntu-12-chinese-setup.htm), [Ubuntu 11](https://www.pinyinjoe.com/linux/ubuntu-11-chinese-setup.htm), and [Ubuntu 10](https://www.pinyinjoe.com/linux/ubuntu-10-chinese-setup.htm)(they share the same [input methods](https://www.pinyinjoe.com/linux/ubuntu-10-chinese-input-pinyin-chewing.htm) and [fonts](https://www.pinyinjoe.com/linux/ubuntu-10-chinese-fonts-openoffice-language-features.htm)) or [Ubuntu 9](https://www.pinyinjoe.com/linux/ubuntu-chinese-setup.htm).

Canonical has dropped the Unity interface and returned to GNOME. The basic installation includes the [IBus input framework ![Open new window](https://www.pinyinjoe.com/images/arrow-new-site.jpg)](https://en.wikipedia.org/wiki/Intelligent_Input_Bus) — which may or may not have everything you need — and many users will want to manually install the [fcitx framework ![Open new window](https://www.pinyinjoe.com/images/arrow-new-site.jpg)](https://en.wikipedia.org/wiki/Fcitx) which is now the standard in China.

 

![No need to install a fully localized Chinese Ubuntu desktop. Just click English](https://www.pinyinjoe.com/images/ubuntu/1710/ubuntu-17-install-english-display.jpg)

If you're doing a clean installation, at the Welcome screen it's OK to select English as shown here. --->

*It is not necessary to use a Chinese language desktop.* Chinese input methods and interfaces will still be available. You can select "English" or another language now, and use Chinese menus later if you wish.

 

**Adding Chinese language support:**

After your install is complete, log in, then click on "Activities" in the upper left corner or tap the Super key (Windows/Ubuntu key).

In the Activities search box, type "language":

![Ubuntu Dash : search for Language Support](https://www.pinyinjoe.com/images/ubuntu/1710/ubuntu-17-search-language-support.jpg)

Double-click Language Support (or just press your <Enter> key).

 

In the Language Support panel, click the "Install / Remove Languages..." button:

![Ubuntu Language Support panel](https://www.pinyinjoe.com/images/ubuntu/1710/language-support.jpg)

 

In the Installed Languages panel, select Chinese (Simplified and/or Traditional), and then click the "Apply Changes" button (not shown here), after which the necessary fonts and other bits will be downloaded and installed.

![Ubuntu Installed Languages panel : installing Chinese](https://www.pinyinjoe.com/images/ubuntu/1710/installed-languages.jpg)

 

After the file installation process is complete, log out and log back in:

![Ubuntu 11 logout](https://www.pinyinjoe.com/images/ubuntu/1804/logout.jpg)



**Let's take a ride on the IBus**
After logging in, click again on "Activities" at the upper left or tap the Super key (Windows/Ubuntu key). In the search box type "region", then select Region & Language:

![img](https://www.pinyinjoe.com/images/ubuntu/1804/region.jpg)

 

Click the "Manage Installed Language" button to reopen the Language Support panel, which will cause the system to automatically check for any missing pieces necessary for Chinese support. Allow that install to proceed, then close Language Support and return to Region and Language. Then click the "+" button to add input methods.

![img](https://www.pinyinjoe.com/images/ubuntu/1710/region-and-language-cr.jpg)

 

After clicking the "+" button, you'll see "Add an Input Source":

![img](https://www.pinyinjoe.com/images/ubuntu/1710/add-an-input-source.jpg)

 

### Chinese (China) input methods

If you select "Chinese (China)" from the above list, you'll see the following choices. As *Pinyin* Joe I'm looking for phonetic input methods. The default install includes Intelligent Pinyin, which supports Simplified and Traditional characters. After selecting it here, log out and log in, and then it should show up on your input menu.

![img](https://www.pinyinjoe.com/images/ubuntu/1804/add-input-source-china.jpg)

 

The gear button in the Region and Language panel will allow you to select Simplified or Traditional and other options:

![img](https://www.pinyinjoe.com/images/ubuntu/1804/gear-intelligent-pinyin.jpg)

*Note that the Traditional characters produced by this IME will be in mainland GB encoding.* If you are sharing messages or documents with anyone using a system set to Taiwan/HK/Macau Big5 encoding, your text could be corrupted into unrecoverable garbage characters. For most situations requiring Traditional characters, it is best to use the China (Hong Kong) IME.

 

Other input methods are available via manual install, including the (Simplified character only) SunPinyin which in some ways offers a superior candidate list algorithm. I have not yet played with this, but as an example to install SunPinyin you would drop into Terminal and enter this:

```
sudo apt-get install ibus-sunpinyin
```



### Chinese (Hong Kong) input methods

If you select Chinese (Hong Kong) you'll find that the basic install offers no phonetic input at all, unless Chewing was carried over from a previous install during an upgrade...and if it still works. QuickClassic is a version of the non-phonetic Quick (簡易) input method popular with people in Hong Kong, but I need Chewing (as in "Zhuyin", though it supports both Bopomofo and Pinyin). 

![img](https://www.pinyinjoe.com/images/ubuntu/1710/add-an-input-source-3.jpg)

I've tried to add Chewing without success, using this in Terminal:

**sudo apt-get install ibus-chewing**

I get a message back saying the latest version is already installed, so nothing happens and I'm still unable to bring Chewing up in any panel or menu. I'm still trying to figure out why. This is yet another reason to go back to fcitx.

 

**Forward with fcitx?**

Maybe IBus just doesn't take you where you need to go. Fcitx has been the standard in China for Ubuntu Kylin for quite some time now, and if you'd like to install it see this article posted by a member of the fcitx team when this problem presented itself in 17.10:

[https://www.csslayer.info/wordpress/fcitx-dev/how-to-use-fcitx-on-ubuntu-17-10/ ![open new site in new window](https://www.pinyinjoe.com/images/arrow-new-site.jpg)](https://www.csslayer.info/wordpress/fcitx-dev/how-to-use-fcitx-on-ubuntu-17-10/)

 

For either framework, to learn more about input methods, fonts, or OpenOffice/LibreOffice features see the next steps below.

 

### fcitx

```
sudo apt install fcitx*
```



#### telegram

1. 修改快捷方式

```
vim /home/ubuntu/.local/share/applications/telegramdesktop.desktop

Exec=env QT_IM_MODULE=fcitx /opt/telegram/Telegram -- %u
```



2. 强制增加变量

```
sudo vim /home/ubuntu/.bashrc

export XIM_PROGRAM=fcitx
export XIM=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```





## 新用户 启动.bashrc

### .profile

```
# ~/.profile: executed by Bourne-compatible login shells.

if [ "$BASH" ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi

mesg n || true
```





### .bashrc

```
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
[ -z "$PS1" ] && return

# don't put duplicate lines in the history. See bash(1) for more options
# ... or force ignoredups and ignorespace
#HISTCONTROL=ignoredups:ignorespace

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
#HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "$debian_chroot" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
	# We have color support; assume it's compliant with Ecma-48
	# (ISO/IEC-6429). (Lack of such support is extremely rare, and such
	# a case would tend to support setf rather than setaf.)
	color_prompt=yes
    else
	color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
#if [ -f /etc/bash_completion ] && ! shopt -oq posix; then
#    . /etc/bash_completion
#fi
```



## Desktop 

```
apt install tasksel

# tasksel --list-tasks
u manual        Manual package selection
u lubuntu-live  Lubuntu live CD
u ubuntu-gnome-live     Ubuntu GNOME live CD
u ubuntu-live   Ubuntu live CD
u ubuntu-mate-live      Ubuntu MATE Live CD
u ubuntustudio-dvd-live Ubuntu Studio live DVD
u ubuntustudio-live     Ubuntu Studio live CD
u xubuntu-live  Xubuntu live CD
i cloud-image   Ubuntu Cloud Image (instance)
u dns-server    DNS server
u lamp-server   LAMP server
u mail-server   Mail server
u postgresql-server     PostgreSQL database
u samba-server  Samba file server
u ubuntu-desktop        Ubuntu desktop
u ubuntu-usb    Ubuntu desktop USB
u virt-host     Virtual Machine host
i openssh-server        OpenSSH server
i server        Basic Ubuntu server
```



### pkexec  修复sudoer 

修复破坏的sudoer 文件

```
pkexec vi /etc/sudoer
```



## Encryped grub

```
grub-mkpasswd-pbkdf2
```

```
sudo vim  /etc/grub.d/40_custom

set superusers="ubuntu"
password_pbkdf2 ubuntu HASH_VALUE
```

```
sudo update-grub
```





# 中文语言显示

首先，安装中文支持包language-pack-zh-hans：

```
$ sudo apt-get install language-pack-zh-hans
```

然后，修改/etc/environment（在文件的末尾追加）：

```
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh:en_US:en"
```

再修改/var/lib/locales/supported.d/local(没有这个文件就新建，同样在末尾追加)：

```
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_CN.GBK GBK
zh_CN GB2312
```

最后，执行命令：

```
$ sudo locale-gen
```

对于中文乱码是空格的情况，安装中文字体解决。

```
$ sudo apt-get install fonts-droid-fallback ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
```

以上，问题解决，中文显示正常。:)



## software center

```
sudo apt update
sudo apt install snap
sudo snap install snap-store
```





# Video



## MPEG-4 AAC decoder, H.264 (Main Profile) decoder 

```
sudo apt install -y libdvdnav4 libdvd-pkg gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly libdvd-pkg
sudo apt install -y ubuntu-restricted-extras
sudo dpkg-reconfigure libdvd-pkg
```



check media info

```
sudo apt install mediainfo -y
```

or

```
sudo apt install ffmpeg -y

ffprobe 1.mp4ls /tmp	
```





# flameshot 截图 

```text
sudo apt install flameshot
```

如果你在安装过程中遇到问题，可以按照[官方的安装说明](https://link.zhihu.com/?target=https%3A//github.com/lupoDharkael/flameshot%23installation)进行操作。安装完成后，你还需要进行配置。尽管可以通过搜索来随时启动 Flameshot，但如果想使用 ctrl+shirt+p 键触发启动，则需要指定对应的键盘快捷键。以下是相关配置步骤：

- 进入系统设置中的“键盘设置” (KDE 是 Shortcuts)

- 页面中会列出所有现有的键盘快捷键，拉到底部就会看见一个 “+” 按钮

- 点击 “+” 按钮添加自定义快捷键并输入以下两个字段：

- - “名称”： flameshot
    - “命令”： `/usr/bin/flameshot gui`





# promote user as administrator

```
sudo usermod -aG sudo USERNAME
```



# Google Drive



## go installation

Extract the archive you downloaded into /usr/local, creating a Go tree in /usr/local/go.
Important: This step will remove a previous installation at /usr/local/go, if any, prior to extracting. Please back up any data before proceeding.

For example, run the following as root or through sudo:

```
wget https://dl.google.com/go/go1.16.linux-amd64.tar.gz

rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.linux-amd64.tar.gz
```

Add /usr/local/go/bin to the PATH environment variable.
You can do this by adding the following line to your $HOME/.profile or /etc/profile (for a system-wide installation):

```
export PATH=$PATH:/usr/local/go/bin

Note: Changes made to a profile file may not apply until the next time you log into your computer. To apply the changes immediately, just run the shell commands directly or execute them from the profile using a command such as source $HOME/.profile.
```

Verify that you've installed Go by opening a command prompt and typing the following command:

```
$ go version
```

Confirm that the command prints the installed version of Go.

Confirm that the command prints the installed version of Go.



## Drive sync



```
vim ~/.bashrc
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/gopath
export PATH=$GOPATH:$GOPATH/bin:$PATH
```



From sources
To install from the latest source, run with root privilege:

```
sudo apt -y install build-essential

go get -u github.com/odeke-em/drive/cmd/drive
```


Otherwise:

In order to address issue #138, where debug information should be bundled with the binary, you'll need to run:

```
go get github.com/odeke-em/drive/drive-gen && drive-gen
```



In case you need a specific binary e.g for Debian folks issue #271 and issue 277

```
go get -u github.com/odeke-em/drive/drive-google
```


That should produce a binary drive-google

OR

To bundle debug information with the binary, you can run:

```
go get -u github.com/odeke-em/drive/drive-gen && drive-gen drive-google
```



start by below	

```
drive init
```







# dropbox

## dbxcli

```
wget https://github.com/dropbox/dbxcli/releases/download/v3.0.0/dbxcli-linux-amd64

sudo mv dbxcli-linux-amd64 /usr/bin/dbxcli
sudo chmod +x /usr/bin/dbxcli
```



```
dbxcli put myfile /myfolder/myfile
```



# Libreoffice



## save and quit

```
xdotool search --name FILENAME key ctrl+s
xdotool search --name FILENAME key ctrl+q
```





# Qv2ray

```
sudo apt install curl -y
curl -sS https://qv2ray.github.io/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://qv2ray.github.io/debian/ stable main" | sudo tee /etc/apt/sources.list.d/qv2ray.list
sudo apt update
```

Or

```
sudo snap install qv2ray
```



core files

```
sudo apt update
sudo apt install wget unzip -y
mkdir -p /home/vnc/qv2ray/
cd /home/vnc/qv2ray/
wget https://github.com/v2ray/v2ray-core/releases/download/v4.28.2/v2ray-linux-64.zip
unzip v2ray-linux-64.zip -d vcore
rm -rf v2ray-linux-64.zip
```



## v2ray client

edit config.json

```
{
    "inbounds":[
        {
            "port":1089,
            "listen":"127.0.0.1",
            "protocol":"socks",
            "settings":{
                "udp":true
            }
        }
    ],
    "outbounds":[
        {
            "mux":{

            },
            "protocol":"vmess",
            "sendThrough":"0.0.0.0",
            "settings":{
                "vnext":[
                    {
                        "address":"",
                        "port":26706,
                        "users":[
                            {
                                "alterId":8,
                                "id":"1886c538-3a62-49f2-864c-dee66841f9d3",
                                "level":0,
                                "security":"auto",
                                "testsEnabled":"none"
                            }
                        ]
                    }
                ]
            },
            "streamSettings":{
                "dsSettings":{
                    "path":"/"
                },
                "httpSettings":{
                    "host":[

                    ],
                    "path":"/"
                },
                "kcpSettings":{
                    "congestion":false,
                    "downlinkCapacity":20,
                    "header":{
                        "type":"none"
                    },
                    "mtu":1350,
                    "readBufferSize":1,
                    "seed":"",
                    "tti":20,
                    "uplinkCapacity":5,
                    "writeBufferSize":1
                },
                "network":"tcp",
                "quicSettings":{
                    "header":{
                        "type":"none"
                    },
                    "key":"",
                    "security":""
                },
                "security":"none",
                "sockopt":{
                    "mark":0,
                    "tcpFastOpen":false,
                    "tproxy":"off"
                },
                "tcpSettings":{
                    "header":{
                        "request":{
                            "headers":{

                            },
                            "method":"GET",
                            "path":[

                            ],
                            "version":"1.1"
                        },
                        "response":{
                            "headers":{

                            },
                            "reason":"OK",
                            "status":"200",
                            "version":"1.1"
                        },
                        "type":"none"
                    }
                },
                "tlsSettings":{
                    "allowInsecure":false,
                    "allowInsecureCiphers":false,
                    "alpn":[

                    ],
                    "certificates":[

                    ],
                    "disableSessionResumption":true,
                    "disableSystemRoot":false,
                    "serverName":""
                },
                "wsSettings":{
                    "headers":{

                    },
                    "path":"/"
                }
            },
            "tag":"outBound_PROXY"
        }
    ],
    "routing":{
        "domainStrategy":"IPOnDemand",
        "rules":[
            {
                "type":"field",
                "ip":[
                    "geoip:private"
                ],
                "outboundTag":"direct"
            }
        ]
    }
}
```



```
./v2ray -config=config.json
```



and finally connect to 1089





# xrdp

Copy the code below into your Terminal console and let it execute. This will create a file called .xsessionrc. This file is kind of login script will load your desktop configuration into the remote session. After the file is created, login back to xRDP session and see if the desktop look like the one you have when logged on locally.

```
cat <<EOF > ~/.xsessionrc
echo export GNOME_SHELL_SESSION_MODE=ubuntu
export XDG_CURRENT_DESKTOP=ubuntu:GNOME
export XDG_CONFIG_DIRS=/etc/xdg/xdg-ubuntu:/etc/xdg
EOF 
```





# Apache Guacamole

https://www.linuxbabe.com/ubuntu/apache-guacamole-remote-desktop-ubuntu-20-04

https://github.com/neutrinolabs/pulseaudio-module-xrdp



# teamviewer

```
wget https://download.teamviewer.com/download/linux/teamviewer_amd64.deb
```



# System load in taskbar

```
sudo apt install indicator-multiload
```

Search for System Load Indicator in dash and launch it



# Wifi Scan authentication

System Policy prevents WiFi Scans



To avoid such dialog box while trying to connect to a WiFi network, you will need to create your own pkla file.  In our scenario, we will create a file called **47-allow-wifi-scans.pkla** which will be created under /etc/polkit-1/localauthority/50-local.d/.  You need administrative privileges in order to write in this location !

The initial pkla file would contains the following information 

```
vim /etc/polkit-1/localauthority/50-local.d/47-allow-wifi-scans.pkla
[Allow Wifi Scan]
Identity=unix-user:*
Action=org.freedesktop.NetworkManager.wifi.scan
ResultAny=yes
ResultInactive=yes
ResultActive=yes
```









# aptitude (fix dependencies)

```
sudo apt-get install aptitude


apt clean
apt autoclean 
aptitude install PACKAGE_NAME
```



# Appendix

https://zhuanlan.zhihu.com/p/45919661

https://golang.org/doc/install

https://github.com/odeke-em/drive#requirements

https://superuser.com/questions/1102630/how-can-i-make-an-open-libreoffice-save-a-file-without-using-the-gui/1102670

https://askubuntu.com/questions/1233088/xrdp-desktop-looks-different-when-connecting-remotely

https://www.v2ray.com/en/welcome/install.html

https://c-nergy.be/blog/?p=16310

