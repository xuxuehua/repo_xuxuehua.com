---
title: "k8s_in_aws"
date: 2019-03-05 11:28
---


[TOC]



# EKS

Kubernetes on AWS



![img](https://snag.gy/FPMuN5.jpg)









## IAM configuration



### Policies

In order to access the EKS cluster services an IAM policy is needed.  The following, while "open" only allows eks service access, and is appropriate for most users who need to manipulate the EKS service.

https://console.aws.amazon.com/iam/
Create a Policy with the following JSON object, and name it AdminEKSPolicy

iam_eks_policy.json
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "eks:*"
            ],
            "Resource": "*"
        }
    ]
}
```

We also need CloudFormation access which we can call AdminCloudFormationPolicy
iam_cf_policy.json

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "*",
            "Resource": "*"
        }
    ]
}
```



### Roles

Create and then select `AWS service` , `ESK`

Click next until the `Role name` with `ClusterEksRole`

This will have two Amazon defined policies:

AmazonEKSServicePolicy 
AmazonEKSClusterPolicy 

View the role in details and copy `Role ARN`



### Users

create two users, an admin user and a second eks system user.

User 1:
 clusterAdmin 

 - eks admin policy
 - k8s admin "system:master" group
   the following policies (at Review section):
   AdminEKSPolicy
   AdminCloudFormationPolicy
   AmazonEKSServicePolicy 
   AmazonEKSClusterPolicy 

User 2:
 clusterUser

 - no IAM policies

 - k8s admin "system:master" group

   the following policies (at Review section):

​       AdminEKSPolicy

For both users, create programmatic credentials, and for the admin user, create a password credential as well.







## VPC created for EKS use

Signout and login as clusterAdmin user with below regions

EKS is only supported in three regions:
us-east-1 (Northern VA)
us-west-2 (Oregon)
eu-west-1 (Ireland)

Make sure you are in one of those regions when launching your VPC template

https://console.aws.amazon.com/cloudformation/



Then `Create Stack`  and specify below template

From S3 template
https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-08-30/amazon-eks-vpc-sample.yaml

(New version   https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/amazon-eks-vpc-private-subnets.yaml )



and specify the stack name `classEKSVPC`



Capture (into an outputs file):
VPC-ID:
SECURITY-GROUP-ID:
SUBNET-IDS:



So EKS will deploy into this specific region which not impact other env.





## Install kubectl for EKS auth

### kubectl

Install kubectl as normal from the instructions found here:
https://kubernetes.io/docs/tasks/tools/install-kubectl/

Linux:
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

MacOS:
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl

Windows:
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.12.0/bin/windows/amd64/kubectl.exe



### aws-iam-authenticator

We also need the aws-iam-authenticator binary:
https://docs.aws.amazon.com/eks/latest/userguide/configure-kubectl.html

You need the binary appropriate to your OS

linux 

```
curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.13.7/2019-06-11/bin/linux/amd64/aws-iam-authenticator
```

mac

```
brew install aws-iam-authenticator
```



In both cases, make the binary executable if necessary (chmod +x), and copy it to a directory in the command PATH (/usr/local/bin or %system32%/)







### AWS CLI

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip" && \ unzip awscli-bundle.zip && \
cd awscli-bundle && \
sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```




## Create cluster control plane
 https://us-west-2.console.aws.amazon.com/eks

We can launch via the EKS console: https://console.aws.amazon.com/ek

Input Cluster Name with `classCluster`, click next

The VPC we need is 192.168.0.0/16 subnet

And Security Group is the right classEKSVPC 



After cluster activing, we go to below command line operation

```
aws configure --profile=clusterAdmin (AWS User ID)

# Key & Secret could get it from IAM console
Default region name [Oregon]: us-west-2
Default output format [json]: json
```



Enable variables

```
export AWS_PROFILE=clusterAdmin # Re-enable it when you reset your terminal (AWS UID)
```



```
aws eks --region (region) update-kubeconfig --name classCluster

# Verify Kubernetes access
And lastly, we should be able to confirm our access

$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   4h


kubectl get pods
kubectl get nodes
```



## Create worker nodes

Go to EC2 to setup Key Pair with Name `clusterAdmin_EC2_keyPair`



https://console.aws.amazon.com/cloudformation/

create stack with below S3 template
https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-08-30/amazon-eks-nodegroup.yaml

(New from AWS TS https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/amazon-eks-nodegroup.yaml )

and then specify the stack name `eksNodeStake` , Clustername `classCluster` and VPC with `classEKSVPS-ControlPlainSecurity`

