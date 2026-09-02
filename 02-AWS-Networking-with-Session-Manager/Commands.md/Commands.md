# AWS Networking with Session Manager — Commands

> **AWS Internship — Center of Excellence, KIET**

This file contains practical commands for the three access methods demonstrated in the project:

1. Bastion Host using SSH
2. Session Manager using NAT Gateway
3. Session Manager using VPC Endpoints

---

## 1. Prerequisites

Before running the commands, make sure:

- AWS CLI is installed and configured.
- You have permission to use EC2 and Systems Manager.
- The private EC2 instance has an IAM role containing `AmazonSSMManagedInstanceCore`.
- The SSM Agent is installed and running on the private EC2 instance.
- Replace all placeholder values such as `<PRIVATE_IP>`, `<INSTANCE_ID>`, and `<KEY_FILE>` with your actual values.

Check AWS CLI:

```bash
aws --version
```

Check the configured AWS identity:

```bash
aws sts get-caller-identity
```

Set the region used by the lab:

```bash
aws configure set region ap-south-1
```

---

# 2. SSH Access — Bastion Host Method

## 2.1 SSH from Local Machine to Bastion Host

The Bastion Host is deployed in the public subnet and has a public IPv4 address.

```bash
ssh -i <KEY_FILE>.pem ec2-user@<BASTION_PUBLIC_IP>
```

For Ubuntu:

```bash
ssh -i <KEY_FILE>.pem ubuntu@<BASTION_PUBLIC_IP>
```

---

## 2.2 Copy the Private Key to Bastion Host

If the Bastion Host needs to SSH into the private EC2 using the same key:

```bash
scp -i <KEY_FILE>.pem <KEY_FILE>.pem ec2-user@<BASTION_PUBLIC_IP>:/home/ec2-user/
```

For Ubuntu:

```bash
scp -i <KEY_FILE>.pem <KEY_FILE>.pem ubuntu@<BASTION_PUBLIC_IP>:/home/ubuntu/
```

> **Security Note:** Copying a private key to a Bastion Host is acceptable for a controlled lab demonstration, but it is generally preferable to use SSH agent forwarding or another secure key-management approach in production.

---

## 2.3 Set Private Key Permissions on Bastion

```bash
chmod 400 <KEY_FILE>.pem
```

---

## 2.4 SSH from Bastion Host to Private EC2

Use the private IPv4 address of the private EC2 instance:

```bash
ssh -i <KEY_FILE>.pem ec2-user@<PRIVATE_EC2_PRIVATE_IP>
```

For Ubuntu:

```bash
ssh -i <KEY_FILE>.pem ubuntu@<PRIVATE_EC2_PRIVATE_IP>
```

The connection path is:

```text
Local Machine
      |
      | SSH
      v
Bastion Host
      |
      | SSH using private IP
      v
Private EC2
```

---

# 3. Verify Private EC2 Networking

Run these commands on the private EC2 instance.

## 3.1 Display Hostname

```bash
hostname
```

## 3.2 Display IP Addresses

```bash
ip addr
```

or:

```bash
hostname -I
```

## 3.3 Display Routing Table

```bash
ip route
```

Expected private-subnet default route for the NAT scenario:

```text
default via <PRIVATE_SUBNET_GATEWAY> dev eth0
```

The exact gateway address depends on the subnet configuration.

## 3.4 Check DNS Configuration

```bash
cat /etc/resolv.conf
```

## 3.5 Test DNS Resolution

```bash
getent hosts amazon.com
```

or:

```bash
nslookup amazon.com
```

---

# 4. Session Manager — NAT Gateway Method

In this method, the private EC2 instance communicates with AWS Systems Manager through the NAT Gateway.

The expected path is:

```text
Private EC2
     |
     v
Private Route Table
     |
     | 0.0.0.0/0
     v
NAT Gateway
     |
     v
Internet Gateway
     |
     v
AWS Systems Manager
     |
     v
Session Manager
```

