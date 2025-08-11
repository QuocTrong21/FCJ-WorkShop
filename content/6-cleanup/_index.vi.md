
---
title: "Dọn Dẹp tài Nguyên"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: " <b> 6. </b> "
---


Chúng ta sẽ tiến hành **dọn dẹp toàn bộ tài nguyên AWS** đã tạo trong quá trình thực hành. Hãy thực hiện theo các bước sau để tránh phát sinh chi phí.

---

### ✅ Xóa IAM User và Access Keys

- Vào AWS Management Console > IAM > Users.  
- Chọn user đã tạo (ví dụ: `network-automation-user`).  
- Xóa các Access Key, Secret Key liên quan.  
- Xóa user nếu không còn dùng.

![Clean](/images/6.clean/6.1.png)
![Clean](/images/6.clean/6.2.png)
---

### ✅ Xóa EC2 Instances và Key Pairs

- Vào EC2 Dashboard > Instances.  
- Dừng và terminate các instance đã tạo.  
- Xóa các Key Pair không dùng nữa trong phần **Key Pairs**.


![Clean](/images/6.clean/6.3.png)
![Clean](/images/6.clean/6.4.png)
---

### ✅ Xóa Mạng do AWS CLI tạo

- Xóa Security Groups đã tạo qua CLI.  
- Xóa Subnet, Route Table, Internet Gateway và cuối cùng là VPC.  
- Lưu ý thứ tự xóa:  
  - Xóa các resource phụ thuộc trước (Subnet, Route Tables associations).  
  - Ngắt kết nối Internet Gateway trước khi xóa.  
![Clean](/images/6.clean/6.5.png)
![Clean](/images/6.clean/6.6.png)
![Clean](/images/6.clean/6.7.png)
![Clean](/images/6.clean/6.8.png)
![Clean](/images/6.clean/6.9.png)

---

### ✅ Xóa CloudFormation Stack 

1. Truy cập [CloudFormation Console](https://console.aws.amazon.com/cloudformation/home)
2. Chọn các stack liên quan đến bài lab.
3. Click **Delete** và xác nhận xóa stack.

![Clean](/images/6.clean/6.10.png)

---

### ✅ Xóa các tài nguyên AWS SDK tạo
- Nếu có tạo Security Group, EC2, CloudWatch Alarm qua SDK, xóa bằng SDK hoặc qua AWS Console.

- Xóa CloudWatch Alarm:
```bash
aws cloudwatch delete-alarm --alarm-name <ALARM_NAME>
```
- Xóa SNS Topic (nếu dùng):
```bash
aws sns delete-topic --topic-arn <TopicARN>
```

![Clean](/images/6.clean/6.11.png)
![Clean](/images/6.clean/6.12.png)

---

> ✅ **Lưu ý:** Sau khi hoàn tất dọn dẹp, bạn có thể vào **Billing Dashboard** để kiểm tra chi phí và đảm bảo không có tài nguyên nào còn tồn tại.

---
