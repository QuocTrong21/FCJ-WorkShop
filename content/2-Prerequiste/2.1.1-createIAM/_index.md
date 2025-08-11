---
title: "Create IAM User and Obtain Access Key, Secret Access Key, Public Key"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1.1 </b> "
---

## 1. Create IAM User and Obtain Access Key, Secret Access Key, Public Key

To work with AWS CLI, AWS SDK, or CloudFormation, the first step is to create an IAM User with appropriate access permissions and obtain the Access Key and Secret Access Key for configuration.

---

### Steps to Perform

1. Log in to the AWS Management Console with Admin privileges.  
2. Navigate to **IAM** > **Users** > **Add user**.  
3. Enter the user name (e.g., `network-automation-user`).  
4. Select **Programmatic access** to generate Access Key and Secret Access Key.  
5. Assign appropriate permissions, for example:  
   - `AmazonEC2FullAccess`  
   - `AmazonVPCFullAccess`  
   - `IAMReadOnlyAccess`  
   - `AdministratorAccess` (for testing purposes)  
6. Click **Create user** and download the CSV file containing the Access Key and Secret Access Key.  
7. Create or import an **EC2 Key Pair** for SSH access (if needed).  
![VPC](/images/2.prerequisite/2.1.png)  
![VPC](/images/2.prerequisite/2.2.png)  

### Important Notes

- **Protect your Access Key and Secret Key:** Do not share or expose them publicly.  
- If the user has broad permissions (AdministratorAccess), use only in testing environments.  
- Use IAM Roles for EC2 instances whenever possible to avoid using Access Keys directly.

---

### Additional References

- [Guide to Creating IAM User on AWS](https://000002.awsstudygroup.com/en/)  
- [Managing Access Keys for IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)  
- [Key Pairs for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)  
