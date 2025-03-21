+++
title = "Dọn dẹp tài nguyên"
date = 2020-05-14T00:38:32+07:00
weight = 10
chapter = false
pre = "<b>10. </b>"
+++

### Tổng Quan
Để tránh phát sinh chi phí không cần thiết, chúng ta cần dọn dẹp tất cả tài nguyên đã tạo trong workshop này. Hãy thực hiện theo thứ tự để đảm bảo xóa an toàn.

### 1. Xóa EC2 Test Permission
```bash
1. Truy cập EC2 Console > Instances
2. Chọn instance "test-permission" (nếu còn)
3. Instance state > Terminate instance
4. Xác nhận terminate
```

### 2. Xóa Auto Scaling Group
```bash
1. Truy cập EC2 Console > Auto Scaling Groups
2. Chọn ASG đã tạo
3. Actions > Delete
4. Xác nhận xóa
5. Đợi tất cả instances terminate
```

{{% notice warning %}}
**Quan trọng:** ASG phải được xóa trước khi xóa Launch Template để đảm bảo tất cả instances được terminate đúng cách.
{{% /notice %}}

### 3. Xóa Load Balancer và Target Group
```bash
1. Xóa Application Load Balancer:
   - EC2 Console > Load Balancers
   - Chọn ALB đã tạo
   - Actions > Delete
   - Xác nhận xóa

2. Xóa Target Group:
   - EC2 Console > Target Groups
   - Chọn Target Group đã tạo
   - Actions > Delete
   - Xác nhận xóa
```

### 4. Xóa Launch Template
```bash
1. EC2 Console > Launch Templates
2. Chọn template đã tạo
3. Actions > Delete template
4. Xác nhận xóa
```

### 5. Xóa AMI và Snapshot
```bash
1. Xóa AMI:
   - EC2 Console > AMIs
   - Chọn AMI đã tạo
   - Actions > Deregister AMI
   
2. Xóa Snapshots:
   - EC2 Console > Snapshots
   - Chọn snapshots liên quan
   - Actions > Delete
```

### 6. Xóa S3 Bucket
```bash
1. S3 Console > Buckets
2. Chọn bucket đã tạo
3. Empty bucket trước:
   - Chọn tất cả objects
   - Delete
4. Delete bucket:
   - Actions > Delete
   - Nhập tên bucket để xác nhận
```

### 7. Xóa CloudWatch Resources
```bash
1. Xóa Alarms:
   - CloudWatch Console > Alarms
   - Chọn các alarms đã tạo
   - Actions > Delete

2. Xóa Dashboards:
   - CloudWatch Console > Dashboards
   - Chọn dashboard đã tạo
   - Actions > Delete dashboard
```

### 8. Xóa Security Groups
```bash
1. EC2 Console > Security Groups
2. Xóa theo thứ tự:
   - public-ec2-sg
   - alb-sg
3. Actions > Delete security groups
```

### 9. Xóa IAM Resources
```bash
1. Xóa IAM Role:
   - IAM Console > Roles
   - Tìm và chọn role đã tạo cho EC2
   - Delete > Xác nhận xóa

2. Xóa IAM Policy:
   - IAM Console > Policies
   - Tìm và chọn custom policy đã tạo
   - Actions > Delete
   - Xác nhận xóa
```

### 10. Verify Cleanup

#### Kiểm tra các console sau:
```bash
1. EC2 Dashboard:
   □ Không còn running instances
   □ Không còn volumes attached
   □ Không còn security groups tạo mới

2. VPC Dashboard:
   □ Không còn VPC tạo mới
   □ Không còn subnets
   □ Không còn route tables

3. S3 Console:
   □ Không còn bucket workshop

4. CloudWatch:
   □ Không còn alarms
   □ Không còn dashboards

5. IAM Console:
   □ Không còn roles tạo mới
   □ Không còn policies tạo mới
```

{{% notice tip %}}
**Mẹo:**
1. Sử dụng AWS Resource Groups để track resources
2. Check AWS Cost Explorer để đảm bảo không còn chi phí phát sinh
3. Verify trong billing dashboard
{{% /notice %}}

{{% notice warning %}}
**Lưu ý quan trọng:**
1. Đảm bảo backup data cần thiết trước khi xóa
2. Tuân thủ thứ tự xóa để tránh dependencies
3. Verify kỹ trước khi xóa resources
4. Kiểm tra lại sau 24h để đảm bảo không còn chi phí
{{% /notice %}}

### Hoàn Thành Workshop!