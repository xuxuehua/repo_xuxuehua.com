---
title: "vscode"
date: 2018-11-06 17:09
---


[TOC]


# vscode

Visual Studio code



## Installation

### Ubuntu

`gdebi` could solve the dependencies of the deb

```
sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) universe"
sudo apt-get update

sudo apt-get install gdebi

sudo gdebi install *.deb
```



OR

```
sudo add-apt-repository -y "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EB3E94ADBE1229CF
sudo apt update
sudo apt -y install code
```



## Auto Save自动保存

The easiest way to turn on `Auto Save` is with the **File** > **Auto Save** toggle which turns on and off save after a delay



## Vim holding down navigation button 长按滚动

To disable the Apple press and hold for VSCode only, run this command in a terminal:

```
defaults write com.microsoft.VSCode ApplePressAndHoldEnabled -bool false
```

Then restart VSCode.

To re-enable, run this command in a terminal:

```
defaults write com.microsoft.VSCode ApplePressAndHoldEnabled -bool true
```