---
title: "chrome"
date: 2018-07-26 18:34
---

[TOC]



# Chrome 



## Debian 安装



Download the Google signing key and install it.

```
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
```



Set up Google Chrome repository.

```
echo "deb http://dl.google.com/linux/chrome/deb/ stable main" | sudo tee /etc/apt/sources.list.d/google-chrome.list
```



Update repository index.

```
sudo apt-get update
```



Install Google Chrome using the below command.

```
apt-get -y install google-chrome-stable
```

Want to try Google Chrome beta, run:

```
apt-get -y install google-chrome-beta
```



访问

Command Line

```
google-chrome
```

OR

```
google-chrome-stable
```

**Google Chrome beta:**

```
google-chrome-beta
```





## Ubuntu 安装

```
wget -O ~/chrome.deb https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```



OR



```
sudo vim /etc/apt/sources.list
deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main
```

```
wget https://dl.google.com/linux/linux_signing_key.pub
```

Then use **apt-key** to add it to your keyring so the package manager can verify the integrity of Google Chrome package.

```
sudo apt-key add linux_signing_key.pub
```

Now update package list and install the stable version of Google Chrome.

```
sudo apt update -y 

sudo apt install -y google-chrome-stable
```





If you want to install the beta or unstable version of Google Chrome, use the following commands:

```
sudo apt install google-chrome-beta

sudo apt install google-chrome-unstable
```



## Centos 安装

```bash
cat << EOF > /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/x86_64
enabled=1
gpgcheck=1
gpgkey=https://dl.google.com/linux/linux_signing_key.pub
EOF
```

```
yum -y install google-chrome-stable
```







# chrome-remote-desktop



## Centos

```
yum -y install epel-release && yum -y upgrade && yum -y update && yum -y install chrome-remote-desktop 
```



## Ubuntu

```
sudo apt update
sudo apt-get install --assume-yes wget
wget https://dl.google.com/linux/direct/chrome-remote-desktop_current_amd64.deb
sudo dpkg --install chrome-remote-desktop_current_amd64.deb
sudo apt install --assume-yes --fix-broken
```



```
echo $(hostname -I | cut -d\  -f1) $(hostname) | sudo tee -a /etc/hosts
```



After installing the Debian package `chrome-remote-desktop_current_amd64.deb`, make sure the current user is part of the `chrome-remote-desktop` group:

```
sudo usermod -a -G chrome-remote-desktop $USER
```



You need to allow Chrome Remote Desktop to access your account. If you approve, the page displays a command line for Debian Linux that looks like the following:

```
DISPLAY= /opt/google/chrome-remote-desktop/start-host \    --code="4/xxxxxxxxxxxxxxxxxxxxxxxx" \    --redirect-url="https://remotedesktop.google.com/_/oauthredirect" \    --name=
```



Go to privacy to disable screen lock

and change user vnc as administrator user

```
adduser vnc sudo 
```



Go to below to install snap core service, after that you could use snap to install those softwares

```
https://snapcraft.io/core
```