## 4.1 Verify SSM Agent

On the private EC2 instance:

```bash
sudo systemctl status amazon-ssm-agent
```

If the service is stopped:

```bash
sudo systemctl start amazon-ssm-agent
```

Enable it at boot:

```bash
sudo systemctl enable amazon-ssm-agent
```

Restart the agent:

```bash
sudo systemctl restart amazon-ssm-agent
```

---

## 4.2 Check SSM Agent Logs

```bash
sudo journalctl -u amazon-ssm-agent
```

To view recent logs:

```bash
sudo journalctl -u amazon-ssm-agent --no-pager -n 50
```

---

## 4.3 Verify IAM Instance Role

The EC2 instance should have an IAM role containing:

```text
AmazonSSMManagedInstanceCore
```

The role can be checked from the AWS Console under:

```text
EC2
→ Instances
→ Select Private EC2
→ Security
→ IAM Role
```

---

## 4.4 Start a Session Using AWS CLI

From a machine where AWS CLI and Session Manager Plugin are configured:

```bash
aws ssm start-session --target <PRIVATE_EC2_INSTANCE_ID> --region ap-south-1
```

Example format:

```bash
aws ssm start-session --target i-0123456789abcdef0 --region ap-south-1
```

A successful command opens an interactive shell session on the private EC2 instance.

---

# 5. Verify NAT Gateway Connectivity

## 5.1 Test Internet Connectivity

From the private EC2:

```bash
curl -I https://www.google.com
```

or:

```bash
curl -I https://aws.amazon.com
```

A successful response indicates outbound HTTPS connectivity.

## 5.2 Test HTTPS Connectivity

```bash
curl -v https://ssm.ap-south-1.amazonaws.com
```

## 5.3 Check Route

```bash
ip route
```

The private route table should contain:

```text
0.0.0.0/0 → NAT Gateway
```

The NAT Gateway itself must be in the public subnet and the public route table must contain:

```text
0.0.0.0/0 → Internet Gateway
```

---

# 6. Session Manager — VPC Endpoint Method

In this method, SSM traffic uses Interface VPC Endpoints.

The expected path is:

```text
Private EC2
     |
     v
Interface VPC Endpoints
     |
     +── SSM
     +── SSMMessages
     +── EC2Messages
     |
     v
AWS Systems Manager
     |
     v
Session Manager
```

The endpoints used in this lab are:

```text
com.amazonaws.ap-south-1.ssm
com.amazonaws.ap-south-1.ec2messages
com.amazonaws.ap-south-1.ssmmessages
```

---

## 6.1 Verify DNS Resolution

From the private EC2:

```bash
getent hosts ssm.ap-south-1.amazonaws.com
```

```bash
getent hosts ssmmessages.ap-south-1.amazonaws.com
```

```bash
getent hosts ec2messages.ap-south-1.amazonaws.com
```

---

## 6.2 Test HTTPS Connectivity to SSM

```bash
curl -I https://ssm.ap-south-1.amazonaws.com
```

```bash
curl -I https://ssmmessages.ap-south-1.amazonaws.com
```

```bash
curl -I https://ec2messages.ap-south-1.amazonaws.com
```

The endpoint Security Group should allow:

```text
TCP 443
Source: Private EC2 Security Group
```

---

# 7. AWS CLI — EC2 Verification

## 7.1 List EC2 Instances

```bash
aws ec2 describe-instances --region ap-south-1
```

## 7.2 Display Instance IDs and Private IPs

```bash
aws ec2 describe-instances \
  --region ap-south-1 \
  --query 'Reservations[].Instances[].[InstanceId,PrivateIpAddress,PublicIpAddress,State.Name]' \
  --output table
```

## 7.3 Describe a Specific Instance

```bash
aws ec2 describe-instances \
  --instance-ids <INSTANCE_ID> \
  --region ap-south-1
```

---

