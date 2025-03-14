+++
title = "Khởi tạo Security Group"
date = 2020-05-14T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3.2 </b>"
+++


#### Khái niệm về VPC Security Group

Security Group là một tập hợp các subnet chạy trong môi trường Amazon Virtual Private Cloud (VPC), cho phép bạn chỉ định các quy tắc kiểm soát lưu lượng đến và đi.

#### Khởi tạo VPC Security Group
Tiếp theo, chúng ta sẽ khởi tạo 2 SGs đại diện cho public subnets (EC2 instance) và ALB (Application Load Balencer)

 Truy cập vào **VPC Service**. Tại thanh điều huướng bên trái, chọn [Security Group](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#SecurityGroups:). Chọn **create security group**


#### Khởi tạo Security Groups

| Security Group | Direction | Protocol | Port Range | Source/Destination | Description |
|---------------|-----------|----------|------------|-------------------|-------------|
| alb-sg        | Inbound   | HTTP     | 80         | 0.0.0.0/0        | Allow HTTP from internet |
| alb-sg        | Inbound   | HTTPS    | 443        | 0.0.0.0/0        | Allow HTTPS from internet |
| alb-sg        | Inbound   | Custom TCP| 9000      | 0.0.0.0/0        | Allow traffic to Node.js app |
| alb-sg        | Outbound  | All      | All        | 0.0.0.0/0        | Allow all outbound traffic |
| public-ec2-sg | Inbound   | HTTP     | 80         | alb-sg           | Allow HTTP from ALB |
| public-ec2-sg | Inbound   | HTTPS    | 443        | alb-sg           | Allow HTTPS from ALB |
| public-ec2-sg | Inbound   | Custom TCP| 9000      | alb-sg           | Allow traffic from ALB to Node.js app |
| public-ec2-sg | Inbound   | Custom TCP| 9000      | 0.0.0.0/0        | Allow direct access to Node.js app |
| public-ec2-sg | Inbound   | SSH      | 22         | All              | Allow SSH access |
| public-ec2-sg | Outbound  | All      | All        | 0.0.0.0/0        | Allow all outbound traffic |

#### 2.1. Khởi tạo Application Load Balencer SG
- Trong interface **Create Security Group**, thêm **Basic Details** cho **alb-sg**
- Thêm **Inbound Rules** tương ứng cho **alb-sg**
- Allows all **outbound** traffic
- Chọn **Create security group**
![alb-sg.png](/images/3-Prepare-VPC/7.png)


#### 2.2 Khởi tạo Public EC2 SG
- Trong interface **Create Security Group**, thêm **Basic Details** cho **public-ec2-sg**
- Thêm **Inbound Rules** tương ứng cho **public-ec2-sg**:
  + HTTP (80) - Source: alb-sg
  + HTTPS (443) - Source: alb-sg  
  + Custom TCP (9000) - Source: alb-sg
  + Custom TCP (9000) - Source: All
  + SSH (22) - Source: All IP
- Allows all **outbound** traffic
- Chọn **Create security group**
![public-ec2-sg.png](/images/3-Prepare-VPC/8.png)
### Hoàn Thành !