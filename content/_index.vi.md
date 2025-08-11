---
title: "Tổng quan – Network Automation với AWS CLI, AWS SDK (Java), CloudFormation"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Network Automation với AWS CLI, AWS SDK for Java, và CloudFormation

### Tổng quan

Đề tài này tập trung vào việc **tự động hoá hạ tầng mạng (Network Automation)** trên AWS cho hệ thống web Java.  
Giải pháp sử dụng kết hợp **AWS CLI**, **AWS SDK for Java**, và **AWS CloudFormation** để:

- **Provisioning**: Tự động tạo VPC, Subnet, Security Group, EC2.
- **Configuration**: Cấu hình tài nguyên mạng qua CLI và Java SDK.
- **Monitoring**: Giám sát hệ thống bằng CloudWatch Alarm, gửi cảnh báo qua SNS.
- **Rollback**: Khôi phục khi triển khai gặp lỗi.
- **Documentation**: Ghi lại quy trình và kiến trúc.

Hướng tiếp cận này giúp giảm thiểu thao tác thủ công, đảm bảo tính nhất quán, và tăng tốc độ triển khai hạ tầng cho dự án web Java (ví dụ: web xem phim).

![AWS-Network-Automation](/images/sodo.png)

### Nội dung

1. [Introduction](1-introduce/)
2. [Yêu cầu Chuẩn bị](2-Prerequiste/)
3. [Tự động hoá bằng AWS CLI](3-awscli/)
4. [Triển khai IaC với CloudFormation](4-cloudformation/)
5. [Quản lý Mạng qua AWS SDK for Java](5-java-sdk/)
6. [Dọn dẹp Tài nguyên](6-cleanup/)
