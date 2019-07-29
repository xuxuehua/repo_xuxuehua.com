---
title: "windows"
date: 2019-07-26 22:05
---
[TOC]



# Win 10 



## disable Credential Guard



```
bcdedit /set hypervisorlaunchtype off
```

restart your system



## enable Credential Guard

```
bcdedit /set hypervisorlaunchtype auto
```

restart your system

