# Passwordless SSH Authentication Between Two Linux/EC2 Servers

> **AWS Internship — Center of Excellence, KIET**

## Table of Contents

## Table of Contents

- [Overview](#overview)
- [Objective](#objective)
- [Architecture](#architecture)
  - [Architecture Diagram](#architecture-diagram)
  - [Authentication Concept](#authentication-concept)
- [Workflow](#workflow)
- [AWS Environment](#aws-environment)
- [Key Components](#key-components)
- [Implementation](#implementation)
- [Documentation](#documentation)
- [Applications](#applications)
- [Security](#security)
- [Result](#result)
- [Project Information](#project-information)
  - [Author](#author)

## Overview

This project demonstrates the configuration of **passwordless SSH authentication between two Linux/EC2 servers using SSH public-key authentication**.

The setup allows **Server A (source)** to securely connect to **Server B (destination)** without requiring a password for every SSH connection. Instead of password-based authentication, the connection is established using an SSH public/private key pair.

This is an important concept in Linux and cloud environments because it enables secure and automated server-to-server communication.

---

## Objective

The objective of this project is to understand and implement:

* SSH public-key authentication
* SSH key-pair generation
* Public-key configuration on a remote server
* Passwordless server-to-server SSH communication
* SSH authentication verification and troubleshooting
* The role of SSH authentication in automation and cloud environments

---

## Architecture

The lab consists of two Linux/EC2 servers:

| Server       | Role                   | Private IP      |
| ------------ | ---------------------- | --------------- |
| **Server A** | Source / Initiator     | `172.31.31.174` |
| **Server B** | Destination / Receiver | `172.31.22.11`  |

> The IP addresses shown above are specific to the lab environment. They may differ for other EC2 instances.

### Architecture Diagram

![Passwordless SSH Authentication Architecture](./Architecture/passwordless-ssh-architecture.png)

### Authentication Concept

The authentication mechanism uses two keys:

* **Private Key (`id_rsa`)** — remains on Server A and is never shared.
* **Public Key (`id_rsa.pub`)** — copied to Server B and stored in `~/.ssh/authorized_keys`.

When Server A connects to Server B, Server B verifies the identity of Server A using the corresponding public key. Once authentication succeeds, the SSH session is established without requiring a password.

---

## Workflow

```text
Server A                              Server B
(Source)                              (Destination)
   │                                      │
   │  SSH Connection Request              │
   ├─────────────────────────────────────>│
   │                                      │
   │  Cryptographic Authentication        │
   │<─────────────────────────────────────┤
   │                                      │
   │  Public Key Verification             │
   ├─────────────────────────────────────>│
   │                                      │
   │       Authentication Successful      │
   │<─────────────────────────────────────┤
   │                                      │
   └──────── SSH Session Established ─────┘
              No Password Required
```

---

## AWS Environment

The implementation is performed using **Linux-based Amazon EC2 instances**.

For communication between the servers, **SSH traffic on port 22** must be permitted through the relevant EC2 Security Group configuration.

---

## Key Components

| Component          | Purpose                                      |
| ------------------ | -------------------------------------------- |
| `id_rsa`           | Private key stored on Server A               |
| `id_rsa.pub`       | Public key generated on Server A             |
| `authorized_keys`  | Stores the authorized public key on Server B |
| SSH                | Provides secure remote communication         |
| EC2 Security Group | Controls network access to SSH port 22       |

---

## Implementation

The implementation involves:

1. Generating an SSH key pair on Server A.
2. Verifying the generated keys.
3. Copying Server A's public key.
4. Adding the public key to Server B's `authorized_keys`.
5. Configuring the required SSH file permissions.
6. Connecting from Server A to Server B without entering a password.
7. Using verbose SSH output to verify authentication when required.

For the complete list of commands used during the implementation, see:

**[Commands.md](./Commands.md)**

---

## Documentation

**[Theory](./Theory)** 

---

## Applications

Passwordless SSH authentication is useful for automated server-to-server workflows such as:

* **Automated backups** using `rsync`
* **CI/CD deployments**
* **Configuration management** using tools such as Ansible, Chef, and Puppet
* **Unattended file transfers** using `scp`, `sftp`, or `rsync`

---

## Security

A key principle of this configuration is that the **private key must remain on the source server and must never be shared**.

Only the public key is transferred to the destination server. The SSH configuration also requires appropriate permissions for the `.ssh` directory and `authorized_keys` file.

---

## Result

Passwordless SSH authentication was configured between the two Linux/EC2 servers.

The final setup enables:

```text
Server A
   │
   │ SSH using key-based authentication
   ▼
Server B
   │
   ▼
Authentication Successful
   │
   ▼
No Password Required
```

This demonstrates how SSH public-key authentication can provide secure and automation-friendly server-to-server access.

---

## Project Information

**Project:** Passwordless SSH Authentication Between Two Linux/EC2 Servers
**Platform:** AWS EC2
**Technology:** Linux, SSH
**Authentication:** SSH Public-Key Authentication
**Internship:** AWS Internship — Center of Excellence, KIET

---

### Author

**Aditi Narang**
