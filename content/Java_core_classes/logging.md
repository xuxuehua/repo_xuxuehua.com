---
title: "logging"
date: 2019-10-23 09:12
---
[TOC]



# Logging (不常用)

日志就是Logging，它的目的是为了取代`System.out.println()`

```
import java.util.logging.Logger;

public class Main {
    public static void main(String[] args) {
        Logger logger = Logger.getGlobal();
        logger.info("Start process");
        logger.warning("Memory is running out");
        logger.fine("ignore");
        logger.severe("Process will be terminated...");
    }
}

>>>
Oct 23, 2019 9:00:25 AM Main main
INFO: Start process
Oct 23, 2019 9:00:25 AM Main main
WARNING: Memory is running out
Oct 23, 2019 9:00:25 AM Main main
SEVERE: Process will be terminated...
```



## 日志级别

从严重到普通, 默认级别是INFO，因此，INFO级别以下的日志，不会被打印出来。使用日志级别的好处在于，调整级别，就可以屏蔽掉很多调试相关的日志输出。

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST



## logging初始化

Logging系统在JVM启动时读取配置文件并完成初始化，一旦开始运行`main()`方法，就无法修改配置；

需要在JVM启动时传递参数`-Djava.util.logging.config.file=<config-file-name>`

