---
title : "ការសម្អាតធនធាន"
date :  "`r Sys.Date()`" 
weight : 8
chapter : false
pre : " <b> 8. </b> "
---

ដើម្បីសម្អាតធនធាន AWS ដែលបានប្រើប្រាស់ក្នុងការអនុវត្តនេះ យើងត្រូវធ្វើតាមជំហានដូចខាងក្រោម៖

1. លុប S3 bucket ដែលបានបង្កើតដោយ CloudFormation Template ដែលមានទម្រង់ `guardduty-example`។ ជំហាននេះចាំបាច់ត្រូវតែធ្វើ ព្រោះ CloudFormation Stack មិនអាចត្រូវបានលុបបានទេ ប្រសិនបើ S3 bucket នៅមានទិន្នន័យនៅខាងក្នុង។

2. លុប IAM Role ដែលត្រូវបានប្រើសម្រាប់ EC2 compromised instance ដែលមានទម្រង់ `GuardDuty-Example-EC2-Compromised`។ ដោយសារក្នុងដំណើរការ Remediation មួយ Lambda Function បានបន្ថែម IAM Policy មួយ CloudFormation Stack នឹងមិនអាចត្រូវបានលុបបានទេ ប្រសិនបើ IAM Role មាន IAM Policy ផ្សេងទៀត។

3. លុប **Custom Threat List** ដែលត្រូវបានប្រើសម្រាប់ GuardDuty។

4. បិទសេវាកម្ម Amazon GuardDuty។
   1. ចូលទៅកាន់ GuardDuty Console។
   2. ចុចលើផ្នែក **Settings**។
   3. ជ្រើសរើស `Disable GuardDuty` និងចុច `Save`។

5. លុប CloudFormation Stack។ ប្រសិនបើអ្នកមិនអាចលុបបាន សូមពិនិត្យមើលពីរជំហានដំបូងឡើងវិញ។