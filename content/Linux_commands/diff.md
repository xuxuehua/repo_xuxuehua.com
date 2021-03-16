---
title: "diff"
date: 2020-09-08 10:12
---
[toc]



# diff



## file in folders

You can use the `diff` command for that:

```sh
diff -bur folder1/ folder2/
```

> **b** flag means ignoring whitespace
>
> **u** flag means a unified context (3 lines before and after)
>
> **r** flag means recursive



### -d target directory

For example extract package.zip into /opt, enter

```
# unzip package.zip -d /opt
# cd /opt
# ls
```

