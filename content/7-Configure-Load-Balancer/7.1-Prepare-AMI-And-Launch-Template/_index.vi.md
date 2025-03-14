+++
title = "Cấu hình AMI và Launch Template"
weight = 1
pre = "<b>7.1 </b>"
+++

### Tạo AMI (Amazon Machine Image)

#### 1. Verify EC2 Instance
```bash
# Kiểm tra ứng dụng đang chạy
pm2 status
pm2 list

# Verify các file cấu hình
ls -la /home/ubuntu/nodejs-app/<YOUR_PROJECT_FOLDER>
cat /etc/systemd/system/nodejs-app.service
```
![ami](/images/7-Configure-Load-Balancer/1.png)
#### 2. Tạo AMI từ EC2 Console

1. **Truy cập EC2 Console**
   - Chọn instance đã cấu hình
   - Actions → Image and templates → Create image

2. **Điền thông tin AMI**
```bash
# Basic settings
Image name: nodejs-app-ami-v1
Description: AMI for Node.js application with PM2 and configured environment

# Advanced settings
No reboot: ✓ (Không restart instance khi tạo AMI)
```
![create-ami](/images/7-Configure-Load-Balancer/2.png)


#### 3. Verify AMI Status

1. **Kiểm tra trạng thái AMI**
   - EC2 Console → Images → AMIs
   - Đợi status chuyển từ "pending" sang "available"
   - Sau đó đặt tên AMI thành `nodejs-app-ami-v1`
![verify-ami](/images/7-Configure-Load-Balancer/3.png)


{{% notice warning %}}
**Lưu ý quan trọng:**
1. Đảm bảo ứng dụng hoạt động ổn định trước khi tạo AMI
{{% /notice %}}


### Tạo Launch Template

#### 1. Cấu hình Launch Template
1. EC2 Console > Launch Templates > Create launch template
![launch-template](/images/7-Configure-Load-Balancer/4.png)
2. Thông tin cơ bản:
   - Name: `nodejs-app-template-v1`
   - Description: `Launch template for Node.js application`
   - Auto Scaling guidance: ✓ Check
![launch-template](/images/7-Configure-Load-Balancer/5.png)
#### 2. Cấu hình chi tiết
1. **Application and OS Images**:
   - My AMIs: Chọn AMI đã tạo (`nodejs-app-ami-v1`)

2. **Instance type**:
   - t2.medium
![launch-template](/images/7-Configure-Load-Balancer/6.png)
3. **Key pair**:
   - Chọn key pair đã tạo

4. **Network settings**:
   - Subnet: Chọn Don't include in launch template
   - FireWall: Select existing security group
   - Common security groups: Chọn **public-ec2-sg** đã tạo ở Security Group

![launch-template](/images/7-Configure-Load-Balancer/7.png)

5. **Advanced details**:
   - User data:
```bash
#!/bin/bash
# Reload systemd để nhận config mới
sudo systemctl daemon-reload

# Enable service để tự động chạy khi boot
sudo systemctl enable nodejs-app

# Khởi động service
sudo systemctl start nodejs-app

# Kiểm tra status
sudo systemctl status nodejs-app
```
![launch-template](/images/7-Configure-Load-Balancer/8.png)
{{% notice warning %}}
**Kiểm tra kỹ các điểm sau:**
1. AMI phải hoàn tất trước khi tạo template
2. Security group phải có đúng inbound rules (ports 9000, 22)
3. IAM role phải có đủ quyền cần thiết
4. User data script phải chính xác
{{% /notice %}}

#### 3. Test Launch Template
1. Chọn template vừa tạo
2. Actions > Launch instance from template
3. Verify:
   - Instance khởi động thành công
   - Ứng dụng Node.js chạy đúng
   - Có thể truy cập port 9000
4. Terminate test instance sau khi verify

#### Tài liệu tham khảo
1. [Creating an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)
2. [Launch Templates](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html)
3. [User Data Scripts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)

### Hoàn Thành !