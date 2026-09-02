# Commands — Self-Healing Infrastructure

> **AWS Internship — Center of Excellence, KIET**

This file contains the commands used to verify and test the Self-Healing Infrastructure project.

---

## 1. Configure AWS Region

```bash
aws configure set region ap-south-1
````

Check the configured region:

```bash
aws configure get region
```

---

## 2. Check EC2 Instance State

Replace `i-xxxxxxxxxxxxxxxxx` with your actual EC2 Instance ID.

```bash
aws ec2 describe-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx \
  --query "Reservations[0].Instances[0].State.Name" \
  --output text
```

Expected initial output:

```text
running
```

---

## 3. Stop the EC2 Instance

```bash
aws ec2 stop-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

---

## 4. Wait Until the Instance Is Stopped

```bash
aws ec2 wait instance-stopped \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

Verify the state:

```bash
aws ec2 describe-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx \
  --query "Reservations[0].Instances[0].State.Name" \
  --output text
```

Expected output:

```text
stopped
```

---

## 5. Check EventBridge Rule

The EventBridge rule used in this project is:

```text
AutoStartStoppedEC2
```

Check the rule:

```bash
aws events describe-rule \
  --name AutoStartStoppedEC2
```

Check whether the rule is enabled:

```bash
aws events describe-rule \
  --name AutoStartStoppedEC2 \
  --query "State" \
  --output text
```

Expected output:

```text
ENABLED
```

---

## 6. Check EventBridge Target

```bash
aws events list-targets-by-rule \
  --rule AutoStartStoppedEC2
```

The target should be configured for:

```text
Systems Manager Automation
```

with the document:

```text
AWS-StartEC2Instance
```

---

## 7. Check IAM Role

The IAM role used by the automation is:

```text
EC2AutoStartAutomationRole
```

Check the role:

```bash
aws iam get-role \
  --role-name EC2AutoStartAutomationRole
```

---

## 8. Check Attached IAM Policies

```bash
aws iam list-attached-role-policies \
  --role-name EC2AutoStartAutomationRole
```

The role should have:

```text
AmazonSSMAutomationRole
```

---

## 9. Check Inline IAM Policy

The inline policy is:

```text
AllowStartEC2Instance
```

List inline policies:

```bash
aws iam list-role-policies \
  --role-name EC2AutoStartAutomationRole
```

Get the policy:

```bash
aws iam get-role-policy \
  --role-name EC2AutoStartAutomationRole \
  --policy-name AllowStartEC2Instance
```

The policy allows:

```text
ec2:StartInstances
ec2:DescribeInstances
```

---

## 10. Start the Automation Manually

The AWS-managed Systems Manager Automation document used in this project is:

```text
AWS-StartEC2Instance
```

A manual test can be performed using:

```bash
aws ssm start-automation-execution \
  --document-name AWS-StartEC2Instance \
  --parameters \
  "InstanceId=i-xxxxxxxxxxxxxxxxx"
```

Replace the Instance ID with your actual EC2 Instance ID.

---

## 11. Check Automation Execution

Use the `ExecutionId` returned by the previous command:

```bash
aws ssm get-automation-execution \
  --automation-execution-id EXECUTION-ID
```

Expected status:

```text
Success
```

---

## 12. Wait Until EC2 Is Running

After EventBridge triggers the automation:

```bash
aws ec2 wait instance-running \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

Verify the final state:

```bash
aws ec2 describe-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx \
  --query "Reservations[0].Instances[0].State.Name" \
  --output text
```

Expected output:

```text
running
```

---

## 13. Complete Test Workflow

### Step 1 — Check Instance

```bash
aws ec2 describe-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx \
  --query "Reservations[0].Instances[0].State.Name" \
  --output text
```

### Step 2 — Stop Instance

```bash
aws ec2 stop-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

### Step 3 — Wait for Stopped State

```bash
aws ec2 wait instance-stopped \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

### Step 4 — EventBridge Detects the Event

EventBridge detects:

```text
EC2 Instance State-change Notification
```

with:

```text
state = stopped
```

### Step 5 — Systems Manager Automation Runs

EventBridge triggers:

```text
AWS-StartEC2Instance
```

### Step 6 — Verify Automatic Recovery

```bash
aws ec2 wait instance-running \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

### Step 7 — Confirm Final State

```bash
aws ec2 describe-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx \
  --query "Reservations[0].Instances[0].State.Name" \
  --output text
```

Expected:

```text
running
```

---

## 14. Expected State Transition

```text
Running
   ↓
Stopped
   ↓
EventBridge detects stopped state
   ↓
Systems Manager Automation
   ↓
AWS-StartEC2Instance
   ↓
Pending
   ↓
Running
```

---

## 15. Disable EventBridge Rule

If the automation is no longer required:

```bash
aws events disable-rule \
  --name AutoStartStoppedEC2
```

Verify:

```bash
aws events describe-rule \
  --name AutoStartStoppedEC2 \
  --query "State" \
  --output text
```

Expected:

```text
DISABLED
```

---

## 16. Delete EventBridge Rule

```bash
aws events remove-targets \
  --rule AutoStartStoppedEC2 \
  --ids TARGET-ID
```

Then delete the rule:

```bash
aws events delete-rule \
  --name AutoStartStoppedEC2
```

---

## 17. Cleanup EC2 Instance

If the test instance is no longer required:

```bash
aws ec2 terminate-instances \
  --instance-ids i-xxxxxxxxxxxxxxxxx
```

> **Warning:** Termination permanently deletes the EC2 instance. Use this command only after the lab is complete and the instance is no longer required.

---

## Result

The automation successfully demonstrates a self-healing workflow:

```text
EC2 Stopped
     ↓
EventBridge
     ↓
Systems Manager Automation
     ↓
AWS-StartEC2Instance
     ↓
EC2 Running
```