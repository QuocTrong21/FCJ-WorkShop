---
title: "Steps to Prepare the Environment for Network Automation on AWS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

{{% notice info %}}
To implement network automation on AWS, you need to prepare an environment that includes AWS CLI for interacting with AWS via command line, AWS SDK (Java) for programmatically managing network resources, and AWS CloudFormation for modeling and deploying network infrastructure as Infrastructure as Code (IaC).
{{% /notice %}}

> 💡 If you are not familiar with the tools and services in this guide, please refer to:

- [AWS Command Line Interface (CLI) – Overview and Installation](https://000011.awsstudygroup.com/en/)
- [AWS SDK for Java – Official Documentation](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/welcome.html)
- [AWS CloudFormation – Basic Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

---

## 🚀 Implementation Contents

In this section, you will step-by-step build the environment to automate networking on AWS:

1. [Create IAM User and obtain Access Key, Secret Access Key, Public Key](2.1.1-createIAM/)  
   → Create an IAM User and configure authentication information (access key, secret key).

2. [Create EC2 Instance and install AWS CLI](2.1.2-createEC2/)  
   → Create an EC2 instance, select an appropriate instance type, configure (VPC, Subnet, Security Group), choose or create a key pair for SSH access, and launch the instance.

---

👉 After completing these steps, you will have a fully prepared environment to develop and deploy network automation solutions on the AWS platform efficiently and professionally.
