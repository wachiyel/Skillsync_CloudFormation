# Skillsync_CloudFormation
CloudFormation template to provision a complete AWS infrastructure.

This CloudFormation template automates the creation of a highly available infrastructure on AWS, which includes a Virtual Private Cloud (VPC), Auto Scaling Group, S3 Bucket, and Application Load Balancer (ALB). Below are the detailed steps and explanations of what the template does.

## Table of Contents
- [Architecture Overview](#architecture-overview)
- [Resources Created](#resources-created)
  - [VPC](#vpc)
  - [Subnets](#subnets)
  - [Internet Gateway](#internet-gateway)
  - [Security Groups](#security-groups)
  - [Launch Template](#launch-template)
  - [Auto Scaling Group](#auto-scaling-group)
  - [Application Load Balancer](#application-load-balancer)
  - [S3 Bucket](#s3-bucket)
- [Outputs](#outputs)
- [Deployment Instructions](#deployment-instructions)

## Architecture Overview

This template sets up a basic web application environment. The architecture includes:
- A VPC with two public subnets in different Availability Zones.
- An Internet Gateway attached to the VPC for internet connectivity.
- Security Groups for controlling access to the EC2 instances and the ALB.
- An Auto Scaling Group that ensures the EC2 instances are scaled based on load.
- An Application Load Balancer that distributes incoming traffic across the EC2 instances.
- An S3 bucket for storing assets or logs.

## Resources Created

### VPC

- **VPC**: A Virtual Private Cloud is created with a CIDR block of `10.0.0.0/16`.
- **Internet Gateway**: An Internet Gateway is attached to the VPC to allow internet access.

### Subnets

- **Public Subnet 1**: A public subnet (`10.0.1.0/24`) in the first Availability Zone.
- **Public Subnet 2**: A public subnet (`10.0.2.0/24`) in the second Availability Zone.
- **Route Table**: A public route table is created and associated with both public subnets. It has a route to direct internet traffic through the Internet Gateway.

### Internet Gateway

- **Internet Gateway**: An Internet Gateway is attached to the VPC, allowing instances within the public subnets to access the internet.

### Security Groups

- **RDP Security Group**: Allows Remote Desktop Protocol (RDP) access (port 3389) from any IP address (`0.0.0.0/0`).
- **ALB Security Group**: Allows HTTP access (port 80) from any IP address (`0.0.0.0/0`).

### Launch Template

- **Launch Template**: Defines the configuration for the EC2 instances, including the AMI (Microsoft Windows Server 2022), instance type (`t3.micro`), security groups, and user data script. The user data script installs IIS (Internet Information Services) on the instances and creates a simple HTML page.

### Auto Scaling Group

- **Auto Scaling Group**: Manages the EC2 instances across the public subnets, ensuring there are always 2 instances running, with the ability to scale up to 3 and down to 1 based on CPU utilization.
- **Scaling Policies**: Two scaling policies are created:
  - **Scale Out**: Adds instances when average CPU utilization exceeds 70%.
  - **Scale In**: Removes instances when average CPU utilization drops below 30%.

### Application Load Balancer

- **ALB**: An Application Load Balancer distributes incoming traffic across the EC2 instances in the Auto Scaling Group.
- **Listener**: The ALB listens on port 80 (HTTP) and forwards traffic to the target group.
- **Target Group**: The ALB targets the EC2 instances managed by the Auto Scaling Group.

### S3 Bucket

- **S3 Bucket**: A simple S3 bucket is created for storing assets or logs. The bucket name is automatically generated using the AWS account ID and region.

## Outputs

The template provides the following outputs:

- **VPCId**: The ID of the created VPC.
- **SubnetId**: The IDs of the public subnets created.
- **RDPSecurityGroupId**: The ID of the security group allowing RDP access.
- **ALBSecurityGroupId**: The ID of the security group associated with the ALB.
- **LaunchTemplateId**: The ID of the Launch Template used by the Auto Scaling Group.
- **AutoScalingGroupId**: The ID of the Auto Scaling Group.
- **ALBName**: The name of the ALB.
- **ALBEndpoint**: The DNS name of the ALB.
- **TargetGroupARN**: The ARN of the Target Group associated with the ALB.
- **S3BucketName**: The name of the S3 bucket.

## Deployment Instructions

To deploy this CloudFormation stack:

1. **Sign in to AWS Console**: Log in to your AWS account.

2. **Navigate to CloudFormation**: Go to the CloudFormation service in the AWS Management Console.

3. **Create a New Stack**:
   - Click on "Create Stack" and choose "With new resources (standard)".
   - Upload the YAML template file or paste the contents directly.
   - Click "Next".

4. **Specify Stack Details**:
   - Provide a name for your stack.
   - Adjust any parameters as needed (if the template had any).

5. **Configure Stack Options**: 
   - Set any optional settings like tags, permissions, or stack policies.

6. **Review and Create**:
   - Review the settings and the template.
   - Acknowledge that CloudFormation might create IAM resources.
   - Click "Create Stack".

7. **Monitor Stack Creation**:
   - CloudFormation will begin creating the stack. You can monitor progress on the "Events" tab.
   - Once the stack creation is complete, you will see the status as `CREATE_COMPLETE`.

8. **Access the Resources**:
   - After the stack is created, you can navigate to the Outputs tab to find useful information like the ALB DNS name, VPC ID, and more.
   - Use the ALB DNS name to access the web server, which should display the message "Welcome to SkillSync week 5 project!".

## Notes

- **Cleanup**: To avoid ongoing charges, delete the stack from the CloudFormation console when it is no longer needed. This will remove all resources created by the template.

