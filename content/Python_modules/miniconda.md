---
title: "miniconda"
date: 2019-07-03 10:56
---
[TOC]



# Installation

## CentOS

```
wget https://repo.continuum.io/miniconda/Miniconda2-latest-Linux-x86_64.sh
bash Miniconda2-latest-Linux-x86_64.sh
```



# Configure



## Set source repo

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```



## Create env 

```
conda create -n mytest python=3.6
```



## Check env

```
conda env list 
```



## Activate env

```
conda activate mytest
```



## Deactivate env

```
conda deactivate
```

