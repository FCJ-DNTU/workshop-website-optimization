+++
title = "AWS Cost Calculator"
weight = 10
chapter = false
pre = "<b>10. </b>"
+++

### Estimated Costs by Service

#### 1. EC2 Instances
```bash
Instance Type: t2.medium
- vCPU: 2
- RAM: 4GB
- Cost/hour: $0.0464 (ap-southeast-1)

ASG Configuration:
- Minimum: 2 instances
- Maximum: 5 instances
- Average: 3 instances

EC2 cost/month:
- Minimum: $0.0464 × 24 × 30 × 2 = $66.82
- Average: $0.0464 × 24 × 30 × 3 = $100.22
- Maximum: $0.0464 × 24 × 30 × 5 = $167.04
```

#### 2. Application Load Balancer
```bash
ALB pricing:
- $0.0225/hour for ALB
- $0.008/LCU-hour

Monthly estimate:
- ALB: $0.0225 × 24 × 30 = $16.20
- LCU (estimated 3 LCU): $0.008 × 3 × 24 × 30 = $17.28

Total ALB/month: $33.48
```

#### 3. S3 Storage
```bash
S3 Standard pricing:
- $0.025/GB/month
- $0.005/1,000 GET requests
- $0.004/10,000 PUT requests

Estimated usage:
- Storage: 50GB
- GET: 100,000 requests/month
- PUT: 10,000 requests/month

S3 cost/month:
- Storage: $0.025 × 50 = $1.25
- GET: $0.005 × (100) = $0.50
- PUT: $0.004 × (1) = $0.004

Total S3/month: $1.75
```

#### 4. CloudWatch
```bash
Pricing:
- $0.30/metric/month
- $0.01/1,000 API requests
- $0.03/GB log data

Estimated usage:
- 10 metrics
- 100,000 API requests
- 5GB logs

CloudWatch cost/month:
- Metrics: $0.30 × 10 = $3.00
- API: $0.01 × (100) = $1.00
- Logs: $0.03 × 5 = $0.15

Total CloudWatch/month: $4.15
```

#### 5. Auto Scaling
```bash
- No charge for ASG service
- Only pay for EC2 instances created
```

### Total Estimated Costs

#### Minimum Cost/Month
```bash
1. EC2 (2 instances): $66.82
2. ALB: $33.48
3. S3: $1.75
4. CloudWatch: $4.15
--------------------------
Total minimum: $106.20
```

#### Average Cost/Month
```bash
1. EC2 (3 instances): $100.22
2. ALB: $33.48
3. S3: $1.75
4. CloudWatch: $4.15
--------------------------
Total average: $139.60
```

#### Maximum Cost/Month
```bash
1. EC2 (5 instances): $167.04
2. ALB: $33.48
3. S3: $1.75
4. CloudWatch: $4.15
--------------------------
Total maximum: $206.42
```

{{% notice info %}}
**Cost Notes:**
1. Prices may vary by region
2. Data transfer costs not included
3. Costs can be reduced using Reserved Instances
4. Monitor costs through AWS Cost Explorer
{{% /notice %}}

{{% notice tip %}}
**Cost Optimization:**
1. Use EC2 Reserved Instances (save 40-60%)
2. Adjust ASG metrics appropriately
3. Optimize S3 lifecycle policies
4. Monitor and alert for unusual costs
{{% /notice %}}