---
title: "basic_knowledge"
date: 2019-06-24 09:43
---
[TOC]

# AWS



## Instance Metadata

Data about your instance

Can be used to configure or manage a running instance



### Retrieving 

```
curl http://169.254.169.254/latest/meta-data
```

returned as text (Content-type: text/plain)







## Instance User data

Can be passed to the instance at launch

Can be used to perform common automated configuration tasks

Runs scripts after the instance starts





## AWS-CLI

### Mac OSX

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"

unzip awscli-bundle.zip


```









# 