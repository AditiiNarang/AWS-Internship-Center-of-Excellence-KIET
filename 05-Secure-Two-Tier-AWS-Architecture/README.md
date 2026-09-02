# AWS Secure Two-Tier Architecture Using CloudFormation

> **AWS Internship — Center of Excellence, KIET**

## 📌 Overview

This project demonstrates the design and deployment of a **secure, scalable, and production-oriented Two-Tier AWS Architecture** using **AWS CloudFormation**.

The entire infrastructure is defined through a **CloudFormation YAML template**, following the principles of **Infrastructure as Code (IaC)**. Instead of provisioning AWS resources manually, the infrastructure can be deployed consistently and automatically from a single template.

The architecture integrates AWS networking, compute, load balancing, monitoring, auditing, security automation, backup, and secure instance management.

---

## 🎯 Objectives

The main objectives of this project are to:

* Design a secure two-tier architecture on AWS.
* Automate infrastructure deployment using CloudFormation.
* Implement public and private networking across multiple Availability Zones.
* Deploy application servers in private subnets.
* Distribute application traffic using an Application Load Balancer.
* Implement monitoring, logging, and auditing.
* Automate security event notifications.
* Configure backup for EC2 instances.
* Manage EC2 instances securely without SSH access.
* Understand production-oriented AWS architecture and Infrastructure as Code.

---

## 🏗️ Architecture

The project follows a **Two-Tier Architecture** consisting primarily of:

* **Public Tier** — Internet-facing resources such as the Application Load Balancer.
* **Private Tier** — Application EC2 instances that are not directly accessible from the internet.

### Architecture Flow

```text
                         Internet
                            │
                            ▼
                 ┌─────────────────────┐
                 │   Internet Gateway   │
                 └──────────┬──────────┘
                            │
                            ▼
              ┌───────────────────────────┐
              │   Public Subnets          │
              │                           │
              │  Application Load        │
              │      Balancer             │
              └────────────┬──────────────┘
                           │
                           │ Application Traffic
                           ▼
              ┌───────────────────────────┐
              │   Private Subnets         │
              │                           │
              │   EC2 Application        │
              │      Servers              │
              └────────────┬──────────────┘
                           │
                           ▼
                     NAT Gateway
                           │
                           ▼
                       Internet
```

The infrastructure is deployed across **multiple Availability Zones** to improve availability and resilience.

---

## ☁️ AWS Services Used

| AWS Service                   | Purpose                                         |
| ----------------------------- | ----------------------------------------------- |
| **AWS CloudFormation**        | Infrastructure as Code and automated deployment |
| **Amazon VPC**                | Custom network architecture                     |
| **Public Subnets**            | Host internet-facing resources                  |
| **Private Subnets**           | Host application servers securely               |
| **Internet Gateway**          | Internet connectivity for public resources      |
| **NAT Gateway**               | Outbound internet access for private resources  |
| **Application Load Balancer** | Distributes incoming application traffic        |
| **Amazon EC2**                | Hosts application servers                       |
| **IAM**                       | Access control and instance permissions         |
| **Amazon CloudWatch**         | Monitoring, logs, metrics, and alarms           |
| **AWS CloudTrail**            | API activity auditing                           |
| **Amazon EventBridge**        | Event-driven automation                         |
| **AWS Lambda**                | Executes automated responses                    |
| **Amazon SNS**                | Sends security event notifications              |
| **AWS Backup**                | Scheduled EC2 backups                           |
| **AWS Systems Manager**       | Secure EC2 management without SSH               |

---

## 🧱 Infrastructure Components

### 1. AWS CloudFormation

A comprehensive **YAML template** is used to define and deploy the AWS infrastructure.

CloudFormation enables:

* Automated provisioning
* Repeatable deployments
* Infrastructure consistency
* Infrastructure as Code
* Easier resource management

---

### 2. Amazon VPC

A custom VPC is created to provide an isolated networking environment.

The VPC includes:

* Public subnets
* Private subnets
* Multiple Availability Zones
* Route tables
* Internet Gateway
* NAT Gateway

---

### 3. Public and Private Subnets

The architecture separates resources based on their accessibility requirements.

**Public Subnets**

Used for internet-facing resources such as the Application Load Balancer.

**Private Subnets**

Used for application EC2 instances so that they are not directly exposed to the internet.

---

### 4. Internet Gateway and NAT Gateway

The **Internet Gateway** provides internet connectivity to resources in public subnets.

The **NAT Gateway** allows resources in private subnets to initiate outbound internet connections without making those resources directly accessible from the internet.

---

### 5. Application Load Balancer

The **Application Load Balancer (ALB)** acts as the entry point for application traffic.

It:

* Receives incoming requests.
* Distributes traffic across application servers.
* Improves availability.
* Prevents direct internet access to private EC2 instances.

---

### 6. Amazon EC2

EC2 instances are deployed as application servers within the private subnets.

The instances are configured with appropriate:

* Security Groups
* IAM Roles
* Networking
* Application configuration

---

## 🔐 Security and Access

Security is implemented through multiple AWS services and architectural controls.

