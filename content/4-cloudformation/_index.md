---
title: "Deploy AWS Network with CloudFormation (IaC)"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4. </b> "
---



### 1. Create `network.yaml` file

- Prepare the CloudFormation configuration file describing the necessary network resources.  
- Ensure network components are clearly and completely declared.
```bash
AWSTemplateFormatVersion: '2010-09-09'
Description: Network stack for Movie Web in Sydney

Resources:
  MovieVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: MovieVPC

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MovieVPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: ap-southeast-2a
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: PublicSubnet

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: MovieIGW

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MovieVPC
      InternetGatewayId: !Ref InternetGateway

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MovieVPC
      Tags:
        - Key: Name
          Value: PublicRouteTable

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  SubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable

  MovieSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH and HTTP
      VpcId: !Ref MovieVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: MovieSG

Outputs:
  VPCId:
    Description: VPC ID
    Value: !Ref MovieVPC
  SubnetId:
    Description: Public Subnet ID
    Value: !Ref PublicSubnet
  SecurityGroupId:
    Description: Security Group ID
    Value: !Ref MovieSecurityGroup
```
![CLF](/images/4.clf/4.1.png)
---

### 2. Deploy the stack

- Use the `aws cloudformation deploy` command to create the stack from the `network.yaml` file.  
- Give the stack a clear name, for example: `MyNetworkStack`.  
- Provide appropriate permissions when deploying if there are IAM-related resources.
```bash
aws cloudformation deploy \
  --template-file network.yaml \
  --stack-name movie-web-network \
  --region ap-southeast-2 \
  --capabilities CAPABILITY_NAMED_IAM
```
![CLF](/images/4.clf/4.2.png)
![CLF](/images/4.clf/4.3.png)
---

### 3. Check stack status

- Monitor the stack status to know if the deployment was successful or encountered errors.  
- Use AWS CLI commands or the Console to view detailed stack events.
```bash
aws cloudformation describe-stacks --stack-name movie-web-network --query 'Stacks[0].StackStatus' --output text

aws cloudformation describe-stack-events --stack-name movie-web-network --max-items 50

```
![CLF](/images/4.clf/4.4.png)
![CLF](/images/4.clf/4.5.png)
![CLF](/images/4.clf/4.6.png)
---

### 4. Intentionally create an error

- Change a parameter or resource to an invalid value (e.g., a non-existent AMI) in the `network.yaml` file.  
- Re-deploy the stack and observe CloudFormation's automatic rollback process.  
- Use event information to debug and understand how CloudFormation handles errors.
``` bash
aws cloudformation deploy \
  --template-file network.yaml \
  --stack-name movie-web-network-testfail \
  --parameter-overrides KeyPairName=MovieKey ImageId=ami-00000000 InstanceType=t3.small \
  --capabilities CAPABILITY_IAM

```
![CLF](/images/4.clf/4.7.png)
---