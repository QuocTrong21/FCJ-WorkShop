---
title: "Tạo IAM User và lấy Access Key, Secret Access Key, Public Key"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1.1 </b> "
---

## 1. Tạo IAM User và lấy Access Key, Secret Access Key, Public Key

Để làm việc với AWS CLI, AWS SDK hoặc CloudFormation, bước đầu tiên là tạo một IAM User có quyền truy cập phù hợp và lấy thông tin Access Key, Secret Access Key để cấu hình.

---

### Các bước thực hiện

1. Đăng nhập AWS Management Console với quyền Admin.  
2. Vào **IAM** > **Users** > **Add user**.  
3. Nhập tên user (ví dụ: `network-automation-user`).  
4. Chọn **Programmatic access** để tạo Access Key và Secret Access Key.  
5. Gán quyền phù hợp, ví dụ:  
   - `AmazonEC2FullAccess`  
   - `AmazonVPCFullAccess`  
   - `IAMReadOnlyAccess`  
   - `AdministratorAccess` (nếu test)  
6. Nhấn **Create user** và tải file CSV chứa Access Key, Secret Access Key.  
7. Tạo hoặc import **Key Pair** trong EC2 để SSH (nếu cần). 
![VPC](/images/2.prerequisite/2.1.png) 
![VPC](/images/2.prerequisite/2.2.png)

### Lưu ý quan trọng

- **Bảo mật Access Key và Secret Key:** Không chia sẻ hoặc để lộ công khai.
- Nếu user có quyền rộng (AdministratorAccess), chỉ nên dùng trong môi trường thử nghiệm.
- Nên sử dụng IAM Role cho EC2 khi có thể để tránh dùng Access Key trực tiếp.

---

### Tham khảo thêm

- [Hướng dẫn tạo IAM User trên AWS](https://000002.awsstudygroup.com/vi/)
- [Quản lý Access Key cho IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html)
- [Key Pairs for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
