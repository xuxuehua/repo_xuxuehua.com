---
title: "CronJob"
date: 2020-04-18 16:17
collection: 作业管理
---
[toc]





# CronJob

CronJob 是一个 Job 对象的控制器(Controller)

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello 
            image: busybox 
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster 
          restartPolicy: OnFailure
```



1min 后，会有对应的job产生

```
root@master:~# kubectl get jobs -w
NAME               COMPLETIONS   DURATION   AGE
hello-1587400860   0/1                      0s
hello-1587400860   0/1           1s         1s
hello-1587400860   1/1           4s         4s

root@master:~# kubectl get cronjob hello
NAME    SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello   */1 * * * *   False     0        35s             85s
```



## concurrencyPolicy

由于定时任务的特殊性，很可能某个 Job 还没有执行完，另外一个新 Job 就产 生了。这时候，你可以通过 spec.concurrencyPolicy 字段来定义具体的处理策略。比如:

concurrencyPolicy=Allow，这也是默认情况，这意味着这些 Job 可以同时存在;
concurrencyPolicy=Forbid，这意味着不会创建新的 Pod，该创建周期被跳过;
concurrencyPolicy=Replace，这意味着新产生的 Job 会替换旧的、没有执行完的 Job。

而如果某一次 Job 创建失败，这次创建就会被标记为“miss”。当在指定的时间窗口内，miss 的数目达到 100 时，那么 CronJob 会停止再创建这个 Job。

这个时间窗口，可以由 spec.startingDeadlineSeconds 字段指定。比如 startingDeadlineSeconds=200，意味着在过去 200 s 里，如果 miss 的数目达到了 100 次， 那么这个 Job 就不会被创建执行了。