NodeGroupName with `workerNodes`

NodeImageID with below Oregon AMI `ami-0a54c984b9f908c81`

```
Region	                     Amazon EKS-optimized   AMI	with GPU support
US West (Oregon) (us-west-2)  ami-0a54c984b9f908c81	ami-0731694d53ef9604b
US East (N. Va)  (us-east-1)	ami-0440e4f6b9713faf6 (new from AWS TS ami-0f2e8e5663e16b436 )	ami-058bfb8c236caae89
EU (Ireland)     (eu-west-1)	ami-0c7a4976cb6fafd3a	ami-0706dc8a5eed2eed9
```

And also complete the rest of option: KeyName, VpsId, Subnets (we have 3), and click next again to go to final page.

acknowledge this create and finish them.





Capture the worker ARN (NodeInstanceRole) from the CloudFormation output.
(store it in our outputs file)

We'll also be updating the aws-auth-cm.yaml document with that ARN

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapRoles: |
    - rolearn: arn:xxxxxxxxxxxxxx
      username: system:node:{{EC2PrivateDNSName}}
      groups:
        - system:bootstrappers
        - system:nodes
```

so that the newly created workers have authorization to connect to our EKS control plane, and then installing the configmap to enable the authentication:

```
$ kubectl apply -f aws-auth-cm.yaml
configmap "aws-auth" created

$ kubectl get nodes
NAME                                            STATUS    ROLES     AGE       VERSION
ip-192-168-126-221.us-west-2.compute.internal   Ready     <none>    38s       v1.10.3
ip-192-168-133-98.us-west-2.compute.internal    Ready     <none>    37s       v1.10.3
ip-192-168-222-158.us-west-2.compute.internal   Ready     <none>    40s       v1.10.3
```







## Scale policy 

In order to make use of the ASG that the default node cloudformation template creates, we need to add a policy to the group via the EC2 console.

Select Autoscaling Groups, and the third tap Scaling Policies

Create a Policy

 - simple policy defines a target CPU utilization.  I find 70% works well
    for many environments.
 - Name: `scale-70` Taget value: 70



In our unused cluster, the number of instances should start to shrink.

```
# before
$ kubectl get nodes
NAME                                            STATUS    ROLES     AGE       VERSION
ip-192-168-126-221.us-west-2.compute.internal   Ready     <none>    10m       v1.10.3
ip-192-168-133-98.us-west-2.compute.internal    Ready     <none>    10m       v1.10.3
ip-192-168-222-158.us-west-2.compute.internal   Ready     <none>    10m       v1.10.3

# after
$ kubectl get nodes
NAME                                            STATUS    ROLES     AGE       VERSION
ip-192-168-222-158.us-west-2.compute.internal   Ready     <none>    54m       v1.10.3
```



## Label the nodes

Go to CloudFormation and create another template

From S3 template
https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-08-30/amazon-eks-nodegroup.yaml



specify the name `labelNodes` , ClusterName `classCluster`,  NodeGroupName `labelNodes`,

and pick up the t2.small instances. and NodeImageId with below Oregon ami `ami-0a54c984b9f908c81`

```
Region	                     Amazon EKS-optimized   AMI	with GPU support
US West (Oregon) (us-west-2)  ami-0a54c984b9f908c81	ami-0731694d53ef9604b
US East (N. Va)  (us-east-1)	ami-0440e4f6b9713faf6	ami-058bfb8c236caae89
EU (Ireland)     (eu-west-1)	ami-0c7a4976cb6fafd3a	ami-0706dc8a5eed2eed9
```

and also BootstrapArguments

```
--kubelet-extra-args '--node-labels "nodetype=generalpurpose"'
```



And acknowledge the create resources at final page.



goto labelNodes stack and find arn info and fill up into output.txt

And also append it into aws-auth-cm.yaml

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapRoles: |
    - rolearn: arn:aws:iam::959612087337:role/eksNodeStack-NodeInstanceRole-N6ET9HHZXHSE
      username: system:node:{{EC2PrivateDNSName}}
      groups:
        - system:bootstrappers
        - system:nodes
    - rolearn: arn:aws:iam::959612087337:role/labelNodes-NodeInstanceRole-HDIXQW4LUDF7
      username: system:node:{{EC2PrivateDNSName}}
      groups:
        - system:bootstrappers
        - system:nodes

```



Apply the yaml file

