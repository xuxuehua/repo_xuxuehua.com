---
title: "log_file 日志文件架构"
date: 2019-03-04 16:03
---


[TOC]



# The Log File

Reference to [The Agile Admin](https://theagileadmin.com/2010/08/20/logging-for-success/)



## 定义规范



### 见名知意

“nifarm_error.log” for example is obviously the error log for NIFarm.  “my.log” is… Who knows.



### 小写，无空格

ensure compatibility cross-Windows and UNIX



### 统一为.log 后缀

Logs should use a .log suffix to distinguish themselves from everything else on the system (not .txt, .xml, etc.)



### 系统环境保留所有log

Logs targeted at a systems environment should never delete or overwrite themselves.  They should always append and never lose information.



### 桌面环境保留部分log

Logs targeted at a desktop environment should log by default, but use size-restricted logging so that the log size does not grow without bound



### 添加时间戳

A good best practice is to roll daily and add a .YYYYMMDD(.log) suffix to the log name so that a directory full of logs is easily navigable



### 可编辑log存储路径

Logs should always have a configurable location.

Systems people prefer to make logs (and temp files and other stuff like that) write to a specific disk/disk location away from the installed product to the point where they could even set the program’s directory/disk to be read only.



### 统一log存储位置

Put all your logs together.  Don’t scatter them throughout an installation where they’re hard to find and manage (if you make their locations configurable per above, you get this for free, but the default locations shouldn’t be scattered).





## 格式及内容



### 文本模式便于查看



### 一行一个event

Remember a multithreaded application could intersperse a bunch of stuff between those lines you think are “adjacent”.



### 使用分隔符分割内容（|, space）

Log with a delimiter between fields so logs can be easily parsed.  Pipes, commas, or spaces are OK but make sure you escape inserted strings that include that delimiter!  Do not use fixed widths.  If you have an empty field, put something in there (like Apache uses “-“)



### 以时间戳作为首行开始

Every log line should start with a date/time stamp, ideally in YYYYMMDDHHMMSS order (e.g. 2010-05-07 19:51:57)

Use 24 hour time format (no AM/PM)

UTC preferred



### 每行凸显log优先级

Every error log line should have a standard severity.



### log行记录进程，线程等信息

You should always log any diagnostic ID that indicates what process, thread, session, or other instance of any multi-instance resource generated the event.



### log行记录用户信息

You should log information about the user’s identity – both network info (e.g. IP address) and any authentication info (e.g. username).



### 显示日志来源

You should be very specific about where the event came from, at the class/method level



### 切勿记录敏感信息

Passwords should never go into a log (whether the authorization event was a pass or fail

Notify operations of any personally identifiable information you log (email addresses, etc.) due to legal requirements surrounding such data (we have to scrub it before sending it outside NI for example).



### 为error指定唯一编号

Log a unique ID for each error that can also be presented to the user by your code



### 便于读写的日志记录

“Tried to run a job requiring 10 service credits for user foo@bar.com but they have only 5 remaining” is much better than “Job failed.”



### 避免使用系统log

You should use your application log and not standard out/system logs/tomcat logs/Windows logs.  Those should be for unforeseen untrapped exceptions and you should expect that anything ending up in those would generate a request to you to handle that exception in the code in the future.



### 避免记录异常的错误为log

Don’t log and rethrow an exception.  Log it only once, as high in the stack as possible.





## Log Levels



### FATAL

Level FATAL is for things that cause the software to not start or crash.  “Can’t load that DLL” or “Out of memory, going down” qualify.  If your app tries to start or crashes, someone should be able to go to the log and find a FATAL line that says when and why.



### ERROR

At level ERROR, the only things that should go into the log are problems that need to be actionable by someone.

There is no such thing as a “routine error.”  Those should be put at level WARN.  

ERRORs are things that aren’t end user error but that indicate something technical’s not right with the system/application.  “Can’t connect to (database, ldap, file server)”, “trying to save this file but am getting an error saying I can’t,” etc.  ERROR log lines should be rare enough that they page operations staff or trigger automated routines.



### WARN

At level WARN, put anything that is a temporary problem.  An example is if an app couldn’t connect to the authorization service the first time, but will try three more times before giving up.  That should generate WARNs until it’s done trying and fails, which would be an ERROR.  

WARN is any activity that is not totally routine.  A failed login due to a bad password is a WARN level.  Bad user inputs are WARN level.  Finding a virus in an uploaded file and disposing of it successfully is a WARN level.  WARN messages would appear on operations consoles and might be monitored for volume.



### INFO

At level INFO and above, you should log every single call to an external dependency.  If you are talking over a network port, that means you. Talking to a Web service, a database, an authentication server, a file server, or anything like that should be logged and ideally a timing taken of how long it took.  One of the most common errors in a complex system is that one of the many elements it depends on gets slow or goes down.  If logging is at INFO level, you should be able to see major activity – every user coming in, every job submitted, etc. – a high level map of “what is going on” with the application.



### DEBUG

At level DEBUG, you should log every time you go into or out of parts of your code.  If you’re using a logging framework, it’s as easy to use this as it is to put in print statements.  Don’t put in print statements, ever, use debug level logging.



### TRACE

TRACE level is what you’d use if you wanted to log loads of input/output or similar – like a Web app that logs every line of HTML it outputs to the user for some reason.







# example



### log4j

```
portal.20100819.log:
2010-08-19 10:20:17,214|INFO|MyfacesConfig.java|185|Starting up Tomahawk on the MyFaces-JSF-Implementation
2010-08-19 10:20:31,229|INFO|TomcatAnnotationLifecycleProvider.java|47|Creating instance of 
```



## Apache logs

It puts a “-“ in for fields that would otherwise be empty

