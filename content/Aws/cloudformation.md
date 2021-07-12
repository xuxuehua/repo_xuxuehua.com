---
title: "cloudformation"
date: 2021-07-11 13:48
---



[toc]



# Cloudformation



## package template

```
aws cloudformation package --template /path_to_template/template.json --s3-bucket mybucket --output json > packaged-template.json
```



## create stack

```
$ aws --profile rxu --region us-east-1 cloudformation create-stack --stack-name rxuTestNatNetwork --template-body file:///Users/rxu/Downloads/rxuTestNatNetwork.yaml
{
    "StackId": "arn:aws:cloudformation:us-east-1:xxx:stack/rxuTestNatNetwork/xxx"
}
```







# example



## nat network

![image-20210711145412823](/Users/rxu/coding/github/repo_xuxuehua.com/content/Aws/cloudformation.assets/image-20210711145412823.png)

```
Description: >-
  This template deploys a VPC, with a pair of public and private subnets spread
  across two Availability Zones. It deploys an internet gateway, with a default
  route on the public subnets. It deploys a pair of NAT gateways (one in each
  AZ), and default routes for them in the private subnets.
Parameters:
  EnvironmentName:
    Description: An environment name that is prefixed to resource names
    Type: String
    Default: rxuTestNatNetwork-
  VpcCIDR:
    Description: Please enter the IP range (CIDR notation) for this VPC
    Type: String
    Default: 10.10.0.0/16
  PublicSubnet1CIDR:
    Description: >-
      Please enter the IP range (CIDR notation) for the public subnet in the
      first Availability Zone
    Type: String
    Default: 10.10.1.0/24
  PrivateSubnet1CIDR:
    Description: >-
      Please enter the IP range (CIDR notation) for the private subnet in the
      first Availability Zone
    Type: String
    Default: 10.10.2.0/24
Resources:
  VPC:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: !Ref VpcCIDR
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Ref EnvironmentName
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 0f554975-6035-47d0-a907-ea00a8d13b39
  InternetGateway:
    Type: 'AWS::EC2::InternetGateway'
    Properties:
      Tags:
        - Key: Name
          Value: !Ref EnvironmentName
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 971f560f-822a-471e-b4b3-f3079f374afb
  InternetGatewayAttachment:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      InternetGatewayId: !Ref InternetGateway
      VpcId: !Ref VPC
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 08c04181-16f5-4713-b31f-0f9fa2b8c29e
  PublicSubnet1:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select 
        - 0
        - !GetAZs ''
      CidrBlock: !Ref PublicSubnet1CIDR
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub '${EnvironmentName} Public Subnet (AZ1)'
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 0de2bd8a-4bd3-4341-8679-1c6801fa7909
  PrivateSubnet1:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select 
        - 0
        - !GetAZs ''
      CidrBlock: !Ref PrivateSubnet1CIDR
      MapPublicIpOnLaunch: false
      Tags:
        - Key: Name
          Value: !Sub '${EnvironmentName} Private Subnet (AZ1)'
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 70e6822e-344a-4eff-9ef8-b20d5be177c6
  NatGateway1EIP:
    Type: 'AWS::EC2::EIP'
    DependsOn: InternetGatewayAttachment
    Properties:
      Domain: vpc
    Metadata:
      'AWS::CloudFormation::Designer':
        id: fc3e58b1-991f-48ba-a53b-7df50ebed80f
  NatGateway1:
    Type: 'AWS::EC2::NatGateway'
    Properties:
      AllocationId: !GetAtt NatGateway1EIP.AllocationId
      SubnetId: !Ref PublicSubnet1
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 8ac246ef-bf9e-43a2-a660-506d557e4dda
  PublicRouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub '${EnvironmentName} Public Routes'
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 02ee79f2-8139-4d27-84eb-5ef4fce999e9
  DefaultPublicRoute:
    Type: 'AWS::EC2::Route'
    DependsOn:
      - InternetGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
    Metadata:
      'AWS::CloudFormation::Designer':
        id: ae93fcc5-9c34-4b67-a806-50c1145465a8
  PublicSubnet1RouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet1
    Metadata:
      'AWS::CloudFormation::Designer':
        id: c9ed66cb-7818-42fe-a94e-1252ee1aa5b5
  PrivateRouteTable1:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub '${EnvironmentName} Private Routes (AZ1)'
    Metadata:
      'AWS::CloudFormation::Designer':
        id: dedcdd84-09a2-4e37-86cf-13f0aa34bdeb
  DefaultPrivateRoute1:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref PrivateRouteTable1
      DestinationCidrBlock: 0.0.0.0/0
      NatGatewayId: !Ref NatGateway1
    Metadata:
      'AWS::CloudFormation::Designer':
        id: acc2f820-eb83-4157-9f77-d4b02f799306
  PrivateSubnet1RouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      RouteTableId: !Ref PrivateRouteTable1
      SubnetId: !Ref PrivateSubnet1
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 6968a49e-e40f-4361-8e84-236f8e864b0a
  NoIngressSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupName: no-ingress-sg
      GroupDescription: Security group with no ingress rule
      VpcId: !Ref VPC
    Metadata:
      'AWS::CloudFormation::Designer':
        id: 6c2b0a6b-b1b1-44ac-bef8-af1c954af09b
Outputs:
  VPC:
    Description: A reference to the created VPC
    Value: !Ref VPC
  PublicSubnets:
    Description: A list of the public subnets
    Value: !Join 
      - ','
      - - !Ref PublicSubnet1
  PrivateSubnets:
    Description: A list of the private subnets
    Value: !Join 
      - ','
      - - !Ref PrivateSubnet1
  PublicSubnet1:
    Description: A reference to the public subnet in the 1st Availability Zone
    Value: !Ref PublicSubnet1
  PrivateSubnet1:
    Description: A reference to the private subnet in the 1st Availability Zone
    Value: !Ref PrivateSubnet1
  NoIngressSecurityGroup:
    Description: Security group with no ingress rule
    Value: !Ref NoIngressSecurityGroup
Metadata:
  'AWS::CloudFormation::Designer':
    971f560f-822a-471e-b4b3-f3079f374afb:
      size:
        width: 60
        height: 60
      position:
        x: 170
        'y': 350
      z: 1
      embeds: []
    0f554975-6035-47d0-a907-ea00a8d13b39:
      size:
        width: 450
        height: 350
      position:
        x: 350
        'y': 90
      z: 1
      embeds:
        - 6c2b0a6b-b1b1-44ac-bef8-af1c954af09b
        - 02ee79f2-8139-4d27-84eb-5ef4fce999e9
        - 70e6822e-344a-4eff-9ef8-b20d5be177c6
        - 0de2bd8a-4bd3-4341-8679-1c6801fa7909
        - 8ac246ef-bf9e-43a2-a660-506d557e4dda
        - dedcdd84-09a2-4e37-86cf-13f0aa34bdeb
    6c2b0a6b-b1b1-44ac-bef8-af1c954af09b:
      size:
        width: 60
        height: 60
      position:
        x: 530
        'y': 230
      z: 2
      parent: 0f554975-6035-47d0-a907-ea00a8d13b39
      embeds: []
      iscontainedinside:
        - 0f554975-6035-47d0-a907-ea00a8d13b39
        - 0f554975-6035-47d0-a907-ea00a8d13b39
    dedcdd84-09a2-4e37-86cf-13f0aa34bdeb:
      size:
        width: 100
        height: 110
      position:
        x: 520
        'y': 100
      z: 2
      parent: 0f554975-6035-47d0-a907-ea00a8d13b39
      embeds:
        - acc2f820-eb83-4157-9f77-d4b02f799306
      iscontainedinside:
        - 0f554975-6035-47d0-a907-ea00a8d13b39
        - 0f554975-6035-47d0-a907-ea00a8d13b39
    02ee79f2-8139-4d27-84eb-5ef4fce999e9:
      size:
        width: 100
        height: 100
      position:
        x: 450
        'y': 330
      z: 2
      parent: 0f554975-6035-47d0-a907-ea00a8d13b39
      embeds:
        - ae93fcc5-9c34-4b67-a806-50c1145465a8
      iscontainedinside:
        - 0f554975-6035-47d0-a907-ea00a8d13b39
        - 0f554975-6035-47d0-a907-ea00a8d13b39
    70e6822e-344a-4eff-9ef8-b20d5be177c6:
      size:
        width: 80
        height: 80
      position:
        x: 660
        'y': 120
      z: 2
      parent: 0f554975-6035-47d0-a907-ea00a8d13b39
      embeds: []
      iscontainedinside:
        - 0f554975-6035-47d0-a907-ea00a8d13b39
        - 0f554975-6035-47d0-a907-ea00a8d13b39
    6968a49e-e40f-4361-8e84-236f8e864b0a:
      source:
        id: dedcdd84-09a2-4e37-86cf-13f0aa34bdeb
      target:
        id: 70e6822e-344a-4eff-9ef8-b20d5be177c6
      z: 2
    0de2bd8a-4bd3-4341-8679-1c6801fa7909:
      size:
        width: 80
        height: 80
      position:
        x: 660
        'y': 340
      z: 2
      parent: 0f554975-6035-47d0-a907-ea00a8d13b39
      embeds: []
      iscontainedinside:
        - 0f554975-6035-47d0-a907-ea00a8d13b39
        - 0f554975-6035-47d0-a907-ea00a8d13b39
    c9ed66cb-7818-42fe-a94e-1252ee1aa5b5:
      source:
        id: 02ee79f2-8139-4d27-84eb-5ef4fce999e9
      target:
        id: 0de2bd8a-4bd3-4341-8679-1c6801fa7909
      z: 2
    08c04181-16f5-4713-b31f-0f9fa2b8c29e:
      source:
        id: 0f554975-6035-47d0-a907-ea00a8d13b39
      target:
        id: 971f560f-822a-471e-b4b3-f3079f374afb
      z: 1
    ae93fcc5-9c34-4b67-a806-50c1145465a8:
      size:
        width: 60
        height: 60
      position:
        x: 470
        'y': 350
      z: 3
      parent: 02ee79f2-8139-4d27-84eb-5ef4fce999e9
      embeds: []
      isassociatedwith:
        - 971f560f-822a-471e-b4b3-f3079f374afb
      iscontainedinside:
        - 02ee79f2-8139-4d27-84eb-5ef4fce999e9
      dependson:
        - 08c04181-16f5-4713-b31f-0f9fa2b8c29e
    fc3e58b1-991f-48ba-a53b-7df50ebed80f:
      size:
        width: 60
        height: 60
      position:
        x: 260
        'y': 130
      z: 1
      embeds: []
      dependson:
        - 08c04181-16f5-4713-b31f-0f9fa2b8c29e
    8ac246ef-bf9e-43a2-a660-506d557e4dda:
      size:
        width: 60
        height: 60
      position:
        x: 370
        'y': 130
      z: 2
      parent: 0f554975-6035-47d0-a907-ea00a8d13b39
      embeds: []
      iscontainedinside:
        - 0de2bd8a-4bd3-4341-8679-1c6801fa7909
    acc2f820-eb83-4157-9f77-d4b02f799306:
      size:
        width: 60
        height: 60
      position:
        x: 540
        'y': 130
      z: 3
      parent: dedcdd84-09a2-4e37-86cf-13f0aa34bdeb
      embeds: []
      isassociatedwith:
        - 8ac246ef-bf9e-43a2-a660-506d557e4dda
      iscontainedinside:
        - dedcdd84-09a2-4e37-86cf-13f0aa34bdeb

```

