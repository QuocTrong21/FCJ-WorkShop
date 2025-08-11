---
title: "Guide to Creating an EC2 Instance on AWS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2.1.2 </b> "
---

## 📌 Objective  
Create an EC2 instance using default configuration to deploy applications or test the environment.

## 🧰 Prerequisites  
- Valid AWS account  
- Logged into AWS Console or have AWS CLI installed  
- Created a key pair for SSH (if needed)  

## 🛠️ Steps to Follow  

### 1. Log in to AWS Console  
Visit: [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)

### 2. Create EC2 Instance  

#### Method 1: Using AWS Console  
1. Go to **EC2 Dashboard**  
2. Click **Launch Instance**  
3. Enter a name: `workshop`  
4. **Select AMI**: `Amazon Linux 2023` (or Amazon Linux 2)  
5. **Instance Type**: `t2.micro` (Free tier eligible)  
6. **Key pair**: Choose or create a new one  
7. **Network Settings**:  
   - Allow SSH (port 22)  
   - Allow HTTP (port 80) if needed  
8. **Storage**: Default 8 GB (gp2)  
9. Click **Launch Instance**  
![VPC](/images/2.prerequisite/12-1.jpg)  
![VPC](/images/2.prerequisite/2.3.png)  
![VPC](/images/2.prerequisite/2.4.png)  
![VPC](/images/2.prerequisite/2.5.png)  

## 🧰 3. Install AWS CLI on Linux  

Run the following commands to install AWS CLI v2:

```bash
# Download AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip the installer
unzip awscliv2.zip

# Install
sudo ./aws/install

# Check version
aws --version
