# Design and set up a Virtual Private Cloud (VPC) with both public and private subnets
## Table of content
- VPC creation
- Subneting 
- Internet Gateway Configuration
- Route Tables
- NAT Gateway configuration
- Deploying Instances  (EC2)

We will begin with creating a Virtual Private Cloud. A VPC (Virtual Private Cloud) is a private network within a cloud provider, such as AWS (Amazon Web Services). To create a VPC in AWS.
 - Head over to your AWS console and search for VPC give you VPC a name and the and the IPV4 CIDR block number. (CIDR (Classless Inter-Domain Routing) is a method used to allocate IP addresses and route IP packets). Using 10.0.0.0/16  this will determine the number of IP addresses that can be can be used within our VPC. Below is a screenshot showing how to create a VPC
![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/0f1e6ad2-5345-4feb-92a4-f5c316dfed9c)

## subnetting 
- Subnetting is a technique used in networking to divide a larger network into smaller, more manageable subnetworks or subnets. It helps improve network performance and security by organizing and segmenting the IP address space into smaller, more efficient subnets.
-We will create to subnets one for our public resources and the other for private resources. To create a subnet, click on subnet and create the subnet named Public-subnet and assign the CIDR attach the VPC you created previusly to the subnet.  Create two subnets one for Private subnet and the other Public-subnet. Below is a screen shot of subnets created 
![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/775dfb4f-8285-4891-b23d-68c0b874bd8c)

## Conguring Internet Gateway (IGW)
An Internet Gateway (IGW) is a horizontally scaled, redundant, and highly available component of a Virtual Private Cloud (VPC) in cloud computing environments, particularly in Amazon Web Services (AWS). It allows communication between instances in your VPC and the internet.
Below is a acsreenshot of an internet gateway created and it has been attched to the KCVPC VPC created

![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/04c3e9cd-d276-447a-89ce-74a38193949b)

## Route Tables
A route table is a key component in network routing that contains a set of rules, called routes, which determine how packets of data should be directed within a network. In the context of cloud environments, such as Amazon Web Services (AWS), a route table is used within a Virtual Private Cloud (VPC) to control the traffic routing for subnets within the VPC. We will create two route tables one will handle routing from  public internet which will be named PublicRouteTable
while the other will be for resources with our VPC this will be named PrivateRouteTable.
Below is a screenshot of two route tables and they are being associated to our subnets


Create the Public Route Table:
Open the AWS Management Console.
Navigate to the VPC Dashboard.
Select "Route Tables" from the left-hand menu.
Click "Create route table."
Name it "PublicRouteTable."
Select the VPC where you want to create the route table.
Click "Create."
Associate Public Subnet with the Public Route Table:

Select the newly created "PublicRouteTable."
Click on the "Subnet associations" tab.
Click "Edit subnet associations."
Select the public subnet (e.g., PublicSubnet) and click "Save."
Add a Route to the Internet Gateway (IGW):

Select the "PublicRouteTable."
Click on the "Routes" tab.
Click "Edit routes."
Click "Add route."
For the destination, enter 0.0.0.0/0.
For the target, select the IGW (e.g., igw-12345678).
Click "Save routes."
2. Create and Configure the Private Route Table
Create the Private Route Table:

Navigate back to the "Route Tables" section in the VPC Dashboard.
Click "Create route table."
Name it "PrivateRouteTable."
Select the VPC where you want to create the route table.
Click "Create."
Associate Private Subnet with the Private Route Table:

Select the newly created "PrivateRouteTable."
Click on the "Subnet associations" tab.
Click "Edit subnet associations."
Select the private subnet (e.g., PrivateSubnet) and click "Save."
![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/3c3d67bd-5521-41e1-990e-d9e7b812dc69)


 ## NAT Gateway
A NAT Gateway (Network Address Translation Gateway) in AWS is a service that enables instances in a private subnet to connect to the internet or other AWS services, while preventing the internet from initiating connections to those instances. This is crucial for maintaining the security of instances that do not need to accept inbound traffic from the internet but still require outbound internet connectivity for updates or external communications.
 Create a NAT Gateway
In the VPC Dashboard, click on "NAT Gateways" in the left-hand menu.
Click the "Create NAT Gateway" button.
Select the Public Subnet:
In the "Subnet" dropdown, select your PublicSubnet.
Allocate an Elastic IP:
In the "Elastic IP allocation ID" section, click the "Allocate Elastic IP" button.
Confirm the allocation by clicking "Allocate" in the popup window.
Select the newly allocated Elastic IP from the dropdown.

