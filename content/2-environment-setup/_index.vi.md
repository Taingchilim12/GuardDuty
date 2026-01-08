---
title : "ជំហានត្រៀម"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2. </b> "
---

**មាតិកា**
- [លក្ខខណ្ឌចាំបាច់](#លក្ខខណ្ឌចាំបាច់)
- [ការដំឡើង](#ការដំឡើង)
- [ធនធាន AWS ដែលត្រូវត្រៀម?](#ធនធាន-aws-ដែលត្រូវត្រៀម)

#### លក្ខខណ្ឌចាំបាច់
- **គណនី AWS**: គណនីសម្រាប់បរិយាកាស **TESTING**។
- **សិទ្ធិ Administrator**: ត្រូវប្រាកដថាអ្នកកំពុងប្រើ **IAM user** ដែលមានសិទ្ធិជា Administrator។
- **AWS CLI**: ត្រូវប្រាកដថាអ្នកអាចប្រើប្រាស់ AWS CLI សម្រាប់ការធ្វើតេស្តការវាយប្រហារពីកុំព្យូទ័ររបស់អ្នក។

#### ការដំឡើង

---

**បើកដំណើរការ Amazon GuardDuty**

1. ចូលទៅក្នុង AWS Console, ចូលទៅកាន់សេវាកម្ម [GuardDuty](https://us-west-2.console.aws.amazon.com/guardduty/home)។
2. ចាប់ផ្តើមដោយជ្រើសរើស **Get Started**។

![guardduty-get-started](/images/2-guardduty-get-started.png?featherlight=false&width=90pc)

3. សម្រាប់គណនីថ្មី AWS នឹងផ្តល់ឱ្យយើង 30 ថ្ងៃសាកល្បង, ដើម្បីចាប់ផ្តើមប្រើប្រាស់ សូមចុចប៊ូតុង `Enable GuardDuty`។

![guardduty-enable](/images/2-guardduty-enable.png?featherlight=false&width=90pc)

---

**ត្រៀមធនធានជាមួយ AWS CloudFormation**

1. ចូលទៅក្នុង AWS Console, ចូលទៅកាន់សេវាកម្ម [CloudFormation](https://us-west-2.console.aws.amazon.com/cloudformation/home)
2. បង្កើត Stack ថ្មីដោយចុចប៊ូតុង `Create Stack`។
3. នៅទំព័រ `Specify template`,
- ទាញយក template នៅ [ទីនេះ](https://github.com/AWS-First-Cloud-Journey/GuardDuty-Hands-On/archive/refs/heads/main.zip)
- ផ្ទុកឡើង Template ដែលមានស្រាប់ដោយប្រើប៊ូតុង `Upload a template file`។
4. នៅទំព័រ `Parameters`, យើងនឹងបញ្ចូលព័ត៌មានចាំបាច់មួយចំនួន៖
   1. `EmailAddress`: អាសយដ្ឋានអ៊ីមែលផ្ទាល់ខ្លួនដើម្បីទទួលបានការជូនដំណឹង។

![cloudformation-stack-specify-parameters](/images/2-cloudformation-stack-specify-parameters.png?featherlight=false&width=90pc)

5. នៅទំព័រ `Specify Stack Details`, ជ្រើសរើសប៊ូតុង `Next`។
6. នៅទំព័រ `Configure stack options`, ជ្រើសរើសប៊ូតុង `Next`។
7. នៅទំព័រ `Capabilities`, យល់ព្រម (Acknowledge) អនុញ្ញាតឱ្យ Template បង្កើត IAM roles, ហើយចុងក្រោយជ្រើសរើសប៊ូតុង `Create Stack`។

![cloudformation-stack-create-complete](/images/2-cloudformation-stack-create-complete.png?featherlight=false&width=90pc)

![sns-notification-subscription-confirmation](/images/2-sns-notification-subscription-confirmation.png?featherlight=false&width=90pc)

![sns-notification-subscription-confirmation](/images/2-sns-notification-subscription-confirmation1.png?featherlight=false&width=90pc)

លទ្ធផលដំបូងនឹងចាប់ផ្តើមបង្ហាញក្នុងរយៈពេល 10 នាទីបន្ទាប់ពីការដំឡើង CloudFormation Stack បានបញ្ចប់។

#### ធនធាន AWS ដែលត្រូវត្រៀម?

**CloudFormation Template** នឹងត្រៀមធនធានខាងក្រោមសម្រាប់យើង៖

1. សេវាកម្ម EC2៖
   - 2 instances ដែលមានឈ្មោះថា `Compromised Instance`។
   - 1 instance ដែលមានឈ្មោះថា `Malicious Instance`។
2. សេវាកម្ម IAM៖
   - 1 **IAM Role** សម្រាប់ EC2 instance ជាមួយនឹងសិទ្ធិចូលប្រើប្រាស់ **SSM Parameter Store** និង **DynamoDB**។
3. សេវាកម្ម SNS៖
   - 1 **SNS Topic** សម្រាប់ផ្ញើការជូនដំណឹងតាមរយៈអ៊ីមែល។
4. សេវាកម្ម EventBridge៖
   - 3 **EventBridge Events Rules** សម្រាប់ដំណើរការការជូនដំណឹងនិងដំណោះស្រាយ។
5. សេវាកម្ម Lambda៖
   - 2 **Lambda Functions** សម្រាប់ដំណោះស្រាយភាពងាយរងគ្រោះ។
6. សេវាកម្ម SSM៖
   - 1 **SSM Parameter Store** សម្រាប់រក្សាទុកពាក្យសម្ងាត់សម្រាប់បរិយាកាស *TESTING*។