```
$ kubectl apply -f aws-auth-cm.yaml
configmap "aws-auth" configured

$ kubectl get nodes
NAME                                            STATUS    ROLES     AGE       VERSION
ip-192-168-111-130.us-west-2.compute.internal   Ready     <none>    42s       v1.10.3
ip-192-168-133-251.us-west-2.compute.internal   Ready     <none>    40s       v1.10.3
ip-192-168-222-158.us-west-2.compute.internal   Ready     <none>    2h        v1.10.3
ip-192-168-236-150.us-west-2.compute.internal   Ready     <none>    38s       v1.10.3
```



and also hostname.yaml

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: hostname-v2
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: hostname-v2
        version: v2
    spec:
      containers:
      - image: rstarmer/hostname:v2
        imagePullPolicy: Always
        name: hostname
      nodeSelector:
        nodetype: generalpurpose
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: hostname-v2
  name: hostname-v2
spec:
  ports:
  - name: web
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: hostname-v2
```



```
$ kubectl apply -f hostname.yaml
deployment.extensions "hostname-v2" created
service "hostname-v2" created
```

```
$ kubectl describe pod hostname-v2-6499cb8cc8-2wzmm
Name:               hostname-v2-6499cb8cc8-2wzmm
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               ip-192-168-236-150.us-west-2.compute.internal/192.168.236.150
Start Time:         Wed, 06 Mar 2019 17:01:10 +0800
Labels:             app=hostname-v2
                    pod-template-hash=2055764774
                    version=v2
...
...
```








## create an ELB
aws iam create-service-linked-role --aws-service-name elasticloadbalancing.amazonaws.com



By default EKS doesn't have any storage classes defined, and we need to have a storage class model in order to be able to create persistent storage.

Luckily the 'plumbing' is already there, and we simply have to enable our storage class connection to the underlying EBS service.



gp-storage.yaml

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: gp2
  annotations:
    storageclass.kubernetes.io/is-default-class: 'true'
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
mountOptions:
  - debug

```



This creates a "standard" EBS based volume when a persistent volume request is created.  Size is determined by the persistent volume request.

In addition, we have configured this resource as our default, so if an application asks for storage without defining a class, we'll get this class configured.

Creating some Fast (100iops/GB) SSD Storage is also straightforward:

fast-storage.yaml

```
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: fast-100
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
  iopsPerGB: "100"
reclaimPolicy: Retain
mountOptions:
  - debug

```




```
$ kubectl create -f gp-storage.yaml -f fast-storage.html
storageclass.storage.k8s.io "gp2" created
storageclass.storage.k8s.io "fast-100" created

$ kubectl get storageclasses
NAME            PROVISIONER             AGE
fast-100        kubernetes.io/aws-ebs   15s
gp2 (default)   kubernetes.io/aws-ebs   15s
```





Additional parameters are available to tune for different classes of storage, and they are defined here:
https://kubernetes.io/docs/concepts/storage/storage-classes/#aws-ebs



### Mapping storage



Once storage classes are defined, mapping uses the standard Kubernetes models, and as we defined "Retain" as the reclaim policy, we have storage
that maintains persistence even when we delete the PersistentVolumeClaim and the Pod that claimed the storage.

Let's create a simple app that gets a 10G volume and mounts it into the web directory:



hostname-volume.yaml

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: hostname-volume
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: hostname-volume
        version: v1
    spec:
      volumes:
      - name: hostname-pvc
        persistentVolumeClaim:
          claimName: hostname-pvc
      containers:
      - image: rstarmer/hostname:v1
        imagePullPolicy: Always
        name: hostname
        volumeMounts:
          - mountPath: "/www"
            name: hostname-pvc
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: hostname-volume
  name: hostname-volume
spec:
  ports:
  - name: web
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: hostname-volume
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: hostname-pvc
spec:
  storageClassName: gp2
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```



```
$ kubectl create -f hostname-volume.yaml
deployment.extensions "hostname-volume" created
service "hostname-volume" created
persistentvolumeclaim "hostname-pvc" created

$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS    CLAIM                  STORAGECLASS   REASON    AGE
pvc-ac9533df-3ffb-11e9-b634-0acae188e214   1Gi        RWO            Retain           Bound     default/hostname-pvc   gp2                      15s

$ kubectl get pvc -o wide
NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
hostname-pvc   Bound     pvc-ac9533df-3ffb-11e9-b634-0acae188e214   1Gi        RWO            gp2            27s

$ kubectl exec -it $(kubectl get pod -l app=hostname-volume -o jsonpath={.items..metadata.name}) -- df -h /www
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvdck      976M  2.6M  907M   1% /www

