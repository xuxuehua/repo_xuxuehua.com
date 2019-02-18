---
title: "merge"
date: 2018-06-27 01:48
collection: Git
---



[TOC]

# merge



## `git mergetool`

命令行图形化展示操作



`git mergetool` for me resulted in `vimdiff` being used. You can install one of the following tools to use it instead: `meld`, `opendiff`, `kdiff3`, `tkdiff`, `xxdiff`, `tortoisemerge`, `gvimdiff`, `diffuse`, `ecmerge`, `p4merge`, `araxis`, `vimdiff`, `emerge`.

Below is the sample procedure to use `vimdiff` for resolve merge conflicts. Based on [this link](http://www.rosipov.com/blog/use-vimdiff-as-git-mergetool/#fromHistor)

**Step 1**: Run following commands in your terminal

```
git config merge.tool vimdiff
git config merge.conflictstyle diff3
git config mergetool.prompt false
```

This will set vimdiff as the default merge tool.

**Step 2**: Run following command in terminal

```
git mergetool
```

**Step 3**: You will see a vimdiff display in following format

```
  +----------------------+
  |       |      |       |
  |LOCAL  |BASE  |REMOTE |
  |       |      |       |
  +----------------------+
  |      MERGED          |
  |                      |
  +----------------------+
```

These 4 views are

> LOCAL – this is file from the current branch
>
> BASE – common ancestor, how file looked before both changes
>
> REMOTE – file you are merging into your branch
>
> MERGED – merge result, this is what gets saved in the repo

You can navigate among these views using `ctrl+w`. You can directly reach MERGED view using `ctrl+w` followed by `j`.

More info about vimdiff navigation [here](https://stackoverflow.com/questions/4556184/vim-move-window-left-right) and [here](https://stackoverflow.com/questions/27151456/how-do-i-jump-to-the-next-prev-diff-in-git-difftool)

**Step 4**. You could edit the MERGED view the following way

If you want to get changes from REMOTE

```
:diffg RE  
```

If you want to get changes from BASE

```
:diffg BA  
```

If you want to get changes from LOCAL

```
:diffg LO 
```

**Step 5**. Save, Exit, Commit and Clean up

`:wqa` save and exit from vi

`git commit -m "message"`

`git clean` Remove extra files (e.g. *.orig) created by diff tool.



### another example

You're going to pull some changes, but oops, you're not up to date:

```
git fetch origin
git pull origin master

From ssh://gitosis@example.com:22/projectname
 * branch            master     -> FETCH_HEAD
Updating a030c3a..ee25213
error: Entry 'filename.c' not uptodate. Cannot merge.
```

So you get up-to-date and try again, but have a conflict:

```
git add filename.c
git commit -m "made some wild and crazy changes"
git pull origin master

From ssh://gitosis@example.com:22/projectname
 * branch            master     -> FETCH_HEAD
Auto-merging filename.c
CONFLICT (content): Merge conflict in filename.c
Automatic merge failed; fix conflicts and then commit the result.
```

So you decide to take a look at the changes:

```
git mergetool
```

Oh me, oh my, upstream changed some things, but just to use my changes...no...their changes...

```
git checkout --ours filename.c
git checkout --theirs filename.c
git add filename.c
git commit -m "using theirs"
```

And then we try a final time

```
git pull origin master

From ssh://gitosis@example.com:22/projectname
 * branch            master     -> FETCH_HEAD
Already up-to-date.
```