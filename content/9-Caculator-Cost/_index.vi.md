+++
title = "Tính Toán Chi Phí AWS"
weight = 9
chapter = false
pre = "<b>9. </b>"
+++

### Chi Phí Ước Tính Theo Service

#### 1. EC2 Instances
```bash
Instance Type: t2.medium
- vCPU: 2
- RAM: 4GB
- Chi phí/giờ: $0.0464 (ap-southeast-1)

Cấu hình ASG:
- Minimum: 2 instances
- Maximum: 5 instances
- Trung bình: 3 instances

Chi phí EC2/tháng:
- Minimum: $0.0464 × 24 × 30 × 2 = $66.82
- Average: $0.0464 × 24 × 30 × 3 = $100.22
- Maximum: $0.0464 × 24 × 30 × 5 = $167.04
```

#### 2. Application Load Balancer
```bash
ALB pricing:
- $0.0225/giờ cho ALB
- $0.008/LCU-giờ

Ước tính tháng:
- ALB: $0.0225 × 24 × 30 = $16.20
- LCU (ước tính 3 LCU): $0.008 × 3 × 24 × 30 = $17.28

Tổng ALB/tháng: $33.48
```

#### 3. S3 Storage
```bash
Giá S3 Standard:
- $0.025/GB/tháng
- $0.005/1,000 GET requests
- $0.004/10,000 PUT requests

Ước tính sử dụng:
- Storage: 50GB
- GET: 100,000 requests/tháng
- PUT: 10,000 requests/tháng

Chi phí S3/tháng:
- Storage: $0.025 × 50 = $1.25
- GET: $0.005 × (100) = $0.50
- PUT: $0.004 × (1) = $0.004

Tổng S3/tháng: $1.75
```

#### 4. CloudWatch
```bash
Pricing:
- $0.30/metric/tháng
- $0.01/1,000 API requests
- $0.03/GB log data

Ước tính sử dụng:
- 10 metrics
- 100,000 API requests
- 5GB logs

Chi phí CloudWatch/tháng:
- Metrics: $0.30 × 10 = $3.00
- API: $0.01 × (100) = $1.00
- Logs: $0.03 × 5 = $0.15

Tổng CloudWatch/tháng: $4.15
```

#### 5. Auto Scaling
```bash
- Không tính phí cho ASG service
- Chỉ tính phí EC2 instances được tạo
```

### Tổng Chi Phí Ước Tính

#### Chi Phí Tối Thiểu/Tháng
```bash
1. EC2 (2 instances): $66.82
2. ALB: $33.48
3. S3: $1.75
4. CloudWatch: $4.15
--------------------------
Tổng tối thiểu: $106.20
```

#### Chi Phí Trung Bình/Tháng
```bash
1. EC2 (3 instances): $100.22
2. ALB: $33.48
3. S3: $1.75
4. CloudWatch: $4.15
--------------------------
Tổng trung bình: $139.60
```

#### Chi Phí Tối Đa/Tháng
```bash
1. EC2 (5 instances): $167.04
2. ALB: $33.48
3. S3: $1.75
4. CloudWatch: $4.15
--------------------------
Tổng tối đa: $206.42
```

{{% notice info %}}
**Lưu ý về chi phí:**
1. Giá có thể thay đổi theo region
2. Data transfer chưa được tính
3. Có thể giảm chi phí bằng Reserved Instances
4. Theo dõi chi phí qua AWS Cost Explorer
{{% /notice %}}

{{% notice tip %}}
**Tối ưu chi phí:**
1. Sử dụng EC2 Reserved Instances (tiết kiệm 40-60%)
2. Điều chỉnh ASG metrics phù hợp
3. Tối ưu S3 lifecycle policies
4. Monitor và alert chi phí bất thường
{{% /notice %}}