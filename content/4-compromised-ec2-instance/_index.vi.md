---
title : "Compromised EC2 Instance"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

**ករណីទី១៖ ម៉ាស៊ីន EC2 ត្រូវបានគ្រប់គ្រង**

ដូចធម្មតា អ្នកមកការិយាល័យមុនម៉ោងនៅព្រឹកថ្ងៃច័ន្ទ រៀបចំកាហ្វេមួយពែង អង្គុយនៅកន្លែងរបស់អ្នក បើកកុំព្យូទ័រយួរដៃ ហើយចាប់ផ្តើមកិច្ចការប្រចាំថ្ងៃដូចជាផ្ញើអ៊ីមែល និងសរសេរផែនការ។ ភ្លាមៗនោះ អ្នកចាប់ផ្តើមទទួលបានអ៊ីមែលជាបន្តបន្ទាប់ដែលមានខ្លឹមសារទាក់ទងនឹងការរកឃើញថ្មីៗចំពោះគ្រោះថ្នាក់ដែលអ្នកមិនធ្លាប់ឃើញពីមុនមក។ ប៉ុន្តែអ្នកចាប់ផ្តើមស៊ើបអង្កេតភ្លាមៗ។ អ្វីដែលល្អនោះគឺមិត្តរួមការងាររបស់អ្នក Alice បានរៀបចំប្រតិកម្មស្វ័យប្រវត្តិសម្រាប់ការរកឃើញទាំងនោះ ដូច្នេះវាត្រូវបានដោះស្រាយភ្លាមៗ។

អ៊ីមែលដំបូងដែលអ្នកទទួលបានទាក់ទងនឹងម៉ាស៊ីន EC2 របស់អ្នកដែលអាចត្រូវបានគ្រប់គ្រងមានដូចខាងក្រោម៖

```text
GuardDuty Finding | ID: 1xx: The EC2 instance i-xxxxxxxxx may be compromised and should be investigated
```

អ៊ីមែលទីពីរដែលអ្នកទទួលបានភ្លាមៗបន្ទាប់ពីនោះដែលស្ថានភាពត្រូវបានដោះស្រាយភ្លាមៗមានដូចខាងក្រោម៖

```text
GuardDuty Remediation | ID: 1xx: GuardDuty discovered an EC2 instance (Instance ID: i-xxx) that is communicating outbound with an IP Address on a threat list that you uploaded. All security groups have been removed and it has been isolated. Please follow up with any additional remediation actions.
```

