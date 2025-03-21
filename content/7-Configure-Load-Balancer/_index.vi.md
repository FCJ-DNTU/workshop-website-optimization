+++
title = "Cấu hình Load Balancer"
date = 2020-05-14T00:38:32+07:00
weight = 7
chapter = false
pre = "<b>7. </b>"
+++

#### Giới thiệu
Trong phần này, chúng ta sẽ thiết lập Load Balancer để phân phối traffic giữa các EC2 instances. Quá trình này được chia thành hai phần chính:

1. [Cấu hình AMI và Launch Template](7.1-ami-launch-template/)
   - Chuẩn bị EC2 Instance với Node.js và các dependencies
   - Tạo AMI từ instance đã cấu hình
   - Thiết lập Launch Template với các cấu hình cần thiết
   - Kiểm tra và xác nhận template hoạt động

2. [Cấu hình ALB và Target Group](7.2-create-alb/)
   - Tạo và cấu hình Application Load Balancer
   - Thiết lập Target Group với health checks
   - Cấu hình listeners cho các ports (80, 443, 9000)
   - Kiểm tra và monitoring hệ thống
3. [Khởi chạy Auto Scaling](7.3-auto-scaling/)
   - Tạo Auto Scaling Group sử dụng Launch Template
   - Cấu hình scaling policies
   - Thiết lập target tracking scaling policy dựa trên CPU utilization
   - Liên kết với Target Group của ALB
   - Kiểm tra hoạt động của Auto Scaling

{{% notice info %}}
Trước khi bắt đầu, hãy đảm bảo:
- EC2 instance đã được cài đặt và cấu hình đúng với ứng dụng Node.js
- Security Groups đã được tạo với các rules phù hợp
- VPC và Subnets đã được cấu hình đúng
{{% /notice %}}

{{% notice tip %}}
**Best Practices:**
- Sử dụng nhiều AZs để tăng tính sẵn sàng
- Cấu hình health checks phù hợp
- Enable logging và monitoring
- Test kỹ lưỡng trước khi triển khai production
{{% /notice %}}