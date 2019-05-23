---
title: "imagemagick"
date: 2018-11-15 22:57
---


[TOC]

# imagemagick

official website -> https://www.imagemagick.org



## 安装

```
yum -y install gcc php-devel php-pear
yum -y install ImageMagick ImageMagick-devel
```



## convert



### -draw

使用-draw选项还可以在图像里面添加文字：

```
convert -fill black -pointsize 60 -font helvetica -draw 'text 10,80 "Hello, World!" ‘  hello.jpg  helloworld.jpg
```



### -quality

大多时候，我们的网站并不需要那么清晰的图片，适量调节JPG图片的压缩比会减少图片大小，肉眼并不会分辨出质量被压缩后的图片。通常75%是最佳比例。

```
convert -quality 75% input.jpg output.jpg
```



### -resize

将图像的像素改为1024*768，注意1024与768之间是小写字母x

```
convert -resize 1024x768  xxx.jpg   xxx1.jpg    
```





### -rotate

将图像顺时针旋转270度

```
convert -rotate 270 sky.jpg sky-final.jpg      
```



### -sample

将图像的缩减为原来的50%*50%

```
convert -sample 50%x50%  xxx.jpg  xxx1.jpg   
```



### -thumnail

So to create a thumbnail of the abc.png image with 200px width, you need to type:

```
$ convert -thumbnail 200 abc.png thumb.abc.png
```

To create a thumbnail of the abc.png image with 200px height, you need to type:

```
$ convert -thumbnail x200 abc.png thumb.abc.png
```



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

