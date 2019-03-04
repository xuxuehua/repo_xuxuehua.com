---
title: "example"
date: 2019-03-03 21:17
---


[TOC]



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



#### add & remove

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

```
xhxu-mac:test xhxu$ kubectl get pods --selector dev-lead!=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
cart-staging       1/1       Running   0          3m
catalog-staging    1/1       Running   0          3m
login-staging      1/1       Running   0          3m
ordering-staging   1/1       Running   0          3m
quote-staging      1/1       Running   0          3m
social-staging     1/1       Running   0          3m

```



#### delete 

```
xhxu-mac:test xhxu$ kubectl get pods --show-labels
NAME                  READY     STATUS    RESTARTS   AGE       LABELS
cart-dev              1/1       Running   0          6m        application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod             1/1       Running   0          6m        application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging          1/1       Running   0          6m        application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-dev           1/1       Running   0          6m        application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod          1/1       Running   0          6m        application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging       1/1       Running   0          6m        application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
helloworld            1/1       Running   0          14m       application_type=ui,author=karthequian,env=production,release-version=1.0
homepage-dev          1/1       Running   0          6m        application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod         1/1       Running   0          6m        application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging      1/1       Running   0          6m        application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web
hw-747fddfdb8-7jmpl   1/1       Running   0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running   0          33m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running   0          33m       pod-template-hash=747fddfdb8,run=hw
login-dev             1/1       Running   0          6m        application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod            1/1       Running   0          6m        application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging         1/1       Running   0          6m        application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev          1/1       Running   0          6m        application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod         1/1       Running   0          6m        application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging      1/1       Running   0          6m        application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev             1/1       Running   0          6m        application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod            1/1       Running   0          6m        application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging         1/1       Running   0          6m        application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev            1/1       Running   0          6m        application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod           1/1       Running   0          6m        application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging        1/1       Running   0          6m        application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
xhxu-mac:test xhxu$ kubectl delete pods -l dev-lead=karthik
pod "homepage-dev" deleted
pod "homepage-prod" deleted
pod "homepage-staging" deleted
xhxu-mac:test xhxu$ kubectl get pods --show-labels
NAME                  READY     STATUS        RESTARTS   AGE       LABELS
cart-dev              1/1       Running       0          7m        application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod             1/1       Running       0          7m        application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging          1/1       Running       0          7m        application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-dev           1/1       Running       0          7m        application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod          1/1       Running       0          7m        application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging       1/1       Running       0          7m        application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
helloworld            1/1       Running       0          14m       application_type=ui,author=karthequian,env=production,release-version=1.0
homepage-dev          0/1       Terminating   0          7m        application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod         0/1       Terminating   0          7m        application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging      0/1       Terminating   0          7m        application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web
hw-747fddfdb8-7jmpl   1/1       Running       0          2d        pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-c5lf6   1/1       Running       0          34m       pod-template-hash=747fddfdb8,run=hw
hw-747fddfdb8-tmrl6   1/1       Running       0          34m       pod-template-hash=747fddfdb8,run=hw
login-dev             1/1       Running       0          7m        application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod            1/1       Running       0          7m        application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging         1/1       Running       0          7m        application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev          1/1       Running       0          7m        application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod         1/1       Running       0          7m        application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging      1/1       Running       0          7m        application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev             1/1       Running       0          7m        application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod            1/1       Running       0          7m        application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging         1/1       Running       0          7m        application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev            1/1       Running       0          7m        application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod           1/1       Running       0          7m        application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging        1/1       Running       0          7m        application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```



### health checks

#### normal

```
vim helloworld-with-probes.yml

apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-probe
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
        readinessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          initialDelaySeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 80
        livenessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          timeoutSeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 80
```



```
xhxu-mac:test xhxu$ kubectl create -f helloworld-with-probes.yml'

xhxu-mac:test xhxu$ kubectl get deployments
NAME                               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-probe   1         1         1            0           9s

xhxu-mac:test xhxu$ kubectl get pods
NAME                                               READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-probe-58b85784c-xnz2r   1/1       Running   0          15s
```



#### unnormal

