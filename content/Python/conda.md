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





## env



### create 

```
conda create --name ENV_NAME python=3.7 -y 
```



### remove 

```
conda env remove -n ENV_NAME
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

