---
title: "tar"
date: 2021-07-12 20:48
---



[toc]



# tar





## Split tar archives into multiple blocks

This will work regardless of what type of compression (or lack thereof) that you use. So files with extensions like `.tar`, `tar.gz`, `tar.xz`, etc. can all be split into chunks. We'll also show you how to extract files from archives that have been split into numerous files.



We can first of all create an archive file as follows:

```
$ tar -cvjf home.tar.bz2 /home/rxu/Documents/* 
```





Then using the split utility, we can break the `home.tar.bz2` archive file into small blocks each of size `10MB` as follows:

```
$ split -b 10M home.tar.bz2 "home.tar.bz2.part"
$ ls -lh home.tar.bz2.parta*
```







 Similar to the case above, here, we can create an archive file of a **Linux Mint ISO** image file.

```
$ tar -cvzf linux-mint-18.tar.gz linuxmint-18-cinnamon-64bit.iso 
```

Then follow the same steps in **example 1** above to split the archive file into small bits of size `200MB`.

```
$ ls -lh linux-mint-18.tar.gz 
$ split -b 200M linux-mint-18.tar.gz "ISO-archive.part"
$ ls -lh ISO-archive.parta*
```







In this instance, we can use a **pipe** to connect the output of the **tar** command to split as follows:

```
$ tar -cvzf - wget/* | split -b 150M - "downloads-part"
```







To split tar archives into multiple files, we'll pipe our `tar` command over to `split`. Let's look at an example.

This command will split a gzip compressed tar archive into 5MB chunks:

```
$ tar cvzf - example-dir/ | split --bytes=5MB - myfiles.tar.gz.
```





## Combine split tar archives



To join back all the blocks or tar files, we issue the command below:

```
# cat home.tar.bz2.parta* > backup.tar.gz.joined
```



To open the split tar archive that we've created, you can use the `cat` command, piped to the `tar` command.

```
$ cat myfiles.tar.gz.* | tar xzvf -
```

