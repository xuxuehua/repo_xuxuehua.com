---
title: "Job"
date: 2020-04-18 16:17
collection: 作业管理
---
[toc]





# Job

“离线业务”，或者叫作 Batch Job(计算业务)。这种业务在计算完成后就直接退出了

Pods管理程序，包含一系列job

类似于cronjob，一次性任务



```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers: 
      - name: pi
        image: resouer/ubuntu-bc
        command: ["sh", "-c", "echo 'scale=10000; 4*a(1)' | bc -l "] 
      restartPolicy: Never
  backoffLimit: 4
```

> scale=10000，指定了输出的小数点后的位数是10000
>
> tan(π/4) = 1。所以，4*atan(1)正好就是π，也就是 3.1415926...
>
> restartPolicy=Never 的原因:离线计算的 Pod 永远都不应 该被重启，否则它们会再重新计算一遍
>
> spec.backoffLimit 字段 里定义了重试次数为 4(即，backoffLimit=4)，而这个字段的默认值是 6

事实上，restartPolicy 在 Job 对象里只允许被设置为 Never 和 OnFailure;而在 Deployment 对象里，restartPolicy 则只允许被设置为 Always。

需要注意的是，Job Controller 重新创建 Pod 的间隔是呈指数增加的，即下一次重新创建 Pod 的动作会分别发生在 10 s、20 s、40 s ...后。



## 部署

```
root@master:~# kubectl describe jobs/pi
Name:           pi
Namespace:      default
Selector:       controller-uid=b56ea22a-7b68-4443-bdbe-478192022f77
Labels:         controller-uid=b56ea22a-7b68-4443-bdbe-478192022f77
                job-name=pi
Annotations:    Parallelism:  1
Completions:    1
Start Time:     Mon, 20 Apr 2020 15:04:19 +0000
Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  controller-uid=b56ea22a-7b68-4443-bdbe-478192022f77
           job-name=pi
  Containers:
   pi:
    Image:      resouer/ubuntu-bc
    Port:       <none>
    Host Port:  <none>
    Command:
      sh
      -c
      echo 'scale=10000; 4*a(1)' | bc -l 
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From            Message
  ----    ------            ----  ----            -------
  Normal  SuccessfulCreate  9s    job-controller  Created pod: pi-bfz7f
```

>  controller-uid 是随机字符串，作为job 这个对象的label保证了 Job 与它所管理的 Pod 之间的匹配关系
>
>  Job Controller 之所以要使用这种携带了 UID 的 Label，就是为了避免不同 Job 对象所管理 的 Pod 发生重合





```
root@master:~# kubectl get pods
NAME       READY   STATUS      RESTARTS   AGE
pi-bfz7f   0/1     Completed   0          20m
root@master:~# kubectl logs pi-bfz7f
3.141592653589793238462643383279502884197169399375105820974944592307\
```







## 

## activeDeadlineSeconds 

spec.activeDeadlineSeconds 字段可以设置最长运行时间



```
spec:
  backoffLimit: 5
  activeDeadlineSeconds: 100
```

> 一旦运行超过了 100 s，这个 Job 的所有 Pod 都会被终止。并且，你可以在 Pod 的状态里看到 终止的原因是 reason: DeadlineExceeded。





# Batch 并行作业



在 Job 对象中，负责并行控制的参数有两个

1. spec.parallelism

定义的是一个 Job 在任意时间最多可以启动多少个 Pod 同时运行;

2. spec.completions

定义的是 Job 至少要完成的 Pod 数目，即 Job 的最小完成数。



```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  parallelism: 2
  completions: 4
  template:
    spec:
      containers: 
      - name: pi
        image: resouer/ubuntu-bc
        command: ["sh", "-c", "echo 'scale=5000; 4*a(1)' | bc -l "] 
      restartPolicy: Never
  backoffLimit: 4
```





每当有一个 Pod 完成计算进入 Completed 状态时，就会有一个新的 Pod 被自动创建出来，并且快速地从 Pending 状态进入到 ContainerCreating 状态

紧接着，Job Controller 第二次创建出来的两个并行的 Pod 也进入了 Running 状态

最终，后面创建的这两个 Pod 也完成了计算，进入了 Completed 状态

由于所有的 Pod 均已经成功退出，这个 Job 也就执行完了，所以你会看到它的 SUCCESSFUL 字段的值变成了 4

```
root@master:~# kubectl apply -f job.yaml  && kubectl get pods -w
job.batch/pi created
NAME       READY   STATUS              RESTARTS   AGE
pi-5wlr9   0/1     ContainerCreating   0          0s
pi-f582v   0/1     ContainerCreating   0          0s
pi-f582v   1/1     Running             0          2s
pi-5wlr9   1/1     Running             0          3s
pi-f582v   0/1     Completed           0          30s
pi-f65qr   0/1     Pending             0          0s
pi-f65qr   0/1     Pending             0          0s
pi-f65qr   0/1     ContainerCreating   0          0s
pi-5wlr9   0/1     Completed           0          32s
pi-gwf4g   0/1     Pending             0          0s
pi-gwf4g   0/1     Pending             0          0s
pi-gwf4g   0/1     ContainerCreating   0          0s
pi-f65qr   1/1     Running             0          3s
pi-gwf4g   1/1     Running             0          3s
pi-f65qr   0/1     Completed           0          30s
pi-gwf4g   0/1     Completed           0          37s


root@master:~# kubectl get job
NAME   COMPLETIONS   DURATION   AGE
pi     4/4           69s        102s
```