这里端口错误，设置成90

```
vim helloworld-with-bad-liveness-probe.yml

apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-bad-liveness-probe
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
        livenessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # How often (in seconds) to perform the probe.
          periodSeconds: 5
          # Amount of time to wait before timing out
          timeoutSeconds: 1
          # Kubernetes will try failureThreshold times before giving up and restarting the Pod
          failureThreshold: 2
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 90

```

```
xhxu-mac:test xhxu$ kubectl get deployments
NAME                                            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-bad-liveness-probe   1         1         1            1           50s
helloworld-deployment-with-probe                1         1         1            1           12m
```



```
xhxu-mac:test xhxu$ kubectl describe helloworld-deployment-with-bad-liveness-probe-5bbc87b8db-rw9zh
error: the server doesn't have a resource type "helloworld-deployment-with-bad-liveness-probe-5bbc87b8db-rw9zh"
xhxu-mac:test xhxu$ kubectl describe pods/helloworld-deployment-with-bad-liveness-probe-5bbc87b8db-rw9zh
Name:               helloworld-deployment-with-bad-liveness-probe-5bbc87b8db-rw9zh
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               minikube/10.0.2.15
Start Time:         Sun, 03 Mar 2019 15:39:45 +0800
Labels:             app=helloworld
                    pod-template-hash=5bbc87b8db
Annotations:        <none>
Status:             Running
IP:                 172.17.0.5
Controlled By:      ReplicaSet/helloworld-deployment-with-bad-liveness-probe-5bbc87b8db
Containers:
  helloworld:
    Container ID:   docker://11986fd2b5d87f85863e1d3b644a354b595b60799924ac922b4f23a8a3e37bd0
    Image:          karthequian/helloworld:latest
    Image ID:       docker-pullable://karthequian/helloworld@sha256:745af01b498d71c954dd3f11930918800de1bd89182b7415809a21d194872e88
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Sun, 03 Mar 2019 15:41:10 +0800
      Finished:     Sun, 03 Mar 2019 15:41:26 +0800
    Ready:          False
    Restart Count:  4
    Liveness:       http-get http://:90/ delay=10s timeout=1s period=5s #success=1 #failure=2
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-47q5g (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             False
  ContainersReady   False
  PodScheduled      True
Volumes:
  default-token-47q5g:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-47q5g
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  1m                default-scheduler  Successfully assigned default/helloworld-deployment-with-bad-liveness-probe-5bbc87b8db-rw9zh to minikube
  Normal   Pulling    52s (x4 over 1m)  kubelet, minikube  pulling image "karthequian/helloworld:latest"
  Warning  Unhealthy  52s (x6 over 1m)  kubelet, minikube  Liveness probe failed: Get http://172.17.0.5:90/: dial tcp 172.17.0.5:90: connect: connection refused
  Normal   Killing    52s (x3 over 1m)  kubelet, minikube  Killing container with id docker://helloworld:Container failed liveness probe.. Container will be killed and recreated.
  Normal   Pulled     49s (x4 over 1m)  kubelet, minikube  Successfully pulled image "karthequian/helloworld:latest"
  Normal   Created    49s (x4 over 1m)  kubelet, minikube  Created container
  Normal   Started    49s (x4 over 1m)  kubelet, minikube  Started container
```





### upgrade

```
vim helloworld-black.yml

apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: navbar-deployment
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 3 # tells deployment to run 3 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:black
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: navbar-service
spec:
  # if your cluster supports it, uncomment the following to automatically create
  # an external load-balanced IP for the frontend service.
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: helloworld

```



```
xhxu-mac:test xhxu$ kubectl create -f helloworld-black.yml --record
deployment.apps "navbar-deployment" created
service "navbar-service" created
xhxu-mac:test xhxu$ kubectl get service
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        51s
navbar-service   NodePort    10.108.47.218   <none>        80:30683/TCP   42s
xhxu-mac:test xhxu$ minikube  service navbar-service
🎉  Opening kubernetes service default/navbar-service in default browser...
```

> 打开浏览器看到页面内容



更新背景色为蓝色

