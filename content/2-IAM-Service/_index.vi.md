+++
title = "Hạn chế quyền truy cập với IAM Service"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

{{% notice note %}}
Best practice, hạn chế sử dụng **Root User**.
Thay vào đó hãy tạo IAM User vừa đủ quyền hạn truy cập tài nguyên để dễ dàng quản lý và tránh những rủi ro về bảo mật.
{{% /notice %}}

Ở **section này**,
chúng ta sẽ tạo IAM User với quyền truy cập đầy đủ (Full Access) cho các dịch vụ:
- **Amazon EC2**
- **Amazon S3** 
- **Auto Scaling**
- **Application Load Balancer**

#### 1. Truy cập AWS IAM Management Console

![iam-management](/images/2-IAM-Service/1.png)

#### 2. Tạo User và gán Full Access Permissions

2.1. Truy cập [Users](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/users) trong IAM Console

2.2. Chọn **Create user**

2.3. Tại **Bước 1 - Specify user details**
- Đặt **username** `restricted_user`
- Chọn **Provide user access to the AWS Management Console**
- Chọn **I want to create an IAM user**
- Đặt **custom password** `Restricted_user_for_workshop`
- Chọn **Next**
![specify-user-detail](/images/2-IAM-Service/2.png)

2.4. Tại **Bước 2 - Set permissions**
- Chọn **Attach policies directly**
- Tìm và chọn các policies sau:
  + **AmazonEC2FullAccess** - Quyền đầy đủ với EC2
  + **AmazonS3FullAccess** - Quyền đầy đủ với S3
  + **AutoScalingFullAccess** - Quyền đầy đủ với Auto Scaling
  + **ElasticLoadBalancingFullAccess** - Quyền đầy đủ với Load Balancer

- Tạo thêm policy cho EC2 Instance Connect:
  + Policy này sẽ giúp chúng ta có thể truy cập được vào EC2
  + Chọn **Create policy**
  + Chọn tab **JSON**
  + Paste đoạn policy sau:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2-instance-connect:SendSSHPublicKey",
            "Resource": "*"
        }
    ]
}
```
  + Đặt tên policy: **EC2InstanceConnectPolicy**
  + Chọn **Create policy**
  + Quay lại màn hình attach policy và chọn policy vừa tạo
- Chọn **Next**

![set-permission](/images/2-IAM-Service/3test.png)

- **Dưới đây là cách filter vào mục policy mình đã tạo**

![policies](/images/2-IAM-Service/8.png)
2.5. Tại **Bước 3 - Review and Create**
- Kiểm tra thông tin user và permissions đã attach
- Chọn **Create user**
![review-pwd](/images/2-IAM-Service/4test.png)
2.6. Tại **Bước 4 - Retrieve password**
- Lưu trữ hoặc tải xuống bản **.csv** chứa thông tin đăng nhập
- Copy **Console sign-in URL**
![change-password](/images/2-IAM-Service/5.png)

#### 3. Đăng nhập và xác thực

3.1. Đăng nhập IAM User
- Sử dụng **sign-in URL** đã copy
- Đăng nhập với **username** và **password**
![login](/images/2-IAM-Service/6.png)
3.2. Đổi mật khẩu lần đầu đăng nhập
![change-password](/images/2-IAM-Service/7.png)

3.3. Kiểm tra quyền truy cập các dịch vụ
- Verify EC2 service
- Verify S3 service  
- Verify Auto Scaling service
- Verify Load Balancer service

{{% notice warning %}}
Lưu ý bảo mật:
- Bảo vệ thông tin đăng nhập cẩn thận
- Nên bật **MFA** cho tài khoản
- Định kỳ đổi mật khẩu
{{% /notice %}}

### Hoàn Thành !
