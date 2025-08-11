---
title: "Triển khai Mạng AWS bằng CloudFormation (IaC)"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4. </b> "
---



### 1. Viết file `network.yaml`

- Chuẩn bị file cấu hình CloudFormation mô tả các tài nguyên mạng cần thiết.  
- Đảm bảo các thành phần mạng được khai báo rõ ràng và đầy đủ.
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

### 2. Triển khai stack

- Dùng lệnh `aws cloudformation deploy` để tạo stack từ file `network.yaml`.  
- Đặt tên stack rõ ràng, ví dụ: `MyNetworkStack`.  
- Cung cấp quyền phù hợp khi deploy nếu có tài nguyên liên quan đến IAM.
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

### 3. Kiểm tra trạng thái stack

- Theo dõi trạng thái stack để biết quá trình triển khai thành công hay gặp lỗi.  
- Sử dụng các lệnh AWS CLI hoặc trên Console để xem chi tiết các sự kiện của stack.
```bash
aws cloudformation describe-stacks --stack-name movie-web-network --query 'Stacks[0].StackStatus' --output text

aws cloudformation describe-stack-events --stack-name movie-web-network --max-items 50

```
![CLF](/images/4.clf/4.4.png)
![CLF](/images/4.clf/4.5.png)
![CLF](/images/4.clf/4.6.png)
---

### 3.Thử tạo lỗi có chủ ý

- Thay đổi một tham số hoặc tài nguyên không hợp lệ (ví dụ AMI không tồn tại) trong file `network.yaml`.  
- Triển khai lại stack và quan sát quá trình rollback tự động của CloudFormation.  
- Dùng thông tin sự kiện để debug và hiểu cách CloudFormation xử lý lỗi.
``` bash
aws cloudformation deploy \
  --template-file network.yaml \
  --stack-name movie-web-network-testfail \
  --parameter-overrides KeyPairName=MovieKey ImageId=ami-00000000 InstanceType=t3.small \
  --capabilities CAPABILITY_IAM

```
![CLF](/images/4.clf/4.7.png)
---

