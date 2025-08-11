---
title : " Hướng Dẫn Tạo EC2 Instance Trên AWS"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.1.2 </b> "
---



## 📌 Mục Tiêu
Tạo một EC2 instance sử dụng cấu hình mặc định để triển khai ứng dụng hoặc kiểm thử môi trường.

## 🧰 Yêu Cầu Trước Khi Bắt Đầu
- Tài khoản AWS hợp lệ
- Đăng nhập AWS Console hoặc cài sẵn AWS CLI
- Tạo key pair để SSH (nếu cần)

## 🛠️ Các Bước Thực Hiện

### 1. Đăng Nhập AWS Console
Truy cập: [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)

### 2. Tạo EC2 Instance

#### Cách 1: Qua AWS Console
1. Vào **EC2 Dashboard**
2. Nhấn **Launch Instance**
3. Nhập tên: `workshop`
4. **Chọn AMI**: `Amazon Linux 2023` (hoặc Amazon Linux 2)
5. **Loại Instance**: `t2.micro` (Free tier)
6. **Key pair**: Chọn hoặc tạo mới
7. **Network Settings**:
   - Allow SSH (port 22)
   - Allow HTTP (port 80) nếu cần
8. **Ổ đĩa**: Mặc định 8 GB (gp2)
9. Nhấn **Launch Instance**
![VPC](/images/2.prerequisite/12-1.jpg)
![VPC](/images/2.prerequisite/2.3.png)
![VPC](/images/2.prerequisite/2.4.png)
![VPC](/images/2.prerequisite/2.5.png)

## 🧰 3. Cài Đặt AWS CLI Trên Linux

Chạy các lệnh sau để cài đặt AWS CLI v2:

```bash
# Tải AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Giải nén
unzip awscliv2.zip

# Cài đặt
sudo ./aws/install

# Kiểm tra phiên bản
aws --version
```
![VPC](/images/2.prerequisite/2.7.png)

---

## ⚙️ 4. Cấu Hình AWS CLI

Sau khi cài đặt, chạy lệnh sau để cấu hình thông tin tài khoản:

```bash
aws configure
```
![VPC](/images/2.prerequisite/2.8.png)
Nhập các thông tin:

- `AWS Access Key ID`: từ IAM user  
- `AWS Secret Access Key`: từ IAM user  
- `Default region name`: `ap-northeast-1` (hoặc vùng bạn sử dụng)  
- `Default output format`: `json`

---
![VPC](/images/2.prerequisite/2.9.png)



## ✅ Kết Quả
- EC2 instance được tạo và chạy trong vài phút.
- Có thể SSH để cài đặt thêm hoặc triển khai ứng dụng.

## 🧹 Mẹo Quản Lý
- Tắt hoặc terminate instance sau khi dùng để tránh mất phí.
- Gắn Elastic IP nếu muốn giữ IP cố định.

## 📚 Tài Liệu Tham Khảo
- [EC2 User Guide](https://docs.aws.amazon.com/ec2/index.html)
- [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/)
