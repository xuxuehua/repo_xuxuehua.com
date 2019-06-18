---
title: "packer"
date: 2019-06-14 14:58
---
[TOC]

# Packer

Creating identical mache images for multiple plaforms from a single source configuration

When building images, Packer is able to use tools like Chef or Puppet to install software onto the image.

The same example include AMIs for EC2, VMDK/VMX files for VMware, OVF exports for VirtualBox, etc.



## Installation

### Linux

```
wget https://releases.hashicorp.com/packer/1.4.1/packer_1.4.1_linux_amd64.zip
```

Unix systems, `~/packer` or `/usr/local/packer` is generally good, depending on whether you want to restrict the install to just your user or install it system-wide.



### Mac OSX

```
brew install packer
```





## Build an Image



Create a file `example.json` and fill it with the following contents:

```
{
  "variables": {
    "aws_access_key": "",
    "aws_secret_key": ""
  },
  "builders": [{
    "type": "amazon-ebs",
    "access_key": "{{user `aws_access_key`}}",
    "secret_key": "{{user `aws_secret_key`}}",
    "region": "us-east-1",
    "source_ami_filter": {
      "filters": {
        "virtualization-type": "hvm",
        "name": "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*",
        "root-device-type": "ebs"
      },
      "owners": ["099720109477"],
      "most_recent": true
    },
    "instance_type": "t2.micro",
    "ssh_username": "ubuntu",
    "ami_name": "packer-example {{timestamp}}"
  }]
}
```



validate example.json

```
# packer validate example.json
	Template validated successfully.
```

### 

### First Images 

create credential info at https://console.aws.amazon.com/iam/home?#/security_credentials



```
 packer build -var 'aws_access_key=YOUR INFO' -var 'aws_secret_key=YOUR INFO' example.json
amazon-ebs output will be in this color.

==> amazon-ebs: Prevalidating AMI Name: packer-example 1560758189
    amazon-ebs: Found Image ID: ami-01d9d5f6cecc31f85
==> amazon-ebs: Creating temporary keypair: packer_5d0747ad-dfb6-bfdb-ef20-2505c207f4ac
==> amazon-ebs: Creating temporary security group for this instance: packer_5d0747b2-aec7-85c4-3e6f-b749f50f1645
==> amazon-ebs: Authorizing access to port 22 from [0.0.0.0/0] in the temporary security groups...
==> amazon-ebs: Launching a source AWS instance...
==> amazon-ebs: Adding tags to source instance
    amazon-ebs: Adding tag: "Name": "Packer Builder"
    amazon-ebs: Instance ID: i-00379261a2d436db3
==> amazon-ebs: Waiting for instance (i-00379261a2d436db3) to become ready...
==> amazon-ebs: Using ssh communicator to connect: 54.144.224.37
==> amazon-ebs: Waiting for SSH to become available...
==> amazon-ebs: Connected to SSH!
==> amazon-ebs: Stopping the source instance...
    amazon-ebs: Stopping instance
==> amazon-ebs: Waiting for the instance to stop...
==> amazon-ebs: Creating AMI packer-example 1560758189 from instance i-00379261a2d436db3
    amazon-ebs: AMI: ami-07638ed17c7898ac5
==> amazon-ebs: Waiting for AMI to become ready...
==> amazon-ebs: Terminating the source AWS instance...
==> amazon-ebs: Cleaning up any extra volumes...
==> amazon-ebs: No volumes to clean up, skipping
==> amazon-ebs: Deleting temporary security group...
==> amazon-ebs: Deleting temporary keypair...
Build 'amazon-ebs' finished.

==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:
us-east-1: ami-07638ed17c7898ac5
```



### Managing Images

AMIs are stored in S3 by Amazon, so unless you want to be charged about $0.01 per month, you'll probably want to remove it. Remove the AMI by first deregistering it on the [AWS AMI management page](https://console.aws.amazon.com/ec2/home?region=us-east-1#s=Images). Next, delete the associated snapshot on the [AWS snapshot management page](https://console.aws.amazon.com/ec2/home?region=us-east-1#s=Snapshots).





### Example

Create a file named `welcome.txt` and add the following:

```
WELCOME TO PACKER!
```

Create a file named `example.sh` and add the following:

```
#!/bin/bash
echo "hello"
```

Set your access key and id as environment variables, so we don't need to pass them in through the command line:

