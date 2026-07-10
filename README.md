<p align="center">
  <img src="assets/banner.png" alt="AWS Internship Banner" />
</p>

<div align="center">

# AWS Internship at Center of Excellence - KIET

### Hands-on AWS Projects | Cloud Security | Automation | Infrastructure as Code | Cloud Engineering

<p>
  <img src="https://img.shields.io/badge/AWS-Cloud-orange?style=for-the-badge&logo=amazonaws" />
  <img src="https://img.shields.io/badge/Python-3.x-blue?style=for-the-badge&logo=python" />
  <img src="https://img.shields.io/badge/Linux-Ubuntu-black?style=for-the-badge&logo=linux" />
</p>

<p>
  <img src="https://img.shields.io/badge/CloudFormation-IaC-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Boto3-AWS%20SDK-yellow?style=for-the-badge" />
</p>

</div>

---

# About

This repository showcases the hands-on projects and practical implementations I completed during my **AWS Internship** conducted by the **Center of Excellence (CoE), Internship and Placement Cell, in collaboration with AWS Academy at KIET Group of Institutions**.

Throughout the internship, I worked on real-world AWS services and cloud engineering concepts including secure Linux access, AWS networking, cloud automation, Infrastructure as Code (IaC), monitoring, cloud security, and production-oriented infrastructure design.

Each project in this repository includes detailed documentation, architecture diagrams, implementation steps, theory notes, screenshots, and key learning outcomes, making it a complete record of my internship journey.

---

# Table of Contents

