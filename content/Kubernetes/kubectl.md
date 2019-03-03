---
title: "kubectl"
date: 2019-02-22 22:32
---


[TOC]



# kubectl

Kubernetes API Server最常用的客户端程序之一，功能强大，能够几乎完成除了安装部署之外的所有管理操作



## syntax

```
kubectl [command] [TYPE] [NAME] [flags]
```



## installation



### mac osx

#### binary

Download the latest release:

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl
```

Make the kubectl binary executable.

```
chmod +x ./kubectl
```

Move the binary in to your PATH.

```
sudo mv ./kubectl /usr/local/bin/kubectl
```





#### brew

```
brew install kubernetes-cli
```



## minikube

### mac osx

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.34.1/minikube-darwin-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube
```



## run

```
kubectl run nginx-deploy --image=nginx:1.12 --replicas=2
```



## expose 暴露服务

```
kubectl expose deployment/nginx --name=nginx-svc --port=80
```

```
kubectl expose deployments/myapp --type="NodePort" --port=80 --name=myapp
```





## create

```
kubectl create -f nignx-deploy.yaml -f nginx-svc.yaml
```



## apply 实现声明

```
kubectl apply -f nginx-deploy.yaml -f nginx-svc.yaml
```





## get

列出所有资源

```
kubectl get namespaces
```



查看多个资源

```
kubectl get pods,services -o wide
```



列出名称空间中拥有k8s-app标签名称的所有Pod 对象

```
kubectl get pods -l k8s-app -n kube-system
```



### -o yaml | json

```
kubectl get pods -l component=kube=apiserver -o yaml -n kube-system
```



### -o wide

```
kubectl get pods -o wide 
```





## describe 详细信息

```
kubectl describe pods -l component=kube-apiserver -n kube-system
```

```
kubectl describe services myapp-svc
```



## log 日志

```
kubectl log [-f] [-p] (POD|TYPE/NAME) [-c CONTAINER] [options] 
```

> -f 类似于tail -f



```
kubectl logs kube-apiserver-master.xuxuehua.com -n kube-system
```



### -c 指定容器名称



## exec 执行命令

```
kubectl exec kube-apiserver-master.xuxuehua.com -n kube-system --ps
```

> Pod对象中的容器里面运行ps命令



### -it 交互Shell

```
kubectl exec -it $POD_NAME /bin/sh
```



## delete 删除

删除默认名称空间中ngnix-svc的Service 资源对象 

```
kubectl delete services nginx-svc
```



删除默认名称空间中所有的Deployment控制器

```
kubectl delete deployment --all
```



### --all

删除kube-public 名称空间中的所有pod对象

```
kubectl delete pods --all -n kube-public
```





## scale 扩容/缩容

```
kubectl scale deployments/myapp --replicas=3 
```

> replicas 指明对象创建或管理Pod对象的副本数量



```
kubectl scale deployments/myapp --replicas=2
```





## api-versions

获取api server 上的相关信息



## explain 

获取相关帮助



解释Pod资源的一级字段

```
kubectl explain pods
```



某对象下的二级字段, 三四级依此类推

```
kubectl explain pods.spec
```





# example



## hello world

```
xhxu-mac:~ xhxu$ minikube start
😄  minikube v0.34.1 on darwin (amd64)
🔥  Creating virtualbox VM (CPUs=2, Memory=2048MB, Disk=20000MB) ...
📶  "minikube" IP address is 192.168.99.104
🐳  Configuring Docker as the container runtime ...
✨  Preparing Kubernetes environment ...
🚜  Pulling images required by Kubernetes v1.13.3 ...
🚀  Launching Kubernetes v1.13.3 using kubeadm ...
🔑  Configuring cluster permissions ...
🤔  Verifying component health .....
💗  kubectl is now configured to use "minikube"
🏄  Done! Thank you for using minikube!

xhxu-mac:~ xhxu$ kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     master    50m       v1.13.3
xhxu-mac:~ xhxu$ kubectl run hw --image=karthequian/helloworld --port=80
deployment.apps "hw" created
xhxu-mac:~ xhxu$ kubectl get deployments
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hw        1         1         1            0           10s
xhxu-mac:~ xhxu$ kubectl get rs
NAME            DESIRED   CURRENT   READY     AGE
hw-747fddfdb8   1         1         0         16s
xhxu-mac:~ xhxu$ kubectl get pods
NAME                  READY     STATUS              RESTARTS   AGE
hw-747fddfdb8-7jmpl   0/1       ContainerCreating   0          24s
```



