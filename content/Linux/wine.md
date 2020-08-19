---
title: "wine"
date: 2020-07-22 01:00
---
[toc]



# wine



## centos 7

if install as vnc and display :1

```
export DISPLAY=:1
```



```
cd /usr/bin/
wget -c https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks
chmod +x winetricks
```



```
yum -y install cabextract

export WINEARCH=win32 WINEPREFIX=/opt/wine/tencent
winecfg    # 选择 windows 7
winetricks riched20 richtx32 corefonts
./winetricks corefonts gdiplus riched20 riched30 wenquanyi
wine /opt/tools/wine/WeChatSetup.exe        # 安装 WeChat
```





### 解决字体发虚问题
winecfg ---> 显示 ---> 屏幕分辨率 ---> 120 dpi





## ubuntu 1604

```
sudo dpkg --add-architecture i386
wget -qO - https://dl.winehq.org/wine-builds/winehq.key | sudo apt-key add -


sudo apt-get update
sudo apt-get install software-properties-common -y 
sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ xenial main'

sudo apt update
sudo apt install --install-recommends winehq-stable
```



```
sudo apt-get install winetricks

```



## ubunt 1804



```
sudo dpkg --add-architecture i386
wget -qO - https://dl.winehq.org/wine-builds/winehq.key | sudo apt-key add -
sudo apt-add-repository 'deb https://dl.winehq.org/wine-builds/ubuntu/ bionic main'
sudo add-apt-repository ppa:cybermax-dexter/sdl2-backport
```

```
sudo apt update
sudo apt install --install-recommends winehq-stable
```

If you face unmet dependencies error during installation, use the following commands to install winehq using aptitude.

```
sudo apt install aptitude
sudo aptitude install winehq-stable
```