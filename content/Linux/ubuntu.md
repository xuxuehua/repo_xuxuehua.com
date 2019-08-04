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



### pkexec 命令

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

