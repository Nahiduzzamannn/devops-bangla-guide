# AWS Core Services: EC2 · S3 · VPC · IAM

```bash
$ aws sts get-caller-identity
```

**Amazon Web Services: ধারণা, CLI, বাস্তব পরিস্থিতি ও অনুশীলন**

- **IAM:** user, role, policy, least privilege, cross-account
- **VPC:** subnet, route table, IGW/NAT, SG বনাম NACL, SSM
- **EC2:** instance type, user data, EBS, ALB + Auto Scaling, খরচ
- **S3:** security, lifecycle, versioning, CloudFront website

| | |
|---|---|
| টপিক কার্ড | ২৯টি |
| হাতে-কলমে Lab | ৬টি |
| ইন্টারভিউ প্রশ্ন | ৬২টি |
| Incident Playbook | ৬টি |
| লেভেল | 🟢 বেসিক · 🟡 ইন্টারমিডিয়েট · 🔴 অ্যাডভান্সড |

---

## এই গাইড কীভাবে ব্যবহার করবে

বইটা সাজানো আছে যে ক্রমে AWS-এ আসলে কাজ শুরু করবে: আগে account নিরাপদ করা আর CLI, তারপর IAM (কে কী করতে পারবে), তারপর VPC (নেটওয়ার্ক, যেখানে সার্ভার বসবে), তারপর EC2 (সার্ভার), তারপর S3 (storage)। এরপর ৬টি হাতে-কলমে lab, খরচ নিয়ন্ত্রণ আর Well-Architected, incident playbook ও ইন্টারভিউ প্রশ্ন ব্যাংক।

প্রতিটি কার্ডে আছে **পূর্ণরূপ**, **সহজ ভাষায়** ব্যাখ্যা, **বাস্তব পরিস্থিতি**, AWS CLI কমান্ড ও তার **অপশনের অক্ষরে অক্ষরে ব্যাখ্যা**, **ভুল ও সমাধানের টেবিল**, আর **ইন্টারভিউ প্রশ্ন**।

> ⚠️ **সতর্কতা:** অনুশীলনের আগে Budget alert চালু করো আর প্রতিটি lab শেষে resource মুছে ফেলো। NAT Gateway, Elastic IP, বড় instance ভুলে গেলে বিল আসবে। Account ID, bucket নাম, ARN গুলো উদাহরণ; নিজের মান বসাবে। দাম ও free tier-এর নিয়ম সময়ে বদলায়, AWS-এর অফিসিয়াল পাতায় যাচাই করো।

---

## সূচিপত্র

