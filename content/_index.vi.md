---
title : "ស្វែងយល់ពី Amazon GuardDuty តាមរយៈការអនុវត្តជាក់ស្តែង"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
---

# ស្វែងយល់ពី **Amazon GuardDuty** តាមរយៈការអនុវត្តជាក់ស្តែង

#### ទិដ្ឋភាពទូទៅ
ជាមួយ **Amazon GuardDuty** ដែលជាសេវាកម្មគ្រប់គ្រងទាំងស្រុងដោយ AWS មេរៀនអនុវត្តនេះនឹងបង្ហាញពីរបៀបរកឃើញគ្រោះថ្នាក់ដល់ប្រព័ន្ធ និងវិធីដោះស្រាយ។ យើងនឹងធ្វើការវិភាគ វាយតម្លៃ និងរបៀបជូនដំណឹង ព្រមទាំងដោះស្រាយបញ្ហាសន្តិសុខដោយផ្អែកលើការរកឃើញ (*Findings*) របស់ GuardDuty។

ដើម្បីត្រៀមសម្រាប់មេរៀនអនុវត្តនេះ ដោយប្រើប្រាស់ **CloudFormation Template** ដែលមានស្រាប់ យើងនឹងធ្វើការចម្លងការវាយប្រហារ និងដំណោះស្រាយស្វ័យប្រវត្តិដោយការរួមបញ្ចូលគ្នារវាង **EventBridge Event Rules** និង **Lambda Functions**។

- **កម្រិត:** 300
- **រយៈពេល:** 1-2 ម៉ោង
- **តម្រូវការមុន:** IAM User (Admin) និង AWS CLI
- **មុខងារ **CSF** (Cybersecurity Framework):**
  - ការពារ (Protect)
  - រកឃើញ (Detect)
  - ឆ្លើយតប (Respond)
- **ទស្សនៈសន្តិសុខនៃ **CAF** (Cloud Adoption Framework):**
  - ការទប់ស្កាត់ (Preventative)
  - ការរុករក (Detective)
  - ការឆ្លើយតប (Responsive)
- **សេវាកម្ម AWS ដែលប្រើប្រាស់:**
  - Amazon EventBridge
  - Amazon GuardDuty
  - AWS CloudTrail
  - AWS Lambda
  - VPC Security Groups
  - Amazon SNS

#### ការរៀបចំ
> មេរៀនអនុវត្តនេះនឹងត្រូវបានរៀបចំនៅ **us-west-2 (Oregon)**។

សម្រាប់ព័ត៌មានលម្អិត សូមមើល [**ការរៀបចំបរិស្ថាន**](1-environment-setup/)។

#### ករណីសិក្សា
មេរៀនអនុវត្តនេះនឹងមានករណីសិក្សាដូចខាងក្រោម:
| លំដាប់ | ឈ្មោះ | ការពិពណ៌នា | ដំណោះស្រាយ |
| ------ | --- | ------ | --------- |
| 1 | [Compromised EC2 instance](3-compromised-ec2-instance/) | រកឃើញនិងស្តារ EC2 instance ដែលត្រូវបានវាយប្រហារ | ការរួមបញ្ចូលគ្នារវាង **Amazon GuardDuty**, **Amazon EventBridge Event Rules** និង **AWS Lambda** |
| 2 | [Compromised IAM credentials](4-compromised-iam-credentials/) | កំណត់អត្តសញ្ញាណបុគ្គលដែលកំពុងហៅ API ទៅកាន់ប្រព័ន្ធនៅលើ AWS | ដោះស្រាយគ្រោះថ្នាក់នេះភ្លាមៗដោយដៃ (manually) |
| 3 | [IAM role exfiltration](5-iam-role-credential-exfiltration/) | តាមរយៈព័ត៌មានសម្ងាត់ដែលលេចធ្លាយ បុគ្គលម្នាក់កំពុងព្យាយាមចូលប្រើនិងហៅ API ពីម៉ាស៊ីនមេខាងក្រៅ | អនុវត្តការដោះស្រាយជាមួយ **AWS Lambda** |

#### ការសម្អាត
ព័ត៌មានលម្អិតនៅផ្នែក [**ការសម្អាតបរិស្ថាន**](7-environment-cleanup/)។

#### មាតិកា

1. [ការរៀបចំបរិស្ថាន](1-environment-setup/)
2. [តើ GuardDuty ដំណើរការយ៉ាងដូចម្តេច?](2-how-it-works/)
3. [ករណីសិក្សាទី 1: Compromised EC2 instance](3-compromised-ec2-instance/)
4. [ករណីសិក្សាទី 2: Compromised IAM credentials](4-compromised-iam-credentials/)
5. [ករណីសិក្សាទី 3: IAM role exfiltration](5-iam-role-credential-exfiltration/)
6. [សេចក្តីសង្ខេប](6-summary/)
7. [ការសម្អាតបរិស្ថាន](7-environment-cleanup/)