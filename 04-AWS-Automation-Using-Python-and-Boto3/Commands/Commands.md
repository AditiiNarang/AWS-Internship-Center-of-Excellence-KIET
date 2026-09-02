# AWS Automation Using Python & Boto3 — `commands.md`

````markdown
# AWS Automation Using Python & Boto3 — Commands

> **AWS Internship — Center of Excellence, KIET**

## 1. Check Python Installation

```bash
python --version
````

```bash
python3 --version
```

---

## 2. Check AWS CLI Installation

```bash
aws --version
```

---

## 3. Install Boto3

```bash
pip install boto3
```

Verify Boto3 installation:

```bash
pip show boto3
```

---

## 4. Configure AWS CLI

```bash
aws configure
```

Enter the following when prompted:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

Example:

```text
Default region name: ap-south-1
Default output format: json
```

---

## 5. Verify AWS Configuration

Check the configured region:

```bash
aws configure get region
```

Check the current AWS identity:

```bash
aws sts get-caller-identity
```

Display AWS CLI configuration:

```bash
aws configure list
```

---

# Amazon S3 Commands

## 6. List S3 Buckets

```bash
aws s3 ls
```

---

## 7. Create an S3 Bucket

For `ap-south-1`:

```bash
aws s3api create-bucket \
    --bucket <unique-bucket-name> \
    --region ap-south-1 \
    --create-bucket-configuration LocationConstraint=ap-south-1
```

---

## 8. Upload a File

```bash
aws s3 cp <local-file> s3://<bucket-name>/
```

Example:

```bash
aws s3 cp sample.txt s3://my-boto3-demo-bucket/
```

---

## 9. List Objects in a Bucket

```bash
aws s3 ls s3://<bucket-name>/
```

---

## 10. Download a File

```bash
aws s3 cp s3://<bucket-name>/<file-name> .
```

Example:

```bash
aws s3 cp s3://my-boto3-demo-bucket/sample.txt .
```

---

## 11. Delete an S3 Object

```bash
aws s3 rm s3://<bucket-name>/<file-name>
```

---

## 12. Delete an S3 Bucket

```bash
aws s3 rb s3://<bucket-name>
```

---

# Amazon EC2 Commands

## 13. List EC2 Instances

```bash
aws ec2 describe-instances
```

---

## 14. List Instance IDs and States

```bash
aws ec2 describe-instances \
    --query "Reservations[].Instances[].[InstanceId,State.Name]" \
    --output table
```

---

## 15. Launch an EC2 Instance

```bash
aws ec2 run-instances \
    --image-id <ami-id> \
    --instance-type <instance-type> \
    --key-name <key-pair-name> \
    --security-group-ids <security-group-id> \
    --count 1
```

---

## 16. Start an EC2 Instance

```bash
aws ec2 start-instances \
    --instance-ids <instance-id>
```

---

## 17. Stop an EC2 Instance

```bash
aws ec2 stop-instances \
    --instance-ids <instance-id>
```

---

## 18. Check EC2 Instance State

```bash
aws ec2 describe-instances \
    --instance-ids <instance-id> \
    --query "Reservations[0].Instances[0].State.Name" \
    --output text
```

---

## 19. Terminate an EC2 Instance

```bash
aws ec2 terminate-instances \
    --instance-ids <instance-id>
```

---

# AWS IAM Commands

## 20. List IAM Users

```bash
aws iam list-users
```

---

## 21. Display IAM Usernames

```bash
aws iam list-users \
    --query "Users[].UserName" \
    --output table
```

---

## 22. Get IAM User Details

```bash
aws iam get-user
```

For a specific user:

```bash
aws iam get-user \
    --user-name <username>
```

---

## 23. List IAM Roles

```bash
aws iam list-roles
```

---

# AWS Inventory Commands

## 24. S3 Inventory

```bash
aws s3api list-buckets \
    --query "Buckets[].Name" \
    --output table
```

---

## 25. EC2 Inventory

```bash
aws ec2 describe-instances \
    --query "Reservations[].Instances[].[InstanceId,InstanceType,State.Name]" \
    --output table
```

---

## 26. IAM Inventory

```bash
aws iam list-users \
    --query "Users[].[UserName,UserId,CreateDate]" \
    --output table
```

---

# Running Python Scripts

Navigate to the project directory:

```bash
cd AWS-Automation-Using-Boto3
```

Run the S3 automation script:

```bash
python scripts/s3_operations.py
```

Run the EC2 automation script:

```bash
python scripts/ec2_operations.py
```

Run the IAM automation script:

```bash
python scripts/iam_operations.py
```

Generate the AWS Inventory Report:

```bash
python scripts/aws_inventory.py
```

---

# Boto3 Verification

## 27. Check Boto3 Version

```bash
python
```

```python
import boto3
print(boto3.__version__)
```

Exit Python:

```python
exit()
```

---

## 28. Verify Boto3 AWS Connection

```python
import boto3

sts = boto3.client("sts")

response = sts.get_caller_identity()

print(response)
```

---

# Cleanup Commands

## 29. Remove an S3 Object

```bash
aws s3 rm s3://<bucket-name>/<file-name>
```

---

## 30. Remove an S3 Bucket

```bash
aws s3 rb s3://<bucket-name>
```

---

## 31. Terminate EC2 Instance

```bash
aws ec2 terminate-instances \
    --instance-ids <instance-id>
```

---
