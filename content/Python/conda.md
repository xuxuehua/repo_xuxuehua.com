---
title: "conda"
date: 2020-03-08 22:13
---
[toc]



# conda



## config



```
conda config --set auto_activate_base false
```



### 国内镜像

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --set show_channel_urls yes
conda create --name python3 python=3.7.4
```



## create 

```
conda create --name ENV_NAME python=3.7 -y 
```



```
conda create --name python3 python=3.7.4
```



## remove 

```
conda remove -n ENV_NAME
```





## activate 

```
conda activate ENV_NAME
```



## deactivate 



```
conda deactivate
```





deactivate current env

```py
 source deactivate
```



## info

```
conda info --envs
```



# FAQ



## disable auto_activate_base

```
conda config --set auto_activate_base false
```