```
xhxu-mac:test xhxu$ kubectl set image deployment/navbar-deployment helloworld=karthequian/helloworld:blue
deployment.apps "navbar-deployment" image updated
xhxu-mac:test xhxu$ kubectl get deployments
NAME                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
navbar-deployment   3         4         1            3           2m
```



恢复到之前版本

```
xhxu-mac:test xhxu$ kubectl rollout history deployment/navbar-deployment
deployments "navbar-deployment"
REVISION  CHANGE-CAUSE
1         kubectl create --filename=helloworld-black.yml --record=true
2         kubectl set image deployment/navbar-deployment helloworld=karthequian/helloworld:blue

xhxu-mac:test xhxu$
xhxu-mac:test xhxu$ kubectl rollout undo deployment/navbar-deployment
deployment.apps "navbar-deployment"
```



## guestbook

### initiaze complicated task

```
vim guestbook.yml
```

```
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis
    tier: backend
    role: master
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis
    tier: backend
    role: master
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: redis-master
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
        role: master
        tier: backend
    spec:
      containers:
      - name: master
        image: gcr.io/google_containers/redis:e2e  # or just image: redis
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        ports:
        - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis
    tier: backend
    role: slave
spec:
  ports:
  - port: 6379
  selector:
    app: redis
    tier: backend
    role: slave
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: redis-slave
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: redis
        role: slave
        tier: backend
    spec:
      containers:
      - name: slave
        image: gcr.io/google_samples/gb-redisslave:v1
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
          # If your cluster config does not include a dns service, then to
          # instead access an environment variable to find the master
          # service's host, comment out the 'value: dns' line above, and
          # uncomment the line below:
          # value: env
        ports:
        - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  type: NodePort
  ports:
  - port: 80
  selector:
    app: guestbook
    tier: frontend
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google-samples/gb-frontend:v4
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
          # If your cluster config does not include a dns service, then to
          # instead access environment variables to find service host
          # info, comment out the 'value: dns' line above, and uncomment the
          # line below:
          # value: env
        ports:
        - containerPort: 80
```



```
xhxu-mac:test xhxu$ kubectl get deployments
NAME           DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
frontend       3         3         3            0           23s
redis-master   1         1         1            0           23s
redis-slave    2         2         2            0           23s
xhxu-mac:test xhxu$ kubectl get services
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
frontend       NodePort    10.105.81.29    <none>        80:32355/TCP   46s
kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        52s
redis-master   ClusterIP   10.102.41.68    <none>        6379/TCP       46s
redis-slave    ClusterIP   10.101.52.152   <none>        6379/TCP       46s
xhxu-mac:test xhxu$ minikube service frontend
🎉  Opening kubernetes service default/frontend in default browser...
```





### enable minikube dashboard

```
xhxu-mac:test xhxu$ minikube addons list
- addon-manager: enabled
- dashboard: disabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- gvisor: disabled
- heapster: disabled
- ingress: disabled
- logviewer: disabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
- storage-provisioner-gluster: disabled

xhxu-mac:test xhxu$ minikube  addons enable dashboard
✅  dashboard was successfully enabled

xhxu-mac:test xhxu$ minikube  addons enable heapster
✅  heapster was successfully enabled

xhxu-mac:test xhxu$ minikube  addons list
- addon-manager: enabled
- dashboard: enabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- gvisor: disabled
- heapster: enabled
- ingress: disabled
- logviewer: disabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
- storage-provisioner-gluster: disabled
xhxu-mac:test xhxu$ minikube dashboard
🔌  Enabling dashboard ...
🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:54649/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/ in your default browser...

```





## configmap

动态传递data



```
vim reader-deployment.yml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: logreader
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: logreader
    spec:
      containers:
      - name: logreader
        image: karthequian/reader:latest
        env:
        - name: log_level
          value: "error"
```

> 这里传递了env 里面的参数





```
xhxu-mac:test xhxu$ kubectl get pods
NAME                           READY     STATUS        RESTARTS   AGE
logreader-86bfcfbf76-4ghzs     1/1       Running       0          38s

xhxu-mac:test xhxu$ kubectl logs logreader-86bfcfbf76-4ghzs
Log level passed via env variables was: 'error'
Log level passed via env variables was: 'error'
Log level passed via env variables was: 'error'
Log level passed via env variables was: 'error'
Log level passed via env variables was: 'error'
```



