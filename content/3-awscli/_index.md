---
title: "Automation with AWS CLI"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

## AWS CLI Script for Creating Network and EC2

---

### 1. Create VPC

```bash
VPC_ID=$(aws ec2 create-vpc \
  --cidr-block 10.0.0.0/16 \
  --query 'Vpc.VpcId' \
  --output text)

echo "VPC ID: $VPC_ID"

aws ec2 create-tags --resources $VPC_ID --tags Key=Name,Value=MovieVPC
```
![CLI](/images/3.cli/3.1.png)
![CLI](/images/3.cli/3.2.png)
![CLI](/images/3.cli/3.3.png)
---

### 2. Create Subnet

Create a subnet within the newly created VPC, specifying the Availability Zone and CIDR for this subnet.
``` bash
SUBNET_ID=$(aws ec2 create-subnet \
  --vpc-id $VPC_ID \
  --cidr-block 10.0.1.0/24 \
  --availability-zone ap-southeast-1a \
  --query 'Subnet.SubnetId' \
  --output text)
echo "Subnet ID: $SUBNET_ID"
```
![CLI](/images/3.cli/3.20.png)
---

### 3. Create Internet Gateway and Attach to VPC

Create an Internet Gateway to allow internet connectivity for the VPC and attach it to the created VPC.
``` bash
IGW_ID=$(aws ec2 create-internet-gateway \
  --query 'InternetGateway.InternetGatewayId' \
  --output text)

aws ec2 attach-internet-gateway \
  --internet-gateway-id $IGW_ID \
  --vpc-id $VPC_ID

# Tag IGW
aws ec2 create-tags --resources $IGW_ID --tags Key=Name,Value=MovieIGW
```
![CLI](/images/3.cli/3.4.png)
![CLI](/images/3.cli/3.5.png)
---

### 4. Create Route Table and Configure Internet Route

Create a Route Table for the VPC, add a route to direct internet traffic to the Internet Gateway, associate this Route Table with the subnet, and enable auto-assign public IP for the subnet.

``` bash
RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --query 'RouteTable.RouteTableId' \
  --output text)

# Assign route 0.0.0.0/0 to IGW
aws ec2 create-route \
  --route-table-id $RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id $IGW_ID

# Associate with subnet
aws ec2 associate-route-table \
  --route-table-id $RT_ID \
  --subnet-id $SUBNET_ID

# Enable auto-assign public IP
aws ec2 modify-subnet-attribute --subnet-id $SUBNET_ID --map-public-ip-on-launch
```
![CLI](/images/3.cli/3.6.png)
![CLI](/images/3.cli/3.7.png)
![CLI](/images/3.cli/3.8.png)
---

### 5. Create Security Group

Create a new Security Group assigned to the VPC with rules allowing necessary ports such as SSH, HTTP, HTTPS, and MySQL.
```bash
SG_ID=$(aws ec2 create-security-group \
  --group-name MovieSG \
  --description "Allow HTTP, HTTPS, SSH, MySQL" \
  --vpc-id $VPC_ID \
  --query 'GroupId' --output text)

# Open necessary ports
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 22 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 443 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 3306 --cidr 10.0.0.0/16

echo "Security Group ID: $SG_ID"

```
![CLI](/images/3.cli/3.9.png)
![CLI](/images/3.cli/3.10.png)
![CLI](/images/3.cli/3.11.png)
![CLI](/images/3.cli/3.12.png)
---

### 6. Create Key Pair

Create a Key Pair to use when SSH connecting to the EC2 Instance, and save the private key for security.
```bash
aws ec2 create-key-pair --key-name MovieKey \
  --query 'KeyMaterial' --output text > MovieKey.pem

chmod 400 MovieKey.pem

```
![CLI](/images/3.cli/3.13.png)
![CLI](/images/3.cli/3.14.png)
---

### 7. Create EC2 Instance

Launch an EC2 Instance in the created subnet, assign the Security Group and Key Pair for management, and allocate a public IP address.
```bash
INSTANCE_ID=$(aws ec2 run-instances \
  --image-id ami-0df7a207adb9748c7 \  # Amazon Linux 2 (or Ubuntu depending on region)
  --instance-type t2.micro \
  --key-name MovieKey \
  --security-group-ids $SG_ID \
  --subnet-id $SUBNET_ID \
  --associate-public-ip-address \
  --query 'Instances[0].InstanceId' \
  --output text)

echo "EC2 Instance ID: $INSTANCE_ID"
```
![CLI](/images/3.cli/3.15.png)
![CLI](/images/3.cli/3.16.png)

---
### 8. Get Public IP of EC2 Instance

After the EC2 is running, get the public IP address to access or SSH into the server.
```bash
aws ec2 describe-instances --instance-ids $INSTANCE_ID \
  --query 'Reservations[0].Instances[0].PublicIpAddress' --output text

```
![CLI](/images/3.cli/3.21.png)
---

You can combine all the above steps into a single AWS CLI script to automate the entire process.