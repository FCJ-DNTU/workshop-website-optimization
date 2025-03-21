+++
title = "Create VPC Instance"
date = 2020-05-14T00:38:32+07:00 
weight = 1
chapter = false
pre = "<b>3.1. </b>"
+++

#### VPC Concept

In this Workshop, *Amazon Virtual Private Cloud (VPC)* is used to create a private network, allowing organization and management of resources in an isolated network space on AWS.

VPC gives you complete control over your network environment, from IP address configuration, route tables, Internet Gateway to subnets.

#### Main use cases of VPC in Workshop

- Deploy applications with **Auto Scaling Group** and **Application Load Balancer**

- Store and manage resources on **S3** through Gateway

- Ensure high availability with **Multi-AZ** architecture

#### VPC Costs

AWS does not charge for creating and using basic VPC features. However, you will be charged for:
- Data transfer between AZs or out of VPC
- Additional VPC services such as NAT Gateways, IP Address Manager, traffic mirroring, Reachability Analyzer, and Network Access Analyzer

#### Create VPC Instance
In this section we will create a **VPC Instance**, including 2 **AZ**, 2 Public Subnets

#### 1. Create VPC Instance
- Access [Your VPCs](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#vpcs:), Create VPC
- At **VPC Settings**, select **VPC and more** option

- Set **name tag** to `optimization-website` and leave other fields as default, select **Create VPC**

![create-vpc](/images/3-Prepare-VPC/1testt.png)
- Successful VPC Instance creation result
![review-result](/images/3-Prepare-VPC/2.png)

#### 2. Next we will assign Public IP4 to public subnets
- Access [Subnets](https://ap-southeast-1.console.aws.amazon.com/vpcconsole/home?region=ap-southeast-1#subnets:)
- Select **Subnet ID** of public subnet e.g. **optimization-website-subnet-public1-ap-southeast-1a**
![subnets](/images/3-Prepare-VPC/3.png)
- Select dropdown **Actions**, **Edit subnet settings**
![edit-subnet](/images/3-Prepare-VPC/4.png)
- Check **Enable auto-assign public IPv4 address**, Save
![enable-ipv4](/images/3-Prepare-VPC/5.png)
- Complete assigning Public IPv4 for public subnet **optimization-website-subnet-public1-ap-southeast-1a**
![complete](/images/3-Prepare-VPC/6.png)

### Complete!