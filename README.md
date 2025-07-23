Project 2: EC2 Instance Launch with Enforced Tagging Policy Using AWS Tag Policies

## 🎯 Objective

This project demonstrates how to enforce AWS tag policies for EC2 instances to ensure that every instance is launched with specific mandatory tags. It helps maintain standardization, cost tracking, and resource accountability in the AWS environment.

---

## 🏗️ Architecture Diagram (Conceptual)

+-------------------+ +-------------------+
| AWS Management | | IAM Policies / |
| Console | --------> | Tag Policies |
+-------------------+ +-------------------+
| |
v v
+-------------------+ +--------------------------+
| Launch EC2 (CLI/GUI)| ----> | Enforce Required Tags |
+-------------------+ +--------------------------+
|
v
+-------------------+
| EC2 Instance (OK) |
+-------------------+


---

## ✅ Project Requirements

### 1. Define Required Tags

The EC2 instance must have these tags at launch:

| Tag Key | Example Value     |
|---------|-------------------|
| Name    | Anjali Dale.      |
| emailID | anjali@example.com|
| phoneNo | 9665153030        |
| Place   | Pune.             |

---

### 2. Implement Tag Policy

- Create a tag policy using **AWS Organizations** or **IAM SCPs** (if Org not used).
- Use AWS CLI or Console to enforce tag checks.
- Validate that EC2 cannot be launched without required tags.

---

### 3. Test EC2 Launch Behavior

- ✅ Launch EC2 with required tags → **Instance should be created**
- ❌ Launch EC2 without required tags → **Instance creation should fail**

---

## 🧑‍💻 Step-by-Step Guide

### Step 1: Create Tag Policy (If AWS Organizations is Enabled)

1. Go to **AWS Organizations → Tag Policies**
2. Create a policy like:

```json
{
  "tags": {
    "Name": {
      "tag_value": {
        "enforced_for": {
          "ec2:RunInstances": true
        }
      }
    },
    "emailID": { "tag_value": { "enforced_for": { "ec2:RunInstances": true } } },
    "phoneNo": { "tag_value": { "enforced_for": { "ec2:RunInstances": true } } },
    "Place": { "tag_value": { "enforced_for": { "ec2:RunInstances": true } } }
  }
}

    Attach the policy to your account or OU.

Step 2: (Alternate) IAM Policy Enforcement (If Organizations Not Used)

Create and attach an IAM policy that denies EC2 launch without tags:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyEC2WithoutTags",
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": "*",
      "Condition": {
        "StringNotEqualsIfExists": {
          "aws:RequestTag/Name": "*",
          "aws:RequestTag/emailID": "*",
          "aws:RequestTag/phoneNo": "*",
          "aws:RequestTag/Place": "*"
        }
      }
    }
  ]
}

Step 3: Launch EC2 with Tags via Console or CLI

✅ Successful example using AWS CLI:

aws ec2 run-instances \
  --image-id ami-0abcdef1234567890 \
  --instance-type t2.micro \
  --key-name my-key \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Anjali},{Key=emailID,Value=anjali@example.com},{Key=phoneNo,Value=9665153030},{Key=Place,Value=Pune}]'

❌ Launch without tags should return access denied or validation error
🔐 Security & Best Practices

    Enforcing tags improves billing, resource management, and compliance.

    Use Service Control Policies if part of an AWS Organization.

    Limit IAM users/roles from bypassing these policies.

📸 Screenshots to Include

    ✅ Screenshot: EC2 Launch Success With Tags

    ❌ Screenshot: EC2 Launch Failure Without Tags

    📄 Screenshot: Tag Policy Configuration (AWS Console or CLI)

📄 Deliverables

    README.md file (this file)

    Screenshots showing:

        Tag policy creation

        EC2 launch success/failure

    Step-by-step commands and code

    Optional: PDF documentation

👤 Submitted by

Name: Anjali Dale
Project: Enforcing EC2 Tagging Policy
Role: AWS Engineer (Internship Project)
📝 Notes

    AWS will deny resource creation if tags are missing or invalid.

    Use tagging standards across your organization.

    Clean up EC2 resources to avoid unnecessary charges.
