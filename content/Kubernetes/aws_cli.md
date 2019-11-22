---
title: "aws_cli"
date: 2019-11-10 11:46
---
[toc]



# AWS CLI



## Installation 

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip" && \ unzip awscli-bundle.zip && \
cd awscli-bundle && \
sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```





## Named Profiles

**`~/.aws/credentials`** (Linux & Mac) 

```
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

[user1]
aws_access_key_id=AKIAI44QH8DHBEXAMPLE
aws_secret_access_key=je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
```

Each profile can specify different credentials—perhaps from different IAM users—and can also specify different AWS Regions and output formats.

**`~/.aws/config`** (Linux & Mac) 

```
[default]
region=us-west-2
output=json

[profile user1]
region=us-east-1
output=text
```

Important

The `credentials` file uses a different naming format than the CLI `config` file for named profiles. Include the prefix word "`profile`" only when configuring a named profile in the `config` file. Do ***not\*** use the word `profile` when creating an entry in the `credentials` file.





## Using Profiles with the AWS CLI

To use a named profile, add the `--profile *`profile-name`*` option to your command. The following example lists all of your Amazon EC2 instances using the credentials and settings defined in the `user1` profile from the previous example files.

```
$ aws ec2 describe-instances --profile user1
```

To use a named profile for multiple commands, you can avoid specifying the profile in every command by setting the `AWS_PROFILE` environment variable at the command line.

**Linux, macOS, or Unix**

```
$ export AWS_PROFILE=user1
```