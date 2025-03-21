+++
title = "Cấu hình ALB và Target Group"
weight = 2
pre = "<b>7.2 </b>"
+++

### 1. Tạo Target Group

#### 1.1. Cấu hình cơ bản Target Group
1. Truy cập EC2 Console > Target Groups > Create target group
![targetgroup](/images/7-Configure-Load-Balancer/9.png)
2. Specify group details:
   - Choose a target type: **Instances**
   - Target group name: `nodejs-app-tg`
   - Protocol: **HTTP**
   - Port: **9000**
   - VPC: Chọn VPC đã tạo
   - Protocol version: **HTTP1**
![targetgroup](/images/7-Configure-Load-Balancer/10.png)
#### 1.2. Health checks
1. Health check settings:
   - Protocol: **HTTP**
   - Path: `/health`
   
   **Lưu ý: Phải Thêm Route Health Check Trên Dự Án**
   
   ```javascript
   // Thêm vào app.js hoặc routes
   app.get('/health', (req, res) => {
   res.status(200).json({
      status: 'OK',
      timestamp: new Date(),
      uptime: process.uptime()
   });
   });
   ```
   - Port: **traffic port** (9000)
   - Healthy threshold: 2
   - Unhealthy threshold: 3
   - Timeout: 5 seconds
   - Interval: 30 seconds
   - Success codes: 200-299
![targetgroup](/images/7-Configure-Load-Balancer/11.png)
#### 1.3. Register Targets
Click "Create target group"
![targetgroup](/images/7-Configure-Load-Balancer/12.png)
### 2. Tạo Application Load Balancer

#### 2.1. Basic Configuration
1. Truy cập EC2 Console > Load Balancers > Create Load Balancer
![ALB](/images/7-Configure-Load-Balancer/13.png)
2. Chọn Application Load Balancer
![ALB](/images/7-Configure-Load-Balancer/14.png)
3. Cấu hình cơ bản:
   - Name: `nodejs-app-alb`
   - Scheme: Internet-facing
   - IP address type: IPv4
![ALB](/images/7-Configure-Load-Balancer/15.png)
#### 2.2. Network Configuration
1. Network mapping:
   - VPC: Chọn VPC đã tạo
   - Mappings: Chọn ít nhất 2 AZs và public subnets

2. Security Groups:
   - Chọn `alb-sg`
   - Verify rules:
     + HTTP (80) từ 0.0.0.0/0
     + HTTPS (443) từ 0.0.0.0/0
![ALB](/images/7-Configure-Load-Balancer/16.png)
#### 2.3. Listeners
1. **HTTP:80**
   - Protocol: HTTP
   - Port: 80
   - Default action: Forward to `nodejs-app-tg`
![ALB](/images/7-Configure-Load-Balancer/17.png)
#### 2.4. Review và Create
1. Review cấu hình
2. Click "Create load balancer"
3. Kiểm tra Resource Map

Sau khi tạo xong ALB, bạn có thể kiểm tra Resource Map để xác nhận:
- ALB đã được kết nối với Target Group
- Target Group đã có các EC2 instances được đăng ký
- Security Groups được cấu hình đúng
![ALB](/images/7-Configure-Load-Balancer/18.png)
{{% notice info %}}
**Port 9000:**
- Traffic flow: Client → ALB (80/443) → Target Group (9000) → EC2 Instance (9000)
- Không cần expose port 9000 trên ALB
- Security group của ALB chỉ cần mở port 80
{{% /notice %}}




#### Tài liệu tham khảo
1. [ALB Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
2. [Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
3. [ALB Monitoring](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-monitoring.html)
### Hoàn Thành !