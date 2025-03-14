+++
title = "Configure AMI and Launch Template"
weight = 1
pre = "<b>7.1 </b>"
+++

### Create AMI (Amazon Machine Image)

#### 1. Verify EC2 Instance
```bash
# Check running application
pm2 status
pm2 list

# Verify configuration files
ls -la /home/ubuntu/nodejs-app/<YOUR_PROJECT_FOLDER>
cat /etc/systemd/system/nodejs-app.service
```
![ami](/images/7-Configure-Load-Balancer/1.png)

#### 2. Create AMI from EC2 Console

1. **Access EC2 Console**
   - Select configured instance
   - Actions → Image and templates → Create image

2. **Fill AMI Information**
```bash
# Basic settings
Image name: nodejs-app-ami-v1
Description: AMI for Node.js application with PM2 and configured environment

# Advanced settings
No reboot: ✓ (Don't restart instance when creating AMI)
```
![create-ami](/images/7-Configure-Load-Balancer/2.png)

#### 3. Verify AMI Status

1. **Check AMI status**
   - EC2 Console → Images → AMIs
   - Wait for status to change from "pending" to "available"
   - Then name the AMI `nodejs-app-ami-v1`
![verify-ami](/images/7-Configure-Load-Balancer/3.png)

{{% notice warning %}}
**Important Notes:**
1. Ensure application is running stably before creating AMI
{{% /notice %}}

### Create Launch Template

#### 1. Configure Launch Template
1. EC2 Console > Launch Templates > Create launch template
![launch-template](/images/7-Configure-Load-Balancer/4.png)
2. Basic information:
   - Name: `nodejs-app-template-v1`
   - Description: `Launch template for Node.js application`
   - Auto Scaling guidance: ✓ Check
![launch-template](/images/7-Configure-Load-Balancer/5.png)

#### 2. Detailed Configuration
1. **Application and OS Images**:
   - My AMIs: Select created AMI (`nodejs-app-ami-v1`)

2. **Instance type**:
   - t2.medium
![launch-template](/images/7-Configure-Load-Balancer/6.png)

3. **Key pair**:
   - Select created key pair

4. **Network settings**:
   - Subnet: Select Don't include in launch template
   - FireWall: Select existing security group
   - Common security groups: Select **public-ec2-sg** created in Security Group

![launch-template](/images/7-Configure-Load-Balancer/7.png)

5. **Advanced details**:
   - User data:
```bash
#!/bin/bash
# Reload systemd to receive new config
sudo systemctl daemon-reload

# Enable service to auto-start on boot
sudo systemctl enable nodejs-app

# Start service
sudo systemctl start nodejs-app

# Check status
sudo systemctl status nodejs-app
```
![launch-template](/images/7-Configure-Load-Balancer/8.png)

{{% notice warning %}}
**Carefully check these points:**
1. AMI must be completed before creating template
2. Security group must have correct inbound rules (ports 9000, 22)
3. IAM role must have necessary permissions
4. User data script must be accurate
{{% /notice %}}

#### 3. Test Launch Template
1. Select created template
2. Actions > Launch instance from template
3. Verify:
   - Instance launches successfully
   - Node.js application runs correctly
   - Can access port 9000
4. Terminate test instance after verification

#### References
1. [Creating an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)
2. [Launch Templates](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-launch-templates.html)
3. [User Data Scripts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)

### Complete!