---
title: "Tự động hóa bằng AWS CLI"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

## Viết Script AWS CLI Tạo Mạng và EC2

---

### 1. Tạo VPC

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

### 2. Tạo Subnet

Tạo subnet con trong VPC vừa tạo, xác định vùng Availability Zone và CIDR cho subnet này.
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

### 3. Tạo Internet Gateway và gắn vào VPC

Tạo Internet Gateway để cho phép kết nối Internet cho VPC và gắn nó vào VPC đã tạo.
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

### 4. Tạo Route Table và cấu hình route internet

Tạo Route Table cho VPC, thêm route để chuyển hướng lưu lượng Internet ra Internet Gateway, và liên kết Route Table này với subnet. Bật chế độ auto-assign public IP cho subnet.

``` bash
RT_ID=$(aws ec2 create-route-table \
  --vpc-id $VPC_ID \
  --query 'RouteTable.RouteTableId' \
  --output text)

# Gán route 0.0.0.0/0 đến IGW
aws ec2 create-route \
  --route-table-id $RT_ID \
  --destination-cidr-block 0.0.0.0/0 \
  --gateway-id $IGW_ID

# Associate với subnet
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

### 5. Tạo Security Group

Tạo Security Group mới gán cho VPC với các quy tắc cho phép các port cần thiết như SSH, HTTP, HTTPS và MySQL.
```bash
SG_ID=$(aws ec2 create-security-group \
  --group-name MovieSG \
  --description "Allow HTTP, HTTPS, SSH, MySQL" \
  --vpc-id $VPC_ID \
  --query 'GroupId' --output text)

# Mở các port cần thiết
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

### 6. Tạo Key Pair

Tạo Key Pair để dùng khi SSH kết nối đến EC2 Instance, lưu lại private key để bảo mật.
```bash
aws ec2 create-key-pair --key-name MovieKey \
  --query 'KeyMaterial' --output text > MovieKey.pem

chmod 400 MovieKey.pem

```
![CLI](/images/3.cli/3.13.png)
![CLI](/images/3.cli/3.14.png)
---

### 7. Tạo EC2 Instance

Khởi tạo EC2 Instance trong subnet đã tạo, gán Security Group và Key Pair để quản lý, đồng thời cấp phát địa chỉ IP công cộng.
```bash
INSTANCE_ID=$(aws ec2 run-instances \
  --image-id ami-0df7a207adb9748c7 \  # Amazon Linux 2 (hoặc Ubuntu tùy vùng)
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
### 8. Lấy Public IP của EC2 Instance

Sau khi EC2 chạy, lấy địa chỉ IP công cộng để có thể truy cập hoặc SSH vào máy chủ.
```bash
aws ec2 describe-instances --instance-ids $INSTANCE_ID \
  --query 'Reservations[0].Instances[0].PublicIpAddress' --output text

```
![CLI](/images/3.cli/3.21.png)
---

Bạn có thể tổng hợp các bước trên thành một script AWS CLI để tự động hóa toàn bộ quy trình.
