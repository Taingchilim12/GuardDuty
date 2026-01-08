---
title : "បង្កើត Finding"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 6.1 </b> "
---

**បង្កើត Finding ដោយដៃ**

ការវាយប្រហារក្លែងក្លាយនិង Finding នឹងត្រូវបានបង្កើតដោយស្វ័យប្រវត្តិពី CloudFormation Template លើកលែងតែ Finding មួយ។ សម្រាប់ Finding នេះ អ្នកនឹងត្រូវធ្វើជំហានជាក់លាក់មួយចំនួនដូចជា:
1. ចម្លង IAM security credential បណ្តោះអាសន្នពី EC2 instance។ 
2. អនុវត្តការហៅ API ពីកុំព្យូទ័រផ្ទាល់ខ្លួនដោយដៃ។

> ដើម្បីបង្កើត Finding នេះ យើងត្រូវតែអនុវត្តការហៅ API ពីក្រៅបណ្តាញ AWS។

**មាតិកា**
- [ទាញយក IAM security credential បណ្តោះអាសន្នជាមួយ Systems Manager](#ទាញយក-iam-security-credential-បណ្តោះអាសន្នជាមួយ-systems-manager)

#### ទាញយក IAM security credential បណ្តោះអាសន្នជាមួយ Systems Manager

ដើម្បីធ្វើការក្លែងក្លាយការវាយប្រហារចុងក្រោយនេះ អ្នកត្រូវតែទាញយក IAM security credential បណ្តោះអាសន្នដែលបង្កើតដោយ IAM role របស់ EC2 instance ឱ្យបានជោគជ័យ។ យើងអាចសាកល្បងវិធីមួយក្នុងចំណោមវិធីទាំងពីរខាងក្រោម:
1. ចូលប្រើ SSH ទៅកាន់ EC2 instance និងធ្វើការសាកសួរទិន្នន័យ **Metadata** របស់ EC2 instance។
2. ប្រើប្រាស់មុខងារ **Session Manager** របស់សេវាកម្ម AWS System Manager។
   1. ចូលទៅកាន់ System Manager Console នៅ **us-west-2**។
   2. នៅរបារនាំផ្លូវខាងឆ្វេង ជ្រើសរើស **Fleet Manager** យើងនឹងឃើញ *managed EC2 instance* មួយដែលមានទម្រង់ឈ្មោះដូចខាងក្រោម - `GuardDuty-Example: Compromised Instance: Scenario 3` ដែលមានស្ថានភាព **SSM Agent ping status** ជា `Online`។

![6-system-manager-fleet-manager](/images/6-system-manager-fleet-manager.png?featherlight=false&width=90pc)

   3. ធ្វើការប្រើប្រាស់មុខងារ **Session Manager** ដោយចុចប៊ូតុង `Instance actions` និងជ្រើសរើស `Start Session`។

![system-manager-fleet-manager-start-session](/images/6-system-manager-fleet-manager-start-session.png?featherlight=false&width=90pc)

   4. អនុវត្តពាក្យបញ្ជាសាកសួរទិន្នន័យ **Metadata**:

   ```bash
   curl http://169.254.169.254/latest/meta-data/iam/security-credentials/GuardDuty-Example-EC2-Compromised
   ```

3. ធ្វើការកត់ចំណាំព័ត៌មានសំខាន់មួយចំនួនដូចខាងក្រោម
   1. **Access Key ID**
   2. **Secret Access Key**
   3. **Session Token**

![system-manager-instance-session-start](/images/6-system-manager-instance-session-start.png?featherlight=false&width=90pc)

# ការបង្កើត AWS CLI Profile នៅលើកុំព្យូទ័រផ្ទាល់ខ្លួន

បន្ទាប់ពីទទួលបានជោគជ័យក្នុងការទាញយក IAM security credential បណ្តោះអាសន្ន យើងនឹងបង្កើត **AWS CLI Profile** មួយនៅលើកុំព្យូទ័រផ្ទាល់ខ្លួន។ ពី Terminal/CMD/PowerShell យើងនឹងជំនួស `<PLACEHOLDER>` ដោយតម្លៃជាក់លាក់ដែលបានប្រមូល រួចបញ្ចូលពាក្យបញ្ជាដូចខាងក្រោម៖

```bash
aws configure set profile.badbob.region us-west-2
aws configure set profile.badbob.aws_access_key_id <ACCESS_KEY_ID>
aws configure set profile.badbob.aws_secret_access_key <SECRET_ACCESS_KEY>
aws configure set profile.badbob.aws_session_token <SESSION_TOKEN>
```

> យើងអាចត្រួតពិនិត្យមើលថាតើ Profile ត្រូវបានបង្កើតហើយឬនៅ ដោយចូលទៅកាន់[កន្លែងផ្ទុក **AWS security credentials**](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html#cli-configure-files-where)។

យើងអាចប្រើពាក្យបញ្ជាខាងក្រោមដើម្បីពិនិត្យមើលយ៉ាងរហ័សថាតើមាន **AWS CLI Profile** ឈ្មោះ `badbob` ហើយឬនៅ។

```bash
aws configure --profile badbob
```

![aws-cli-configure-profile](/images/6-aws-cli-configure-profile.png?featherlight=false&width=90pc)

# ការប្រតិបត្តិពាក្យបញ្ជា AWS CLI ដោយប្រើ IAM security credential បណ្តោះអាសន្ន

ដោយប្រើពាក្យបញ្ជា **AWS CLI** ខាងក្រោម យើងនឹងហៅ API ទៅកាន់សេវាកម្ម AWS ផ្សេងៗ។

---
**តើ IAM user មានសិទ្ធិអ្វីខ្លះ?**

```bash
aws iam get-user --profile badbob
aws iam create-user --user-name Chuck --profile badbob
```

---
**តើមានសិទ្ធិចូលប្រើ DynamoDB ដែរឬទេ?**

```bash
aws dynamodb list-tables --profile badbob
aws dynamodb describe-table --table-name GuardDuty-Example-Customer-DB --profile badbob
```

---
**តើមានសិទ្ធិស្វែងរកទិន្នន័យពី DynamoDB ដែរឬទេ?**

```bash
aws dynamodb scan --table-name GuardDuty-Example-Customer-DB --profile badbob
aws dynamodb put-item --table-name GuardDuty-Example-Customer-DB --item '{"name":{"S":"Joshua Tree"},"state":{"S":"Michigan"},"website":{"S":"https://www.nps.gov/yell/index.htm"}}' --profile badbob
aws dynamodb scan --table-name GuardDuty-Example-Customer-DB --profile badbob
aws dynamodb delete-table --table-name GuardDuty-Example-Customer-DB --profile badbob
aws dynamodb list-tables --profile badbob
```

---
**តើអាចចូលប្រើ System Manager Parameter Store បានដែរឬទេ?**

```bash
aws ssm describe-parameters --profile badbob
aws ssm get-parameters --names "gd_prod_dbpwd_sample" --profile badbob
aws ssm get-parameters --names "gd_prod_dbpwd_sample" --with-decryption --profile badbob
aws ssm delete-parameter --name "gd_prod_dbpwd_sample" --profile badbob
```