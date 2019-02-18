---
title: "imagemagick"
date: 2018-11-15 22:57
---


[TOC]

# imagemagick

official website -> https://www.imagemagick.org



## 安装

```
yum install imagemagick
```



## convert

So to create a thumbnail of the abc.png image with 200px width, you need to type:
`$ convert -thumbnail 200 abc.png thumb.abc.png`

To create a thumbnail of the abc.png image with 200px height, you need to type:
`$ convert -thumbnail x200 abc.png thumb.abc.png`



## script

```
#!/bin/bash
FILES="$@"
for i in $FILES
do
echo "Prcoessing image $i ..."
/usr/bin/convert -thumbnail 200 $i thumb-$i
done
```