**មាតិកា**
- [ទិដ្ឋភាពទូទៅនៃស្ថាបត្យកម្ម](#ទិដ្ឋភាពទូទៅនៃស្ថាបត្យកម្ម)
- [ដំណើរការស៊ើបអង្កេត](#ដំណើរការស៊ើបអង្កេត)
- [សំណួររំលឹក](#សំណួររំលឹក)

#### ទិដ្ឋភាពទូទៅនៃស្ថាបត្យកម្ម
![architecture-overview](/images/4-architecture-overview.png?featherlight=false&width=60pc)

1. *ម៉ាស៊ីន EC2 ដែលត្រូវបានគ្រប់គ្រង* បញ្ជូនកញ្ចប់ ping ទៅកាន់អាសយដ្ឋាន EIP របស់ម៉ាស៊ីន EC2 គ្រោះថ្នាក់។ អាសយដ្ឋាន EIP នោះត្រូវបានបន្ថែមនៅក្នុង **បញ្ជីគំរាមកំហែងផ្ទាល់ខ្លួន**។

2. GuardDuty តាមដានកំណត់ហេតុ VPC Flow (រួមទាំងកំណត់ហេតុ CloudTrail និង DNS) និងវិភាគស្ថានភាពដោយផ្អែកលើការរៀនម៉ាស៊ីន **បញ្ជីគំរាមកំហែងផ្ទាល់ខ្លួន** និងមូលដ្ឋានផ្សេងទៀត។

3. GuardDuty បង្កើតការរកឃើញនិងបង្ហាញនៅលើកុងសូល GuardDuty និងផ្ញើព្រឹត្តិការណ៍នេះទៅ EventBridge Events។

4. ដោយផ្អែកលើព្រឹត្តិការណ៍នេះ វិធាន EventBridge Event ធ្វើប្រតិកម្មនិងជំរុញទាំង SNS Topic និងមុខងារ Lambda ដែលពាក់ព័ន្ធ។

5. SNS Topic នឹងផ្ញើអ៊ីមែលជាមួយនឹងព័ត៌មានលម្អិតអំពីការរកឃើញដល់អ្នក។

6. មុខងារ Lambda នឹងដាក់ដាច់ដោយឡែក *ម៉ាស៊ីន EC2 ដែលត្រូវបានគ្រប់គ្រង*។

នៅពេល Alice រៀបចំការជូនដំណឹងតាមអ៊ីមែលសម្រាប់ព្រឹត្តិការណ៍នេះ នាងគ្រាន់តែបន្ថែមព័ត៌មានជាក់លាក់មួយចំនួនអំពីការរកឃើញនោះ និងកំណត់រចនាសម្ព័ន្ធមុខងារ Lambda ដើម្បីដាក់ដាច់ដោយស្វ័យប្រវត្តិនូវ *ម៉ាស៊ីន EC2 ដែលត្រូវបានគ្រប់គ្រង*។ ទោះបីជាការរកឃើញត្រូវបានដោះស្រាយក៏ដោយ អ្នកនៅតែសម្រេចចិត្តស្វែងយល់បន្ថែមអំពីការរៀបចំនិងការកំណត់រចនាសម្ព័ន្ធបច្ចុប្បន្នរបស់ Alice។


#### ដំណើរការស៊ើបអង្កេត

---

**ការចូលប្រើប្រាស់ GuardDuty Console**

ទោះបីជាអ្នកអាចមើលឃើញ Findings ទាំងនេះពី GuardDuty Console ក៏ដោយ អតិថិជនភាគច្រើនចង់ប្រមូលផ្តុំពួកវាពី AWS Regions និង AWS Accounts ទៅក្នុងប្រព័ន្ធគ្រប់គ្រងទិន្នន័យសន្តិសុខកណ្តាល (**SIEM**) ដើម្បីធ្វើការវិភាគ និងអនុវត្តដំណើរការ Remediations។ វិធីសាស្រ្តដែលពេញនិយមបំផុតគឺការកំណត់រចនាសម្ព័ន្ធ GuardDuty ក្រោមគំរូ `Admin/Member` និងប្រើប្រាស់ដំណើរការរួមបញ្ចូលគ្នារវាង EventBridge Event Rules និង Lambda Function ដើម្បីបញ្ជូន Findings ទាំងនេះទៅកាន់ **SIEM** ឬ **Centralized Logging Framework**។ លើសពីនេះទៅទៀត មានដំណោះស្រាយមួយចំនួនមកពីដៃគូរបស់ AWS ដែលជួយឱ្យអតិថិជនអនុវត្តការបញ្ចូលគ្នា និងការបញ្ជូនទិន្នន័យបានយ៉ាងងាយស្រួលបំផុត។

1. ចូលទៅកាន់ GuardDuty Console នៅ **us-west-2**
2. យើងនឹងឃើញ Finding មួយដែលមានទម្រង់ដូចខាងក្រោម `UnauthorizedAccess:EC2/MaliciousIPCaller.Custom`។

![findings-ec2-malicous-ip-caller](/images/4-findings-ec2-malicous-ip-caller.png?featherlight=false&width=90pc)

3. ប្រសិនបើគ្មាន Finding ណាមួយទេ សូមចុចប៊ូតុង Refresh និងរង់ចាំ។

> អ្នកប្រើប្រាស់អាចទាញយក Findings ពី GuardDuty console ក្នុងរយៈពេល 90 ថ្ងៃ។

> ផ្អែកលើទម្រង់ដែលបានពិនិត្យលម្អិតនៅផ្នែកមុន តើអ្នកអាចកំណត់បញ្ហាសន្តិសុខជាក់លាក់ណាមួយតាមរយៈប្រភេទ Finding នេះ?

1. នៅក្នុងបរិស្ថានរបស់អ្នក ប្រភេទ Finding នេះបង្ហាញថា EC2 instance កំពុងធ្វើការទំនាក់ទំនងទៅកាន់អាសយដ្ឋាន IP (ដែលត្រូវបានបន្ថែមទៅក្នុង **Threat Lists**)។
2. ជ្រើសរើស **Lists** នៅលើរបារបញ្ជាការ (ខាងឆ្វេង) ដើម្បីមើល **Threat List** ដែល Alice បានបន្ថែមពីមុន - `Example-Threat-List`។

![guardduty-lists](/images/4-guardduty-lists.png?featherlight=false&width=90pc)

> GuardDuty ប្រើប្រាស់ប្រព័ន្ធ Threat Intelligence ដែលផ្តល់ដោយក្រុម AWS Security និងភាគីទីបី ដូចជា *ProofPoint* និង *CrowdStike*។ អ្នកអាចពង្រីកការមើលឃើញរបស់ GuardDuty ដោយការកំណត់រចនាសម្ព័ន្ធបញ្ជី IP ដែលទុកចិត្តបាន (**Trusted IP Lists**) និងបញ្ជីគំរាមកំហែង (**Threat Lists**) ដោយខ្លួនឯង។ ប្រសិនបើអ្នកបានរៀបចំ GuardDuty តាមរចនាសម្ព័ន្ធ Admin/Member ពីគណនី GuardDuty Admin អ្នកអាចគ្រប់គ្រងបញ្ជីខាងលើ និងអនុញ្ញាតឱ្យគណនី Members ទទួលបានការកំណត់ទាំងនោះ។ ជាលំនាំដើម គណនី Members នឹងមិនមានសិទ្ធិកែប្រែបញ្ជីទាំងនេះទេ។

---

**ពិនិត្យមើល EventBridge Event Rule**

Alice ប្រើប្រាស់ EventBridge Event Rules ដើម្បីផ្ញើការជូនដំណឹងអំពី Findings និងជំហានក្នុងដំណើរការ Remediations។ យើងនឹងពិនិត្យមើលលម្អិតថាតើ Alice បានរៀបចំអ្វីខ្លះ និងរបៀបដែលដំណើរការនេះដំណើរការ។

1. ចូលទៅកាន់ EventBridge Console នៅក្នុង **us-west-2**
2. នៅក្នុងរបារនាំផ្លូវខាងឆ្វេង ក្រោម **Events** ជ្រើសរើស **Rules**។ អ្នកនឹងឃើញច្បាប់ចំនួន 3 ដែលត្រូវបានបង្កើត (ដោយ CloudFormation Template) ដែលចាប់ផ្តើមដោយបុព្វបទ `GuardDuty-Event`។

![eventbridge-events-rules](/images/4-eventbridge-events-rules.png?featherlight=false&width=90pc)

3. ជ្រើសរើសច្បាប់ដែលមានឈ្មោះ `GuardDuty-Event-EC2-MaliciousIPCaller`។

![eventbridge-event-ec2-malicious-ip-caller](/images/4-eventbridge-event-ec2-malicious-ip-caller.png?featherlight=false&width=90pc)

4. អ្នកនឹងឃើញគោលដៅចំនួន 2 នៅក្នុងផ្នែក **Targets**។
   1. **Lambda Function**
   2. **SNS Topic**: ផ្ញើការជូនដំណឹងតាមអ៊ីមែលទៅអ្នកដោយផ្អែកលើទិន្នន័យដែលផ្តល់ដោយ EventBridge Event Rule។ ជំនួសឱ្យការប្រើទិន្នន័យ JSON ទាំងអស់ Alice បានប្តូរខ្លឹមសារនៃការជូនដំណឹងដោយប្រើ **Input Transformer**។

![eventbridge-event-ec2-malicious-ip-caller-targets](/images/4-eventbridge-event-ec2-malicious-ip-caller-targets.png?featherlight=false&width=90pc)

---

**ពិនិត្យមើលដំណើរការ Remediation ដែលផ្អែកលើ Lambda Function**

Lambda Function គឺជាគន្លឹះដែលមានតក្កវិជ្ជាសម្រាប់អនុវត្តជំហាននៃដំណើរការ Remediations សម្រាប់ Findings។ Alice បានរៀបចំ Lambda Function ដើម្បីដកចេញ និងជំនួស **Security Group** នៃ *EC2 compromised instance* ដោយ **Security Group** ថ្មីដែលគ្មានច្បាប់ `Ingress/Egress` ណាមួយ។ នេះនឹងជួយដាច់ *EC2 compromised instance* ចេញពីបណ្តាញបច្ចុប្បន្ន។

ដើម្បីពិនិត្យមើលដំណើរការ Remediation:
1. ពីច្បាប់ `GuardDuty-Event-EC2-MaliciousIPCaller` នៅក្នុងផ្នែក **Targets** ក្នុងប្រភេទ Lambda Function រកមើល **Resource Name** ដែលត្រូវគ្នា។

![eventbridge-event-ec2-malicious-ip-caller-targets-lambda](/images/4-eventbridge-event-ec2-malicious-ip-caller-targets-lambda.png?featherlight=false&width=90pc)

2. នៅក្នុង Lambda Function console ស្វែងរក **Resource Name** ពីជំហានមុន។

![lambda-Remediation-EC2MaliciousIPCaller](/images/4-lambda-Remediation-EC2MaliciousIPCaller.png?featherlight=false&width=90pc)

3. យើងអាចពិនិត្យមើលផ្នែកមួយចំនួន
   1. Configuration
      1. នៅក្នុងរបារ **Designer** យើងនឹងឃើញទំនាក់ទំនងជាមួយ EventBridge Event Rule។
      2. នៅក្នុងផ្នែក **Function code** តក្កវិជ្ជាកូដនឹងត្រូវបានប្រតិបត្តិនៅទីនេះ។
   2. Permissions
   3. Monitoring

![lambda-Remediation-EC2MaliciousIPCaller-overview](/images/4-lambda-Remediation-EC2MaliciousIPCaller-overview.png?featherlight=false&width=90pc)

---

**បញ្ជាក់ថាដំណើរការ Remediation បានជោគជ័យ**

ដើម្បីធានាថាលទ្ធផលនៃដំណើរការ Remediation យើងត្រូវពិនិត្យមើលថាតើ EC2 instance ត្រូវបានដាច់ដោយឡែកហើយឬនៅ។ នៅពេលនេះ អ្នកគួរតែបានទទួលអ៊ីមែលជាមួយនឹងព័ត៌មានសំខាន់មួយចំនួន។

1. ចូលទៅកាន់ EC2 console នៅក្នុង **us-west-2**។
2. ជ្រើសរើស `Instances (Running)` អ្នកនឹងឃើញ EC2 instance ចំនួន 3 ដែលចាប់ផ្តើមដោយបុព្វបទ `GuardDuty-Example`។

![ec2-running](/images/4-ec2-running.png?width=90pc)

3. ដោយផ្អែកលើ instance ID ពី GuardDuty Finding ឬការជូនដំណឹងតាមអ៊ីមែល យើងជ្រើសរើស EC2 instance ដែលត្រូវគ្នា - `GuardDuty-Example: Compromised Instance: Scenario 1`។

![guardduty-finding-MaliciousIPCaller-target](/images/4-guardduty-finding-MaliciousIPCaller-target.png?featherlight=false&width=90pc)

![ec2-compromised-scenario-1](/images/4-ec2-compromised-scenario-1.png?featherlight=false&width=90pc)

4. បន្ទាប់ពីដំណើរការ Remediation បានបញ្ចប់ យើងនឹងពិនិត្យមើល **Security Group** នៃ *EC2 compromised instance* នេះ ដែលនឹងមានឈ្មោះដូច `ForensicSecurityGroup`។
5. `ForensicSecurityGroup` នឹងគ្មានច្បាប់ `Ingress/Egress` ណាមួយដែលមាន IP address នៅក្នុង `Example-Threat-List`។

![ec2-compromised-scenario-1-security-group](/images/4-ec2-compromised-scenario-1-security-group.png?featherlight=false&width=90pc)

#### សំណួររំលឹក
1. តើប្រភពទិន្នន័យណាដែល GuardDuty បានប្រើដើម្បីកំណត់អត្តសញ្ញាណការគំរាមកំហែងនេះ?
2. តើការដាច់ដោយឡែកប៉ះពាល់ដល់កម្មវិធីដែលកំពុងដំណើរការនៅក្នុង EC2 instance នេះដែរឬទេ?
3. តើយើងអាចបន្ថែមព័ត៌មានលម្អិតបន្ថែមទៅក្នុងការជូនដំណឹងតាមអ៊ីមែលដោយរបៀបណា?