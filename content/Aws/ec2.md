---
title: "ec2"
date: 2019-06-24 09:48
---
[TOC]



# Elastic Compute Cloud

Deploy across AWS Regions and Availablity Zones for reliability



## Characteristic

All data is automatically deleted when an EC2 instance stops, fails or is terminated

## Launch 

Can be launch from a pre-configured Amazon Machine Image (AMI)



### User data

In Advanced Details, here we could input below data for bootstrap

```
#!/bin/bash
yum -y install httpd
systemctl start httpd 
if [ ! -f /var/www/html/lab2-app.tar.gz ]; then 
cd /var/www/html
wget https://us-west-2-aws-training.s3.amazonaws.com/awsu-ilt/AWS-100-ESS/v4.1/lab-2-configure-website-datastore/scripts/lab2-app.tar.gz

tar xvfz lab2-app.tar.gz
chown apache:root /var/www/html/rds.config.php
fi
```



query user-data info

```
curl http://169.254.169.254/latest/user-data
```



### Metadata Service

e.g.

```
http://169.254.169.254/latest/meta-data/iam/security-credentials/rolename
```





## Instance Technologies

![image-20200313170002408](ec2.assets/image-20200313170002408.png)



## Current Generation Instances

![image-20200313170015940](ec2.assets/image-20200313170015940.png)



## Reboot vs Stop vs Terminate 

![image-20200313170029266](ec2.assets/image-20200313170029266.png)





# Auto-scaling



## Launch configurations

Specify below info when launch a instance

```
AMI ID
Instance type
Key pair
Security groups
Block deivce mapping
User data
```



![image-20200313170041422](ec2.assets/image-20200313170041422.png)



## Load Balancer Types

![image-20200313170057040](ec2.assets/image-20200313170057040.png)