$ kubectl get pods
NAME                               READY     STATUS    RESTARTS   AGE
hostname-v2-6499cb8cc8-2wzmm       1/1       Running   0          1h
hostname-volume-8479ffdd6f-lsj84   1/1       Running   0          1m
```



We can see that there is now a PV that is created with a PVC that claims it, and if we check our pod, we can see that our www directory is empty (because it has a 1G volume mounted to it).



### Clean up the storage

A simple challenge.  We can clean up our hostname-volume "stack" with:

```
kubectl delete -f hostname-volume.yaml
```

But this leaves a resource behind. If we really want to clean up _all_ of our storage, how would we go about cleaning up the volumes themselves.



The solution is simple:

First remove the "stack" we created earlier:

```
$ kubectl delete -f hostname-volume.yaml
deployment.extensions "hostname-volume" deleted
service "hostname-volume" deleted
persistentvolumeclaim "hostname-pvc" deleted
```

Then clean up the PV itself.  Note, you do _not_ want to do this if there are more than one PVs created, as this will grab _all_ of the volumes and delete them!

```
$ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS    CLAIM                  STORAGECLASS   REASON    AGE
pvc-ac9533df-3ffb-11e9-b634-0acae188e214   1Gi        RWO            Retain           Bound     default/hostname-pvc   gp2                      6m

$ kubectl delete pv $(kubectl get pv -o jsonpath={.items..metadata.name})
persistentvolume "pvc-ac9533df-3ffb-11e9-b634-0acae188e214" deleted
```





## Networking

Networking in EKS uses the VPC-CNI project to use the AWS VPC network model to provide connectivity across the cluster.  This is more efficient than having another layer of networking (e.g. Flannel, Calico, Weave, etc.) deployed as an overlay on top of the system, and maps perfectly into the VPC environment, using the VPC network management and IPAM services to support address management further improving the efficiency of the overall Kubernetes deployment.

We can see this in action by looking at the network information for any pod in the system:

```
# deprecated, but a simple way to run a pod
$ kubectl run --image alpine alpine sleep 3600
deployment.apps "alpine" created 

$ IPs=`kubectl get pod $(kubectl get pod -l run=alpine -o jsonpath={.items..metadata.name}) -o yaml | awk '/IP/ {print $2}'`


$ echo $IPs
192.168.222.158 192.168.196.154

# get pods IPs
$ for n in $IPs; do kubectl exec -it $(kubectl get pod -l run=alpine -o jsonpath={.items..metadata.name})  traceroute $n ; done
traceroute to 192.168.222.158 (192.168.222.158), 30 hops max, 46 byte packets
 1  ip-192-168-222-158.us-west-2.compute.internal (192.168.222.158)  0.005 ms  0.004 ms  0.002 ms
traceroute to 192.168.196.154 (192.168.196.154), 30 hops max, 46 byte packets
 1  alpine-6b9858595b-n9xqt (192.168.196.154)  0.005 ms  0.004 ms  0.003 ms