通过configmap 修改输出



```
xhxu-mac:test xhxu$ kubectl create configmap logger --from-literal=log_level=debug
configmap "logger" created
xhxu-mac:test xhxu$ vim reader-configmap-deployment.yml
```

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: logreader-dynamic
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: logreader-dynamic
    spec:
      containers:
      - name: logreader
        image: karthequian/reader:latest
        env:
        - name: log_level
          valueFrom:
            configMapKeyRef:
              name: logger #Read from a configmap called log-level
              key: log_level  #Read the key called log_level
```



```
xhxu-mac:test xhxu$ kubectl create -f reader-configmap-deployment.yml
deployment.extensions "logreader-dynamic" created
xhxu-mac:test xhxu$ kubectl get configmaps
NAME      DATA      AGE
logger    1         1m

xhxu-mac:test xhxu$ kubectl get configmap/logger -o yaml
apiVersion: v1
data:
  log_level: debug
kind: ConfigMap
metadata:
  creationTimestamp: 2019-03-03T09:07:11Z
  name: logger
  namespace: default
  resourceVersion: "78987"
  selfLink: /api/v1/namespaces/default/configmaps/logger
  uid: ba88bdbd-3d93-11e9-9f7b-0800273e7607
```



```
xhxu-mac:test xhxu$ kubectl get pods
NAME                                READY     STATUS    RESTARTS   AGE
logreader-86bfcfbf76-4ghzs          1/1       Running   0          4m
logreader-dynamic-f49f5fcdb-nl7dt   1/1       Running   0          1m
xhxu-mac:test xhxu$ kubectl logs logreader-dynamic-f49f5fcdb-nl7dt
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
Log level passed via env variables was: 'debug'
```

> 这里的输出变了







## secrets

### 生成

```
xhxu-mac:test xhxu$ kubectl create secret generic apikey --from-literal=api_key=123456
secret "apikey" created

xhxu-mac:test xhxu$ kubectl get secrets
NAME                  TYPE                                  DATA      AGE
apikey                Opaque                                1         18s
default-token-47q5g   kubernetes.io/service-account-token   3         2d

xhxu-mac:test xhxu$ kubectl get secret apikey
NAME      TYPE      DATA      AGE
apikey    Opaque    1         31s
xhxu-mac:test xhxu$ kubectl get secret apikey  -o yaml
apiVersion: v1
data:
  api_key: MTIzNDU2
kind: Secret
metadata:
  creationTimestamp: 2019-03-03T09:25:49Z
  name: apikey
  namespace: default
  resourceVersion: "80356"
  selfLink: /api/v1/namespaces/default/secrets/apikey
  uid: 5526b626-3d96-11e9-9f7b-0800273e7607
type: Opaque

```



### 使用

部署secret reader

```
vim secretreader-deployment.yaml

apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: secretreader
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: secretreader
    spec:
      containers:
      - name: secretreader
        image: karthequian/secretreader:latest
        env:
        - name: api_key
          valueFrom:
            secretKeyRef:
              name: apikey
              key: api_key
```



```
deployment.extensions "secretreader" created
xhxu-mac:test xhxu$ kubectl get pods
NAME                                READY     STATUS    RESTARTS   AGE
logreader-86bfcfbf76-4ghzs          1/1       Running   0          2h
logreader-dynamic-f49f5fcdb-nl7dt   1/1       Running   0          2h
secretreader-554bbcd469-xfthd       1/1       Running   0          10s
xhxu-mac:test xhxu$ kubectl logs secretreader-554bbcd469-xfthd
api_key passed via env variable was: '123456'
api_key passed via env variable was: '123456'
api_key passed via env variable was: '123456'
```



## running job



### simple job

```
vim simplejob.yaml

apiVersion: batch/v1
kind: Job
metadata:
  name: finalcountdown
spec:
  template:
    metadata:
      name: finalcountdown
    spec:
      containers:
      - name: counter
        image: busybox
        command:
         - bin/sh
         - -c
         - "for i in 9 8 7 6 5 4 3 2 1 ; do echo $i ; done"
      restartPolicy: Never #could also be Always or OnFailure
