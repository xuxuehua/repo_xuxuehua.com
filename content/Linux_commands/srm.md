---
title: "srm Secure Delete"
date: 2018-12-23 16:44
---


[TOC]





# SRM

Overwrite and remove (unlink) the files. By default use the 35-pass Gutmann
method to overwrite files.



## Installation

```
yum -y install epel-release && yum -y install srm
```



## Usage

```
Usage: srm [OPTION]... [FILE]...
```



## Options



### -d, --directory       

ignored (for compatability with rm(1))



### -f, --force         

ignore nonexistant files, never prompt



### -i, --interactive   

prompt before any removal



### -x, --one-file-system 

do not cross file system boundaries



### -s, --simple        

overwrite with single pass using 0x00 (default)



### -P, --openbsd       

overwrite with three passes like OpenBSD rm



### -D, --dod      

overwrite with 7 US DoD compliant passes



### -E, --doe     

overwrite with 3 US DoE compliant passes



### -G, --gutmann         

overwrite with 35-pass Gutmann method



### -C, --rcmp            

overwrite with Royal Canadian Mounted Police passes



### -r, -R, --recursive   

remove the contents of directories



### -v, --verbose         

explain what is being done



### -h, --help            

display this help and exit



### -V, --version        

display version information and exit