# Aws
Master AWS Load Balancing: Launch &amp; Load-Balance 3 EC2 NGINX Servers
Step 1: Launch EC2 Instances Using a Bash Script
Go to the AWS EC2 Console
Click on Launch Instance
Fill in the details:
Name: server-1
Amazon Machine Image (AMI): Select Ubuntu Server 22.04 LTS (HVM), SSD Volume Type
Instance type: t2.micro (Free Tier eligible)
Key pair: Create new or select existing
Network Settings:
Type: HTTP | Protocol: TCP | Port: 80 | Source: Anywhere (0.0.0.0/0)
Type: SSH | Protocol: TCP | Port: 22 | Source: My IP


Add User Data Script
In the Advanced Details section, paste the following user-data script:

#!/bin/bash
apt-get update
apt-get install nginx -y
echo "<h1>Welcome to Server $(hostname)</h1>" > /var/www/html/index.html

Similarly create three servers: server-1, server-2 and server-3 respectively



✅ Step 2: Create a Target Group and Register EC2 Instances
Once your EC2 instances are launched and NGINX is running on them (port 80), the next step is to create a Target Group and register your instances. This Target Group will later be linked with a Load Balancer.

Navigate to Target Groups
Go to the AWS Management Console
Open the EC2 Dashboard
From the left-hand sidebar, click on Target Groups under Load Balancing
Click Create target group


Configure Target Group
Fill out the target group creation form as follows:

Target type: Instances
Target group name: TargetGroup-1 (or any name you prefer)
Protocol: HTTP
Port: 80
VPC: Select the VPC where your EC2 instances are running
Health checks:
Protocol: HTTP
Path: /
Leave other settings as default unless you have specific health check requirements.

Click Next.

Register Targets (EC2 Instances)
On the Register targets page:

You’ll see a list of running EC2 instances in the selected VPC.
Select the checkboxes for the 3 NGINX EC2 instances you created.
Under Ports for the selected instances, make sure the port is set to 80.
Click Include as pending below.
Scroll down and click Create target group.


Verify Registration
After creation:

Go back to Target Groups
Select your newly created target group
Click on the Targets tab
You should see the EC2 instances with a healthy status after a few seconds (if health checks pass)


✅ Step 3: Creating an Application Load Balancer and Associating It with a Target Group
After launching EC2 instances and registering them in a Target Group, the next step is to set up a Load Balancer to distribute traffic among them.

Navigate to Load Balancers
Go to the AWS Management Console
Open the EC2 Dashboard
From the left-hand menu, click Load Balancers
Click Create Load Balancer
Select Load Balancer Type
Choose Application Load Balancer (ALB):

Suitable for HTTP/HTTPS traffic
Operates at Layer 7 (Application Layer)
Click Create under Application Load Balancer.



Configure Basic Settings
Fill in the required fields:

Name: ALB-Testing
Scheme: Internet-facing
IP address type: IPv4
Listener: HTTP on port 80


Click Next: Configure Security Settings



Register Target Group
In this step, you’ll associate the previously created Target Group with the Load Balancer.

Target group name: Select your existing target group (e.g., TargetGroup-1)
Protocol: HTTP
Port: 80
Click Next: Register Targets



Review and Create
Review all the configurations
Click Create Load Balancer
After a few moments, your load balancer will be provisioned and active.

Access Your Load Balancer
Go to EC2 → Load Balancers
Copy the DNS name of your ALB (e.g., my-alb-123456789.us-east-1.elb.amazonaws.com)
Paste it in your browser:
Note: You cannot access the instance through the ALB DNS name why? because the SG of ALB only allows to communicate within itself to one thing you can do you can add below rule to ALB SG this will work.
Type	Protocol	Port	Source
HTTP	TCP	80	0.0.0.0/0
Exposing ALB to Internet is ok, but our instance also exposed to internet and hackers can directly reach the instance by the public ip, so we need to update instances SG and allow trusted traffic through ALB.
Type	Port	Source
HTTP	80	ALB's Security Group ID ✅ (Only allow traffic from ALB)




omkar sharma

Hurray!! We are getting response from all three servers.
