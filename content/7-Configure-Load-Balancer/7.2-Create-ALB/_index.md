+++
title = "Configure ALB and Target Group"
weight = 2
pre = "<b>7.2 </b>"
+++

### 1. Create Target Group

#### 1.1. Basic Target Group Configuration
1. Access EC2 Console > Target Groups > Create target group
![targetgroup](/images/7-Configure-Load-Balancer/9.png)
2. Specify group details:
   - Choose a target type: **Instances**
   - Target group name: `nodejs-app-tg`
   - Protocol: **HTTP**
   - Port: **9000**
   - VPC: Select created VPC
   - Protocol version: **HTTP1**
![targetgroup](/images/7-Configure-Load-Balancer/10.png)

#### 1.2. Health checks
1. Health check settings:
   - Protocol: **HTTP**
   - Path: `/health`
   
   **Note: Must Add Health Check Route to Project**
   
   ```javascript
   // Add to app.js or routes
   app.get('/health', (req, res) => {
   res.status(200).json({
      status: 'OK',
      timestamp: new Date(),
      uptime: process.uptime()
   });
   });
   ```
   - Port: **traffic port** (9000)
   - Healthy threshold: 2
   - Unhealthy threshold: 3
   - Timeout: 5 seconds
   - Interval: 30 seconds
   - Success codes: 200-299
![targetgroup](/images/7-Configure-Load-Balancer/11.png)

#### 1.3. Register Targets
Click "Create target group"
![targetgroup](/images/7-Configure-Load-Balancer/12.png)

### 2. Create Application Load Balancer

#### 2.1. Basic Configuration
1. Access EC2 Console > Load Balancers > Create Load Balancer
![ALB](/images/7-Configure-Load-Balancer/13.png)
2. Choose Application Load Balancer
![ALB](/images/7-Configure-Load-Balancer/14.png)
3. Basic configuration:
   - Name: `nodejs-app-alb`
   - Scheme: Internet-facing
   - IP address type: IPv4
![ALB](/images/7-Configure-Load-Balancer/15.png)

#### 2.2. Network Configuration
1. Network mapping:
   - VPC: Select created VPC
   - Mappings: Select at least 2 AZs and public subnets

2. Security Groups:
   - Select `alb-sg`
   - Verify rules:
     + HTTP (80) from 0.0.0.0/0
     + HTTPS (443) from 0.0.0.0/0
![ALB](/images/7-Configure-Load-Balancer/16.png)

#### 2.3. Listeners
1. **HTTP:80**
   - Protocol: HTTP
   - Port: 80
   - Default action: Forward to `nodejs-app-tg`
![ALB](/images/7-Configure-Load-Balancer/17.png)

#### 2.4. Review and Create
1. Review configuration
2. Click "Create load balancer"

{{% notice info %}}
**Port 9000:**
- Traffic flow: Client → ALB (80/443) → Target Group (9000) → EC2 Instance (9000)
- No need to expose port 9000 on ALB
- ALB security group only needs to open port 80
{{% /notice %}}

#### References
1. [ALB Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
2. [Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)
3. [ALB Monitoring](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-monitoring.html)

### Complete!