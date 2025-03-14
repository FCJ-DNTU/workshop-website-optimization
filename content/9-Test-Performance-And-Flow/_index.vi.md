+++
title = "Kiểm tra hiệu năng và luồng hoạt động Auto Scaling"
weight = 9
pre = "<b>9 </b>"
+++

### Tổng quan

Trong phần này, chúng ta sẽ:
1. Kiểm tra hiệu năng của ứng dụng thông qua việc test tải
2. Theo dõi luồng hoạt động của Auto Scaling khi có tải cao

### Nội dung

{{% notice info %}}
Chúng ta sẽ sử dụng công cụ k6 để test hiệu năng và theo dõi cách Auto Scaling Group phản ứng với tải.
{{% /notice %}}

#### 1. Kiểm tra hiệu năng
- Test tải ứng dụng với các kịch bản khác nhau
- Phân tích kết quả và metrics

#### 2. Luồng hoạt động Auto Scaling
- Theo dõi quá trình scale out khi tải tăng
- Kiểm tra health check và instance states
- Verify hoạt động của Load Balancer

### Mục tiêu
- Hiểu được hiệu năng hiện tại của ứng dụng
- Xác nhận Auto Scaling hoạt động đúng như cấu hình
- Đảm bảo hệ thống có thể tự động mở rộng khi cần thiết

{{% notice tip %}}
Hãy theo dõi CloudWatch metrics trong quá trình test để có cái nhìn tổng quan về hiệu năng hệ thống.
{{% /notice %}}