### expose it as service

```
xhxu-mac:~ xhxu$ kubectl expose deployment hw --type=NodePort
service "hw" exposed
xhxu-mac:~ xhxu$ kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
hw           NodePort    10.109.209.48   <none>        80:30201/TCP   9s
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        56m
xhxu-mac:~ xhxu$ minikube service hw
🎉  Opening kubernetes service default/hw in default browser...
```



### check the service

```
xhxu-mac:~ xhxu$ kubectl get all
NAME                      READY     STATUS    RESTARTS   AGE
pod/hw-747fddfdb8-7jmpl   1/1       Running   0          1d

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/hw           NodePort    10.109.209.48   <none>        80:30201/TCP   1d
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        2d

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hw   1         1         1            1           1d

NAME                            DESIRED   CURRENT   READY     AGE
replicaset.apps/hw-747fddfdb8   1         1         1         1d
xhxu-mac:~ xhxu$ kubectl get deploy/hw -o yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: 2019-03-01T06:15:15Z
  generation: 1
  labels:
    run: hw
  name: hw
  namespace: default
  resourceVersion: "4354"
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/hw
  uid: 610a81f5-3be9-11e9-b4d5-0800273e7607
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 1
  revisionHistoryLimit: 2147483647
  selector:
    matchLabels:
      run: hw
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: hw
    spec:
      containers:
      - image: karthequian/helloworld
        imagePullPolicy: Always
        name: hw
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: 2019-03-01T06:15:15Z
    lastUpdateTime: 2019-03-01T06:15:15Z
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
```



### Scale deploy

```
xhxu-mac:~ xhxu$ kubectl get deployments
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hw        1         1         1            1           2d

xhxu-mac:~ xhxu$ kubectl scale --replicas=3 deploy/hw
deployment.extensions "hw" scaled
xhxu-mac:~ xhxu$ kubectl get deploy/hw
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hw        3         3         3            3           2d

xhxu-mac:~ xhxu$ kubectl get pods
NAME                  READY     STATUS    RESTARTS   AGE
hw-747fddfdb8-7jmpl   1/1       Running   0          2d
hw-747fddfdb8-c5lf6   1/1       Running   0          49s
hw-747fddfdb8-tmrl6   1/1       Running   0          49s
```



### label operation

```
vim helloworld-pod-with-lables.yml

apiVersion: v1
kind: Pod
metadata:
  name: helloworld
  labels:
    env: production
    author: karthequian
    application_type: ui
    release-version: "1.0"
spec:
  containers:
  - name: helloworld
    image: karthequian/helloworld:latest
```

```
xhxu-mac:test xhxu$ kubectl create -f helloworld-pod-with-lables.yml
pod "helloworld" created

xhxu-mac:test xhxu$ kubectl get pods
NAME                  READY     STATUS    RESTARTS   AGE
helloworld            1/1       Running   0          21s
hw-747fddfdb8-7jmpl   1/1       Running   0          2d
hw-747fddfdb8-c5lf6   1/1       Running   0          19m
hw-747fddfdb8-tmrl6   1/1       Running   0          19m
```



#### add & delete