Click the "Create NAT Gateway" button.
Wait for the NAT Gateway to become available. You can check the status on the NAT Gateways page.
Update the Private Route Table:

In the VPC Dashboard, click on "Route Tables" in the left-hand menu.
Select the PrivateRouteTable.
Click on the "Routes" tab.
Click "Edit routes" and then "Add route."
Set the destination to 0.0.0.0/0.
Set the target to the NAT Gateway ID (e.g., nat-12345678).
Click "Save routes."
Below is a NAT gateway created and it is associated to the public subnet
![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/ee889d9a-2e9f-42f3-b692-4d3071333eac)

## Security Group
A Security Group in AWS acts as a virtual firewall for your EC2 instances to control inbound and outbound traffic. Security Groups are stateful, meaning that if you allow an incoming request from a particular IP or port, the response is automatically allowed. You can attach one or more security groups to an instance when you launch it or at any time after it's running.

In the EC2 Dashboard, select "Security Groups" from the left-hand menu.
Click "Create security group."
Configure the Security Group:
Security group name: Enter a name, e.g., PublicInstancesSG.
Description: Enter a description, e.g., Security group for public instances.
VPC: Select the VPC where your public instances are located.
Inbound rules: Click "Add rule" and configure the following:
Type: HTTP
Protocol: TCP
Port range: 80
Source: Anywhere (0.0.0.0/0)
Type: HTTPS
Protocol: TCP
Port range: 443
Source: Anywhere (0.0.0.0/0)
Type: SSH
Protocol: TCP
Port range: 22
Source: Custom (enter your specific IP from WhatIsMyIP)
Outbound rules: Allow all outbound traffic (default setting):
Type: All traffic
Protocol: All
Port range: All
Destination: Anywhere (0.0.0.0/0)
Click "Create security group."
2. Create a Security Group for Private Instances (e.g., Database Servers)
Create a Security Group:

In the EC2 Dashboard, select "Security Groups" from the left-hand menu.
Click "Create security group."
Configure the Security Group:

Security group name: Enter a name, e.g., PrivateInstancesSG.
Description: Enter a description, e.g., Security group for private instances.
VPC: Select the VPC where your private instances are located.
Inbound rules: Click "Add rule" and configure the following:
Type: MySQL/Aurora
Protocol: TCP
Port range: 3306
Source: Custom (enter the CIDR block of your PublicSubnet, e.g., 10.0.1.0/24)
Outbound rules: Allow all outbound traffic (default setting):
Type: All traffic
Protocol: All
Port range: All
Destination: Anywhere (0.0.0.0/0)
Click "Create security group."

Here is a screenshot of security groups one nameed public-secirity-group and the other private-security-group which ill be used when creating our EC2 instances
![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/ca4abb1e-d2c6-4d03-bfab-5aef54be1565)


## NACL Configuration
Network Access Control Lists (NACLs) in AWS provide an additional layer of security for your VPC by controlling traffic to and from one or more subnets. NACLs operate at the subnet level and are stateless, meaning that you must specify both inbound and outbound rules separately.
Create or Select the NACL:
In the VPC Dashboard, select "Network ACLs" from the left-hand menu.
You can either create a new NACL or use an existing one. Click "Create network ACL" if creating a new one.
Configure the NACL:

Name tag: Enter a name, e.g., PublicSubnetNACL.
VPC: Select the VPC where your public subnet is located.
Click "Create."
Associate NACL with Public Subnet:

Select the newly created PublicSubnetNACL.
Click on the "Subnet associations" tab.
Click "Edit subnet associations."
Select the public subnet (e.g., PublicSubnet) and click "Save."
Configure Inbound Rules:

Select the PublicSubnetNACL.
Click on the "Inbound rules" tab.
Click "Edit inbound rules" and then "Add rule."
Rule #100: Allow HTTP
Type: HTTP
Protocol: TCP
Port range: 80
Source: 0.0.0.0/0
Allow/Deny: Allow
Rule #110: Allow HTTPS
Type: HTTPS
Protocol: TCP
Port range: 443
Source: 0.0.0.0/0
Allow/Deny: Allow
Rule #120: Allow SSH
Type: SSH
Protocol: TCP
Port range: 22
Source: Your specific IP (e.g., your local IP)
Allow/Deny: Allow
Click "Save changes."
Configure Outbound Rules:

