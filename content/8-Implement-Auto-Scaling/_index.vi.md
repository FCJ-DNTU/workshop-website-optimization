+++
title = "Khởi chạy Auto Scaling"
date = 2020-05-14T00:38:32+07:00
weight = 8
chapter = false
pre = "<b>8. </b>"
+++

#### Auto Scaling
**Amazon EC2 Auto Scaling** giúp đảm bảo bạn có đủ số lượng EC2 instances để xử lý tải của ứng dụng. Nó tự động thêm hoặc xóa instances dựa trên nhu cầu thực tế.

#### Tác dụng của Auto Scaling trong workshop

Auto Scaling sẽ giúp:
- **Tự động mở rộng**: Thêm EC2 instances khi tải tăng
- **Tự động thu hẹp**: Giảm số lượng instances khi tải giảm
- **Tiết kiệm chi phí**: Chỉ sử dụng tài nguyên khi cần thiết
- **Đảm bảo sẵn sàng**: Tự động thay thế instances gặp sự cố

#### Tạo Auto Scaling Group
Thực hiện theo các bước sau:

{{% notice info %}}
Cần có sẵn Launch Template hoặc Launch Configuration trước khi tạo Auto Scaling Group.
{{% /notice %}}


#### 1. Tạo Auto Scaling Group
- Truy cập vào **AWS Management Console**.
- Chọn **Auto Scaling Groups**
- Click **Create Auto Scaling group**
- Đặt tên cho group
![asg](/images/8-Implement-Auto-Scaling/1.png)
#### 2. Cấu hình Launch Template
- Chọn Launch Template vừa tạo
- Chọn phiên bản (version) của Launch Template (thường là phiên bản mới nhất).
![asg](/images/8-Implement-Auto-Scaling/2.png)
#### 3. Cấu hình Network
- Chọn VPC
- Chọn các Availability Zones và public subnets để đảm bảo tính sẵn sàng cao.
- **Lưu ý**: Nên chọn ít nhất 2 Availability Zones để đảm bảo ứng dụng vẫn hoạt động nếu một AZ gặp sự cố.
![asg](/images/8-Implement-Auto-Scaling/3.png)
#### 4. Cấu hình Load Balancing
- Chọn Attach to an existing load balancer
- Chọn Target Group đã tạo trước đó
![asg](/images/8-Implement-Auto-Scaling/4.png)
![asg](/images/8-Implement-Auto-Scaling/5.png)
#### 5. Cấu hình Group Size

1. **Desired capacity**
- Số lượng instance mong muốn: 2
- Đây là số lượng instance sẽ được duy trì trong điều kiện bình thường
- Phải nằm trong khoảng giữa Minimum và Maximum capacity

2. **Minimum capacity**
- Số lượng instance tối thiểu: 2
- ASG sẽ luôn đảm bảo ít nhất số lượng này, kể cả khi tải thấp
- Nên set tối thiểu 2 để đảm bảo high availability

3. **Maximum capacity**
- Số lượng instance tối đa: 5
- Giới hạn số lượng instance tối đa khi scale out
- Nên set bằng 2-3 lần Minimum để đảm bảo khả năng scale

#### 6. Cấu hình Auto Scaling Policies

1. **Thêm Target Tracking Policy**
```bash
# Basic settings
Policy name: Target-Tracking-Policy
Metric type: Average CPU utilization
Target value: 40%

# Advanced settings
Instance warmup: 300 seconds
Disable scale in: No
```
![target-tracking](/images/8-Implement-Auto-Scaling/6.png)


2. **Instance Maintenance Policy**
```bash
# Best Practice: Prioritize Availability
Replacement behavior: Launch before terminating
- Zero downtime trong quá trình thay thế
- Instance mới sẵn sàng trước khi terminate instance cũ
- Set healthy percentage: 100% đến 110%

# Capacity Settings
Capacity Reservations: First
- Tận dụng reserved capacity
- Fallback to On-Demand khi cần
- Tối ưu chi phí

# Protection Settings
Instance scale-in protection: Enable
- Bảo vệ instances mới
- Đảm bảo stability
- Tránh scale in/out liên tục

# Monitoring
Group metrics collection: Enable
Default instance warmup: 300 seconds
- Thu thập metrics đầy đủ
- Đảm bảo accuracy
- Tránh false metrics
```
![target-tracking](/images/8-Implement-Auto-Scaling/7.png)
{{% notice tip %}}
**Best Practices cho Maintenance Policy:**
1. Luôn chọn "Launch before terminating" cho production
2. Enable đầy đủ monitoring
3. Set warmup period phù hợp (300s)
4. Bảo vệ instances mới với scale-in protection
{{% /notice %}}

{{% notice warning %}}
**Lưu ý Quan Trọng:**
1. Cân nhắc trade-off giữa availability và cost
2. Monitor metrics trong quá trình replacement
3. Có plan rollback nếu cần
{{% /notice %}}

#### 7. Notifications
- Bỏ qua bước này vì chưa cần cấu hình notifications
- Có thể thêm sau khi cần monitor hệ thống
![target-tracking](/images/8-Implement-Auto-Scaling/8.png)
#### 8. Add Tags
- Có thể bổ sung thêm tags sau khi cần

#### 9. Review và Launch
- Kiểm tra lại các cấu hình đã setup
- Click **Create Auto Scaling group**
- Đợi ASG được tạo và instances bắt đầu launch
![target-tracking](/images/8-Implement-Auto-Scaling/9.png)
{{% notice tip %}}
Sau khi launch xong, verify:
- Instances đã được tạo theo desired capacity
- Instances đã register vào Target Group
- Health check status OK
{{% /notice %}}

### Verify Auto Scaling
#### Check Instance được tạo từ desired capacity
Truy cập vào EC2 Instance chúng ta sẽ thấy có 2 Instance đang được khởi tạo có tên Webserver (Tên tagname đã đặt trong Launch Template).
![target-tracking](/images/8-Implement-Auto-Scaling/10.png)
#### Truy cập vào Website trên ALB
- Copy DNS name của ALB từ EC2 Console > Load Balancers
![target-tracking](/images/8-Implement-Auto-Scaling/11.png)
- Paste vào browser để truy cập website
- Verify website hoạt động bình thường
![target-tracking](/images/8-Implement-Auto-Scaling/12.png)

#### Health check status OK
**Kiểm tra Target Group Health Status**
- Vào EC2 Console > Target Groups
- Chọn Target Group đã tạo
- Tab Targets sẽ hiển thị health status của các instances
- Verify status là "healthy" (màu xanh)
![target-tracking](/images/8-Implement-Auto-Scaling/13.png)

{{% notice tip %}}
Nếu instances hiện "unhealthy", kiểm tra:
- Security group rules
- Health check settings trong Target Group
- Application logs trên instances
{{% /notice %}}

#### Như vậy về phần config Auto Scaling đã hợp lý, chúng ta hãy cùng nhau kiểm tra hiệu năng và hiểu về luồng hoạt động của ứng dụng trong sections sau.

### Hoàn Thành !