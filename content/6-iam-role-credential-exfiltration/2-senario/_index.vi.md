---
title : "ការលួចព័ត៌មានសម្ងាត់"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 6.2 </b> "
---

**មាតិកា**
- [ទិដ្ឋភាពរួមនៃរចនាសម្ព័ន្ធ](#ទិដ្ឋភាពរួមនៃរចនាសម្ព័ន្ធ)
- [ដំណើរការស៊ើបអង្កេត](#ដំណើរការស៊ើបអង្កេត)
- [សំណួររំលឹក](#សំណួររំលឹក)

#### ទិដ្ឋភាពរួមនៃរចនាសម្ព័ន្ធ
![architecture-overview](/images/6-architecture-overview.png?featherlight=false&width=60pc)

1. ម៉ាស៊ីនមេពីចម្ងាយចូលទៅកាន់ *EC2 compromised instance* និងលួចយក **IAM role credential** តាមរយៈទិន្នន័យ **Metadata**។
2. ម៉ាស៊ីនមេនេះបង្កើត AWS CLI Profile ដើម្បីធ្វើការហៅ API ទៅកាន់គណនី AWS។
3. GuardDuty បង្កើត Findings ពាក់ព័ន្ធ និងផ្ញើទៅកាន់ GuardDuty console និង EventBridge Events។
4. EventBridge Event Rule ជំរុញឱ្យដំណើរការ SNS Topic និង Lambda Function។
5. SNS Topic ធ្វើការផ្ញើការជូនដំណឹងតាមអ៊ីមែលជាមួយនឹងព័ត៌មានលម្អិតនៃ Finding។
6. Lambda Function ធ្វើការកំណត់គោលការណ៍ថ្មីដើម្បីដកហូត Sessions ដែលកំពុងដំណើរការទាំងអស់។

#### ដំណើរការស៊ើបអង្កេត

---
**ចូលទៅកាន់ GuardDuty Console**

ដើម្បីពិនិត្យមើល Findings:
1. ចូលទៅកាន់ GuardDuty Console នៅ **us-west-2**
2. យើងនឹងឃើញ Findings មានទម្រង់ដូចខាងក្រោម - `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration`។

![guardduty-finding-unauthorized-iam-instance-credential-exfiltration](/images/6-guardduty-finding-unauthorized-iam-instance-credential-exfiltration.png?featherlight=false&width=90pc)

3. ប្រសិនបើគ្មាន Finding ណាមួយ សូមចុចប៊ូតុង Refresh និងរង់ចាំ។
4. ពី Finding - `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration` យើងអាចទាញយកព័ត៌មានមួយចំនួនដូចខាងក្រោម:
   1. **កម្រិតគ្រោះថ្នាក់ខ្ពស់**
   2. ការជូនដំណឹងថាមាននរណាម្នាក់កំពុងព្យាយាមប្រើប្រាស់ IAM role credential នៅខាងក្រៅ EC2 instance

![guardduty-finding-unauthorized-iam-instance-credential-exfiltration-details](/images/6-guardduty-finding-unauthorized-iam-instance-credential-exfiltration-details.png?featherlight=false&width=90pc)

> GuardDuty Finding នីមួយៗនឹងត្រូវបានកំណត់កម្រិតគ្រោះថ្នាក់ជាក់លាក់ - ទាប/មធ្យម/ខ្ពស់។ កម្រិតទាំងនេះត្រូវបានកំណត់ដោយ AWS ដែលត្រូវបានប្រើប្រាស់ដើម្បីចាត់ថ្នាក់និងកំណត់អត្តសញ្ញាណ

---
**ពិនិត្យមើល EventBridge Event Rule**

1. ចូលទៅកាន់ CloudWatch Console នៅ **us-west-2**។
2. នៅរបារនាំផ្លូវខាងឆ្វេង ក្រោម **Events** ជ្រើសរើស **Rules**។ អ្នកនឹងឃើញច្បាប់ចំនួន 3 ត្រូវបានបង្កើត (ដោយ CloudFormation Template) ដែលចាប់ផ្តើមដោយបុព្វបទដូចខាងក្រោម `GuardDuty-Event.`។
3. ជ្រើសរើសច្បាប់ដែលមានឈ្មោះ `GuardDuty-Event-IAMUser-InstanceCredentialExfiltration`។

![eventbridge-event-iam-credential-exfiltration](/images/6-eventbridge-event-iam-credential-exfiltration.png?featherlight=false&width=90pc)

4. នៅក្នុងផ្នែក **Event Pattern** យើងអាចមើលឃើញប្រភពទិន្នន័យដែល Event នេះនឹងកត់ត្រា និងជំរុញឱ្យដំណើរការ **Target** នៅពេលមានព្រឹត្តិការណ៍ណាមួយកើតឡើង។

![eventbridge-iam-exfiltration-event-pattern-targets](/images/6-eventbridge-iam-exfiltration-event-pattern-targets.png?featherlight=false&width=90pc)

> អ្នកអាចបង្កើត EventBridge Event Rule ដើម្បីកត់ត្រាព្រឹត្តិការណ៍នៃ Finding ជាក់លាក់ឬ Finding ណាមួយ។

ខាងក្រោមនេះគឺជាឧទាហរណ៍ដើម្បីកត់ត្រាព្រឹត្តិការណ៍ណាមួយដែលជា GuardDuty Findings។
```
{
  "detail-type": [
    "GuardDuty Finding"
  ],
  "source": [
    "aws.guardduty"
  ]
}
```

---
**ពិនិត្យដំណើរការ Remediation ជាមួយ Lambda Function**

Alice បានរៀបចំដំណើរការ Remediation ដើម្បីឆ្លើយតបដោយស្វ័យប្រវត្តិទៅនឹងការគំរាមកំហែងនេះតាមរយៈ Lambda function។ យើងអាចពិនិត្យមើលកូដដែលបានសរសេរដើម្បីយល់ដឹងបន្ថែមអំពីដំណើរការនេះ។

1. ចូលទៅកាន់ Lambda Console នៅ **us-west-2**។
2. នៅបន្ទាត់រុករកខាងឆ្វេង ជ្រើសរើស **Functions** ហើយស្វែងរក `GuardDuty-Example-Remediation-InstanceCredentialExfiltration`។

![lambda-function](/images/6-lambda-function.png?featherlight=false&width=90pc)

3. ជាមូលដ្ឋាន Lambda function នេះនឹងទាញយកព័ត៌មានអំពី IAM Role ពី Finding និងបន្ថែម IAM Policy។

![lambda-function-code](/images/6-lambda-function-code.png?featherlight=false&width=90pc)

> តើ Permissions អ្វីខ្លះដែល Lambda Function ត្រូវការដើម្បីអនុវត្តដំណើរការ Remediation? តើមានហានិភ័យអ្វីខ្លះដែលអាចកើតឡើងជាមួយនឹងកម្រិត Permissions បច្ចុប្បន្ន?

---
**ផ្ទៀងផ្ទាត់ដំណើរការ Remediation**

ដើម្បីធ្វើការផ្ទៀងផ្ទាត់ថា Finding `InstanceCredentialExfiltration` ត្រូវបានដោះស្រាយទាំងស្រុង យើងនឹងធ្វើតាមជំហានដូចខាងក្រោម។

- **ផ្ទៀងផ្ទាត់តាមរយៈ AWS CLI**

អនុវត្តពាក្យបញ្ជាខាងក្រោម:

```bash
aws dynamodb list-tables --profile badbob
```

យើងនឹងទទួលបាន `AccessDeniedException` សម្រាប់ការអនុវត្តពាក្យបញ្ជា។

![aws-cli-dynamodb-access-denied-exception](/images/6-aws-cli-dynamodb-access-denied-exception.png?featherlight=false&width=90pc)

- **ផ្ទៀងផ្ទាត់តាមរយៈ AWS Console**

ធ្វើការវាយតម្លៃ IAM Policy ដែលត្រូវបានបន្ថែមទៅ IAM Role ក្នុងដំណើរការ Remediation។
1. ចូលទៅកាន់ IAM Console។
2. នៅបន្ទាត់រុករកខាងឆ្វេង ជ្រើសរើស **Roles** ហើយស្វែងរក `GuardDuty-Example-EC2-Compromised`។ នេះគឺជា IAM Role ដែលយើងនឹងកំណត់តាមរយៈ GuardDuty Finding។

![iam-role](/images/6-iam-role.png?featherlight=false&width=90pc)

3. ចុចលើផ្ទាំង **Permissions** ចុចលើគោលការណ៍ IAM Policy `RevokeOldSessions`។

![iam-role-permissions](/images/6-iam-role-permissions.png?featherlight=false&width=90pc)

#### សំណួររំលឹក
1. តើមានហានិភ័យពាក់ព័ន្ធអ្វីខ្លះដែលកើតឡើងក្នុងដំណើរការ Remediation?
2. តើមាន EC2 instances ផ្សេងទៀតដែលប្រើប្រាស់ IAM Role នេះដែរឬទេ?