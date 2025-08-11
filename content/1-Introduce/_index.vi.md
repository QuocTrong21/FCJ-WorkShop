---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---

Trong bối cảnh phát triển và vận hành hệ thống hiện đại, đặc biệt với các ứng dụng web Java chạy trên nền tảng AWS, yêu cầu **triển khai nhanh, bảo mật cao, khả năng mở rộng và sẵn sàng 24/7** đã trở thành tiêu chuẩn bắt buộc.  

Việc cấu hình hạ tầng thủ công tiềm ẩn nhiều rủi ro:
- Mất thời gian
- Thiếu tính nhất quán
- Khó quản lý phiên bản cấu hình
- Khó kiểm soát rollback khi gặp sự cố

---

## ⚙️ Network Automation là gì?

**Network Automation** là việc sử dụng công cụ và mã lệnh (scripts) để **tự động hoá quá trình tạo lập, cấu hình, giám sát và quản lý hạ tầng mạng**.  
Với AWS, điều này bao gồm:
- **Provisioning**: Tự động tạo VPC, Subnet, Internet Gateway, Route Table, Security Group
- **Configuration**: Cấu hình quyền truy cập, port, rule bảo mật
- **Monitoring**: Giám sát lưu lượng, CPU, kết nối
- **Rollback**: Khôi phục nhanh khi triển khai lỗi

---

## 🛠 Công cụ chính trong dự án

1. **AWS CLI** – Chạy script tạo và cấu hình mạng qua dòng lệnh  
2. **AWS SDK for Java** – Tích hợp quản lý hạ tầng trực tiếp từ ứng dụng Java  
3. **AWS CloudFormation** – Mô tả toàn bộ hạ tầng bằng file YAML (Infrastructure as Code – IaC)  

---

## 🎯 Mục tiêu Workshop

Workshop này sẽ giúp bạn:
- Hiểu cách **tự động hoá toàn bộ hạ tầng mạng AWS** cho ứng dụng web Java
- Triển khai nhanh, chuẩn xác, ít lỗi hơn so với thao tác thủ công
- Kết hợp CLI, Java SDK và CloudFormation để đạt hiệu quả tối đa
- Nắm được **quy trình rollback**, testing và quản lý phiên bản cấu hình

---

### ✅ Provisioning & Configuration
Tự động tạo và cấu hình mạng từ 0 → hệ thống sẵn sàng chạy ứng dụng web Java

### ✅ Monitoring & Alerting
Tích hợp CloudWatch Alarm và SNS để giám sát và cảnh báo kịp thời

### ✅ Rollback & Version Control
Dễ dàng quay lại trạng thái ổn định khi triển khai lỗi, quản lý lịch sử cấu hình với Git

---

## 📚 Nội dung Workshop

1. **Giới thiệu kiến trúc hệ thống**  
2. **Chuẩn bị môi trường**  
3. **Automation với AWS CLI**  
4. **Infrastructure as Code với CloudFormation**  
5. **AWS SDK for Java – Quản lý mạng và giám sát**  
6. **Triển khai giám sát & cảnh báo**  
7. **Kiểm thử & Rollback**    

> 🧠 **Mục tiêu cuối cùng**: Xây dựng framework Network Automation hoàn chỉnh trên AWS cho ứng dụng web Java – nhanh, bảo mật, sẵn sàng mở rộng và dễ quản lý.
