---
title: "Resource Cleanup"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

We will proceed to **clean up all AWS resources** created during the practice. Please follow the steps below to avoid incurring unnecessary costs.

---

### ✅ Delete IAM Users and Access Keys

- Go to AWS Management Console > IAM > Users.  
- Select the user you created (e.g., `network-automation-user`).  
- Delete all related Access Keys and Secret Keys.  
- Delete the user if no longer needed.

![Clean](/images/6.clean/6.1.png)  
![Clean](/images/6.clean/6.2.png)  
---

### ✅ Delete EC2 Instances and Key Pairs

- Go to EC2 Dashboard > Instances.  
- Stop and terminate the created instances.  
- Delete unused Key Pairs in the **Key Pairs** section.

![Clean](/images/6.clean/6.3.png)  
![Clean](/images/6.clean/6.4.png)  
---

### ✅ Delete Network Resources Created by AWS CLI

- Delete Security Groups created via CLI.  
- Delete Subnets, Route Tables, Internet Gateways, and finally the VPC.  
- Note the deletion order:  
  - Delete dependent resources first (Subnets, Route Table associations).  
  - Detach the Internet Gateway before deleting it.

![Clean](/images/6.clean/6.5.png)  
![Clean](/images/6.clean/6.6.png)  
![Clean](/images/6.clean/6.7.png)  
![Clean](/images/6.clean/6.8.png)  
![Clean](/images/6.clean/6.9.png)  

---

### ✅ Delete CloudFormation Stacks

1. Go to [CloudFormation Console](https://console.aws.amazon.com/cloudformation/home)  
2. Select the stacks related to the lab.  
3. Click **Delete** and confirm stack deletion.

![Clean](/images/6.clean/6.10.png)

---

### ✅ Delete Resources Created via AWS SDK

- If you created Security Groups, EC2 instances, or CloudWatch Alarms via SDK, delete them either via SDK or AWS Console.

- Delete CloudWatch Alarm:
```bash
aws cloudwatch delete-alarm --alarm-name <ALARM_NAME>
```
- Delete SNS Topic:
```bash
aws sns delete-topic --topic-arn <TopicARN>
```

![Clean](/images/6.clean/6.11.png)
![Clean](/images/6.clean/6.12.png)

✅ Note: After completing the cleanup, you can check the Billing Dashboard to verify your costs and ensure no resources remain active.