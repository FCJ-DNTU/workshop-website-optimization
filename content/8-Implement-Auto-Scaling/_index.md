+++
title = "Implement Auto Scaling"
date = 2020-05-14T00:38:32+07:00
weight = 8
chapter = false
pre = "<b>8. </b>"
+++

#### Auto Scaling
**Amazon EC2 Auto Scaling** helps ensure you have the right number of EC2 instances to handle your application's load. It automatically adds or removes instances based on actual demand.

#### Benefits of Auto Scaling in this workshop

Auto Scaling will help:
- **Automatic expansion**: Add EC2 instances when load increases
- **Automatic reduction**: Decrease number of instances when load decreases
- **Cost savings**: Only use resources when needed
- **Ensure availability**: Automatically replace failed instances

#### Creating Auto Scaling Group
Follow these steps:

{{% notice info %}}
A Launch Template or Launch Configuration is required before creating an Auto Scaling Group.
{{% /notice %}}

#### 1. Create Auto Scaling Group
- Access the **AWS Management Console**
- Select **Auto Scaling Groups**
- Click **Create Auto Scaling group**
- Name the group
![asg](/images/8-Implement-Auto-Scaling/1.png)

#### 2. Configure Launch Template
- Select the created Launch Template
- Choose the Launch Template version (usually the latest)
![asg](/images/8-Implement-Auto-Scaling/2.png)

#### 3. Configure Network
- Select VPC
- Choose Availability Zones and public subnets for high availability
- **Note**: Choose at least 2 Availability Zones to ensure application continues running if one AZ fails
![asg](/images/8-Implement-Auto-Scaling/3.png)

#### 4. Configure Load Balancing
- Choose Attach to an existing load balancer
- Select previously created Target Group
![asg](/images/8-Implement-Auto-Scaling/4.png)
![asg](/images/8-Implement-Auto-Scaling/5.png)

#### 5. Configure Group Size

1. **Desired capacity**
- Desired number of instances: 2
- This is the number of instances maintained under normal conditions
- Must be between Minimum and Maximum capacity

2. **Minimum capacity**
- Minimum number of instances: 2
- ASG will always maintain at least this number, even under low load
- Set minimum 2 to ensure high availability

3. **Maximum capacity**
- Maximum number of instances: 5
- Limits maximum instances during scale out
- Set to 2-3 times Minimum for scaling capacity

#### 6. Configure Auto Scaling Policies

1. **Add Target Tracking Policy**
```bash
# Basic settings
Policy name: Target-Tracking-Policy
Metric type: Average CPU utilization
Target value: 40%

# Advanced settings
Instance warmup: 300 seconds
Disable scale in: No
```
![target-tracking](/images/8-Implement-Auto-Scaling/6.png)

2. **Instance Maintenance Policy**
```bash
# Best Practice: Prioritize Availability
Replacement behavior: Launch before terminating
- Zero downtime during replacement
- New instance ready before terminating old one
- Set healthy percentage: 100% to 110%

# Capacity Settings
Capacity Reservations: First
- Utilize reserved capacity
- Fallback to On-Demand when needed
- Optimize costs

# Protection Settings
Instance scale-in protection: Enable
- Protect new instances
- Ensure stability
- Avoid continuous scale in/out

# Monitoring
Group metrics collection: Enable
Default instance warmup: 300 seconds
- Collect comprehensive metrics
- Ensure accuracy
- Avoid false metrics
```
![target-tracking](/images/8-Implement-Auto-Scaling/7.png)

{{% notice tip %}}
**Best Practices for Maintenance Policy:**
1. Always choose "Launch before terminating" for production
2. Enable full monitoring
3. Set appropriate warmup period (300s)
4. Protect new instances with scale-in protection
{{% /notice %}}

{{% notice warning %}}
**Important Notes:**
1. Consider trade-off between availability and cost
2. Monitor metrics during replacement
3. Have rollback plan if needed
{{% /notice %}}

#### 7. Notifications
- Skip this step as notifications aren't needed yet
- Can be added later when system monitoring is required
![target-tracking](/images/8-Implement-Auto-Scaling/8.png)

#### 8. Add Tags
- Can add additional tags later when needed

#### 9. Review and Launch
- Review all configured settings
- Click **Create Auto Scaling group**
- Wait for ASG to be created and instances to launch
![target-tracking](/images/8-Implement-Auto-Scaling/9.png)

{{% notice tip %}}
After launch, verify:
- Instances created according to desired capacity
- Instances registered with Target Group
- Health check status OK
{{% /notice %}}

### Verify Auto Scaling
#### Check Instances Created from Desired Capacity
Access EC2 Instances to see 2 Instances being initialized with the name Webserver (Tag name set in Launch Template).
![target-tracking](/images/8-Implement-Auto-Scaling/10.png)

#### Access Website on ALB
- Copy ALB DNS name from EC2 Console > Load Balancers
![target-tracking](/images/8-Implement-Auto-Scaling/11.png)
- Paste into browser to access website
- Verify website works normally
![target-tracking](/images/8-Implement-Auto-Scaling/12.png)

#### Health Check Status OK
**Check Target Group Health Status**
- Go to EC2 Console > Target Groups
- Select created Target Group
- Targets tab shows instance health status
- Verify status is "healthy" (green)
![target-tracking](/images/8-Implement-Auto-Scaling/13.png)

{{% notice tip %}}
If instances show "unhealthy", check:
- Security group rules
- Health check settings in Target Group
- Application logs on instances
{{% /notice %}}

#### Auto Scaling configuration is now complete, let's test performance and understand application flow in the following sections.
### Complete!