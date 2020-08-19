---
title: "aws_cli"
date: 2020-06-06 18:00
---
[toc]





# AWS CLI



## Linux

```
$ curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
$ unzip awscli-bundle.zip
$ sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```

Add your Access Key ID and Secret Access Key to ~/`.aws/config` using this format:

```
[default]
aws_access_key_id = <access key id>
aws_secret_access_key = <secret access key>
region = us-east-1
```

Protect the config file:

```
chmod 600 ~/.aws/config
```

Optionally, you can set an environment variable pointing to the config file. This is especially important if you want to keep it in a non-standard location. For future convenience, also add this line to your ~/.bashrc file:

```
export AWS_CONFIG_FILE=$HOME/.aws/config
```





## Mac OSX

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"

unzip awscli-bundle.zip

sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```



Example

![image-20200310181746852](aws_cli.assets/image-20200310181746852.png)





# AWS CLI Profile 

```
$ cat ~/.aws/credentials
[dp]
aws_access_key_id = xx
aws_secret_access_key = xx
[dp-stg]
aws_access_key_id = xx
aws_secret_access_key = xx
[custom_dp_da_sts]
aws_access_key_id = xx
aws_secret_access_key = xx
```



```
$ aws configure --profile custom_bd_da_sts
AWS Access Key ID [None]: xx
AWS Secret Access Key [None]: xx
Default region name [None]: us-east-1
Default output format [None]: json
```



```
$ aws sts get-caller-identity --profile custom_bd_da_sts
```







## Assume Role

```
aws sts assume-role --role-arn "arn:aws:iam::680404271483:role/ASSUME_ROLE" --role-session-name AWSCLI-Session
```

Replace with above output

```
export AWS_ACCESS_KEY_ID=RoleAccessKeyID
export AWS_SECRET_ACCESS_KEY=RoleSecretKey
export AWS_SESSION_TOKEN=RoleSessionToken

aws sts get-caller-identity
aws s3 ls s3://ASSUMED_BUCKET
```



To return to the IAM user, remove the environment variables

```
unset AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN
aws sts get-caller-identity
```



Reference to https://aws.amazon.com/premiumsupport/knowledge-center/iam-assume-role-cli/?nc1=h_ls





# SSM

```
aws ssm get-parameters --names /pkg
{
    "InvalidParameters": [],
    "Parameters": [
        {
            "Name": "/pkg",
            "LastModifiedDate": 1589791491.805,
            "Value": "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",
            "Version": 1,
            "Type": "SecureString",
            "ARN": "arn:aws:ssm:us-east-1:982645107754:parameter/pkg"
        }
    ]
}
```

