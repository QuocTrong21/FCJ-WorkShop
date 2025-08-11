---
title: "Các bước Chuẩn bị Môi trường cho Network Automation trên AWS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

{{% notice info %}}
Để thực hiện tự động hóa mạng trên AWS, bạn cần chuẩn bị một môi trường bao gồm AWS CLI để tương tác với AWS bằng dòng lệnh, AWS SDK (Java) để lập trình tự động quản lý tài nguyên mạng, và AWS CloudFormation để mô hình hóa và triển khai hạ tầng mạng dưới dạng Infrastructure as Code (IaC).
{{% /notice %}}

> 💡 Nếu bạn chưa quen với các công cụ và dịch vụ trong hướng dẫn này, hãy xem thêm:

- [AWS Command Line Interface (CLI) - Tổng quan và cài đặt](https://000011.awsstudygroup.com/vi/)
- [AWS SDK for Java - Tài liệu chính thức](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/welcome.html)
- [AWS CloudFormation - Hướng dẫn cơ bản](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

---

## 🚀 Nội dung Thực hiện

Trong phần này, bạn sẽ từng bước xây dựng môi trường để tự động hóa mạng trên AWS:

1. [Tạo IAM User và lấy Access Key, Secret Access Key, Public Key](2.1.1-createIAM/)  
   → Tạo IAM User, cấu hình thông tin xác thực (access key, secret key).

2. [Tạo EC2 Instance và cài đặt AWS CLI](2.1.2-createEC2/)  
   → Tạo EC2, chọn instance phù hợp, cấu hình (VPC, Subnet, Security Group),chọn hoặc tạo một keypair để SSH vào instance và launch instance.

---

👉 Sau khi hoàn thành các bước này, bạn sẽ có một môi trường đầy đủ để phát triển và triển khai giải pháp tự động hóa mạng trên nền tảng AWS một cách hiệu quả và chuyên nghiệp.
