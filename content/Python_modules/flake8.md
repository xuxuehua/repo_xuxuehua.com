---
title: "flake8"
date: 2020-06-24 16:40
---
[toc]





# flake8

flake8 是将 PEP 8、Pyflakes（类似 Pylint）、McCabe（代码复杂性检查器）和第三方插件整合到一起，以检查 Python 代码风格和质量的一个 Python 工具”。



## setup on local env

1. 在 Python 环境 (最好是与项目对应的 virtualenv 里) 安装 flake8

```
pip install flake8
```



2. 在本地 git 仓库创建 .git/hooks/pre-commit 

```
cat > ./.git/hooks/pre-commit << EOF
#!/usr/bin/env bash

# activate your virtualenv/condaenv/pipenv if necessary
git diff --staged | flake8 --diff
EOF
```



3. 设置可执行

```
chmod +x .git/hooks/pre-commit
```




4. 在本地 git 仓库创建 ./.flake8 内容如下

```
cat > ./.flake8 << EOF
[flake8]
ignore = W292
exclude = .git, build/*
max-complexity = 12
max-line-length = 120
EOF
```

其中 pre-commit 默认不进仓库，可以自己修改，不影响别人；.flake8 会进仓库，通不过 flake8 检查将不能 commit

```
$ cat a.py
a=2
a == None

$ git add a.py
$ git commit -m "you shall not pass"
a.py:1:2: E225 missing whitespace around operator
a.py:2:3: E711 comparison to None should be 'if cond is None:'
```





# C901 too complex

disable 

```
# flake8: noqa: C901
```







