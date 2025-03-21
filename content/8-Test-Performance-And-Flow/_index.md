+++
title = "Test performance and Auto Scaling workflow"
weight = 8
pre = "<b>8. </b>"
+++

### Overview

In this section, we will:
1. Test application performance through load testing
2. Monitor Auto Scaling workflow under high load

### Content

{{% notice info %}}
We will use autocannon tool to test performance and monitor how the Auto Scaling Group responds to load.
{{% /notice %}}

#### 1. Performance Testing
- Load test application with different scenarios
- Analyze results and metrics

#### 2. Auto Scaling Workflow
- Monitor scale out process when load increases
- Check health checks and instance states
- Verify Load Balancer operation

### Objectives
- Understand current application performance
- Confirm Auto Scaling works as configured
- Ensure system can automatically scale when needed

{{% notice tip %}}
Monitor CloudWatch metrics during testing to get an overview of system performance.
{{% /notice %}}