```

```
xhxu-mac:test xhxu$ kubectl get jobs
NAME             DESIRED   SUCCESSFUL   AGE
finalcountdown   1         1            13s

xhxu-mac:test xhxu$ kubectl  get pods
NAME                                READY     STATUS      RESTARTS   AGE
finalcountdown-xgwpk                0/1       Completed   0          37s

xhxu-mac:test xhxu$ kubectl  get pods --show-all
Flag --show-all has been deprecated, will be removed in an upcoming release
NAME                                READY     STATUS      RESTARTS   AGE
finalcountdown-xgwpk                0/1       Completed   0          45s
logreader-86bfcfbf76-4ghzs          1/1       Running     0          2h
logreader-dynamic-f49f5fcdb-nl7dt   1/1       Running     0          2h
secretreader-554bbcd469-xfthd       1/1       Running     0          3m

xhxu-mac:test xhxu$ kubectl logs finalcountdown-xgwpk
9
8
7
6
5
4
3
2
1
```



### cronjob

```
vim cronjob.yaml

apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hellocron
spec:
  schedule: "*/1 * * * *" #Runs every minute (cron syntax) or @hourly.
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hellocron
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from your Kubernetes cluster
          restartPolicy: OnFailure #could also be Always or Never
  suspend: false #Set to true if you want to suspend in the future

```



```
xhxu-mac:test xhxu$ kubectl create -f cronjob.yaml
cronjob.batch "hellocron" created

xhxu-mac:test xhxu$ kubectl get cronjobs
NAME        SCHEDULE      SUSPEND   ACTIVE    LAST SCHEDULE   AGE
hellocron   */1 * * * *   False     0         <none>          13s

xhxu-mac:test xhxu$ kubectl edit cronjobs/hellocron
cronjob.batch "hellocron" edited

# 修改如下
+ suspend: true
- suspend: false


xhxu-mac:test xhxu$ kubectl get cronjobs
NAME        SCHEDULE      SUSPEND   ACTIVE    LAST SCHEDULE   AGE
hellocron   */1 * * * *   True      0         32s             2m
```



## applications

运行daemonset

```
vim daemonset.yaml

apiVersion: apps/v1beta2
kind: DaemonSet
metadata:
  name: example-daemonset
  namespace: default
  labels:
    k8s-app: example-daemonset
spec:
  selector:
    matchLabels:
      name: example-daemonset
  template:
    metadata:
      labels:
        name: example-daemonset
    spec:
      #nodeSelector: minikube # Specify if you want to run on specific nodes
      containers:
      - name: example-daemonset
        image: busybox
        args:
        - /bin/sh
        - -c
        - date; sleep 1000
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
      terminationGracePeriodSeconds: 30
```

```
xhxu-mac:test xhxu$ kubectl create -f daemonset.yaml
daemonset.apps "example-daemonset" created
xhxu-mac:test xhxu$ kubectl get daemonsets
NAME                DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
example-daemonset   1         1         1         1            1           <none>          12s
xhxu-mac:test xhxu$ kubectl get pods
NAME                                READY     STATUS      RESTARTS   AGE
example-daemonset-dchr2             1/1       Running     0          20s
finalcountdown-xgwpk                0/1       Completed   0          10m
hellocron-1551613860-rthrc          0/1       Completed   0          6m
hellocron-1551613920-sc4sc          0/1       Completed   0          5m
logreader-86bfcfbf76-4ghzs          1/1       Running     0          2h
logreader-dynamic-f49f5fcdb-nl7dt   1/1       Running     0          2h
secretreader-554bbcd469-xfthd       1/1       Running     0          13m
xhxu-mac:test xhxu$ kubectl get nodes --show-labels
NAME       STATUS    ROLES     AGE       VERSION   LABELS
minikube   Ready     master    2d        v1.13.3   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=minikube,node-role.kubernetes.io/master=
```



nodeSelector: development 

```
vim daemonset-infra-development.yaml

apiVersion: apps/v1beta2
kind: DaemonSet
metadata:
  name: example-daemonset2
  namespace: default
  labels:
    k8s-app: example-daemonset2