# 使用job 对象



## 外部管理器 +Job 模板

把 Job 的 YAML 文件定义为一个“模板”，然后用一个外部工具控制 这些“模板”来生成 Job

```
apiVersion: batch/v1
kind: Job
metadata:
  name: process-item-$ITEM 
  labels:
    jobgroup: jobexample
spec:
  template:
    metadata:
      name: jobexample
      labels:
        jobgroup: jobexample
    spec:
      containers: 
      - name: c
        image: busybox
        command: ["sh", "-c", "echo Processing item $ITEM && sleep 5"] 
      restartPolicy: Never
```



使用shell 把 $ITEM 替换掉

```
$ mkdir ./jobs
$ for i in apple banana cherry
do
  cat job-tmpl.yaml | sed "s/\$ITEM/$i/" > ./jobs/job-$i.yaml 
done
```



这个模式看起来虽然很“傻”，但却是 Kubernetes 社区里使用 Job 的一个很普遍的模式。

原因很简单:大多数用户在需要管理 Batch Job 的时候，都已经有了一套自己的方案，需要做 的往往就是集成工作。这时候，Kubernetes 项目对这些方案来说最有价值的，就是 Job 这个 API 对象。所以，你只需要编写一个外部工具(等同于我们这里的 for 循环)来管理这些 Job 即可。

这种模式最典型的应用，就是 TensorFlow 社区的 KubeFlow 项目。

很容易理解，在这种模式下使用 Job 对象，completions 和 parallelism 这两个字段都应该使 用默认值 1，而不应该由我们自行设置。而作业 Pod 的并行控制，应该完全交由外部工具来进 行管理(比如，KubeFlow)



## 拥有固定任务数目的并行 Job

这种模式下，我只关心最后是否有指定数目(spec.completions)个任务成功退出。至于执行时的并行度是多少，我并不关心

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job-wq-1 
spec:
  completions: 8
  parallelism: 2
  template:
    metadata:
      name: job-wq-1
    spec:
      containers:
      - name: c
        image: myrepo/job-wq-1 
        env:
        - name: BROKER_URL
          value: amqp://guest:guest@rabbitmq-service:5672 
        - name: QUEUE
          value: job1
      restartPolicy: OnFailure
```

> completions 的值是:8，这意味着我们总共要处理的任务数目是 8 个。 也就是说，总共会有 8 个任务会被逐一放入工作队列里



在这个实例中，我选择充当工作队列的是一个运行在 Kubernetes 里的 RabbitMQ。所以，我 们需要在 Pod 模板里定义 BROKER_URL，来作为消费者。

所以，一旦你用 kubectl create 创建了这个 Job，它就会以并发度为 2 的方式，每两个 Pod 一 组，创建出 8 个 Pod。每个 Pod 都会去连接 BROKER_URL，从 RabbitMQ 里读取任务，然后 各自进行处理。这个 Pod 里的执行逻辑，我们可以用这样一段伪代码来表示

```
/* job-wq-1 的伪代码 */
queue := newQueue($BROKER_URL, $QUEUE) 3 task := queue.Pop()
process(task)
exit
```

可以看到，每个 Pod 只需要将任务信息读取出来，处理完成，然后退出即可。而作为用户，我 只关心最终一共有 8 个计算任务启动并且退出，只要这个目标达到，我就认为整个 Job 处理完 成了。所以说，这种用法，对应的就是“任务总数固定”的场景





## 指定并行度(parallelism) (常用)

不设置固定的 completions 的值



你就必须自己想办法，来决定什么时候启动新 Pod，什么时候 Job 才算执行完成。在这 种情况下，任务的总数是未知的，所以你不仅需要一个工作队列来负责任务分发，还需要能够判断工作队列已经为空(即:所有的工作已经结束了)

Job 的定义基本上没变化，只不过是不再需要定义 completions 的值了而已

```
apiVersion: batch/v1
kind: Job
metadata:
  name: job-wq-2 
spec:
  parallelism: 2
  template:
    metadata:
      name: job-wq-1
    spec:
      containers:
        - name: c
          image: gcr.io/myproject/job-wq-2 
          env:
          - name: BROKER_URL
            value: amqp://guest:guest@rabbitmq-service:5672 
          - name: QUEUE
            value: job2
      restartPolicy: OnFailure
```

而对应的 Pod 的逻辑会稍微复杂一些，可以用这样一段伪代码来描述

```
/* job-wq-2 的伪代码 */
for !queue.IsEmpty($BROKER_URL, $QUEUE) { 
    task := queue.Pop()
    process(task)
}
print("Queue empty, exiting")
exit
```

由于任务数目的总数不固定，所以每一个 Pod 必须能够知道，自己什么时候可以退出。比如， 在这个例子中，我简单地以“队列为空”，作为任务全部完成的标志。所以说，这种用法，对应 的是“任务总数不固定”的场景。





