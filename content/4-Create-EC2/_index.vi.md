+++
title = "Khởi tạo EC2 instance"
date = 2020-05-14T00:38:32+07:00
weight = 4
chapter = false
pre = "<b>4. </b>"
+++

#### EC2
**Amazon Elastic Compute Cloud** (Amazon EC2) là một cơ sở hạ tầng điện toán đám mây được cung cấp bởi **Amazon Web Services** (AWS) giúp cung cấp tài nguyên máy tính ảo hoá theo yêu cầu.

#### Amazon EC2 Instance Type 

**Amazon EC2 Instance Type** (Loại máy chủ EC2) là các cấu hình phần cứng được xác định trước trên Amazon EC2, bao gồm CPU, RAM, bộ nhớ lưu trữ và băng thông mạng. Vì vậy trong Workshop này chúng ta sẽ sử dụng **t2.medium** để tối ưu tài nguyên và tiết kiệm chi phí.

#### Lý do chọn t2.medium
+ **Tiết kiệm chi phí**: Hỗ trợ Burstable Performance, chỉ dùng CPU cao khi cần.
+ **Hiệu năng phù hợp**: 2 vCPU, 4GB RAM, đủ cho web server, API backend, môi trường phát triển.
+ **Dễ mở rộng**: Có thể nâng cấp lên instance cao hơn khi cần.
+ **Tích hợp tốt**: Hỗ trợ AWS services như RDS, S3, CloudWatch. 🚀


#### Tạo một EC2 Instance trên AWS
Trong phần này, chúng ta sẽ cùng nhau tạo một EC2 instance để chạy ứng dụng web của mình. Mặc dù AWS cung cấp nhiều tùy chọn nâng cao, nhưng chúng ta sẽ tập trung vào các bước cơ bản nhất để nhanh chóng có được một máy chủ hoạt động.

{{% notice info %}}
Nếu bạn muốn tìm hiểu thêm về các tùy chọn nâng cao, bạn có thể tham khảo thêm tài liệu chính thức của AWS về [Launch Instance Wizard](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html).
{{% /notice %}}

Hãy bắt đầu với các bước đơn giản sau:
#### 1. Truy cập AWS Console

- Mở trình duyệt và truy cập vào Amazon EC2 console tại https://console.aws.amazon.com/ec2/.

#### 2. Chọn Launch instance

- Tại màn hình dashboard của EC2 console, trong hộp Launch instance, chọn Launch instance, sau đó chọn Launch instance từ các tùy chọn xuất hiện.
![dashboard-ec2](/images/4-Create-EC2/1.png)

#### 3. Đặt tên cho instance

- Dưới phần Name and tags, cho Name, nhập tên mô tả cho instance của bạn.

#### 4. Chọn image (Amazon Machine Image - AMI)

- Dưới phần Application and OS Images (Amazon Machine Image), làm theo các bước sau:
  + Chọn **Quick Start**
  + Chọn **Ubuntu**
  + Từ danh sách AMI, chọn **Ubuntu Server 22.04 LTS (HVM)**
  + Kiểm tra AMI có nhãn **Free tier eligible**

![ami.png](/images/4-Create-EC2/2.png)

{{% notice info %}}
**Lý do chọn Ubuntu 22.04 LTS**:
- LTS (Long Term Support): Được hỗ trợ và cập nhật bảo mật trong 5 năm
- Ổn định và được kiểm thử kỹ
- Kho phần mềm phong phú, dễ dàng cài đặt Node.js và các package cần thiết
- Phổ biến trong cộng đồng developer
{{% /notice %}}

#### 5. Chọn loại instance

- Dưới phần Instance type, từ danh sách Instance type, chọn **t2.medium**
  + 2 vCPU
  + 4GB RAM
  + Network performance: Low to Moderate
  + EBS-optimized available

![instances.png](/images/4-Create-EC2/3.png)

#### 6. Chọn Key Pair

- Tạo Key pair
![create-keypair.png](/images/4-Create-EC2/4.png)

- Sau đó sử dụng Key pair đã được tạo
![keypair.png](/images/4-Create-EC2/5.png)

{{% notice warning %}}
Cảnh báo: Đừng chọn Proceed without a key pair (Không được khuyến nghị). Nếu bạn tạo instance mà không có key pair, bạn sẽ không thể kết nối vào nó.
{{% /notice %}}

#### 7. Cấu hình Security Group

- Bên cạnh Network settings, chọn Edit. Đối với Security group name, bạn sẽ thấy rằng hướng dẫn đã tạo và chọn một security group cho bạn. Bạn chọn security group mà bạn đã tạo khi cài đặt bằng các bước sau đây:

- **Network settings**:
  + VPC: Chọn VPC đã tạo
  + Subnet: Chọn public subnet
  + Auto-assign public IP: **Enable**

- **Firewall (security groups)**:
  + Chọn **Select existing security group**
  + Từ Common security groups, chọn **public-ec2-sg**
  + Xác nhận security group có các rules:
    * HTTP (80) từ ALB
    * HTTPS (443) từ ALB  
    * Custom TCP (9000) từ ALB
    * SSH (22) từ IP của bạn

![sg-config.png](/images/4-Create-EC2/6.png)

#### 8. Xác nhận và kiểm tra

- Một trang xác nhận sẽ thông báo rằng instance của bạn đang được khởi động. Chọn **View all instances** để đóng trang xác nhận và trở lại giao diện console.
- Trên màn hình Instances, bạn có thể xem trạng thái của quá trình khởi động. Có một thời gian ngắn để instance được khởi động. Khi bạn khởi động instance, trạng thái ban đầu của nó là pending. Sau khi instance khởi động, trạng thái của nó sẽ thay đổi thành running và nó sẽ nhận được một tên DNS công cộng. Nếu cột Public IPv4 DNS bị ẩn, hãy chọn biểu tượng cài đặt (Biểu tượng cài đặt) ở góc trên bên phải, bật Public IPv4 DNS và chọn Confirm.
- Có thể mất vài phút để instance sẵn sàng để bạn kết nối vào. Hãy kiểm tra xem instance của bạn đã vượt qua kiểm tra trạng thái; bạn có thể xem thông tin này trong cột Status check.

![review.png](/images/4-Create-EC2/7.png)

- Kiểm tra Public IPv4 click chọn vào **EC2 Instance** > **Networking** tab > **Public IPv4 Address**

![check-ipv4.png](/images/4-Create-EC2/8.png)


#### Kiểm tra kết nối tới EC2 Instance

#### 1. Kết nối EC2 Instance qua AWS Console
- Chọn EC2 Instance vừa khởi tạo, chọn **Connect**
- Tại giao diện **Connect to instance**, chọn tab **EC2 Instance Connect**
- Kiểm tra các thông tin:
  + User name: ubuntu
  + Instance ID: i-xxxxxxxx (ID của instance bạn vừa tạo)
- Nhấn nút **Connect** để kết nối tới instance
![connect-ec2.png](/images/4-Create-EC2/9.png)
{{% notice info %}}
EC2 Instance Connect là một phương pháp kết nối đơn giản và an toàn, cho phép bạn truy cập instance thông qua trình duyệt web mà không cần cấu hình SSH key hay cài đặt phần mềm bổ sung.
{{% /notice %}}

#### 2. Truy cập thành công 
Sau khi kết nối, bạn sẽ thấy terminal trong trình duyệt web hiển thị thông tin của Ubuntu server.

![complete.png](/images/4-Create-EC2/10.png)
### Hoàn Thành !