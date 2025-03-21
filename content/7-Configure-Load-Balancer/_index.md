+++
title = "Configure Load Balancer"
date = 2020-05-14T00:38:32+07:00
weight = 7
chapter = false
pre = "<b>7. </b>"
+++

#### Introduction
In this section, we will set up a Load Balancer to distribute traffic between EC2 instances. This process is divided into two main parts:

1. [Configure AMI and Launch Template](7.1-ami-launch-template/)
   - Prepare EC2 Instance with Node.js and dependencies
   - Create AMI from configured instance
   - Set up Launch Template with necessary configurations
   - Test and verify template functionality

2. [Configure ALB and Target Group](7.2-create-alb/)
   - Create and configure Application Load Balancer
   - Set up Target Group with health checks
   - Configure listeners for ports (80, 443, 9000)
   - Test and monitor the system
3. [Implement Auto Scaling](7.3-auto-scaling/)
   - Create Auto Scaling Group using Launch Template
   - Configure scaling policies
   - Set up target tracking scaling policy based on CPU utilization
   - Link with ALB Target Group
   - Verify Auto Scaling operation
{{% notice info %}}
Before starting, ensure:
- EC2 instance is installed and properly configured with Node.js application
- Security Groups are created with appropriate rules
- VPC and Subnets are correctly configured
{{% /notice %}}

{{% notice tip %}}
**Best Practices:**
- Use multiple AZs to increase availability
- Configure appropriate health checks
- Enable logging and monitoring
- Thoroughly test before production deployment
{{% /notice %}}