# AWS Networking with Session Manager

> **AWS Internship — Center of Excellence, KIET**

## Table of Contents

- [Overview](#overview)
- [Objective](#objective)
- [Architecture](#architecture)
  - [Network Addressing](#network-addressing)
  - [Architecture Diagram](#architecture-diagram)
  - [Network Layout](#network-layout)
- [Access Methods](#access-methods)
  - [Method 1 — Bastion Host](#method-1--bastion-host)
  - [Method 2 — Session Manager with NAT Gateway](#method-2--session-manager-with-nat-gateway)
  - [Method 3 — Session Manager with VPC Endpoints](#method-3--session-manager-with-vpc-endpoints)
- [AWS Environment](#aws-environment)
- [Key Components](#key-components)
- [Implementation Overview](#implementation-overview)
- [Security](#security)
- [Applications](#applications)
- [Verification](#verification)
- [Documentation](#documentation)
- [Result](#result)
- [Project Information](#project-information)
- [Author](#author)

---

## Overview

This project demonstrates how to build a custom **AWS Virtual Private Cloud (VPC)** with public and private subnets and securely access a private EC2 instance using different connectivity approaches.

The same private EC2 instance is used to demonstrate three different methods of access:

1. **Bastion Host** using SSH.
2. **AWS Systems Manager Session Manager** using NAT Gateway connectivity.
3. **AWS Systems Manager Session Manager** using Interface VPC Endpoints without requiring Internet/NAT connectivity for SSM traffic.

The project provides practical understanding of how **VPC networking, subnets, route tables, Internet Gateway, NAT Gateway, Security Groups, IAM, Systems Manager, and VPC Endpoints** work together to provide controlled access to private AWS resources.

---

## Objective

The main objectives of this project are to:

- Build a custom VPC with public and private subnets.
- Understand public and private subnet routing.
- Configure an Internet Gateway.
- Configure a NAT Gateway for private subnet outbound connectivity.
- Configure public and private route tables.
- Launch public and private EC2 instances.
- Configure a Bastion Host for SSH-based access.
- Configure AWS Systems Manager Session Manager.
- Access a private EC2 instance without assigning it a public IP.
- Configure Interface VPC Endpoints for private Systems Manager connectivity.
- Compare different approaches for accessing private EC2 resources.
- Understand the security implications of each access method.

---

## Architecture

The lab is built using a single VPC containing one public subnet and one private subnet.

The public subnet contains the **Bastion Host** and **NAT Gateway**, while the private subnet contains the **private EC2 instance**.

The architecture supports three access paths:

```text
1. Bastion Host
   Local Machine → Bastion Host → Private EC2

2. Session Manager with NAT Gateway
   Private EC2 → NAT Gateway → Internet Gateway → Systems Manager

3. Session Manager with VPC Endpoints
   Private EC2 → Interface VPC Endpoints → Systems Manager
````

### Network Addressing

| Resource                | Configuration               | Purpose                                                       |
| ----------------------- | --------------------------- | ------------------------------------------------------------- |
| **VPC**                 | `10.0.0.0/16`               | Main isolated AWS network                                     |
| **Public Subnet**       | `10.0.1.0/24`               | Bastion Host and NAT Gateway                                  |
| **Private Subnet**      | `10.0.2.0/24`               | Private EC2 instance                                          |
| **Public Route Table**  | `public-rt`                 | Routes public traffic through Internet Gateway                |
| **Private Route Table** | `private-rt`                | Routes private outbound traffic through NAT Gateway           |
| **Internet Gateway**    | `my-igw`                    | Provides Internet connectivity                                |
| **NAT Gateway**         | `my-natgw`                  | Provides outbound Internet connectivity for private resources |
| **Elastic IP**          | Associated with NAT Gateway | Public address used by NAT Gateway                            |

### Architecture Diagram

![AWS Networking with Session Manager Architecture](./Architecture/AWS%20Networking%20with%20Session%20Manager.png)

### Network Layout

```text
                               INTERNET
                                  │
                                  │
                           Internet Gateway
                                  │
                     ┌─────────────┴─────────────┐
                     │         my-vpc            │
                     │       10.0.0.0/16         │
                     │                           │
           ┌─────────┴─────────┐     ┌──────────┴──────────┐
           │   Public Subnet   │     │   Private Subnet    │
           │    10.0.1.0/24    │     │    10.0.2.0/24      │
           │                    │     │                     │
           │  Bastion Host      │     │   Private EC2       │
           │  NAT Gateway       │     │   No Public IP      │
           │  Elastic IP        │     │                     │
           └─────────┬──────────┘     └──────────┬──────────┘
                     │                           │
                     │                           │
              Public Route Table          Private Route Table
                     │                           │
                     │                     ┌─────┴─────┐
                     │                     │           │
                     │                 NAT Gateway   VPC
                     │                     │        Endpoints
                     │                     │           │
                     │                     └─────┬─────┘
                     │                           │
                     └───────────────┬───────────┘
                                     ▼
                            AWS Systems Manager
                                     │
                                     ▼
                               Session Manager
```

---

## Access Methods

This project demonstrates three different methods for accessing the private EC2 instance.

| Access Method                       | Access Mechanism                            | Private EC2 Public IP | NAT Required       |
| ----------------------------------- | ------------------------------------------- | --------------------- | ------------------ |
| **Bastion Host**                    | SSH through public EC2                      | No                    | No for private EC2 |
| **Session Manager + NAT**           | Systems Manager through NAT Gateway         | No                    | Yes                |
| **Session Manager + VPC Endpoints** | Systems Manager through Interface Endpoints | No                    | No for SSM traffic |

---

### Method 1 — Bastion Host

A **Bastion Host** is a public EC2 instance that acts as an intermediate SSH hop to reach a private EC2 instance.

The connection path is:

```text
Your Local Machine
        │
        │ SSH
        ▼
Bastion Host
(Public EC2)
        │
        │ SSH using Private IP
        ▼
Private EC2
```

In this configuration:

* The Bastion Host is deployed in the public subnet.
* The Bastion Host has a public IPv4 address.
* The private EC2 instance does not have a public IPv4 address.
* SSH access to the Bastion Host is restricted to the required source.
* The private EC2 Security Group allows SSH from the Bastion Host Security Group.
* The private instance is accessed using its private IP address.

This approach demonstrates traditional **SSH-based access through a controlled jump host**.

---

### Method 2 — Session Manager with NAT Gateway

The second approach uses **AWS Systems Manager Session Manager** while the private EC2 instance obtains outbound connectivity through the NAT Gateway.

The private EC2 instance does not require a public IP address.

```text
Private EC2
     │
     ▼
Private Route Table
     │
     │ 0.0.0.0/0
     ▼
NAT Gateway
     │
     ▼
Internet Gateway
     │
     ▼
AWS Systems Manager
     │
     ▼
Session Manager
```

The private route table contains:

```text
0.0.0.0/0 → NAT Gateway
```

The NAT Gateway is located in the public subnet and uses an Elastic IP.

The private EC2 instance requires an IAM instance role containing:

```text
AmazonSSMManagedInstanceCore
```

The SSM Agent initiates communication with Systems Manager. Therefore, Session Manager can provide shell access without requiring an inbound SSH connection to the private EC2 instance.

---

### Method 3 — Session Manager with VPC Endpoints

The third approach uses **Interface VPC Endpoints** to provide private connectivity between the VPC and AWS Systems Manager services.

This allows the private EC2 instance to communicate with Systems Manager without requiring Internet/NAT connectivity for SSM traffic.

```text
Private EC2
     │
     ▼
Interface VPC Endpoints
     │
     ├── SSM
     ├── SSMMessages
     └── EC2Messages
     │
     ▼
AWS Systems Manager
     │
     ▼
Session Manager
```

The typical SSM Interface Endpoints used in this setup are:

```text
com.amazonaws.<region>.ssm
com.amazonaws.<region>.ec2messages
com.amazonaws.<region>.ssmmessages
```

For the `ap-south-1` region:

```text
com.amazonaws.ap-south-1.ssm
com.amazonaws.ap-south-1.ec2messages
com.amazonaws.ap-south-1.ssmmessages
```

Private DNS is enabled for the Interface Endpoints, and the endpoint Security Group allows TCP port `443` from the private EC2 Security Group.

---

## AWS Environment

The project is implemented using the following AWS services:

* **Amazon VPC**
* **Amazon EC2**
* **Internet Gateway**
* **NAT Gateway**
* **Elastic IP**
* **Route Tables**
* **Security Groups**
* **AWS IAM**
* **AWS Systems Manager**
* **Session Manager**
* **Interface VPC Endpoints**

### EC2 Instances

| Instance                           | Subnet         | Purpose         | Public IP |
| ---------------------------------- | -------------- | --------------- | --------- |
| **Public Instance / Bastion Host** | Public Subnet  | SSH entry point | Yes       |
| **Private Instance**               | Private Subnet | Target instance | No        |

The public instance is used as the Bastion Host, while the private instance is deployed without a public IPv4 address.

---

## Key Components

| Component            | Purpose                                                            |
| -------------------- | ------------------------------------------------------------------ |
| **VPC**              | Provides the isolated virtual network                              |
| **Public Subnet**    | Hosts the Bastion Host and NAT Gateway                             |
| **Private Subnet**   | Hosts the private EC2 instance                                     |
| **Internet Gateway** | Provides a path between the VPC and Internet                       |
| **NAT Gateway**      | Allows private resources to initiate outbound Internet connections |
| **Elastic IP**       | Provides public addressing for the NAT Gateway                     |
| **Route Tables**     | Control network traffic routing                                    |
| **Bastion Host**     | Provides SSH-based access to the private EC2                       |
| **IAM Role**         | Provides required permissions for Systems Manager                  |
| **SSM Agent**        | Communicates between the EC2 instance and Systems Manager          |
| **Session Manager**  | Provides interactive shell access without inbound SSH              |
| **VPC Endpoints**    | Provide private connectivity to supported AWS services             |

---

## Implementation Overview

The implementation is divided into the following stages.

### 1. Build the Network

The network is configured by:

* Creating the VPC.
* Creating the public subnet.
* Creating the private subnet.
* Creating and attaching the Internet Gateway.
* Creating the public route table.
* Associating the public subnet with the public route table.
* Allocating an Elastic IP.
* Creating the NAT Gateway in the public subnet.
* Creating the private route table.
* Associating the private subnet with the private route table.

### 2. Launch EC2 Instances

Two EC2 instances are configured:

```text
public-instance
private-instance
```

The public instance is launched in the public subnet and acts as the Bastion Host.

The private instance is launched in the private subnet with public IPv4 assignment disabled.

### 3. Configure IAM and Systems Manager

The required IAM instance role is attached to the EC2 instance.

The role contains:

```text
AmazonSSMManagedInstanceCore
```

The SSM Agent is also required to communicate with AWS Systems Manager.

### 4. Configure VPC Endpoints

Interface VPC Endpoints are configured for Systems Manager services.

Private DNS is enabled and the endpoint Security Group allows the required HTTPS traffic.

### 5. Demonstrate the Three Access Methods

The same private EC2 instance is used to demonstrate:

```text
Method 1
Bastion Host
     │
     └── SSH → Private EC2


Method 2
Session Manager + NAT Gateway
     │
     └── Private EC2 → NAT Gateway → Systems Manager


Method 3
Session Manager + VPC Endpoints
     │
     └── Private EC2 → VPC Endpoints → Systems Manager
```

For the complete commands used during implementation:

[**View Commands.md**](./Commands.md/)

---

## Security

Security is an important part of this architecture.

### Private EC2

The private EC2 instance:

* Does not have a public IPv4 address.
* Does not require inbound SSH for Session Manager.
* Uses Security Groups to control network traffic.

### Bastion Host

SSH access to the Bastion Host should be restricted to the required source IP.

The private EC2 Security Group should allow SSH only from the Bastion Host Security Group when Bastion-based access is being demonstrated.

```text
Bastion Security Group
TCP 22 → Required Source IP

Private EC2 Security Group
TCP 22 → Bastion Security Group
```

SSH should not be opened to:

```text
0.0.0.0/0
```

### Session Manager

Session Manager provides shell access without requiring an inbound SSH listener on the private EC2 instance.

### VPC Endpoints

For Interface VPC Endpoints, the endpoint Security Group should allow:

```text
TCP 443
```

from the private EC2 Security Group.

---

## Applications

The concepts demonstrated in this project can be applied to:

* Secure administration of private EC2 instances.
* Production environments where direct SSH access is restricted.
* DevOps and cloud infrastructure management.
* Controlled access to private workloads.
* Systems administration without exposing servers publicly.
* Private connectivity to AWS services.
* Environments requiring reduced inbound network exposure.
* Secure management of infrastructure without exposing private instances to the public Internet.

---

## Verification

Each access method can be verified independently.

### Bastion Host

```text
✓ Bastion Host has a public IP
✓ SSH access is restricted
✓ Private EC2 has no public IP
✓ Private EC2 allows SSH from Bastion Security Group
✓ SSH from Bastion to private IP works
```

### Session Manager with NAT Gateway

```text
✓ Private EC2 has no public IP
✓ Private Route Table → NAT Gateway
✓ NAT Gateway is Available
✓ NAT Gateway is located in Public Subnet
✓ Public Route Table → Internet Gateway
✓ AmazonSSMManagedInstanceCore is attached
✓ SSM Agent is running
✓ Session Manager connection works
```

### Session Manager with VPC Endpoints

```text
✓ Interface VPC Endpoints exist
✓ Required SSM endpoints are configured
✓ Private DNS is enabled
✓ Endpoint Security Group allows TCP 443
✓ Private EC2 allows outbound TCP 443
✓ IAM role is attached
✓ SSM Agent is running
✓ Session Manager connection works
```

---

## Documentation

Detailed project resources are available below:

### Theory

[**Theory.pdf**](./Theory/)

Contains the detailed theoretical concepts, networking explanation, architecture, implementation process, access scenarios, troubleshooting, and final summary.

---

## Result

The project demonstrates three different ways to access a private EC2 instance within a custom AWS VPC.

```text
                         PRIVATE EC2
                              │
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
        Bastion Host      SSM + NAT       SSM + VPC
             SSH           Gateway          Endpoints
              │               │               │
              ▼               ▼               ▼
        Private Access   Private Access   Private Access
```

### Final Comparison

| Access Method                       | Main Technology | Internet/NAT                 | Inbound SSH to Private EC2 |
| ----------------------------------- | --------------- | ---------------------------- | -------------------------- |
| **Bastion Host**                    | SSH             | Required for Bastion         | Required                   |
| **Session Manager + NAT Gateway**   | Session Manager | NAT Required                 | Not Required               |
| **Session Manager + VPC Endpoints** | Session Manager | Not Required for SSM Traffic | Not Required               |

### Final Outcome

The lab successfully demonstrates:

* Public and private subnet architecture.
* Routing through an Internet Gateway.
* Outbound private-subnet connectivity through a NAT Gateway.
* SSH access through a Bastion Host.
* Session Manager access using NAT Gateway connectivity.
* Session Manager access using private VPC Endpoints.
* Secure access to a private EC2 instance without assigning it a public IP.

A key concept demonstrated by this project is that a subnet being called **private** does not itself determine its connectivity. The actual connectivity depends on the **route table, addressing, gateways, VPC endpoints, and Security Groups**.

---

## Project Information

| Field              | Details                                                                    |
| ------------------ | -------------------------------------------------------------------------- |
| **Project**        | AWS Networking with Session Manager                                        |
| **Platform**       | AWS                                                                        |
| **Technology**     | VPC, EC2, IAM, Systems Manager, NAT Gateway, VPC Endpoints                 |
| **Focus**          | Secure access to private EC2 instances                                     |
| **Access Methods** | Bastion Host, Session Manager with NAT, Session Manager with VPC Endpoints |
| **Internship**     | AWS Internship — Center of Excellence, KIET                                |

---

## Author

**Aditi Narang**

```