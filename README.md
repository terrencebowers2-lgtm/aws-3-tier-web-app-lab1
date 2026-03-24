# aws-3-tier-web-app-lab1
AWS 3-Tier Web App - EC2, ALB, ASG, RDS Multi-AZ

# March 19th - 20th, 2026 I created this lab to help me understand and build something to teach me the building blocks in the Cloud.

Here is step by step on how I built the 3 Tier Web App

#Tier 1 - Presentation layer
- Created a Launch Template instead of a EC2 instance, so that if a instance is unhealthy it does not hace to wait on me to recreate it
  - AMI: Amazon Linux 2023
  - Instance type: t3.micro
  - User Data script to auto-install Apache on boot
- After that was created I added a ALB
  - Internet-facing across two AZs. us-east-1a and us-east-1b
  - Sends traffic across healthy EC2 instances
- Last a Target group was made
  - Did a health check on port 80 every 30 seconds
  - Automatically register ASG instances
 
#Tier 2 - Application Layer
- Added a ASG to the lab so that it automatically add or remove EC2s based off traffic
  - Desired: 2,Min: 1, and Max: 4
  - Launches and terminates EC2 instances automatically
  - Scale-out policy: CPU > 70%, add a EC2 instance
  - Scale-in policy: CPU <= 30%, remove a EC2 instance
 
#Tier 3 - Database Layer
- Next added a RDS MySQL database
  - Single-AZ (free tier limitatiion). Would use Multi-AZ for high avaliability
  - only accessible from EC2 instances inside VPC

#Monitoring
- CloudWatch high-CPU alarm**: triggers when CPU exceeds 70%
- CloudWatch low-CPU alarm**: triggers when CPU drops below 30%
- SNS email notifications**: alerts sent when alarms fire

#Verification
- Confirmed app live via ALB DNS name
- "Hello from ip-172-31-94-88.ec2.internal" response confirmed
- Refreshing showed different hostnames proving load balancing works

  # Region
  us-east-1a
  us-east-1b

#Key Lessons Learned
- Launch Template must be created before the ASG
- Never manually register instances in the Target Group
- Always delete ASG first during cleanup or it relaunches instances
- Remove "yum update" from User Data in restricted subnets

  #Date completed
  March 21, 2026