```
xhxu-mac:test xhxu$ kubectl get pods --show-labels
NAME                  READY     STATUS    RESTARTS   AGE       LABELS
helloworld            1/1       Running   0          1m        application_type=ui,author=karthequian,env=production,release-version=1.0
hw-747fddfdb8-7jmpl   1/1       Running   0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running   0          20m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running   0          20m       pod-template-hash=747fddfdb8,run=hw
xhxu-mac:test xhxu$ kubectl label po/helloworld app=helloworldapp --overwrite
pod "helloworld" labeled
xhxu-mac:test xhxu$ kubectl get pods --show-labels
NAME                  READY     STATUS    RESTARTS   AGE       LABELS
helloworld            1/1       Running   0          1m        app=helloworldapp,application_type=ui,author=karthequian,env=production,release-version=1.0
hw-747fddfdb8-7jmpl   1/1       Running   0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running   0          20m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running   0          20m       pod-template-hash=747fddfdb8,run=hw
xhxu-mac:test xhxu$ kubectl label pod/helloworld app-
pod "helloworld" labeled
xhxu-mac:test xhxu$ kubectl get pods --show-labels
NAME                  READY     STATUS    RESTARTS   AGE       LABELS
helloworld            1/1       Running   0          2m        application_type=ui,author=karthequian,env=production,release-version=1.0
hw-747fddfdb8-7jmpl   1/1       Running   0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running   0          21m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running   0          21m       pod-template-hash=747fddfdb8,run=hw
```



#### searching

```
vim sample-infrastructure-with-labels.yml

apiVersion: v1
kind: Pod
metadata:
  name: homepage-dev
  labels:
    env: development
    dev-lead: karthik
    team: web
    application_type: ui
    release-version: "12.0"
spec:
  containers:
  - name: helloworld
    image: karthequian/helloworld:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: homepage-staging
  labels:
    env: staging
    team: web
    dev-lead: karthik
    application_type: ui
    release-version: "12.0"
spec:
  containers:
  - name: helloworld
    image: karthequian/helloworld:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: homepage-prod
  labels:
    env: production
    team: web
    dev-lead: karthik
    application_type: ui
    release-version: "12.0"
spec:
  containers:
  - name: helloworld
    image: karthequian/helloworld:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: login-dev
  labels:
    env: development
    team: auth
    dev-lead: jim
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: login
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: login-staging
  labels:
    env: staging
    team: auth
    dev-lead: jim
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: login
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: login-prod
  labels:
    env: production
    team: auth
    dev-lead: jim
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: login
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: cart-dev
  labels:
    env: development
    team: ecommerce
    dev-lead: carisa
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: cart
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: cart-staging
  labels:
    env: staging
    team: ecommerce
    dev-lead: carisa
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: cart
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: cart-prod
  labels:
    env: production
    team: ecommerce
    dev-lead: carisa
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: cart
    image: karthequian/ruby:latest
---

apiVersion: v1
kind: Pod
metadata:
  name: social-dev
  labels:
    env: development
    team: marketing
    dev-lead: carisa
    application_type: api
    release-version: "2.0"
spec:
  containers:
  - name: social
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: social-staging
  labels:
    env: staging
    team: marketing
    dev-lead: marketing
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: social
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: social-prod
  labels:
    env: production
    team: marketing
    dev-lead: marketing
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: social
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: catalog-dev
  labels:
    env: development
    team: ecommerce
    dev-lead: daniel
    application_type: api
    release-version: "4.0"
spec:
  containers:
  - name: catalog
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: catalog-staging
  labels:
    env: staging
    team: ecommerce
    dev-lead: daniel
    application_type: api
    release-version: "4.0"
spec:
  containers:
  - name: catalog
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: catalog-prod
  labels:
    env: production
    team: ecommerce
    dev-lead: daniel
    application_type: api
    release-version: "4.0"
spec:
  containers:
  - name: catalog
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: quote-dev
  labels:
    env: development
    team: ecommerce
    dev-lead: amy
    application_type: api
    release-version: "2.0"
spec:
  containers:
  - name: quote
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: quote-staging
  labels:
    env: staging
    team: ecommerce
    dev-lead: amy
    application_type: api
    release-version: "2.0"
spec:
  containers:
  - name: quote
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: quote-prod
  labels:
    env: production
    team: ecommerce
    dev-lead: amy
    application_type: api
    release-version: "1.0"
spec:
  containers:
  - name: quote
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: ordering-dev
  labels:
    env: development
    team: purchasing
    dev-lead: chen
    application_type: backend
    release-version: "2.0"
spec:
  containers:
  - name: ordering
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: ordering-staging
  labels:
    env: staging
    team: purchasing
    dev-lead: chen
    application_type: backend
    release-version: "2.0"
spec:
  containers:
  - name: ordering
    image: karthequian/ruby:latest
---
apiVersion: v1
kind: Pod
metadata:
  name: ordering-prod
  labels:
    env: production
    team: purchasing
    dev-lead: chen
    application_type: backend
    release-version: "2.0"
spec:
  containers:
  - name: ordering
    image: karthequian/ruby:latest
---
```