spec:
  selector:
    matchLabels:
      name: example-daemonset2
  template:
    metadata:
      labels:
        name: example-daemonset2
    spec:
      containers:
      - name: example-daemonset2
        image: busybox
        args:
        - /bin/sh
        - -c
        - date; sleep 1000
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
      terminationGracePeriodSeconds: 30
      nodeSelector: 
        infra: "development"

```





nodeSelector: production

```
vim daemonset-infra-prod.yaml

apiVersion: apps/v1beta2
kind: DaemonSet
metadata:
  name: prod-daemonset
  namespace: default
  labels:
    k8s-app: prod-daemonset
spec:
  selector:
    matchLabels:
      name: prod-daemonset
  template:
    metadata:
      labels:
        name: prod-daemonset
    spec:
      containers:
      - name: prod-daemonset
        image: busybox
        args:
        - /bin/sh
        - -c
        - date; sleep 1000
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
      terminationGracePeriodSeconds: 30
      nodeSelector: 
        infra: "production"

```

```
xhxu-mac:test xhxu$ kubectl create -f daemonset-infra-prod.yaml
daemonset.apps "prod-daemonset" created
xhxu-mac:test xhxu$ kubectl get daemonsets
NAME                 DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR       AGE
example-daemonset    1         1         1         1            1           <none>              11m
example-daemonset2   0         0         0         0            0           infra=development   8m
prod-daemonset       0         0         0         0            0           infra=production    7s
xhxu-mac:test xhxu$ kubectl get nodes --show-labels
NAME       STATUS    ROLES     AGE       VERSION   LABELS
minikube   Ready     master    2d        v1.13.3   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=minikube,node-role.kubernetes.io/master=
```



### stateful

```
vim statefulset.yaml

apiVersion: v1
kind: Service
metadata:
  name: zk-cs
  labels:
    app: zk
spec:
  ports:
  - port: 2181
    name: client
  selector:
    app: zk
---
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: zk
spec:
  serviceName: zk-hs
  replicas: 1
  podManagementPolicy: Parallel
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: zk
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - none
              topologyKey: "kubernetes.io/hostname"
      containers:
      - name: kubernetes-zookeeper
        imagePullPolicy: Always
        image: "gcr.io/google_containers/kubernetes-zookeeper:1.0-3.4.10"
        resources:
          requests:
            memory: "500Mi"
            cpu: "0.5"
        ports:
        - containerPort: 2181
          name: client
        - containerPort: 2888
          name: server
        - containerPort: 3888
          name: leader-election
        command:
        - sh
        - -c
        - "start-zookeeper \
          --servers=1 \
          --data_dir=/var/lib/zookeeper/data \
          --data_log_dir=/var/lib/zookeeper/data/log \
          --conf_dir=/opt/zookeeper/conf \
          --client_port=2181 \
          --election_port=3888 \
          --server_port=2888 \
          --tick_time=2000 \
          --init_limit=10 \
          --sync_limit=5 \
          --heap=512M \
          --max_client_cnxns=60 \
          --snap_retain_count=3 \
          --purge_interval=12 \
          --max_session_timeout=40000 \
          --min_session_timeout=4000 \
          --log_level=INFO"
        readinessProbe:
          exec:
            command:
            - sh
            - -c
            - "zookeeper-ready 2181"
          initialDelaySeconds: 10
          timeoutSeconds: 5
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - "zookeeper-ready 2181"
          initialDelaySeconds: 10
          timeoutSeconds: 5
        volumeMounts:
        - name: datadir
          mountPath: /var/lib/zookeeper
      securityContext:
        runAsUser: 1000
        fsGroup: 1000
  volumeClaimTemplates:
  - metadata:
      name: datadir
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi
```



```
xhxu-mac:test xhxu$ kubectl create -f statefulset.yaml
service "zk-cs" created
statefulset.apps "zk" created

xhxu-mac:test xhxu$ kubectl get statefulsets
NAME      DESIRED   CURRENT   AGE
zk        1         1         13s

xhxu-mac:test xhxu$ kubectl get pods
zk-0                                0/1       ContainerCreating   0          1m
```