- [০০. শুরুর আগে: Cloud, AWS-এর গঠন ও নিরাপদ শুরু](#০০-শুরুর-আগে-cloud-aws-এর-গঠন-ও-নিরাপদ-শুরু)
- [০১. IAM: কে কী করতে পারবে](#০১-iam-কে-কী-করতে-পারবে)
- [০২. VPC: AWS-এ তোমার নিজের নেটওয়ার্ক](#০২-vpc-aws-এ-তোমার-নিজের-নেটওয়ার্ক)
- [০৩. EC2: ভার্চুয়াল সার্ভার](#০৩-ec2-ভার্চুয়াল-সার্ভার)
- [০৪. S3: অসীম ফাইল ভাণ্ডার](#০৪-s3-অসীম-ফাইল-ভাণ্ডার)
- [০৫. AWS: Scenario ভিত্তিক অনুশীলন (Lab)](#০৫-aws-scenario-ভিত্তিক-অনুশীলন-lab)
- [০৬. খরচ ও Well-Architected: ভালো নকশার নীতি](#০৬-খরচ-ও-well-architected-ভালো-নকশার-নীতি)
- [০৭. বাস্তব Incident Playbook](#০৭-বাস্তব-incident-playbook)
- [০৮. ইন্টারভিউ প্রশ্ন ব্যাংক](#০৮-ইন্টারভিউ-প্রশ্ন-ব্যাংক)

---

## ০০. শুরুর আগে: Cloud, AWS-এর গঠন ও নিরাপদ শুরু

AWS-এ প্রথম দিনের কয়েকটা সিদ্ধান্ত (root account, MFA, billing alert) ভুল হলে পরে হাজার ডলারের বিল বা hack হওয়া account-এর ঝুঁকি। তাই আগে ভিত্তি।

### Cloud কী আর AWS কেন

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **AWS** | **A**mazon **W**eb **S**ervices → Amazon-এর cloud সেবা |
| **IaaS** | **I**nfrastructure **a**s **a** **S**ervice → ভাড়ার সার্ভার, নেটওয়ার্ক, storage (EC2, VPC, S3) |
| **PaaS** | **P**latform **a**s **a** **S**ervice → শুধু কোড দাও, platform চালায় (Elastic Beanstalk) |
| **SaaS** | **S**oftware **a**s **a** **S**ervice → তৈরি সফটওয়্যার (Gmail) |
| **Pay-as-you-go** | যতটুকু ব্যবহার ততটুকু বিল |

**সহজ ভাষায়:** Cloud মানে নিজের data center-এ সার্ভার না কিনে ইন্টারনেটের মাধ্যমে মিনিটে সার্ভার, storage, database ভাড়া নেওয়া, আর যতক্ষণ ব্যবহার ততক্ষণের টাকা দেওয়া। AWS বাজারের সবচেয়ে বড় cloud, ২০০-এর বেশি সার্ভিস। কিন্তু প্রায় সবকিছুর ভিত্তি চারটা: IAM (কে কী করতে পারবে), VPC (নেটওয়ার্ক), EC2 (সার্ভার), S3 (ফাইল)।

> **বাস্তব পরিস্থিতি:** একটা বাংলাদেশি স্টার্টআপ আগে ঢাকার একটা ডেটা সেন্টারে ২টা সার্ভার ভাড়া নিত, নতুন সার্ভার পেতে ২ সপ্তাহ লাগত। AWS-এ গিয়ে ৫ মিনিটে নতুন সার্ভার, ঈদের ভিড়ে বেশি সার্ভার, পরে আবার কমানো, আর শুধু সেই সময়ের টাকা।

| বিষয় | নিজের data center | AWS cloud |
|---|---|---|
| নতুন সার্ভার | সপ্তাহ, কেনা লাগে | মিনিট, API/console দিয়ে |
| খরচ | শুরুতে বড় বিনিয়োগ (CapEx) | ব্যবহার অনুযায়ী মাসিক (OpEx) |
| Scale | আগে থেকে বেশি কিনে রাখা | চাহিদা অনুযায়ী বাড়ানো-কমানো |
| বিশ্বজুড়ে | কঠিন | বিভিন্ন Region-এ কয়েক ক্লিকে |
| দায়িত্ব | সব নিজের | ভাগ করা (Shared Responsibility) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IaaS, PaaS আর SaaS-এর পার্থক্য উদাহরণসহ বলো।
**উত্তর:** IaaS-এ তুমি OS থেকে উপরে সব সামলাও (EC2)। PaaS-এ শুধু অ্যাপ আর data (Elastic Beanstalk, Lambda অনেকটা)। SaaS-এ শুধু ব্যবহার করো (Gmail, Salesforce)।

### Region, Availability Zone ও Edge location

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Region** | পৃথিবীর একটা ভৌগোলিক এলাকা, যেমন `ap-southeast-1` (সিঙ্গাপুর) |
| **AZ** | **A**vailability **Z**one → Region-এর ভেতরে আলাদা এক বা একাধিক data center, নিজস্ব বিদ্যুৎ ও নেটওয়ার্ক |
| **Edge location** | CloudFront-এর cache কেন্দ্র, user-এর কাছাকাছি |

**সহজ ভাষায়:** প্রতিটি Region-এ সাধারণত ৩টা বা বেশি AZ, কয়েক কিলোমিটার দূরে, যাতে একটায় আগুন বা বন্যা হলে অন্যগুলো চলে। High availability মানে অ্যাপ অন্তত ২টা AZ-এ চালানো। বেশিরভাগ সার্ভিস Region-ভিত্তিক (EC2, VPC, S3 bucket), কিছু global (IAM, Route 53, CloudFront)। Region বাছাই করো ব্যবহারকারীর কাছাকাছি (latency), আইন (data কোথায় থাকবে), খরচ আর সার্ভিসের প্রাপ্যতা দেখে।

> **বাস্তব পরিস্থিতি:** বাংলাদেশে AWS-এর Region নেই। বাংলাদেশি ব্যবহারকারীর জন্য সাধারণত `ap-south-1` (মুম্বাই) বা `ap-southeast-1` (সিঙ্গাপুর) বেছে নেওয়া হয়, ঢাকা থেকে latency মোটামুটি ৩০-৬০ ms। Static ফাইল CloudFront দিয়ে কাছের edge থেকে দেওয়া হয়।

```text
Region: ap-southeast-1 (Singapore)
┌───────────────┬───────────────┬───────────────┐
│ AZ: 1a        │ AZ: 1b        │ AZ: 1c        │
│ data center   │ data center   │ data center   │
│ [web][db-pri] │ [web][db-sby] │ [web]         │
└───────────────┴───────────────┴───────────────┘
        একটা AZ বন্ধ হলেও বাকিগুলো চলে
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Region আর Availability Zone-এর পার্থক্য?
**উত্তর:** Region একটা ভৌগোলিক এলাকা যেখানে একাধিক AZ আছে। AZ হলো Region-এর ভেতরে আলাদা বিদ্যুৎ, ঠান্ডা আর নেটওয়ার্কসহ বিচ্ছিন্ন data center (এক বা একাধিক)। HA-এর জন্য একাধিক AZ, disaster recovery-র জন্য একাধিক Region।

**প্রশ্ন:** IAM কি Region-ভিত্তিক?
**উত্তর:** না, IAM global; একবার তৈরি user/role সব Region-এ কাজ করে।

### Shared Responsibility ও নিরাপদ account সেটআপ

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Root user** | account খোলার email, সব ক্ষমতা |
| **MFA** | **M**ulti-**F**actor **A**uthentication → password-এর সাথে দ্বিতীয় প্রমাণ (phone app/key) |
| **Shared Responsibility** | shared responsibility model → AWS আর গ্রাহকের দায়িত্ব ভাগ |
| **Budget** | AWS Budgets → খরচের সীমা ও সতর্কতা |

**সহজ ভাষায়:** AWS-এর দায়িত্ব "cloud-এর নিরাপত্তা" (hardware, data center, virtualization)। তোমার দায়িত্ব "cloud-এর ভেতরের নিরাপত্তা" (IAM, security group, OS patch, data encryption, S3-কে public না রাখা)। Account খোলার পর প্রথম কাজ: root-এ MFA, root-এর access key কখনো না, দৈনন্দিন কাজের জন্য আলাদা admin (IAM Identity Center বা IAM user), billing alert, আর CloudTrail চালু।

> **বাস্তব পরিস্থিতি:** একজন ছাত্র GitHub-এ ভুল করে root access key push করল। কয়েক ঘণ্টার মধ্যে বট সেটা খুঁজে বের করে সব Region-এ বড় বড় GPU instance চালিয়ে crypto mining শুরু করল। পরদিন সকালে বিল ১২,০০০ ডলার। Root key না থাকলে, MFA আর budget alert থাকলে এটা ঠেকানো যেত।

| দায়িত্ব | AWS | তুমি (গ্রাহক) |
|---|---|---|
| Data center, hardware | ✅ | ❌ |
| Hypervisor, network infrastructure | ✅ | ❌ |
| EC2-এর OS patch | ❌ | ✅ |
| Security group, NACL | ❌ | ✅ |
| IAM user, policy, MFA | ❌ | ✅ |
| Data encryption আর S3 permission | সুবিধা দেয় | চালু ও ঠিক করা তোমার |

```text
প্রথম দিনের checklist:
[ ] Root user-এ MFA চালু, root-এর password নিরাপদে রাখো
[ ] Root-এর কোনো access key না (থাকলে মুছে দাও)
[ ] দৈনন্দিন কাজের জন্য IAM Identity Center/IAM admin, তাতেও MFA
[ ] Billing > Budgets: মাসিক সীমা (যেমন $20), ৮০%-এ email
[ ] CloudTrail: সব Region-এর API কার্যকলাপ log
[ ] IAM Access Analyzer আর Security Hub/Trusted Advisor-এ চোখ রাখো
[ ] অনুশীলনের পর resource মুছে ফেলার অভ্যাস
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Shared responsibility model ব্যাখ্যা করো।
**উত্তর:** AWS cloud-এর অবকাঠামোর নিরাপত্তার দায়িত্ব নেয় (physical, hardware, managed সার্ভিসের ভেতরের অংশ); গ্রাহক cloud-এর ভেতরে যা রাখে তার দায়িত্ব নেয় (IAM, network config, OS, অ্যাপ, data, encryption)। Managed সার্ভিস যত উঁচু স্তরের, গ্রাহকের দায়িত্ব তত কম।

**প্রশ্ন:** Root account দিয়ে কোন কাজগুলো করা উচিত?
**উত্তর:** শুধু সেই অল্প কাজ যা root ছাড়া হয় না (account বন্ধ করা, support plan বা কিছু billing সেটিং বদলানো); দৈনন্দিন কাজে কখনো না।

> 💡 **প্রো টিপ:** নতুন account-এ free tier আর credit থাকে, তবে নিয়ম সময়ে সময়ে বদলায়; aws.amazon.com/free-এ বর্তমান নিয়ম দেখে নাও আর budget alert অবশ্যই দাও।

### AWS CLI: সেটআপ ও মূল ব্যবহার

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CLI** | **C**ommand **L**ine **I**nterface → কমান্ড লাইন টুল |
| **ARN** | **A**mazon **R**esource **N**ame → প্রতিটি resource-এর পূর্ণ অনন্য নাম |
| **profile** | CLI profile → নামযুক্ত credential ও region সেট |
| **JMESPath** | JMES (James) + Path → `--query`-তে JSON থেকে অংশ বের করার ভাষা |

**সহজ ভাষায়:** Console-এ ক্লিক করা শেখার জন্য ভালো, কিন্তু DevOps-এ সবকিছু CLI, SDK (boto3) আর Terraform দিয়ে। CLI-এর গঠন: `aws <service> <কাজ> [অপশন]`। `--query` দিয়ে লম্বা JSON থেকে শুধু দরকারি অংশ নাও, `--output` দিয়ে format। একাধিক account বা role-এর জন্য profile ব্যবহার করো।

> **বাস্তব পরিস্থিতি:** তুমি dev আর prod দুই account-এ কাজ করো। ভুল account-এ resource মুছে ফেলা এড়াতে প্রতিটির আলাদা profile, আর প্রতিটি কমান্ডের আগে `aws sts get-caller-identity` দিয়ে নিশ্চিত হও কোন account-এ আছ।

```bash
aws --version
aws configure sso                       # IAM Identity Center (পছন্দনীয়)
aws configure --profile dev             # access key (শুধু শেখার জন্য)
aws sts get-caller-identity --profile dev
export AWS_PROFILE=dev
export AWS_REGION=ap-southeast-1

aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query "Reservations[].Instances[].[InstanceId,InstanceType,Tags[?Key=='Name']|[0].Value]" \
  --output table

aws s3 ls
aws ec2 describe-regions --query "Regions[].RegionName" --output text

# ARN-এর গঠন
# arn:aws:<service>:<region>:<account-id>:<resource>
# arn:aws:s3:::acme-backups
# arn:aws:iam::123456789012:role/web-ec2-role
# arn:aws:ec2:ap-southeast-1:123456789012:instance/i-0abc123
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `configure --profile dev` | named profile → `dev` নামে আলাদা credential/region সেট |
| `sts get-caller-identity` | `sts` = Security Token Service → আমি কে, কোন account, কোন user/role |
| `--filters Name=...,Values=...` | server-side filter → AWS-এর দিকেই বাছাই (দ্রুত) |
| `--query` | JMESPath query → JSON থেকে নির্দিষ্ট অংশ; `[]` = সব উপাদান, `[?...]` = শর্ত |
| `--output table/text/json` | output format → table মানুষের জন্য, text script-এর জন্য, json default |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `AWS_PROFILE` / `AWS_REGION` | environment variable দিয়ে default ঠিক করো |
| `Tags[?Key=='Name']\|[0].Value` | tag তালিকা থেকে Name tag-এর মান |
| `arn:aws:s3:::bucket` | S3 global, তাই region আর account ফাঁকা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `--query` আর `--filters`-এর পার্থক্য?
**উত্তর:** `--filters` AWS-এর server-এ বাছাই করে কম data পাঠায়; `--query` তোমার মেশিনে আসা JSON থেকে অংশ বের করে। বড় তালিকায় আগে filters।

**প্রশ্ন:** ARN কী?
**উত্তর:** Amazon Resource Name: যেকোনো AWS resource-এর অনন্য পূর্ণ নাম (partition, service, region, account, resource), IAM policy-তে Resource হিসেবে ব্যবহার হয়।

---

## ০১. IAM: কে কী করতে পারবে

**I**dentity and **A**ccess **M**anagement। AWS-এর প্রতিটি API call-এর আগে IAM প্রশ্ন করে: তুমি কে, তোমার কি এই কাজের অনুমতি আছে? IAM ভুল হলে বাকি সব নিরাপত্তা অর্থহীন।

### User, Group, Role ও Policy

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **IAM** | **I**dentity and **A**ccess **M**anagement → পরিচয় ও প্রবেশাধিকার ব্যবস্থাপনা |
| **User** | একজন মানুষ বা অ্যাপের স্থায়ী পরিচয় |
| **Group** | user-দের দল, policy একসাথে দেওয়া |
| **Role** | অস্থায়ী পরিচয় যা কেউ বা কোনো সার্ভিস "ধারণ" করে |
| **Policy** | JSON-এ লেখা অনুমতির নিয়ম |

**সহজ ভাষায়:** User হলো স্থায়ী পরিচয় (password বা access key)। Group-এ user রেখে একবারে অনুমতি দাও। Role-এর কোনো স্থায়ী password/key নেই; যে role ধারণ (assume) করে সে কয়েক ঘণ্টার অস্থায়ী credential পায়। EC2, Lambda, অন্য account, GitHub Actions সবাই role ব্যবহার করে। Policy হলো অনুমতির JSON, user/group/role-এ লাগানো হয়। আধুনিক নিয়ম: মানুষের জন্য IAM Identity Center (SSO), মেশিনের জন্য role; দীর্ঘমেয়াদি access key যত কম তত ভালো।

> **বাস্তব পরিস্থিতি:** নতুন developer join করল। তাকে আলাদা করে ১৫টা policy না দিয়ে `Developers` group-এ যোগ করা হলো। আর web সার্ভার (EC2)-কে S3 থেকে ছবি পড়তে access key না দিয়ে একটা role দেওয়া হলো, যাতে সার্ভারে কোনো স্থায়ী key-ই নেই।

| পরিচয় | credential | কখন |
|---|---|---|
| IAM User | password, দীর্ঘমেয়াদি access key | শুধু যখন বিকল্প নেই (পুরনো টুল) |
| Group | নিজের credential নেই | অনেক user-কে একই অনুমতি |
| Role | অস্থায়ী credential (STS) | EC2, Lambda, ECS, অন্য account, CI/CD (OIDC), SSO |
| IAM Identity Center user | SSO login, অস্থায়ী credential | মানুষের জন্য পছন্দনীয় উপায় |

```bash
aws iam create-group --group-name Developers
aws iam attach-group-policy --group-name Developers \
  --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
aws iam create-user --user-name rahim
aws iam add-user-to-group --user-name rahim --group-name Developers
aws iam list-attached-group-policies --group-name Developers
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--policy-arn` | policy ARN → কোন policy লাগাবে; `arn:aws:iam::aws:policy/...` = AWS managed |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IAM user আর IAM role-এর পার্থক্য?
**উত্তর:** User-এর স্থায়ী credential (password/access key) থাকে আর একজনের সাথে যুক্ত। Role-এর স্থায়ী credential নেই; যে অনুমোদিত সে assume করে অস্থায়ী credential পায়, সার্ভিস ও অন্য account-এর জন্য আদর্শ।

**প্রশ্ন:** EC2 থেকে S3-এ পৌঁছানোর নিরাপদ উপায় কী?
**উত্তর:** EC2-কে instance profile-এর মাধ্যমে IAM role দেওয়া; SDK/CLI নিজে metadata service থেকে অস্থায়ী credential নেয়, সার্ভারে কোনো access key রাখতে হয় না।

### Policy-এর গঠন ও evaluation logic

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Effect** | Allow বা Deny |
| **Action** | কোন API কাজ, যেমন `s3:GetObject` |
| **Resource** | কোন resource-এর উপর (ARN) |
| **Condition** | কখন প্রযোজ্য (IP, MFA, tag) |
| **Principal** | কার জন্য (resource-based policy-তে) |

**সহজ ভাষায়:** Policy-এর প্রতিটি Statement বলে: এই Effect, এই Action গুলো, এই Resource-এর উপর, এই Condition-এ। সিদ্ধান্তের নিয়ম: default-এ সব নিষেধ (implicit deny); কোথাও Allow থাকলে অনুমতি, কিন্তু কোথাও স্পষ্ট Deny থাকলে সেটাই জেতে, যত Allow থাকুক। Identity-based policy (user/role-এ লাগানো) আর resource-based policy (S3 bucket policy-এর মতো, Principal সহ) দুটো মিলে হিসাব হয়।

> **বাস্তব পরিস্থিতি:** Backup অ্যাপকে শুধু একটা bucket-এর একটা ফোল্ডারে ফাইল লেখার অনুমতি দিতে হবে, মুছতে পারবে না, আর শুধু অফিসের IP থেকে। একটা ছোট policy-তে পুরোটা।

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "WriteBackups",
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:GetObject"],
      "Resource": "arn:aws:s3:::acme-backups/db/*",
      "Condition": { "IpAddress": { "aws:SourceIp": "203.0.113.0/24" } }
    },
    {
      "Sid": "ListOnlyDbPrefix",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::acme-backups",
      "Condition": { "StringLike": { "s3:prefix": "db/*" } }
    },
    {
      "Sid": "NeverDelete",
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::acme-backups/*"
    }
  ]
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `"Version": "2012-10-17"` | policy ভাষার version; সবসময় এটাই লেখো (তারিখ নয়) |
| `Sid` | Statement-এর নাম, শুধু বোঝার জন্য |
| `Action: s3:PutObject` | `service:কাজ`; `*` দিয়ে সব (এড়িয়ে চলো) |
| `Resource: bucket/db/*` | object-এর জন্য `bucket/path/*`; bucket-এর নিজের কাজের (ListBucket) জন্য শুধু bucket ARN |
| `Condition IpAddress aws:SourceIp` | শুধু এই IP রেঞ্জ থেকে |
| `s3:prefix` | শুধু `db/` ফোল্ডারের তালিকা দেখতে পারবে |
| `Effect: Deny` | স্পষ্ট নিষেধ, অন্য যেকোনো Allow-কে হারিয়ে দেয় |

```text
সিদ্ধান্তের ক্রম:
১. স্পষ্ট Deny আছে কোথাও?                            -> হ্যাঁ: DENY (শেষ)
২. Organization SCP / permission boundary অনুমতি দেয়?  -> না: DENY
৩. কোনো Allow আছে (identity বা resource policy)?      -> হ্যাঁ: ALLOW
৪. কিছুই নেই                                         -> implicit DENY
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IAM policy evaluation-এ explicit deny আর implicit deny-এর পার্থক্য?
**উত্তর:** Implicit deny হলো default: কোনো Allow না থাকলে নিষেধ, একটা Allow দিয়েই খোলা যায়। Explicit deny হলো policy-তে লেখা Deny, যা যেকোনো Allow-কে ছাপিয়ে যায়।

**প্রশ্ন:** Identity-based আর resource-based policy-এর পার্থক্য?
**উত্তর:** Identity-based user/group/role-এ লাগে, বলে "এই পরিচয় কী করতে পারবে"। Resource-based resource-এ লাগে (S3 bucket policy, KMS key policy), Principal দিয়ে বলে "কে এই resource-এ কী করতে পারবে"; অন্য account-কে অনুমতি দিতে কাজের।

### Role, Trust policy, AssumeRole ও Instance profile

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Trust policy** | কে এই role assume করতে পারবে |
| **STS** | **S**ecurity **T**oken **S**ervice → অস্থায়ী credential দেওয়ার সার্ভিস |
| **Instance profile** | EC2-এর সাথে role যুক্ত করার মোড়ক |
| **Cross-account** | cross-account access → এক account থেকে অন্য account-এ প্রবেশ |

**সহজ ভাষায়:** Role-এর দুটো policy: trust policy (কে assume করতে পারবে, যেমন `ec2.amazonaws.com` বা অন্য account) আর permission policy (assume করার পর কী করতে পারবে)। EC2-এ role দিতে instance profile লাগে (console-এ এটা নিজে তৈরি হয়)। অন্য account-এর role-এ `sts assume-role` দিয়ে প্রবেশ করা হয়।

> **বাস্তব পরিস্থিতি:** কোম্পানির dev আর prod আলাদা AWS account। Developer রা dev account-এ login করে, prod-এ শুধু পড়ার জন্য `prod-readonly` role assume করে; লেখার অনুমতি শুধু CI/CD-এর role-এর।

```jsonc
// trust.json: EC2 এই role নিতে পারবে
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "ec2.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }]
}
```

```bash
aws iam create-role --role-name web-ec2-role --assume-role-policy-document file://trust.json
aws iam attach-role-policy --role-name web-ec2-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
aws iam attach-role-policy --role-name web-ec2-role \
  --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore
aws iam create-instance-profile --instance-profile-name web-ec2-profile
aws iam add-role-to-instance-profile --instance-profile-name web-ec2-profile --role-name web-ec2-role

# অন্য account-এর role ধারণ
aws sts assume-role --role-arn arn:aws:iam::222233334444:role/prod-readonly \
  --role-session-name rahim-debug --duration-seconds 3600
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--assume-role-policy-document file://trust.json` | `file://` = file prefix → JSON ফাইল থেকে পড়ো |
| `assume-role --role-session-name` | session name → CloudTrail-এ কে assume করেছে বোঝার নাম |
| `--duration-seconds 3600` | credential কত সেকেন্ড টিকবে |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `Principal.Service: ec2.amazonaws.com` | EC2 সার্ভিস এই role তার instance-এর জন্য নিতে পারবে |
| `AmazonSSMManagedInstanceCore` | Session Manager দিয়ে SSH ছাড়া সার্ভারে ঢোকার অনুমতি |
| `assume-role`-এর output | `AccessKeyId`, `SecretAccessKey`, `SessionToken`: অস্থায়ী, মেয়াদ শেষে অচল |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Trust policy আর permission policy-এর পার্থক্য?
**উত্তর:** Trust policy ঠিক করে কে role টা assume করতে পারবে (Principal)। Permission policy ঠিক করে assume করার পর কী কী করা যাবে।

**প্রশ্ন:** Cross-account access কীভাবে দেবে?
**উত্তর:** লক্ষ্য account-এ একটা role বানাও যার trust policy-তে উৎস account (বা নির্দিষ্ট role) Principal, permission policy-তে দরকারি অনুমতি; উৎস account-এর user/role-কে `sts:AssumeRole`-এর অনুমতি দাও।

### Least privilege, MFA, access key ও নিরীক্ষা

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Least privilege** | শুধু যতটুকু দরকার ততটুকু অনুমতি |
| **Permission boundary** | একটা user/role সর্বোচ্চ কতটা পেতে পারে তার সীমা |
| **SCP** | **S**ervice **C**ontrol **P**olicy → AWS Organizations-এ পুরো account-এর সীমা |
| **CloudTrail** | কে কখন কোন API call করেছে তার log |
| **Access Analyzer** | IAM Access Analyzer → বাইরের কাছে খোলা resource আর অব্যবহৃত অনুমতি খোঁজে |

**সহজ ভাষায়:** শুরুতে `AdministratorAccess` দিয়ে কাজ চালানো সহজ, কিন্তু একটা key ফাঁস হলে পুরো account শেষ। ন্যূনতম অনুমতি দাও, পরে দরকারে বাড়াও। CloudTrail-এর log আর Access Analyzer দিয়ে দেখো কে কোন অনুমতি আসলে ব্যবহার করে, অব্যবহৃত গুলো সরাও। Access key থাকলে নিয়মিত rotate করো, অব্যবহৃত key মুছে দাও।

> **বাস্তব পরিস্থিতি:** Audit-এ ধরা পড়ল ৪০ জন user-এর মধ্যে ৩৫ জনের `AdministratorAccess`, ১২টা access key এক বছরের বেশি পুরনো, ৫টা এমন মানুষের যারা চাকরি ছেড়েছে। IAM Identity Center-এ গিয়ে ভূমিকা অনুযায়ী permission set, সব পুরনো key বাতিল, আর SCP দিয়ে বাধা যাতে কেউ CloudTrail বন্ধ বা অনুমোদিত Region-এর বাইরে resource বানাতে না পারে।

```bash
aws iam generate-credential-report && aws iam get-credential-report \
  --query Content --output text | base64 -d > creds.csv     # সব user, MFA, key-এর বয়স
aws iam list-access-keys --user-name rahim
aws iam update-access-key --user-name rahim --access-key-id AKIA... --status Inactive
aws iam delete-access-key --user-name rahim --access-key-id AKIA...
aws iam simulate-principal-policy \
  --policy-source-arn arn:aws:iam::123456789012:role/web-ec2-role \
  --action-names s3:DeleteObject --resource-arns arn:aws:s3:::acme-backups/db/x.gz
aws cloudtrail lookup-events --lookup-attributes AttributeKey=EventName,AttributeValue=DeleteBucket --max-results 5
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `get-credential-report --query Content` | report content → base64-এ থাকা CSV, `base64 -d` দিয়ে খোলো |
| `update-access-key --status Inactive` | মোছার আগে নিষ্ক্রিয় করো, কিছু ভাঙে কিনা দেখো |
| `simulate-principal-policy` | আসলে না করে দেখো এই role-এর এই কাজের অনুমতি আছে কিনা |
| `cloudtrail lookup-events` | কে কখন কোন API ডেকেছে খোঁজো |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| সবাইকে `AdministratorAccess` | অনুমতির error এড়াতে | ভূমিকা অনুযায়ী ন্যূনতম অনুমতি, Access Analyzer দিয়ে policy তৈরি |
| কোড, `.env` বা GitHub-এ access key | দ্রুত চালানোর তাড়া | Role/OIDC/SSO; ফাঁস হলে সাথে সাথে key বাতিল |
| `"Action": "*", "Resource": "*"` | কোন action লাগবে জানা নেই | নির্দিষ্ট action আর resource; CloudTrail দেখে ঠিক করো |
| MFA ছাড়া console user | ঝামেলা মনে হয় | সব মানুষের জন্য MFA বাধ্যতামূলক (policy condition দিয়েও) |
| চাকরি ছাড়া মানুষের account চালু | offboarding প্রক্রিয়া নেই | SSO-তে কেন্দ্রীয়ভাবে বন্ধ, নিয়মিত credential report |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Least privilege কীভাবে বাস্তবায়ন করবে?
**উত্তর:** ন্যূনতম অনুমতি দিয়ে শুরু, AWS managed-এর বদলে নির্দিষ্ট customer managed policy, resource আর condition দিয়ে সীমিত, CloudTrail/Access Analyzer দিয়ে আসল ব্যবহার দেখে policy তৈরি ও অব্যবহৃত অনুমতি সরানো, permission boundary আর SCP দিয়ে সর্বোচ্চ সীমা।

**প্রশ্ন:** Access key ফাঁস হলে কী করবে?
**উত্তর:** সাথে সাথে key Inactive/মুছে দাও, CloudTrail দেখে কী কী করা হয়েছে খোঁজো, অনাকাঙ্ক্ষিত resource মোছো, অন্য credential-ও rotate করো, AWS Support-কে জানাও (বিল মওকুফের জন্যও), আর কারণ ঠিক করো (secret scan, role ব্যবহার)।

---

## ০২. VPC: AWS-এ তোমার নিজের নেটওয়ার্ক

**V**irtual **P**rivate **C**loud। প্রতিটি EC2, database আর load balancer কোনো একটা VPC-এর subnet-এ বসে। Subnet, route table আর security group ঠিক না থাকলে সার্ভার হয় পৌঁছানো যায় না, নয়তো সবার জন্য খোলা থাকে।

### VPC, CIDR ও Subnet পরিকল্পনা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **VPC** | **V**irtual **P**rivate **C**loud → AWS-এর ভেতরে তোমার আলাদা logical নেটওয়ার্ক |
| **CIDR** | **C**lassless **I**nter-**D**omain **R**outing → IP রেঞ্জ লেখার পদ্ধতি, যেমন `10.0.0.0/16` |
| **Subnet** | sub-network → VPC-এর ছোট ভাগ, একটা AZ-এর ভেতরে |
| **Public subnet** | route table-এ Internet Gateway-এর পথ আছে |
| **Private subnet** | ইন্টারনেট থেকে সরাসরি পৌঁছানো যায় না |

**সহজ ভাষায়:** VPC একটা Region জুড়ে থাকে, subnet থাকে একটা AZ-এর ভেতরে। High availability-র জন্য প্রতিটি স্তরের subnet অন্তত দুটো AZ-এ বানাও। VPC-এর CIDR পরে বদলানো কঠিন, আর অন্য VPC বা অফিস নেটওয়ার্কের সাথে মিললে peering/VPN করা যায় না, তাই শুরুতেই পরিকল্পনা করো (যেমন `/16`)। AWS প্রতিটি subnet-এর প্রথম ৪টা আর শেষ ১টা IP রেখে দেয়।

> **বাস্তব পরিস্থিতি:** একটা স্টার্টআপ সব কিছু default VPC-এর public subnet-এ রেখেছিল, database-ও সরাসরি ইন্টারনেটে দেখা যাচ্ছিল। নতুন নকশা: `10.20.0.0/16` VPC, দুই AZ-এ public subnet (load balancer, NAT), private app subnet (EC2) আর private data subnet (database)। Database এখন শুধু app subnet থেকে পৌঁছানো যায়।

```text
VPC 10.20.0.0/16 (ap-southeast-1)
┌──────────────── AZ a ────────────────┬──────────────── AZ b ────────────────┐
│ public 10.20.0.0/24   (ALB, NAT)     │ public 10.20.1.0/24   (ALB, NAT)     │
│ app    10.20.10.0/24  (EC2)          │ app    10.20.11.0/24  (EC2)          │
│ data   10.20.20.0/24  (RDS)          │ data   10.20.21.0/24  (RDS)          │
└──────────────────────────────────────┴──────────────────────────────────────┘
Internet Gateway <-> public subnet ; NAT Gateway <- private subnet (শুধু বাইরে যেতে)
```

| সংরক্ষিত IP (`10.20.0.0/24`-এ) | কাজ |
|---|---|
| `10.20.0.0` | network address |
| `10.20.0.1` | VPC router |
| `10.20.0.2` | AWS DNS (VPC base + 2) |
| `10.20.0.3` | ভবিষ্যতের জন্য সংরক্ষিত |
| `10.20.0.255` | broadcast (VPC-তে broadcast নেই, তবু সংরক্ষিত) |
| **ফলাফল** | `/24`-এ ২৫৬ - ৫ = ২৫১টা ব্যবহারযোগ্য |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Default VPC-তে সব কিছু, সব public subnet-এ | সহজ শুরু | নিজের VPC, স্তর অনুযায়ী public/private subnet |
| ছোট CIDR (`/24` পুরো VPC-এর জন্য) | ভবিষ্যৎ চিন্তা না করা | VPC-তে `/16`, subnet `/24` বা প্রয়োজন মতো |
| সব VPC-তে একই `10.0.0.0/16` | ডিফল্ট উদাহরণ কপি | কেন্দ্রীয় IP পরিকল্পনা যাতে peering/VPN-এ সংঘাত না হয় |
| শুধু একটা AZ-এ subnet | খরচ বা অজ্ঞতা | অন্তত ২টা AZ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Public আর private subnet-এর পার্থক্য কী আসলে?
**উত্তর:** পার্থক্য শুধু route table-এ: public subnet-এর route table-এ `0.0.0.0/0 -> Internet Gateway` আছে; private-এ নেই (বাইরে যেতে NAT Gateway)। Subnet-এর নামে কিছু যায় আসে না।

**প্রশ্ন:** একটা `/24` subnet-এ AWS-এ কয়টা IP ব্যবহার করা যায়?
**উত্তর:** ২৫১টা, কারণ AWS প্রতিটি subnet-এ ৫টা IP সংরক্ষণ করে।

### Internet Gateway, NAT Gateway ও Route table

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **IGW** | **I**nternet **G**ate**w**ay → VPC আর ইন্টারনেটের দরজা (দুদিকে) |
| **NAT GW** | **N**etwork **A**ddress **T**ranslation **G**ate**w**ay → private subnet-কে শুধু বাইরে যেতে দেয় |
| **Route table** | কোন গন্তব্যে কোন পথে যাবে |
| **EIP** | **E**lastic **IP** → স্থির public IPv4 |

**সহজ ভাষায়:** প্রতিটি subnet একটা route table-এর সাথে যুক্ত। `local` route (VPC-এর ভেতরে) সবসময় থাকে। Public subnet-এর route table-এ `0.0.0.0/0 -> igw`। Private subnet-এর সার্ভারকে OS update বা বাইরের API ডাকতে হলে `0.0.0.0/0 -> nat-gw`, যেটা নিজে public subnet-এ বসে থাকে। NAT একমুখী: ভেতর থেকে বাইরে যাওয়া যায়, বাইরে থেকে ভেতরে আসা যায় না। NAT Gateway ঘণ্টা আর GB হিসেবে বিল হয়, খরচের একটা বড় উৎস।

> **বাস্তব পরিস্থিতি:** Private subnet-এর app সার্ভার Stripe API ডাকতে পারছে না, timeout। দেখা গেল private route table-এ NAT-এর route নেই। আরেকটা টিমের মাসিক বিলে NAT-এর খরচ ৮০০ ডলার, কারণ S3 থেকে TB-ভরা data NAT দিয়ে আসছিল; S3 Gateway Endpoint যোগ করে (বিনামূল্যে) খরচ প্রায় শূন্য।

```text
Public route table:                 Private route table (AZ a):
Destination    Target               Destination    Target
10.20.0.0/16   local                10.20.0.0/16   local
0.0.0.0/0      igw-0abc             0.0.0.0/0      nat-0aaa  (AZ a-এর NAT)
                                    pl-xxxx (S3)   vpce-0s3  (Gateway endpoint)
```

```bash
aws ec2 describe-route-tables --filters Name=vpc-id,Values=vpc-0abc \
  --query "RouteTables[].{id:RouteTableId,routes:Routes[].[DestinationCidrBlock,GatewayId,NatGatewayId]}"
aws ec2 create-vpc-endpoint --vpc-id vpc-0abc \
  --service-name com.amazonaws.ap-southeast-1.s3 \
  --route-table-ids rtb-0priv-a rtb-0priv-b
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `create-vpc-endpoint --service-name` | কোন AWS সার্ভিসের জন্য (S3 gateway endpoint বিনামূল্যে) |
| `--route-table-ids` | কোন route table-এ S3-এর route বসবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** NAT Gateway আর Internet Gateway-এর পার্থক্য?
**উত্তর:** IGW দুদিকেই যোগাযোগ দেয়, public IP থাকা resource-এর জন্য। NAT Gateway শুধু private subnet থেকে বাইরে যাওয়ার (outbound) সুযোগ দেয়, বাইরে থেকে আসা সংযোগ নয়।

**প্রশ্ন:** NAT Gateway-কে highly available কীভাবে করবে?
**উত্তর:** NAT Gateway একটা AZ-এর ভেতরে; প্রতিটি AZ-এ আলাদা NAT আর প্রতিটি AZ-এর private route table নিজের AZ-এর NAT-এ, যাতে একটা AZ বন্ধ হলে অন্যটা চলে।

### Security Group বনাম Network ACL

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SG** | **S**ecurity **G**roup → instance/ENI স্তরের stateful firewall |
| **NACL** | **N**etwork **A**ccess **C**ontrol **L**ist → subnet স্তরের stateless firewall |
| **ENI** | **E**lastic **N**etwork **I**nterface → ভার্চুয়াল network card |
| **Ephemeral port** | ক্লায়েন্টের অস্থায়ী port, 1024-65535 |

**সহজ ভাষায়:** Security Group হলো প্রতিটি resource-এর নিজের firewall: শুধু Allow নিয়ম, stateful (ভেতরে আসার অনুমতি থাকলে উত্তর নিজে বেরোয়)। SG-এর সবচেয়ে শক্তিশালী ব্যবহার: অন্য SG-কে উৎস বানানো, যেমন "database-এ 5432 শুধু app-sg থেকে", IP মনে রাখতে হয় না। NACL subnet-এর সীমানায়, allow ও deny দুটোই, ক্রমানুসারে, stateless: ফিরতি traffic-এর জন্য ephemeral port আলাদা খুলতে হয়। সাধারণত SG দিয়েই কাজ চলে, NACL শুধু বাড়তি স্তর বা নির্দিষ্ট IP ব্লক করতে।

> **বাস্তব পরিস্থিতি:** একজন developer database-এর SG-এ 5432-এর জন্য `0.0.0.0/0` দিয়েছিল "পরীক্ষার জন্য"। কয়েক ঘণ্টায় brute force শুরু। ঠিক করা হলো: db-sg-তে 5432 শুধু উৎস app-sg। একটা বিরক্তিকর IP থেকে আক্রমণ ঠেকাতে NACL-এ সেই IP-এর জন্য deny নিয়ম।

```bash
aws ec2 create-security-group --group-name app-sg --description "app servers" --vpc-id vpc-0abc
aws ec2 create-security-group --group-name db-sg --description "database" --vpc-id vpc-0abc

# ALB থেকে app-এ 8080
aws ec2 authorize-security-group-ingress --group-id sg-0app \
  --protocol tcp --port 8080 --source-group sg-0alb

# app থেকে db-তে 5432
aws ec2 authorize-security-group-ingress --group-id sg-0db \
  --protocol tcp --port 5432 --source-group sg-0app

aws ec2 describe-security-groups --group-ids sg-0db \
  --query "SecurityGroups[].IpPermissions"
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `authorize-security-group-ingress` | `ingress` → ভেতরে আসার নিয়ম যোগ (`egress` = বাইরে যাওয়া) |
| `--protocol tcp --port 5432` | `protocol` → tcp/udp/icmp · `port` → কোন port |
| `--source-group sg-0app` | source security group → এই SG যুক্ত resource থেকেই শুধু অনুমতি |
| `--cidr 203.0.113.10/32` | নির্দিষ্ট IP রেঞ্জ থেকে (`/32` = একটাই IP) |

| বিষয় | Security Group | NACL |
|---|---|---|
| স্তর | instance/ENI | subnet |
| নিয়ম | শুধু Allow | Allow ও Deny |
| State | Stateful | Stateless (ফিরতি port খুলতে হয়) |
| ক্রম | সব নিয়ম একসাথে মূল্যায়ন | নম্বর অনুযায়ী, প্রথম মিলেই থামে |
| Default | inbound সব বন্ধ, outbound সব খোলা | default NACL সব খোলা |
| বিশেষ শক্তি | অন্য SG-কে উৎস হিসেবে | নির্দিষ্ট IP ব্লক |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| SSH (22) বা DB port `0.0.0.0/0`-এ খোলা | পরীক্ষার জন্য, পরে ভুলে যাওয়া | SSM Session Manager (কোনো port খোলা লাগে না), DB শুধু app-sg থেকে |
| IP দিয়ে SG নিয়ম (app সার্ভারের IP) | SG reference জানা নেই | source-group ব্যবহার, IP বদলালেও কাজ করে |
| NACL-এ ফিরতি ephemeral port বন্ধ | stateless-এর মানে না বোঝা | outbound 1024-65535 খোলো বা NACL default রাখো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Security group আর NACL-এর পার্থক্য?
**উত্তর:** টেবিলটাই উত্তর: SG instance স্তরে stateful, শুধু allow; NACL subnet স্তরে stateless, allow/deny ক্রমানুসারে।

**প্রশ্ন:** Security group-এ deny নিয়ম দেওয়া যায় কি?
**উত্তর:** না। SG-তে শুধু allow; যা allow করা নেই তা নিষেধ। নির্দিষ্ট IP ব্লক করতে NACL বা WAF।

### Bastion বনাম SSM Session Manager, VPC Peering, Flow Logs

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Bastion** | bastion host (দুর্গের প্রাচীর) → private সার্ভারে ঢোকার জন্য public-এ একটা jump সার্ভার |
| **SSM** | AWS **S**ystems **M**anager → সার্ভার ব্যবস্থাপনার সার্ভিস |
| **Session Manager** | port/key ছাড়া IAM দিয়ে সার্ভারে shell |
| **Peering** | VPC peering → দুটো VPC সরাসরি যুক্ত |
| **TGW** | **T**ransit **G**ate**w**ay → অনেক VPC আর অফিসের কেন্দ্রীয় সংযোগ |
| **Flow Logs** | VPC Flow Logs → কোন IP:port থেকে কোথায় traffic, গৃহীত না প্রত্যাখ্যাত |

**সহজ ভাষায়:** পুরনো পদ্ধতি: public subnet-এ একটা bastion, তার মধ্য দিয়ে SSH। আধুনিক পদ্ধতি: Session Manager, যেখানে কোনো inbound port খোলা লাগে না, SSH key লাগে না, অনুমতি IAM দিয়ে, আর প্রতিটি session-এর log থাকে। দুটো VPC যুক্ত করতে peering (non-transitive); অনেক VPC আর অফিস হলে Transit Gateway। কোন traffic আটকে যাচ্ছে বুঝতে Flow Logs।

> **বাস্তব পরিস্থিতি:** Audit-এর পর নিয়ম: কোনো সার্ভারে port 22 খোলা থাকবে না। সব EC2-এ SSM agent আর `AmazonSSMManagedInstanceCore` role, engineer রা `aws ssm start-session` দিয়ে ঢোকে, কে কী কমান্ড দিল সব S3-তে রেকর্ড। আর একটা সার্ভিস অন্য VPC-এর database-এ পৌঁছাতে পারছিল না; Flow Logs-এ REJECT দেখে বোঝা গেল peering-এর route ঠিক থাকলেও db-sg-তে অন্য VPC-এর CIDR অনুমোদিত ছিল না।

```bash
# Session Manager (ল্যাপটপে session-manager-plugin লাগবে)
aws ssm start-session --target i-0abc123def456

# private RDS-এ port forwarding
aws ssm start-session --target i-0abc123def456 \
  --document-name AWS-StartPortForwardingSessionToRemoteHost \
  --parameters '{"host":["db.internal"],"portNumber":["5432"],"localPortNumber":["15432"]}'

# Flow Logs S3-এ
aws ec2 create-flow-logs --resource-type VPC --resource-ids vpc-0abc \
  --traffic-type REJECT --log-destination-type s3 \
  --log-destination arn:aws:s3:::acme-flowlogs

# একটা flow log লাইন
# 2 123456789012 eni-0a1b 10.30.5.9 10.20.20.15 49152 5432 6 3 180 1700000000 1700000060 REJECT OK
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `ssm start-session --target` | কোন instance-এ shell |
| `--document-name AWS-StartPortForwardingSessionToRemoteHost` | SSM document → instance-এর মধ্য দিয়ে অন্য host-এর port-এ সুড়ঙ্গ |
| `create-flow-logs --traffic-type REJECT` | শুধু প্রত্যাখ্যাত (ACCEPT/ALL-ও হয়) |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `10.30.5.9 -> 10.20.20.15 ... 5432 ... REJECT` | অন্য VPC (10.30) থেকে database-এর 5432-এ আসা traffic আটকে গেছে: SG/NACL দেখো |
| `6` | protocol number: 6 = TCP |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Bastion host-এর বদলে কী ব্যবহার করবে আর কেন?
**উত্তর:** SSM Session Manager: কোনো inbound port বা SSH key লাগে না, IAM দিয়ে নিয়ন্ত্রণ, session log CloudWatch/S3-তে, private subnet থেকেও কাজ করে (VPC endpoint বা NAT দিয়ে)।

**প্রশ্ন:** VPC peering কি transitive?
**উত্তর:** না। A-B আর B-C peering থাকলেও A থেকে C-তে যাওয়া যায় না; অনেক VPC হলে Transit Gateway।

---

## ০৩. EC2: ভার্চুয়াল সার্ভার

**E**lastic **C**ompute **C**loud। AWS-এর সবচেয়ে পুরনো আর মূল সার্ভিস। কোন instance, কোন disk, কীভাবে চালু, কীভাবে নিরাপদ আর কীভাবে খরচ কমানো।

### Instance type, AMI ও Key pair

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **EC2** | **E**lastic **C**ompute **C**loud → ভাড়ার ভার্চুয়াল সার্ভার |
| **AMI** | **A**mazon **M**achine **I**mage → সার্ভারের ছাঁচ: OS ও সফটওয়্যার |
| **vCPU** | virtual CPU → ভার্চুয়াল CPU |
| **Graviton** | AWS-এর নিজের ARM প্রসেসর, সস্তা ও দক্ষ |

**সহজ ভাষায়:** Instance type-এর নাম পড়তে শেখো: `m7g.large` মানে family `m` (general purpose), generation `7`, `g` = Graviton (ARM), size `large`। `t` = burstable সস্তা (CPU credit), `c` = compute, `r` = memory, `i` = দ্রুত storage, `g`/`p` = GPU। AMI থেকে OS আসে (Amazon Linux 2023, Ubuntu)। Key pair দিয়ে SSH, যদিও এখন SSM Session Manager ভালো।

> **বাস্তব পরিস্থিতি:** একটা Java API ৪ GB RAM-এ চলে, CPU কম লাগে। `c7i.large` (৪ GB)-এর বদলে `r7g.medium` (৮ GB, ARM, সস্তা) বেছে নেওয়া হলো, খরচ কমল আর memory বেশি। আগে নিশ্চিত হওয়া হলো image ARM-এর জন্য build করা (Docker buildx)।

| Family | উদ্দেশ্য | উদাহরণ ব্যবহার |
|---|---|---|
| t (t3, t4g) | burstable, সস্তা, CPU credit | ছোট web, dev, অনুশীলন |
| m (m7i, m7g) | general purpose, CPU:RAM = 1:4 | সাধারণ অ্যাপ সার্ভার |
| c (c7i, c7g) | compute, CPU বেশি | API, batch, encoding |
| r (r7i, r7g) | memory, RAM বেশি | cache, in-memory DB, Java |
| i (i4i) | local NVMe storage | database, search |
| g / p | GPU | ML, ভিডিও |

```bash
aws ec2 describe-instance-types --instance-types t3.micro m7g.large \
  --query "InstanceTypes[].[InstanceType,VCpuInfo.DefaultVCpus,MemoryInfo.SizeInMiB]" --output table

# সর্বশেষ Amazon Linux 2023 AMI (SSM public parameter থেকে)
aws ssm get-parameter --name /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 \
  --query Parameter.Value --output text

aws ec2 create-key-pair --key-name rahim-key --key-type ed25519 \
  --query KeyMaterial --output text > rahim-key.pem && chmod 400 rahim-key.pem
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--key-type ed25519` | আধুনিক ছোট ও নিরাপদ key |
| `--query KeyMaterial` | private key → শুধু এই একবারই দেখানো হয়, সংরক্ষণ করো |
| `chmod 400` | owner read only → SSH-এর জন্য দরকারি permission |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `m7g.large` নামের প্রতিটি অংশের মানে কী?
**উত্তর:** `m` = family (general purpose), `7` = generation, `g` = processor বৈশিষ্ট্য (Graviton/ARM), `large` = size (vCPU/RAM-এর পরিমাণ)।

**প্রশ্ন:** t-series instance-এর CPU credit কী?
**উত্তর:** Baseline-এর নিচে চললে credit জমে, বেশি চাপে সেই credit খরচ করে burst করে; credit শেষ হলে baseline-এ নেমে আসে (unlimited mode-এ বাড়তি টাকা দিয়ে চালু থাকে)।

### Instance চালু করা: user data, IMDSv2, tag

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **User data** | প্রথম boot-এ চলা script |
| **IMDS** | **I**nstance **M**eta**d**ata **S**ervice → instance-এর নিজের তথ্য ও role credential দেয় (`169.254.169.254`) |
| **IMDSv2** | IMDS version 2 → token ভিত্তিক নিরাপদ সংস্করণ |
| **Tag** | key=value লেবেল, খরচ ও ব্যবস্থাপনার জন্য |

**সহজ ভাষায়:** `run-instances`-এ AMI, type, subnet, SG, role, user data সব একসাথে দাও। User data দিয়ে প্রথম boot-এ সফটওয়্যার ইনস্টল আর কনফিগার হয়, হাতে ঢুকতে হয় না। IMDSv2 বাধ্যতামূলক করো (`HttpTokens=required`): এতে SSRF আক্রমণে attacker role-এর credential চুরি করতে পারে না। প্রতিটি resource-এ Name, Owner, Env, Project tag দাও, খরচ হিসাব আর পরিষ্কারের জন্য।

> **বাস্তব পরিস্থিতি:** Capital One-এর ২০১৯-এর data breach-এ একটা ভুল কনফিগার করা WAF-এর SSRF দিয়ে IMDSv1 থেকে role credential চুরি হয়েছিল। এই কারণে এখন সব নতুন instance-এ IMDSv2 বাধ্যতামূলক রাখা নিরাপত্তার মৌলিক নিয়ম।

```bash
# user-data.sh
#!/bin/bash
dnf -y update
dnf -y install nginx
echo "<h1>Hello from $(hostname)</h1>" > /usr/share/nginx/html/index.html
systemctl enable --now nginx
```

```bash
aws ec2 run-instances \
  --image-id resolve:ssm:/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 \
  --instance-type t3.micro \
  --subnet-id subnet-0pub-a \
  --security-group-ids sg-0web \
  --iam-instance-profile Name=web-ec2-profile \
  --user-data file://user-data.sh \
  --metadata-options HttpTokens=required,HttpEndpoint=enabled \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=web-1},{Key=Env,Value=dev},{Key=Owner,Value=rahim}]' \
  --query "Instances[0].InstanceId" --output text

# instance-এর ভেতর থেকে IMDSv2
TOKEN=$(curl -s -X PUT http://169.254.169.254/latest/api/token -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id
curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--image-id resolve:ssm:...` | SSM parameter resolve → সর্বশেষ AMI ID নিজে বের করো |
| `--subnet-id` / `--security-group-ids` | কোন subnet-এ (AZ-ও ঠিক হয়) / কোন firewall |
| `--iam-instance-profile Name=...` | instance profile → এই role দাও |
| `--user-data file://...` | প্রথম boot-এর script |
| `--metadata-options HttpTokens=required` | IMDSv2 বাধ্যতামূলক, token ছাড়া metadata নয় |
| `--tag-specifications` | tags at creation → তৈরির সাথে সাথে tag |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `dnf -y install` | Amazon Linux 2023-এর package manager |
| `PUT .../api/token` | IMDSv2: প্রথমে token নাও (৬ ঘণ্টা পর্যন্ত) |
| `security-credentials/` | role-এর অস্থায়ী credential; এখান থেকেই SDK নেয় |
| `/var/log/cloud-init-output.log` | user data চলেছে কিনা, error থাকলে এখানে দেখো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** User data কখন চলে আর কীভাবে debug করবে?
**উত্তর:** Default-এ শুধু প্রথম boot-এ root হিসেবে চলে (cloud-init)। Debug: `/var/log/cloud-init-output.log`।

**প্রশ্ন:** IMDSv2 কেন জরুরি?
**উত্তর:** Session token লাগে (PUT অনুরোধ), তাই অ্যাপের SSRF দুর্বলতা দিয়ে সাধারণ GET অনুরোধে attacker metadata থেকে role credential চুরি করতে পারে না।

### EBS volume, snapshot ও instance-এর জীবনচক্র

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **EBS** | **E**lastic **B**lock **S**tore → EC2-এর network disk |
| **gp3** | **G**eneral **P**urpose SSD v3 → সাধারণ SSD, আলাদা করে IOPS/throughput |
| **Snapshot** | EBS snapshot → volume-এর incremental backup (S3-এ) |
| **Instance store** | host-এর নিজের disk, stop/terminate-এ মুছে যায় |

**সহজ ভাষায়:** EBS একটা AZ-এর ভেতরে থাকে আর একই AZ-এর instance-এ যুক্ত হয়। gp3-এ baseline ৩০০০ IOPS আর ১২৫ MB/s, আলাদা টাকায় বাড়ানো যায় (gp2-এর মতো আকারের উপর নির্ভর নয়)। Snapshot দিয়ে backup, অন্য AZ বা Region-এ কপি। Stop করলে instance-এর বিল বন্ধ কিন্তু EBS-এর বিল চলে; terminate করলে instance শেষ, আর `DeleteOnTermination` সত্য থাকলে root volume-ও মুছে যায়। Public IP stop/start-এ বদলে যায় (Elastic IP ছাড়া)।

> **বাস্তব পরিস্থিতি:** একজন developer ভুল করে production database-এর instance terminate করল, root volume-এর সাথে data-ও গেল। এরপর নিয়ম: termination protection চালু, data আলাদা EBS volume-এ (`DeleteOnTermination false`), আর Data Lifecycle Manager দিয়ে প্রতিদিন স্বয়ংক্রিয় snapshot, ৭ দিন রাখা।

```bash
aws ec2 create-volume --availability-zone ap-southeast-1a --size 100 --volume-type gp3 \
  --iops 6000 --throughput 250 --encrypted
aws ec2 attach-volume --volume-id vol-0abc --instance-id i-0abc --device /dev/sdf
aws ec2 modify-volume --volume-id vol-0abc --size 200     # চলমান অবস্থায় বড় করা
# তারপর instance-এর ভেতরে: growpart + resize2fs/xfs_growfs
aws ec2 create-snapshot --volume-id vol-0abc --description "before upgrade"
aws ec2 modify-instance-attribute --instance-id i-0abc --disable-api-termination
aws ec2 stop-instances --instance-ids i-0abc
aws ec2 start-instances --instance-ids i-0abc
aws ec2 terminate-instances --instance-ids i-0abc
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--volume-type gp3 --iops --throughput` | General Purpose SSD 3 → আকার ছাড়াই IOPS/throughput আলাদা ঠিক করা যায় |
| `--encrypted` | KMS দিয়ে at-rest encryption (account-এ default চালু রাখা ভালো) |
| `--device /dev/sdf` | device name → OS-এ `nvme1n1`-এর মতো নামে দেখাবে (Nitro) |
| `--disable-api-termination` | termination protection → ভুল করে terminate ঠেকাও |

| অবস্থা | instance-এর বিল | EBS-এর বিল | public IP | instance store data |
|---|---|---|---|---|
| Running | ✅ | ✅ | থাকে | থাকে |
| Stopped | ❌ | ✅ | হারায় (EIP ছাড়া) | মুছে যায় |
| Terminated | ❌ | `DeleteOnTermination` অনুযায়ী | হারায় | মুছে যায় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Stop আর terminate-এর পার্থক্য?
**উত্তর:** Stop-এ instance বন্ধ, EBS থাকে, পরে চালু করা যায়, শুধু EBS-এর বিল। Terminate-এ instance চিরতরে মুছে যায়, root EBS-ও সাধারণত মুছে যায়।

**প্রশ্ন:** EBS snapshot কি incremental?
**উত্তর:** হ্যাঁ, প্রথমটা পূর্ণ, পরেরগুলো শুধু বদলানো block রাখে; তবে প্রতিটি snapshot থেকে পুরো volume restore করা যায়।

### ALB + Auto Scaling Group: highly available web tier

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ALB** | **A**pplication **L**oad **B**alancer → HTTP/HTTPS (Layer 7) load balancer |
| **ASG** | **A**uto **S**caling **G**roup → instance সংখ্যা স্বয়ংক্রিয় ঠিক রাখে |
| **Launch template** | নতুন instance-এর ছাঁচ (AMI, type, SG, user data) |
| **Target group** | ALB যাদের কাছে traffic পাঠায় |
| **Health check** | instance সুস্থ কিনা পরীক্ষা |

**সহজ ভাষায়:** একটা সার্ভার একটা ব্যর্থতার বিন্দু। Launch template-এ instance-এর ছাঁচ, ASG দুই বা বেশি AZ-এর private subnet-এ min/desired/max সংখ্যা ধরে রাখে, ALB public subnet-এ বসে traffic ভাগ করে আর health check দিয়ে অসুস্থ instance বাদ দেয়। ASG-এর health check type `ELB` দিলে ALB অসুস্থ বললে ASG নিজে instance বদলায়। Target tracking policy দিয়ে CPU ৫০% রাখার মতো স্বয়ংক্রিয় scaling।

> **বাস্তব পরিস্থিতি:** একটা নিউজ সাইটে বড় খবর এলে traffic ২০ গুণ। ASG ২ থেকে ১৬ instance-এ গেল, ALB সবার মধ্যে ভাগ করল। একটা AZ-এ সমস্যা হলে সেই AZ-এর instance গুলো health check fail করল, ALB অন্য AZ-এ traffic পাঠাল, ASG নতুন instance চালু করল।

```text
              Internet
                 │
              [ ALB ]  (public subnet a + b, HTTPS:443)
                 │  target group (health check /health)
        ┌────────┴─────────┐
 [EC2 a] [EC2 a]    [EC2 b] [EC2 b]   <- ASG (private app subnet, min 2 max 16)
```

```bash
aws ec2 create-launch-template --launch-template-name web-lt --launch-template-data '{
  "ImageId": "ami-0abc...",
  "InstanceType": "t3.small",
  "SecurityGroupIds": ["sg-0app"],
  "IamInstanceProfile": {"Name": "web-ec2-profile"},
  "MetadataOptions": {"HttpTokens": "required"},
  "UserData": "<base64 script>"
}'

aws autoscaling create-auto-scaling-group --auto-scaling-group-name web-asg \
  --launch-template LaunchTemplateName=web-lt,Version='$Latest' \
  --min-size 2 --max-size 16 --desired-capacity 2 \
  --vpc-zone-identifier "subnet-0app-a,subnet-0app-b" \
  --target-group-arns arn:aws:elasticloadbalancing:...:targetgroup/web-tg/abc \
  --health-check-type ELB --health-check-grace-period 120

aws autoscaling put-scaling-policy --auto-scaling-group-name web-asg \
  --policy-name cpu50 --policy-type TargetTrackingScaling \
  --target-tracking-configuration '{"PredefinedMetricSpecification":{"PredefinedMetricType":"ASGAverageCPUUtilization"},"TargetValue":50}'

aws autoscaling start-instance-refresh --auto-scaling-group-name web-asg   # নতুন AMI rolling ভাবে
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--min-size --max-size --desired-capacity` | minimum → কখনো এর কম না · maximum → কখনো এর বেশি না · desired → এখন কতগুলো |
| `--vpc-zone-identifier` | subnets → কোন subnet গুলোতে (একাধিক AZ) |
| `--health-check-type ELB` | load balancer health → ALB-এর health check fail হলেও instance বদলাও |
| `--health-check-grace-period 120` | grace period → চালুর পর ১২০ সেকেন্ড health check উপেক্ষা |
| `TargetTrackingScaling TargetValue 50` | target tracking → গড় CPU ৫০% রাখতে নিজে বাড়াবে-কমাবে |
| `start-instance-refresh` | instance refresh → নতুন launch template দিয়ে ধাপে ধাপে সব instance বদলাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Highly available web application-এর নকশা কেমন হবে?
**উত্তর:** অন্তত দুই AZ; ALB public subnet-এ; ASG private subnet-এ, min ২, health check ELB; database Multi-AZ (RDS); stateless app (session Redis/DB-তে); static ফাইল S3 + CloudFront; সব কিছু IaC দিয়ে।

**প্রশ্ন:** ALB আর NLB-এর পার্থক্য?
**উত্তর:** ALB Layer 7 (HTTP/HTTPS): path/host routing, header, WAF। NLB Layer 4 (TCP/UDP/TLS): খুব কম latency, স্থির IP, অনেক বেশি throughput, non-HTTP protocol।

### EC2-এর খরচ: On-Demand, Savings Plans, Spot

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **On-Demand** | ঘণ্টা/সেকেন্ড হিসেবে, কোনো প্রতিশ্রুতি নেই |
| **Savings Plans** | ১/৩ বছরের প্রতি ঘণ্টা খরচের প্রতিশ্রুতিতে ছাড় |
| **RI** | **R**eserved **I**nstance → নির্দিষ্ট instance-এর ১/৩ বছরের সংরক্ষণ |
| **Spot** | spot instance → AWS-এর অব্যবহৃত ক্ষমতা, অনেক সস্তা, ২ মিনিটের নোটিশে ফেরত নিতে পারে |

**সহজ ভাষায়:** On-Demand নমনীয় কিন্তু দামি। সারাক্ষণ চলা কাজে Savings Plans বা Reserved দিয়ে উল্লেখযোগ্য ছাড়। Spot সবচেয়ে সস্তা (প্রায়ই ৭০-৯০% কম) কিন্তু যেকোনো সময় ২ মিনিটের সতর্কতায় কেড়ে নিতে পারে, তাই শুধু বাধা সহ্য করতে পারে এমন কাজে: CI runner, batch, ASG-এর বাড়তি অংশ। সঠিক আকার (rightsizing), রাতে dev বন্ধ, আর Graviton দিয়েও খরচ কমে।

> **বাস্তব পরিস্থিতি:** মাসিক EC2 বিল ৫,০০০ ডলার। Compute Optimizer দেখাল অর্ধেক instance প্রয়োজনের দ্বিগুণ বড়: ছোট করা হলো। ২৪/৭ চলা base-এর জন্য Savings Plan, CI runner আর batch কাজ Spot-এ, dev environment রাত ৮টা থেকে সকাল ৮টা বন্ধ। বিল প্রায় অর্ধেক।

| ধরন | ছাড় | প্রতিশ্রুতি | কখন |
|---|---|---|---|
| On-Demand | নেই | নেই | অনিশ্চিত, স্বল্পমেয়াদি, নতুন কাজ |
| Savings Plans | অনেক (মেয়াদ ও অগ্রিম অনুযায়ী) | ১ বা ৩ বছর, $/ঘণ্টা | স্থির base load, নমনীয় (family/Region বদলানো যায়, ধরন অনুযায়ী) |
| Reserved Instance | অনেক | ১ বা ৩ বছর, নির্দিষ্ট attribute | স্থির, পুরনো পদ্ধতি; RDS-এর মতো সার্ভিসেও |
| Spot | সবচেয়ে বেশি | নেই, কিন্তু কেড়ে নিতে পারে | CI, batch, stateless, fault-tolerant |
| Dedicated Host | নেই | - | license বা compliance-এর জন্য নিজস্ব hardware |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Spot instance কখন ব্যবহার করবে না?
**উত্তর:** যে কাজ হঠাৎ বন্ধ হলে ক্ষতি হয় আর পুনরায় শুরু করা যায় না: একক database, stateful সার্ভার, সময়-সংবেদনশীল কাজ যার বিকল্প নেই।

---

## ০৪. S3: অসীম ফাইল ভাণ্ডার

**S**imple **S**torage **S**ervice। Backup, log, ছবি, website, data lake: প্রায় সব কিছু S3-এ যায়। আর সবচেয়ে বেশি data ফাঁসের ঘটনাও ঘটে ভুল কনফিগার করা S3 bucket থেকে।

### Bucket, Object, Key ও aws s3 কমান্ড

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **S3** | **S**imple **S**torage **S**ervice → সহজ object storage |
| **Bucket** | object রাখার পাত্র, নাম পৃথিবীতে অনন্য |
| **Object** | ফাইল + metadata (সর্বোচ্চ ৫ TB) |
| **Key** | object key → bucket-এর ভেতরে object-এর পূর্ণ নাম, যেমন `logs/2026/10/app.log` |
| **Prefix** | key-এর শুরুর অংশ, ফোল্ডারের মতো দেখায় |

**সহজ ভাষায়:** S3 ফাইল সিস্টেম নয়, object store: আসলে কোনো ফোল্ডার নেই, key-তে `/` থাকলে console ফোল্ডারের মতো দেখায়। Bucket-এর নাম সব AWS account মিলিয়ে অনন্য হতে হয়, আর bucket একটা নির্দিষ্ট Region-এ থাকে। S3-এ data-কে ১১টা ৯ (৯৯.৯৯৯৯৯৯৯৯৯%) durability দেওয়ার মতো করে নকশা করা, অর্থাৎ হারানোর সম্ভাবনা প্রায় শূন্য। S3 এখন strong read-after-write consistent: লেখার পরপরই পড়লে নতুনটা পাবে।

> **বাস্তব পরিস্থিতি:** প্রতি রাতে database dump S3-এ যায়, আর একটা static website S3 থেকে CloudFront দিয়ে চলে। Developer রা `aws s3 sync` দিয়ে build ফোল্ডার মুহূর্তে upload করে।

```bash
aws s3 mb s3://acme-backups-apse1 --region ap-southeast-1
aws s3 ls
aws s3 ls s3://acme-backups-apse1/db/ --recursive --human-readable --summarize
aws s3 cp db-2026-10-06.sql.gz s3://acme-backups-apse1/db/
aws s3 cp s3://acme-backups-apse1/db/db-2026-10-06.sql.gz ./restore/
aws s3 sync ./dist s3://acme-site/ --delete --exclude "*.map"
aws s3 sync ./dist s3://acme-site/ --delete --dryrun
aws s3 rm s3://acme-backups-apse1/tmp/ --recursive
aws s3 mv s3://acme-in/new.csv s3://acme-in/processed/new.csv
aws s3 presign s3://acme-reports/q3.pdf --expires-in 3600
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `s3 mb` | `mb` = make bucket → নতুন bucket |
| `ls --recursive --human-readable --summarize` | recursive → ভেতরের সব · human-readable → KB/MB-এ · summarize → শেষে মোট সংখ্যা ও আকার |
| `s3 cp` | `cp` = copy → local থেকে S3, S3 থেকে local বা S3 থেকে S3 |
| `s3 sync --delete` | `sync` → শুধু পার্থক্য কপি · `delete` → উৎসে নেই এমন object গন্তব্য থেকে মোছো (সাবধান) |
| `--exclude "*.map"` | এই প্যাটার্ন বাদ |
| `--dryrun` | dry run → কিছু না করে কী হবে দেখাও |
| `s3 presign --expires-in 3600` | pre-signed URL → login ছাড়া সাময়িক ডাউনলোড লিংক · expires-in → ৩৬০০ সেকেন্ড পর অচল |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** S3 কি file system? ফোল্ডার আছে?
**উত্তর:** না, flat object store। ফোল্ডার আসলে key-এর prefix (`logs/2026/`), console শুধু সুবিধার জন্য ফোল্ডারের মতো দেখায়।

**প্রশ্ন:** Pre-signed URL কী আর কখন?
**উত্তর:** অল্প সময়ের জন্য স্বাক্ষরিত লিংক যা দিয়ে AWS credential ছাড়াই নির্দিষ্ট object download/upload করা যায়; যেমন user-কে নিজের invoice ডাউনলোড বা browser থেকে সরাসরি S3-এ upload করতে দেওয়া।

### Block Public Access, Bucket policy ও encryption

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **BPA** | **B**lock **P**ublic **A**ccess → bucket বা account স্তরে public হওয়া আটকানোর সুইচ |
| **Bucket policy** | bucket-এ লাগানো resource-based policy |
| **SSE-S3** | **S**erver-**S**ide **E**ncryption with **S3** managed keys → S3 নিজের key দিয়ে এনক্রিপ্ট (default) |
| **SSE-KMS** | **S**erver-**S**ide **E**ncryption with **KMS** keys → KMS key দিয়ে, কে decrypt করতে পারবে তাও নিয়ন্ত্রিত |
| **ACL** | **A**ccess **C**ontrol **L**ist → পুরনো object স্তরের অনুমতি, এখন default বন্ধ |

**সহজ ভাষায়:** নতুন bucket-এ Block Public Access default চালু, ACL বন্ধ (Bucket owner enforced), আর SSE-S3 encryption default। এগুলো বন্ধ কোরো না যদি না সত্যিই public website বানাও, আর তখনও CloudFront ব্যবহার করে bucket private রাখো। Bucket policy দিয়ে নির্দিষ্ট role, অন্য account, বা শুধু HTTPS-এর মতো শর্ত দাও। সংবেদনশীল data-তে SSE-KMS, যাতে S3 অনুমতি থাকলেও KMS অনুমতি না থাকলে কেউ পড়তে পারে না।

> **বাস্তব পরিস্থিতি:** একটা কোম্পানির bucket-এ গ্রাহকদের NID-এর ছবি public ছিল, একজন researcher খুঁজে পেয়ে সংবাদে চলে এল। তদন্তে দেখা গেল কেউ "সমস্যা সমাধানে" Block Public Access বন্ধ করে `"Principal": "*"` দিয়েছিল। এরপর account স্তরে BPA চালু আর SCP দিয়ে বন্ধ করা নিষেধ।

```bash
aws s3api put-public-access-block --bucket acme-kyc \
  --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true

aws s3control put-public-access-block --account-id 123456789012 \
  --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

```jsonc
// bucket policy: শুধু HTTPS, আর শুধু app role পড়তে পারবে
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyInsecureTransport",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": ["arn:aws:s3:::acme-kyc", "arn:aws:s3:::acme-kyc/*"],
      "Condition": { "Bool": { "aws:SecureTransport": "false" } }
    },
    {
      "Sid": "AppRead",
      "Effect": "Allow",
      "Principal": { "AWS": "arn:aws:iam::123456789012:role/kyc-app-role" },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::acme-kyc/*"
    }
  ]
}
```

```bash
aws s3api put-bucket-policy --bucket acme-kyc --policy file://policy.json
aws s3api put-bucket-encryption --bucket acme-kyc --server-side-encryption-configuration \
  '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"aws:kms","KMSMasterKeyID":"alias/kyc"},"BucketKeyEnabled":true}]}'
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `s3api` | S3 low-level API → `s3`-এর উচ্চ-স্তরের কমান্ডে নেই এমন সব সেটিং |
| `BlockPublicAcls` / `IgnorePublicAcls` | নতুন public ACL বন্ধ / পুরনো public ACL উপেক্ষা |
| `BlockPublicPolicy` / `RestrictPublicBuckets` | public bucket policy বসানো বন্ধ / public policy থাকলেও বাইরের প্রবেশ বন্ধ |
| `s3control --account-id` | account-level → পুরো account-এর সব bucket-এ প্রযোজ্য |
| `aws:SecureTransport false` | HTTPS কিনা → HTTP দিয়ে আসা অনুরোধ নিষেধ |
| `SSEAlgorithm aws:kms`, `BucketKeyEnabled` | KMS key দিয়ে encryption · bucket key → KMS call কমিয়ে খরচ কমায় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** S3 bucket কীভাবে নিরাপদ রাখবে?
**উত্তর:** Account ও bucket স্তরে Block Public Access, ACL বন্ধ, least-privilege bucket/IAM policy, HTTPS বাধ্যতামূলক, সংবেদনশীল data-তে SSE-KMS, versioning ও MFA Delete/Object Lock প্রয়োজনে, access logging/CloudTrail data events, আর Access Analyzer/Macie দিয়ে নিয়মিত পরীক্ষা।

**প্রশ্ন:** SSE-S3 আর SSE-KMS-এর পার্থক্য?
**উত্তর:** SSE-S3-এ S3 নিজের key পরিচালনা করে, আলাদা নিয়ন্ত্রণ নেই। SSE-KMS-এ KMS key; key policy দিয়ে কে decrypt করতে পারবে নিয়ন্ত্রণ, CloudTrail-এ key ব্যবহারের log; তবে KMS-এর খরচ ও সীমা আছে।

### Storage class, Versioning ও Lifecycle

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Storage class** | দাম আর প্রাপ্যতার স্তর |
| **IA** | **I**nfrequent **A**ccess → কম ব্যবহৃত |
| **Glacier** | S3 Glacier → archive, খুব সস্তা, ফেরত আনতে সময়/খরচ |
| **Versioning** | প্রতিটি পরিবর্তনের আগের version রাখা |
| **Lifecycle** | lifecycle rule → বয়স অনুযায়ী class বদল বা মুছে ফেলা |

**সহজ ভাষায়:** সব data একই রকম না: নতুন ছবি প্রতিদিন দেখা হয় (Standard), ৩ মাসের পুরনো log কালেভদ্রে (Standard-IA), ১ বছরের audit data প্রায় কখনো না (Glacier)। Lifecycle rule দিয়ে স্বয়ংক্রিয় স্থানান্তর আর মোছা। Versioning চালু থাকলে ভুল করে মোছা বা overwrite করা ফাইল ফেরত আনা যায়; ransomware বা ভুল `sync --delete` থেকে রক্ষা। Versioning-এর সাথে পুরনো version মোছার lifecycle নিয়মও দিও, নইলে খরচ বাড়তেই থাকে।

> **বাস্তব পরিস্থিতি:** একজন intern `aws s3 sync --delete` ভুল ফোল্ডার থেকে চালিয়ে website-এর অর্ধেক ছবি মুছে দিল। Versioning চালু থাকায় delete marker সরিয়ে ১০ মিনিটে সব ফেরত। আর log bucket-এ lifecycle দিয়ে ৩০ দিনে IA, ৯০ দিনে Glacier, ৩৬৫ দিনে মুছে ফেলায় storage খরচ ৭০% কম।

| Class | কখন | বিশেষত্ব |
|---|---|---|
| Standard | ঘন ঘন ব্যবহৃত | সবচেয়ে দামি storage, retrieval ফ্রি |
| Intelligent-Tiering | ব্যবহারের ধরন অজানা | নিজে নিজে স্তর বদলায়, ছোট monitoring ফি |
| Standard-IA | মাসে একবারের মতো | সস্তা storage, retrieval ফি, ন্যূনতম ৩০ দিন |
| One Zone-IA | আবার বানানো যায় এমন কম ব্যবহৃত data | একটা AZ, আরও সস্তা |
| Glacier Instant Retrieval | বছরে কয়েকবার, তবে তাৎক্ষণিক লাগে | মিলিসেকেন্ডে পড়া |
| Glacier Flexible Retrieval | archive | মিনিট থেকে ঘণ্টায় ফেরত |
| Glacier Deep Archive | দীর্ঘমেয়াদি compliance | সবচেয়ে সস্তা, ১২-৪৮ ঘণ্টায় ফেরত |

```bash
aws s3api put-bucket-versioning --bucket acme-site --versioning-configuration Status=Enabled
aws s3api list-object-versions --bucket acme-site --prefix img/logo.png
# delete marker মুছে দিলেই আগের version ফেরত
aws s3api delete-object --bucket acme-site --key img/logo.png --version-id <DELETE_MARKER_ID>
```

```jsonc
// lifecycle.json
{
  "Rules": [{
    "ID": "logs-tiering",
    "Filter": { "Prefix": "logs/" },
    "Status": "Enabled",
    "Transitions": [
      { "Days": 30, "StorageClass": "STANDARD_IA" },
      { "Days": 90, "StorageClass": "GLACIER" }
    ],
    "Expiration": { "Days": 365 },
    "NoncurrentVersionExpiration": { "NoncurrentDays": 30 },
    "AbortIncompleteMultipartUpload": { "DaysAfterInitiation": 7 }
  }]
}
```

```bash
aws s3api put-bucket-lifecycle-configuration --bucket acme-logs --lifecycle-configuration file://lifecycle.json
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `Status=Enabled` | versioning চালু; একবার চালু করলে শুধু Suspended করা যায়, বন্ধ নয় |
| delete marker | versioned bucket-এ মুছলে আসলে একটা "মোছা" চিহ্ন বসে; সেটা সরালেই ফেরত |
| `Transitions Days 30 / 90` | ৩০ দিনে IA, ৯০ দিনে Glacier |
| `Expiration 365` | ১ বছর পর মুছে ফেলো |
| `NoncurrentVersionExpiration` | পুরনো version ৩০ দিন পর মোছো, খরচ নিয়ন্ত্রণে |
| `AbortIncompleteMultipartUpload` | অসম্পূর্ণ বড় upload-এর টুকরো ৭ দিনে পরিষ্কার (লুকানো খরচ) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Versioning চালু থাকা bucket-এ object মুছলে কী হয়?
**উত্তর:** Object আসলে মোছে না; একটা delete marker বসে যা সর্বশেষ version হয়ে যায়। Delete marker মুছলে বা নির্দিষ্ট version ID দিয়ে আগের version ফেরত পাওয়া যায়।

**প্রশ্ন:** Lifecycle দিয়ে খরচ কীভাবে কমাবে?
**উত্তর:** ব্যবহারের ধরন অনুযায়ী পুরনো data-কে IA/Glacier-এ সরাও, মেয়াদ শেষে মোছো, পুরনো version আর অসম্পূর্ণ multipart upload পরিষ্কার করো; ধরন অজানা হলে Intelligent-Tiering।

### Static website: S3 + CloudFront (OAC)

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CloudFront** | Amazon CloudFront → AWS-এর CDN |
| **CDN** | **C**ontent **D**elivery **N**etwork → কাছের edge থেকে content |
| **OAC** | **O**rigin **A**ccess **C**ontrol → শুধু CloudFront-ই private bucket পড়তে পারবে |
| **ACM** | AWS **C**ertificate **M**anager → বিনামূল্যে TLS certificate |

**সহজ ভাষায়:** S3-এর নিজের website endpoint শুধু HTTP দেয় আর bucket public রাখতে হয়। আধুনিক উপায়: bucket private, সামনে CloudFront, OAC দিয়ে শুধু CloudFront-এর অনুমতি, ACM certificate দিয়ে HTTPS (CloudFront-এর জন্য certificate `us-east-1`-এ বানাতে হয়), আর নিজের domain Route 53 থেকে। Deploy-এর পর পরিবর্তিত ফাইলের জন্য cache invalidation।

> **বাস্তব পরিস্থিতি:** কোম্পানির React marketing site আগে একটা EC2-তে nginx-এ চলত, মাসে ৩০ ডলার আর patch-এর ঝামেলা। S3 + CloudFront-এ সরানোর পর খরচ কয়েক ডলার, বাংলাদেশ থেকেও দ্রুত (কাছের edge), সার্ভার patch-এর ঝামেলা নেই।

```bash
# ১. build upload
npm run build
aws s3 sync ./dist s3://acme-site-prod/ --delete

# ২. CloudFront distribution (console-এ সহজ):
#    origin = acme-site-prod S3, Origin access = OAC,
#    Viewer protocol = Redirect HTTP to HTTPS,
#    Default root object = index.html, Alternate domain = www.acme.com, ACM cert (us-east-1)
```

```jsonc
// ৩. bucket policy (CloudFront তৈরি করে দেয়, মূল অংশ)
{
  "Effect": "Allow",
  "Principal": { "Service": "cloudfront.amazonaws.com" },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::acme-site-prod/*",
  "Condition": {
    "StringEquals": { "AWS:SourceArn": "arn:aws:cloudfront::123456789012:distribution/E1ABCDEF" }
  }
}
```

```bash
# ৪. প্রতিটি deploy-এর পর
aws cloudfront create-invalidation --distribution-id E1ABCDEF --paths "/index.html" "/assets/*"
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `create-invalidation --paths` | cache invalidation → edge-এর cache থেকে এই path গুলো সরাও, নতুন ফাইল আসবে |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| OAC | bucket private থাকে, শুধু এই distribution পড়তে পারে |
| `AWS:SourceArn` condition | অন্য কারো CloudFront দিয়ে তোমার bucket পড়া আটকায় |
| ACM cert `us-east-1` | CloudFront global, তার certificate সবসময় N. Virginia-তে |
| hashed asset নাম (`app.3f2a.js`) | এগুলো invalidate করতে হয় না; শুধু `index.html` |

---

## ০৫. AWS: Scenario ভিত্তিক অনুশীলন (Lab)

নিজের account-এ করো, budget alert চালু রেখে। প্রতিটি lab শেষে resource মুছে ফেলো (শেষ lab-এ পরিষ্কারের তালিকা আছে)। প্রথমে কাজ পড়ে নিজে চেষ্টা, তারপর সমাধান।

### Lab ১: সম্পূর্ণ VPC নিজে বানানো (CLI দিয়ে)

`🟡 ইন্টারমিডিয়েট`

**কাজ (Task):** `10.20.0.0/16` VPC বানাও, একটা public আর একটা private subnet (`ap-southeast-1a`), Internet Gateway, public route table, আর public subnet-এ চালু হওয়া instance স্বয়ংক্রিয় public IP পাবে।

> **পরিস্থিতি:** Console-এ ক্লিক করে বানানো VPC কেউ পুনরায় বানাতে পারে না। CLI/IaC-এ বানালে প্রতিটি ধাপ স্পষ্ট আর পুনরাবৃত্তিযোগ্য। (বাস্তবে এটাই পরে Terraform-এ লিখবে।)

```bash
export AWS_REGION=ap-southeast-1
VPC=$(aws ec2 create-vpc --cidr-block 10.20.0.0/16 \
  --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=lab-vpc}]' \
  --query Vpc.VpcId --output text)
aws ec2 modify-vpc-attribute --vpc-id $VPC --enable-dns-hostnames

PUB=$(aws ec2 create-subnet --vpc-id $VPC --cidr-block 10.20.0.0/24 \
  --availability-zone ap-southeast-1a --query Subnet.SubnetId --output text)
PRIV=$(aws ec2 create-subnet --vpc-id $VPC --cidr-block 10.20.10.0/24 \
  --availability-zone ap-southeast-1a --query Subnet.SubnetId --output text)

IGW=$(aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text)
aws ec2 attach-internet-gateway --internet-gateway-id $IGW --vpc-id $VPC

RT=$(aws ec2 create-route-table --vpc-id $VPC --query RouteTable.RouteTableId --output text)
aws ec2 create-route --route-table-id $RT --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW
aws ec2 associate-route-table --route-table-id $RT --subnet-id $PUB
aws ec2 modify-subnet-attribute --subnet-id $PUB --map-public-ip-on-launch

echo "VPC=$VPC PUB=$PUB PRIV=$PRIV IGW=$IGW RT=$RT"
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `VAR=$(... --query X --output text)` | তৈরি হওয়া ID সরাসরি variable-এ, পরের কমান্ডে ব্যবহার |
| `--enable-dns-hostnames` | instance গুলো DNS নাম পাবে |
| `attach-internet-gateway` | IGW তৈরি করলেই হয় না, VPC-তে যুক্ত করতে হয় |
| `create-route 0.0.0.0/0 -> IGW` | এই route table যুক্ত subnet-ই public |
| `associate-route-table` | public subnet-কে এই route table দাও; private subnet main route table-এ (শুধু local) |
| `--map-public-ip-on-launch` | এই subnet-এ নতুন instance নিজে public IP পাবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** একটা subnet-কে public বানাতে কী কী লাগে?
**উত্তর:** VPC-তে যুক্ত Internet Gateway, subnet-এর route table-এ `0.0.0.0/0 -> IGW`, আর instance-এর public IP (auto-assign বা Elastic IP); সাথে SG/NACL-এ অনুমতি।

### Lab ২: Web server চালু, তারপর "খুলছে না" সমস্যা সমাধান

`🟢 বেসিক`

**কাজ (Task):** Lab ১-এর public subnet-এ user data সহ nginx সার্ভার চালাও (আগের EC2 কার্ডের script)। Browser-এ public IP খোলো। না খুললে কারণ খুঁজে ঠিক করো।

> **পরিস্থিতি:** সার্ভার running, status check ২/২ পাস, তবু browser-এ timeout।

```bash
# web SG বানাও কিন্তু (ইচ্ছা করে) কোনো inbound নিয়ম ছাড়া
SG=$(aws ec2 create-security-group --group-name lab-web --description web --vpc-id $VPC \
  --query GroupId --output text)
# instance চালু (run-instances, subnet $PUB, sg $SG, user-data, IMDSv2)

# খোঁজার ক্রম:
aws ec2 describe-instances --instance-ids $I \
  --query "Reservations[0].Instances[0].[State.Name,PublicIpAddress,SubnetId]"
curl -m 5 http://<public-ip>          # timeout = network/firewall স্তরে আটকাচ্ছে
aws ec2 describe-security-groups --group-ids $SG --query "SecurityGroups[0].IpPermissions"
# -> [] : inbound নিয়ম নেই!

# সমাধান: শুধু নিজের IP থেকে 80
MYIP=$(curl -s https://checkip.amazonaws.com)
aws ec2 authorize-security-group-ingress --group-id $SG --protocol tcp --port 80 --cidr $MYIP/32
curl http://<public-ip>               # Hello from ip-10-20-0-x
```

| লক্ষণ | সম্ভাব্য কারণ |
|---|---|
| Timeout | SG/NACL-এ port বন্ধ, route table-এ IGW নেই, public IP নেই |
| Connection refused | network ঠিক আছে, কিন্তু nginx চলছে না বা ভুল port; user data fail (cloud-init log) |
| পুরনো/ভুল পেজ | user data চলেছে কিনা, ভুল instance-এর IP |
| Stop/start-এর পর পৌঁছানো যায় না | public IP বদলে গেছে, Elastic IP দাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** EC2-এর website খুলছে না। ধাপে ধাপে কী দেখবে?
**উত্তর:** Instance running আর status check, public IP আছে, subnet-এর route table-এ IGW, SG inbound-এ port, NACL, তারপর instance-এর ভেতরে সার্ভিস চলছে কিনা আর সঠিক port-এ শুনছে কিনা (SSM দিয়ে ঢুকে), user data log।

### Lab ৩: Access key ছাড়া EC2 থেকে S3 পড়া (IAM role)

`🟡 ইন্টারমিডিয়েট`

**কাজ (Task):** একটা bucket-এ ফাইল রাখো। EC2-কে শুধু সেই bucket পড়ার role দাও (instance profile)। Instance-এর ভেতরে `aws s3 cp` চালিয়ে দেখো, কোনো `aws configure` ছাড়াই কাজ করে। তারপর অন্য bucket-এ লেখার চেষ্টা করে AccessDenied দেখো।

> **পরিস্থিতি:** আগের developer সার্ভারে `~/.aws/credentials`-এ নিজের admin key রেখে গিয়েছিল। সেটা সরিয়ে নিরাপদ পদ্ধতিতে নিতে হবে।

```jsonc
// role (trust: ec2.amazonaws.com) + এই inline policy (read.json)
{
  "Version": "2012-10-17",
  "Statement": [
    { "Effect": "Allow", "Action": "s3:ListBucket", "Resource": "arn:aws:s3:::acme-lab-assets" },
    { "Effect": "Allow", "Action": "s3:GetObject",  "Resource": "arn:aws:s3:::acme-lab-assets/*" }
  ]
}
```

```bash
aws iam put-role-policy --role-name lab-ec2-role --policy-name read-assets --policy-document file://read.json
aws ec2 associate-iam-instance-profile --instance-id $I --iam-instance-profile Name=lab-ec2-profile

# instance-এর ভেতরে (SSM দিয়ে ঢুকে)
aws sts get-caller-identity                          # arn:aws:sts::...:assumed-role/lab-ec2-role/i-0abc
aws s3 cp s3://acme-lab-assets/hello.txt -
aws s3 cp /etc/hostname s3://acme-lab-assets/x.txt   # AccessDenied (লেখার অনুমতি নেই)
rm -f ~/.aws/credentials                             # পুরনো স্থায়ী key সরাও (আর IAM-এ সেই key বাতিল করো)
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `put-role-policy` | role-এ inline policy (শুধু এই role-এর) |
| `associate-iam-instance-profile` | চলমান instance-এ role যুক্ত (নতুন instance-এ run-instances-এ দেওয়া যায়) |
| `assumed-role/.../i-0abc` | credential এসেছে role থেকে, instance ID সহ |
| `AccessDenied` | least privilege কাজ করছে: শুধু পড়া |
| `~/.aws/credentials` থাকলে | CLI আগে সেটাই নেয়, role নয়; credential-এর ক্রম মনে রাখো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** AWS CLI/SDK কোন ক্রমে credential খোঁজে?
**উত্তর:** কমান্ডের অপশন, environment variable (`AWS_ACCESS_KEY_ID` ইত্যাদি), shared credentials/config ফাইল (profile, SSO), container credential (ECS), তারপর instance metadata (EC2 role)। তাই সার্ভারে পুরনো ফাইল থাকলে role উপেক্ষিত হয়।

### Lab ৪: Private সার্ভারে SSH ছাড়া প্রবেশ ও NAT

`🔴 অ্যাডভান্সড`

**কাজ (Task):** Lab ১-এর private subnet-এ একটা instance চালাও (public IP নেই, SG-তে কোনো inbound নেই)। SSM Session Manager দিয়ে ঢোকো। তারপর ভেতর থেকে `dnf update` চালাও; প্রথমে fail করবে, NAT Gateway যোগ করে ঠিক করো।

> **পরিস্থিতি:** Security নিয়ম: কোনো সার্ভারে port 22 খোলা যাবে না, আর app সার্ভার ইন্টারনেট থেকে দেখা যাবে না।

```bash
# NAT Gateway (public subnet-এ) + private route table
EIP=$(aws ec2 allocate-address --domain vpc --query AllocationId --output text)
NAT=$(aws ec2 create-nat-gateway --subnet-id $PUB --allocation-id $EIP \
  --query NatGateway.NatGatewayId --output text)
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT

PRT=$(aws ec2 create-route-table --vpc-id $VPC --query RouteTable.RouteTableId --output text)
aws ec2 create-route --route-table-id $PRT --destination-cidr-block 0.0.0.0/0 --nat-gateway-id $NAT
aws ec2 associate-route-table --route-table-id $PRT --subnet-id $PRIV

# private instance: role-এ AmazonSSMManagedInstanceCore, SG-তে inbound কিছু না
aws ssm describe-instance-information --query "InstanceInformationList[].[InstanceId,PingStatus]"
aws ssm start-session --target i-0priv
# ভেতরে: sudo dnf -y update (এখন NAT দিয়ে কাজ করবে)

# সতর্কতা: NAT Gateway ঘণ্টা হিসেবে বিল হয়; lab শেষে মুছে দাও
aws ec2 delete-nat-gateway --nat-gateway-id $NAT && aws ec2 release-address --allocation-id $EIP
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `allocate-address --domain vpc` | NAT-এর জন্য Elastic IP |
| `wait nat-gateway-available` | প্রস্তুত না হওয়া পর্যন্ত অপেক্ষা (CLI waiter) |
| `route 0.0.0.0/0 -> NAT` | private subnet-কে শুধু বাইরে যাওয়ার পথ |
| `PingStatus Online` | SSM agent AWS-এর সাথে কথা বলতে পারছে; না হলে role, NAT/VPC endpoint (ssm, ssmmessages, ec2messages) দেখো |
| SSH key বা port 22 ছাড়াই shell | IAM দিয়ে নিয়ন্ত্রিত, session log সম্ভব |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** NAT ছাড়া private subnet-এর instance কীভাবে SSM বা S3 ব্যবহার করতে পারে?
**উত্তর:** VPC endpoint দিয়ে: S3-এর জন্য gateway endpoint (বিনামূল্যে), SSM-এর জন্য ssm, ssmmessages, ec2messages interface endpoint; তখন traffic AWS নেটওয়ার্কের ভেতরে থাকে।

### Lab ৫: ভুল করে মোছা ফাইল ফেরত আনা (Versioning)

`🟢 বেসিক`

**কাজ (Task):** একটা bucket-এ versioning চালু করো, একটা ফাইল upload করো, overwrite করো, তারপর মুছে দাও। প্রথম version টা ফেরত আনো।

> **পরিস্থিতি:** মার্কেটিং টিম ভুল করে নতুন ব্যানার দিয়ে পুরনোটা overwrite করেছে, তারপর ভয়ে মুছেও ফেলেছে। পুরনোটাই দরকার।

```bash
B=acme-lab-versioning-$RANDOM
aws s3 mb s3://$B
aws s3api put-bucket-versioning --bucket $B --versioning-configuration Status=Enabled
echo "v1 banner" > banner.txt && aws s3 cp banner.txt s3://$B/
echo "v2 banner" > banner.txt && aws s3 cp banner.txt s3://$B/
aws s3 rm s3://$B/banner.txt
aws s3 ls s3://$B/                    # কিছু নেই মনে হয়

aws s3api list-object-versions --bucket $B --prefix banner.txt \
  --query "{versions:Versions[].[VersionId,IsLatest,LastModified],markers:DeleteMarkers[].[VersionId,IsLatest]}"

# সবচেয়ে পুরনো (v1) version-এর ID দিয়ে নামাও
aws s3api get-object --bucket $B --key banner.txt --version-id <V1_ID> restored.txt && cat restored.txt

# বা delete marker মুছে v2-কে আবার সর্বশেষ বানাও
aws s3api delete-object --bucket $B --key banner.txt --version-id <MARKER_ID>
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `$RANDOM` | bucket নাম অনন্য রাখতে এলোমেলো সংখ্যা |
| `list-object-versions` | সব version আর delete marker, IsLatest সহ |
| `get-object --version-id` | নির্দিষ্ট পুরনো version নামাও |
| `delete-object --version-id <MARKER_ID>` | শুধু delete marker টা সরাও, ফাইল ফিরে আসে |

### Lab ৬: Lab শেষে সব পরিষ্কার ও খরচ যাচাই

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Cost Explorer** | AWS Cost Explorer → খরচ বিশ্লেষণের টুল |
| **Tag Editor** | Resource Groups Tag Editor → tag দিয়ে সব resource খোঁজা |

**কাজ (Task):** Lab-এর সব resource খুঁজে মুছে ফেলো, যাতে অপ্রত্যাশিত বিল না আসে। তারপর Cost Explorer আর Budgets দেখো।

> **পরিস্থিতি:** অনেকে অনুশীলনের পর একটা NAT Gateway বা Elastic IP ভুলে যায়, মাস শেষে ৩০-৫০ ডলারের বিল।

```bash
# চলমান instance (সব Region-এ দেখা ভালো)
aws ec2 describe-instances --filters Name=instance-state-name,Values=running \
  --query "Reservations[].Instances[].[InstanceId,Tags[?Key=='Name']|[0].Value]" --output table
aws ec2 terminate-instances --instance-ids i-0abc i-0def

aws ec2 describe-nat-gateways --filter Name=state,Values=available --query "NatGateways[].NatGatewayId"
aws ec2 describe-addresses --query "Addresses[?AssociationId==null].AllocationId"          # অব্যবহৃত EIP
aws ec2 describe-volumes --filters Name=status,Values=available --query "Volumes[].VolumeId"  # অযুক্ত EBS

# VPC মোছার ক্রম: instance -> NAT -> EIP -> IGW detach+delete -> subnet -> route table -> SG -> VPC
aws ec2 detach-internet-gateway --internet-gateway-id $IGW --vpc-id $VPC
aws ec2 delete-internet-gateway --internet-gateway-id $IGW
aws ec2 delete-subnet --subnet-id $PUB && aws ec2 delete-subnet --subnet-id $PRIV
aws ec2 delete-route-table --route-table-id $RT
aws ec2 delete-security-group --group-id $SG
aws ec2 delete-vpc --vpc-id $VPC
aws s3 rb s3://$B --force             # versioned bucket-এ সব version আগে মুছতে হয়
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `AssociationId==null` | কোনো কিছুর সাথে যুক্ত নয় এমন Elastic IP: এর জন্য বিল হয় |
| `status=available` volume | কোনো instance-এ যুক্ত নয় এমন EBS: বিল চলছে |
| মোছার ক্রম | নির্ভরতা থাকলে VPC মোছা যায় না (`DependencyViolation`) |
| `s3 rb --force` | bucket ও তার সব object মোছো (versioned হলে পুরনো version আলাদা মুছতে হতে পারে) |

> 💡 **প্রো টিপ:** অনুশীলনের সব resource-এ `Project=lab` tag দাও; পরে Tag Editor-এ সেই tag দিয়ে একবারে সব খুঁজে পাবে।

---

## ০৬. খরচ ও Well-Architected: ভালো নকশার নীতি

AWS-এ ভুল নকশা মানে হয় নিরাপত্তা ঝুঁকি, নয়তো বিশাল বিল। AWS-এর নিজস্ব Well-Architected framework-এর ছয়টা স্তম্ভ মনে রাখো।

### খরচ কোথা থেকে আসে আর কীভাবে কমাবে

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Data transfer** | ইন্টারনেটে বা AZ/Region-এর মধ্যে data যাওয়ার খরচ |
| **Budgets** | AWS Budgets → সীমা আর সতর্কতা |
| **Cost allocation tag** | tag অনুযায়ী বিল ভাগ |
| **Compute Optimizer** | AWS Compute Optimizer → সঠিক আকারের পরামর্শ |

**সহজ ভাষায়:** নতুনরা শুধু EC2-এর ঘণ্টার দাম দেখে, কিন্তু বিলে আসে NAT Gateway, data transfer (ইন্টারনেটে যাওয়া, AZ-এর মধ্যে), অযুক্ত EBS, পুরনো snapshot, অব্যবহৃত Elastic IP (public IPv4-এর জন্য এখন ঘণ্টা হিসেবে চার্জ), CloudWatch log, আর ভুলে যাওয়া resource। Tag দিয়ে খরচ টিম/প্রজেক্ট অনুযায়ী ভাগ করো, Budgets দিয়ে সতর্কতা।

> **বাস্তব পরিস্থিতি:** একটা স্টার্টআপের বিল হঠাৎ দ্বিগুণ। Cost Explorer-এ service অনুযায়ী দেখে বোঝা গেল "EC2-Other" (NAT Gateway data processing) বেড়েছে: নতুন একটা সার্ভিস S3 থেকে প্রতিদিন ৫ TB NAT দিয়ে টানছিল। S3 gateway endpoint যোগ করে সমাধান।

| খরচের উৎস | কমানোর উপায় |
|---|---|
| বড় বা অলস EC2 | rightsizing (Compute Optimizer), রাতে dev বন্ধ, Graviton |
| ২৪/৭ চলা compute | Savings Plans |
| বাধা সহনশীল কাজ | Spot |
| NAT Gateway data | S3/DynamoDB gateway endpoint, interface endpoint, একই AZ-এ traffic |
| ইন্টারনেটে data transfer | CloudFront, compression |
| S3 storage | lifecycle, Intelligent-Tiering, পুরনো version মোছা |
| অযুক্ত EBS, পুরনো snapshot, অব্যবহৃত EIP | নিয়মিত পরিষ্কার, DLM retention |
| CloudWatch Logs | retention ঠিক করা (default চিরকাল) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** AWS বিল হঠাৎ বেড়ে গেলে কীভাবে তদন্ত করবে?
**উত্তর:** Cost Explorer-এ service, region, usage type আর tag অনুযায়ী দিন ধরে দেখো কোনটা বেড়েছে; CloudTrail-এ সেই সময়ে কে কী তৈরি করেছে; অনাকাঙ্ক্ষিত resource (সব Region-এ) খোঁজো; key ফাঁসের সম্ভাবনা যাচাই; তারপর Budgets/anomaly detection চালু করো।

### Well-Architected Framework-এর ৬টি স্তম্ভ

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Well-Architected** | AWS Well-Architected Framework → ভালো cloud নকশার নির্দেশিকা |

**সহজ ভাষায়:** AWS-এর নিজস্ব নির্দেশিকা, interview-এ প্রায়ই জিজ্ঞেস করে। ছয়টা স্তম্ভ মনে রাখো আর প্রতিটির একটা করে বাস্তব উদাহরণ বলতে পারো।

> **বাস্তব পরিস্থিতি:** নতুন একটা প্রজেক্টের নকশা review-এ প্রতিটি স্তম্ভ ধরে প্রশ্ন করা হলো, আর দেখা গেল database single-AZ (Reliability), কোনো budget নেই (Cost), আর সব instance-এ SSH খোলা (Security)। Production-এর আগেই ঠিক।

| স্তম্ভ | মূল প্রশ্ন | EC2/S3/VPC/IAM-এ উদাহরণ |
|---|---|---|
| Operational Excellence | কীভাবে চালাই ও উন্নত করি | IaC, CI/CD, runbook, CloudWatch alarm |
| Security | কীভাবে রক্ষা করি | least privilege IAM, MFA, private subnet, SG, S3 BPA, encryption, CloudTrail |
| Reliability | ব্যর্থতা থেকে কীভাবে ফিরি | Multi-AZ, ASG, ALB health check, backup/snapshot, versioning |
| Performance Efficiency | সঠিক resource ঠিকমতো ব্যবহার করছি | সঠিক instance type, CloudFront, auto scaling |
| Cost Optimization | অপ্রয়োজনীয় খরচ এড়াচ্ছি | rightsizing, Savings Plans, Spot, lifecycle, tag, budget |
| Sustainability | পরিবেশের প্রভাব কমাচ্ছি | Graviton, অলস resource বন্ধ, দক্ষ storage class |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** AWS Well-Architected framework-এর স্তম্ভগুলো কী কী?
**উত্তর:** Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability।

---

## ০৭. বাস্তব Incident Playbook

AWS-এর সবচেয়ে কমন সমস্যায় ধাপে ধাপে কী করবে। ইন্টারভিউতে "X কাজ করছে না, কী করবে?" প্রশ্নেও এভাবে উত্তর দিও। বাস্তব কাজেও checklist হিসেবে ব্যবহার করো।

### 🚨 EC2-তে SSH বা SSM দিয়ে ঢুকতে পারছি না

1. Instance running আর status check ২/২? (system check fail = AWS দিক, stop/start)
2. SSH: public IP আছে? SG-তে 22 তোমার IP থেকে? NACL? route table-এ IGW?
3. সঠিক key আর user? (Amazon Linux: `ec2-user`, Ubuntu: `ubuntu`); `chmod 400 key.pem`
4. `ssh -v` দিয়ে কোন ধাপে আটকাচ্ছে দেখো (timeout = network, Permission denied = key/user)
5. SSM: role-এ `AmazonSSMManagedInstanceCore`? agent চলছে? NAT বা ssm VPC endpoint আছে? PingStatus Online?
6. শেষ উপায়: EC2 Serial Console, বা volume অন্য instance-এ লাগিয়ে ঠিক করা

### 🚨 AccessDenied error

1. `aws sts get-caller-identity` → আসলে কোন user/role-এ আছি? (ভুল profile খুব কমন)
2. Error message-এ কোন action আর resource বলছে পড়ো
3. Identity policy-তে সেই action+resource-এর Allow আছে? ARN ঠিক (`bucket` বনাম `bucket/*`)?
4. কোথাও explicit Deny? (SCP, permission boundary, bucket policy, KMS key policy)
5. KMS এনক্রিপ্টেড হলে `kms:Decrypt` অনুমতি আছে?
6. `iam simulate-principal-policy` আর CloudTrail-এর `errorCode` দিয়ে যাচাই

### 🚨 S3-এর data public হয়ে গেছে / ফাঁসের সন্দেহ

1. সাথে সাথে bucket আর account স্তরে Block Public Access চালু করো
2. Bucket policy আর ACL থেকে `Principal "*"` সরাও
3. CloudTrail data events / S3 server access log দেখে কে কী ডাউনলোড করেছে বোঝো
4. ফাঁস হওয়া data-তে secret থাকলে rotate করো, প্রয়োজনে আইনি/গ্রাহক জানানো
5. ভবিষ্যতে: SCP দিয়ে BPA বন্ধ করা নিষেধ, Access Analyzer, Macie, Config rule

### 🚨 হঠাৎ বিশাল AWS বিল

1. Cost Explorer: কোন service, কোন Region, কবে থেকে
2. সব Region-এ চলমান EC2/GPU instance খোঁজো (key ফাঁস হলে attacker দূরের Region-এ চালায়)
3. CloudTrail: কে `RunInstances`/`CreateUser` করেছে
4. সন্দেহজনক হলে: access key বাতিল, অজানা user/role মোছো, resource বন্ধ, AWS Support-এ case
5. NAT data, data transfer, ভুলে যাওয়া resource যাচাই
6. Budgets + Cost Anomaly Detection চালু করো

### 🚨 Private subnet থেকে ইন্টারনেট/AWS API-তে যাওয়া যাচ্ছে না

1. Private route table-এ `0.0.0.0/0 -> NAT Gateway` আছে? সেই route table subnet-এর সাথে যুক্ত?
2. NAT Gateway public subnet-এ, available অবস্থায়, Elastic IP সহ?
3. NAT-এর public subnet-এর route table-এ IGW আছে?
4. SG-এর outbound খোলা? NACL-এ outbound আর ফিরতি ephemeral port?
5. শুধু AWS সার্ভিস দরকার হলে VPC endpoint ব্যবহার করো (S3 gateway, interface endpoint)

### 🚨 Stop/start বা reboot-এর পর instance-এ পৌঁছানো যাচ্ছে না

1. Public IP বদলে গেছে? (Elastic IP না থাকলে stop/start-এ নতুন IP) → DNS আপডেট বা EIP
2. Instance store-এর data হারিয়েছে? (stop-এ মুছে যায়)
3. fstab-এ নতুন volume-এর ভুল entry boot আটকাচ্ছে? Serial console বা system log দেখো
4. Status check fail হলে system log: `aws ec2 get-console-output --instance-id i-0abc`

---

## ০৮. ইন্টারভিউ প্রশ্ন ব্যাংক

প্রতিটি অধ্যায়ের কার্ডে থাকা প্রশ্নের বাইরে আরও কিছু জনপ্রিয় প্রশ্ন, লেভেল অনুযায়ী সাজানো। উত্তর মুখস্থ না করে নিজের ভাষায় বলার অনুশীলন করো।

### 🟢 বেসিক লেভেল

**প্রশ্ন ১. EC2, S3, VPC আর IAM কী কাজে লাগে এক লাইনে বলো।**
EC2 ভার্চুয়াল সার্ভার; S3 object storage; VPC তোমার নিজের আলাদা নেটওয়ার্ক; IAM কে কোন AWS resource-এ কী করতে পারবে তার নিয়ন্ত্রণ।

**প্রশ্ন ২. Region আর Availability Zone-এর পার্থক্য?**
Region ভৌগোলিক এলাকা; AZ তার ভেতরে আলাদা বিদ্যুৎ ও নেটওয়ার্কের এক বা একাধিক data center। HA-এর জন্য একাধিক AZ।

**প্রশ্ন ৩. Security Group কী?**
EC2 বা অন্য resource-এর instance স্তরের stateful virtual firewall, শুধু allow নিয়ম; inbound default বন্ধ।

**প্রশ্ন ৪. S3 bucket-এর নাম কেন অনন্য হতে হয়?**
Bucket নাম সব AWS account মিলিয়ে global namespace-এ থাকে আর DNS নাম (`bucket.s3.amazonaws.com`) হিসেবেও ব্যবহৃত হয়।

**প্রশ্ন ৫. IAM user, group, role আর policy কী?**
User = স্থায়ী পরিচয়; group = user-এর দল; role = অস্থায়ী credential দেওয়া পরিচয় যা সার্ভিস বা মানুষ assume করে; policy = অনুমতির JSON।

### 🟡 ইন্টারমিডিয়েট লেভেল

**প্রশ্ন ৬. Public আর private subnet কীভাবে আলাদা করবে?**
Route table দিয়ে: public subnet-এ `0.0.0.0/0 -> Internet Gateway`, private-এ নেই (NAT Gateway দিয়ে শুধু বাইরে)।

**প্রশ্ন ৭. Security Group আর NACL-এর পার্থক্য?**
SG instance স্তরে stateful, শুধু allow, সব নিয়ম একসাথে; NACL subnet স্তরে stateless, allow ও deny, ক্রমানুসারে।

**প্রশ্ন ৮. EC2 থেকে S3-এ নিরাপদে কীভাবে যাবে?**
Instance profile দিয়ে IAM role, least-privilege policy; private subnet হলে S3 gateway endpoint; কোনো access key সার্ভারে নয়।

**প্রশ্ন ৯. S3-এর storage class আর lifecycle কীভাবে খরচ কমায়?**
ব্যবহারের ধরন অনুযায়ী data-কে সস্তা class-এ (IA, Glacier) স্বয়ংক্রিয় সরানো আর মেয়াদ শেষে মোছা; ধরন অজানা হলে Intelligent-Tiering।

**প্রশ্ন ১০. Stop আর terminate-এর পার্থক্য? Instance store-এ কী হয়?**
Stop-এ instance বন্ধ, EBS থাকে, পরে চালু করা যায়; terminate-এ মুছে যায়। Instance store-এর data stop আর terminate দুটোতেই মুছে যায়।

**প্রশ্ন ১১. IMDSv2 কী আর কেন চালু রাখবে?**
Token ভিত্তিক instance metadata service; SSRF আক্রমণে metadata থেকে role credential চুরি ঠেকায়।

**প্রশ্ন ১২. Bastion host-এর আধুনিক বিকল্প কী?**
SSM Session Manager: inbound port বা SSH key ছাড়া IAM নিয়ন্ত্রিত shell, session logging সহ।

### 🔴 অ্যাডভান্সড লেভেল

**প্রশ্ন ১৩. Highly available আর secure 3-tier architecture AWS-এ নকশা করো।**
VPC-তে দুই/তিন AZ জুড়ে public (ALB, NAT), private app (ASG-এর EC2), private data (RDS Multi-AZ) subnet; SG chaining (ALB → app → db), IAM role, SSM, S3 + CloudFront static ফাইলের জন্য, WAF, CloudWatch alarm, backup, আর সবকিছু IaC-এ।

**প্রশ্ন ১৪. IAM policy evaluation logic ব্যাখ্যা করো।**
Default implicit deny; কোথাও explicit deny থাকলে deny; SCP/permission boundary/session policy অনুমতির সীমা দেয়; তারপর identity বা resource policy-তে Allow থাকলে allow।

**প্রশ্ন ১৫. Cross-account access কীভাবে দেবে?**
লক্ষ্য account-এ role, trust policy-তে উৎস account/role Principal (দরকারে ExternalId/MFA condition), permission policy-তে প্রয়োজনীয় অনুমতি; উৎসে `sts:AssumeRole` অনুমতি। S3-এর ক্ষেত্রে bucket policy-তেও সরাসরি অন্য account-কে দেওয়া যায়।

**প্রশ্ন ১৬. NAT Gateway-এর খরচ কীভাবে কমাবে?**
S3/DynamoDB-এর জন্য gateway endpoint, অন্য AWS সার্ভিসের জন্য interface endpoint, ভারী traffic একই AZ-এ রাখা, অপ্রয়োজনীয় outbound কমানো, dev-এ একটা NAT শেয়ার করা।

**প্রশ্ন ১৭. AWS account-এ access key ফাঁস হলে incident response কী হবে?**
Key নিষ্ক্রিয়/মোছা, CloudTrail দিয়ে কার্যকলাপ তদন্ত, তৈরি হওয়া resource ও IAM entity মোছা, অন্য credential rotate, সব Region পরীক্ষা, AWS Support-কে জানানো, মূল কারণ ঠিক করা (role/OIDC, secret scanning, SCP)।

**প্রশ্ন ১৮. S3 data-কে ransomware বা ভুল মোছা থেকে কীভাবে রক্ষা করবে?**
Versioning, MFA Delete বা Object Lock (WORM), অন্য account/Region-এ replication, least privilege (DeleteObject সীমিত), আর backup (AWS Backup)।

---

<p align="center"><sub>AWS Core Services: EC2 · S3 · VPC · IAM · <a href="../README.md">← সব গাইড</a></sub></p>
