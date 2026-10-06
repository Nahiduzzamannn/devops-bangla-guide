# DevOps Bangla Guide

```bash
$ devops learn --lang=bn --from=basic --to=advanced
```

**বাংলায় সম্পূর্ণ DevOps হ্যান্ডবুক: কমান্ড, বাস্তব প্রোডাকশন পরিস্থিতি, ভুল ও সমাধান, আর ইন্টারভিউ প্রশ্ন।**

প্রতিটি টপিক একটা আলাদা Markdown গাইডে আছে। প্রতিটি কার্ডে শুধু কমান্ড না, সাথে আছে কমান্ডের পূর্ণরূপ, অপশনের অক্ষরে অক্ষরে মানে, একটা বাস্তব পরিস্থিতি আর প্রো টিপ।

---

## 📚 গাইডসমূহ

| # | গাইড | কী আছে |
|---|---|---|
| ০১ | [Linux for DevOps](guides/01-linux-for-devops.md) | ১০৫টি কমান্ড কার্ড: ফাইল, টেক্সট প্রসেসিং, permission, প্রসেস, disk, networking, SSH, systemd, cron, security, kernel tuning, Incident Playbook |
| ০২ | [Networking: Basic to Advanced](guides/02-networking.md) | OSI, IP, subnetting, switching, routing, security, wireless, cloud networking; Linux, Cisco ও Windows troubleshooting কমান্ড |
| ০৩ | [Python & Go for DevOps](guides/03-python-and-go-for-devops.md) | Python: subprocess, JSON/YAML, API, SSH, boto3, Kubernetes client · Go: goroutine, channel, CLI, HTTP service, Prometheus exporter, Operator |
| ০৪ | [Docker: Complete Guide](guides/04-docker.md) | run, exec, logs, volume, network, buildx, multi-stage build, security, CI/CD, troubleshooting |
| ০৫ | [CI/CD Pipeline: Complete Guide](guides/05-cicd-pipeline.md) | Branching, build, test, security scan, deploy কৌশল, rollback; GitHub Actions ও Jenkins লাইন ধরে ব্যাখ্যা |
| ০৬ | [Kubernetes & Monitoring](guides/06-kubernetes-and-monitoring.md) | Architecture থেকে probe, HPA, Ingress; ১৪টি হাতে-কলমে scenario; PromQL, alert rule, Grafana, ELK |
| ০৭ | [AWS Core Services](guides/07-aws-core-services.md) | IAM, VPC, EC2, S3: least privilege, subnet ও routing, Auto Scaling, lifecycle, CloudFront |

---

## 🧭 কোন ক্রমে পড়বে

```text
Linux ─► Networking ─► Python & Go ─► Docker ─► CI/CD ─► Kubernetes & Monitoring ─► AWS
```

একদম নতুন হলে উপরের ক্রমে পড়ো। কোনো নির্দিষ্ট বিষয় দরকার হলে সরাসরি সেই গাইডে গিয়ে সূচিপত্র থেকে লাফ দাও।

---

## 🏷️ লেভেল ট্যাগ

প্রতিটি কার্ডে একটা লেভেল ট্যাগ আছে:

| ট্যাগ | মানে |
|---|---|
| 🟢 বেসিক | প্রথম দিন থেকেই দরকার |
| 🟡 ইন্টারমিডিয়েট | প্রোডাকশনে নিয়মিত কাজে লাগে |
| 🔴 অ্যাডভান্সড | সিনিয়র-লেভেলের troubleshooting ও ডিজাইন |

---

## 🧩 প্রতিটি কার্ডের গঠন

- **পূর্ণরূপ:** কমান্ড বা শব্দটা কিসের সংক্ষেপ
- **সহজ ভাষায় / কী করে:** এক-দুই লাইনে মূল ধারণা
- **বাস্তব পরিস্থিতি:** প্রোডাকশনে কখন এটা লাগে
- **কোড:** কপি করে চালানোর মতো উদাহরণ
- **অপশনের পূর্ণরূপ:** প্রতিটি flag-এর অক্ষরে অক্ষরে মানে
- **ভুল ও সমাধান:** সাধারণ ভুল, কেন হয়, কীভাবে ঠিক করবে
- **ইন্টারভিউ প্রশ্ন ও উত্তর**
- **প্রো টিপ**

---

## 🤝 অবদান

ভুল চোখে পড়লে বা নতুন কিছু যোগ করতে চাইলে issue বা pull request খোলো।

<p align="center"><sub>শেখা চলুক · Happy shipping 🚀</sub></p>
