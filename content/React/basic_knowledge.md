---
title: "basic_knowledge"
date: 2019-04-21 19:48
---


[TOC]



# React



## installation



### NVM

#### Linux

Node Version Manager

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```



#### OSX



```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
```



append them into `~/.bash_profile`

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```





### npm

Node Package Management

```
curl -L https://www.npmjs.com/install.sh | sh
```



## Create React App

```
npm install -g create-react-app
```



```
cd learn_react/

create-react-app reactdemo1

cd reactdemo1

npm start
```

