title: "apscheduler"
date: 2020-03-29 00:05

[toc]



# APScheduler

Advanced Python Scheduler

APScheduler基于Quartz的一个Python定时任务框架，实现了Quartz的所有功能，使用起来十分方便。提供了基于日期、固定时间间隔以及crontab类型的任务，并且可以持久化任务。



APScheduler 也可以集成到几个常见的 Python 框架中，如

```
- asyncio
- gevent
- Tornado
- Twisted
- Qt（使用 PyQt 或 PySide）
```

​    



## trigger 触发器

包含调度逻辑，每一个作业有它自己的触发器，用于决定接下来哪一个作业会运行。除了他们自己初始配置意外，触发器完全是无状态的。

APScheduler 有三个内置的 trigger 类型

```
date 在某个确定的时间点运行你的 job （只运行一次）
interval 在固定的时间间隔周期性地运行你的 job
cron 在一天的某些固定时间点周期性地运行你的 job
```



### date trigger

`date` 是最基本的一种调度，job 只会执行一次，它表示特定的时间点触发，其参数如下所示：

```
run_date(datetime|str) ： job 要运行的时间，如果 run_date 为空，则默认取当前时间
timezone(datetime.tzinfo|str) ：指定 `run_date` 的时区
```

​    

```
from datetime import date
from apscheduler.schedulers.blocking import BlockingScheduler

sched = BlockingScheduler

def my_job(text):
    print(text)

# job 将在 2009 年 11 月 6 日 16:30:05 运行
sched.add_job(my_job, "date", run_date=datetime(2009, 11, 6, 16, 30, 5), args=["text"])
# 另一种写法
sched.add_job(my_job, "date", run_date="2009-11-06 16:30:05", args=["text"])

sched.start()
```







### interval trigger

interval 表示周期性触发触发，其参数如下

```
weeks(int) ：间隔礼拜数
days(int) ：间隔天数
hours(int) ：间隔小时数
minutes(int) ：间隔分钟数
seconds(int) ：间隔秒数
start_date(datetime|str) ：周期执行的起始时间点
end_date(datetime|str) ：最后 可能 触发时间
timezone(datetime.tzinfo|str) ：计算 date/time 类型时需要使用的时区
jitter(int|None) ：最多提前或延后执行 job 的 偏振 秒数
```

> 如果 `start_date` 为空，则默认是 `datetime.now() + interval` 作为起始时间。
> 如果 `start_date` 是过去的时间，trigger 不会追溯触发多次，而是根据过去的起始时间计算从当前时间开始下一次的运行时间。



```
from datetime import datetime

from apscheduler.schedulers.blocking import BlockingScheduler


def job_function():
    print("Hello World")

sched = BlockingScheduler()

# job_function 每两个小时执行一次，同时添加了 jitter 可以增加随机性
# 防止如多个服务器在同一时间运行某个 job 时会非常有用
sched.add_job(job_function, 'interval', hours=2, jitter=120, start_date="2010-10-10 09:30:00", end_date="2014-06-15 11:00:00")

sched.start()
```





### cron trigger

cron 提供了和 Linux crontab 格式兼容的触发器，是功能最为强大的触发器，其参数如下所示

```
year(int|str) - 4 位年份
month(int|str) - 2 位月份(1-12)
day(int|str) - 一个月内的第几天(1-31)
week(int|str) - ISO 礼拜数(1-53)
day_of_week(int|str) - 一周内的第几天(0-6 或者 mon, tue, wed, thu, fri, sat, sun)
hour(int|str) - 小时(0-23)
minute(int|str) - 分钟(0-59)
second(int|str) - 秒(0-59)
start_date(datetime|str) - 最早可能触发的时间(date/time)，含该时间点
end_date(datetime|str) - 最后可能触发的时间(date/time)，含该时间点
timezone(datetime.tzinfo|str) - 计算 date/time 时所指定的时区（默认为 scheduler 的时区）
jitter(int|None) - 最多提前或延后执行 job 的 偏振 秒数
```



```
from apscheduler.schedulers.blocking import BlockingScheduler


def job_function():
    print "Hello World"

sched = BlockingScheduler()

# job_function 会在 6、7、8、11、12 月的第三个周五的 00:00, 01:00, 02:00 以及 03:00 执行
sched.add_job(job_function, 'cron', month='6-8,11-12', day='3rd fri', hour='0-3')

# 可以使用装饰器模式

def some_decorated_task():
    print("I am printed at 00:00:00 on the last Sunday of every month!")

# 或者直接使用 crontab 表达式
sched.add_job(job_function, CronTrigger.from_crontab('0 0 1-15 may-aug *'))

sched.start()
```



## job store 作业存储

存储被调度的作业，默认的作业存储是简单地把作业保存在内存中，其他的作业存储是将作业保存在数据库中。

一个作业的数据讲在保存在持久化作业存储时被序列化，并在加载时被反序列化。

调度器scheduler不能分享同一个作业存储。

如果你希望使用 executor 或 job store 来序列化 job ，那么 job 必须满足以下两个条件：

```
1.（被调度的）目标里的可调用对象必须时全局可访问的
2. 可调用对象的任何参数都可以被序列化
```

​    



### 添加 job

有两种途径可以为 scheduler 添加 job 

```
调用 add_job() 方法
使用 scheduled_job() 装饰一个函数 #通过声明 job 而不修改应用程序运行时是最为方便的。
```



### 移除 job

