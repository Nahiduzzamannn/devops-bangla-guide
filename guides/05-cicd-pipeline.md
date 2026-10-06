# CI/CD Pipeline: Complete Guide

```bash
$ git push origin feat/discount-code   # ...then the robots take over
```

**Continuous Integration & Continuous Deployment — কোড push থেকে live সার্ভার পর্যন্ত, ধাপে ধাপে**

Branching · Build · Test · Security Scan · Deploy কৌশল · Rollback · GitHub Actions ও Jenkins (ভেতরে কীভাবে কাজ করে, লাইন ধরে উদাহরণ) · প্রতিটি ধাপে developer-দের ভুল, কেন করে, কীভাবে ঠিক করবে

| | |
|---|---|
| অধ্যায় | ১০টি + Incident Playbook + প্রশ্ন ব্যাংক |
| টপিক কার্ড | ৩২টি |
| ইন্টারভিউ প্রশ্ন | ৫৫টি |
| Incident Playbook | ৬টি |
| লেভেল | 🟢 বেসিক · 🟡 ইন্টারমিডিয়েট · 🔴 অ্যাডভান্সড |

---

## এই গাইড কীভাবে ব্যবহার করবে

বইটা একটা কোডের যাত্রা অনুসরণ করে: developer-এর branch থেকে শুরু করে pull request, build, test, security scan, artifact, deploy, যাচাই আর rollback পর্যন্ত। প্রতিটি ধাপে আছে **বাস্তব পরিস্থিতি** আর একটা **ভুলের টেবিল**: developer রা কী ভুল করে, কেন করে, আর কীভাবে ঠিক করবে। তারপর GitHub Actions আর Jenkins কীভাবে কাজ করে, পূর্ণ উদাহরণ আর লাইন ধরে ব্যাখ্যাসহ। শেষে একজন developer-এর কোডের পুরো দিনের গল্প, GitOps, সব ভুলের সংকলন, incident playbook আর ইন্টারভিউ প্রশ্ন ব্যাংক।

প্রতিটি কার্ডে আছে **পূর্ণরূপ**, **সহজ ভাষায়** ব্যাখ্যা, উদাহরণ, **লাইন ধরে ব্যাখ্যা**, **ইন্টারভিউ প্রশ্ন** আর **প্রো টিপ**। নিজের GitHub-এ একটা ছোট repo বানিয়ে উদাহরণগুলো চালিয়ে দেখো; GitHub Actions public repo-তে বিনামূল্যে।

> **নোট:** YAML, Jenkinsfile আর কমান্ড ইংরেজিতে রাখা হয়েছে, কারণ ঠিক এভাবেই লিখতে হয়। Account ID, URL আর নামগুলো উদাহরণ মাত্র; নিজের মান বসাবে।

---

## সূচিপত্র

