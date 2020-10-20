---
title: "conda"
date: 2020-07-23 09:55
---
[toc]





# Ananconda





## create env

```
(base) rxus-MacBook-Pro:~ rxu$ conda env list
# conda environments:
#
base                  *  /opt/anaconda3

(base) rxus-MacBook-Pro:~ rxu$ conda create -n mypy37env python=3.7
…
…
Proceed ([y]/n)? y
…
…
(base) rxus-MacBook-Pro:~ rxu$ conda activate mypy37env
(mypy37env) rxus-MacBook-Pro:~ rxu$ conda env list
# conda environments:
#
base                     /opt/anaconda3
mypy37env             *  /opt/anaconda3/envs/mypy37env
```



## conda init 

cat ~/.bash_profile

```
# added by Anaconda3 2019.10 installer
# >>> conda init >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$(CONDA_REPORT_ERRORS=false '/opt/anaconda3/bin/conda' shell.bash hook 2> /dev/null)"
if [ $? -eq 0 ]; then
    \eval "$__conda_setup"
else
    if [ -f "/opt/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/opt/anaconda3/etc/profile.d/conda.sh"
        CONDA_CHANGEPS1=false conda activate base
    else
        \export PATH="/opt/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda init <<<
```





# FAQ

## Disable base env for auto-activating

```
conda config --set auto_activate_base false
```