# 8. AWS CLI — Route Table Verification

List route tables:

```bash
aws ec2 describe-route-tables --region ap-south-1
```

Display route table information:

```bash
aws ec2 describe-route-tables \
  --region ap-south-1 \
  --query 'RouteTables[].[RouteTableId,Routes[].DestinationCidrBlock,Routes[].GatewayId,Routes[].NatGatewayId]' \
  --output table
```

For the private route table, verify:

```text
0.0.0.0/0 → NAT Gateway
```

For the public route table, verify:

```text
0.0.0.0/0 → Internet Gateway
```

---

# 9. AWS CLI — NAT Gateway Verification

List NAT Gateways:

```bash
aws ec2 describe-nat-gateways --region ap-south-1
```

Display NAT Gateway state:

```bash
aws ec2 describe-nat-gateways \
  --region ap-south-1 \
  --query 'NatGateways[].[NatGatewayId,State,SubnetId,ConnectivityType]' \
  --output table
```

The NAT Gateway should be in:

```text
available
```

state.

---

# 10. AWS CLI — Security Group Verification

List Security Groups:

```bash
aws ec2 describe-security-groups --region ap-south-1
```

Describe a specific Security Group:

```bash
aws ec2 describe-security-groups \
  --group-ids <SECURITY_GROUP_ID> \
  --region ap-south-1
```

For the Bastion Host:

```text
TCP 22 → Required Source IP
```

For the Private EC2 when Bastion access is used:

```text
TCP 22 → Bastion Security Group
```

For the VPC Endpoint Security Group:

```text
TCP 443 → Private EC2 Security Group
```

---

# 11. AWS CLI — VPC Endpoint Verification

List VPC Endpoints:

```bash
aws ec2 describe-vpc-endpoints --region ap-south-1
```

Display endpoint IDs and services:

```bash
aws ec2 describe-vpc-endpoints \
  --region ap-south-1 \
  --query 'VpcEndpoints[].[VpcEndpointId,ServiceName,VpcEndpointType,State,PrivateDnsEnabled]' \
  --output table
```

Expected endpoint services:

```text
com.amazonaws.ap-south-1.ssm
com.amazonaws.ap-south-1.ec2messages
com.amazonaws.ap-south-1.ssmmessages
```

Expected configuration:

```text
VpcEndpointType     → Interface
State               → Available
PrivateDnsEnabled   → True
```

---

# 12. AWS CLI — IAM Role Verification

List instance profiles:

```bash
aws iam list-instance-profiles --region ap-south-1
```

Get the IAM role:

```bash
aws iam get-role --role-name <ROLE_NAME>
```

List attached policies:

```bash
aws iam list-attached-role-policies \
  --role-name <ROLE_NAME>
```

Verify that:

```text
AmazonSSMManagedInstanceCore
```

is attached to the IAM role used by the private EC2 instance.

---

# 13. AWS CLI — Systems Manager Verification

List managed instances:

```bash
aws ssm describe-instance-information \
  --region ap-south-1
```

Display managed instance IDs:

```bash
aws ssm describe-instance-information \
  --region ap-south-1 \
  --query 'InstanceInformationList[].[InstanceId,PingStatus,AgentVersion]' \
  --output table
```

Expected status:

```text
PingStatus → Online
```

---

# 14. Start Session from AWS CLI

```bash
aws ssm start-session \
  --target <PRIVATE_EC2_INSTANCE_ID> \
  --region ap-south-1
```

To terminate the session:

```text
Ctrl + C
```

---

# 15. Useful Linux Commands During the Lab

Display current user:

```bash
whoami
```

Display hostname:

```bash
hostname
```

Display operating system:

```bash
cat /etc/os-release
```

Display IP information:

```bash
ip addr
```

Display routes:

```bash
ip route
```

Check listening ports:

```bash
sudo ss -tulpn
```

Check active network connections:

```bash
ss -tunap
```

Test a port:

