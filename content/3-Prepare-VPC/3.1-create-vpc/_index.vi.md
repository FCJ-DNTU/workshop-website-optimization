+++
title = "Khởi tạo VPC Instance"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>3.1. </b>"
+++

#### Khái niệm về VPC

Trong bài Workshop này, *Amazon Virtual Private Cloud (VPC)* được sử dụng để tạo một mạng riêng ảo (private network), cho phép tổ chức và quản lý các tài nguyên trong một không gian mạng riêng biệt trên AWS.

VPC giúp bạn kiểm soát hoàn toàn môi trường mạng của mình, từ cấu hình địa chỉ IP, bảng định tuyến (route table), cổng internet (Internet Gateway) cho đến các mạng con (subnet).


#### Các trường hợp sử dụng chính của VPC trong Workshop

- Triển khai ứng dụng với **Auto Scaling Group** và **Application Load Balancer**

- Lưu trữ và quản lý tài nguyên trên **S3** thông qua Gateway

- Đảm bảo tính sẵn sàng cao với kiến trúc **Multi-AZ**

#### Chi phí cho VPC

Sẽ không phát sinh chi phí khi sử dụng VPC. Tuy nhiên, sẽ có phí dịch vụ cho những **dịch vụ của VPC**, ví dụ NAT Gateways, IP Address Manager, traffic mirroring, Reachability Analyzer, và Network Access Analyzer.

#### Khởi tạo VPC Instance
Ở section này chúng ta sẽ khởi tạo **VPC Instance**, bao gồm 2 **AZ**, 2 Public Subnets
  
#### 1. Tạo VPC Instance 
- Truy cập [Your VPCs](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#vpcs:), Create VPC
- Tại **VPC Settings**, chọn option **VPC and more**

- Đặt tên **name tag** `optimization-website` và để mặc định các field khác, chọn **Create VPC**

![create-vpc](/images/3-Prepare-VPC/1testt.png)
- Kết quả tạo VPC Instance thành công
![review-result](/images/3-Prepare-VPC/2.png)

#### 2. Tiếp theo chúng ta sẽ gán Public IP4 cho các public subnet
- Truy cập [Subnets](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#subnets:)
- Chọn **Subnet ID** của public subnet e.g. **optimization-website-subnet-public1-ap-southeast-1a**
![subnets](/images/3-Prepare-VPC/3.png)
- Chọn dropdown **Actions**, **Edit subnet settings**
![edit-subnet](/images/3-Prepare-VPC/4.png)
- Tích vào **Enable auto-assign public IPv4 address**, Save
![enable-ipv4](/images/3-Prepare-VPC/5.png)
- Hoàn thành gán Public IPv4 cho một public subnet **optimization-website-subnet-public1-ap-southeast-1a**
![complete](/images/3-Prepare-VPC/6.png)

### Hoàn Thành !