```
xhxu-mac:test xhxu$ kubectl create -f sample-infrastructure-with-labels.yml
pod "homepage-dev" created
pod "homepage-staging" created
pod "homepage-prod" created
pod "login-dev" created
pod "login-staging" created
pod "login-prod" created
pod "cart-dev" created
pod "cart-staging" created
pod "cart-prod" created
pod "social-dev" created
pod "social-staging" created
pod "social-prod" created
pod "catalog-dev" created
pod "catalog-staging" created
pod "catalog-prod" created
pod "quote-dev" created
pod "quote-staging" created
pod "quote-prod" created
pod "ordering-dev" created
pod "ordering-staging" created
pod "ordering-prod" created

xhxu-mac:test xhxu$ kubectl get pods --show-labels
NAME                  READY     STATUS              RESTARTS   AGE       LABELS
cart-dev              0/1       ContainerCreating   0          1m        application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod             0/1       ContainerCreating   0          1m        application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging          1/1       Running             0          1m        application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-dev           0/1       ContainerCreating   0          1m        application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod          0/1       ContainerCreating   0          1m        application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging       0/1       ContainerCreating   0          1m        application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
helloworld            1/1       Running             0          8m        application_type=ui,author=karthequian,env=production,release-version=1.0
homepage-dev          1/1       Running             0          1m        application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod         1/1       Running             0          1m        application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging      1/1       Running             0          1m        application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web
hw-747fddfdb8-7jmpl   1/1       Running             0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running             0          27m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running             0          27m       pod-template-hash=747fddfdb8,run=hw
login-dev             1/1       Running             0          1m        application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod            0/1       ContainerCreating   0          1m        application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging         0/1       ContainerCreating   0          1m        application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev          1/1       Running             0          1m        application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod         0/1       ContainerCreating   0          1m        application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging      0/1       ContainerCreating   0          1m        application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev             0/1       ContainerCreating   0          1m        application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod            0/1       ContainerCreating   0          1m        application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging         0/1       ContainerCreating   0          1m        application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev            0/1       ContainerCreating   0          1m        application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod           0/1       ContainerCreating   0          1m        application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging        0/1       ContainerCreating   0          1m        application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```



##### single label