Click on the "Outbound rules" tab.
Click "Edit outbound rules" and then "Add rule."
Rule #100: Allow All Outbound Traffic
Type: All traffic
Protocol: All
Port range: All
Destination: 0.0.0.0/0
Allow/Deny: Allow
Click "Save changes."
2. Configure NACL for Private Subnet
Create or Select the NACL:

In the VPC Dashboard, select "Network ACLs" from the left-hand menu.
You can either create a new NACL or use an existing one. Click "Create network ACL" if creating a new one.
Configure the NACL:

Name tag: Enter a name, e.g., PrivateSubnetNACL.
VPC: Select the VPC where your private subnet is located.
Click "Create."
Associate NACL with Private Subnet:

Select the newly created PrivateSubnetNACL.
Click on the "Subnet associations" tab.
Click "Edit subnet associations."
Select the private subnet (e.g., PrivateSubnet) and click "Save."
Configure Inbound Rules:

Select the PrivateSubnetNACL.
Click on the "Inbound rules" tab.
Click "Edit inbound rules" and then "Add rule."
Rule #100: Allow Traffic from Public Subnet
Type: Custom TCP Rule
Protocol: TCP
Port range: Specific ports you need (e.g., 3306 for MySQL)
Source: CIDR block of the public subnet (e.g., 10.0.1.0/24)
Allow/Deny: Allow
Click "Save changes."
Configure Outbound Rules:

Click on the "Outbound rules" tab.
Click "Edit outbound rules" and then "Add rule."
Rule #100: Allow All Outbound Traffic
Type: All traffic
Protocol: All
Port range: All
Destination: CIDR block of the public subnet (e.g., 10.0.1.0/24)
Allow/Deny: Allow
Click "Save changes."

![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/e78e1811-0e0f-45db-97b7-ace07948930b)

![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/8081a17b-0e9b-46b9-83ad-23fc5f93da26)


## Configuring EC2 Instance 
Navigate to the EC2 Dashboard by selecting "Services" -> "EC2".
Launch an Instance:

Click "Launch Instance."
Name and Tags: Enter a name for your instance (e.g., PublicInstance).
Application and OS Images (Amazon Machine Image): Choose an AMI (e.g., Amazon Linux 2 AMI).
Instance type: Choose an instance type (e.g., t2.micro for free tier).
Key pair (login): Select an existing key pair or create a new one to SSH into the instance.
Network settings:
VPC: Select the VPC where your public subnet is located.
Subnet: Select the public subnet.
Auto-assign Public IP: Ensure this is enabled so that the instance gets a public IP address.
Firewall (security groups): Select "Select existing security group" and choose the public security group (PublicInstancesSG) you created earlier.
Configure Storage: Leave default settings or adjust as needed.
Review and Launch:

Click "Review and Launch."
Verify the configuration details.
Click "Launch."
Verify Access:

Once the instance is running, select the instance from the EC2 Dashboard.
Note the public IP address.
Use an SSH client (e.g., PuTTY or Terminal) to connect to the instance using the key pair.

Launch an Instance:

Click "Launch Instance" again.
Configure Instance Details:

Name and Tags: Enter a name for your instance (e.g., PrivateInstance).
Application and OS Images (Amazon Machine Image): Choose the same AMI (e.g., Amazon Linux 2 AMI).
Instance type: Choose an instance type (e.g., t2.micro for free tier).
Key pair (login): Select the same key pair used for the public instance.
Network settings:
VPC: Select the VPC where your private subnet is located.
Subnet: Select the private subnet.
Auto-assign Public IP: Ensure this is disabled so that the instance does not get a public IP address.
Firewall (security groups): Select "Select existing security group" and choose the private security group (PrivateInstancesSG) you created earlier.
Configure Storage: Leave default settings or adjust as needed.
Review and Launch:

Click "Review and Launch."
Verify the configuration details.
Click "Launch."
Verify Connectivity:

Once the instance is running, you will not be able to SSH directly into the private instance from the internet.
SSH into the public instance first.
From the public instance, SSH into the private instance using its private IP address.

![image](https://github.com/chile4coding/DEVOPS-AWS/assets/93575252/7ba1cd61-cf8b-45a1-95ee-bf04c8f5c4b6)

A diagram of the VPC architecture, showing the VPC, subnets, route tables, and security configurations.

![image](https://github.com/user-attachments/assets/7eee9b42-580c-4ae3-bb6a-7aa283b05243)