当从 scheduler 中移除一个 job 时，它会从关联的 job store 中被移除，不再被执行。有两种途径可以移除 job 

```
1. 通过 job 的 ID 以及 job store 的别名来调用 remove_job() 方法
2. 对你在 add_job() 中得到的 job 实例调用 remove() 方法
```

> 后者看起来更方便，实际上它要求你必须将调用 `add_job()` 得到的 `Job` 实例存储在某个地方。而对于通过 `scheduled_job()` 装饰器来调度 job 的就只能使用第一种方法

```
job = scheduler.add_job(myfunc, 'interval', minutes=2)
job.remove()

scheduler.add_job(myfunc, 'interval', minutes=2, id='my_job_id')
scheduler.remove_job('my_job_id')
```



### 暂停/恢复 job

通过 Job 实例或者 scheduler 本身你可以轻易地暂停和恢复 job 。当一个 job 被暂停，它的下一次运行时间将会被清空，同时不再计算之后的运行时间，直到这个 job 被恢复



暂停一个 job ，使用以下方法

```
apscheduler.job.Job.pause()
apscheduler.schedulers.base.BaseScheduler.pause_job()
```



而恢复一个 job ，则可以

```
apscheduler.job.Job.resume()
apscheduler.schedulers.base.BaseScheduler.resume_job()
```



### 获取 job

可以使用 [`get_jobs`](https://apscheduler.readthedocs.io/en/latest/modules/schedulers/base.html#apscheduler.schedulers.base.BaseScheduler.get_jobs) 方法来获得机器上可处理的作业调度列表。方法会返回一个 `Job` 实例的列表，如果你仅仅对特定的 job store 中的 job 感兴趣，可以将 job store 的别名作为第二个参数。

更方便的做法时，使用print_job()来格式化输出作业列表以及它们的触发器和下一次的运行时间。



### 修改 job

通过 `apscheduler.job.Job.modify()` 或者 `modify_job()` 方法均可修改 job 的属性。你可以根据 `id` 修改该任何 Job 的属性

```
job.modify(max_instances=6, name='Alternate name')
```



重新调度一个 job （这意味着要修改其 trigger），你可以使用 `apscheduler.job.Job.reschedule()`或 `reschedule_job()`方法。这些方法都会为 job 构建新的 trigger ，然后根据新的 trigger 重新计算其下一次的运行时间

```
scheduler.reschedule_job('my_job_id', trigger='cron', minute='*/5')
```





## executor 执行器

处理作业的运行，通过在作业中提交制定的可调用对象到一个线程或者进城池来进行。当作业完成时，执行器将会通知调度器。

executor 的选择基于你是否选择了任意一个 Python 框架。如果都没有，那么默认的 `ThreadPoolExecutor` 足够满足大部分的需求。

如果你的作业包含了 CPU 密集型操作，你应该考虑使用 `ProcessPoolExecutor` 以便充分利用多核 CPU 。

甚至你可以同时使用它们两者，将 *process pool executor* 作为备用 executor 。



## scheduler 调度器

一般情况下, 只会有一个调度器在运行，应用的开发者通常不会直接处理作业存储、调度器和触发器，相反，调度器提供了处理这些的合适的接口。配置作业存储和执行器可以在调度器中完成，例如添加、修改和移除作业。



为了选到合适的 job store ，你需要明确你是否需要将你的 job 持久化

```
BlockingScheduler: 导入调度器模块 BlockingScheduler，这是最简单的调度器，调用 start 方阻塞当前进程，如果你的程序只用于调度，除了调度进程外没有其他后台进程，那么请用 BlockingScheduler 非常有用，此时调度进程相当于守护进程。
BackgroundScheduler: 如果你想你的调度器可以在你的应用程序后台静默运行，同时也不打算使用以下任何 Python 框架，请选择它
AsyncIOScheduler: 如果你的程序使用了 asyncio 库，请使用这个调度器
GeventScheduler: 如果你的程序使用了 gevent 库，请使用这个调度器
TornadoScheduler: 如果你打算构建一个 Tornado 程序，请使用这个调度器
TwistedScheduler: 如果你打算构建一个 Twisted 程序，请使用这个调度器
QtScheduler: 如果你打算构建一个 Qt 程序，请使用这个调度器
```



### 终止 scheduler

以下方法可以终止 scheduler

```
scheduler.shutdown()

scheduler.shutdown(wait=False) #终止 job store 和 executor ，但不会等待任何运行中的任务完成
```





### 暂停/恢复 scheduler

你可以用以下方法暂停被调度的 job 的运行：

```
scheduler.pause()
```

这会导致 scheduler 再被恢复之前一直处于休眠状态：

```
scheduler.resume()
```

如果没有进行过唤醒，也可以对处于暂停状态的 scheduler 执行 `start` 操作：

```
scheduler.start(paused=True)
```







# example

```
from apscheduler.schedulers.blocking import BlockingScheduler
from datetime import datetime

sched = BlockingScheduler()　　#构造定时器对象

def my_job():
    print(f'{datetime.now():%H:%M:%S} Hello World ')

sched.add_job(my_job, 'interval', seconds=5) #给定时器添加任务，触发条件，以及间隔时间
sched.start()                                 #开启定时器
```

> 实例化一个 BlockingScheduler 类，不带参数表明使用默认的作业存储器-内存，默认的执行器是线程池执行器，最大并发线程数默认为 10 个（另一个是进程池执行器）



