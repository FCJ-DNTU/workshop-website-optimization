+++
title = "Clean Up Resources"
date = 2020-05-14T00:38:32+07:00
weight = 10
chapter = false
pre = "<b>10. </b>"
+++

### Overview
To avoid unnecessary costs, we need to clean up all resources created in this workshop. Please follow the order to ensure safe deletion.

### 1. Delete EC2 Test Permission
```bash
1. Go to EC2 Console > Instances
2. Select "test-permission" instance (if exists)
3. Instance state > Terminate instance
4. Confirm termination
```

### 2. Delete Auto Scaling Group
```bash
1. Go to EC2 Console > Auto Scaling Groups
2. Select the created ASG
3. Actions > Delete
4. Confirm deletion
5. Wait for all instances to terminate
```

{{% notice warning %}}
**Important:** ASG must be deleted before deleting the Launch Template to ensure all instances are terminated properly.
{{% /notice %}}

### 3. Delete Load Balancer and Target Group
```bash
1. Delete Application Load Balancer:
   - EC2 Console > Load Balancers
   - Select the created ALB
   - Actions > Delete
   - Confirm deletion

2. Delete Target Group:
   - EC2 Console > Target Groups
   - Select the created Target Group
   - Actions > Delete
   - Confirm deletion
```

### 4. Delete Launch Template
```bash
1. EC2 Console > Launch Templates
2. Select the created template
3. Actions > Delete template
4. Confirm deletion
```

### 5. Delete AMI and Snapshot
```bash
1. Delete AMI:
   - EC2 Console > AMIs
   - Select the created AMI
   - Actions > Deregister AMI
   
2. Delete Snapshots:
   - EC2 Console > Snapshots
   - Select related snapshots
   - Actions > Delete
```

### 6. Delete S3 Bucket
```bash
1. S3 Console > Buckets
2. Select the created bucket
3. Empty bucket first:
   - Select all objects
   - Delete
4. Delete bucket:
   - Actions > Delete
   - Enter bucket name to confirm
```

### 7. Delete CloudWatch Resources
```bash
1. Delete Alarms:
   - CloudWatch Console > Alarms
   - Select created alarms
   - Actions > Delete

2. Delete Dashboards:
   - CloudWatch Console > Dashboards
   - Select created dashboard
   - Actions > Delete dashboard
```

### 8. Delete Security Groups
```bash
1. EC2 Console > Security Groups
2. Delete in order:
   - public-ec2-sg
   - alb-sg
3. Actions > Delete security groups
```

### 9. Delete IAM Resources
```bash
1. Delete IAM Role:
   - IAM Console > Roles
   - Find and select the role created for EC2
   - Delete > Confirm deletion

2. Delete IAM Policy:
   - IAM Console > Policies
   - Find and select the custom policy created
   - Actions > Delete
   - Confirm deletion
```

### 10. Verify Cleanup

#### Check the following consoles:
```bash
1. EC2 Dashboard:
   □ No running instances
   □ No attached volumes
   □ No new security groups

2. VPC Dashboard:
   □ No new VPC
   □ No subnets
   □ No route tables

3. S3 Console:
   □ No workshop bucket

4. CloudWatch:
   □ No alarms
   □ No dashboards

5. IAM Console:
   □ No new roles
   □ No new policies
```

{{% notice tip %}}
**Tips:**
1. Use AWS Resource Groups to track resources
2. Check AWS Cost Explorer to ensure no ongoing costs
3. Verify in billing dashboard
{{% /notice %}}

{{% notice warning %}}
**Important Notes:**
1. Ensure necessary data is backed up before deletion
2. Follow deletion order to avoid dependencies
3. Verify carefully before deleting resources
4. Check again after 24h to ensure no remaining costs
{{% /notice %}}

### Workshop Completed!