### Security Groups

Security Groups control network traffic to and from AWS resources.

The architecture restricts direct access to the private EC2 instances and allows application traffic through the appropriate path.

### IAM Roles

IAM roles provide AWS permissions to EC2 instances and AWS services without requiring hard-coded credentials.

### AWS Systems Manager

Systems Manager enables secure instance management without requiring traditional SSH access.

---

## 📊 Monitoring and Auditing

### Amazon CloudWatch

CloudWatch is used for:

* Monitoring metrics
* Collecting logs
* Creating alarms
* Observing resource activity

### AWS CloudTrail

CloudTrail provides auditing of API activities across the AWS environment.

This helps track:

* AWS API calls
* Resource activity
* Account activity
* Security-related events

---

## 🚨 Security Event Automation

The architecture uses an event-driven security notification workflow:

```text
AWS Event
    │
    ▼
Amazon EventBridge
    │
    ▼
AWS Lambda
    │
    ▼
Amazon SNS
    │
    ▼
Security Notification
```

Amazon EventBridge detects relevant events and triggers AWS Lambda, which processes the event and sends notifications through Amazon SNS.

---

## 💾 Backup

**AWS Backup** is configured to provide scheduled backups for EC2 instances.

This helps improve:

* Data protection
* Recovery capability
* Operational resilience

---

## ⚙️ Infrastructure as Code Workflow

The overall deployment process follows:

```text
CloudFormation YAML Template
            │
            ▼
      CloudFormation
            │
            ▼
   AWS Resource Provisioning
            │
            ▼
      Network + Security
            │
            ▼
    Load Balancer + EC2
            │
            ▼
 Monitoring + Auditing
            │
            ▼
 Automation + Backup
```

---

## ▶️ Deployment

The infrastructure is deployed using an AWS CloudFormation YAML template.

### Create a CloudFormation Stack

```bash
aws cloudformation create-stack \
    --stack-name <stack-name> \
    --template-body file://<template-file>.yaml
```

### Check Stack Status

```bash
aws cloudformation describe-stacks \
    --stack-name <stack-name>
```

### List Stack Resources

```bash
aws cloudformation list-stack-resources \
    --stack-name <stack-name>
```

### Delete the Stack

When the infrastructure is no longer required:

```bash
aws cloudformation delete-stack \
    --stack-name <stack-name>
```

---

## 🧪 Verification

After deployment, the following components can be verified:

* CloudFormation stack status
* VPC and subnet configuration
* Route tables
* Internet Gateway
* NAT Gateway
* Application Load Balancer
* EC2 instances
* Security Groups
* IAM Roles
* CloudWatch monitoring
* CloudTrail activity
* EventBridge rules
* Lambda functions
* SNS notifications
* AWS Backup configuration
* Systems Manager connectivity

---

## 💡 Key Learnings

Through this project, I gained hands-on experience with:

* AWS Infrastructure as Code using CloudFormation.
* Writing reusable CloudFormation YAML templates.
* Designing secure VPC architectures.
* Public and private subnet configuration.
* Multi-AZ infrastructure design.
* Application Load Balancer configuration.
* EC2 deployment and security.
* CloudWatch monitoring and alarms.
* CloudTrail auditing.
* Event-driven automation using EventBridge and Lambda.
* Security notifications using SNS.
* EC2 backup using AWS Backup.
* Secure instance management using Systems Manager.

---

## 🔒 Security Considerations

* Keep application EC2 instances in private subnets.
* Avoid direct internet access to private application servers.
* Use IAM roles instead of hard-coded AWS credentials.
* Apply least-privilege IAM permissions.
* Restrict Security Group rules to only required traffic.
* Monitor AWS activity using CloudWatch and CloudTrail.
* Use automated security notifications for relevant events.
* Maintain regular backups using AWS Backup.

---

## 📚 Documentation

Detailed project documentation is maintained separately from this README.

Recommended project documentation:

**[Problem Statement](./Theory/Project%20Discussion.docx)**            
**[Project Submission](./Theory/Project%20Submission.docx)**           
**[CloudFormation YAML Template](./Theory/template.yml)**

---

## 📌 Project Information

| **Field**               | **Details**                                 |
| ----------------------- | ------------------------------------------- |
| **Project**             | Secure Two-Tier AWS Architecture            |
| **Platform**            | Amazon Web Services (AWS)                   |
| **IaC Tool**            | AWS CloudFormation                          |
| **Template Format**     | YAML                                        |
| **Architecture**        | Two-Tier Architecture                       |
| **Networking**          | Amazon VPC                                  |
| **Compute**             | Amazon EC2                                  |
| **Load Balancing**      | Application Load Balancer                   |
| **Monitoring**          | Amazon CloudWatch                           |
| **Auditing**            | AWS CloudTrail                              |
| **Automation**          | EventBridge + Lambda + SNS                  |
| **Backup**              | AWS Backup                                  |
| **Instance Management** | AWS Systems Manager                         |
| **Internship**          | AWS Internship — Center of Excellence, KIET |

---

## 👩‍💻 Author

**Aditi Narang**

---