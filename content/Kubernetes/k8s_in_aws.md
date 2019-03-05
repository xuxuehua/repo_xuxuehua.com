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

and specify the stack name `classEKSVPS`



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

You need the binary appropriate to your OS:
Linux:
curl -sLO https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-07-26/bin/linux/amd64/aws-iam-authenticator

MacOS:
curl -sLO https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-07-26/bin/darwin/amd64/aws-iam-authenticator

Windows:
curl -sLO https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-07-26/bin/windows/amd64/aws-iam-authenticator.exe

In both cases, make the binary executable if necessary (chmod +x), and copy it to a directory in the command PATH (/usr/local/bin or %system32%/)



### AWS CLI

Install the AWS CLI if you don't already have it
https://docs.aws.amazon.com/cli/latest/userguide/installing.html
(note, if you are on a Mac and don't have pip installed: sudo easy_install pip)

Install the awscli tool via python pip:
pip install awscli --upgrade --user




## Create cluster control plane
 https://us-west-2.console.aws.amazon.com/eks



## Establish Kubectl credentials 

(install aws-iam-authenticator and kubec###l)

 aws eks update-kubeconfig --name cluster_name




## Create worker nodes
 Create an autoscaling group of nodes with cloudformation




## Create an aws-auth configmap to allow the nodes to register
 kubectl apply -f aws-auth-cm.yaml




## Wait for the nodes to register
 kubectl get nodes -w




## Ensure you can create an ELB
aws iam create-service-linked-role --aws-service-name elasticloadbalancing.amazonaws.com




## Use your Kubernetes environment!