---
title: "pyicu"
date: 2020-06-28 15:59
---
[toc]



# pyicu



## mac installation

1) install `icu4c` with brew:

```py
brew install icu4c
brew link icu4c --force
```

2) check the version:

```py
ls /usr/local/Cellar/icu4c/
```

it prompts something like: `64.2`

3) execute bellow commands with substitution of proper version from previous step (first line only integer part, second and third line with decimal part):

```py
export ICU_VERSION=64
export PYICU_INCLUDES=/usr/local/Cellar/icu4c/64.2/include
export PYICU_LFLAGS=-L/usr/local/Cellar/icu4c/64.2/lib
```

4) finally install python package for pyicu:

```py
pip install pyicu --upgrade
```

IF YOU FAIL with above (happen already to me on OS X `10.15`) you may need:

```py
brew install pkg-config

export PYICU_CFLAGS=-std=c++11:-DPYICU_VER='"2.3.1"'
```

