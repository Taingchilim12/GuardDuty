---
title : "សេចក្តីផ្តើម"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
#### ទិដ្ឋភាពទូទៅ
ជាមួយ **Amazon GuardDuty** ដែលជាសេវាកម្មគ្រប់គ្រងដោយ AWS ទាំងស្រុង មេរៀនអនុវត្តន៍នេះនឹងពន្យល់ពីរបៀបរកឃើញការគំរាមកំហែងប្រព័ន្ធ និងដោះស្រាយវា។ យើងនឹងធ្វើការវិភាគ វាយតម្លៃ និងរបៀបជូនដំណឹង ក៏ដូចជាដោះស្រាយបញ្ហាសន្តិសុខដោយផ្អែកលើការរកឃើញ (*Findings*) របស់ GuardDuty។

ដើម្បីត្រៀមសម្រាប់មេរៀនអនុវត្តន៍នេះ ដោយប្រើប្រាស់ **CloudFormation Template** ដែលមានស្រាប់ យើងនឹងធ្វើការចម្លងការវាយប្រហារ និងដំណោះស្រាយស្វ័យប្រវត្តិដោយរួមបញ្ចូល **EventBridge Event Rules** និង **Lambda Functions**។

- **កម្រិត:** 300
- **រយៈពេល:** 1-2 ម៉ោង
- **តម្រូវការមុន:** IAM User (Admin) និង AWS CLI
- **មុខងារ **CSF** (Cybersecurity Framework):**
  - ការពារ (Protect)
  - រកឃើញ (Detect)
  - ឆ្លើយតប (Respond)
- **ទស្សនៈសន្តិសុខនៃ **CAF** (Cloud Adoption Framework):**
  - ការបង្ការ (Preventative)
  - ការរកឃើញ (Detective)
  - ការឆ្លើយតប (Responsive)
- **សេវាកម្ម AWS ដែលប្រើប្រាស់:**
  - Amazon EventBridge
  - Amazon GuardDuty
  - AWS CloudTrail
  - AWS Lambda
  - VPC Security Groups
  - Amazon SNS

#### ការរៀបចំ
> មេរៀនអនុវត្តន៍នឹងត្រូវបានរៀបចំនៅក្នុង **us-west-2 (Oregon)**។

សម្រាប់ព័ត៌មានលម្អិត សូមមើល [**ការរៀបចំបរិយាកាស**](1-environment-setup/)។

#### ករណីសិក្សា
មេរៀនអនុវត្តន៍នឹងរួមបញ្ចូលករណីដូចខាងក្រោម៖
| លំដាប់ | ឈ្មោះ | ការពិពណ៌នា | ដំណោះស្រាយ |
| ------ | --- | ------ | --------- |
| 1 | [Compromised EC2 instance](3-compromised-ec2-instance/) | រកឃើញនិងស្តារ EC2 instance ដែលត្រូវបានវាយប្រហារ | ការរួមបញ្ចូលគ្នានៃ **Amazon GuardDuty**, **Amazon EventBridge Event Rules** និង **AWS Lambda** |
| 2 | [Compromised IAM credentials](4-compromised-iam-credentials/) | កំណត់អត្តសញ្ញាណបុគ្គលដែលកំពុងហៅ API ទៅកាន់ប្រព័ន្ធនៅលើ AWS | ដោះស្រាយការគំរាមកំហែងនេះភ្លាមៗដោយដៃ (manually) |
| 3 | [IAM role exfiltration](5-iam-role-credential-exfiltration/) | តាមរយៈ credential ដែលលេចធ្លាយ បុគ្គលម្នាក់កំពុងព្យាយាមចូលប្រើនិងហៅ API ពីម៉ាស៊ីនមេខាងក្រៅ | អនុវត្តការដោះស្រាយជាមួយ **AWS Lambda** |

#### ការសម្អាត
ព័ត៌មានលម្អិតនៅក្នុងផ្នែក [**ការសម្អាតបរិយាកាស**](7-environment-cleanup/)។