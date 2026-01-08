---
title : "លិខិតស្នាមព័ត៌មាន IAM ដែលត្រូវបានគេប្រើប្រាស់ដោយមិនត្រឹមត្រូវ"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

**ករណីទី២៖ លិខិតស្នាមព័ត៌មាន IAM ដែលត្រូវបានគេប្រើប្រាស់ដោយមិនត្រឹមត្រូវ**

អ្នកបានបញ្ចប់ករណីវាយប្រហារសាកល្បងដំបូង ហើយត្រឡប់មកញ៉ាំកាហ្វេរបស់អ្នកវិញ។ ទោះជាយ៉ាងណាក៏ដោយ អ្នកបានទទួលការជូនដំណឹងថ្មីៗទាក់ទងនឹងរកឃើញសេវាកម្ម AWS IAM។ ការជូនដំណឹងដំបូងបង្ហាញថា ដោយប្រើ **លិខិតស្នាម IAM** មាន **API calls** មួយចំនួនត្រូវបានធ្វើឡើងពីអាសយដ្ឋាន IP ដែលត្រូវបានបន្ថែមទៅក្នុង **បញ្ជីការគំរាមកំហែង** (នៅមេរៀនមុន)។

**មាតិកា**
- [ទិដ្ឋភាពរួមនៃរចនាសម្ព័ន្ធ](#ទិដ្ឋភាពរួមនៃរចនាសម្ព័ន្ធ)
- [ដំណើរការស៊ើបអង្កេត](#ដំណើរការស៊ើបអង្កេត)
- [សំណួររំលឹក](#សំណួររំលឹក)

#### ទិដ្ឋភាពរួមនៃរចនាសម្ព័ន្ធ
![architecture-overview](/images/5-architecture-overview.png?featherlight=false&width=60pc)

1. *EC2 instance មានគ្រោះថ្នាក់* នេះបានធ្វើ **API calls**, EIP របស់ instance នេះត្រូវបានបន្ថែមទៅក្នុង **បញ្ជីការគំរាមកំហែង**។ ខ្លឹមសារនៃ **API calls** ត្រូវបានរក្សាទុកនៅក្នុង CloudTrail។
2. GuardDuty តាមដានកំណត់ត្រា **CloudTrail Logs** ជាមួយនឹង **VPC Flow Logs** និង **DNS Logs** ដើម្បីវាយតម្លៃស្ថានភាពដោយផ្អែកលើលក្ខណៈវិនិច្ឆ័យជាក់លាក់។
3. GuardDuty បង្កើតការរកឃើញដែលត្រូវគ្នា និងផ្ញើព័ត៌មានលម្អិតទៅកាន់ GuardDuty Console និង EventBridge Events។
4. **EventBridge Event Rule** ដំណើរការ **SNS Topic**។
5. **SNS Topic** ផ្ញើការជូនដំណឹងតាមអ៊ីមែលជាមួយនឹងព័ត៌មានពាក់ព័ន្ធ។

#### ដំណើរការស៊ើបអង្កេត

---

**ចូលទៅកាន់ GuardDuty Console**

ដើម្បីពិនិត្យមើលការរកឃើញ៖
1. ចូលទៅកាន់ GuardDuty Console នៅ **us-west-2**
2. យើងនឹងឃើញការរកឃើញដែលមានទម្រង់ដូចខាងក្រោម៖
   1.  `Recon:IAMUser`
   2.  `UnauthorizedAccess:IAMUser`

![guardduty-finding-recon-iamuser](/images/5-guardduty-findings.png?featherlight=false&width=90pc)

3. បើសិនជាគ្មានការរកឃើញណាមួយ សូមចុចប៊ូតុង Refresh និងរង់ចាំ។
4. ពីការរកឃើញ - `Recon:IAMUser/MaliciousIPCaller.Custom` យើងអាចទាញយកព័ត៌មានមួយចំនួនដូចខាងក្រោម៖
   1. តើមានអ្វីកើតឡើង?
   2. តើធនធាន AWS ណាខ្លះដែលរងផលប៉ះពាល់?
   3. តើព្រឹត្តិការណ៍នេះកើតឡើងនៅពេលណា?
5. នៅក្រោមផ្នែក **Resource Affected** អ្នកនឹងរកឃើញ `User Name` ដែលទាក់ទងនឹងការរកឃើញនេះ។

![guardduty-finding-recon-iamuser-affected-resources](/images/5-guardduty-finding-recon-iamuser-affected-resources.png?featherlight=false&width=90pc)

> ដោយផ្អែកលើទម្រង់ដែលបានពិនិត្យយ៉ាងល្អិតល្អន់នៅផ្នែកមុន តើអ្នកអាចកំណត់បញ្ហាសន្តិសុខជាក់លាក់តាមរយៈប្រភេទនៃការរកឃើញបានយ៉ាងដូចម្តេច?

ការរកឃើញនេះបង្ហាញថា **លិខិតស្នាម IAM** របស់ `User Name` ខាងលើប្រហែលជាត្រូវបានបង្ហាញដោយ **API calls** ដែលមកពីអាសយដ្ឋាន IP ដែលត្រូវបានបន្ថែមទៅក្នុង **បញ្ជីការគំរាមកំហែង** ពីមុន។

> តើសកម្មភាពអ្វីខ្លះដែល IAM User នេះបានអនុវត្ត?

នៅក្នុងផ្នែក **Action** យើងឃើញថាសកម្មភាព `DescribeParameters` ត្រូវបានអនុវត្ត។

![guardduty-finding-recon-iamuser-action](/images/5-guardduty-finding-recon-iamuser-action.png?featherlight=false&width=90pc)

> តើយើងអាចមើលឃើញសកម្មភាពផ្សេងទៀតទាំងអស់ដែលត្រូវបានអនុវត្តដោយ IAM User នេះក្នុងរយៈពេល 1 ម៉ោងមុន ឬ 1 ថ្ងៃមុនដោយរបៀបណា?

GuardDuty អាចវិភាគទិន្នន័យយ៉ាងច្រើនដើម្បីកំណត់ការគំរាមកំហែងនៅក្នុងបរិស្ថានរបស់អ្នក។ ទោះយ៉ាងណាក៏ដោយ នៅក្នុងដំណើរការស៊ើបអង្កេត និងជំហានក្នុងការដោះស្រាយ យើងត្រូវរួមបញ្ចូលប្រភពទិន្នន័យផ្សេងៗគ្នាដើម្បីទទួលបានទស្សនៈយ៉ាងទូលំទូលាយបំផុត។

ក្នុងករណីនេះ អ្នកវិភាគអាចប្រើប្រាស់ព័ត៌មានលម្អិតដែលអាចរកបាននៅក្នុងកំណត់ត្រាឥរិយាបថអ្នកប្រើប្រាស់តាមរយៈ **CloudTrail**។

![cloudtrail-event-history](/images/5-cloudtrail-event-history.png?featherlight=false&width=90pc)

---
**ការពិនិត្យមើល EventBridge Event Rule**

1. ចូលទៅកាន់ CloudWatch Console នៅក្នុង **us-west-2**។
2. នៅក្នុងរបារនាំផ្លូវខាងឆ្វេង ក្រោម **Events** ជ្រើសរើស **Rules**។ អ្នកនឹងឃើញច្បាប់ចំនួន 3 ដែលត្រូវបានរៀបចំ (ដោយ CloudFormation Template) ដែលចាប់ផ្តើមជាមួយនឹងបុព្វបទដូចខាងក្រោម `GuardDuty-Event.`។
3. ជ្រើសរើសច្បាប់ដែលមានឈ្មោះ `GuardDuty-Event-IAMUser-MaliciousIPCaller`។

![eventbridge-event-iam-malicious-ip-caller](/images/5-eventbridge-event-iam-malicious-ip-caller.png?featherlight=false&width=90pc)

4. អ្នកនឹងឃើញយ៉ាងច្បាស់ថាមានតែគោលដៅមួយនៅក្នុងផ្នែក **Targets** គឺ **SNS Topic**។

![eventbridge-event-iam-malicious-ip-caller-targets](/images/5-eventbridge-event-iam-malicious-ip-caller-targets.png?featherlight=false&width=90pc)

ពិតណាស់ Alice មិនដែលបានរៀបចំ Lambda Function សម្រាប់ដំណើរការ Remediation ដោយសារក្រុមសន្តិសុខបានសម្រេចចិត្តថាពួកគេនឹងអនុវត្តដោយដៃចំពោះការរកឃើញនេះ។

> ការរួមបញ្ចូលគ្នារវាង **GuardDuty និង EventBridge Events** ផ្តល់នូវភាពបត់បែនដែលអនុញ្ញាតឱ្យយើងបង្កើតដំណើរការ Remediation ស្វ័យប្រវត្តិយ៉ាងងាយស្រួល។ Lambda function ឬដំណោះស្រាយពី [ដៃគូរបស់ AWS](https://aws.amazon.com/guardduty/resources/partners/) គឺជាជម្រើសល្អបំផុត។

សម្រាប់ការរកឃើញជាក់លាក់មួយចំនួន យើងអាចកំណត់រចនាសម្ព័ន្ធការជូនដំណឹងនិងដោះស្រាយបញ្ហាដោយដៃ ជំនួសឱ្យការធ្វើស្វ័យប្រវត្តិកម្ម។ នៅពេលរៀបចំដំណើរការស្វ័យប្រវត្តិកម្ម យើងត្រូវប្រុងប្រយ័ត្នខ្ពស់និងវាយតម្លៃលទ្ធផលដែលដំណើរការ Remediation នាំមកនូវគុណសម្បត្តិនិងគុណវិបត្តិ។

> លើសពីនេះទៀត យើងអាចរៀបចំ **Target** សម្រាប់ធនធាន AWS ផ្សេងទៀតដូចជា **SSM Run Commands** ឬ **Step Function State Machine**។

---

**ការដោះស្រាយស្ថានការណ៍**

ដោយសារតែ Alice មិនដែលបានរៀបចំដំណើរការ Remediation សម្រាប់ការរកឃើញនេះ យើងត្រូវតែអនុវត្តដោយដៃ។ ខណៈពេលដែលក្រុមសន្តិសុខកំពុងវិភាគអាកប្បកិរិយារបស់អ្នកប្រើប្រាស់ IAM នេះដើម្បីកំណត់វិសាលភាពនៃភាពងាយរងគ្រោះកាន់តែច្បាស់ យើងត្រូវអនុវត្តជំហានមួយចំនួនដើម្បីបិទ **Access Key** ដើម្បីបញ្ឈប់សកម្មភាពបន្ទាប់ភ្លាមៗ។

1. ចូលទៅកាន់ IAM Console។
2. នៅក្នុងរបារនាំផ្លូវខាងឆ្វេង ជ្រើសរើស **Users**។

![iam-users](/images/5-iam-users.png?width=90pc)

3. ផ្អែកលើ GuardDuty Finding និងការជូនដំណឹងតាមអ៊ីមែល យើងអាចជ្រើសរើសអ្នកប្រើប្រាស់ IAM យ៉ាងងាយស្រួល - `GuardDuty-Example-Compromised-Simulated`។
4. នៅក្នុងអ្នកប្រើប្រាស់ `GuardDuty-Example-Compromised-Simulated` យើងជ្រើសរើសផ្ទាំង **Security Credentials**។

![iam-users-compromised-simulated-credential](/images/5-iam-users-compromised-simulated-credential.png?featherlight=false&width=90pc)

5. នៅក្នុងផ្នែក **Access Keys** ផ្អែកលើព័ត៌មាន **Access Key ID** ពី Finding យើងចុចប៊ូតុង `Make Inactive`។

![iam-users-compromised-simulated-credential-deactivate](/images/5-iam-users-compromised-simulated-credential-deactivate.png?featherlight=false&width=90pc)

#### សំណួររំលឹក
1. តើប្រភពទិន្នន័យណាមួយដែល GuardDuty បានប្រើដើម្បីកំណត់អត្តសញ្ញាណគ្រោះថ្នាក់នេះ?
2. តើការអនុញ្ញាតណាមួយដែលអ្នកប្រើប្រាស់ IAM ត្រូវបានអនុញ្ញាត?
3. ហេតុអ្វីបានជាក្រុមសន្តិសុខសម្រេចចិត្តប្រឆាំងនឹងដំណើរការ Remediation ស្វ័យប្រវត្តិ?