- [০০. শুরুর আগে: CI/CD আসলে কী আর কেন](#০০-শুরুর-আগে-cicd-আসলে-কী-আর-কেন)
- [০১. ধাপ ১: Source Control, Branching ও Pull Request](#০১-ধাপ-১-source-control-branching-ও-pull-request)
- [০২. ধাপ ২: Build](#০২-ধাপ-২-build)
- [০৩. ধাপ ৩: Test](#০৩-ধাপ-৩-test)
- [০৪. ধাপ ৪: Code Quality ও Security Scan (DevSecOps)](#০৪-ধাপ-৪-code-quality-ও-security-scan-devsecops)
- [০৫. ধাপ ৫: Deploy — Environment, Secret ও কৌশল](#০৫-ধাপ-৫-deploy--environment-secret-ও-কৌশল)
- [০৬. GitHub Actions: কীভাবে কাজ করে](#০৬-github-actions-কীভাবে-কাজ-করে)
- [০৭. Jenkins: কীভাবে কাজ করে](#০৭-jenkins-কীভাবে-কাজ-করে)
- [০৮. GitHub Actions বনাম Jenkins বনাম অন্যরা](#০৮-github-actions-বনাম-jenkins-বনাম-অন্যরা)
- [০৯. সব একসাথে: এক developer-এর কোডের পুরো যাত্রা](#০৯-সব-একসাথে-এক-developer-এর-কোডের-পুরো-যাত্রা)
- [১০. Developer-দের সবচেয়ে কমন ভুল: সংকলন](#১০-developer-দের-সবচেয়ে-কমন-ভুল-সংকলন)
- [১১. বাস্তব Incident Playbook](#১১-বাস্তব-incident-playbook)
- [১২. ইন্টারভিউ প্রশ্ন ব্যাংক](#১২-ইন্টারভিউ-প্রশ্ন-ব্যাংক)

---

## ০০. শুরুর আগে: CI/CD আসলে কী আর কেন

একটা ছোট গল্প দিয়ে শুরু: CI/CD ছাড়া একটা কোম্পানির deploy দিন কেমন হয়, আর CI/CD আসার পর কী বদলায়?

### CI/CD ছাড়া জীবন বনাম CI/CD সহ জীবন

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CI** | **C**ontinuous **I**ntegration → অবিরাম একীকরণ: সবার কোড বারবার মিলিয়ে স্বয়ংক্রিয় build ও test |
| **CD** | **C**ontinuous **D**elivery / **C**ontinuous **D**eployment → অবিরাম সরবরাহ / অবিরাম deploy |
| **Pipeline** | কোডের জন্য স্বয়ংক্রিয় ধাপের পাইপলাইন |

**সহজ ভাষায়:** CI/CD হলো একটা স্বয়ংক্রিয় কারখানার লাইন। Developer কোড push করলেই মেশিন নিজে কোড build করে, test চালায়, নিরাপত্তা পরীক্ষা করে, প্যাকেজ বানায়, আর ঠিক থাকলে সার্ভারে পৌঁছে দেয়। মানুষ শুধু কোড লেখে আর review করে; বাকি পুনরাবৃত্ত কাজ মেশিন করে, প্রতিবার একই নিয়মে।

> **বাস্তব পরিস্থিতি:** একটা ই-কমার্স কোম্পানি, CI/CD ছাড়া। শুক্রবার রাত ১১টা। একজন senior developer নিজের ল্যাপটপে build করে FTP দিয়ে ফাইল সার্ভারে তোলেন। একটা config ফাইল ভুলে যান, সাইট ভেঙে যায়। কেউ জানে না আগের version কোনটা ছিল, rollback-এ ২ ঘণ্টা লাগে। একই কোম্পানি CI/CD সহ: developer PR merge করে, ১২ মিনিটে test পাস করে staging-এ যায়, smoke test পাস হলে এক ক্লিকে production। সমস্যা হলে আগের version-এ rollback ২ মিনিটে।

| বিষয় | CI/CD ছাড়া (Manual) | CI/CD সহ (Automated) |
|---|---|---|
| Deploy কতবার | মাসে একবার, ভয়ে ভয়ে | দিনে কয়েকবার, স্বাভাবিকভাবে |
| Test | কেউ মনে থাকলে হাতে চালায় | প্রতিটি push-এ নিজে চলে |
| পরিবেশ | "আমার ল্যাপটপে তো চলে" | প্রতিবার একই পরিষ্কার পরিবেশে build |
| কে deploy করতে পারে | একজন "হিরো" যিনি জানেন কীভাবে | নিয়ম মেনে যে কেউ, বা স্বয়ংক্রিয় |
| Bug কখন ধরা পড়ে | production-এ, গ্রাহক জানায় | PR-এই, merge-এর আগে |
| Rollback | ঘণ্টার পর ঘণ্টা | কয়েক মিনিট, এক ক্লিকে |
| ইতিহাস | কে কী deploy করল জানা নেই | প্রতিটি deploy-এর log, commit, approver |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** CI/CD কী? সহজ ভাষায় বলো।
**উত্তর:** CI মানে প্রতিটি কোড পরিবর্তন স্বয়ংক্রিয়ভাবে build ও test করে মূল কোডের সাথে দ্রুত মেলানো, যাতে সমস্যা শুরুতেই ধরা পড়ে। CD মানে পাস হওয়া কোডকে স্বয়ংক্রিয়ভাবে deploy যোগ্য বানানো (Delivery) বা সরাসরি production-এ পাঠানো (Deployment)।

> 💡 **প্রো টিপ:** CI/CD একটা টুল না, একটা অভ্যাস। GitHub Actions বা Jenkins শুধু সেই অভ্যাসকে স্বয়ংক্রিয় করার যন্ত্র।

### Continuous Integration, Delivery আর Deployment-এর পার্থক্য

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Continuous Integration** | বারবার মেলাও আর পরীক্ষা করো |
| **Continuous Delivery** | সবসময় deploy যোগ্য রাখো, production-এ যেতে মানুষের অনুমোদন |
| **Continuous Deployment** | সব পরীক্ষা পাস হলে মানুষ ছাড়াই production-এ |

**সহজ ভাষায়:** তিনটা ধাপ একটার উপর আরেকটা। CI-তে প্রতিটি push-এ build আর test। Continuous Delivery-তে তার উপর প্রতিটি সফল build একটা release যোগ্য প্যাকেজ হয়ে staging-এ যায়, production-এ যেতে একজন বোতাম চাপে। Continuous Deployment-এ সেই বোতামও নেই: সব পরীক্ষা পাস করলে নিজেই production-এ।

> **বাস্তব পরিস্থিতি:** একটা ব্যাংক Continuous Delivery ব্যবহার করে: সব স্বয়ংক্রিয়, কিন্তু production-এ যাওয়ার আগে release manager অনুমোদন দেন (নিয়ন্ত্রক সংস্থার নিয়ম)। একটা SaaS স্টার্টআপ Continuous Deployment চালায়: main-এ merge হলে ১৫ মিনিটে গ্রাহকের কাছে, দিনে ৩০ বার।

```text
Continuous Integration:  push -> build -> unit test -> lint -> scan -> (পাস/ফেল রিপোর্ট)
Continuous Delivery:     CI -> artifact/image -> staging deploy -> auto test -> [মানুষের অনুমোদন] -> production
Continuous Deployment:   CI -> artifact/image -> staging deploy -> auto test -> production (স্বয়ংক্রিয়)
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Continuous Delivery আর Continuous Deployment-এর পার্থক্য?
**উত্তর:** Delivery-তে production deploy-এর আগে মানুষের অনুমোদন থাকে; Deployment-এ পুরোটাই স্বয়ংক্রিয়, পরীক্ষা পাস করলেই production।

### পুরো যাত্রার মানচিত্র: push থেকে production

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **PR** | **P**ull **R**equest → মূল branch-এ কোড মেলানোর অনুরোধ |
| **Artifact** | build-এর ফল: jar, zip, Docker image |
| **Staging** | production-এর মতো পরীক্ষার পরিবেশ |
| **Rollback** | আগের ভালো version-এ ফেরত যাওয়া |

**সহজ ভাষায়:** নিচের মানচিত্রটা পুরো বইয়ের সারাংশ। প্রতিটি অধ্যায় এর একেকটা ধাপ বিস্তারিত বোঝায়। মনে রাখো, প্রতিটি ধাপ একটা ছাঁকনি: সমস্যা যত আগের ধাপে ধরা পড়ে, ঠিক করা তত সস্তা।

> **বাস্তব পরিস্থিতি:** Developer রহিম একটা "discount code" feature লিখলেন। নিচের ধাপগুলো ধরে তাঁর কোড কীভাবে গ্রাহকের কাছে পৌঁছায়, পরের অধ্যায়গুলোতে সেটাই দেখব।

```text
 ১. Developer: feature branch-এ কোড লেখে, local-এ test করে
 ২. git push -> GitHub/GitLab-এ কোড যায়
 ৩. Pull Request খোলে -> webhook CI-কে জানায়
 ৪. CI: checkout -> dependency install -> lint -> unit test -> build
 ৫. CI: security scan (কোড, dependency, secret, image)
 ৬. সহকর্মী code review করে, সব check সবুজ হলে merge
 ৭. main-এ merge -> আবার CI + Docker image build + tag (commit SHA)
 ৮. Image registry-তে push (একবার build, সব জায়গায় একই image)
 ৯. Staging-এ auto deploy -> integration/E2E/smoke test
১০. অনুমোদন (Delivery) বা স্বয়ংক্রিয় (Deployment)
১১. Production deploy: rolling / blue-green / canary
১২. Deploy-এর পর smoke test + monitoring + alert
১৩. সমস্যা হলে স্বয়ংক্রিয় বা এক ক্লিকে rollback
১৪. Slack/Teams-এ জানানো, DORA metric হিসাব
```

| শব্দ | মানে |
|---|---|
| Trigger / Event | কোন ঘটনায় pipeline চালু হবে (push, PR, schedule, হাতে) |
| Pipeline / Workflow | পুরো স্বয়ংক্রিয় প্রক্রিয়া |
| Stage / Job | বড় ধাপ (build, test, deploy); job আলাদা মেশিনে চলতে পারে |
| Step | job-এর ভেতরের একেকটা কমান্ড |
| Runner / Agent | যে মেশিন আসলে কাজগুলো চালায় |
| Artifact | build-এর ফল যা পরের ধাপে বা deploy-এ যায় |
| Environment | dev, staging, production-এর মতো লক্ষ্য পরিবেশ |
| Secret | password, token, key যা pipeline ব্যবহার করে কিন্তু কেউ দেখে না |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** একটা সাধারণ CI/CD pipeline-এর ধাপগুলো বলো।
**উত্তর:** Source (push/PR) → Build → Test (unit, lint) → Security scan → Artifact/Image → Staging deploy → Integration/E2E test → Approval → Production deploy → Verify/Monitor → Rollback যদি লাগে।

**প্রশ্ন:** Shift left মানে কী?
**উত্তর:** পরীক্ষা ও নিরাপত্তা যাচাই যতটা সম্ভব প্রক্রিয়ার শুরুর দিকে (বাম দিকে) সরিয়ে আনা, যাতে bug ও দুর্বলতা দ্রুত আর সস্তায় ধরা পড়ে।

> 💡 **প্রো টিপ:** এই মানচিত্রটা ইন্টারভিউর আগে নিজের ভাষায় এক মিনিটে বলতে পারার মতো অনুশীলন করো। প্রায় সব DevOps interview-এ এই প্রশ্ন আসে।

---

## ০১. ধাপ ১: Source Control, Branching ও Pull Request

Pipeline-এর শুরু Git থেকে। Branch কীভাবে চলবে, কে কী merge করবে, কোন ঘটনায় pipeline চলবে: এখানে ভুল হলে পুরো pipeline এলোমেলো হয়।

### Branching কৌশল: Trunk-based বনাম GitFlow

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Trunk** | main branch → মূল branch |
| **GitFlow** | develop, release, hotfix সহ বহু-branch মডেল |
| **Feature flag** | কোড deploy হলেও feature বন্ধ রাখার সুইচ |

**সহজ ভাষায়:** Trunk-based-এ সবাই ছোট ছোট short-lived branch বানায় (১-২ দিন), দ্রুত main-এ merge করে; অসম্পূর্ণ feature, feature flag দিয়ে লুকানো থাকে। এটা CI/CD-এর সাথে সবচেয়ে ভালো মানায়। GitFlow-এ `develop`, `release/*`, `hotfix/*`-এর মতো অনেক দীর্ঘস্থায়ী branch; নির্দিষ্ট সময়ে release দেওয়া প্রজেক্টে (mobile app, packaged software) কাজে লাগে, কিন্তু merge সংঘাত বেশি।

> **বাস্তব পরিস্থিতি:** একটা টিম ৩ সপ্তাহ ধরে বড় feature branch-এ কাজ করল। Merge-এর দিন ৪০০ লাইনের conflict, দুই দিন লাগল মেলাতে, আর নতুন bug ঢুকল। পরের quarter-এ তারা trunk-based-এ গেল: প্রতিদিন ছোট PR, অসম্পূর্ণ অংশ feature flag-এর পেছনে। Conflict প্রায় শূন্য।

```text
Trunk-based:
  main ──●──●──●──●──●──●──●──>   (সবসময় deploy যোগ্য)
          \_/   \_/   \_/          (ছোট feature branch, ১-২ দিন)

GitFlow:
  main     ──●───────────●──────>  (শুধু release)
  develop  ──●──●──●──●──●──●───>
  feature     \___/ \_____/
  release              \__/
  hotfix   (main থেকে, main+develop-এ ফেরত)
```

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| দীর্ঘদিনের feature branch | "পুরো feature শেষ হলে একবারে merge করব" | ছোট PR, প্রতিদিন merge, feature flag ব্যবহার |
| সরাসরি main-এ push | তাড়াহুড়ো, "ছোট পরিবর্তন তো" | Branch protection: PR আর সবুজ check ছাড়া merge বন্ধ |
| main ভাঙা অবস্থায় রেখে দেওয়া | "পরে ঠিক করব" | main লাল হলে সবার আগে ঠিক করা টিমের নিয়ম |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Trunk-based development কেন CI/CD-এর জন্য ভালো?
**উত্তর:** ছোট আর ঘন ঘন merge মানে কম conflict, দ্রুত feedback, main সবসময় deploy যোগ্য; বড় অসম্পূর্ণ কাজ feature flag দিয়ে নিরাপদে deploy করা যায়।

### Pull Request, Code Review ও Branch protection

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **PR / MR** | **P**ull **R**equest / **M**erge **R**equest → কোড মেলানোর অনুরোধ (GitHub / GitLab) |
| **CODEOWNERS** | কোন ফোল্ডারের review কে করবে |
| **Status check** | PR-এ CI-এর পাস/ফেল ফলাফল |

**সহজ ভাষায়:** Developer সরাসরি main-এ না দিয়ে PR খোলে। PR খোলার সাথে সাথে CI চলে, আর ফলাফল PR-এর নিচে সবুজ টিক বা লাল ক্রস হিসেবে দেখায়। Branch protection নিয়ম দিয়ে বাধ্য করা যায়: অন্তত একজনের approval, সব check পাস, আর branch আপ-টু-ডেট না হলে merge বন্ধ।

> **বাস্তব পরিস্থিতি:** একজন নতুন developer ভুল করে পুরো database টেবিল drop করার migration সহ সরাসরি main-এ push করল, আর CD সেটা production-এ পাঠিয়ে দিল। এর পর টিম branch protection চালু করল: PR ছাড়া main-এ push অসম্ভব, CODEOWNERS অনুযায়ী migration ফোল্ডারে DBA-এর approval বাধ্যতামূলক।

```text
# .github/CODEOWNERS
/db/migrations/   @acme/dba-team
/infra/           @acme/platform-team
*.tf              @acme/platform-team

# GitHub > Settings > Branches > main-এর নিয়ম:
# [x] Require a pull request before merging (1 approval)
# [x] Require status checks to pass (ci / test, ci / lint)
# [x] Require branches to be up to date before merging
# [x] Require review from Code Owners
# [x] Do not allow bypassing the above settings
```

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| বিশাল PR (২০০০ লাইন) | একসাথে অনেক কাজ জমিয়ে ফেলা | PR ছোট রাখো (৪০০ লাইনের নিচে), এক PR-এ এক উদ্দেশ্য |
| লাল CI থাকা অবস্থায় merge | admin অধিকার দিয়ে bypass | Branch protection-এ admin-ও bypass করতে পারবে না |
| "LGTM" লিখে না পড়েই approve | সময়ের চাপ | Review checklist, ছোট PR, CODEOWNERS |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Branch protection-এ কী কী নিয়ম রাখবে?
**উত্তর:** PR বাধ্যতামূলক, অন্তত একজন reviewer, সব required status check পাস, branch আপ-টু-ডেট, CODEOWNERS review, force push ও মোছা বন্ধ, admin bypass বন্ধ।

### Pipeline কখন চলবে: Trigger ও Webhook

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Webhook** | কোনো ঘটনা ঘটলে HTTP দিয়ে অন্য সিস্টেমকে খবর দেওয়া |
| **Cron** | chronos = সময় → নির্দিষ্ট সময়ে চালানো |
| **Tag** | Git tag → নির্দিষ্ট commit-এর স্থায়ী নাম, যেমন `v2.4.1` |

**সহজ ভাষায়:** Git সার্ভার (GitHub) কোনো ঘটনা ঘটলে (push, PR খোলা, tag) CI সিস্টেমকে webhook পাঠায়। GitHub Actions-এ এটা ভেতরেই হয়; Jenkins-এ GitHub-এর webhook-কে Jenkins-এর URL দিতে হয়। আলাদা ঘটনায় আলাদা কাজ চালানো ভালো অভ্যাস।

> **বাস্তব পরিস্থিতি:** টিম চায়: PR-এ শুধু দ্রুত test (৫ মিনিট), main-এ merge হলে image build ও staging deploy, `v*` tag দিলে production release, আর প্রতি রাতে একবার পুরো E2E আর security scan।

| ঘটনা | কী চালাবে | কেন |
|---|---|---|
| `pull_request` | lint, unit test, build, scan | merge-এর আগে দ্রুত feedback |
| `push` to main | আবার test, image build, staging deploy | main সবসময় staging-এ চলছে |
| tag `v*` | production deploy, release notes | নিয়ন্ত্রিত release |
| `schedule` (রাতে) | পুরো E2E, dependency scan, পরিষ্কার | ধীর কাজ PR-কে ধীর করবে না |
| `workflow_dispatch` (হাতে) | rollback, বিশেষ deploy | জরুরি অবস্থায় মানুষের নিয়ন্ত্রণ |
| `paths` filter | শুধু সংশ্লিষ্ট ফোল্ডার বদলালে | monorepo-তে অপ্রয়োজনীয় কাজ বাঁচাতে |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| সব ঘটনায় সব কাজ চালানো | একটা workflow লিখে সব রেখে দেওয়া | event অনুযায়ী আলাদা job/workflow, paths filter |
| Docs-এর এক লাইন বদলালেও ৪০ মিনিটের pipeline | paths filter না জানা | `paths-ignore: ['**.md', 'docs/**']` |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Webhook আর polling-এর পার্থক্য?
**উত্তর:** Webhook-এ Git সার্ভার ঘটনা ঘটার সাথে সাথে CI-কে জানায় (দ্রুত, কার্যকর)। Polling-এ CI নিজে কিছুক্ষণ পরপর জিজ্ঞেস করে কিছু বদলেছে কিনা (ধীর, অপচয়); webhook সম্ভব না হলে ব্যবহার।

### Versioning ও Release tag

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SemVer** | **Sem**antic **Ver**sioning → `MAJOR.MINOR.PATCH` নিয়মে version |
| **SHA** | **S**ecure **H**ash **A**lgorithm (commit hash) → প্রতিটি commit-এর অনন্য পরিচয় |
| **Conventional Commits** | `feat:`, `fix:` দিয়ে নিয়মিত commit message |

**সহজ ভাষায়:** প্রতিটি build-এর একটা অনন্য, অপরিবর্তনীয় পরিচয় থাকতে হবে, যাতে বলা যায় "production-এ ঠিক এই কোড চলছে"। সাধারণত commit SHA দিয়ে প্রতিটি image tag হয়, আর release-এ SemVer (`v2.4.1`)। MAJOR = পুরনো ব্যবহার ভাঙে এমন বদল, MINOR = নতুন feature, PATCH = bug fix। Conventional commit message থেকে version আর changelog স্বয়ংক্রিয়ভাবে বানানো যায়।

> **বাস্তব পরিস্থিতি:** গ্রাহক বলছে গতকাল থেকে একটা bug। কোন version-এ ঢুকল? Image-এ commit SHA tag থাকায় দেখা গেল production-এ `a1b2c3d` চলছে, আর `git log a1b2c3d`-এ ঠিক সেই পরিবর্তন পাওয়া গেল।

```bash
git commit -m "feat(cart): add discount code support"
git commit -m "fix(payment): handle timeout from gateway"
git commit -m "feat!: remove v1 API"        # ! = breaking change -> MAJOR
git tag -a v2.4.1 -m "Release 2.4.1"
git push origin v2.4.1

# image tag:
# ghcr.io/acme/shop:a1b2c3d     # প্রতিটি commit
# ghcr.io/acme/shop:2.4.1       # release
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `git tag -a -m` | `a` = annotated → লেখক, তারিখ, বার্তাসহ পূর্ণ tag · `m` = message → tag-এর বার্তা |
| `git push origin v2.4.1` | `origin` → মূল remote repository; tag আলাদা করে push করতে হয় |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| সব image-এ শুধু `latest` tag | সহজ মনে হয় | commit SHA আর SemVer tag; `latest` কখনো deploy-এ না |
| একই version tag দুবার ব্যবহার | ভুল ঠিক করে আবার একই নামে push | Tag অপরিবর্তনীয় রাখো, নতুন PATCH version দাও |
| "fixed stuff"-এর মতো commit message | অভ্যাস | Conventional Commits, commitlint দিয়ে CI-তে যাচাই |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** SemVer অনুযায়ী 2.4.1 থেকে breaking change-এ কোন version হবে?
**উত্তর:** 3.0.0। নতুন backward-compatible feature হলে 2.5.0, শুধু bug fix হলে 2.4.2।

---

## ০২. ধাপ ২: Build

CI-এর প্রথম আসল কাজ: পরিষ্কার মেশিনে কোড নামিয়ে dependency ইনস্টল করে চালানো যোগ্য কিছু বানানো। এখানেই ধরা পড়ে "আমার মেশিনে চলে" সমস্যা।

### Reproducible build: প্রতিবার একই ফল

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Lockfile** | প্রতিটি dependency-এর হুবহু version লেখা ফাইল |
| **Reproducible build** | একই কোড থেকে প্রতিবার একই ফল |

**সহজ ভাষায়:** CI runner প্রতিবার একদম খালি মেশিন। তাই build-এ যা লাগে সব কোডে বা config-এ লেখা থাকতে হবে। Dependency-এর version lockfile (`package-lock.json`, `poetry.lock`, `go.sum`) দিয়ে বাঁধতে হবে, আর install-এ এমন কমান্ড দিতে হবে যা lockfile হুবহু মানে (`npm ci`, `pip install -r` with hashes)। Runtime version (Node 20, Python 3.12)-ও নির্দিষ্ট রাখো।

> **বাস্তব পরিস্থিতি:** সোমবার যে কোড ঠিক build হচ্ছিল, বুধবার কোনো পরিবর্তন ছাড়াই CI fail। কারণ `package.json`-এ `"lodash": "^4.17.0"` ছিল আর lockfile commit করা হয়নি; `npm install` নতুন version এনেছে যেটাতে একটা পরিবর্তন ছিল। Lockfile commit আর `npm ci` ব্যবহারে সমাধান।

```bash
# Node
npm ci                                          # npm install নয়

# Python
pip install --require-hashes -r requirements.txt

# Go
go mod download && go build ./...

# .nvmrc / .python-version / go.mod-এ runtime version
# 20.17.0
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `npm ci` | `ci` = clean install → `node_modules` মুছে lockfile হুবহু মেনে ইনস্টল, lockfile না মিললে fail |
| `--require-hashes` | প্রতিটি package-এর hash মিলতে হবে (supply chain নিরাপত্তা) |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| lockfile commit না করা বা `.gitignore`-এ রাখা | "generated ফাইল, দরকার নেই" | lockfile সবসময় commit করো |
| CI-তে `npm install` | local-এর অভ্যাস | CI-তে `npm ci` (বা সমতুল্য) |
| local-এর কোনো global টুলের উপর নির্ভরতা | নিজের মেশিনে আগে থেকে ইনস্টল ছিল | সব টুল pipeline বা Docker image-এ ঘোষণা করো |
| build-এ internet থেকে "latest" কিছু নামানো | সহজ মনে হয় | নির্দিষ্ট version pin করো, checksum যাচাই |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** "আমার মেশিনে চলে কিন্তু CI-তে চলে না" সমস্যার সাধারণ কারণ কী?
**উত্তর:** Lockfile নেই বা মানা হয়নি, ভিন্ন runtime version, local-এ থাকা environment variable বা ফাইল (`.env`), OS-এর পার্থক্য (case-sensitive path), global টুল, বা commit না করা ফাইল।

### Dependency cache ও build দ্রুত করা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Cache** | আগের রানের ফাইল জমিয়ে রেখে আবার ব্যবহার |
| **Cache key** | কোন অবস্থায় cache একই ধরা হবে তার চিহ্ন |

**সহজ ভাষায়:** প্রতিবার সব dependency নতুন করে নামালে মিনিট নষ্ট। Cache-এ `~/.npm`, `~/.cache/pip`, Go module-এর মতো ফোল্ডার রাখা হয়, key হিসেবে lockfile-এর hash: lockfile না বদলালে cache কাজে লাগে। Docker build-এর জন্য layer cache (registry বা gha)।

> **বাস্তব পরিস্থিতি:** PR প্রতি CI ১৪ মিনিট লাগত, এর মধ্যে ৭ মিনিট শুধু `npm install`। Cache যোগ করায় ২ মিনিটে নামল, developer রা আর CI-এর অপেক্ষায় চা খেতে যায় না।

```yaml
- uses: actions/setup-node@v4
  with:
    node-version-file: .nvmrc
    cache: npm                 # ~/.npm নিজে cache করে

- uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: pip-${{ runner.os }}-${{ hashFiles('requirements.txt') }}
    restore-keys: pip-${{ runner.os }}-
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `cache: npm` | setup-node নিজেই npm cache সামলায় |
| `path` | কোন ফোল্ডার cache হবে |
| `key: ...hashFiles('requirements.txt')` | ফাইলের hash বদলালে নতুন cache |
| `restore-keys` | হুবহু key না মিললে এই prefix-এর সবচেয়ে কাছেরটা নাও |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| `node_modules` পুরোটা cache করা | সবচেয়ে সহজ মনে হয় | package manager-এর cache (`~/.npm`) cache করো, `node_modules` নয় |
| ভুল cache key (সবসময় একই) | hash না দিয়ে স্থির নাম | lockfile-এর hash দিয়ে key |

### Artifact: build একবার, deploy অনেকবার

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Artifact** | build-এর তৈরি জিনিস: jar, zip, binary, Docker image |
| **Registry** | artifact রাখার ভাণ্ডার: ECR, GHCR, Nexus, Artifactory |
| **Promotion** | একই artifact-কে পরের পরিবেশে উন্নীত করা |

**সহজ ভাষায়:** সোনালি নিয়ম: build একবার, তারপর সেই একই artifact staging, তারপর production-এ যায়। প্রতিটি পরিবেশে আলাদা করে build করলে staging-এ যা পরীক্ষা হলো আর production-এ যা গেল, দুটো একই নাও হতে পারে। পরিবেশের পার্থক্য (URL, password) artifact-এ না, config/environment variable-এ রাখো।

> **বাস্তব পরিস্থিতি:** Staging-এ সব test পাস। Production-এর জন্য আবার build হলো, আর মাঝখানে একটা dependency-এর নতুন version বেরিয়েছিল। Production ভাঙল, অথচ staging-এ সব ঠিক ছিল। এরপর নিয়ম: staging-এ পরীক্ষিত image-এর হুবহু digest production-এ যাবে।

```text
build ──> image ghcr.io/acme/shop:a1b2c3d (sha256:9f2e...)
            │
            ├──> staging     (একই image, staging config)
            └──> production  (একই image, production config)
```

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| প্রতি পরিবেশে আলাদা build | Dockerfile-এ পরিবেশ অনুযায়ী ফাইল কপি | একটা image, config runtime-এ environment variable বা secret দিয়ে |
| Image-এ production password ঢুকিয়ে build | সহজ মনে হয় | Secret runtime-এ inject করো, image-এ কখনো না |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** "Build once, deploy many" কেন গুরুত্বপূর্ণ?
**উত্তর:** যাতে যে artifact পরীক্ষিত হয়েছে ঠিক সেটাই production-এ যায়; আলাদা build-এ dependency বা পরিবেশ বদলে ভিন্ন ফল হতে পারে।

---

## ০৩. ধাপ ৩: Test

CI-এর হৃদয়। ভালো test ছাড়া CI/CD মানে শুধু দ্রুত bug পাঠানোর যন্ত্র।

### Test pyramid: কোন test কতটা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Unit test** | একটা ছোট function একা পরীক্ষা |
| **Integration test** | কয়েকটা অংশ (API + DB) একসাথে |
| **E2E** | **E**nd-to-**E**nd test → ব্যবহারকারীর মতো পুরো অ্যাপ পরীক্ষা |
| **Smoke test** | deploy-এর পর মূল জিনিস চলছে কিনা দ্রুত দেখা |

**সহজ ভাষায়:** Pyramid-এর নিচে অনেক দ্রুত unit test (মিলিসেকেন্ডে চলে, প্রতিটি PR-এ), মাঝে কিছু integration test (database, queue সহ), উপরে অল্প E2E (browser দিয়ে পুরো flow, ধীর আর ভঙ্গুর)। উল্টো pyramid (শুধু E2E) হলে CI ধীর আর অবিশ্বস্ত হয়।

> **বাস্তব পরিস্থিতি:** Discount code feature: unit test দেখে 20% ছাড়ের হিসাব ঠিক কিনা; integration test দেখে API ঠিকমতো database-এ code খুঁজে পায় কিনা; E2E দেখে ব্যবহারকারী cart-এ code দিয়ে checkout করতে পারে কিনা; deploy-এর পর smoke test দেখে `/health` আর checkout পেজ খুলছে কিনা।

```text
        /\          E2E (কম, ধীর, রাতে বা staging-এ)
       /  \
      /----\        Integration (মাঝারি, PR বা main-এ)
     /      \
    /--------\      Unit (অনেক, দ্রুত, প্রতিটি push-এ)
   /__________\
```

| Test | কখন চলবে | কতক্ষণ |
|---|---|---|
| Lint / format / type check | প্রতিটি push ও PR | সেকেন্ড |
| Unit | প্রতিটি push ও PR | ১-৩ মিনিট |
| Integration | PR ও main | ৩-১০ মিনিট |
| E2E | main/staging deploy-এর পর বা রাতে | ১০-৩০ মিনিট |
| Smoke | প্রতিটি deploy-এর পর | ১ মিনিটের কম |
| Load / performance | release-এর আগে বা নির্ধারিত সময়ে | অনেকক্ষণ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Unit, integration আর E2E test-এর পার্থক্য?
**উত্তর:** Unit একটা ছোট অংশ একা (dependency mock করে), দ্রুত। Integration কয়েকটা আসল অংশ একসাথে (API + database)। E2E পুরো সিস্টেম ব্যবহারকারীর দৃষ্টিকোণ থেকে, সবচেয়ে ধীর ও ভঙ্গুর।

### CI-তে আসল database দিয়ে integration test

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Service container** | job-এর পাশে চলা সহায়ক container (DB, Redis) |
| **Testcontainers** | test কোড থেকেই container চালানোর লাইব্রেরি |

**সহজ ভাষায়:** Integration test-এর জন্য CI-তে একটা আসল (কিন্তু অস্থায়ী) database চালাও: GitHub Actions-এ `services` দিয়ে, Jenkins-এ docker দিয়ে, বা Testcontainers দিয়ে। Shared স্থায়ী test database ব্যবহার কোরো না; একসাথে চলা pipeline গুলো একে অপরের data নষ্ট করবে।

> **বাস্তব পরিস্থিতি:** টিমের সবার pipeline একটা shared "test-db" সার্ভার ব্যবহার করত। দুটো PR একসাথে চললে একটার test অন্যটার data মুছে দিত, এলোমেলোভাবে test fail হতো। প্রতিটি job-এ নিজস্ব Postgres service container দেওয়ার পর সমস্যা চলে গেল।

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: test
          POSTGRES_DB: shop_test
        ports: ["5432:5432"]
        options: >-
          --health-cmd "pg_isready -U postgres"
          --health-interval 5s
          --health-retries 10
    env:
      DATABASE_URL: postgres://postgres:test@localhost:5432/shop_test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - run: npm ci
      - run: npm run migrate
      - run: npm run test:integration
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `services: postgres:` | job-এর পাশে একটা Postgres container চলবে |
| `options: --health-cmd ...` | database প্রস্তুত না হওয়া পর্যন্ত step শুরু হবে না |
| `ports: 5432:5432` | runner থেকে `localhost:5432`-এ পাওয়া যাবে |
| `npm run migrate` | test-এর আগে খালি database-এ টেবিল বানাও |

### Flaky test, ধীর test ও coverage

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Flaky test** | কখনো পাস কখনো ফেল, কোড না বদলেও |
| **Coverage** | code coverage → কোডের কত % test-এ চলেছে |
| **Quality gate** | নির্দিষ্ট মান না পূরণ হলে pipeline থামানো |

**সহজ ভাষায়:** Flaky test CI-এর সবচেয়ে বড় শত্রু: মানুষ লাল দেখেও "আবার চালাও" বলে, ধীরে ধীরে কেউ আর CI বিশ্বাস করে না, আর আসল bug-ও উপেক্ষা হয়। কারণ সাধারণত সময় নির্ভরতা (sleep), test-এর মধ্যে শেয়ার করা অবস্থা, ক্রম নির্ভরতা, বাইরের সার্ভিস, বা concurrency। Coverage একটা দিকনির্দেশক, লক্ষ্য না: ১০০% coverage মানেই ভালো test না।

> **বাস্তব পরিস্থিতি:** একটা checkout test সপ্তাহে ৩-৪ বার এলোমেলোভাবে fail করত। দেখা গেল test-এ `sleep(2)` দিয়ে অপেক্ষা করা হচ্ছিল, CI runner ধীর হলে ২ সেকেন্ডে কাজ শেষ হতো না। Sleep-এর বদলে "অবস্থা না আসা পর্যন্ত অপেক্ষা (wait until)" দিয়ে ঠিক করা হলো।

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Flaky test বারবার re-run করে পাস করানো | deadline-এর চাপ | Flaky test-কে quarantine করো, ticket খোলো, মালিক ঠিক করো, দ্রুত ঠিক করো |
| `sleep()` দিয়ে অপেক্ষা | সহজ মনে হয় | নির্দিষ্ট অবস্থার জন্য wait/poll, timeout সহ |
| test গুলোর মধ্যে শেয়ার করা data | setup বাঁচাতে | প্রতিটি test নিজের data বানায় ও পরিষ্কার করে |
| আসল বাইরের API-কে test-এ ডাকা | mock বানাতে আলস্য | mock/stub বা contract test, sandbox |
| Coverage বাড়াতে assert ছাড়া test | coverage-এর লক্ষ্য পূরণ | নতুন কোডের coverage দেখো, অর্থপূর্ণ assert |
| সব test একটা লম্বা job-এ ক্রমানুসারে | শুরুতে সহজ ছিল | parallel/sharding, দ্রুত test আগে (fail fast) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Flaky test কীভাবে সামলাবে?
**উত্তর:** শনাক্ত করো (retry ইতিহাস দেখে), quarantine করো যাতে pipeline আটকে না থাকে কিন্তু রিপোর্ট হয়, কারণ খুঁজে ঠিক করো (timing, shared state, ক্রম, বাইরের নির্ভরতা), আর মালিকানা দাও। স্থায়ী auto-retry দিয়ে লুকিয়ে রাখা নয়।

> 💡 **প্রো টিপ:** CI-কে টিমের বিশ্বাস রাখতে হবে। লাল মানে সত্যিই কিছু ভাঙা, সবুজ মানে নিশ্চিন্তে merge।

---

## ০৪. ধাপ ৪: Code Quality ও Security Scan (DevSecOps)

Test দেখে কোড ঠিকমতো কাজ করে কিনা; scan দেখে কোড নিরাপদ ও পরিচ্ছন্ন কিনা। দুটোই merge-এর আগে।

### কোন scan কী ধরে

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SAST** | **S**tatic **A**pplication **S**ecurity **T**esting → কোড না চালিয়ে নিরাপত্তা দুর্বলতা খোঁজা |
| **SCA** | **S**oftware **C**omposition **A**nalysis → dependency-তে পরিচিত দুর্বলতা খোঁজা |
| **DAST** | **D**ynamic **A**pplication **S**ecurity **T**esting → চলমান অ্যাপে আক্রমণের মতো পরীক্ষা |
| **CVE** | **C**ommon **V**ulnerabilities and **E**xposures → পরিচিত দুর্বলতার তালিকা |
| **IaC** | **I**nfrastructure **a**s **C**ode → Terraform, Kubernetes YAML ইত্যাদি |

**সহজ ভাষায়:** প্রতিটি scan আলাদা ধরনের সমস্যা ধরে। Lint ও format কোডের মান ঠিক রাখে, SAST কোডে SQL injection-এর মতো দুর্বলতা খোঁজে, SCA তৃতীয় পক্ষের লাইব্রেরির পরিচিত দুর্বলতা খোঁজে, secret scan ভুল করে commit করা password/key ধরে, container scan image-এর OS package-এর দুর্বলতা খোঁজে, IaC scan Terraform/K8s-এর ভুল config (খোলা S3, root container) ধরে।

> **বাস্তব পরিস্থিতি:** একটা PR-এ developer ভুল করে AWS access key সহ config ফাইল commit করেছিল। Secret scanning PR-এই আটকে দিল, merge হওয়ার আগেই key বাতিল করা হলো। আরেকবার Trivy ধরল base image-এ critical OpenSSL দুর্বলতা; base image আপডেট করে নতুন build।

| Scan | জনপ্রিয় টুল | কী ধরে | কখন |
|---|---|---|---|
| Lint / Format | ESLint, Ruff, golangci-lint, Prettier | কোড স্টাইল, সম্ভাব্য bug | প্রতিটি PR |
| SAST | CodeQL, Semgrep, SonarQube | injection, অনিরাপদ কোড | প্রতিটি PR |
| SCA / Dependency | Dependabot, Snyk, npm audit, pip-audit, OSV-Scanner | লাইব্রেরির CVE | PR আর রাতে |
| Secret scan | Gitleaks, GitHub secret scanning, TruffleHog | commit করা key/password | pre-commit আর PR |
| Container scan | Trivy, Grype, Docker Scout | image-এর OS package CVE | image build-এর পর |
| IaC scan | Checkov, tfsec/Trivy config, kube-linter | ভুল infra config | PR |
| DAST | OWASP ZAP | চলমান অ্যাপের দুর্বলতা | staging-এ |

```yaml
- name: Gitleaks
  uses: gitleaks/gitleaks-action@v2
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

- name: Trivy image scan
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: ghcr.io/acme/shop:${{ github.sha }}
    severity: CRITICAL,HIGH
    ignore-unfixed: true
    exit-code: "1"
```

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Secret commit করে পরের commit-এ মুছে দেওয়া | ভাবে মুছলেই শেষ | Git ইতিহাসে থেকে যায়; key সাথে সাথে বাতিল (rotate) করো, তারপর ইতিহাস পরিষ্কার |
| Scan-কে শুধু রিপোর্ট হিসেবে রাখা, কেউ দেখে না | pipeline আটকানোর ভয় | Critical-এ fail (quality gate), বাকিগুলোর ticket |
| হাজারো পুরনো সতর্কতা দেখে সব বন্ধ করে দেওয়া | alert fatigue | baseline নাও, শুধু নতুন কোডের সমস্যায় আটকাও, ধীরে পুরনো ঠিক করো |
| Dependency কখনো আপডেট না করা | "চলছে তো" | Dependabot/Renovate দিয়ে নিয়মিত ছোট আপডেট PR |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** SAST আর DAST-এর পার্থক্য?
**উত্তর:** SAST source code বিশ্লেষণ করে, চালানো ছাড়াই, শুরুর দিকে (PR-এ)। DAST চলমান অ্যাপের বাইরে থেকে আক্রমণের মতো পরীক্ষা করে, staging-এ।

**প্রশ্ন:** ভুল করে secret Git-এ push হলে প্রথম কাজ কী?
**উত্তর:** সাথে সাথে secret বাতিল/rotate করা, কারণ ধরে নিতে হবে এটা ফাঁস হয়েছে। তারপর ইতিহাস থেকে সরানো (`git filter-repo`), আর ভবিষ্যতের জন্য pre-commit ও CI secret scan।

> 💡 **প্রো টিপ:** Secret scan সবচেয়ে ভালো কাজ করে developer-এর মেশিনেই, pre-commit hook দিয়ে, push-এর আগেই।

---

## ০৫. ধাপ ৫: Deploy — Environment, Secret ও কৌশল

সবচেয়ে ঝুঁকিপূর্ণ ধাপ। এখানে ভুল মানে গ্রাহক সরাসরি সমস্যা দেখে। তাই কৌশল, পরীক্ষা আর ফেরার পথ সব আগে থেকে ঠিক রাখতে হয়।

### Environment: dev, staging, production

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Dev** | **Dev**elopment → developer-দের পরীক্ষার পরিবেশ |
| **Staging** | production-এর মতো শেষ পরীক্ষার পরিবেশ |
| **Prod** | **Prod**uction → আসল গ্রাহকের পরিবেশ |
| **Preview env** | প্রতিটি PR-এর জন্য অস্থায়ী পরিবেশ |

**সহজ ভাষায়:** Staging যত production-এর মতো, পরীক্ষা তত নির্ভরযোগ্য: একই image, একই ধরনের infra, একই config-এর গঠন, শুধু মান আলাদা (ছোট database, test payment gateway)। Config পরিবেশ অনুযায়ী environment variable বা config store থেকে আসে, কোডে `if env == 'prod'` লিখে নয়।

> **বাস্তব পরিস্থিতি:** Staging-এ সব পাস, production-এ crash। কারণ staging-এ Redis ছিল না, অ্যাপ cache ছাড়া চলছিল; production-এ Redis-এর connection নিয়মে একটা ভুল ছিল যা কখনো পরীক্ষাই হয়নি। এরপর staging-এ production-এর মতো সব সার্ভিস রাখা হলো।

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Staging আর production-এর infra খুব আলাদা | খরচ বাঁচাতে | একই নকশা, ছোট আকারে (IaC দিয়ে একই code) |
| কোডে পরিবেশ অনুযায়ী hardcode URL | দ্রুত কাজ চালানো | সব config environment variable/config file-এ (12-factor) |
| Production-এ হাতে config বদলানো | জরুরি অবস্থায় শর্টকাট | সব পরিবর্তন Git আর pipeline দিয়ে; হাতে বদলালে পরে Git-এ তোলো |
| Staging-এ আসল গ্রাহকের data কপি | বাস্তব data চাই | anonymize/mask করা data বা synthetic data |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** 12-factor app-এর config নীতি কী?
**উত্তর:** Config কোড থেকে আলাদা, environment-এ রাখো; একই build সব পরিবেশে চলে, শুধু environment variable বদলায়।

### Pipeline-এ Secret ব্যবস্থাপনা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Secret** | password, API key, token, certificate |
| **OIDC** | **O**pen**ID** **C**onnect → স্থায়ী key ছাড়া অস্থায়ী পরিচয় দিয়ে cloud-এ login |
| **Vault** | HashiCorp Vault → কেন্দ্রীয় secret ভাণ্ডার |

**সহজ ভাষায়:** Secret কখনো কোডে, Dockerfile-এ বা pipeline YAML-এ লেখা থাকবে না। CI টুলের secret store (GitHub Secrets, Jenkins Credentials)-এ রাখো, যাতে log-এ নিজে লুকানো (`***`) থাকে। আরও ভালো: OIDC দিয়ে cloud-এ login, যাতে কোনো স্থায়ী access key-ই না লাগে; pipeline প্রতিবার কয়েক মিনিটের অস্থায়ী credential পায়।

> **বাস্তব পরিস্থিতি:** এক বছর আগের একজন প্রাক্তন কর্মীর AWS access key সব pipeline-এ ব্যবহার হচ্ছিল, কেউ জানত না কোথায় কোথায়। Key ফাঁস হলে পুরো AWS ঝুঁকিতে। OIDC-তে গিয়ে সব স্থায়ী key মুছে ফেলা হলো; এখন GitHub Actions শুধু নির্দিষ্ট repo আর branch থেকে নির্দিষ্ট IAM role নিতে পারে।

```yaml
permissions:
  id-token: write        # OIDC token চাইতে
  contents: read

steps:
  - uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::123456789012:role/gha-deploy-prod
      aws-region: ap-southeast-1
  - run: aws sts get-caller-identity

  # সাধারণ secret ব্যবহার
  - run: ./deploy.sh
    env:
      SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `id-token: write` | GitHub থেকে OIDC token নেওয়ার অনুমতি |
| `role-to-assume` | AWS-এর এই IAM role নাও; role-এর trust policy-তে শুধু এই repo/branch অনুমোদিত |
| `${{ secrets.X }}` | GitHub-এ সংরক্ষিত secret; log-এ `***` দেখাবে |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| YAML বা কোডে password লেখা | দ্রুত চালানোর তাড়া | Secret store, OIDC, Vault |
| Debug-এর জন্য secret echo/print করা | সমস্যা খুঁজতে | কখনো না; শুধু দৈর্ঘ্য বা শেষের ৪ অক্ষর, বা অস্তিত্ব যাচাই |
| সব পরিবেশে একই secret | সহজ ব্যবস্থাপনা | প্রতি পরিবেশে আলাদা secret, production-এর গুলো environment protection-এর পেছনে |
| Fork থেকে আসা PR-এ secret দেওয়া | `pull_request_target`-এর ভুল ব্যবহার | অবিশ্বস্ত কোডের সাথে secret কখনো না |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** CI/CD-তে cloud credential-এর সবচেয়ে নিরাপদ উপায় কী?
**উত্তর:** OIDC federation: CI প্রতিবার স্বল্পমেয়াদি token নিয়ে নির্দিষ্ট IAM role assume করে; কোথাও স্থায়ী access key সংরক্ষণ করতে হয় না।

### Deployment কৌশল: Recreate, Rolling, Blue-Green, Canary

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Rolling** | rolling update → একটা একটা করে পুরনো বদলে নতুন |
| **Blue-Green** | দুটো সম্পূর্ণ পরিবেশ, traffic এক ঝটকায় বদল |
| **Canary** | canary release (খনির ক্যানারি পাখি) → অল্প ব্যবহারকারীকে আগে নতুন version |
| **Feature flag** | feature toggle → deploy আর release আলাদা করার সুইচ |

**সহজ ভাষায়:** **Recreate:** পুরনো সব বন্ধ, নতুন চালু; সহজ কিন্তু downtime। **Rolling:** ধাপে ধাপে বদল; downtime নেই, কিন্তু কিছুক্ষণ দুই version একসাথে। **Blue-Green:** পুরো নতুন পরিবেশ (green) তৈরি করে পরীক্ষা, তারপর load balancer এক মুহূর্তে সরিয়ে দেয়; rollback খুব দ্রুত, তবে দ্বিগুণ resource। **Canary:** প্রথমে ৫% traffic নতুন version-এ, metric ভালো থাকলে ২৫%, ৫০%, ১০০%; ঝুঁকি সবচেয়ে কম।

> **বাস্তব পরিস্থিতি:** একটা payment সার্ভিসের নতুন version-এ সূক্ষ্ম bug ছিল যা শুধু কিছু ব্যাংকের কার্ডে হতো। Canary-তে ৫% traffic-এ error rate ০.১% থেকে ২%-এ উঠল, Argo Rollouts নিজে থেকেই rollback করল। মাত্র কয়েকশ লেনদেন প্রভাবিত, সব গ্রাহক নয়।

| কৌশল | Downtime | Rollback গতি | খরচ | কখন |
|---|---|---|---|---|
| Recreate | আছে | ধীর | কম | dev, বা দুই version একসাথে চলা অসম্ভব হলে |
| Rolling | নেই | মাঝারি | কম | Kubernetes-এর default, বেশিরভাগ সার্ভিস |
| Blue-Green | নেই | তাৎক্ষণিক | দ্বিগুণ (অস্থায়ী) | গুরুত্বপূর্ণ সার্ভিস, সহজ rollback চাই |
| Canary | নেই | দ্রুত, স্বয়ংক্রিয় সম্ভব | সামান্য বেশি | বেশি traffic, ঝুঁকিপূর্ণ পরিবর্তন |
| Feature flag | নেই | তাৎক্ষণিক (সুইচ বন্ধ) | কোডে জটিলতা | deploy আর release আলাদা করতে |

```bash
# Kubernetes rolling update (Helm দিয়ে deploy)
helm upgrade --install shop ./chart \
  --set image.tag=a1b2c3d \
  --namespace prod --atomic --wait --timeout 10m

# সমস্যা হলে
helm rollback shop 0 -n prod          # 0 = আগের revision
kubectl rollout undo deployment/shop -n prod
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `helm upgrade --install` | `upgrade` → release থাকলে আপডেট · `install` → না থাকলে নতুন তৈরি |
| `--set image.tag=...` | set value → chart-এর একটা মান বদলাও |
| `--atomic` | ব্যর্থ হলে নিজে আগের অবস্থায় ফেরত |
| `--wait --timeout 10m` | `wait` → সব pod প্রস্তুত না হওয়া পর্যন্ত অপেক্ষা · `timeout` → সর্বোচ্চ ১০ মিনিট |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Blue-green আর canary deployment-এর পার্থক্য?
**উত্তর:** Blue-green-এ পুরো traffic একসাথে নতুন পরিবেশে যায়, rollback মানে আবার পুরোটা ফেরত। Canary-তে ধীরে ধীরে কিছু শতাংশ করে বাড়ানো হয় আর metric দেখে সিদ্ধান্ত, তাই সমস্যা হলে অল্প ব্যবহারকারী প্রভাবিত হয়।

**প্রশ্ন:** Deploy আর release-এর পার্থক্য?
**উত্তর:** Deploy মানে কোড সার্ভারে পৌঁছানো; release মানে ব্যবহারকারী feature দেখতে পাওয়া। Feature flag দিয়ে দুটো আলাদা করা যায়: কোড আগে deploy, পরে সুইচ চালু করে release।

### Database migration: সবচেয়ে বিপজ্জনক অংশ

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Migration** | database migration → database-এর গঠন (schema) বদলানোর script |
| **Expand-Contract** | expand and contract pattern → আগে যোগ, পরে সরানো |
| **Backward compatible** | পুরনো কোডও নতুন schema-তে চলে |

**সহজ ভাষায়:** কোড rollback করা সহজ, database rollback প্রায় অসম্ভব (data ইতিমধ্যে বদলে গেছে)। Rolling deploy-এর সময় কিছুক্ষণ পুরনো আর নতুন কোড একসাথে একই database-এ চলে। তাই migration এমন হতে হবে যাতে দুটো version-ই চলে। কৌশল: expand (নতুন column যোগ, পুরনো রাখো) → নতুন কোড deploy → data সরাও → পরের release-এ contract (পুরনো column মোছো)।

> **বাস্তব পরিস্থিতি:** Developer column-এর নাম `name` থেকে `full_name`-এ বদলাল একটা migration-এ, আর নতুন কোড deploy করল। Rolling update-এর সময় পুরনো pod গুলো `name` খুঁজে না পেয়ে ১০ মিনিট ধরে 500 দিল। আর rollback করলে পুরনো কোড আবার ভাঙবে। Expand-contract মানলে এটা হতো না।

```sql
-- Release 1 (expand): কোড দুটোতেই লেখে, পুরনো থেকে পড়ে
ALTER TABLE users ADD COLUMN full_name text;

-- Release 2 (migrate): পুরনো data কপি; কোড full_name থেকে পড়ে
UPDATE users SET full_name = name WHERE full_name IS NULL;

-- Release 3 (contract)
ALTER TABLE users DROP COLUMN name;
```

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| একই release-এ column rename/drop | এক ধাপে কাজ শেষ করতে চাওয়া | expand-contract, কয়েক release-এ ভাগ |
| বড় টেবিলে lock নেওয়া migration দিনের বেলা | local-এ ছোট data-তে দ্রুত চলেছিল | online migration টুল (gh-ost, pg concurrent index), production-এর মতো data-তে পরীক্ষা |
| প্রতিটি pod চালুর সময় migration চালানো | সহজ মনে হয় | আলাদা একবারের job (pre-deploy step / K8s Job) দিয়ে, lock সহ |
| Migration-এর আগে backup না নেওয়া | আত্মবিশ্বাস | স্বয়ংক্রিয় snapshot/backup ধাপ, restore অনুশীলন |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Zero-downtime database migration কীভাবে করবে?
**উত্তর:** Backward-compatible পরিবর্তন: expand-contract pattern, nullable column যোগ, পুরনো ও নতুন দুটোতে লেখা, backfill, তারপর পুরনো সরানো; lock এড়াতে online migration, আর migration আলাদা ধাপে চালানো।

### Deploy-এর পর যাচাই ও Rollback

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Smoke test** | দ্রুত মূল ফাংশন যাচাই |
| **SLO** | **S**ervice **L**evel **O**bjective → সেবার লক্ষ্য মান (যেমন ৯৯.৯% সফল) |
| **MTTR** | **M**ean **T**ime **T**o **R**ecovery → সমস্যা থেকে ফিরতে গড় সময় |

**সহজ ভাষায়:** Deploy "সফল" মানে শুধু কমান্ড শেষ হওয়া নয়, অ্যাপ সত্যিই ঠিকমতো কাজ করছে। তাই deploy-এর পর স্বয়ংক্রিয় smoke test (health endpoint, login, মূল API), তারপর কিছুক্ষণ metric দেখা (error rate, latency)। খারাপ হলে স্বয়ংক্রিয় rollback বা এক ক্লিক rollback। Rollback-এর পথ আগে থেকেই পরীক্ষিত থাকতে হবে।

> **বাস্তব পরিস্থিতি:** Deploy pipeline সবুজ, কিন্তু ২০ মিনিট পর গ্রাহক অভিযোগ: checkout হচ্ছে না। নতুন version-এর payment URL-এর environment variable ভুল ছিল, `/health` ঠিক থাকায় কেউ বোঝেনি। এরপর smoke test-এ একটা test checkout যোগ হলো, আর error rate বাড়লে স্বয়ংক্রিয় rollback।

```yaml
- name: Smoke test
  run: |
    for i in $(seq 1 10); do
      code=$(curl -s -o /dev/null -w "%{http_code}" https://shop.example.com/health)
      [ "$code" = "200" ] && exit 0
      sleep 6
    done
    echo "health check failed"; exit 1

- name: Rollback on failure
  if: failure()
  run: helm rollback shop 0 -n prod
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `seq 1 10 ... sleep 6` | সর্বোচ্চ ১ মিনিট ধরে ১০ বার চেষ্টা, নতুন pod চালু হওয়ার সময় দাও |
| `curl -s -o /dev/null -w "%{http_code}"` | শুধু HTTP status code নাও |
| `if: failure()` | আগের কোনো step fail করলে তবেই এই step চলবে |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| শুধু `/health` দেখে deploy সফল ধরা | সহজ check | মূল ব্যবসায়িক flow-এর smoke test আর metric দেখা |
| Rollback কখনো অনুশীলন না করা | "দরকার হবে না" | নিয়মিত rollback ড্রিল, স্বয়ংক্রিয় rollback |
| শুক্রবার বিকেলে বড় deploy | সপ্তাহের কাজ শেষ করার চাপ | ছোট ঘন deploy, ঝুঁকিপূর্ণ কাজে freeze নীতি, সাথে on-call |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Deploy-এর পর কী কী যাচাই করবে?
**উত্তর:** Health/readiness, smoke test (মূল flow), error rate, latency, resource ব্যবহার, log-এ নতুন error, আর ব্যবসায়িক metric (order সংখ্যা)। খারাপ হলে rollback।

---

## ০৬. GitHub Actions: কীভাবে কাজ করে

GitHub-এর ভেতরেই CI/CD। কোনো সার্ভার রাখতে হয় না, repo-তে একটা YAML ফাইল যোগ করলেই চলে।

### GitHub Actions-এর গঠন: Workflow, Job, Step, Runner

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Workflow** | `.github/workflows/`-এর একটা YAML ফাইল, একটা স্বয়ংক্রিয় প্রক্রিয়া |
| **Job** | একটা runner-এ চলা step-এর দল |
| **Step** | একটা কমান্ড (`run`) বা action (`uses`) |
| **Runner** | যে VM/মেশিন job চালায় |
| **Action** | পুনঃব্যবহারযোগ্য তৈরি step (marketplace থেকে) |

**সহজ ভাষায়:** Repo-তে `.github/workflows/ci.yml` রাখলে GitHub সেটা পড়ে। `on:` অংশে বলা হয় কোন ঘটনায় চলবে। `jobs:`-এ এক বা একাধিক job; প্রতিটি job একটা নতুন, পরিষ্কার runner VM-এ চলে (`ubuntu-latest`)। Job গুলো default-এ একসাথে (parallel) চলে, `needs:` দিয়ে ক্রম ঠিক হয়। প্রতিটি job-এর step গুলো ক্রমানুসারে চলে, একই মেশিনে, তাই ফাইল শেয়ার করে।

> **বাস্তব পরিস্থিতি:** রহিম PR খুললেন। GitHub দেখল `.github/workflows/ci.yml`-এ `on: pull_request` আছে। GitHub একটা নতুন Ubuntu VM চালু করল, কোড checkout করল, Node সেট করল, test চালাল, আর PR-এ সবুজ টিক দেখাল। VM টা কাজ শেষে মুছে গেল।

```text
Event (push/PR) ──> Workflow (ci.yml)
                      ├── Job: lint  ─┐  (একসাথে চলে)
                      ├── Job: test  ─┤
                      └── Job: build ─┴─ needs: [lint, test]
                                │
                                └── Steps: checkout -> setup -> run -> upload
```

```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
      - run: npm ci
      - run: npm run lint
      - run: npm test -- --coverage
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `name: CI` | workflow-এর নাম, Actions ট্যাবে দেখায় |
| `on: pull_request` / `push: branches: [main]` | যেকোনো PR-এ আর main-এ push হলে চলবে |
| `jobs: test:` | `test` নামের একটা job |
| `runs-on: ubuntu-latest` | GitHub-এর দেওয়া Ubuntu VM-এ চলবে |
| `uses: actions/checkout@v4` | তৈরি action: repo-এর কোড runner-এ নামাও; `@v4` = version |
| `with:` | action-কে input দাও |
| `run: npm ci` | shell কমান্ড চালাও |
| `npm test -- --coverage` | `--`-এর পরের অংশ npm না, test টুল পায় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** GitHub Actions-এ workflow, job আর step-এর পার্থক্য?
**উত্তর:** Workflow পুরো YAML প্রক্রিয়া; job একটা আলাদা runner-এ চলা কাজের দল (job গুলো default-এ parallel); step job-এর ভেতরে ক্রমানুসারে চলা একেকটা কমান্ড বা action।

**প্রশ্ন:** দুটো job-এর মধ্যে ফাইল কীভাবে শেয়ার করবে?
**উত্তর:** প্রতিটি job আলাদা VM-এ চলে, তাই সরাসরি না। `actions/upload-artifact` আর `download-artifact` দিয়ে, বা registry-তে push করে।

### Matrix, needs, cache, artifact, concurrency

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Matrix** | matrix strategy → একই job কয়েকটা সংমিশ্রণে চালানো |
| **needs** | কোন job-এর পরে চলবে |
| **concurrency** | concurrency group → একই ধরনের রান একসাথে না চলা |

**সহজ ভাষায়:** Matrix দিয়ে একই test কয়েকটা Node/Python version বা OS-এ একসাথে চালানো যায়। `needs` দিয়ে ক্রম আর job-এর output পাঠানো যায়। Artifact দিয়ে এক job-এর ফাইল আরেক job-এ বা ডাউনলোডের জন্য। Concurrency দিয়ে একই branch-এ নতুন push এলে পুরনো রান বাতিল, আর production deploy একসাথে দুটো যেন না চলে।

> **বাস্তব পরিস্থিতি:** একটা open-source লাইব্রেরি Node 18, 20, 22 আর Ubuntu ও Windows দুটোতেই চলতে হবে। Matrix-এ ৬টা job একসাথে চলে। Developer পরপর ৫টা commit push করলে আগের ৪টা রান নিজে বাতিল হয়, runner-এর সময় বাঁচে।

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  test:
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [18, 20, 22]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: "${{ matrix.node }}", cache: npm }
      - run: npm ci && npm test

  build:
    needs: test
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.ver.outputs.version }}
    steps:
      - uses: actions/checkout@v4
      - id: ver
        run: echo "version=$(git rev-parse --short HEAD)" >> "$GITHUB_OUTPUT"
      - run: npm ci && npm run build
      - uses: actions/upload-artifact@v4
        with: { name: dist, path: dist/, retention-days: 7 }

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with: { name: dist, path: dist/ }
      - run: echo "deploying ${{ needs.build.outputs.version }}"
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `concurrency.group` | একই workflow + branch-এর রানগুলো একটা দল |
| `cancel-in-progress: true` | নতুন রান এলে পুরনোটা বাতিল (production deploy-এ `false` রাখো) |
| `matrix: os x node` | ২ x ৩ = ৬টা job |
| `fail-fast: false` | একটা fail করলেও বাকিগুলো চলবে, সবার ফল দেখা যাবে |
| `${{ matrix.os }}` | প্রতিটি job-এর নিজস্ব মান |
| `needs: test` | test job সফল হলেই build চলবে |
| `>> "$GITHUB_OUTPUT"` | step-এর output লেখার নিয়ম, পরের job-এ পাঠাতে |
| `upload-artifact` / `download-artifact` | job-এর মধ্যে বা ডাউনলোডের জন্য ফাইল |
| `if: github.ref == 'refs/heads/main'` | শুধু main branch-এ deploy |

### Environment protection, OIDC সহ সম্পূর্ণ CD workflow

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Environment** | GitHub Environment → staging/production-এর জন্য নিয়ম ও secret-এর দল |
| **Required reviewers** | deploy-এর আগে অনুমোদনকারী |
| **GHCR** | **G**it**H**ub **C**ontainer **R**egistry → GitHub-এর image registry |

**সহজ ভাষায়:** GitHub-এ Settings > Environments-এ `production` তৈরি করে required reviewer, wait timer আর কোন branch deploy করতে পারবে ঠিক করা যায়। Job-এ `environment: production` দিলে ওই job চালুর আগে অনুমোদন চাইবে, আর শুধু তখনই production-এর secret পাবে। নিচের workflow পুরো যাত্রা দেখায়: image build, push, staging deploy, smoke test, অনুমোদন, production deploy।

> **বাস্তব পরিস্থিতি:** main-এ merge হলো। ১০ মিনিটে staging-এ নতুন version, smoke test পাস। Release manager Slack-এ নোটিফিকেশন পেলেন, GitHub-এ "Approve" চাপলেন, production-এ rolling deploy শুরু। কে কখন অনুমোদন দিলেন সবই ইতিহাসে থাকে।

```yaml
name: CD
on:
  push:
    branches: [main]

permissions:
  contents: read
  packages: write
  id-token: write

env:
  IMAGE: ghcr.io/${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      tag: ${{ steps.meta.outputs.tag }}
    steps:
      - uses: actions/checkout@v4
      - id: meta
        run: echo "tag=$(git rev-parse --short HEAD)" >> "$GITHUB_OUTPUT"
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v6
        with:
          push: true
          tags: ${{ env.IMAGE }}:${{ steps.meta.outputs.tag }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - uses: actions/checkout@v4
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_DEPLOY_ROLE }}
          aws-region: ap-southeast-1
      - run: aws eks update-kubeconfig --name shop-staging
      - run: helm upgrade --install shop ./chart -n staging --set image.tag=${{ needs.build.outputs.tag }} --atomic --wait
      - run: ./scripts/smoke.sh https://staging.shop.example.com

  deploy-production:
    needs: [build, deploy-staging]
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://shop.example.com
    concurrency: production-deploy
    steps:
      - uses: actions/checkout@v4
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_DEPLOY_ROLE }}
          aws-region: ap-southeast-1
      - run: aws eks update-kubeconfig --name shop-prod
      - run: helm upgrade --install shop ./chart -n prod --set image.tag=${{ needs.build.outputs.tag }} --atomic --wait
      - run: ./scripts/smoke.sh https://shop.example.com
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `permissions:` | `GITHUB_TOKEN`-কে ন্যূনতম অনুমতি; `packages: write` = GHCR-এ push, `id-token: write` = OIDC |
| `${{ github.repository }}` | owner/repo, যেমন `acme/shop` |
| `push: true` + `tags` | build শেষে commit SHA tag দিয়ে registry-তে push |
| `environment: staging` | staging environment-এর নিয়ম আর secret/variable প্রযোজ্য |
| `${{ vars.AWS_DEPLOY_ROLE }}` | environment variable (secret নয়), প্রতি environment-এ আলাদা role |
| `aws eks update-kubeconfig` | EKS cluster-এর kubeconfig বানাও |
| `needs: [build, deploy-staging]` | staging সফল না হলে production শুরুই হবে না |
| `environment: name: production` | required reviewer-এর অনুমোদনের পর তবেই চলবে |
| `concurrency: production-deploy` | একসাথে দুটো production deploy কখনো না |
| একই tag দুই জায়গায় | build once, deploy many: staging-এ পরীক্ষিত image-ই production-এ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** GitHub Actions-এ production deploy-এ মানুষের অনুমোদন কীভাবে বসাবে?
**উত্তর:** `production` নামে Environment বানিয়ে required reviewers সেট করো, আর deploy job-এ `environment: production` দাও। Job চালুর আগে অনুমোদন চাইবে; environment-এর secret শুধু তখনই পাওয়া যাবে।

**প্রশ্ন:** `GITHUB_TOKEN` কী?
**উত্তর:** প্রতিটি workflow রানের জন্য GitHub-এর তৈরি স্বল্পমেয়াদি token, শুধু ওই repo-তে; `permissions:` দিয়ে তার ক্ষমতা সীমিত করা হয়।

### Reusable workflow, composite action ও self-hosted runner

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Reusable workflow** | `workflow_call` → অন্য workflow থেকে ডাকা যায় এমন workflow |
| **Composite action** | কয়েকটা step একসাথে একটা action হিসেবে |
| **Self-hosted runner** | নিজের মেশিনে চলা runner |

**সহজ ভাষায়:** ৫০টা repo-তে একই CI কপি-পেস্ট করলে একটা পরিবর্তন ৫০ জায়গায় করতে হয়। কেন্দ্রীয় repo-তে reusable workflow রেখে সবাই `uses:` দিয়ে ডাকে। Self-hosted runner লাগে যখন private network-এ পৌঁছাতে হবে (ভেতরের database), বিশেষ hardware (GPU) বা খরচ কমাতে; তবে public repo-তে self-hosted runner বিপজ্জনক, কারণ যে কেউ PR দিয়ে কোড চালাতে পারে।

> **বাস্তব পরিস্থিতি:** Platform টিম একটা "docker-build-scan-push" reusable workflow বানাল। এখন প্রতিটি সার্ভিসের repo-তে ১০ লাইনে পুরো build-scan-push, আর Trivy-এর নিয়ম বদলালে একবার বদলালেই সবখানে প্রযোজ্য।

```yaml
# acme/ci-templates/.github/workflows/docker.yml
on:
  workflow_call:
    inputs:
      image:
        type: string
        required: true
    secrets:
      REGISTRY_TOKEN:
        required: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "building ${{ inputs.image }}"
```

```yaml
# প্রতিটি সার্ভিসের repo-তে
jobs:
  docker:
    uses: acme/ci-templates/.github/workflows/docker.yml@v1
    with:
      image: ghcr.io/acme/orders
    secrets:
      REGISTRY_TOKEN: ${{ secrets.REGISTRY_TOKEN }}

# self-hosted runner-এ চালানো
# runs-on: [self-hosted, linux, private-net]
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `on: workflow_call` | এই workflow অন্য workflow থেকে ডাকা যাবে |
| `inputs` / `secrets` | ডাকার সময় কী কী দিতে হবে |
| `uses: org/repo/.github/workflows/x.yml@v1` | নির্দিষ্ট version-এর reusable workflow ডাকো |
| `runs-on: [self-hosted, linux, private-net]` | এই label যুক্ত নিজের runner-এ চালাও |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Third-party action `@main` বা `@master` দিয়ে ব্যবহার | সবসময় সর্বশেষ চাই | নির্দিষ্ট version বা commit SHA-তে pin করো (supply chain আক্রমণ ঠেকাতে) |
| `GITHUB_TOKEN`-এ সব অনুমতি রেখে দেওয়া | default বদলানো হয়নি | `permissions:` দিয়ে ন্যূনতম অনুমতি |
| public repo-তে self-hosted runner | খরচ বাঁচানো | public repo-তে GitHub-hosted, বা ephemeral isolated runner |
| একই YAML ৫০ repo-তে কপি | সহজ শুরু | reusable workflow / composite action |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Reusable workflow আর composite action-এর পার্থক্য?
**উত্তর:** Reusable workflow পুরো job (নিজের runner সহ) পুনঃব্যবহার করে, jobs স্তরে `uses:` দিয়ে ডাকা হয়। Composite action কয়েকটা step একসাথে, একটা step হিসেবে অন্য job-এর ভেতরে ব্যবহার হয়।

**প্রশ্ন:** Third-party action কেন commit SHA দিয়ে pin করা উচিত?
**উত্তর:** Tag বা branch পরে বদলে যেতে পারে (মালিক বা হ্যাকার), তখন তোমার pipeline-এ ক্ষতিকর কোড চলবে। SHA অপরিবর্তনীয়।

---

## ০৭. Jenkins: কীভাবে কাজ করে

সবচেয়ে পুরনো আর এখনো বহুল ব্যবহৃত open-source CI/CD সার্ভার। অনেক ব্যাংক, টেলিকম আর বড় কোম্পানিতে Jenkins চলে, তাই interview-এ প্রায় নিশ্চিত আসে।

### Jenkins-এর architecture: Controller ও Agent

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Jenkins** | (Butler-এর নাম) → open-source automation সার্ভার |
| **Controller** | (আগে Master) → UI, scheduling, config রাখে |
| **Agent** | (আগে Slave/Node) → যে মেশিন আসলে build চালায় |
| **Executor** | agent-এ একসাথে কতগুলো build চলতে পারে তার slot |
| **Plugin** | Jenkins-এ নতুন ক্ষমতা যোগের অংশ |

**সহজ ভাষায়:** Jenkins হলো তোমার নিজের সার্ভারে চলা একটা Java অ্যাপ্লিকেশন। Controller web UI দেয়, job-এর config আর ইতিহাস রাখে, আর কোন build কোন agent-এ যাবে ঠিক করে। আসল build agent-এ চালানো উচিত (Linux VM, Docker container, Kubernetes pod), controller-এ নয়। প্রায় সব ক্ষমতা (Git, Docker, Slack, Kubernetes) plugin দিয়ে আসে। GitHub Actions-এর বিপরীতে Jenkins-এর সার্ভার, আপডেট, backup, নিরাপত্তা সব নিজেকে সামলাতে হয়।

> **বাস্তব পরিস্থিতি:** একটা ব্যাংকের কোড internet-এ যেতে পারে না, সব কিছু নিজের data center-এ। তাই GitHub Actions-এর বদলে ভেতরের নেটওয়ার্কে Jenkins: controller একটা VM-এ, আর Kubernetes plugin দিয়ে প্রতিটি build-এর জন্য নতুন pod agent হিসেবে তৈরি হয়, কাজ শেষে মুছে যায়।

```text
Developer push ──> GitHub/Bitbucket ──(webhook)──> Jenkins Controller
                                                   │ (Jenkinsfile পড়ে, scheduling)
                        ┌──────────────────────────┼──────────────────────┐
                 Agent 1 (Linux)           Agent 2 (Docker)        Agent 3 (K8s pod)
                  build, test                image build               deploy
```

```bash
# Docker দিয়ে Jenkins চালানো (শেখার জন্য)
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts-jdk17
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
# browser: http://localhost:8080 -> suggested plugins ইনস্টল
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `-p 8080:8080` | web UI → Jenkins-এর ওয়েব পেজ |
| `-p 50000:50000` | agent port → inbound agent-এর সংযোগ (JNLP) |
| `-v jenkins_home:/var/jenkins_home` | `v` = volume → সব config, job, plugin, ইতিহাস এখানে; volume ছাড়া container মুছলে সব হারাবে |
| `lts-jdk17` | `lts` = Long Term Support → স্থিতিশীল সংস্করণ, Java 17 সহ |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Controller-এ build চালানো | শুরুতে সহজ, আলাদা agent-এর ঝামেলা নেই | Controller-এ executor 0, সব build agent-এ |
| শত শত plugin, কখনো আপডেট না | প্রতিটি সমস্যায় নতুন plugin | ন্যূনতম plugin, নিয়মিত আপডেট, staging Jenkins-এ আগে পরীক্ষা |
| UI-তে হাতে job বানানো (ClickOps) | দ্রুত মনে হয় | সব কিছু Jenkinsfile (Pipeline as Code) আর JCasC দিয়ে Git-এ |
| `jenkins_home`-এর backup নেই | কখনো ভাবা হয়নি | নিয়মিত backup বা JCasC দিয়ে পুনর্গঠনযোগ্য |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Jenkins-এ controller আর agent-এর ভূমিকা কী?
**উত্তর:** Controller UI, config, scheduling আর ফল সংরক্ষণ করে; agent আসলে build/test/deploy চালায়। নিরাপত্তা ও পারফরম্যান্সের জন্য build controller-এ না চালিয়ে agent-এ চালানো হয়।

**প্রশ্ন:** Jenkins-এর বড় অসুবিধা কী?
**উত্তর:** নিজে সার্ভার পরিচালনা (আপডেট, backup, scale), plugin-এর নির্ভরতা ও নিরাপত্তা ঝুঁকি, Groovy শেখা, আর UI কিছুটা পুরনো।

### Jenkinsfile (Declarative Pipeline) লাইন ধরে

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Jenkinsfile** | repo-তে রাখা pipeline-এর কোড |
| **Declarative** | declarative pipeline → নির্দিষ্ট কাঠামোর সহজ syntax |
| **Groovy** | Jenkins pipeline-এর ভাষা (Java ভিত্তিক) |
| **stage** | pipeline-এর একটা বড় ধাপ |
| **post** | শেষে ফলাফল অনুযায়ী কাজ |

**সহজ ভাষায়:** Jenkinsfile repo-এর root-এ থাকে, তাই pipeline-ও কোডের মতো version control-এ থাকে আর review হয়। Declarative syntax-এ `pipeline { }`-এর ভেতরে `agent` (কোথায় চলবে), `environment` (variable), `options` (timeout ইত্যাদি), `stages` (ধাপগুলো), আর `post` (শেষে কী হবে)।

> **বাস্তব পরিস্থিতি:** নিচের Jenkinsfile পুরো যাত্রা দেখায়: test (Node container-এ), lint আর security scan একসাথে, Docker image build ও push, staging deploy, অনুমোদন, production deploy, আর শেষে Slack-এ জানানো।

```groovy
pipeline {
  agent none
  environment {
    REGISTRY = '123456789012.dkr.ecr.ap-southeast-1.amazonaws.com'
    IMAGE    = "${REGISTRY}/shop"
  }
  options {
    timeout(time: 30, unit: 'MINUTES')
    buildDiscarder(logRotator(numToKeepStr: '30'))
    disableConcurrentBuilds()
  }
  stages {
    stage('Test') {
      agent { docker { image 'node:20-alpine' } }
      steps {
        sh 'npm ci'
        sh 'npm test -- --ci --reporters=default --reporters=jest-junit'
      }
      post { always { junit 'junit.xml' } }
    }
    stage('Quality') {
      parallel {
        stage('Lint') {
          agent { docker { image 'node:20-alpine' } }
          steps { sh 'npm ci && npm run lint' }
        }
        stage('Secret scan') {
          agent { docker { image 'zricethezav/gitleaks:latest' } }
          steps { sh 'gitleaks detect --source . --no-banner' }
        }
      }
    }
    stage('Build & Push image') {
      when { branch 'main' }
      agent { label 'docker' }
      steps {
        script {
          env.TAG = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
        }
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-ci']]) {
          sh '''
            aws ecr get-login-password --region ap-southeast-1 | docker login -u AWS --password-stdin $REGISTRY
            docker build -t $IMAGE:$TAG .
            docker push $IMAGE:$TAG
          '''
        }
      }
    }
    stage('Deploy staging') {
      when { branch 'main' }
      agent { label 'deploy' }
      steps { sh './scripts/deploy.sh staging $TAG && ./scripts/smoke.sh staging' }
    }
    stage('Approve production') {
      when { branch 'main' }
      steps { input message: 'Deploy to production?', submitter: 'release-managers' }
    }
    stage('Deploy production') {
      when { branch 'main' }
      agent { label 'deploy' }
      steps { sh './scripts/deploy.sh prod $TAG && ./scripts/smoke.sh prod' }
    }
  }
  post {
    success { slackSend color: 'good', message: "OK ${env.JOB_NAME} #${env.BUILD_NUMBER}" }
    failure { slackSend color: 'danger', message: "FAILED ${env.JOB_NAME} #${env.BUILD_NUMBER} ${env.BUILD_URL}" }
  }
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `agent none` | পুরো pipeline-এর default agent নেই, প্রতিটি stage নিজে ঠিক করবে |
| `environment { }` | সব stage-এ পাওয়া যাবে এমন variable |
| `timeout(time: 30, ...)` | ৩০ মিনিটের বেশি চললে build বাতিল, আটকে থাকা agent মুক্ত হবে |
| `buildDiscarder(logRotator(numToKeepStr: '30'))` | শুধু শেষ ৩০টা build-এর ইতিহাস রাখো, disk বাঁচে |
| `disableConcurrentBuilds()` | একই job-এর দুটো build একসাথে চলবে না |
| `agent { docker { image 'node:20-alpine' } }` | এই stage একটা Node container-এর ভেতরে চলবে, agent-এ Node ইনস্টল লাগবে না |
| `sh '...'` | shell কমান্ড চালাও (Windows agent-এ `bat`) |
| `post { always { junit ... } }` | পাস/ফেল যাই হোক test রিপোর্ট Jenkins-এ দেখাও |
| `parallel { }` | ভেতরের stage গুলো একসাথে চলবে |
| `when { branch 'main' }` | শুধু main branch-এ এই stage চলবে (multibranch pipeline-এ) |
| `agent { label 'docker' }` | `docker` label যুক্ত agent-এ চালাও |
| `script { env.TAG = sh(..., returnStdout: true).trim() }` | কমান্ডের output variable-এ রাখো; `script` ব্লকে Groovy কোড |
| `withCredentials([...])` | Jenkins credential store থেকে secret শুধু এই ব্লকে দাও, log-এ লুকানো |
| `sh ''' ... '''` | কয়েক লাইনের shell script; single quote-এ Groovy নিজে `$` বসায় না, shell বসায় |
| `input message:, submitter:` | pipeline থেমে মানুষের অনুমোদনের অপেক্ষা করবে, শুধু এই দল অনুমোদন দিতে পারবে |
| `post { success / failure }` | শেষ ফল অনুযায়ী Slack-এ বার্তা |
| `env.BUILD_NUMBER` / `BUILD_URL` | Jenkins-এর নিজের variable: build নম্বর আর লিংক |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Declarative আর Scripted pipeline-এর পার্থক্য?
**উত্তর:** Declarative-এ নির্দিষ্ট কাঠামো (`pipeline`, `agent`, `stages`, `post`), পড়া ও শেখা সহজ, ভুল কম। Scripted পুরোপুরি Groovy (`node { }`), বেশি নমনীয় কিন্তু জটিল। নতুন কাজে Declarative, দরকারে `script { }` ব্লক।

**প্রশ্ন:** Jenkins-এ secret কীভাবে ব্যবহার করো?
**উত্তর:** Credentials store-এ রাখো আর pipeline-এ `withCredentials` বা `environment`-এ `credentials()` দিয়ে নাও; Jenkins log-এ মান mask করে। Groovy string interpolation (double quote)-এ secret বসিও না, single quote-এ shell variable হিসেবে ব্যবহার করো।

> 💡 **প্রো টিপ:** `sh "...$SECRET..."` (double quote) লিখলে Groovy নিজেই secret বসিয়ে দেয়, যা process list-এ দেখা যেতে পারে; Jenkins এ নিয়ে সতর্কও করে। `sh '...$SECRET...'` (single quote) নিরাপদ।

### Multibranch, Webhook, Shared Library ও Agent কৌশল

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Multibranch Pipeline** | প্রতিটি branch ও PR-এর জন্য নিজে job তৈরি |
| **Shared Library** | অনেক Jenkinsfile-এ ব্যবহারযোগ্য Groovy কোড |
| **JCasC** | **J**enkins **C**onfiguration **a**s **C**ode → Jenkins-এর config YAML-এ |
| **Ephemeral agent** | প্রতিটি build-এর জন্য নতুন, শেষে মুছে যায় |

**সহজ ভাষায়:** Multibranch Pipeline repo স্ক্যান করে প্রতিটি branch আর PR-এ Jenkinsfile পেলে নিজে job বানায়। GitHub webhook (`http://jenkins/github-webhook/`) দিলে push-এর সাথে সাথে build শুরু হয়। Shared Library দিয়ে সাধারণ কাজ (`dockerBuildPush`, `deployHelm`) একবার লিখে সব repo-তে ব্যবহার। Kubernetes plugin দিয়ে প্রতিটি build একটা নতুন pod-এ চললে পরিষ্কার পরিবেশ, আর বাড়তি agent লাগলে নিজে বাড়ে।

> **বাস্তব পরিস্থিতি:** ৪০টা microservice-এর Jenkinsfile প্রায় একই, ৪০০ লাইন করে। Shared library-তে নেওয়ার পর প্রতিটি Jenkinsfile মাত্র ৫ লাইন, আর Trivy যোগ করতে শুধু library-তে একবার বদল।

```groovy
// vars/standardPipeline.groovy (shared library repo)
def call(Map cfg) {
  pipeline {
    agent { kubernetes { yamlFile 'ci/pod.yaml' } }
    stages {
      stage('Test')  { steps { sh cfg.testCmd } }
      stage('Image') {
        when { branch 'main' }
        steps { dockerBuildPush(image: cfg.image) }
      }
    }
  }
}
```

```groovy
// প্রতিটি সার্ভিসের Jenkinsfile
@Library('acme-ci@v3') _
standardPipeline(image: 'shop/orders', testCmd: 'npm ci && npm test')
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `vars/standardPipeline.groovy` | shared library-তে `vars/`-এর ফাইলের নামই function-এর নাম |
| `def call(Map cfg)` | Jenkinsfile থেকে `standardPipeline(...)` ডাকলে এটা চলে |
| `kubernetes { yamlFile ... }` | এই pod spec দিয়ে প্রতিটি build-এর জন্য নতুন agent pod |
| `@Library('acme-ci@v3') _` | shared library-এর v3 version লোড করো |
| `dockerBuildPush(...)` | library-তে লেখা আরেকটা পুনঃব্যবহারযোগ্য step |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| সব agent স্থায়ী আর পুরনো build-এর জঞ্জাল নিয়ে | একবার সেটআপ করে ভুলে যাওয়া | ephemeral agent (Docker/K8s pod), প্রতিবার পরিষ্কার পরিবেশ |
| Polling দিয়ে প্রতি মিনিটে Git চেক | webhook সেটআপ জানা নেই | GitHub webhook বা GitHub App |
| প্রতিটি repo-তে লম্বা কপি করা Jenkinsfile | শুরুতে সহজ | shared library, version সহ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Jenkins-এ build কীভাবে scale করবে?
**উত্তর:** Controller-এ build না চালিয়ে বেশি agent যোগ, বিশেষ করে Kubernetes/cloud plugin দিয়ে চাহিদা অনুযায়ী ephemeral agent; ভারী কাজ parallel stage-এ ভাগ।

---

## ০৮. GitHub Actions বনাম Jenkins বনাম অন্যরা

কোনটা "সেরা" না, পরিস্থিতি অনুযায়ী ঠিকটা। Interview-এ তুলনা আর বেছে নেওয়ার যুক্তি জিজ্ঞেস করে।

### তুলনা ও কখন কোনটা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SaaS** | **S**oftware **a**s **a** **S**ervice → সেবাদাতা চালায়, তুমি ব্যবহার করো |
| **GitLab CI** | GitLab Continuous Integration → GitLab-এর built-in CI |
| **Argo CD** | Argo Continuous Delivery → Kubernetes-এর GitOps টুল |

**সহজ ভাষায়:** **GitHub Actions:** কোড GitHub-এ থাকলে সবচেয়ে সহজ, কোনো সার্ভার নেই, বিশাল marketplace। **Jenkins:** পুরো নিয়ন্ত্রণ, on-premise, যেকোনো জটিল প্রয়োজন, কিন্তু রক্ষণাবেক্ষণ নিজের। **GitLab CI:** GitLab ব্যবহারকারীদের জন্য Actions-এর মতো সমন্বিত। **Argo CD:** build নয়, শুধু Kubernetes-এ deploy-এর GitOps টুল, প্রায়ই Actions/Jenkins-এর সাথে মিলিয়ে।

> **বাস্তব পরিস্থিতি:** স্টার্টআপ (কোড GitHub-এ, ক্লাউডে): GitHub Actions + Argo CD। ব্যাংক (internet বিচ্ছিন্ন, পুরনো সিস্টেম, কঠোর নিয়ম): Jenkins। যে কোম্পানি self-hosted GitLab ব্যবহার করে: GitLab CI।

| বিষয় | GitHub Actions | Jenkins | GitLab CI |
|---|---|---|---|
| সার্ভার | লাগে না (hosted runner) | নিজে চালাতে হয় | SaaS বা self-hosted |
| Config | YAML (`.github/workflows`) | Groovy (`Jenkinsfile`) | YAML (`.gitlab-ci.yml`) |
| শেখা | সহজ | মাঝারি থেকে কঠিন | সহজ |
| Ecosystem | Marketplace actions | ১৮০০+ plugin | built-in বৈশিষ্ট্য অনেক |
| On-premise / air-gapped | self-hosted runner + GHES | সেরা | ভালো (self-managed) |
| রক্ষণাবেক্ষণ | কম | বেশি (আপডেট, plugin, backup) | কম থেকে মাঝারি |
| খরচ | private repo-তে মিনিট অনুযায়ী | সফটওয়্যার ফ্রি, সার্ভার ও মানুষের খরচ | plan অনুযায়ী |
| সেরা কখন | কোড GitHub-এ, ক্লাউড, দ্রুত শুরু | জটিল/পুরনো পরিবেশ, পুরো নিয়ন্ত্রণ | GitLab ব্যবহারকারী |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Jenkins থেকে GitHub Actions-এ যাবে কিনা কীভাবে সিদ্ধান্ত নেবে?
**উত্তর:** কোড কোথায় থাকে, নিরাপত্তা/নেটওয়ার্ক বাধ্যবাধকতা (on-prem লাগবে কিনা), Jenkins রক্ষণাবেক্ষণের খরচ, বিশেষ plugin-এর উপর নির্ভরতা, টিমের দক্ষতা, আর runner খরচ। প্রায়ই ধাপে ধাপে, নতুন প্রজেক্ট আগে।

---

## ০৯. সব একসাথে: এক developer-এর কোডের পুরো যাত্রা

রহিমের "discount code" feature-এর গল্প দিয়ে আগের সব অধ্যায় মিলিয়ে দেখা, সাথে GitOps।

### End-to-end গল্প: সকাল ১০টা থেকে বিকেল ৩টা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **DORA** | **D**ev**O**ps **R**esearch and **A**ssessment → DevOps-এর কার্যকারিতা মাপার গবেষণা দল |
| **Lead time** | lead time for changes → commit থেকে production-এ পৌঁছানোর সময় |

**সহজ ভাষায়:** নিচের সময়রেখায় দেখো প্রতিটি ধাপে কী স্বয়ংক্রিয়ভাবে হলো, কোথায় মানুষের ভূমিকা থাকল, আর কোথায় সমস্যা ধরা পড়ল। মানুষ শুধু লিখল, review করল আর অনুমোদন দিল; বাকি সব pipeline করল।

> **বাস্তব পরিস্থিতি:** রহিম একটা feature-এ কাজ করছেন: cart-এ discount code দিলে ছাড় বসবে। পুরো দিনের ঘটনা নিচে।

| সময় | কী ঘটল |
|---|---|
| ১০:০০ | রহিম branch বানালেন: `git switch -c feat/discount-code` |
| ১০:০০-১১:৩০ | কোড + unit test লিখলেন, local-এ `npm test` |
| ১১:৩২ | `git push` → PR খুললেন |
| ১১:৩২ | CI শুরু (GitHub Actions): lint, unit, integration (Postgres service), build |
| ১১:৩৫ | Gitleaks লাল! `.env.example`-এ আসল Stripe key ছিল → রহিম key বদলে ফেললেন (Stripe-এ rotate), ফাইল ঠিক করে আবার push |
| ১১:৪৬ | সব check সবুজ, coverage রিপোর্ট PR-এ মন্তব্য হিসেবে |
| ১২:১৫ | করিম review করলেন, দুটো পরামর্শ; রহিম ঠিক করলেন |
| ১৩:০০ | Approve + merge (squash) main-এ |
| ১৩:০১ | CD শুরু: test আবার, Docker image build, Trivy scan পাস |
| ১৩:০৮ | `ghcr.io/acme/shop:a1b2c3d` push |
| ১৩:০৯ | Staging-এ `helm upgrade --atomic`, smoke test, E2E (Playwright) পাস |
| ১৩:২০ | Slack: "production অনুমোদন প্রয়োজন" |
| ১৪:০০ | Release manager approve করলেন |
| ১৪:০১ | Production canary: ১০% traffic নতুন version-এ |
| ১৪:১১ | Error rate আর latency স্বাভাবিক → ৫০% → ১০০% |
| ১৪:২৫ | Production smoke test পাস, Slack-এ সবুজ বার্তা |
| ১৪:৩০ | Feature flag `discount_code` মার্কেটিং টিম চালু করল (release) |
| ১৫:০০ | Grafana-তে নতুন discount ব্যবহারের graph |

**Lead time:** ১১:৩২ (push) → ১৪:২৫ (production) = প্রায় ৩ ঘণ্টা

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** একটা কোড পরিবর্তন production-এ যাওয়ার পুরো প্রক্রিয়া বর্ণনা করো।
**উত্তর:** এই সময়রেখার মতো ধাপে ধাপে বলো: branch, push, PR CI (lint, test, scan), review, merge, image build ও scan, registry, staging deploy ও test, অনুমোদন, production কৌশল (canary), যাচাই, monitoring, rollback প্রস্তুতি।

### GitOps ও Argo CD

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **GitOps** | Git + Operations → Git-কে infra ও deploy-এর একমাত্র সত্যের উৎস বানানো |
| **Argo CD** | Argo Continuous Delivery → Git-কে Kubernetes-এর সাথে মিলিয়ে রাখার টুল |
| **Drift** | configuration drift → আসল অবস্থা Git থেকে সরে যাওয়া |
| **Push vs Pull** | push / pull deployment → CI ঠেলে দেয় / cluster নিজে টেনে নেয় |

**সহজ ভাষায়:** সাধারণ CD-তে CI pipeline cluster-এ kubectl/helm চালিয়ে deploy ঠেলে দেয় (push), ফলে CI-এর হাতে production-এর চাবি। GitOps-এ CI শুধু image বানিয়ে একটা config repo-তে নতুন tag লেখে; cluster-এর ভেতরে চলা Argo CD সেই repo দেখে নিজে টেনে নেয় (pull) আর cluster-কে Git-এর সাথে মিলিয়ে রাখে। কেউ হাতে কিছু বদলালে Argo CD সেটা ফেরত Git-এর মতো করে দেয়। Rollback মানে শুধু `git revert`।

> **বাস্তব পরিস্থিতি:** রাত ২টায় কেউ হাতে `kubectl edit` দিয়ে production-এর replica ২০ থেকে ২ করে ফেলেছিল। Argo CD drift দেখে ৩ মিনিটে আবার ২০ করে দিল আর alert পাঠাল। আর প্রতিটি production পরিবর্তন এখন একটা PR, তাই audit সহজ।

```text
App repo (কোড)                          Config repo (deploy অবস্থা)
  push -> CI -> image a1b2c3d ──> PR: values-prod.yaml-এ image.tag: a1b2c3d
                                            │ merge
                                            v
                            Argo CD (cluster-এর ভেতরে) Git দেখে -> sync -> Kubernetes
```

```bash
# CI-এর শেষ ধাপ (config repo আপডেট)
yq -i '.image.tag = "a1b2c3d"' envs/prod/values.yaml
git commit -am "deploy shop a1b2c3d to prod" && git push
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| App repo / Config repo | কোড আর deploy অবস্থা আলাদা repo-তে, আলাদা অনুমতি |
| `yq -i '.image.tag = ...'` | YAML ফাইলে মান বদলাও (`-i` = in-place) |
| sync | Argo CD Git-এর অবস্থা cluster-এ প্রয়োগ করে |
| self-heal | হাতে বদলানো জিনিস আবার Git-এর মতো করে দেয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** GitOps কী আর এর সুবিধা কী?
**উত্তর:** Git-কে কাঙ্ক্ষিত অবস্থার একমাত্র উৎস বানিয়ে একটা agent (Argo CD/Flux) cluster-কে নিরন্তর তার সাথে মিলিয়ে রাখে। সুবিধা: প্রতিটি পরিবর্তন PR ও audit সহ, drift স্বয়ংক্রিয় ঠিক হয়, rollback = `git revert`, CI-এর হাতে cluster credential লাগে না।

**প্রশ্ন:** Push আর pull ভিত্তিক deployment-এর পার্থক্য?
**উত্তর:** Push-এ CI বাইরে থেকে cluster-এ কমান্ড পাঠায় (credential CI-তে)। Pull-এ cluster-এর ভেতরের agent Git থেকে পরিবর্তন টেনে প্রয়োগ করে (credential cluster-এর বাইরে যায় না)।

### Pipeline-এর স্বাস্থ্য: DORA metric ও গতি

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Deployment frequency** | কতবার production-এ deploy |
| **Change failure rate** | কত % deploy সমস্যা তৈরি করে |
| **MTTR** | **M**ean **T**ime **T**o **R**estore → সমস্যা থেকে ফিরতে গড় সময় |

**সহজ ভাষায়:** ভালো CI/CD মাপার চারটি DORA metric: deployment frequency, lead time for changes, change failure rate আর time to restore service। লক্ষ্য দ্রুত আর নিরাপদ দুটোই। Pipeline নিজেও দ্রুত রাখতে হয়: PR-এর CI ১০ মিনিটের মধ্যে হলে developer মনোযোগ ধরে রাখে।

> **বাস্তব পরিস্থিতি:** একটা টিমের PR CI ৪৫ মিনিট লাগত; developer রা ছোট PR না দিয়ে বড় PR জমাত। Cache, parallel test, Docker layer cache আর paths filter দিয়ে ৯ মিনিটে নামানো হলো; deploy frequency সপ্তাহে ২ থেকে দিনে ৫-এ উঠল।

| Pipeline দ্রুত করার উপায় | কতটা লাভ |
|---|---|
| Dependency cache | প্রায়ই সবচেয়ে বড় লাভ |
| Test parallel / sharding | test সময় ভাগ হয়ে যায় |
| Docker layer cache (gha/registry) | image build মিনিট থেকে সেকেন্ডে |
| Fail fast: lint আর unit আগে | ভুল দ্রুত জানা যায় |
| paths filter, monorepo-তে শুধু বদলানো অংশ | অপ্রয়োজনীয় কাজ বাদ |
| ধীর E2E রাতে বা staging-এ | PR হালকা থাকে |
| বড় runner বা self-hosted cache | ভারী build-এ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** DORA-এর চারটি metric কী কী?
**উত্তর:** Deployment frequency, lead time for changes, change failure rate, আর failed deployment recovery time (MTTR)। প্রথম দুটো গতি, শেষ দুটো স্থিতিশীলতা মাপে।

---

## ১০. Developer-দের সবচেয়ে কমন ভুল: সংকলন

প্রতিটি অধ্যায়ের ভুলের টেবিল একসাথে, আরও কিছু সহ। নতুন pipeline বানানোর আগে বা review-এর সময় checklist হিসেবে ব্যবহার করো।

### কোড, test ও pipeline লেখার ভুল

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Anti-pattern** | জনপ্রিয় কিন্তু ক্ষতিকর অভ্যাস |

**সহজ ভাষায়:** এই ভুলগুলো প্রায় প্রতিটি টিমে দেখা যায়। কারণগুলো সাধারণত প্রযুক্তিগত নয়: সময়ের চাপ, "পরে ঠিক করব" মানসিকতা, আর জ্ঞানের অভাব। তাই সমাধানের অনেকটাই নিয়ম আর স্বয়ংক্রিয় বাধা দিয়ে।

> **বাস্তব পরিস্থিতি:** এগুলো একসাথে মিলিয়ে একটা PR template আর pipeline review checklist বানাও।

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Local-এ test না চালিয়েই push, CI-কে test runner বানানো | "CI তো চালাবেই" | pre-commit hook (lint, format, secret scan), local-এ দ্রুত test |
| "CI fix" নামে ২০টা commit | YAML-এর ভুল push করে করে খোঁজা | `act` (local-এ Actions), actionlint, ছোট পরীক্ষামূলক branch |
| Test fail করলে test মুছে বা skip করে দেওয়া | deadline | আসল কারণ ঠিক করো; skip করলে ticket আর মেয়াদ |
| Pipeline-এ `continue-on-error` / `\|\| true` দিয়ে error লুকানো | লাল দেখতে ভালো লাগে না | শুধু সত্যিকারের ঐচ্ছিক ধাপে, কারণ লিখে |
| Pipeline YAML-এ হাজার লাইনের shell script | সব এক জায়গায় রাখতে | script আলাদা ফাইলে (`scripts/`), যাতে local-এও চালানো ও test করা যায় |
| Hardcoded পরিবেশের মান (URL, account ID) | দ্রুত চালানো | variable/environment config |
| Timeout ছাড়া job | default-এর কথা না জানা | `timeout-minutes` / options timeout; আটকে থাকা job runner খেয়ে ফেলে |
| প্রতি repo-তে আলাদা আলাদা নিয়মের pipeline | কেন্দ্রীয় মান নেই | reusable workflow / shared library দিয়ে মানসম্মত pipeline |

### Deploy, secret ও ব্যবস্থাপনার ভুল

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Hotfix** | hot fix → জরুরি সংশোধন |

**সহজ ভাষায়:** Deploy-এর ভুলগুলোর মূল্য সবচেয়ে বেশি, কারণ গ্রাহক সরাসরি দেখে। আর জরুরি অবস্থায় মানুষ pipeline এড়িয়ে শর্টকাট নেয়, যেটাই পরের বড় সমস্যার কারণ হয়।

> **বাস্তব পরিস্থিতি:** Production-এ জরুরি bug। একজন engineer সার্ভারে SSH করে হাতে ফাইল বদলালেন। কাজ হলো, কিন্তু পরের দিনের স্বাভাবিক deploy সেই পরিবর্তন মুছে দিল আর bug ফিরে এল। সমাধান: hotfix-ও একই pipeline দিয়ে, দরকার হলে দ্রুত পথ (ছোট test সেট) কিন্তু pipeline-এর বাইরে না।

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| জরুরি অবস্থায় সার্ভারে হাতে পরিবর্তন | pipeline ধীর মনে হয় | দ্রুত hotfix পথ (একই pipeline), পরিবর্তন সবসময় Git-এ |
| প্রতি পরিবেশে আলাদা build | Dockerfile-এ পরিবেশের ফাইল | build once, deploy many; config runtime-এ |
| Production deploy-এ কোনো অনুমোদন বা নিয়ন্ত্রণ নেই | ছোট টিম, "আমরা সবাই বিশ্বস্ত" | environment protection, অন্তত ঝুঁকিপূর্ণ পরিবর্তনে |
| Rollback পরিকল্পনা নেই | আশাবাদ | প্রতিটি deploy-এর আগে rollback পথ জানা ও পরীক্ষিত |
| Migration আর কোড একসাথে ভাঙা পরিবর্তন | এক ধাপে শেষ করার ইচ্ছা | expand-contract |
| Pipeline-এর log-এ secret ছাপানো | debug | masking, কখনো echo না, `set -x` বন্ধ |
| স্থায়ী cloud access key CI-তে | পুরনো পদ্ধতি | OIDC, স্বল্পমেয়াদি credential |
| Third-party action/plugin-এর version pin না করা | সবসময় সর্বশেষ চাওয়া | SHA বা নির্দিষ্ট version, Dependabot দিয়ে আপডেট |
| Deploy হলো কিনা শুধু pipeline সবুজ দেখে ধরা | সহজ | smoke test, metric, alert |
| Notification সবাইকে সব বার্তা | সবাই জানুক ভেবে | শুধু ব্যর্থতা আর গুরুত্বপূর্ণ ঘটনা, সঠিক চ্যানেলে, নইলে সবাই উপেক্ষা করে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Pipeline বানাতে গিয়ে তুমি কোন ভুল থেকে শিখেছ?
**উত্তর:** নিজের একটা বাস্তব উদাহরণ বলো (যেমন secret leak, flaky test, migration সমস্যা): কী হয়েছিল, কেন, কী ঠিক করলে, আর ভবিষ্যতে ঠেকাতে কী স্বয়ংক্রিয় নিয়ম যোগ করলে। Interviewer সততা আর শেখার ক্ষমতা দেখেন।

---

## ১১. বাস্তব Incident Playbook

CI/CD-এর সবচেয়ে কমন সমস্যায় ধাপে ধাপে কী করবে। ইন্টারভিউতে "X কাজ করছে না, কী করবে?" প্রশ্নেও এভাবে উত্তর দিও। বাস্তব কাজেও checklist হিসেবে ব্যবহার করো।

### 🚨 Local-এ চলে কিন্তু CI-তে fail

1. CI log-এ **প্রথম** error টা খুঁজে বের করো (শেষেরটা নয়)
2. Runtime version মেলে? (`.nvmrc`, `.python-version`, `go.mod`)
3. Lockfile commit করা আর `npm ci`/সমতুল্য ব্যবহার হচ্ছে?
4. Local-এ থাকা কিন্তু commit না করা ফাইল? (`git status`, `.gitignore`)
5. Environment variable/secret CI-তে সেট করা আছে?
6. Case-sensitive path (Mac-এ চলে, Linux-এ না)?
7. CI-এর মতো পরিষ্কার পরিবেশে চালাও: `docker run --rm -v $PWD:/app -w /app node:20 npm ci && npm test`

### 🚨 Deploy-এর পর production ভাঙল

1. প্রথমে গ্রাহককে বাঁচাও: rollback (`helm rollback` / আগের tag / feature flag বন্ধ / GitOps-এ `git revert`)
2. তারপর কারণ খোঁজো: deploy-এর diff, নতুন error log, metric-এর পরিবর্তনের সময়
3. Config/secret-এর পার্থক্য? Migration-এর প্রভাব?
4. কেন staging/smoke test ধরতে পারেনি তা খুঁজে test যোগ করো
5. Blameless postmortem লেখো, প্রক্রিয়ায় স্থায়ী উন্নতি

### 🚨 Secret Git-এ push হয়ে গেছে

1. সাথে সাথে secret বাতিল/rotate করো (ধরে নাও ফাঁস হয়েছে)
2. নতুন secret, secret store-এ রাখো, ব্যবহারকারী সিস্টেম আপডেট
3. Cloud/সার্ভিসের log-এ অপব্যবহার হয়েছে কিনা দেখো
4. Git ইতিহাস থেকে সরাও (`git filter-repo`), force push, সবাইকে নতুন clone করতে বলো
5. ভবিষ্যতে: pre-commit secret scan, CI-তে Gitleaks, GitHub push protection

### 🚨 Pipeline খুব ধীর

1. কোন job/step কত সময় নিচ্ছে দেখো (Actions-এর timing, Jenkins stage view)
2. Dependency cache আছে আর কাজ করছে? (cache hit/miss)
3. Docker build-এ layer cache, `.dockerignore`, ঠিক ক্রম?
4. Test parallel করা যায়? ধীর E2E PR থেকে সরানো যায়?
5. paths filter, অপ্রয়োজনীয় workflow বন্ধ
6. `concurrency cancel-in-progress` দিয়ে পুরনো রান বাতিল

### 🚨 Flaky test বারবার pipeline আটকাচ্ছে

1. কোন test, কত শতাংশ সময় fail করে, ইতিহাস দেখো
2. Quarantine: আলাদা non-blocking job-এ সরাও, ticket খোলো, মালিক দাও
3. কারণ খোঁজো: sleep/timing, shared data, test-এর ক্রম, বাইরের API, timezone/তারিখ
4. ঠিক করে আবার মূল suite-এ ফেরত আনো
5. নিয়ম: নতুন flaky test-এ অবহেলা নয়, সাথে সাথে ঠিক

### 🚨 Jenkins job queue-তে আটকে আছে

1. Build queue-তে কারণ দেখো: "waiting for next available executor" বা "no agent with label"
2. Agent গুলো online? (Manage Jenkins > Nodes)
3. Label-এর বানান ঠিক? Kubernetes plugin-এর pod তৈরি হচ্ছে? (`kubectl get pods -n jenkins`)
4. Disk ভরা agent offline হয়ে যায়: পরিষ্কার করো
5. আটকে থাকা পুরনো build বাতিল, timeout option যোগ করো

---

## ১২. ইন্টারভিউ প্রশ্ন ব্যাংক

প্রতিটি অধ্যায়ের কার্ডে থাকা প্রশ্নের বাইরে আরও কিছু জনপ্রিয় প্রশ্ন, লেভেল অনুযায়ী সাজানো। উত্তর মুখস্থ না করে নিজের ভাষায় বলার অনুশীলন করো।

### 🟢 বেসিক লেভেল

**প্রশ্ন ১. CI আর CD-এর পার্থক্য বলো।**
CI প্রতিটি কোড পরিবর্তন স্বয়ংক্রিয়ভাবে build ও test করে মূল কোডে ঘন ঘন মেলায়। CD সেই পরীক্ষিত পরিবর্তনকে স্বয়ংক্রিয়ভাবে release যোগ্য বানায় (Delivery) বা সরাসরি production-এ পাঠায় (Deployment)।

**প্রশ্ন ২. একটা CI pipeline-এ সাধারণত কী কী stage থাকে?**
Checkout, dependency install, lint/format, unit test, build, security scan (SAST, dependency, secret), artifact/image তৈরি ও push।

**প্রশ্ন ৩. Artifact কী?**
Build-এর ফল যা deploy করা হয়: jar, zip, binary বা Docker image; অপরিবর্তনীয় আর version সহ registry-তে রাখা হয়।

**প্রশ্ন ৪. GitHub Actions-এর workflow ফাইল কোথায় থাকে আর কী দিয়ে চালু হয়?**
`.github/workflows/*.yml`-এ; `on:` অংশে দেওয়া ঘটনা (push, pull_request, schedule, workflow_dispatch, tag) দিয়ে চালু হয়।

**প্রশ্ন ৫. Jenkinsfile কী?**
Repo-তে রাখা pipeline-এর কোড (Groovy, সাধারণত declarative syntax), যাতে pipeline-ও version control-এ থাকে, review হয় আর সব branch-এ একই নিয়ম মানে।

### 🟡 ইন্টারমিডিয়েট লেভেল

**প্রশ্ন ৬. Build once, deploy many নীতি কী আর কেন?**
একবার artifact বানিয়ে সেই একই artifact সব পরিবেশে উন্নীত করা; পরিবেশের পার্থক্য config-এ। এতে staging-এ যা পরীক্ষিত, production-এ হুবহু তাই যায়।

**প্রশ্ন ৭. Pipeline-এ secret কীভাবে নিরাপদে ব্যবহার করবে?**
CI-এর secret store বা বাইরের vault-এ রাখা, log masking, ন্যূনতম অনুমতি, প্রতি পরিবেশে আলাদা secret, OIDC দিয়ে স্থায়ী cloud key বাদ, আর অবিশ্বস্ত PR (fork)-এ secret না দেওয়া।

**প্রশ্ন ৮. Blue-green, canary আর rolling deployment-এর পার্থক্য?**
Rolling ধাপে ধাপে instance বদলায়। Blue-green দুটো পূর্ণ পরিবেশ রেখে traffic একবারে সরায়, rollback তাৎক্ষণিক। Canary অল্প শতাংশ traffic দিয়ে শুরু করে metric দেখে বাড়ায়, ঝুঁকি সবচেয়ে কম।

**প্রশ্ন ৯. Rollback কীভাবে করবে?**
আগের image tag আবার deploy (`helm rollback`, `kubectl rollout undo`), blue-green-এ traffic ফেরত, feature flag বন্ধ, বা GitOps-এ `git revert`; database migration backward-compatible থাকলেই কোড rollback নিরাপদ।

**প্রশ্ন ১০. Flaky test-এর সমস্যা কী আর কীভাবে সামলাবে?**
CI-এর উপর আস্থা নষ্ট করে আর আসল bug লুকায়। শনাক্ত করে quarantine, কারণ (timing, shared state, বাইরের নির্ভরতা) ঠিক করা, মালিকানা দেওয়া; auto-retry দিয়ে চিরকাল লুকিয়ে না রাখা।

**প্রশ্ন ১১. GitHub Actions-এ needs, matrix আর environment কী?**
`needs` job-এর ক্রম আর নির্ভরতা ঠিক করে; `matrix` একই job কয়েকটা সংমিশ্রণে (version/OS) চালায়; `environment` deploy লক্ষ্যের নিয়ম (অনুমোদন, branch সীমা) আর তার secret দেয়।

**প্রশ্ন ১২. Monorepo-তে CI কীভাবে দক্ষ রাখবে?**
paths filter বা পরিবর্তন শনাক্ত করে শুধু প্রভাবিত সার্ভিস build/test, build টুলের cache (Nx, Turborepo, Bazel), আর reusable workflow।

### 🔴 অ্যাডভান্সড লেভেল

**প্রশ্ন ১৩. Pipeline-এর supply chain নিরাপত্তা কীভাবে নিশ্চিত করবে?**
Dependency pin ও lockfile, action/plugin SHA দিয়ে pin, ন্যূনতম token অনুমতি, ephemeral runner, artifact-এ SBOM, image signing (cosign) ও deploy-এর আগে যাচাই, provenance (SLSA), আর branch protection।

**প্রশ্ন ১৪. Zero-downtime deploy-এর সাথে database migration কীভাবে মেলাবে?**
Expand-contract pattern, backward-compatible migration, migration আলাদা ধাপে (pre-deploy job) চালানো, বড় টেবিলে online migration টুল, আর পুরনো-নতুন দুই কোড version একসাথে চলতে পারে এমন নকশা।

**প্রশ্ন ১৫. GitOps আর সাধারণ push-ভিত্তিক CD-এর পার্থক্য আর সুবিধা কী?**
GitOps-এ Git কাঙ্ক্ষিত অবস্থার উৎস, cluster-এর ভেতরের agent pull করে মিলিয়ে রাখে; drift সংশোধন, audit, সহজ rollback, আর CI-তে cluster credential লাগে না।

**প্রশ্ন ১৬. Pipeline ১ ঘণ্টা লাগে। কীভাবে ১০ মিনিটে আনবে?**
প্রথমে মাপো কোথায় সময় যায়; তারপর cache, parallel/sharding, Docker layer cache, fail-fast ক্রম, অপ্রয়োজনীয় কাজ বাদ (paths filter), ধীর E2E আলাদা সময়ে, বড় বা cache যুক্ত runner।

**প্রশ্ন ১৭. DORA metric গুলো কী আর কীভাবে উন্নত করবে?**
Deployment frequency, lead time, change failure rate, recovery time। ছোট PR আর trunk-based, দ্রুত নির্ভরযোগ্য pipeline, ভালো test, progressive delivery (canary, feature flag) আর দ্রুত rollback দিয়ে।

**প্রশ্ন ১৮. Self-hosted runner/agent-এর নিরাপত্তা ঝুঁকি কী?**
PR-এর অবিশ্বস্ত কোড runner-এ চলে; স্থায়ী runner-এ আগের build-এর secret/ফাইল থেকে যায়; ভেতরের নেটওয়ার্কে প্রবেশাধিকার। সমাধান: ephemeral isolated runner, public repo-তে না, ন্যূনতম নেটওয়ার্ক অনুমতি।

---

<p align="center"><sub>CI/CD Pipeline: Complete Guide · <a href="../README.md">← সব গাইড</a></sub></p>
