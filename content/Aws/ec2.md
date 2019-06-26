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



### Metadata Service

e.g.

```
http://169.254.169.254/latest/meta-data/iam/security-credentials/rolename
```





## Instance Technologies

![img](https://snag.gy/WReOKS.jpg)



## Current Generation Instances

![img](https://snag.gy/yG2A6Y.jpg)



## Reboot vs Stop vs Terminate 

![img](https://snag.gy/aubQM7.jpg)





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



![img](https://snag.gy/qIGpfX.jpg)



## Load Balancer Types













