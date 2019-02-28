---
title: "sublime"
date: 2018-12-18 20:03
---


[TOC]



# sublime



## Vintage Mode (Enable VIM)

Preferences -> Settings	

```
{
	"font_size": 17,
	"ignored_packages": [],
	"vintage_start_in_command_mode": true,
}
```



## Installation

### Ubuntu

Install the GPG key:

```
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
```

Ensure apt is set up to work with https sources:

```
sudo apt-get install apt-transport-https
```

Select the channel to use:

Stable

```
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list 
```



Dev

```
echo "deb https://download.sublimetext.com/ apt/dev/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
```



Update apt sources and install Sublime Text

```
sudo apt-get -y update
sudo apt-get -y install sublime-text
```



### CentOS

Install the GPG key:

```
sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
```

Select the channel to use:

Stable

```
sudo yum-config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
```



Dev

```
sudo yum-config-manager --add-repo https://download.sublimetext.com/rpm/dev/x86_64/sublime-text.repo
```

Update yum and install Sublime Text

```
sudo yum install sublime-text
```