```bash
nc -zv <HOST> 443
```

Test SSH port:

```bash
nc -zv <PRIVATE_EC2_PRIVATE_IP> 22
```

---

# 16. Verification Checklist

## Bastion Host

```text
✓ Bastion Host has a public IP
✓ SSH access to Bastion is restricted
✓ Private EC2 has no public IP
✓ Private EC2 allows TCP 22 from Bastion Security Group
✓ Private EC2 is reachable using its private IP
```

## Session Manager + NAT Gateway

```text
✓ Private EC2 has no public IP
✓ Private route table contains 0.0.0.0/0 → NAT Gateway
✓ NAT Gateway is available
✓ NAT Gateway is in the public subnet
✓ Public route table contains 0.0.0.0/0 → Internet Gateway
✓ IAM role contains AmazonSSMManagedInstanceCore
✓ SSM Agent is running
✓ Private EC2 has outbound HTTPS connectivity
✓ Session Manager connection works
```

## Session Manager + VPC Endpoints

```text
✓ Interface VPC Endpoints exist
✓ SSM endpoint exists
✓ SSMMessages endpoint exists
✓ EC2Messages endpoint exists
✓ Private DNS is enabled
✓ Endpoint Security Group allows TCP 443
✓ Private EC2 allows outbound TCP 443
✓ IAM role is attached
✓ SSM Agent is running
✓ Private EC2 resolves SSM endpoint DNS
✓ Session Manager connection works
```

---

# 17. Important Security Notes

Do not expose the private EC2 instance directly to the Internet.

Avoid:

```text
TCP 22 → 0.0.0.0/0
```

Prefer:

```text
TCP 22 → Bastion Security Group
```

For VPC Endpoints:

```text
TCP 443 → Private EC2 Security Group
```

The private EC2 instance should remain without a public IPv4 address.

---

# 18. Project Access Summary

```text
METHOD 1 — BASTION HOST

Local Machine
      |
      | SSH
      v
Bastion Host
      |
      | SSH using Private IP
      v
Private EC2
```

```text
METHOD 2 — SESSION MANAGER + NAT

Private EC2
      |
      v
Private Route Table
      |
      v
NAT Gateway
      |
      v
Internet Gateway
      |
      v
AWS Systems Manager
      |
      v
Session Manager
```

```text
METHOD 3 — SESSION MANAGER + VPC ENDPOINTS

Private EC2
      |
      v
Interface VPC Endpoints
      |
      +── SSM
      +── SSMMessages
      +── EC2Messages
      |
      v
AWS Systems Manager
      |
      v
Session Manager
```

---

## 19. Quick Command Reference

### Bastion SSH

```bash
ssh -i <KEY_FILE>.pem ec2-user@<BASTION_PUBLIC_IP>
```

### Private EC2 SSH

```bash
ssh -i <KEY_FILE>.pem ec2-user@<PRIVATE_EC2_PRIVATE_IP>
```

### SSM Session

```bash
aws ssm start-session --target <INSTANCE_ID> --region ap-south-1
```

### SSM Agent Status

```bash
sudo systemctl status amazon-ssm-agent
```

### IP Address

```bash
ip addr
```

### Routing Table

```bash
ip route
```

### Internet Test

```bash
curl -I https://www.google.com
```

### SSM Managed Instance Check

```bash
aws ssm describe-instance-information --region ap-south-1
```

### VPC Endpoint Check

```bash
aws ec2 describe-vpc-endpoints --region ap-south-1
```

### NAT Gateway Check

```bash
aws ec2 describe-nat-gateways --region ap-south-1
```

---

## Conclusion

These commands support the practical implementation and verification of the three connectivity methods demonstrated in this project:

- **Bastion Host + SSH**
- **Session Manager + NAT Gateway**
- **Session Manager + VPC Endpoints**

The commands should be executed according to the network configuration of the AWS environment. Replace all placeholders with the actual values from your AWS resources before execution.