```



### Ingress

Adding ingress is a Kubernetes function

We'll add the traffic load balancer as an ingress function, and make use of the EKS integration with Amazon ELB to enable external access.

As ingress can route based on DNS, we can also do a little DNS manipulation to get traffic routed to our resources.

1) Since we're using 1.10.0 Kubernetes (or newer) we'll need to make sure we have a cluster role binding for the services to use:

```
$ kubectl apply -f https://raw.githubusercontent.com/containous/traefik/master/examples/k8s/traefik-rbac.yaml
clusterrole.rbac.authorization.k8s.io "traefik-ingress-controller" created
clusterrolebinding.rbac.authorization.k8s.io "traefik-ingress-controller" created
```



2) We'll leverage the deployment model for our ingress controller, as we don't necessarily want to bind host address, and would rather have the ingress transit through the normal kube-proxy functions (note that we're changing the default "NodePort" type to "LoadBalancer"):

```
$ kubectl apply -f <(curl -so - https://raw.githubusercontent.com/containous/traefik/master/examples/k8s/traefik-deployment.yaml | sed -e 's/NodePort/LoadBalancer/')
serviceaccount "traefik-ingress-controller" created
deployment.extensions "traefik-ingress-controller" created
service "traefik-ingress-service" created

$ kubectl get svc
NAME          TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
hostname-v2   ClusterIP   10.100.137.6   <none>        80/TCP    1h
kubernetes    ClusterIP   10.100.0.1     <none>        443/TCP   6h

$ kubectl get svc -n kube-system
NAME                      TYPE           CLUSTER-IP      EXTERNAL-IP        PORT(S)                       AGE
kube-dns                  ClusterIP      10.100.0.10     <none>             53/UDP,53/TCP                 6h
traefik-ingress-service   LoadBalancer   10.100.220.28   aeeec6e723ffd...   80:30763/TCP,8080:30982/TCP   55s
```



3) We can now expose our hostname app as an ingress resource:

hostname-ingress.yaml

```
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hostname-ingress
  namespace: default
spec:
  rules:
  - host: hostname-v1.local
    http:
      paths:
      - path: /
        backend:
          serviceName: hostname-v1
          servicePort: web
```



```
$ kubectl create -f hostname-ingress.yaml
ingress.extensions "hostname-ingress" created
```

Note that it assumes a hostname of traefik-ui.minikube, so we can confirm access as follows:



1) Get the loadbalancer service address:

```
$ export INGRESS=`kubectl get svc -n kube-system traefik-ingress-service -o jsonpath={.status.loadBalancer.ingress[0].hostname}`

$ echo $INGRESS
aeeec6e723ffd11e9b6340acae188e21-56059226.us-west-2.elb.amazonaws.com
```



Capture the actual IP of one of the loadbalancers in the set:

```
$ export INGRESS_ADDR=`host $INGRESS | head -1 | cut -d' ' -f 4`

$ echo $INGRESS_ADDR
34.213.98.29
```



Verify that it's responding to web requests:

```
curl -sLo /dev/null -Hhost:hostname-v1.local http://${INGRESS_ADDR}/ -w "%{http_code}\n"
```



Add an entry to the local /etc/hosts file to point to our resource:

```
$ echo "$INGRESS_ADDR hostname-v1.local" | sudo tee -a /etc/hosts
34.213.98.29 hostname-v1.local
```

Now try pointing a web browser at that hostname:

http://hostname-v1.local





### Network Policy with Callco 

Enable_Calico_Policy.md
In order to enable policy, a CNI network needs to be in place, and by default the VPC based networking in EKS is already configured appropriately.  Policy however is not part of the VPC networking provided by Amazon, and instead, an integration with the Calico policy manager has been integrated with the VPC CNI service.

Enabling this function is as simple as launching the calico CNI policy manifest from the Amazon VPC CNI project:

```
$ kubectl apply -f https://raw.githubusercontent.com/aws/amazon-vpc-cni-k8s/release-1.1/config/v1.1/calico.yaml --validate=false
daemonset.extensions "calico-node" configured
customresourcedefinition.apiextensions.k8s.io "felixconfigurations.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "bgpconfigurations.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "ippools.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "hostendpoints.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "clusterinformations.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "globalnetworkpolicies.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "globalnetworksets.crd.projectcalico.org" created
customresourcedefinition.apiextensions.k8s.io "networkpolicies.crd.projectcalico.org" created
serviceaccount "calico-node" created
clusterrole.rbac.authorization.k8s.io "calico-node" created
clusterrolebinding.rbac.authorization.k8s.io "calico-node" created
deployment.extensions "calico-typha" created
clusterrolebinding.rbac.authorization.k8s.io "typha-cpha" created
clusterrole.rbac.authorization.k8s.io "typha-cpha" created
configmap "calico-typha-horizontal-autoscaler" created
deployment.extensions "calico-typha-horizontal-autoscaler" created
role.rbac.authorization.k8s.io "typha-cpha" created
serviceaccount "typha-cpha" created
rolebinding.rbac.authorization.k8s.io "typha-cpha" created
service "calico-typha" created
```



This will create a daemonset running the calico policy engine on each configured node.

Let's run a container with curl enabled to test our target system (hostname-v1 from the initial install):

```
$ kubectl run --image rstarmer/curl:v1 curl
deployment.apps "curl" created
```



And let's verify that we can communicate to the http://hostname-v1 service endpoint:

```
kubectl exec -it $(kubectl get pod -l run=curl -o jsonpath={.items..metadata.name})  -- curl --connect-timeout 5 http://hostname-v1
```



Now, we can first disable network access by installing the baseline "Default Deny" policy, which will break our application access:



default-deny.yaml

```
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: default-deny
  namespace: default
spec:
  podSelector:
    matchLabels: {}
```

```
kubectl apply -f default-deny.yaml
```



And check to see that we can't communicate:

```
kubectl exec -it $(kubectl get pod -l run=curl -o jsonpath={.items..metadata.name})  -- curl --connect-timeout 5 http://hostname-v1
```

And then we can add back in a rule allowing access again:



allow-hostname.yaml

```
kind: NetworkPolicy
apiVersion: extensions/v1beta1
metadata:
  namespace: default
  name: allow-hostname
spec:
  podSelector:
    matchLabels:
      app: hostname-v1
  ingress:
    - from:
        - namespaceSelector:
            matchLabels: {}
```

```
kubectl apply -f allow-hostname.yaml
```



And one more check:

```
kubectl exec -it $(kubectl get pod -l run=curl -o jsonpath={.items..metadata.name})  -- curl --connect-timeout 5 http://hostname-v1
```



## Adding Users



Users within the EKS environment are authenticated against AWS IAM, which provides enhanced security.  If we add our 'clusterUser' credentials to the local aws client:

```
cat ~/Downloads/credentials-2.csv

# using above key and secret to access, region is us-west-2
aws configure --profile=clusterUser

export AWS_PROFILE=clusterUser
```



We will see that kubernetes will still try to talk to the API, but wil fail:

```
kubectl get pods
```



Adding additional users to the kubernetes cluster in EKS is done by adding new
users to the "system:masters" group which maps to the equivalent of the ClusterAdmin role in Kubernetes RBAC rules.

The key parameter we need is the User's IAM ARN, which can be pulled from the User IAM page in the AWS console:

aws-auth-cm.yaml

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: aws-auth
  namespace: kube-system
data:
  mapUsers: |
    - userarn: USER-ARN
      username: admin
      groups:
        - system:masters
  mapRoles: |
    - rolearn: NODE-ROLE-ARN
      username: system:node:{{EC2PrivateDNSName}}
      groups:
        - system:bootstrappers
        - system:nodes
    - rolearn: LABEL-NODE-ROLE-ARN
      username: system:node:{{EC2PrivateDNSName}}
      groups:
        - system:bootstrappers
        - system:nodes
```



We need to add the mapUsers: section to the aws-auth-cm.yaml document, and we can do that either locally and "apply" the changes, or we can edit the document in place in the kubernetes service.

We will edit the file in place, as we don't want to have to recreate the worker node role mappings which are part of the same auth structure:

```
export AWS_PROFILE=clusterAdmin
kubectl edit configmap aws-auth -n kube-system
```



Once we're done with the edit, we can switch back to our clusterUser and we should have access to the system:

```
export AWS_PROFILE=clusterUser
kubectl get pods
```



Add the mapUsers: section right after the data: key, above the mapRoles: | line. It should look similar to the aws-auth-cm.yaml document.







## Install_Prometheus

To add metrics to our Kubernetes environment, we'll use Helm to install Prometheus.

First we need helm installed as a client on our workstation, and then we can install the Kubernetes side component in our EKS system.  Get the helm binary for your environment here:

MacOSX:
https://storage.googleapis.com/kubernetes-helm/helm-v2.11.0-darwin-amd64.tar.gz

Linux:
https://storage.googleapis.com/kubernetes-helm/helm-v2.11.0-linux-amd64.tar.gz

Windows:
https://storage.googleapis.com/kubernetes-helm/helm-v2.11.0-windows-amd64.zip

Or use a package manager.

Then we can install the RBAC configuration for tiller so that it has the appropriate access, and lastly we can initialize our helm system:



helm-rbac.yaml

```
# Create a service account for Helm and grant the cluster admin role.
# It is assumed that helm should be installed with this service account
# (tiller).
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: tiller
  namespace: kube-system
```



```
kubectl create -f helm-rbac.yaml
helm init --service-account=tiller
```



Once Helm is installed, launching Prometheus is a simple command, though note that we are defining the storage class that Prometheus should use to store it's metrics:

```
helm install --name promeks --set server.persistentVolume.storageClass=gp2 stable/prometheus
```



And lastly, we want to expose the Prometheus UI so that we can have a look at some of the Pod/Container level metrics:

```
kubectl --namespace default port-forward $(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}") 9090 &
```



Once the portforward is working, we can point a web browser at:

http://localhost:9090

look to see what metrics are being gathered.

container_cpu_usage_seconds_total

And we can also generate a little load if we'd like:

```
kubectl run --image rstarmer/curl:v1 curl
kubectl exec -it $(kubectl get pod -l run=curl -o jsonpath={.items..metadata.name}) -- \
sh -c 'while [[ true ]]; do curl -o - http://hostname-v1/version/ ; done'


```