```
xhxu-mac:test xhxu$ kubectl get pods --selector env=production
NAME            READY     STATUS              RESTARTS   AGE
cart-prod       0/1       ContainerCreating   0          1m
catalog-prod    1/1       Running             0          1m
helloworld      1/1       Running             0          9m
homepage-prod   1/1       Running             0          1m
login-prod      1/1       Running             0          1m
ordering-prod   0/1       ContainerCreating   0          1m
quote-prod      1/1       Running             0          1m
social-prod     0/1       ContainerCreating   0          1m
xhxu-mac:test xhxu$ kubectl get pods --selector env=production --show-labels
NAME            READY     STATUS              RESTARTS   AGE       LABELS
cart-prod       1/1       Running             0          1m        application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
catalog-prod    1/1       Running             0          1m        application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
helloworld      1/1       Running             0          9m        application_type=ui,author=karthequian,env=production,release-version=1.0
homepage-prod   1/1       Running             0          1m        application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
login-prod      1/1       Running             0          1m        application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
ordering-prod   1/1       Running             0          1m        application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
quote-prod      1/1       Running             0          1m        application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
social-prod     0/1       ContainerCreating   0          1m        application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
xhxu-mac:test xhxu$ kubectl get pods --selector dev-lead=carisa
NAME           READY     STATUS    RESTARTS   AGE
cart-dev       1/1       Running   0          2m
cart-prod      1/1       Running   0          2m
cart-staging   1/1       Running   0          2m
social-dev     1/1       Running   0          2m
```



```
xhxu-mac:test xhxu$ kubectl get pods -l 'release-version in (1.0,2.0)'
NAME               READY     STATUS    RESTARTS   AGE
cart-dev           1/1       Running   0          4m
cart-prod          1/1       Running   0          4m
cart-staging       1/1       Running   0          4m
helloworld         1/1       Running   0          12m
login-dev          1/1       Running   0          4m
login-prod         1/1       Running   0          4m
login-staging      1/1       Running   0          4m
ordering-dev       1/1       Running   0          4m
ordering-prod      1/1       Running   0          4m
ordering-staging   1/1       Running   0          4m
quote-dev          1/1       Running   0          4m
quote-prod         1/1       Running   0          4m
quote-staging      1/1       Running   0          4m
social-dev         1/1       Running   0          4m
social-prod        1/1       Running   0          4m
social-staging     1/1       Running   0          4m

xhxu-mac:test xhxu$ kubectl get pods -l 'release-version in (1.0,2.0)' --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
cart-dev           1/1       Running   0          5m        application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod          1/1       Running   0          5m        application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging       1/1       Running   0          5m        application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
helloworld         1/1       Running   0          13m       application_type=ui,author=karthequian,env=production,release-version=1.0
login-dev          1/1       Running   0          5m        application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod         1/1       Running   0          5m        application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging      1/1       Running   0          5m        application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev       1/1       Running   0          5m        application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod      1/1       Running   0          5m        application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging   1/1       Running   0          5m        application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev          1/1       Running   0          5m        application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod         1/1       Running   0          5m        application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging      1/1       Running   0          5m        application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev         1/1       Running   0          5m        application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod        1/1       Running   0          5m        application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging     1/1       Running   0          5m        application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing

xhxu-mac:test xhxu$ kubectl get pods -l 'release-version notin (1.0,2.0)' --show-labels
NAME                  READY     STATUS    RESTARTS   AGE       LABELS
catalog-dev           1/1       Running   0          5m        application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod          1/1       Running   0          5m        application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging       1/1       Running   0          5m        application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
homepage-dev          1/1       Running   0          5m        application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod         1/1       Running   0          5m        application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging      1/1       Running   0          5m        application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web
hw-747fddfdb8-7jmpl   1/1       Running   0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running   0          32m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running   0          32m       pod-template-hash=747fddfdb8,run=hw
```



##### multipe labels

```
xhxu-mac:test xhxu$ kubectl get pods --selector dev-lead=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
homepage-staging   1/1       Running   0          3m
```



取反

<pre>
xhxu-mac:test xhxu$ kubectl get pods --selector dev-lead!=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
cart-staging       1/1       Running   0          3m
catalog-staging    1/1       Running   0          3m
login-staging      1/1       Running   0          3m
ordering-staging   1/1       Running   0          3m
quote-staging      1/1       Running   0          3m
social-staging     1/1       Running   0          3m
</pre>


