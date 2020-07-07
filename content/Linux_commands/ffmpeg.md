---
title: "ffmpeg"
date: 2020-06-21 16:20
---
[toc]





# ffmpeg



## gif 

The order of command line arguments matters. This command line should work but will generate a giant file:

```
ffmpeg -i yesbuddy.mov -pix_fmt rgb24 output.gif
```

Note that you probably want to reduce the frame rate and size when you convert, as well as specify a start time and duration. You probably do not want to convert the entire file at its original resolution and frame rate.

```
ffmpeg -ss 00:00:00.000 -i yesbuddy.mov -pix_fmt rgb24 -r 10 -s 320x240 -t 00:00:10.000 output.gif
```

The file size will still be huge. You may be able to use [ImageMagick](http://www.imagemagick.org/)'s GIF optimizer to reduce the size:

```
convert -layers Optimize output.gif output_optimized.gif
```