- [About](#-about)
- [Internship Objectives](#-internship-objectives)
- [Repository Structure](#-repository-structure)
- [Projects Completed](#-projects-completed)
- [Skills Gained](#️-skills-gained)
- [Learning Journey](#-learning-journey)
- [Projects Overview](#-projects-overview)
- [Project Documentation](#-project-documentation)
- [Key Learning Outcomes](#-key-learning-outcomes)

---

# Internship Objectives

During this internship, I focused on gaining practical experience with AWS services by building and deploying cloud-based solutions instead of only studying theoretical concepts.

The primary objectives were to:

- Build a strong foundation in AWS Cloud services.
- Understand secure cloud infrastructure design.
- Learn Linux-based server management and secure remote access.
- Explore AWS networking components and traffic flow.
- Implement automation using AWS services and Python.
- Understand Infrastructure as Code (IaC) using AWS CloudFormation.
- Design scalable and production-oriented AWS architectures.
- Learn cloud security best practices.
- Gain hands-on experience with monitoring and automation.

---

# Repository Structure

```text
AWS-Internship-at-Center-of-Excellence-KIET
│
├── README.md
│
├── 01-Passwordless-SSH-Authentication
│   ├── README.md
│   ├── Architecture/
│   ├── Images/
│   ├── Theory.pdf
│   └── Commands.md
│
├── 02-AWS-Networking-with-Session-Manager
│   ├── README.md
│   ├── Architecture/
│   ├── Images/
│   ├── Theory.pdf
│   └── Commands.md
│
├── 03-Self-Healing-Infrastructure
│   ├── README.md
│   ├── Architecture/
│   ├── Images/
│   ├── Theory.pdf
│   └── Commands.md
│
├── 04-AWS-Automation-Using-Python-and-Boto3
│   ├── README.md
│   ├── Architecture/
│   ├── Images/
│   ├── Theory.pdf
│   └── Source-Code/
│
└── 05-Secure-Two-Tier-AWS-Architecture
    ├── README.md
    ├── Architecture/
    ├── Images/
    ├── CloudFormation/
    ├── Theory.pdf
    └── Source-Code/
```

---

# Projects Completed

| Project | Description | Technologies |
|----------|-------------|--------------|
| **[Project 01](01-Passwordless-SSH-Authentication)** | Passwordless SSH Authentication using SSH Public Key Authentication | SSH, SCP, Rsync, SSH Agent Forwarding, ProxyJump |
| **[Project 02](02-AWS-Networking-with-Session-Manager)** | Secure AWS networking with AWS Systems Manager Session Manager | Amazon VPC, EC2, IAM, Internet Gateway, NAT Gateway, AWS Systems Manager |
| **[Project 03](03-Self-Healing-Infrastructure)** | Automated EC2 recovery using EventBridge and Systems Manager Automation | Amazon EventBridge, Systems Manager Automation, IAM, EC2 |
| **[Project 04](04-AWS-Automation-Using-Python-and-Boto3)** | Automating AWS resources using Python and Boto3 | Python, AWS CLI, Boto3, Amazon S3, EC2, IAM |
| **[Project 05](05-Secure-Two-Tier-AWS-Architecture)** | Production-style secure AWS infrastructure deployed using CloudFormation | CloudFormation, VPC, ALB, CloudWatch, CloudTrail, EventBridge, Lambda, SNS, AWS Backup |

---

# Highlights

During this internship, I successfully:

- Implemented passwordless SSH authentication using public key cryptography.
- Designed secure AWS networking architectures with public and private subnets.
- Accessed EC2 instances securely without opening SSH (Port 22) using AWS Systems Manager Session Manager.
- Built a self-healing cloud infrastructure using Amazon EventBridge and AWS Systems Manager Automation.
- Automated AWS resource management using Python and Boto3.
- Developed a production-oriented two-tier AWS architecture using Infrastructure as Code (CloudFormation).
- Worked with monitoring, automation, security, and cloud networking concepts widely used in industry.

---

# Skills Gained

Throughout this internship, I worked with a wide range of AWS services, Linux utilities, and cloud engineering concepts.

## AWS Services

- Amazon EC2
- Amazon VPC
- AWS Systems Manager (SSM)
- Amazon EventBridge
- Amazon CloudWatch
- AWS CloudTrail
- AWS Lambda
- Amazon SNS
- AWS Backup
- AWS IAM
- Amazon S3
- NAT Gateway
- Internet Gateway
- Elastic IP
- Application Load Balancer (ALB)

---

## 💻 Linux & System Administration

- SSH Public Key Authentication
- Passwordless Authentication
- SCP (Secure Copy Protocol)
- Rsync
- SSH Agent Forwarding
- SSH ProxyJump
- Linux Terminal Commands
- Secure Remote Access

---

## DevOps & Cloud Engineering

- Infrastructure as Code (IaC)
- AWS CloudFormation
- Cloud Automation
- Event-Driven Architecture
- Self-Healing Infrastructure
- Cloud Monitoring
- Cloud Security
- Secure Infrastructure Design
- Production-Oriented Cloud Architecture

---

## Programming & Automation

- Python
- Boto3 (AWS SDK for Python)
- AWS CLI
- YAML
- Automation Scripts

---

# Learning Journey

This internship followed a progressive learning path, starting from Linux security fundamentals and gradually moving toward production-ready cloud architecture.

```text
Linux & SSH Security
        │
        ▼
Passwordless Authentication
        │
        ▼
AWS Networking
        │
        ▼
Secure EC2 Access using Systems Manager
        │
        ▼
Cloud Automation
        │
        ▼
Python + Boto3
        │
        ▼
Infrastructure as Code
        │
        ▼
Production-Ready AWS Architecture
```

Each project built upon concepts learned in the previous one, resulting in a comprehensive understanding of AWS cloud infrastructure, automation, and security.

---

# Projects Overview

## Project 01 — Passwordless SSH Authentication

Learned how SSH Public Key Authentication enables secure, passwordless access to Linux servers while reducing the risks associated with password-based authentication.

### Topics Covered

- SSH Key Pair Authentication
- Passwordless Login
- SCP
- Rsync
- SSH Agent Forwarding
- SSH ProxyJump

### Key Takeaways

- Understood SSH public-private key cryptography.
- Learned secure remote server access.
- Explored efficient file transfer techniques.
- Practiced Linux security best practices.

---

## Project 02 — AWS Networking with Session Manager

Designed and implemented a secure AWS networking environment consisting of public and private subnets while securely accessing EC2 instances using AWS Systems Manager Session Manager.

### Topics Covered

- Amazon VPC
- Public & Private Subnets
- Route Tables
- Internet Gateway
- NAT Gateway
- Elastic IP
- EC2
- IAM Roles
- AWS Systems Manager Session Manager

### Key Takeaways

- Understood AWS networking fundamentals.
- Learned traffic flow inside a VPC.
- Managed EC2 instances without opening SSH (Port 22).
- Implemented secure instance management.

---

## Project 03 — Self-Healing Infrastructure

Built an event-driven automation workflow capable of automatically recovering EC2 instances using Amazon EventBridge and AWS Systems Manager Automation.

### Topics Covered

- Amazon EventBridge
- AWS Systems Manager Automation
- IAM Roles
- EC2 State Change Events

### Key Takeaways

- Learned event-driven cloud architecture.
- Built self-healing infrastructure.
- Reduced manual operational effort.
- Improved infrastructure reliability.

---

## Project 04 — AWS Automation using Python & Boto3

Automated common AWS operations using Python and the AWS SDK (Boto3).

### Topics Covered

- AWS CLI
- Boto3
- Amazon S3
- Amazon EC2
- AWS IAM
- Inventory Reporting

### Key Takeaways

- Managed AWS resources programmatically.
- Automated repetitive cloud operations.
- Improved operational efficiency using Python.

---

## Project 05 — Secure Two-Tier AWS Architecture using CloudFormation

Designed and deployed a production-oriented AWS infrastructure entirely through Infrastructure as Code using AWS CloudFormation.

### Topics Covered

- AWS CloudFormation
- Amazon VPC
- Multi-AZ Deployment
- Internet Gateway
- NAT Gateway
- Application Load Balancer
- EC2
- CloudWatch
- CloudTrail
- EventBridge
- Lambda
- SNS
- AWS Backup
- Systems Manager

### Key Takeaways

- Designed scalable cloud infrastructure.
- Implemented Infrastructure as Code.
- Applied monitoring, security, automation, and backup strategies.
- Understood production-grade AWS architecture.

---

# Project Documentation

Each project is organized to provide a structured learning experience.

Every project folder contains:

- Detailed README
- Architecture Image
- Theory Notes
- Step-by-Step Implementation Guide
- Commands Used (where applicable)
- Screenshots
- Key Learning Outcomes

The documentation is designed to help learners understand not only *how* to implement each solution, but also *why* each AWS service or concept is used.

---

# Key Learning Outcomes

Completing this internship significantly strengthened my understanding of AWS cloud services and modern cloud engineering practices.

### Throughout this internship, I learned to:

- Design secure and scalable cloud infrastructures on AWS.
- Configure Linux servers for secure remote access using SSH Public Key Authentication.
- Build custom AWS networking environments using Amazon VPC.
- Manage EC2 instances securely using AWS Systems Manager Session Manager.
- Automate cloud operations using Amazon EventBridge and Systems Manager Automation.
- Develop self-healing infrastructure capable of responding automatically to operational events.
- Automate AWS resource management using Python, Boto3, and AWS CLI.
- Implement Infrastructure as Code (IaC) using AWS CloudFormation.
- Design production-oriented AWS architectures following cloud security best practices.
- Understand how multiple AWS services work together to build reliable, scalable, and maintainable cloud solutions.

These projects helped bridge the gap between theoretical cloud concepts and their practical implementation in real-world scenarios.

---

# Future Enhancements

This repository will continue to grow as I expand my knowledge of AWS and cloud technologies.

Some planned additions include:

- Containerization using Docker
- Kubernetes (Amazon EKS)
- CI/CD Pipelines using GitHub Actions
- Terraform for Infrastructure as Code
- AWS ECS & Fargate
- AWS Elastic Beanstalk
- Monitoring with Prometheus & Grafana
- Advanced AWS Security Services
- Serverless Applications using AWS Lambda
- Real-world DevOps projects

---

# References

- AWS Documentation
- AWS Academy Learning Resources
- AWS Skill Builder
- Linux Manual Pages
- OpenSSH Documentation

---

# Support

If you found this repository helpful or learned something new from it, consider giving it a ⭐.

Your support motivates me to continue learning, building, and sharing more cloud and DevOps projects.

---

# Author

**Aditi Narang**

---
