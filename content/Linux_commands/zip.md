---
title: "zip"
date: 2020-09-15 00:03
---
[toc]





# zip



## -g append to

Grow  (append  to)  the  specified zip archive, instead of creating a new one. If this operation fails, zip attempts to restore the archive to its





## -r recursive



## -q quiet



## -x/--exclude 

 Explicitly exclude the specified files, as in:                                                                                                                                                                                                                                                                                  

```
zip -r foo foo -x \*.o         
```

which  will  include  the contents of **foo** in **foo.zip** while excluding all the files that end in **.o**.  The backslash avoids the shell filename                  substitution, so that the name matching is performed by zip at all directory levels. Also possible:                                                                                                                                               

```
zip -r foo foo -x@exclude.lst  
```



```
zip -qr rxu_test.zip * -x "*.js" -x "*dill" -x "*.mmdb" -x "*csv" -x "*Cities*gz" -x "*geojson" -x "*ropeproject*" -x "*numbers*" -x "*pyc*"  -x "*swp" -x "bdp*.zip" -x "common*" 
```





