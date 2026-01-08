---
title : "Getting Hands on with Amazon GuardDuty"
date : "`r Sys.Date()`"
weight : 1
chapter : false
---

# Getting Hands on with Amazon GuardDuty

#### Overview
With **Amazon GuardDuty**, a fully managed service by AWS, this exercise will cover how to detect system threats and remediate them. We will conduct analysis, assessment and how to alarm and remediate security issues based on the findings (*Findings*) of GuardDuty.

To prepare for this exercise, using the built-in **CloudFormation Template**, we will reproduce the attacks and automated fixes by combining **EventBridge Event Rules** and **Lambda Functions**.
- **Level:** 300
- **Duration:** 1-2 hours
- **Requirement:** IAM User (Admin) and AWS CLI
- **Functions of **CSF** (Cybersecurity Framework):**
  - Protect (Protect)
  - Detect (Detect)
  - Feedback (Respond)
- **Security Perspectives of **CAF** (Cloud Adoption Framework):**
  - Preventative (Ability to prevent)
  - Detective (Traceability)
  - Responsive
- **AWS Services Used:**
  - Amazon EventBridge
  - Amazon GuardDuty
  - AWS CloudTrail
  - AWS Lambda
  - VPC Security Groups
  - Amazon SNS

#### Establish
> The exercise will be set at **us-west-2 (Oregon)**.

For more details, see [**Environment Setup**](1-environment-setup/).

#### Situation
The exercise will cover the following scenarios:
| Order | Name | Specification | Solution |
| ------ | --- | ------ | --------- |
| 1 | [Compromised EC2 instance](3-compromised-ec2-instance/) | Detect and recover hacked EC2 instances | Combination of **Amazon GuardDuty**, **Amazon EventBridge Event Rules** and **AWS Lambda** |
| 2 | [Compromised IAM credentials](4-compromised-iam-credentials/) | Identify an individual who is actively calling an API to the system on AWS | Fix this hazard immediately (manually) |
| 3 | [IAM role exfiltration](5-iam-role-credential-exfiltration/) | Through a leaked credential, an individual is attempting to hack and call the API from an external server | Fix it with **AWS Lambda** |

#### Clean up
Details are in [**Environment Cleanup**](7-environment-cleanup/).

#### Content

1. [Environment Setup](1-environment-setup/)
2. [How does GuardDuty Work?](2-how-it-works/)
3. [Scenario 1: Compromised EC2 instance](3-compromised-ec2-instance/)
4. [Scenario 2: Compromised IAM credentials](4-compromised-iam-credentials/)
5. [Scenario 3: IAM role exfiltration](5-iam-role-credential-exfiltration/)
6. [Summary](6-summary/)
7. [Cleanup environment](7-environment-cleanup/)