# AWS Infrastructure Setup for Capstone Project

## 1. VPC Setup

1.1. Create a new VPC  
   - Name: `PhotoSharingVPC`  
   - IPv4 CIDR block: `10.0.0.0/16`

1.2. Create 6 subnets across 2 Availability Zones (AZs)  
   - **Public Subnet 1**: `10.0.1.0/24` (AZ1) - For ALB and NAT Gateway  
   - **Public Subnet 2**: `10.0.2.0/24` (AZ2) - For ALB and NAT Gateway  
   - **Private Subnet 1 (Web Tier)**: `10.0.3.0/24` (AZ1)  
   - **Private Subnet 2 (Web Tier)**: `10.0.4.0/24` (AZ2)  
   - **Private Subnet 3 (App Tier)**: `10.0.5.0/24` (AZ1)  
   - **Private Subnet 4 (App Tier)**: `10.0.6.0/24` (AZ2)

1.3. Create an Internet Gateway and attach it to the VPC

1.4. Create 2 NAT Gateways (one in each public subnet)

1.5. Create route tables:  
   - **Public Route Table**: Add route to Internet Gateway  
   - **Private Route Table 1**: Add route to NAT Gateway 1  
   - **Private Route Table 2**: Add route to NAT Gateway 2

1.6. Associate subnets with route tables

## 2. Security Groups

2.1. Create **Bastion Security Group** (Bastion-SG)  
   - Inbound: Allow SSH (22) from your IP address  
   - Outbound: Allow all

2.2. Create **Internet facing ALB Security Group** (ALB-SG)  
   - Inbound: Allow HTTP (80) and HTTPS (443) from anywhere  
   - Outbound: Allow all

2.3. Create **Web Tier Security Group** (Web-SG)  
   - Inbound: Allow HTTP (80) from ALB-SG  
   - Inbound: Allow SSH from Bastion-SG
   - Outbound: Send HTTP (80) to ALB-INTERNAL-SG

2.4. Create **Internal ALB Security Group** (ALB-INTERNAL-SG)  
   - Inbound: Allow HTTP (80) from Web-SG
   - Outbound: Send all traffic to App-SG   

2.5. Create **App Tier Security Group** (App-SG)  
   - Inbound: Allow SSH from Web-SG
   - Inbound: Allow SSH from Bastion-SG  
   - Outbound: Allow all

2.6. Create **RDS Security Group** (RDS-SG)  
   - Inbound: Allow Aurora/MySQL (3306) from App-SG
   - Inbound: Allow Aurora/MySQL (3306) from Bastion-SG
   - Outbound: Allow all