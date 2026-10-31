# AWS--VPC-peering
# AWS VPC Peering 

Author: Mohammed Sumair Ahmed
AWS Region: US East (N. Virginia) — us-east-1
Project: AWS Networking — VPC Peering

# Project Overview

This project demonstrates how to connect two Amazon Virtual Private Clouds (VPCs) using VPC Peering. It enables private communication between EC2 instances located in different VPCs without sending traffic over the public internet.

The project uses a Bastion Host, private EC2 instances, route tables, security groups, and a VPC Peering connection.

# Architecture

1. AWS Region: US East (N. Virginia) – us-east-1
2. VPC-A: 10.10.0.0/16
3. VPC-A Public Subnet: 10.10.1.0/24
4. Bastion Host: 10.10.1.93 (Public IP: 98.81.25.69)
5. VPC-A Private Subnet: 10.10.2.0/24
6. App-A EC2: 10.10.2.153
7. VPC-B: 10.20.0.0/16
8. VPC-B Private Subnet: 10.20.1.0/24
9. App-B EC2: 10.20.1.191
10. VPC Peering: Connects VPC-A and VPC-B for private communication.
11. Internet Gateway: Attached to VPC-A.
12. Route Tables: Configured in both VPCs to route traffic through the peering connection.
13. Security Groups: Control SSH and ICMP traffic between EC2 instances.
    
## Network Configuration

| Resource | CIDR / Private IP |
| :--- | :--- |
| VPC-A | `10.10.0.0/16` |
| VPC-A Public Subnet | `10.10.1.0/24` |
| VPC-A Private Subnet | `10.10.2.0/24` |
| Bastion Host | `10.10.1.93` |
| Bastion Public IP | `98.81.25.69` |
| App-A EC2 | `10.10.2.153` |
| VPC-B | `10.20.0.0/16` |
| VPC-B Private Subnet | `10.20.1.0/24` |
| App-B EC2 | `10.20.1.191` |

# AWS Services Used

Amazon VPC

Amazon EC2

VPC Peering

Internet Gateway

Route Tables

Security Groups

SSH

# Implementation Steps

Created VPC-A and VPC-B with non-overlapping CIDR blocks.

Created public and private subnets.

Launched a Bastion Host in the public subnet of VPC-A.

Launched App-A and App-B EC2 instances in private subnets.

Attached an Internet Gateway to VPC-A.

Created a VPC Peering connection between VPC-A and VPC-B.

Configured routes in both VPCs to allow private communication.

Configured security groups to permit required SSH and ICMP traffic.

Connected to private EC2 instances using SSH.

## Route Table Configuration

### VPC-A Private Route Table

| Destination | Target |
|---|---|
| `10.20.0.0/16` | VPC Peering Connection |

### VPC-B Private Route Table

| Destination | Target |
|---|---|
| `10.10.0.0/16` | VPC Peering Connection |

# Security Configuration

Security groups are configured to allow only the required traffic.

SSH uses TCP port 22.

ICMP Echo Request is used for ping testing.

Private EC2 communication uses private IP addresses.

Access is restricted to the required source IP addresses.

# Connectivity Testing

SSH connection from App-A to App-B:

ssh -i file1.pem ec2-user@10.20.1.191

Ping from App-A to App-B:

ping -c 4 10.20.1.191

Ping from App-B to App-A:

ping -c 4 10.10.2.153

### Testing status:
SSH access from App-A to App-B was observed. Successful ICMP ping replies have not yet been confirmed.

# Key Learnings

Understanding VPC and subnet architecture.

Creating and configuring EC2 instances.

Understanding public and private subnets.

Configuring route tables.

Establishing VPC Peering.

Configuring security groups.

Connecting to EC2 instances using SSH.

Understanding private network communication.

# Conclusion

This project demonstrates private communication between two AWS VPCs using VPC Peering. It provides practical experience with AWS networking, EC2, routing, security groups, and secure access to private instances.

### Created by Mohammed Sumair Ahmed
