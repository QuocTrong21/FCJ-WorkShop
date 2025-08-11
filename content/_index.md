---
title: "Overview – Network Automation with AWS CLI, AWS SDK (Java), and CloudFormation"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Network Automation with AWS CLI, AWS SDK for Java, and CloudFormation

### Overview

This project focuses on **Network Automation** on AWS for Java web systems.  
The solution combines **AWS CLI**, **AWS SDK for Java**, and **AWS CloudFormation** to:

- **Provisioning**: Automatically create VPC, Subnet, Security Group, EC2 instances.  
- **Configuration**: Configure network resources via CLI and Java SDK.  
- **Monitoring**: Monitor the system using CloudWatch Alarms and send alerts via SNS.  
- **Rollback**: Restore infrastructure when deployment errors occur.  
- **Documentation**: Record processes and architecture.

This approach minimizes manual operations, ensures consistency, and speeds up infrastructure deployment for Java web projects (e.g., a movie streaming web app).

![AWS-Network-Automation](/images/sodo.png)

### Contents

1. [Introduction](1-introduce/)  
2. [Preparation Requirements](2-Prerequiste/)  
3. [Automation with AWS CLI](3-awscli/)  
4. [IaC Deployment with CloudFormation](4-cloudformation/)  
5. [Network Management via AWS SDK for Java](5-java-sdk/)  
6. [Resource Cleanup](6-cleanup/)  