```
export AWS_ACCESS_KEY_ID=MYACCESSKEYID
export AWS_SECRET_ACCESS_KEY=MYSECRETACCESSKEY
```

Now save the following text in a file named `firstrun.json`:

```
{
    "variables": {
        "aws_access_key": "{{env `AWS_ACCESS_KEY_ID`}}",
        "aws_secret_key": "{{env `AWS_SECRET_ACCESS_KEY`}}",
        "region":         "us-east-1"
    },
    "builders": [
        {
            "access_key": "{{user `aws_access_key`}}",
            "ami_name": "packer-linux-aws-demo-{{timestamp}}",
            "instance_type": "t2.micro",
            "region": "us-east-1",
            "secret_key": "{{user `aws_secret_key`}}",
            "source_ami_filter": {
              "filters": {
              "virtualization-type": "hvm",
              "name": "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*",
              "root-device-type": "ebs"
              },
              "owners": ["099720109477"],
              "most_recent": true
            },
            "ssh_username": "ubuntu",
            "type": "amazon-ebs"
        }
    ],
    "provisioners": [
        {
            "type": "file",
            "source": "./welcome.txt",
            "destination": "/home/ubuntu/"
        },
        {
            "type": "shell",
            "inline":[
                "ls -al /home/ubuntu",
                "cat /home/ubuntu/welcome.txt"
            ]
        },
        {
            "type": "shell",
            "script": "./example.sh"
        }
    ]
}
```

> The `"provisioners"` section of the template demonstrates use of provisioners to customize and control the build process:

and to build, run `packer build firstrun.json`

Note that if you wanted to use a `source_ami` instead of a `source_ami_filter` it might look something like this: `"source_ami": "ami-fce3c696"`.







Your output will look like this:

```
amazon-ebs output will be in this color.

==> amazon-ebs: Prevalidating AMI Name: packer-linux-aws-demo-1507231105
    amazon-ebs: Found Image ID: ami-fce3c696
==> amazon-ebs: Creating temporary keypair: packer_59d68581-e3e6-eb35-4ae3-c98d55cfa04f
==> amazon-ebs: Creating temporary security group for this instance: packer_59d68584-cf8a-d0af-ad82-e058593945ea
==> amazon-ebs: Authorizing access to port 22 on the temporary security group...
==> amazon-ebs: Launching a source AWS instance...
==> amazon-ebs: Adding tags to source instance
    amazon-ebs: Adding tag: "Name": "Packer Builder"
    amazon-ebs: Instance ID: i-013e8fb2ced4d714c
==> amazon-ebs: Waiting for instance (i-013e8fb2ced4d714c) to become ready...
==> amazon-ebs: Waiting for SSH to become available...
==> amazon-ebs: Connected to SSH!
==> amazon-ebs: Uploading ./scripts/welcome.txt => /home/ubuntu/
==> amazon-ebs: Provisioning with shell script: /var/folders/8t/0yb5q0_x6mb2jldqq_vjn3lr0000gn/T/packer-shell661094204
    amazon-ebs: total 32
    amazon-ebs: drwxr-xr-x 4 ubuntu ubuntu 4096 Oct  5 19:19 .
    amazon-ebs: drwxr-xr-x 3 root   root   4096 Oct  5 19:19 ..
    amazon-ebs: -rw-r--r-- 1 ubuntu ubuntu  220 Apr  9  2014 .bash_logout
    amazon-ebs: -rw-r--r-- 1 ubuntu ubuntu 3637 Apr  9  2014 .bashrc
    amazon-ebs: drwx------ 2 ubuntu ubuntu 4096 Oct  5 19:19 .cache
    amazon-ebs: -rw-r--r-- 1 ubuntu ubuntu  675 Apr  9  2014 .profile
    amazon-ebs: drwx------ 2 ubuntu ubuntu 4096 Oct  5 19:19 .ssh
    amazon-ebs: -rw-r--r-- 1 ubuntu ubuntu   18 Oct  5 19:19 welcome.txt
    amazon-ebs: WELCOME TO PACKER!
==> amazon-ebs: Provisioning with shell script: ./example.sh
    amazon-ebs: hello
==> amazon-ebs: Stopping the source instance...
    amazon-ebs: Stopping instance, attempt 1
==> amazon-ebs: Waiting for the instance to stop...
==> amazon-ebs: Creating the AMI: packer-linux-aws-demo-1507231105
    amazon-ebs: AMI: ami-f76ea98d
==> amazon-ebs: Waiting for AMI to become ready...
```

