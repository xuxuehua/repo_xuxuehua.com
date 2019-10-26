---
title: "gunicorn"
date: 2019-10-24 22:16
---
[TOC]



# gunicorn




## `-c CONFIG, --config=CONFIG`

Specify a config file in the form `$(PATH)`, `file:$(PATH)`, or `python:$(MODULE_NAME)`.


## `-b BIND, --bind=BIND` 

Specify a server socket to bind. Server sockets can be any of `$(HOST)`, `$(HOST):$(PORT)`, or `unix:$(PATH)`. An IP is a valid `$(HOST)`.


## `-w WORKERS, --workers=WORKERS` 

The number of worker processes. This number should generally be between 2-4 workers per core in the server. Check the [FAQ](http://docs.gunicorn.org/en/latest/faq.html#faq) for ideas on tuning this parameter.

## `-k WORKERCLASS, --worker-class=WORKERCLASS` 

The type of worker process to run. You’ll definitely want to read the production page for the implications of this parameter. You can set this to `$(NAME)` where `$(NAME)` is one of `sync`, `eventlet`, `gevent`, `tornado`, `gthread`, `gaiohttp` (deprecated). `sync` is the default. See the [worker_class](http://docs.gunicorn.org/en/latest/settings.html#worker-class) documentation for more information.


## `-n APP_NAME, --name=APP_NAME` 

If [setproctitle](https://pypi.python.org/pypi/setproctitle) is installed you can adjust the name of Gunicorn process as they appear in the process system table (which affects tools like `ps` and `top`).