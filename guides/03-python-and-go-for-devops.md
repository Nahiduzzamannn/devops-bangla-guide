# Python & Go for DevOps

```bash
$ python3 automate.py && go build ./cmd/ops
```

**স্ক্রিপ্টিং, অটোমেশন ও টুল বানানো — Python কোথায়, Go কোথায়, আর কেন**

- **Python:** ফাইল, subprocess, JSON/YAML, regex, API, SSH, boto3, Kubernetes
- **Go:** goroutine, channel, CLI, HTTP সার্ভিস, Prometheus exporter, Operator

| | |
|---|---|
| অধ্যায় | ১২টি + প্রশ্ন ব্যাংক |
| টপিক কার্ড | ৩৮টি, লাইন ধরে বাংলা ব্যাখ্যা |
| ইন্টারভিউ প্রশ্ন | ৮০টি প্রশ্ন ও উত্তর |
| প্রজেক্ট আইডিয়া | ১০টি |
| লেভেল | 🟢 বেসিক · 🟡 ইন্টারমিডিয়েট · 🔴 অ্যাডভান্সড |

---

## এই গাইড কীভাবে ব্যবহার করবে

প্রথম অধ্যায়ে দেখবে DevOps-এ কেন প্রোগ্রামিং লাগে আর Python ও Go-এর তুলনা। তারপর **Part A**-তে Python: ভাষার ভিত্তি থেকে শুরু করে ফাইল, শেল কমান্ড, JSON/YAML, API, SSH, AWS, Kubernetes অটোমেশন আর টেস্টিং। **Part B**-তে Go: ভাষার ভিত্তি, concurrency, CLI টুল, HTTP সার্ভিস, Prometheus exporter, ছোট Docker image আর Kubernetes operator। শেষে একই কাজ দুই ভাষায়, সিদ্ধান্তের টেবিল, প্রজেক্ট আইডিয়া আর ইন্টারভিউ প্রশ্ন ব্যাংক।

প্রতিটি কার্ডে আছে **পূর্ণরূপ**, **সহজ ভাষায়** ব্যাখ্যা, **বাস্তব পরিস্থিতি**, কাজ করা কোড, তার নিচে **লাইন ধরে ব্যাখ্যা**, **ইন্টারভিউ প্রশ্ন**, আর **প্রো টিপ**। কোড শুধু পড়বে না, নিজে টাইপ করে চালিয়ে দেখবে আর একটু বদলে পরীক্ষা করবে।

> **নোট:** কোডগুলো Python 3.10+ আর Go 1.22+-এর জন্য। জায়গা বাঁচাতে Go উদাহরণে কোথাও কোথাও `package` আর `import` লাইন বাদ রাখা হয়েছে; পুরো প্রোগ্রাম বানাতে প্রথম Go কার্ডের মতো সেগুলো যোগ করবে (gopls/goimports নিজে যোগ করে দেয়)।

---

## সূচিপত্র

- [০০. শুরুর আগে: Python বনাম Go, কোনটা কখন](#০০-শুরুর-আগে-python-বনাম-go-কোনটা-কখন)

**Part A: Python**

- [০১. Python বেসিক: ভাষার ভিত্তি](#০১-python-বেসিক-ভাষার-ভিত্তি)
- [০২. Python দিয়ে ফাইল, OS ও শেল কমান্ড](#০২-python-দিয়ে-ফাইল-os-ও-শেল-কমান্ড)
- [০৩. ডেটা ফরম্যাট: JSON, YAML, CSV ও Regex](#০৩-ডেটা-ফরম্যাট-json-yaml-csv-ও-regex)
- [০৪. Python দিয়ে API, নেটওয়ার্ক ও SSH অটোমেশন](#০৪-python-দিয়ে-api-নেটওয়ার্ক-ও-ssh-অটোমেশন)
- [০৫. Python দিয়ে Cloud ও DevOps অটোমেশন](#০৫-python-দিয়ে-cloud-ও-devops-অটোমেশন)
- [০৬. Python: ভালো অনুশীলন, টেস্টিং ও Concurrency](#০৬-python-ভালো-অনুশীলন-টেস্টিং-ও-concurrency)

**Part B: Go**

- [০৭. Go বেসিক: ভাষার ভিত্তি](#০৭-go-বেসিক-ভাষার-ভিত্তি)
- [০৮. Go Concurrency: goroutine, channel, context](#০৮-go-concurrency-goroutine-channel-context)
- [০৯. Go দিয়ে DevOps টুল ও সার্ভিস বানানো](#০৯-go-দিয়ে-devops-টুল-ও-সার্ভিস-বানানো)
- [১০. Go টেস্টিং, টুলিং ও ভালো অনুশীলন](#১০-go-টেস্টিং-টুলিং-ও-ভালো-অনুশীলন)

**শেষ অংশ**

- [১১. সিদ্ধান্ত: বাস্তব কাজে Python না Go](#১১-সিদ্ধান্ত-বাস্তব-কাজে-python-না-go)
- [১২. প্রজেক্ট আইডিয়া: শিখে হাতে-কলমে করো](#১২-প্রজেক্ট-আইডিয়া-শিখে-হাতে-কলমে-করো)
- [১৩. ইন্টারভিউ প্রশ্ন ব্যাংক](#১৩-ইন্টারভিউ-প্রশ্ন-ব্যাংক)

---

## ০০. শুরুর আগে: Python বনাম Go, কোনটা কখন

DevOps ও SRE কাজে দুটো ভাষাই খুব জনপ্রিয়, কিন্তু দুটোর শক্তি আলাদা জায়গায়। আগে বড় ছবিটা বোঝো, তারপর বিস্তারিত।

### কেন DevOps engineer-এর প্রোগ্রামিং লাগে

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **DevOps** | **Dev**elopment + **Op**eration**s** → ডেভেলপমেন্ট ও অপারেশনের মিলন |
| **SRE** | **S**ite **R**eliability **E**ngineering → সাইটের নির্ভরযোগ্যতা প্রকৌশল |
| **IaC** | **I**nfrastructure **a**s **C**ode → কোড দিয়ে infrastructure |

**সহজ ভাষায়:** Bash দিয়ে ছোট কাজ ভালো চলে, কিন্তু কাজ একটু জটিল হলেই (API call, JSON, error handling, অনেক সার্ভারে একসাথে কাজ, টেস্ট) Bash দুর্বল হয়ে পড়ে। তখন একটা সত্যিকারের প্রোগ্রামিং ভাষা লাগে। DevOps-এ সাধারণত Python দিয়ে স্ক্রিপ্ট ও অটোমেশন, আর Go দিয়ে দ্রুত, নির্ভরযোগ্য টুল ও সার্ভিস বানানো হয়।

> **বাস্তব পরিস্থিতি:** প্রতি মাসে হাতে হাতে AWS-এ গিয়ে দেখতে হয় কোন EC2 instance-এ "Owner" tag নেই, তারপর Slack-এ জানাতে হয়; ২ ঘণ্টার কাজ। একটা ৪০ লাইনের Python স্ক্রিপ্ট cron-এ চালালে এটা প্রতিদিন নিজে নিজে হয়। আবার কোম্পানির জন্য একটা internal CLI টুল দরকার যেটা সবার ল্যাপটপে (Mac, Linux, Windows) কোনো dependency ছাড়া চলবে: এখানে Go সেরা, কারণ একটাই binary ফাইল।

| কাজ | Bash | Python | Go |
|---|---|---|---|
| ৫-১০ লাইনের ছোট কাজ | সেরা | ভালো | বাড়াবাড়ি |
| API, JSON, cloud SDK | কষ্টকর | সেরা | ভালো |
| দ্রুত prototype / one-off স্ক্রিপ্ট | ভালো | সেরা | মাঝারি |
| সবাইকে দেওয়ার মতো CLI টুল | দুর্বল | ভালো (Python লাগবে) | সেরা (একটা binary) |
| উচ্চ পারফরম্যান্স সার্ভিস / agent | না | মাঝারি | সেরা |
| হাজারো কাজ একসাথে (concurrency) | না | মাঝারি | সেরা (goroutine) |
| Kubernetes operator / controller | না | সম্ভব (kopf) | সেরা (client-go) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** DevOps-এ Python আর Go কোনটা শিখব?
**উত্তর:** দুটোই কাজে লাগে। শুরু করো Python দিয়ে, কারণ শেখা সহজ আর অটোমেশন, AWS (boto3), Ansible, স্ক্রিপ্টিং-এ সর্বত্র। তারপর Go শেখো, কারণ Docker, Kubernetes, Terraform, Prometheus সব Go-তে লেখা, আর নিজের টুল, exporter, operator বানাতে Go সেরা।

> 💡 **প্রো টিপ:** শুরুতে একটা ভাষা ভালো করে শেখো, দুটো একসাথে অর্ধেক অর্ধেক না।

### Python এক নজরে

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Python** | (Monty Python থেকে নাম) → সহজ, পড়ার মতো ভাষা |
| **PyPI** | **Py**thon **P**ackage **I**ndex → Python লাইব্রেরির ভাণ্ডার |
| **pip** | **P**ip **I**nstalls **P**ackages → package ইনস্টলার |
| **REPL** | **R**ead-**E**val-**P**rint **L**oop → লাইন ধরে কোড চালানোর শেল |

**সহজ ভাষায়:** Python একটা interpreted, dynamically typed ভাষা। Interpreted মানে আগে compile করতে হয় না, সরাসরি চালানো যায়। Dynamic typing মানে variable-এর type আগে বলতে হয় না। কোড ইংরেজির মতো পড়া যায়, আর indentation (space) দিয়ে block বোঝায়, তাই `{ }` লাগে না। বিশাল লাইব্রেরি আছে: AWS (boto3), Docker, Kubernetes, SSH, Excel, সবকিছুর জন্য।

> **বাস্তব পরিস্থিতি:** Ansible, AWS CLI, SaltStack, OpenStack সবই Python-এ লেখা। প্রায় সব Linux সার্ভারে Python আগে থেকেই থাকে, তাই স্ক্রিপ্ট কপি করে সাথে সাথে চালানো যায়।

| বৈশিষ্ট্য | Python |
|---|---|
| চালানো | `python3 script.py` (compile লাগে না) |
| Type | Dynamic (চলার সময় ঠিক হয়) |
| গতি | Go/C-এর চেয়ে ধীর, স্ক্রিপ্টিং-এর জন্য যথেষ্ট |
| Deployment | সার্ভারে Python আর লাইব্রেরি থাকতে হবে |
| Concurrency | Thread (GIL-এর সীমা), asyncio, multiprocessing |
| শক্তি | লেখা দ্রুত, লাইব্রেরি অসংখ্য, শেখা সহজ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Python interpreted বলতে কী বোঝায়?
**উত্তর:** সোর্স কোড আগে machine code-এ compile না করে interpreter (CPython) চলার সময় bytecode বানিয়ে চালায়। তাই দ্রুত লিখে চালানো যায়, কিন্তু compiled ভাষার চেয়ে ধীর।

### Go এক নজরে

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Go / Golang** | (golang.org ওয়েবসাইট থেকে Golang) → Google-এর তৈরি ভাষা |
| **goroutine** | go + routine → Go-এর হালকা thread |
| **GC** | **G**arbage **C**ollector → অব্যবহৃত memory নিজে পরিষ্কার |

**সহজ ভাষায়:** Go ২০০৯ সালে Google প্রকাশ করে, বড় সার্ভার সফটওয়্যার সহজে আর দ্রুত বানানোর জন্য। এটা compiled আর statically typed: compile করলে একটা মাত্র binary ফাইল তৈরি হয়, যেটা অন্য কিছু ইনস্টল ছাড়াই চলে। ভাষাটা ছোট আর সরল (মাত্র ২৫টা keyword), আর concurrency ভাষার ভেতরেই আছে (goroutine, channel)।

> **বাস্তব পরিস্থিতি:** Docker, Kubernetes, Terraform, Prometheus, Grafana Loki, etcd, Consul, Vault, Helm, kubectl: আধুনিক cloud-native দুনিয়ার প্রায় সব বড় টুল Go-তে লেখা। তাই এগুলোর source code পড়া, plugin বা operator লেখা, কিংবা bug খুঁজতে Go জানা বড় সুবিধা।

| বৈশিষ্ট্য | Go |
|---|---|
| চালানো | `go build` দিয়ে binary বানিয়ে `./app` |
| Type | Static (compile-এর সময় ধরা পড়ে) |
| গতি | অনেক দ্রুত, C-এর কাছাকাছি |
| Deployment | একটা binary কপি করলেই চলে, cross-compile সহজ |
| Concurrency | goroutine + channel, লাখো একসাথে |
| শক্তি | সরল, দ্রুত, নির্ভরযোগ্য, ছোট Docker image |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Go কেন cloud-native টুলের জন্য জনপ্রিয়?
**উত্তর:** Static single binary (সহজ deployment ও ছোট container), দ্রুত compile ও চলা, built-in concurrency (অনেক নেটওয়ার্ক সংযোগ একসাথে), শক্তিশালী standard library (HTTP, JSON), আর সরল ভাষা যাতে বড় টিম সহজে কাজ করতে পারে।

### পরিবেশ তৈরি: Python ও Go সেটআপ

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **venv** | **V**irtual **Env**ironment → প্রজেক্টের আলাদা Python পরিবেশ |
| **go mod** | Go **mod**ule → Go প্রজেক্টের dependency ব্যবস্থাপনা |

**সহজ ভাষায়:** Python-এ প্রতিটি প্রজেক্টের জন্য আলাদা virtual environment বানাও, যাতে এক প্রজেক্টের লাইব্রেরি অন্যটার সাথে না মেশে। Go-তে প্রতিটি প্রজেক্ট একটা module, `go.mod` ফাইলে dependency লেখা থাকে।

> **বাস্তব পরিস্থিতি:** একটা পুরনো স্ক্রিপ্টে `requests` 2.20 লাগে, নতুনটায় 2.32। সিস্টেমে একটাই রাখলে একটা ভাঙবে; প্রতিটির নিজের venv থাকলে দুটোই চলে।

```bash
# Python
python3 --version
python3 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install requests boto3 pyyaml
pip freeze > requirements.txt
pip install -r requirements.txt
deactivate

# Go
go version
mkdir healthcheck && cd healthcheck
go mod init github.com/rahim/healthcheck
go get github.com/spf13/cobra@latest
go run .
go build -o healthcheck .
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `python3 -m venv .venv` | `-m` = module চালাও; venv module দিয়ে `.venv` নামে নতুন পরিবেশ বানাও |
| `source .venv/bin/activate` | পরিবেশ চালু করো; এখন pip/python এই প্রজেক্টের |
| `pip freeze > requirements.txt` | ইনস্টল থাকা সব package আর version ফাইলে লেখো |
| `pip install -r requirements.txt` | `-r` = requirement file থেকে সব ইনস্টল করো |
| `go mod init <path>` | নতুন module বানাও, `go.mod` তৈরি হয়; path সাধারণত repo-এর ঠিকানা |
| `go get pkg@latest` | dependency যোগ করো, `go.mod` ও `go.sum` আপডেট হয় |
| `go run .` | `.` = বর্তমান ফোল্ডারের package compile করে সাথে সাথে চালাও |
| `go build -o healthcheck .` | `-o` = output: এই নামে binary বানাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** venv কেন দরকার?
**উত্তর:** প্রতিটি প্রজেক্টের dependency আলাদা রাখতে, যাতে version সংঘাত না হয় আর system Python নষ্ট না হয়।

**প্রশ্ন:** `go.sum` ফাইল কী?
**উত্তর:** প্রতিটি dependency-এর checksum, যাতে ডাউনলোড করা কোড পরিবর্তিত না হয় (supply chain নিরাপত্তা)। এটা Git-এ commit করতে হয়।

> 💡 **প্রো টিপ:** Ubuntu-তে সরাসরি `sudo pip install` দিও না; system Python ভেঙে যেতে পারে। সবসময় venv বা pipx ব্যবহার করো।

---

# Part A: Python

## ০১. Python বেসিক: ভাষার ভিত্তি

স্ক্রিপ্ট লেখার জন্য যা যা লাগবে: data type, collection, শর্ত, লুপ, function আর error handling।

### Variable, Data type ও String

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **str** | **str**ing → লেখা |
| **int** | **int**eger → পূর্ণ সংখ্যা |
| **float** | **float**ing point → দশমিক সংখ্যা |
| **bool** | **bool**ean → True বা False |
| **f-string** | **f**ormatted **string** → ভেতরে variable বসানো লেখা |

**সহজ ভাষায়:** Python-এ variable বানাতে শুধু `নাম = মান` লেখো; type নিজে বুঝে নেয়। f-string দিয়ে লেখার মধ্যে সহজে variable বসানো যায়, যেটা log আর মেসেজ বানাতে সবচেয়ে বেশি লাগে।

> **বাস্তব পরিস্থিতি:** একটা deploy স্ক্রিপ্ট সার্ভারের নাম, port আর version নিয়ে একটা মেসেজ বানিয়ে Slack-এ পাঠাবে।

```python
host = "web-01"        # str
port = 8080            # int
cpu = 73.5             # float
healthy = True         # bool
version = None         # কোনো মান নেই

msg = f"Deployed v2.3 to {host}:{port}, CPU {cpu:.1f}%"
print(msg)
print(type(port))                  # <class 'int'>

port_str = str(port)               # int -> str
num = int("42")                    # str -> int
name = "  Web-01 ".strip().lower() # "web-01"
print("web" in name)               # True
print(host.upper(), host.replace("01", "02"), host.split("-"))
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `host = "web-01"` | variable তৈরি; type বলতে হয় না |
| `None` | কিছু নেই বোঝায় (অন্য ভাষার null) |
| `f"...{host}..."` | f-string: `{ }`-এর ভেতরে variable বা expression বসে |
| `{cpu:.1f}` | দশমিকের পর ১ ঘর পর্যন্ত দেখাও (73.5) |
| `type()` | কোন type তা দেখায় |
| `str()` / `int()` | type রূপান্তর (casting) |
| `.strip().lower()` | শুরু-শেষের space বাদ, তারপর ছোট হাতের অক্ষর; method chaining |
| `"web" in name` | লেখার ভেতরে আছে কিনা, True/False |
| `.split("-")` | `-` দিয়ে ভাগ করে list বানাও: `['web', '01']` |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Python-এ mutable আর immutable type কী?
**উত্তর:** Immutable বদলানো যায় না (int, float, str, tuple); বদলালে নতুন object তৈরি হয়। Mutable জায়গায় বদলানো যায় (list, dict, set)।

**প্রশ্ন:** `is` আর `==`-এর পার্থক্য?
**উত্তর:** `==` মান সমান কিনা দেখে; `is` একই object (memory-তে একই জায়গা) কিনা দেখে। None তুলনায় `is None` ব্যবহার করো।

### List, Dict, Set, Tuple

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **list** | ক্রমযুক্ত, বদলানো যায় এমন তালিকা |
| **dict** | **dict**ionary → key: value জোড়া |
| **set** | duplicate ছাড়া সংগ্রহ |
| **tuple** | বদলানো যায় না এমন তালিকা |

**সহজ ভাষায়:** অটোমেশনের প্রায় সব data এই চারটা দিয়ে রাখা হয়। সার্ভারের তালিকা = list, একটা সার্ভারের তথ্য (নাম, IP, region) = dict, অনন্য IP গুলো = set, কখনো বদলাবে না এমন জোড়া = tuple। API থেকে আসা JSON Python-এ dict আর list হয়ে যায়।

> **বাস্তব পরিস্থিতি:** Inventory: প্রতিটি সার্ভারের নাম, IP আর environment আছে। শুধু production সার্ভারগুলোর IP বের করে একটা তালিকা বানাতে হবে, duplicate বাদ দিয়ে।

```python
servers = [
    {"name": "web-01", "ip": "10.0.1.10", "env": "prod"},
    {"name": "web-02", "ip": "10.0.1.11", "env": "prod"},
    {"name": "dev-01", "ip": "10.0.9.5",  "env": "dev"},
]
print(servers[0]["name"])                           # web-01
servers.append({"name": "db-01", "ip": "10.0.2.5", "env": "prod"})

prod_ips = [s["ip"] for s in servers if s["env"] == "prod"]
print(prod_ips, len(prod_ips))

config = {"region": "ap-southeast-1", "retries": 3}
region = config.get("region", "us-east-1")          # না থাকলে default
config["timeout"] = 10
for key, value in config.items():
    print(key, "=", value)

unique_envs = {s["env"] for s in servers}           # set: {'prod', 'dev'}
point = ("10.0.1.10", 22)                           # tuple
ip, port = point                                    # unpacking
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `[ {...}, {...} ]` | dict-এর list: JSON-এর মতো গঠন |
| `servers[0]["name"]` | প্রথম (index 0) সার্ভারের name |
| `.append()` | list-এর শেষে যোগ করো |
| `[s["ip"] for s in servers if ...]` | list comprehension: এক লাইনে loop + filter করে নতুন list |
| `len()` | কয়টা উপাদান |
| `config.get("region", default)` | key না থাকলে error না দিয়ে default দাও |
| `.items()` | dict-এর প্রতিটি (key, value) জোড়া |
| `{s["env"] for s in servers}` | set comprehension, duplicate নিজে বাদ |
| `ip, port = point` | unpacking: tuple-এর মান আলাদা variable-এ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** List আর tuple-এর পার্থক্য?
**উত্তর:** List mutable `[ ]`, tuple immutable `( )`। Tuple dict-এর key হতে পারে, কিছুটা দ্রুত ও কম memory নেয়।

**প্রশ্ন:** `dict[key]` আর `dict.get(key)`-এর পার্থক্য?
**উত্তর:** key না থাকলে `dict[key]` KeyError দেয়; `get()` None বা দেওয়া default ফেরত দেয়।

**প্রশ্ন:** List comprehension কী?
**উত্তর:** `[expression for item in iterable if condition]` আকারে এক লাইনে নতুন list তৈরির পদ্ধতি।

### শর্ত (if) ও লুপ (for, while)

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **if / elif / else** | if / else if / else → শর্ত অনুযায়ী সিদ্ধান্ত |
| **for** | প্রতিটির জন্য |
| **while** | যতক্ষণ শর্ত সত্য |

**সহজ ভাষায়:** Python-এ block বোঝাতে কোলন (`:`) আর ৪ space indentation লাগে। `for` লুপ কোনো তালিকার প্রতিটি উপাদানের উপর চলে; `while` শর্ত সত্য থাকা পর্যন্ত চলে। `break` লুপ থামায়, `continue` পরের ধাপে যায়।

> **বাস্তব পরিস্থিতি:** Disk ব্যবহারের তালিকা দেখে ৮০%-এর বেশি হলে warning, ৯০%-এর বেশি হলে critical লিখতে হবে। আরেকটা স্ক্রিপ্ট সার্ভিস চালু না হওয়া পর্যন্ত প্রতি ৫ সেকেন্ডে চেক করবে, সর্বোচ্চ ১২ বার।

```python
disks = {"/": 72, "/var": 91, "/data": 85}
for mount, used in disks.items():
    if used >= 90:
        level = "CRITICAL"
    elif used >= 80:
        level = "WARNING"
    else:
        continue                      # ঠিক আছে, পরেরটায় যাও
    print(f"{level}: {mount} is {used}% full")

import time
attempt = 0
while attempt < 12:
    attempt += 1
    if service_is_up():               # নিজের লেখা function
        print("service is up")
        break
    time.sleep(5)
else:
    print("gave up after 12 tries")   # break না হলে চলে

for i, name in enumerate(["a", "b"], start=1):
    print(i, name)
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `for mount, used in disks.items():` | dict-এর প্রতিটি key আর value একসাথে নাও |
| `if / elif / else` | elif = else if; উপর থেকে প্রথম সত্য শর্তটা চলে |
| `continue` | এই ধাপের বাকিটা বাদ দিয়ে পরের উপাদানে যাও |
| `attempt += 1` | `attempt = attempt + 1`-এর সংক্ষেপ |
| `break` | লুপ এখনই শেষ |
| `while ... else` | লুপ break ছাড়া স্বাভাবিকভাবে শেষ হলে else চলে; retry-তে কাজের |
| `time.sleep(5)` | ৫ সেকেন্ড অপেক্ষা |
| `enumerate(..., start=1)` | index সহ লুপ, ১ থেকে গোনা শুরু |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `break` আর `continue`-এর পার্থক্য?
**উত্তর:** `break` পুরো লুপ থামায়; `continue` শুধু বর্তমান ধাপ বাদ দিয়ে পরের ধাপে যায়।

### Function, Module ও `__main__`

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **def** | **def**ine → function সংজ্ঞায়িত করো |
| **module** | একটা `.py` ফাইল |
| **package** | module-এর ফোল্ডার |
| **`__main__`** | main (dunder = **d**ouble **under**score) → সরাসরি চালানো ফাইল |

**সহজ ভাষায়:** Function হলো নাম দেওয়া কোডের টুকরো যা বারবার ব্যবহার করা যায়। Default মান, keyword argument আর return মান দেওয়া যায়। Type hint দিলে কোড পড়া সহজ হয় আর editor ভুল ধরতে পারে। প্রতিটি `.py` ফাইল একটা module, import করে অন্য ফাইলে ব্যবহার করা যায়।

> **বাস্তব পরিস্থিতি:** `check_port()` function টা health check স্ক্রিপ্ট, deploy স্ক্রিপ্ট আর monitoring স্ক্রিপ্ট তিন জায়গাতেই লাগে। একটা `utils.py`-তে রেখে সবাই import করে।

```python
import socket

def check_port(host: str, port: int, timeout: float = 3.0) -> bool:
    """Return True if TCP port is open."""
    try:
        with socket.create_connection((host, port), timeout=timeout):
            return True
    except OSError:
        return False

def main() -> None:
    for host in ["10.0.1.10", "10.0.1.11"]:
        status = "OPEN" if check_port(host, 22) else "CLOSED"
        print(f"{host}:22 {status}")

if __name__ == "__main__":
    main()

# অন্য ফাইল থেকে:
# from utils import check_port
# check_port("db.local", 5432, timeout=1)
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `def check_port(...)` | function সংজ্ঞা |
| `host: str, port: int` | type hint: কোন type আশা করা হয় (বাধ্যতামূলক না, তবে ভালো অনুশীলন) |
| `timeout: float = 3.0` | default মান; না দিলে 3.0 |
| `-> bool` | এই function bool ফেরত দেয় |
| `"""..."""` | docstring: function কী করে তার বর্ণনা |
| `with ... :` | context manager: কাজ শেষে socket নিজে বন্ধ হয় |
| `x if cond else y` | এক লাইনের শর্ত (ternary) |
| `if __name__ == "__main__":` | ফাইলটা সরাসরি চালালে `main()` চলবে, import করলে চলবে না |
| `timeout=1` | keyword argument: নাম দিয়ে মান দেওয়া |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `if __name__ == "__main__"` কেন ব্যবহার হয়?
**উত্তর:** যাতে ফাইলটা সরাসরি চালালে কোড চলে, কিন্তু অন্য ফাইল import করলে শুধু function গুলো পায়, কোড আপনা থেকে চলে না।

**প্রশ্ন:** `*args` আর `**kwargs` কী?
**উত্তর:** `*args` যত খুশি positional argument tuple হিসেবে নেয়, `**kwargs` যত খুশি keyword argument dict হিসেবে নেয়।

### Error handling: try / except / finally

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Exception** | চলার সময়ের ব্যতিক্রম বা ত্রুটি |
| **raise** | নিজে error তোলো |

**সহজ ভাষায়:** স্ক্রিপ্টে নেটওয়ার্ক কাটা, ফাইল না থাকা, API ভুল উত্তর দেওয়া এসব হবেই। `try`-এর ভেতরে ঝুঁকিপূর্ণ কোড, `except`-এ নির্দিষ্ট error ধরে সামলাও, `finally` সবসময় চলে (পরিষ্কারের কাজ)। সব error চুপচাপ গিলে ফেলবে না; অন্তত log করো।

> **বাস্তব পরিস্থিতি:** ১০০টা সার্ভারে config push করছ। একটা সার্ভার বন্ধ থাকায় পুরো স্ক্রিপ্ট crash করে বাকি ৫০টা বাদ পড়ল। প্রতিটি সার্ভারের কাজ try/except-এ রাখলে একটার ব্যর্থতা লিখে রেখে বাকিগুলো চালিয়ে যাবে, শেষে ব্যর্থ তালিকা দেখাবে।

```python
import sys

failed = []
for host in hosts:
    try:
        push_config(host)
    except TimeoutError:
        failed.append((host, "timeout"))
    except PermissionError as e:
        failed.append((host, f"permission: {e}"))
    except Exception as e:                 # অপ্রত্যাশিত সব
        failed.append((host, repr(e)))
    else:
        print(f"{host}: OK")               # কোনো error না হলে
    finally:
        close_connection(host)             # সবসময় চলে

if failed:
    print("FAILED:", failed)
    sys.exit(1)                            # CI-কে ব্যর্থতা জানাও

def set_replicas(n: int) -> None:
    if n < 1:
        raise ValueError("replicas must be >= 1")
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `try:` | এখানের কোড error দিতে পারে |
| `except TimeoutError:` | শুধু এই ধরনের error ধরো; নির্দিষ্ট error আগে লেখো |
| `as e` | error object টা `e` নামে পাও, মেসেজ দেখার জন্য |
| `except Exception` | বাকি সব সাধারণ error; শেষে রাখো |
| `else:` | try-তে কোনো error না হলে চলে |
| `finally:` | error হোক বা না হোক, সবসময় চলে |
| `sys.exit(1)` | non-zero exit code: cron/CI বুঝবে স্ক্রিপ্ট ব্যর্থ |
| `raise ValueError(...)` | নিজে error তোলো, ভুল ইনপুট আটকাতে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Bare `except:` কেন খারাপ?
**উত্তর:** এটা KeyboardInterrupt, SystemExit সহ সব ধরে ফেলে; Ctrl+C দিয়েও থামানো যায় না আর আসল bug লুকিয়ে যায়। অন্তত `except Exception` ব্যবহার করো, আর log করো।

**প্রশ্ন:** `finally` কখন কাজে লাগে?
**উত্তর:** ফাইল, connection, lock বন্ধ করার মতো পরিষ্কারের কাজে, যেটা error হলেও করতে হবে। অনেক ক্ষেত্রে `with` ব্লক এটা আরও সুন্দরভাবে করে।

> 💡 **প্রো টিপ:** স্ক্রিপ্ট ব্যর্থ হলে সবসময় non-zero exit code দাও; নইলে cron বা CI pipeline ভাববে সব ঠিক আছে।

---

## ০২. Python দিয়ে ফাইল, OS ও শেল কমান্ড

সার্ভারের ফাইল, ফোল্ডার, environment variable আর shell কমান্ড চালানো: অটোমেশনের দৈনন্দিন কাজ।

### ফাইল পড়া-লেখা ও pathlib

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **pathlib** | path library → ফাইল path নিয়ে কাজের লাইব্রেরি |
| **shutil** | **sh**ell **util**ities → কপি, সরানো, মোছার টুল |

**সহজ ভাষায়:** pathlib দিয়ে path-কে object হিসেবে ধরা যায়: `/` দিয়ে জোড়া লাগানো, ফাইল আছে কিনা, size, পুরনো কিনা সব সহজে। `with open()` দিয়ে ফাইল খুললে কাজ শেষে নিজে বন্ধ হয়।

> **বাস্তব পরিস্থিতি:** `/var/log/myapp` ফোল্ডারে ৭ দিনের পুরনো `.log` ফাইলগুলো gzip করে archive ফোল্ডারে সরাতে হবে, আর কত জায়গা খালি হলো দেখাতে হবে।

```python
from pathlib import Path
import gzip, shutil, time

log_dir = Path("/var/log/myapp")
archive = log_dir / "archive"
archive.mkdir(parents=True, exist_ok=True)

cutoff = time.time() - 7 * 24 * 3600
freed = 0
for f in log_dir.glob("*.log"):
    if f.stat().st_mtime < cutoff:
        target = archive / (f.name + ".gz")
        with open(f, "rb") as src, gzip.open(target, "wb") as dst:
            shutil.copyfileobj(src, dst)
        freed += f.stat().st_size
        f.unlink()                          # আসল ফাইল মোছো
print(f"Freed {freed / 1024**2:.1f} MB")

text = Path("/etc/hostname").read_text().strip()
Path("report.txt").write_text(f"host={text}\n")

with open("app.log") as fh:
    for line in fh:                         # বড় ফাইলও লাইন ধরে, memory কম
        if "ERROR" in line:
            print(line.rstrip())
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `Path("/var/log/myapp")` | path object |
| `log_dir / "archive"` | `/` দিয়ে path জোড়া লাগাও (OS অনুযায়ী ঠিক separator) |
| `mkdir(parents=True, exist_ok=True)` | `mkdir -p`-এর মতো: মাঝের ফোল্ডারও বানাও, থাকলে error নয় |
| `glob("*.log")` | `.log` দিয়ে শেষ সব ফাইল |
| `stat().st_mtime` | শেষ modify সময় (epoch সেকেন্ড) |
| `"rb"` / `"wb"` | read binary / write binary mode |
| `shutil.copyfileobj` | এক ফাইল থেকে আরেক ফাইলে stream করে কপি |
| `unlink()` | ফাইল মোছো (rm) |
| `read_text()` / `write_text()` | ছোট ফাইল এক লাইনে পড়া/লেখা |
| `for line in fh:` | বড় ফাইল একবারে memory-তে না তুলে লাইন ধরে পড়া |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `open()`-এর সাথে `with` কেন ব্যবহার করা উচিত?
**উত্তর:** `with` ব্লক শেষ হলে, error হলেও, ফাইল নিজে বন্ধ হয়; নইলে file descriptor leak হতে পারে।

**প্রশ্ন:** বড় (১০ GB) log ফাইল কীভাবে পড়বে?
**উত্তর:** `f.read()` দিয়ে পুরোটা না, `for line in f` দিয়ে লাইন ধরে পড়বে, যাতে memory-তে একসাথে একটা লাইনই থাকে।

### subprocess: shell কমান্ড চালানো

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **subprocess** | sub-process → চাইল্ড প্রসেস চালানো |
| **stdout / stderr** | **St**andar**d Out**put / **St**andar**d Err**or → সাধারণ আউটপুট / error আউটপুট |

**সহজ ভাষায়:** Python থেকে systemctl, kubectl, git, terraform-এর মতো কমান্ড চালাতে `subprocess.run()` ব্যবহার হয়। কমান্ডকে list আকারে দাও (`shell=False`), যাতে user ইনপুট দিয়ে command injection না হয়। `check=True` দিলে কমান্ড fail করলে exception ওঠে।

> **বাস্তব পরিস্থিতি:** Deploy স্ক্রিপ্ট: `git pull`, তারপর `systemctl restart`, তারপর health check। কোনো ধাপ fail করলে থেমে যাবে আর error মেসেজ দেখাবে।

```python
import subprocess

def run(cmd: list[str]) -> str:
    result = subprocess.run(
        cmd, capture_output=True, text=True, check=True, timeout=120
    )
    return result.stdout.strip()

try:
    run(["git", "-C", "/opt/app", "pull", "--ff-only"])
    run(["sudo", "systemctl", "restart", "myapp"])
    status = run(["systemctl", "is-active", "myapp"])
    print("myapp:", status)
except subprocess.CalledProcessError as e:
    print(f"Command failed ({e.returncode}): {e.cmd}\n{e.stderr}")
    raise SystemExit(1)
except subprocess.TimeoutExpired:
    print("Command timed out")
    raise SystemExit(1)
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `subprocess.run(cmd, ...)` | কমান্ড চালাও আর শেষ হওয়া পর্যন্ত অপেক্ষা করো |
| `["git", "-C", ...]` | কমান্ড list আকারে: প্রতিটি argument আলাদা, shell ছাড়া চলে, নিরাপদ |
| `capture_output=True` | stdout আর stderr ধরে রাখো, স্ক্রিনে না দেখিয়ে |
| `text=True` | byte-এর বদলে string হিসেবে দাও |
| `check=True` | exit code 0 না হলে `CalledProcessError` তোলো |
| `timeout=120` | ১২০ সেকেন্ডের বেশি চললে থামাও |
| `e.returncode` / `e.stderr` | ব্যর্থ কমান্ডের exit code আর error লেখা |
| `raise SystemExit(1)` | `sys.exit(1)`-এর মতো, exit code 1 দিয়ে বের হও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `shell=True` কেন বিপজ্জনক?
**উত্তর:** পুরো লেখাটা shell-এ যায়; user ইনপুট থাকলে `; rm -rf /`-এর মতো injection সম্ভব। List আকারে argument দিয়ে `shell=False` রাখা নিরাপদ।

**প্রশ্ন:** `os.system` আর `subprocess.run`-এর পার্থক্য?
**উত্তর:** `os.system` পুরনো, শুধু exit code দেয়, output ধরতে পারে না, সবসময় shell ব্যবহার করে। `subprocess.run` output, error, timeout, exit code সব নিয়ন্ত্রণ দেয়।

### Environment variable, argparse ও logging

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **argparse** | **arg**ument **parse**r → কমান্ড লাইনের argument বোঝার লাইব্রেরি |
| **env var** | **env**ironment **var**iable → পরিবেশের variable |
| **CLI** | **C**ommand **L**ine **I**nterface → কমান্ড লাইন ইন্টারফেস |

**সহজ ভাষায়:** Secret (password, token) কোডে লিখবে না; environment variable থেকে পড়ো। argparse দিয়ে স্ক্রিপ্টকে সত্যিকারের CLI টুল বানাও: `--help` নিজে তৈরি হয়। `print`-এর বদলে logging ব্যবহার করো: সময়, level (INFO, WARNING, ERROR) সহ, আর চাইলে ফাইলে।

> **বাস্তব পরিস্থিতি:** একই backup স্ক্রিপ্ট staging আর production দুই জায়গায় চলবে, আর আগে `--dry-run` দিয়ে দেখা যাবে কী হবে। DB password আসবে CI-এর secret থেকে environment variable হয়ে।

```python
import argparse, logging, os, sys

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s",
)
log = logging.getLogger("backup")

def main() -> int:
    p = argparse.ArgumentParser(description="Backup database to S3")
    p.add_argument("--env", choices=["staging", "prod"], required=True)
    p.add_argument("--dry-run", action="store_true")
    p.add_argument("--keep", type=int, default=7, help="days to keep")
    args = p.parse_args()

    password = os.environ.get("DB_PASSWORD")
    if not password:
        log.error("DB_PASSWORD is not set")
        return 2

    log.info("env=%s keep=%d dry_run=%s", args.env, args.keep, args.dry_run)
    if args.dry_run:
        log.warning("dry run: nothing uploaded")
        return 0
    # ... backup কাজ ...
    return 0

if __name__ == "__main__":
    sys.exit(main())

# চালানো: DB_PASSWORD=xxx python3 backup.py --env prod --keep 14
# python3 backup.py --help
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `logging.basicConfig(level=..., format=...)` | log-এর level আর format ঠিক করো |
| `%(asctime)s %(levelname)s` | সময় আর level (INFO/WARNING/ERROR) প্রতিটি লাইনে |
| `log.info("env=%s", args.env)` | `%` ফরম্যাট lazily হয়, level বন্ধ থাকলে খরচ নেই |
| `add_argument("--env", choices=..., required=True)` | `--env` দিতেই হবে, শুধু এই মানগুলো চলবে |
| `action="store_true"` | `--dry-run` দিলে True, না দিলে False (flag) |
| `type=int, default=7` | সংখ্যায় রূপান্তর, না দিলে 7 |
| `os.environ.get("DB_PASSWORD")` | environment variable পড়ো, না থাকলে None |
| `return 2` / `sys.exit(main())` | `main()`-এর ফল exit code হিসেবে ফেরত |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Secret কোডে না রেখে কোথায় রাখবে?
**উত্তর:** Environment variable, secret manager (AWS Secrets Manager, Vault), বা CI-এর secret store-এ; কখনো Git-এ না।

**প্রশ্ন:** `print` আর logging-এর পার্থক্য?
**উত্তর:** logging-এ level, সময়, কোন module, ফাইল/syslog-এ পাঠানো, আর level দিয়ে চালু/বন্ধ করা যায়। Production স্ক্রিপ্টে logging ব্যবহার করো।

---

## ০৩. ডেটা ফরম্যাট: JSON, YAML, CSV ও Regex

API JSON দেয়, Kubernetes ও Ansible YAML চায়, রিপোর্ট CSV-তে যায়, আর log থেকে তথ্য বের করতে regex।

### JSON ও YAML

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **JSON** | **J**ava**S**cript **O**bject **N**otation → data বিনিময়ের লেখার ফরম্যাট |
| **YAML** | **Y**AML **A**in't **M**arkup **L**anguage → মানুষের পড়ার মতো config ফরম্যাট |

**সহজ ভাষায়:** JSON আর YAML দুটোই dict/list-এর মতো গঠন রাখে। Python-এ `json` লাইব্রেরি built-in; YAML-এর জন্য `pip install pyyaml`। YAML পড়তে সবসময় `yaml.safe_load` ব্যবহার করো, `yaml.load` নয়, কারণ অবিশ্বস্ত YAML থেকে কোড চলতে পারে।

> **বাস্তব পরিস্থিতি:** একটা Kubernetes `deployment.yaml`-তে সব environment-এর জন্য image tag বদলাতে হবে CI pipeline-এ, আর ফলাফলের একটা JSON রিপোর্ট বানাতে হবে।

```python
import json, yaml

with open("deployment.yaml") as f:
    dep = yaml.safe_load(f)

container = dep["spec"]["template"]["spec"]["containers"][0]
old = container["image"]
container["image"] = "registry.local/myapp:2.4.1"

with open("deployment.yaml", "w") as f:
    yaml.safe_dump(dep, f, sort_keys=False)

report = {"file": "deployment.yaml", "old": old, "new": container["image"]}
print(json.dumps(report, indent=2))
with open("report.json", "w") as f:
    json.dump(report, f)

data = json.loads('{"status": "ok", "nodes": 3}')
print(data["nodes"])
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `yaml.safe_load(f)` | YAML ফাইল নিরাপদে পড়ে dict/list বানাও |
| `dep["spec"]["template"]...[0]` | nested গঠনে ঢুকে প্রথম container |
| `yaml.safe_dump(..., sort_keys=False)` | আবার YAML হিসেবে লেখো, key-এর ক্রম না বদলে |
| `json.dumps(obj, indent=2)` | dump to string: সুন্দর করে JSON লেখা বানাও |
| `json.dump(obj, f)` | সরাসরি ফাইলে লেখো |
| `json.loads(text)` | load from string: JSON লেখা থেকে dict |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `json.load` আর `json.loads`-এর পার্থক্য?
**উত্তর:** `load` ফাইল object থেকে পড়ে, `loads` (load string) একটা string থেকে।

**প্রশ্ন:** `yaml.load` কেন বিপজ্জনক?
**উত্তর:** পুরনো full loader YAML tag দিয়ে যেকোনো Python object বানাতে পারত, ফলে কোড execution সম্ভব। `safe_load` শুধু সাধারণ type বানায়।

### CSV ও রিপোর্ট

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CSV** | **C**omma-**S**eparated **V**alues → কমা দিয়ে আলাদা মান |

**সহজ ভাষায়:** Inventory, cost রিপোর্ট বা audit ম্যানেজারকে দিতে CSV সবচেয়ে সহজ, Excel-এ খোলা যায়। `csv.DictReader` প্রতিটি লাইনকে header অনুযায়ী dict বানায়।

> **বাস্তব পরিস্থিতি:** প্রতি মাসে cloud bill-এর CSV থেকে service অনুযায়ী মোট খরচ বের করে ম্যানেজারকে নতুন CSV পাঠাতে হয়।

```python
import csv
from collections import defaultdict

totals = defaultdict(float)
with open("billing.csv", newline="") as f:
    for row in csv.DictReader(f):
        totals[row["service"]] += float(row["cost"])

with open("summary.csv", "w", newline="") as f:
    w = csv.writer(f)
    w.writerow(["service", "total_usd"])
    for svc, cost in sorted(totals.items(), key=lambda x: x[1], reverse=True):
        w.writerow([svc, round(cost, 2)])
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `csv.DictReader(f)` | header দেখে প্রতিটি লাইন dict হয় |
| `defaultdict(float)` | না থাকা key-তে নিজে 0.0 দিয়ে শুরু করে |
| `newline=""` | csv মডিউলের জন্য দরকার, নইলে Windows-এ ফাঁকা লাইন আসে |
| `csv.writer` / `writerow` | লাইন হিসেবে লেখো |
| `sorted(..., key=lambda x: x[1], reverse=True)` | খরচ (দ্বিতীয় মান) ধরে বড় থেকে ছোট সাজাও; lambda = ছোট নামহীন function |

### Regex দিয়ে log বিশ্লেষণ

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **regex** | **reg**ular **ex**pression → লেখার প্যাটার্ন খোঁজার ভাষা |
| **re** | **R**egular **E**xpression module → Python-এর regex লাইব্রেরি |

**সহজ ভাষায়:** Regex দিয়ে লেখার ভেতর থেকে নির্দিষ্ট প্যাটার্ন (IP, status code, সময়) বের করা যায়। Group `( )` দিয়ে আলাদা অংশ ধরা হয়, named group `(?P<name>...)` দিয়ে নাম দিয়ে পাওয়া যায়। প্যাটার্ন `r"..."` (raw string)-এ লেখো, যাতে backslash ঠিক থাকে।

> **বাস্তব পরিস্থিতি:** Nginx access log থেকে কোন IP সবচেয়ে বেশি 5xx error পাচ্ছে আর কোন path সবচেয়ে বেশি 404 দিচ্ছে, তার রিপোর্ট।

```python
import re
from collections import Counter

pattern = re.compile(
    r'(?P<ip>\d+\.\d+\.\d+\.\d+) .*? "(?P<method>\w+) (?P<path>\S+) [^"]*" (?P<status>\d{3})'
)
errors, not_found = Counter(), Counter()

with open("/var/log/nginx/access.log") as f:
    for line in f:
        m = pattern.search(line)
        if not m:
            continue
        status = int(m["status"])
        if status >= 500:
            errors[m["ip"]] += 1
        elif status == 404:
            not_found[m["path"]] += 1

print("Top 5xx IPs:", errors.most_common(5))
print("Top 404 paths:", not_found.most_common(5))
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `re.compile(r'...')` | প্যাটার্ন একবার compile করো, লুপে বারবার দ্রুত ব্যবহার; `r` = raw string |
| `(?P<ip>...)` | named group: পরে `m["ip"]` দিয়ে পাওয়া যাবে |
| `\d+\.\d+` | `\d` = digit, `+` = এক বা বেশি, `\.` = আসল ডট |
| `.*?` | যেকোনো কিছু, যত কম সম্ভব (non-greedy) |
| `\w+` / `\S+` | `\w` = অক্ষর/সংখ্যা (GET), `\S` = space ছাড়া যেকোনো (path) |
| `\d{3}` | ঠিক ৩টা digit (status code) |
| `pattern.search(line)` | লাইনের যেকোনো জায়গায় মিল খোঁজো; না পেলে None |
| `Counter()` / `most_common(5)` | গণনা করার dict; সবচেয়ে বেশি ৫টা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `re.match` আর `re.search`-এর পার্থক্য?
**উত্তর:** `match` শুধু লেখার শুরু থেকে মেলায়, `search` যেকোনো জায়গায় খোঁজে।

**প্রশ্ন:** Greedy আর non-greedy-এর পার্থক্য?
**উত্তর:** `.*` যত বেশি সম্ভব নেয় (greedy), `.*?` যত কম সম্ভব নেয়।

> 💡 **প্রো টিপ:** জটিল log-এ regex-এর বদলে আগে দেখো format JSON log করা যায় কিনা; তখন `json.loads` দিয়ে সহজে পার্স হয়।

---

## ০৪. Python দিয়ে API, নেটওয়ার্ক ও SSH অটোমেশন

প্রায় সব আধুনিক টুলের REST API আছে; আর পুরনো সার্ভার ও নেটওয়ার্ক যন্ত্রে SSH দিয়ে কাজ।

### requests: REST API call

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **REST** | **Re**presentational **S**tate **T**ransfer → HTTP ভিত্তিক API-এর ধরন |
| **API** | **A**pplication **P**rogramming **I**nterface → প্রোগ্রাম থেকে প্রোগ্রামে কথা বলার নিয়ম |
| **HTTP** | **H**yper**T**ext **T**ransfer **P**rotocol → ওয়েবের protocol |
| **token** | access token → পরিচয়পত্রের মতো গোপন চাবি |

**সহজ ভাষায়:** `requests` লাইব্রেরি দিয়ে GET, POST সহজে করা যায়। Production স্ক্রিপ্টে সবসময় timeout দাও (নইলে চিরকাল আটকে থাকতে পারে), `raise_for_status()` দিয়ে HTTP error ধরো, আর অস্থায়ী ব্যর্থতায় retry করো।

> **বাস্তব পরিস্থিতি:** Deploy শেষে GitHub API দিয়ে release তৈরি করে, তারপর Slack webhook-এ খবর পাঠাতে হবে। নেটওয়ার্ক মাঝে মাঝে ঝামেলা করে, তাই 5xx-এ ৩ বার retry।

```python
import os, requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()
retry = Retry(total=3, backoff_factor=1, status_forcelist=[502, 503, 504])
session.mount("https://", HTTPAdapter(max_retries=retry))
session.headers["Authorization"] = f"Bearer {os.environ['GITHUB_TOKEN']}"

r = session.get("https://api.github.com/repos/acme/app/releases/latest", timeout=10)
r.raise_for_status()
print("latest:", r.json()["tag_name"])

resp = requests.post(
    os.environ["SLACK_WEBHOOK_URL"],
    json={"text": ":rocket: app v2.4.1 deployed to prod"},
    timeout=5,
)
resp.raise_for_status()
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `requests.Session()` | একই connection আর header বারবার ব্যবহার, দ্রুত |
| `Retry(total=3, backoff_factor=1, ...)` | সর্বোচ্চ ৩ বার, প্রতিবার অপেক্ষা বাড়িয়ে (exponential backoff), শুধু এই status-এ |
| `session.mount("https://", ...)` | সব https request-এ retry নিয়ম লাগাও |
| `Authorization: Bearer ...` | token দিয়ে পরিচয় দাও |
| `timeout=10` | ১০ সেকেন্ডে উত্তর না এলে ছেড়ে দাও; সবসময় দেবে |
| `raise_for_status()` | 4xx/5xx হলে exception তোলো |
| `r.json()` | উত্তরের JSON-কে dict বানাও |
| `json={...}` | body JSON হিসেবে পাঠাও, header-ও ঠিক করে দেয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** requests-এ timeout না দিলে কী হয়?
**উত্তর:** Default-এ কোনো timeout নেই; সার্ভার উত্তর না দিলে স্ক্রিপ্ট চিরকাল অপেক্ষা করতে পারে।

**প্রশ্ন:** Exponential backoff কী?
**উত্তর:** প্রতিবার retry-এর আগে অপেক্ষার সময় দ্বিগুণ করা (1, 2, 4 সেকেন্ড), যাতে সমস্যাগ্রস্ত সার্ভারে চাপ না বাড়ে।

### Paramiko ও Netmiko: SSH অটোমেশন

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SSH** | **S**ecure **Sh**ell → নিরাপদ remote terminal |
| **Paramiko** | para(noid) + miko (Esperanto শব্দ) → Python-এর SSH লাইব্রেরি |
| **Netmiko** | net(work) + (para)miko → নেটওয়ার্ক যন্ত্রের জন্য SSH লাইব্রেরি |

**সহজ ভাষায়:** Paramiko দিয়ে Linux সার্ভারে SSH করে কমান্ড চালানো আর ফাইল কপি করা যায়। Netmiko Paramiko-এর উপর তৈরি, Cisco, Juniper, Arista সহ শত শত নেটওয়ার্ক যন্ত্রের prompt আর পেজিং নিজে সামলায়। অনেক যন্ত্র বা সার্ভারে কাজ হলে Ansible-ও ভালো বিকল্প।

> **বাস্তব পরিস্থিতি:** ২০০টা Cisco switch-এর running-config প্রতি রাতে backup নিতে হবে আর Git-এ রাখতে হবে, যাতে কে কী বদলেছে দেখা যায়।

```python
from netmiko import ConnectHandler
from pathlib import Path
import os

switches = ["10.0.0.11", "10.0.0.12"]
for ip in switches:
    device = {
        "device_type": "cisco_ios",
        "host": ip,
        "username": os.environ["NET_USER"],
        "password": os.environ["NET_PASS"],
    }
    with ConnectHandler(**device) as conn:
        config = conn.send_command("show running-config")
    Path(f"backups/{ip}.cfg").write_text(config)
    print(f"saved {ip}")

# Linux সার্ভারে paramiko
import paramiko
client = paramiko.SSHClient()
client.load_system_host_keys()
client.connect("10.0.1.10", username="ubuntu", key_filename="/home/ci/.ssh/id_ed25519")
_, stdout, _ = client.exec_command("df -h /")
print(stdout.read().decode())
client.close()
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `device_type: cisco_ios` | Netmiko-কে বলো কোন ধরনের যন্ত্র |
| `ConnectHandler(**device)` | `**` = dict-কে keyword argument হিসেবে খুলে দাও |
| `send_command("show ...")` | কমান্ড পাঠিয়ে পুরো output ফেরত আনো |
| `load_system_host_keys()` | `~/.ssh/known_hosts` থেকে চেনা host key লোড, MITM থেকে রক্ষা |
| `key_filename` | password-এর বদলে SSH key দিয়ে login |
| `exec_command` | remote-এ কমান্ড চালাও; stdin, stdout, stderr ফেরত দেয় |
| `_` | যে মান লাগবে না তার জন্য প্রচলিত নাম |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Paramiko-তে AutoAddPolicy কেন সাবধানে ব্যবহার করবে?
**উত্তর:** এটা অচেনা host key কোনো যাচাই ছাড়াই মেনে নেয়, ফলে man-in-the-middle আক্রমণের ঝুঁকি। known_hosts ব্যবহার করাই নিরাপদ।

---

## ০৫. Python দিয়ে Cloud ও DevOps অটোমেশন

বাস্তব DevOps কাজে Python সবচেয়ে বেশি লাগে AWS, Docker, Kubernetes, template আর alert পাঠানোয়।

### boto3: AWS অটোমেশন

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **boto3** | boto (আমাজন নদীর ডলফিন) version 3 → AWS-এর Python SDK |
| **SDK** | **S**oftware **D**evelopment **K**it → সফটওয়্যার বানানোর টুলকিট |
| **EC2** | **E**lastic **C**ompute **C**loud → AWS-এর virtual server |
| **S3** | **S**imple **S**torage **S**ervice → AWS-এর ফাইল স্টোরেজ |
| **IAM** | **I**dentity and **A**ccess **M**anagement → পরিচয় ও অনুমতি ব্যবস্থাপনা |

**সহজ ভাষায়:** boto3 দিয়ে AWS-এর প্রায় সব সার্ভিস কোড থেকে চালানো যায়। Credential কোডে না লিখে AWS CLI profile, environment variable বা EC2/Lambda-এর IAM role থেকে নাও। অনেক ফলাফলের জন্য paginator ব্যবহার করো, কারণ API একবারে সীমিত ফল দেয়।

> **বাস্তব পরিস্থিতি:** খরচ কমাতে: রাত ৮টায় সব dev environment-এর EC2 বন্ধ করো, আর যেসব instance-এ "Owner" tag নেই তাদের তালিকা Slack-এ পাঠাও; পাশাপাশি রাতের backup S3-এ upload।

```python
import boto3

ec2 = boto3.client("ec2", region_name="ap-southeast-1")
paginator = ec2.get_paginator("describe_instances")

to_stop, untagged = [], []
for page in paginator.paginate(
    Filters=[{"Name": "instance-state-name", "Values": ["running"]}]
):
    for res in page["Reservations"]:
        for inst in res["Instances"]:
            tags = {t["Key"]: t["Value"] for t in inst.get("Tags", [])}
            if tags.get("Env") == "dev":
                to_stop.append(inst["InstanceId"])
            if "Owner" not in tags:
                untagged.append(inst["InstanceId"])

if to_stop:
    ec2.stop_instances(InstanceIds=to_stop)
print("stopped:", to_stop)
print("untagged:", untagged)

s3 = boto3.client("s3")
s3.upload_file("/backup/db.sql.gz", "acme-backups", "db/2026-10-06.sql.gz")
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `boto3.client("ec2", region_name=...)` | নির্দিষ্ট region-এর EC2 API-এর client |
| `get_paginator("describe_instances")` | সব পাতা (page) নিজে নিজে আনবে |
| `Filters=[...]` | শুধু running instance চাও (API স্তরে filter, দ্রুত) |
| `Reservations -> Instances` | AWS-এর উত্তরের গঠন: reservation-এর ভেতরে instance |
| `{t["Key"]: t["Value"] for t in ...}` | dict comprehension: tag list থেকে সহজ dict |
| `inst.get("Tags", [])` | tag না থাকলে খালি list |
| `stop_instances(InstanceIds=...)` | একসাথে সব বন্ধ করো |
| `upload_file(local, bucket, key)` | ফাইল S3 bucket-এ এই নামে (key) তোলো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** boto3-এ client আর resource-এর পার্থক্য?
**উত্তর:** client নিম্ন-স্তরের, সরাসরি AWS API-এর মতো dict ফেরত দেয়, সব সার্ভিসে আছে। resource উচ্চ-স্তরের object-oriented, তবে নতুন ফিচার আর আসছে না; client ব্যবহার করাই এখন পরামর্শ।

**প্রশ্ন:** Credential কোথা থেকে নেওয়া উচিত?
**উত্তর:** IAM role (EC2 instance profile, Lambda role, EKS IRSA) সবচেয়ে নিরাপদ; local-এ AWS CLI profile বা SSO। কোডে access key কখনো না।

### Docker ও Kubernetes Python client

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SDK** | **S**oftware **D**evelopment **K**it → সফটওয়্যার টুলকিট |
| **kubeconfig** | Kubernetes configuration → cluster-এ যুক্ত হওয়ার ফাইল |
| **Pod** | (তিমির দল) → Kubernetes-এর সবচেয়ে ছোট চলমান একক |

**সহজ ভাষায়:** `docker` লাইব্রেরি দিয়ে container চালানো, তালিকা, পরিষ্কার করা যায়; `kubernetes` লাইব্রেরি kubectl-এর মতো সব কাজ করতে পারে: pod দেখা, deployment scale করা, event পড়া।

> **বাস্তব পরিস্থিতি:** প্রতিদিন সকালে সব namespace-এ CrashLoopBackOff বা অনেকবার restart হওয়া pod খুঁজে রিপোর্ট দাও; আর CI runner-এ পুরনো বন্ধ container পরিষ্কার করো।

```python
from kubernetes import client, config

config.load_kube_config()       # cluster-এর ভেতরে: config.load_incluster_config()
v1 = client.CoreV1Api()

for pod in v1.list_pod_for_all_namespaces().items:
    for cs in pod.status.container_statuses or []:
        waiting = cs.state.waiting
        if cs.restart_count > 5 or (waiting and waiting.reason == "CrashLoopBackOff"):
            print(pod.metadata.namespace, pod.metadata.name, cs.restart_count)

apps = client.AppsV1Api()
apps.patch_namespaced_deployment_scale(
    name="web", namespace="prod", body={"spec": {"replicas": 5}}
)

import docker
dc = docker.from_env()
for c in dc.containers.list(all=True, filters={"status": "exited"}):
    c.remove()
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `config.load_kube_config()` | `~/.kube/config` থেকে cluster-এর তথ্য নাও |
| `load_incluster_config()` | pod-এর ভেতর থেকে চললে ServiceAccount দিয়ে যুক্ত হও |
| `CoreV1Api` / `AppsV1Api` | pod, service (core) আর deployment (apps)-এর API |
| `container_statuses or []` | None হলে খালি list, যাতে loop-এ error না হয় |
| `patch_namespaced_deployment_scale` | `kubectl scale deployment web --replicas=5`-এর সমান |
| `docker.from_env()` | DOCKER_HOST বা local socket দিয়ে Docker-এ যুক্ত হও |
| `containers.list(all=True, filters=...)` | বন্ধ সহ সব, শুধু exited গুলো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Pod-এর ভেতর থেকে Python Kubernetes API-তে কীভাবে যুক্ত হয়?
**উত্তর:** `load_incluster_config()` ServiceAccount token আর cluster CA ব্যবহার করে; প্রয়োজনীয় অনুমতি RBAC (Role/RoleBinding) দিয়ে দিতে হয়।

### Jinja2 template দিয়ে config বানানো

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Jinja2** | Jinja (জাপানি মন্দির) version 2 → template ইঞ্জিন |

**সহজ ভাষায়:** একই রকম config (nginx, switch config, Kubernetes manifest) অনেকগুলো বানাতে হলে template লিখে variable বসাও। Ansible-ও ভেতরে Jinja2 ব্যবহার করে, তাই এটা শেখা দুই জায়গাতেই কাজে লাগে।

> **বাস্তব পরিস্থিতি:** ২০টা microservice-এর জন্য nginx-এর upstream config বানাতে হবে, প্রতিটির নাম, port আর server আলাদা।

```python
from jinja2 import Template

tpl = Template("""
{% for svc in services %}
upstream {{ svc.name }} {
{% for s in svc.servers %}    server {{ s }}:{{ svc.port }};
{% endfor %}}
{% endfor %}
""")

services = [
    {"name": "users",  "port": 8001, "servers": ["10.0.1.10", "10.0.1.11"]},
    {"name": "orders", "port": 8002, "servers": ["10.0.1.12"]},
]
print(tpl.render(services=services))
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `{{ ... }}` | এখানে variable-এর মান বসবে |
| `{% for ... %} ... {% endfor %}` | template-এর ভেতরে loop |
| `svc.name` | dict-এর key-কে ডট দিয়েও পাওয়া যায় |
| `tpl.render(services=...)` | data দিয়ে template পূরণ করে শেষ লেখা বানাও |

---

## ০৬. Python: ভালো অনুশীলন, টেস্টিং ও Concurrency

স্ক্রিপ্ট থেকে নির্ভরযোগ্য টুল বানাতে: dependency, টেস্ট, একসাথে অনেক কাজ আর কোডের মান।

### Concurrency: একসাথে অনেক সার্ভার

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **GIL** | **G**lobal **I**nterpreter **L**ock → একসাথে একটাই thread Python কোড চালাতে পারে এমন তালা |
| **I/O** | **I**nput/**O**utput → নেটওয়ার্ক, ডিস্ক |
| **asyncio** | **async**hronous **I/O** → অ্যাসিঙ্ক্রোনাস প্রোগ্রামিং |

**সহজ ভাষায়:** ৫০০টা সার্ভারে একটা একটা করে health check করলে, প্রতিটিতে ১ সেকেন্ড হলে ৮ মিনিট। কাজগুলো বেশিরভাগ সময় নেটওয়ার্কের অপেক্ষায় (I/O-bound), তাই thread দিয়ে একসাথে করলে কয়েক সেকেন্ডে শেষ। GIL-এর কারণে CPU-bound কাজে thread লাভ দেয় না; তখন multiprocessing লাগে।

> **বাস্তব পরিস্থিতি:** ৫০০টা endpoint-এর HTTPS certificate কবে শেষ হবে তা একসাথে ৫০টা করে পরীক্ষা করে রিপোর্ট।

```python
from concurrent.futures import ThreadPoolExecutor, as_completed
import requests

urls = [f"https://svc{i}.internal/health" for i in range(500)]

def check(url: str) -> tuple[str, int | str]:
    try:
        return url, requests.get(url, timeout=5).status_code
    except requests.RequestException as e:
        return url, type(e).__name__

with ThreadPoolExecutor(max_workers=50) as pool:
    futures = [pool.submit(check, u) for u in urls]
    for fut in as_completed(futures):
        url, result = fut.result()
        if result != 200:
            print("BAD", url, result)
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `ThreadPoolExecutor(max_workers=50)` | সর্বোচ্চ ৫০টা thread একসাথে |
| `pool.submit(check, u)` | কাজটা pool-এ জমা দাও, একটা future (ভবিষ্যতের ফল) ফেরত পাও |
| `as_completed(futures)` | যেটা আগে শেষ হয় সেটা আগে দাও |
| `fut.result()` | ঐ কাজের return মান |
| `int \| str` | type hint: int অথবা str (Python 3.10+) |
| `type(e).__name__` | error-এর ধরনের নাম, যেমন ConnectTimeout |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** GIL কী আর এটা অটোমেশনে কতটা সমস্যা?
**উত্তর:** CPython-এ একসাথে একটাই thread Python bytecode চালাতে পারে। নেটওয়ার্ক/ডিস্ক অপেক্ষার কাজে thread অপেক্ষার সময় GIL ছেড়ে দেয়, তাই অটোমেশনে সমস্যা কম। CPU-ভারী কাজে multiprocessing বা অন্য ভাষা (Go) ভালো।

**প্রশ্ন:** Thread, multiprocessing আর asyncio কখন?
**উত্তর:** I/O-bound আর সহজ: thread। CPU-bound: multiprocessing। হাজারো একসাথে নেটওয়ার্ক সংযোগ আর async লাইব্রেরি (aiohttp) থাকলে: asyncio।

### pytest দিয়ে টেস্ট, lint ও packaging

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **pytest** | **Py**thon **test** → Python-এর জনপ্রিয় টেস্ট ফ্রেমওয়ার্ক |
| **CI** | **C**ontinuous **I**ntegration → প্রতিটি পরিবর্তনে স্বয়ংক্রিয় build/test |
| **lint** | (কাপড়ের আঁশ) → কোডের ছোটখাটো ভুল ও স্টাইল ধরা |

**সহজ ভাষায়:** অটোমেশন স্ক্রিপ্ট production-এ ভুল করলে বড় ক্ষতি (ভুল সার্ভার মুছে ফেলা)। তাই মূল logic-কে ছোট function-এ ভাগ করে pytest দিয়ে টেস্ট করো। ruff/black দিয়ে lint আর format, mypy দিয়ে type check, আর সব CI-তে চালাও।

> **বাস্তব পরিস্থিতি:** Disk ব্যবহার থেকে alert level বের করার function ভুল করে ৯০%-কে WARNING দিচ্ছিল। একটা ছোট টেস্ট থাকলে merge-এর আগেই ধরা পড়ত।

```python
# alerts.py
def level(used: int) -> str:
    if used >= 90:
        return "CRITICAL"
    if used >= 80:
        return "WARNING"
    return "OK"
```

```python
# test_alerts.py
import pytest
from alerts import level

@pytest.mark.parametrize("used,expected", [
    (50, "OK"),
    (80, "WARNING"),
    (89, "WARNING"),
    (90, "CRITICAL"),
])
def test_level(used, expected):
    assert level(used) == expected
```

```bash
# টার্মিনালে:
pip install pytest ruff mypy
pytest -q
ruff check . && ruff format .
mypy alerts.py
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `test_*.py` / `def test_*` | pytest এই নামের ফাইল আর function নিজে খুঁজে চালায় |
| `@pytest.mark.parametrize` | একই টেস্ট অনেকগুলো ইনপুটে চালাও (table-driven) |
| `assert` | সত্য না হলে টেস্ট fail |
| `pytest -q` | `-q` = quiet, সংক্ষেপে ফল |
| `ruff check` / `ruff format` | lint (ভুল খোঁজা) আর format (স্টাইল ঠিক করা), খুব দ্রুত |
| `mypy` | type hint দেখে type ভুল ধরা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Boundary value test কেন জরুরি?
**উত্তর:** ভুল বেশিরভাগ সীমানায় হয় (`>=` বনাম `>`); তাই 79, 80, 89, 90-এর মতো সীমার মান টেস্টে রাখো।

> 💡 **প্রো টিপ:** ঝুঁকিপূর্ণ স্ক্রিপ্টে (মোছা, বন্ধ করা) সবসময় `--dry-run` রাখো আর default হিসেবে dry-run চালু রাখার কথা ভাবো।

---

# Part B: Go

## ০৭. Go বেসিক: ভাষার ভিত্তি

Go ছোট আর কড়া ভাষা; নিয়মগুলো একবার বুঝলে কোড সবার একই রকম দেখায়, পড়া সহজ হয়।

### প্রথম প্রোগ্রাম, package ও import

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **package main** | এই package থেকে executable তৈরি হয় |
| **func** | **func**tion → function |
| **fmt** | **f**or**m**a**t** → লেখা ছাপানো ও ফরম্যাট করার package |
| **gofmt** | Go format → কোড নিজে সাজানোর টুল |

**সহজ ভাষায়:** প্রতিটি Go ফাইল একটা package-এর অংশ। `package main` আর তার `func main()` থেকে প্রোগ্রাম শুরু হয়। ব্যবহার না করা import বা variable থাকলে Go compile-ই করবে না; এতে কোড পরিষ্কার থাকে। বড় হাতের অক্ষরে শুরু নাম (`Println`) package-এর বাইরে দেখা যায় (exported), ছোট হাতের গুলো ভেতরে সীমিত।

> **বাস্তব পরিস্থিতি:** টিমের পাঁচজন পাঁচ রকম স্টাইলে কোড লেখে, review-এ শুধু space নিয়ে ঝগড়া। Go-তে gofmt সবার কোড একই রকম করে দেয়, তর্কের বিষয়ই থাকে না।

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	host, _ := os.Hostname()
	fmt.Printf("Hello from %s\n", host)
	fmt.Println("args:", os.Args[1:])
}

// চালাও: go run main.go web prod
// বানাও: go build -o hello . && ./hello
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `package main` | executable প্রোগ্রামের package |
| `import ( ... )` | একসাথে কয়েকটা package আনো |
| `func main()` | প্রোগ্রাম এখান থেকে শুরু |
| `host, _ := os.Hostname()` | `:=` নতুন variable বানাও ও মান দাও; `_` মানে দ্বিতীয় মান (error) লাগবে না (ভালো অভ্যাস না, শুধু উদাহরণ) |
| `fmt.Printf("%s\n", ...)` | ফরম্যাট করে ছাপো: `%s` = string, `%d` = সংখ্যা, `%v` = যেকোনো মান |
| `os.Args[1:]` | কমান্ড লাইনের argument, প্রথমটা (প্রোগ্রামের নাম) বাদ দিয়ে |
| `// ...` | comment |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Go-তে exported আর unexported কীভাবে ঠিক হয়?
**উত্তর:** নামের প্রথম অক্ষর বড় হাতের হলে exported (অন্য package থেকে ব্যবহারযোগ্য), ছোট হাতের হলে শুধু নিজের package-এ।

**প্রশ্ন:** অব্যবহৃত import থাকলে কী হয়?
**উত্তর:** Compile error। Go এটা ইচ্ছা করেই কড়া রাখে।

### Variable, type, slice ও map

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **slice** | পরিবর্তনশীল দৈর্ঘ্যের তালিকা |
| **map** | key-value সংগ্রহ |
| **struct** | **struct**ure → নিজের data type, field-এর সমষ্টি |
| **zero value** | মান না দিলে default মান |

**সহজ ভাষায়:** Go-তে প্রতিটি variable-এর type নির্দিষ্ট। `var` দিয়ে বা `:=` দিয়ে বানানো যায়। মান না দিলে zero value পায় (int-এ 0, string-এ "", bool-এ false, pointer/slice/map-এ nil)। Python-এর list-এর মতো Go-তে slice, dict-এর মতো map, আর class-এর বদলে struct।

> **বাস্তব পরিস্থিতি:** একটা inventory টুলে প্রতিটি সার্ভারের নাম, IP, port আর tag রাখতে হবে, আর environment অনুযায়ী গোনা দরকার।

```go
type Server struct {
	Name string
	IP   string
	Port int
	Tags map[string]string
}

func main() {
	var retries int = 3
	timeout := 5.0 // float64 অনুমান

	servers := []Server{
		{Name: "web-01", IP: "10.0.1.10", Port: 80, Tags: map[string]string{"env": "prod"}},
		{Name: "dev-01", IP: "10.0.9.5", Port: 80, Tags: map[string]string{"env": "dev"}},
	}
	servers = append(servers, Server{Name: "db-01", IP: "10.0.2.5", Port: 5432})

	count := map[string]int{}
	for i, s := range servers {
		env, ok := s.Tags["env"]
		if !ok {
			env = "unknown"
		}
		count[env]++
		fmt.Println(i, s.Name, env)
	}
	fmt.Println(count, len(servers), retries, timeout)
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `type Server struct { ... }` | নিজের type: কয়েকটা field একসাথে |
| `var retries int = 3` | type বলে variable; package স্তরেও ব্যবহারযোগ্য |
| `timeout := 5.0` | short declaration, type নিজে বুঝে নেয় (শুধু function-এর ভেতরে) |
| `[]Server{ ... }` | Server-এর slice |
| `map[string]string{...}` | string key থেকে string value-এর map |
| `append(servers, ...)` | slice-এ যোগ করে নতুন slice ফেরত দেয়, তাই আবার assign করতে হয় |
| `for i, s := range servers` | index আর মান সহ loop (Go-তে লুপ শুধু `for`) |
| `env, ok := s.Tags["env"]` | "comma ok": key আছে কিনা `ok`-তে জানা যায় |
| `count[env]++` | না থাকলে zero value 0 থেকে বাড়ে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Array আর slice-এর পার্থক্য?
**উত্তর:** Array-এর দৈর্ঘ্য নির্দিষ্ট আর type-এর অংশ (`[5]int`)। Slice পরিবর্তনশীল, ভেতরে একটা array-কে নির্দেশ করে (pointer, length, capacity)। বাস্তবে প্রায় সবসময় slice।

**প্রশ্ন:** nil map-এ লিখলে কী হয়?
**উত্তর:** Panic। map ব্যবহারের আগে `make(map[string]int)` বা map literal দিয়ে তৈরি করতে হয়।

### Function, একাধিক return ও error handling

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **error** | error interface → ভুলের মান |
| **nil** | (কিছুই না) → শূন্য/খালি মান |
| **defer** | (পিছিয়ে দাও) → function শেষে চালাও |
| **panic** | অপ্রত্যাশিত গুরুতর ভুলে প্রোগ্রাম থামানো |

**সহজ ভাষায়:** Go-তে exception নেই। Function সাধারণত শেষ return মান হিসেবে error দেয়, আর caller প্রতিবার `if err != nil` দিয়ে পরীক্ষা করে। দেখতে লম্বা লাগে, কিন্তু প্রতিটি ভুল কোথায় সামলানো হলো স্পষ্ট বোঝা যায়। `fmt.Errorf`-এ `%w` দিয়ে error মোড়ালে মূল কারণ হারায় না। `defer` দিয়ে ফাইল/connection বন্ধ নিশ্চিত করা হয়।

> **বাস্তব পরিস্থিতি:** Config ফাইল পড়ার function ফাইল না পেলে বা ভুল JSON হলে আলাদা স্পষ্ট মেসেজ দেবে, যাতে log দেখেই বোঝা যায় কোন ফাইলে কী সমস্যা।

```go
import (
	"encoding/json"
	"errors"
	"fmt"
	"os"
)

type Config struct {
	Region  string `json:"region"`
	Retries int    `json:"retries"`
}

func loadConfig(path string) (*Config, error) {
	f, err := os.Open(path)
	if err != nil {
		return nil, fmt.Errorf("open config %s: %w", path, err)
	}
	defer f.Close()

	var cfg Config
	if err := json.NewDecoder(f).Decode(&cfg); err != nil {
		return nil, fmt.Errorf("parse config %s: %w", path, err)
	}
	if cfg.Retries == 0 {
		cfg.Retries = 3
	}
	return &cfg, nil
}

func main() {
	cfg, err := loadConfig("config.json")
	if errors.Is(err, os.ErrNotExist) {
		fmt.Println("config.json not found, using defaults")
		cfg = &Config{Region: "ap-southeast-1", Retries: 3}
	} else if err != nil {
		fmt.Fprintln(os.Stderr, "error:", err)
		os.Exit(1)
	}
	fmt.Printf("%+v\n", *cfg)
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `(*Config, error)` | দুটো মান ফেরত: config-এর pointer আর error |
| `if err != nil { return ... }` | ভুল হলে সাথে সাথে উপরে পাঠাও; Go-এর মূল রীতি |
| `fmt.Errorf("...: %w", err)` | প্রসঙ্গ যোগ করে error মোড়াও; `%w` = wrap, মূল error রেখে দেয় |
| `defer f.Close()` | function যেভাবেই শেষ হোক, ফাইল বন্ধ হবে |
| `` `json:"region"` `` | struct tag: JSON-এর কোন key এই field-এ যাবে |
| `Decode(&cfg)` | `&` = cfg-এর ঠিকানা (pointer) দাও, যাতে Decode সেটা পূরণ করতে পারে |
| `errors.Is(err, os.ErrNotExist)` | মোড়ানো error-এর ভেতরেও "ফাইল নেই" কিনা দেখো |
| `os.Exit(1)` | exit code 1 দিয়ে বের হও |
| `%+v` | struct ছাপাও field-এর নামসহ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Go-তে exception নেই কেন; error কীভাবে সামলায়?
**উত্তর:** Go ভুলকে সাধারণ মান হিসেবে দেখে, যাতে control flow স্পষ্ট থাকে। Function error ফেরত দেয়, caller `if err != nil` দিয়ে সামলায়। শুধু সত্যিকারের অসম্ভব অবস্থায় panic, আর দরকার হলে recover।

**প্রশ্ন:** defer কখন চলে আর ক্রম কী?
**উত্তর:** Function return করার ঠিক আগে চলে; একাধিক defer হলে শেষেরটা আগে (LIFO = Last In First Out)।

**প্রশ্ন:** Errorf-এ `%w` আর `%v`-এর পার্থক্য?
**উত্তর:** `%w` মূল error-কে wrap করে রাখে, তাই `errors.Is/As` দিয়ে খোঁজা যায়; `%v` শুধু লেখা হিসেবে বসায়।

### Pointer, Method ও Interface

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **pointer** | মানের ঠিকানা |
| **method** | কোনো type-এর সাথে যুক্ত function |
| **interface** | কী কী method থাকতে হবে তার চুক্তি |
| **receiver** | method কোন type-এর |

**সহজ ভাষায়:** Pointer (`*T`) দিয়ে মানের কপি না করে আসলটায় কাজ করা যায়। Struct-এর সাথে method যুক্ত করা যায়। Interface বলে "যে type এই method গুলো রাখে, সে-ই এই interface"; আলাদা করে ঘোষণা করতে হয় না (implicit)। এতে বিভিন্ন implementation সহজে বদলানো যায়, টেস্টে fake বসানো যায়।

> **বাস্তব পরিস্থিতি:** Alert পাঠানোর টুল Slack, Email, PagerDuty তিন জায়গায় পাঠাবে। সবগুলোর একটা `Send()` method থাকলে মূল কোড `Notifier` interface নিয়ে কাজ করে, কোথায় যাচ্ছে জানতে হয় না। টেস্টে একটা fake notifier দিলেই হয়।

```go
type Notifier interface {
	Send(msg string) error
}

type Slack struct{ WebhookURL string }

func (s Slack) Send(msg string) error {
	fmt.Println("slack:", msg) // আসলে HTTP POST
	return nil
}

type Counter struct{ n int }

func (c *Counter) Inc() { c.n++ } // pointer receiver: আসলটা বদলায়

func alertAll(ns []Notifier, msg string) {
	for _, n := range ns {
		if err := n.Send(msg); err != nil {
			fmt.Println("send failed:", err)
		}
	}
}

func main() {
	alertAll([]Notifier{Slack{WebhookURL: "https://hooks..."}}, "disk 95% on web-01")
	c := &Counter{}
	c.Inc()
	c.Inc()
	fmt.Println(c.n) // 2
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `type Notifier interface { Send(...) error }` | যার Send method আছে সে-ই Notifier |
| `func (s Slack) Send(...)` | Slack type-এর method; `(s Slack)` হলো receiver |
| `func (c *Counter) Inc()` | pointer receiver: c-এর আসল মান বদলায়, কপি না |
| `[]Notifier{Slack{...}}` | Slack-কে আলাদা ঘোষণা ছাড়াই Notifier হিসেবে ব্যবহার (implicit) |
| `c := &Counter{}` | `&` = নতুন Counter-এর pointer |
| `for _, n := range ns` | `_` = index লাগবে না |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Value receiver আর pointer receiver কখন?
**উত্তর:** Method যদি মান বদলায়, বা struct বড় (কপি ব্যয়বহুল), বা `sync.Mutex`-এর মতো কপি করা যায় না এমন field থাকে: pointer receiver। ছোট, অপরিবর্তনীয় type-এ value receiver।

**প্রশ্ন:** Go interface implicit বলতে কী বোঝায়?
**উত্তর:** কোনো type-কে "implements" লিখতে হয় না; প্রয়োজনীয় method থাকলেই সে interface পূরণ করে। এতে package গুলো কম নির্ভরশীল থাকে।

---

## ০৮. Go Concurrency: goroutine, channel, context

Go-এর সবচেয়ে বড় শক্তি: একসাথে হাজারো সার্ভার চেক, লাখো request সামলানো: সহজে আর নিরাপদে।

### Goroutine ও WaitGroup

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **goroutine** | go + routine → Go runtime-এর খুব হালকা thread |
| **WaitGroup** | wait group → সবাই শেষ হওয়ার অপেক্ষা |
| **Mutex** | **Mut**ual **Ex**clusion → একসাথে একজনই প্রবেশ করবে এমন তালা |

**সহজ ভাষায়:** যেকোনো function-এর আগে `go` লিখলে সেটা আলাদা goroutine-এ একসাথে চলে। একটা goroutine-এর শুরুতে মাত্র কয়েক KB memory লাগে, তাই লাখো চালানো যায়। `sync.WaitGroup` দিয়ে সবগুলো শেষ হওয়া পর্যন্ত অপেক্ষা করা হয়। একাধিক goroutine একই data বদলালে `sync.Mutex` দিয়ে রক্ষা করতে হয়, নইলে race condition।

> **বাস্তব পরিস্থিতি:** ১০০০টা সার্ভারের port 22 খোলা কিনা দেখতে হবে। একটা একটা করে করলে (প্রতিটি ২ সেকেন্ড timeout) ৩০ মিনিট পর্যন্ত লাগতে পারে; goroutine দিয়ে কয়েক সেকেন্ডে।

```go
import (
	"fmt"
	"net"
	"sync"
	"time"
)

func main() {
	hosts := []string{"10.0.1.10", "10.0.1.11", "10.0.1.12"}
	var (
		wg   sync.WaitGroup
		mu   sync.Mutex
		down []string
	)
	for _, h := range hosts {
		wg.Add(1)
		go func(host string) {
			defer wg.Done()
			conn, err := net.DialTimeout("tcp", host+":22", 2*time.Second)
			if err != nil {
				mu.Lock()
				down = append(down, host)
				mu.Unlock()
				return
			}
			conn.Close()
		}(h)
	}
	wg.Wait()
	fmt.Println("down:", down)
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `wg.Add(1)` | আরও একটা কাজ বাকি আছে জানাও |
| `go func(host string){...}(h)` | নামহীন function-কে goroutine হিসেবে চালাও; `h`-কে argument হিসেবে দাও |
| `defer wg.Done()` | কাজ শেষে গণনা এক কমাও |
| `net.DialTimeout("tcp", addr, 2*time.Second)` | ২ সেকেন্ডের মধ্যে TCP সংযোগের চেষ্টা |
| `mu.Lock()` / `mu.Unlock()` | একসাথে একটাই goroutine `down` slice বদলাবে |
| `wg.Wait()` | সব Done না হওয়া পর্যন্ত এখানে অপেক্ষা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Goroutine আর OS thread-এর পার্থক্য?
**উত্তর:** Goroutine Go runtime পরিচালনা করে, শুরুতে ছোট stack (কয়েক KB) যা দরকার মতো বাড়ে, আর অনেক goroutine অল্প কিছু OS thread-এ ভাগ করে চলে (M:N scheduling)। তাই লাখো goroutine চালানো যায়; OS thread-এ সেটা অসম্ভব।

**প্রশ্ন:** Race condition কীভাবে ধরবে?
**উত্তর:** `go run -race` বা `go test -race` দিয়ে race detector একই memory-তে অরক্ষিত একসাথে লেখা/পড়া ধরে।

> 💡 **প্রো টিপ:** অসীম goroutine চালু না করে একটা সীমা রাখো (worker pool বা semaphore), নইলে ১০ লাখ host-এ একসাথে সংযোগ দিয়ে নিজের মেশিনের file descriptor শেষ করে ফেলবে।

### Channel ও Worker pool

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **channel** | goroutine-দের মধ্যে data পাঠানোর নল |
| **buffered channel** | কিছু মান জমিয়ে রাখতে পারে এমন channel |
| **select** | একাধিক channel থেকে যেটা আগে প্রস্তুত |

**সহজ ভাষায়:** Go-এর নীতি: "memory শেয়ার করে কথা বলো না, কথা বলে memory শেয়ার করো"। Channel দিয়ে একটা goroutine অন্যটাকে data পাঠায়। Worker pool প্যাটার্নে কয়েকটা worker একটা jobs channel থেকে কাজ নেয় আর results channel-এ ফল দেয়, ফলে একসাথে চলা কাজের সংখ্যা নিয়ন্ত্রণে থাকে।

> **বাস্তব পরিস্থিতি:** ১০,০০০ URL-এর health check, কিন্তু একসাথে সর্বোচ্চ ৫০টা, যাতে নিজের নেটওয়ার্ক বা টার্গেট সার্ভার চাপে না পড়ে।

```go
type result struct {
	url    string
	status int
	err    error
}

func worker(client *http.Client, jobs <-chan string, results chan<- result) {
	for url := range jobs {
		resp, err := client.Get(url)
		if err != nil {
			results <- result{url: url, err: err}
			continue
		}
		resp.Body.Close()
		results <- result{url: url, status: resp.StatusCode}
	}
}

func main() {
	urls := loadURLs() // ১০,০০০ URL
	client := &http.Client{Timeout: 5 * time.Second}
	jobs := make(chan string)
	results := make(chan result, len(urls))

	for i := 0; i < 50; i++ {
		go worker(client, jobs, results)
	}
	go func() {
		for _, u := range urls {
			jobs <- u
		}
		close(jobs) // আর কাজ নেই
	}()
	for range urls {
		r := <-results
		if r.err != nil || r.status != 200 {
			fmt.Println("BAD", r.url, r.status, r.err)
		}
	}
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `make(chan string)` | unbuffered channel: পাঠানো আর নেওয়া একসাথে মিলতে হয় |
| `make(chan result, n)` | buffered channel: n টা পর্যন্ত জমা রাখতে পারে |
| `jobs <-chan string` | শুধু পড়ার channel (worker নিতে পারবে, পাঠাতে না) |
| `results chan<- result` | শুধু লেখার channel |
| `for url := range jobs` | channel বন্ধ না হওয়া পর্যন্ত একটা একটা করে নাও |
| `results <- result{...}` | channel-এ পাঠাও |
| `close(jobs)` | আর কিছু আসবে না জানাও, worker-দের loop শেষ হবে |
| `r := <-results` | channel থেকে একটা মান নাও |
| `http.Client{Timeout: ...}` | client-এর সব request-এ timeout; default client-এ timeout নেই |
| `resp.Body.Close()` | body বন্ধ না করলে connection leak হয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Buffered আর unbuffered channel-এর পার্থক্য?
**উত্তর:** Unbuffered-এ পাঠানো তখনই সম্পূর্ণ হয় যখন কেউ নেয় (synchronization)। Buffered-এ capacity পর্যন্ত জমা রেখে পাঠানো সাথে সাথে হয়, ভরে গেলে অপেক্ষা।

**প্রশ্ন:** বন্ধ channel-এ পাঠালে বা পড়লে কী হয়?
**উত্তর:** বন্ধ channel-এ পাঠালে panic। বন্ধ channel থেকে পড়লে zero value পাওয়া যায় সাথে সাথে (`v, ok := <-ch`-এ `ok` false)।

**প্রশ্ন:** Goroutine leak কী?
**উত্তর:** এমন goroutine যেটা কখনো শেষ হয় না, যেমন এমন channel-এর অপেক্ষায় যেখানে কেউ পাঠাবে না; memory ধীরে ধীরে বাড়ে। context বা channel বন্ধ করে এড়ানো হয়।

### context: timeout ও বাতিল

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **context** | request-এর সময়সীমা, বাতিলের সংকেত ও মান বহনকারী |
| **SIGTERM** | **Sig**nal **Term**inate → বন্ধ হওয়ার অনুরোধ সংকেত |

**সহজ ভাষায়:** `context.Context` দিয়ে একটা কাজের সময়সীমা ঠিক করা হয় আর বাতিলের খবর সব goroutine-এ ছড়িয়ে দেওয়া হয়। Kubernetes pod বন্ধ করার আগে SIGTERM পাঠায়; ভালো সার্ভিস সেটা ধরে চলমান কাজ শেষ করে সুন্দরভাবে বন্ধ হয় (graceful shutdown)।

> **বাস্তব পরিস্থিতি:** একটা deploy টুল ৫ মিনিটের মধ্যে rollout শেষ না হলে থেমে যাবে আর rollback করবে; আবার user Ctrl+C দিলে সব চলমান API call সাথে সাথে বাতিল হবে।

```go
func waitForRollout(ctx context.Context, check func() bool) error {
	ticker := time.NewTicker(5 * time.Second)
	defer ticker.Stop()
	for {
		select {
		case <-ctx.Done():
			return ctx.Err() // deadline exceeded বা canceled
		case <-ticker.C:
			if check() {
				return nil
			}
		}
	}
}

func main() {
	ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt, syscall.SIGTERM)
	defer stop()
	ctx, cancel := context.WithTimeout(ctx, 5*time.Minute)
	defer cancel()

	if err := waitForRollout(ctx, rolloutDone); err != nil {
		fmt.Println("rollout failed:", err, "- rolling back")
		os.Exit(1)
	}
	fmt.Println("rollout complete")
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `context.Background()` | সবচেয়ে উপরের খালি context |
| `signal.NotifyContext(..., os.Interrupt, syscall.SIGTERM)` | Ctrl+C বা SIGTERM এলে ctx নিজে বাতিল হবে |
| `context.WithTimeout(ctx, 5*time.Minute)` | ৫ মিনিট পর নিজে বাতিল হবে |
| `defer cancel()` | কাজ শেষে resource ছেড়ে দাও; সবসময় দিতে হয় |
| `time.NewTicker(5*time.Second)` | প্রতি ৫ সেকেন্ডে একটা সংকেত দেয় |
| `select { case ...: }` | যে channel আগে প্রস্তুত সেই case চলে |
| `<-ctx.Done()` | বাতিল বা সময় শেষ হলে এই channel বন্ধ হয় |
| `ctx.Err()` | কেন বাতিল: `context.DeadlineExceeded` বা `context.Canceled` |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** context কেন function-এর প্রথম parameter হিসেবে পাঠানো হয়?
**উত্তর:** Go-এর রীতি: যে কাজ সময় নিতে পারে বা বাতিলযোগ্য, সে ctx নেয়, আর নিচের সব call-এ পাঠায়, যাতে উপর থেকে একবার বাতিল করলে পুরো শৃঙ্খল থামে।

**প্রশ্ন:** Graceful shutdown কী?
**উত্তর:** SIGTERM পেলে নতুন request নেওয়া বন্ধ, চলমান request শেষ করা, connection বন্ধ করা, তারপর বের হওয়া। Go-তে `http.Server.Shutdown(ctx)` এটা করে।

---

## ০৯. Go দিয়ে DevOps টুল ও সার্ভিস বানানো

বাস্তবে Go দিয়ে যা বানানো হয়: CLI টুল, HTTP সার্ভিস, Prometheus exporter, আর ছোট container image।

### Cobra দিয়ে CLI টুল

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CLI** | **C**ommand **L**ine **I**nterface → কমান্ড লাইন ইন্টারফেস |
| **Cobra** | (সাপ) → Go-এর জনপ্রিয় CLI লাইব্রেরি |
| **flag** | `--name`-এর মতো অপশন |

**সহজ ভাষায়:** ছোট টুলে standard library-এর `flag` package যথেষ্ট। kubectl, helm, hugo-এর মতো subcommand সহ বড় CLI-তে Cobra ব্যবহার হয়: help, auto-completion নিজে তৈরি হয়।

> **বাস্তব পরিস্থিতি:** কোম্পানির internal টুল "ops": `ops deploy --env prod --version 2.4.1` আর `ops status`। Go-তে বানিয়ে Mac, Linux, Windows-এর binary GitHub Release-এ রাখলে সবাই ডাউনলোড করে সাথে সাথে চালায়, Python ইনস্টলের ঝামেলা নেই।

```go
package main

import (
	"fmt"
	"os"

	"github.com/spf13/cobra"
)

func main() {
	var env, version string

	root := &cobra.Command{Use: "ops", Short: "Internal ops tool"}
	deploy := &cobra.Command{
		Use:   "deploy",
		Short: "Deploy a version",
		RunE: func(cmd *cobra.Command, args []string) error {
			if env == "prod" && version == "" {
				return fmt.Errorf("--version is required for prod")
			}
			fmt.Printf("deploying %s to %s\n", version, env)
			return nil
		},
	}
	deploy.Flags().StringVarP(&env, "env", "e", "staging", "target environment")
	deploy.Flags().StringVar(&version, "version", "", "version to deploy")
	root.AddCommand(deploy)

	if err := root.Execute(); err != nil {
		os.Exit(1)
	}
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `cobra.Command{Use: "ops"}` | মূল কমান্ড |
| `RunE: func(...) error` | কমান্ড চালালে এই function চলে; error দিলে Cobra দেখায় |
| `StringVarP(&env, "env", "e", "staging", ...)` | `--env` আর ছোট রূপ `-e`, default staging; `P` = shorthand সহ |
| `root.AddCommand(deploy)` | ops-এর নিচে deploy subcommand |
| `root.Execute()` | argument পড়ে সঠিক subcommand চালাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Python-এর বদলে Go দিয়ে CLI বানানোর সুবিধা কী?
**উত্তর:** একটা static binary, কোনো runtime/dependency ইনস্টল লাগে না, দ্রুত চালু হয়, cross-compile করে সব OS-এর জন্য বানানো যায়।

### HTTP সার্ভিস ও health endpoint

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **net/http** | network / HTTP package → Go-এর built-in web সার্ভার ও client |
| **handler** | request সামলানোর function |
| **liveness / readiness** | liveness / readiness probe → বেঁচে আছে কিনা / traffic নিতে প্রস্তুত কিনা |

**সহজ ভাষায়:** Go-এর standard library দিয়েই production মানের HTTP সার্ভার বানানো যায়, কোনো framework ছাড়া। Go 1.22 থেকে route-এ method আর path parameter লেখা যায়। Kubernetes-এর জন্য `/healthz` (liveness) আর `/readyz` (readiness) endpoint আর graceful shutdown থাকা জরুরি।

> **বাস্তব পরিস্থিতি:** একটা ছোট internal সার্ভিস যেটা deploy-এর তথ্য JSON-এ দেয়। Kubernetes-এ চলে, তাই probe আর SIGTERM-এ সুন্দরভাবে বন্ধ হওয়া দরকার।

```go
func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("GET /healthz", func(w http.ResponseWriter, r *http.Request) {
		w.WriteHeader(http.StatusOK)
		w.Write([]byte("ok"))
	})
	mux.HandleFunc("GET /deploys/{app}", func(w http.ResponseWriter, r *http.Request) {
		app := r.PathValue("app")
		w.Header().Set("Content-Type", "application/json")
		json.NewEncoder(w).Encode(map[string]string{"app": app, "version": "2.4.1"})
	})

	srv := &http.Server{Addr: ":8080", Handler: mux, ReadHeaderTimeout: 5 * time.Second}
	go func() {
		if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
			log.Fatal(err)
		}
	}()

	ctx, stop := signal.NotifyContext(context.Background(), syscall.SIGTERM, os.Interrupt)
	defer stop()
	<-ctx.Done() // SIGTERM-এর অপেক্ষা

	shutdownCtx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
	defer cancel()
	srv.Shutdown(shutdownCtx) // চলমান request শেষ করে বন্ধ
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `http.NewServeMux()` | router: কোন path-এ কোন handler |
| `"GET /deploys/{app}"` | শুধু GET, আর `{app}` হলো path parameter (Go 1.22+) |
| `r.PathValue("app")` | path থেকে app-এর মান |
| `json.NewEncoder(w).Encode(...)` | সরাসরি response-এ JSON লেখো |
| `ReadHeaderTimeout` | ধীর client দিয়ে আক্রমণ (Slowloris) ঠেকাতে timeout |
| `http.ErrServerClosed` | Shutdown-এর পর এটা স্বাভাবিক, error নয় |
| `srv.Shutdown(ctx)` | নতুন request বন্ধ, চলমানগুলো শেষ হলে বা সময় ফুরালে বন্ধ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Liveness আর readiness probe-এর পার্থক্য?
**উত্তর:** Liveness fail হলে Kubernetes container restart করে (আটকে গেছে)। Readiness fail হলে restart না করে service থেকে traffic সরিয়ে নেয় (এখনো প্রস্তুত নয়, যেমন DB সংযোগ হয়নি)।

### Prometheus exporter বানানো

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Prometheus** | (গ্রিক দেবতা) → metric সংগ্রহ ও alert-এর সিস্টেম |
| **exporter** | কোনো সিস্টেমের metric Prometheus ফরম্যাটে দেয় |
| **Gauge / Counter / Histogram** | ওঠানামা করা মান / শুধু বাড়ে / মানের বণ্টন |

**সহজ ভাষায়:** Prometheus প্রতি কয়েক সেকেন্ডে `/metrics` endpoint থেকে metric টেনে নেয় (pull)। কোনো সিস্টেমের নিজস্ব metric না থাকলে ছোট একটা Go exporter লিখে সেই তথ্য metric হিসেবে দেওয়া হয়। Prometheus নিজেই Go-তে লেখা, আর এর Go client library সবচেয়ে পরিপক্ব।

> **বাস্তব পরিস্থিতি:** একটা পুরনো license server-এর API থেকে কয়টা license বাকি আছে Grafana-তে দেখাতে আর ১০টার নিচে নামলে alert দিতে হবে। ৬০ লাইনের Go exporter, তারপর Prometheus alert rule।

```go
var (
	licensesFree = prometheus.NewGauge(prometheus.GaugeOpts{
		Name: "license_free_total",
		Help: "Number of free licenses",
	})
	scrapeErrors = prometheus.NewCounter(prometheus.CounterOpts{
		Name: "license_scrape_errors_total",
		Help: "Failed calls to license API",
	})
)

func collect() {
	for {
		n, err := fetchFreeLicenses() // নিজের API call
		if err != nil {
			scrapeErrors.Inc()
		} else {
			licensesFree.Set(float64(n))
		}
		time.Sleep(30 * time.Second)
	}
}

func main() {
	prometheus.MustRegister(licensesFree, scrapeErrors)
	go collect()
	http.Handle("/metrics", promhttp.Handler())
	log.Fatal(http.ListenAndServe(":9100", nil))
}

// Prometheus alert:
// expr: license_free_total < 10
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `prometheus.NewGauge` | Gauge: এমন মান যা বাড়ে-কমে (ফ্রি license, তাপমাত্রা) |
| `prometheus.NewCounter` | Counter: শুধু বাড়ে (error সংখ্যা, request সংখ্যা) |
| `Name: ..._total` | নামকরণের রীতি: counter-এর শেষে `_total` |
| `.Inc()` / `.Set()` | counter এক বাড়াও / gauge-এ মান বসাও |
| `prometheus.MustRegister` | metric নিবন্ধন, ভুল হলে panic |
| `go collect()` | পেছনে আলাদা goroutine-এ তথ্য সংগ্রহ চলতে থাকে |
| `promhttp.Handler()` | `/metrics`-এ Prometheus ফরম্যাটে সব metric দেখায় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Counter আর Gauge-এর পার্থক্য?
**উত্তর:** Counter শুধু বাড়ে (restart-এ শূন্য হয়), `rate()` দিয়ে প্রতি সেকেন্ডের হার বের করা হয়। Gauge ইচ্ছামতো বাড়ে-কমে, বর্তমান অবস্থা দেখায়।

**প্রশ্ন:** Prometheus pull model কেন?
**উত্তর:** Prometheus নিজে টার্গেট খুঁজে টেনে নেয়, তাই কোন টার্গেট down তা সহজে বোঝা যায় (`up` metric), আর টার্গেটকে Prometheus-এর ঠিকানা জানতে হয় না। স্বল্পমেয়াদি job-এর জন্য Pushgateway আছে।

### Cross-compile ও ছোট Docker image

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **GOOS / GOARCH** | Go **O**perating **S**ystem / Go **Arch**itecture → কোন OS আর CPU-এর জন্য build |
| **CGO** | C + Go → Go থেকে C কোড ব্যবহার |
| **ldflags** | **l**inker **flags** → link ধাপের অপশন |
| **distroless** | distro + less → OS-এর অপ্রয়োজনীয় অংশ ছাড়া container base image |

**সহজ ভাষায়:** Go-তে GOOS আর GOARCH বদলে যেকোনো মেশিন থেকে Linux, Mac, Windows, ARM সবার binary বানানো যায়। `CGO_ENABLED=0` দিলে binary পুরোপুরি static হয়, তখন খালি (scratch) বা distroless image-এ চলে। ফলে image মাত্র ১০-২০ MB, attack surface কম, pull দ্রুত।

> **বাস্তব পরিস্থিতি:** Python সার্ভিসের image ছিল ৯০০ MB, Kubernetes-এ নতুন node-এ scale হতে অনেক সময় লাগত। একই সার্ভিস Go-তে লিখে multi-stage build-এ distroless image হলো ১৫ MB।

```bash
# এক ল্যাপটপ থেকে সব প্ল্যাটফর্মের binary
GOOS=linux   GOARCH=amd64 CGO_ENABLED=0 go build -ldflags="-s -w -X main.version=2.4.1" -o dist/ops-linux-amd64 .
GOOS=linux   GOARCH=arm64 CGO_ENABLED=0 go build -o dist/ops-linux-arm64 .
GOOS=darwin  GOARCH=arm64 go build -o dist/ops-darwin-arm64 .
GOOS=windows GOARCH=amd64 go build -o dist/ops.exe .
```

```dockerfile
# Dockerfile (multi-stage)
FROM golang:1.23 AS build
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -ldflags="-s -w" -o /app .

FROM gcr.io/distroless/static-debian12:nonroot
COPY --from=build /app /app
USER nonroot
ENTRYPOINT ["/app"]
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `go build -o` | `o` = output → binary-এর নাম/path |
| `-ldflags="-s -w"` | `s` = strip symbol table → symbol টেবিল বাদ · `w` = without DWARF → debug তথ্য বাদ, ফলে binary ছোট |
| `-X main.version=2.4.1` | `X` = set string variable → build-এর সময় main package-এর version variable-এ মান বসাও |
| `CGO_ENABLED=0` | CGO = C + Go → C লাইব্রেরি ছাড়া, পুরো static binary |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `FROM golang:1.23 AS build` | প্রথম ধাপ: compile করার বড় image, নাম build |
| `COPY go.mod go.sum` + `go mod download` | আগে dependency, যাতে কোড বদলালেও এই layer cache থেকে আসে |
| `FROM gcr.io/distroless/static-debian12:nonroot` | দ্বিতীয় ধাপ: শুধু চালানোর জন্য ক্ষুদ্র image, shell-ও নেই |
| `COPY --from=build` | প্রথম ধাপ থেকে শুধু binary টা আনো |
| `USER nonroot` | root ছাড়া চালাও, নিরাপদ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Multi-stage Docker build কেন?
**উত্তর:** Build-এর টুল (compiler, source) চূড়ান্ত image-এ যায় না; শুধু binary থাকে। ফলে image ছোট, দ্রুত, আর নিরাপদ।

**প্রশ্ন:** `CGO_ENABLED=0` কেন দরকার scratch/distroless-এ?
**উত্তর:** CGO চালু থাকলে binary glibc-এর মতো system C লাইব্রেরির উপর নির্ভর করে, যেটা scratch image-এ নেই, ফলে "no such file or directory" error দিয়ে চলবে না।

### Kubernetes client-go ও Operator ধারণা

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **client-go** | client for Go → Kubernetes-এর অফিসিয়াল Go client |
| **CRD** | **C**ustom **R**esource **D**efinition → নিজের বানানো Kubernetes resource-এর ধরন |
| **Operator** | নিজের resource-কে নিজে পরিচালনা করা controller |
| **reconcile** | (মিলিয়ে নেওয়া) → কাঙ্ক্ষিত আর আসল অবস্থা মেলানো |

**সহজ ভাষায়:** Kubernetes নিজে Go-তে লেখা, তাই সবচেয়ে ভালো সমর্থন Go-তে। client-go দিয়ে API-তে সবকিছু করা যায়। Operator হলো এমন controller যেটা একটা নিজের CRD (যেমন `kind: PostgresCluster`) দেখে আর reconcile loop-এ বারবার আসল অবস্থাকে কাঙ্ক্ষিত অবস্থার সাথে মেলায়। Kubebuilder বা Operator SDK দিয়ে এর কাঠামো তৈরি হয়।

> **বাস্তব পরিস্থিতি:** প্রতিটি নতুন টিমের জন্য namespace, ResourceQuota, NetworkPolicy, RBAC আর monitoring হাতে বানাতে হয়। একটা "Team" CRD আর ছোট operator বানালে, `kind: Team`-এর একটা YAML apply করলেই operator সব নিজে তৈরি করে, আর কেউ ভুল করে মুছলে আবার তৈরি করে।

```go
config, _ := clientcmd.BuildConfigFromFlags("", os.Getenv("HOME")+"/.kube/config")
clientset, _ := kubernetes.NewForConfig(config)

pods, err := clientset.CoreV1().Pods("prod").List(ctx, metav1.ListOptions{
	LabelSelector: "app=web",
})
if err != nil {
	log.Fatal(err)
}
for _, p := range pods.Items {
	fmt.Println(p.Name, p.Status.Phase)
}

// Operator-এর মূল ধারণা (controller-runtime):
// func (r *TeamReconciler) Reconcile(ctx, req) (ctrl.Result, error) {
//     team := get Team object
//     ensure Namespace, Quota, NetworkPolicy exist and match spec
//     return ctrl.Result{}, nil
// }
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `BuildConfigFromFlags("", path)` | kubeconfig ফাইল থেকে cluster-এর তথ্য; cluster-এর ভেতরে `rest.InClusterConfig()` |
| `kubernetes.NewForConfig` | সব API group-এর client-এর সেট |
| `CoreV1().Pods("prod").List(...)` | prod namespace-এর pod-এর তালিকা |
| `LabelSelector: "app=web"` | শুধু এই label যুক্ত pod |
| `Reconcile` | প্রতিবার পরিবর্তন হলে বা নির্দিষ্ট সময় পরপর ডাকা হয়; কাঙ্ক্ষিত অবস্থা নিশ্চিত করে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Operator pattern কী?
**উত্তর:** Kubernetes-কে নতুন resource (CRD) শেখানো আর একটা controller লেখা যেটা reconcile loop দিয়ে সেই resource-এর জন্য মানুষের (operator-এর) জ্ঞান স্বয়ংক্রিয়ভাবে প্রয়োগ করে, যেমন database cluster তৈরি, backup, upgrade।

**প্রশ্ন:** Reconcile loop idempotent হতে হয় কেন?
**উত্তর:** একই অবস্থার জন্য বারবার ডাকা হতে পারে; প্রতিবার নিরাপদে একই ফল দিতে হবে, দুবার তৈরি করা বা ভুল করে মুছে ফেলা যাবে না।

---

## ১০. Go টেস্টিং, টুলিং ও ভালো অনুশীলন

Go-এর টুলচেইনে টেস্ট, format, vet, race detector সব built-in। এগুলো ব্যবহারই ভালো Go কোডের চাবি।

### go test ও table-driven test

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **go test** | built-in টেস্ট টুল |
| **go vet** | go vet(erinarian) → সন্দেহজনক কোড পরীক্ষা |
| **golangci-lint** | golang CI lint → অনেক linter একসাথে |

**সহজ ভাষায়:** Go-তে টেস্টের জন্য আলাদা framework লাগে না; `_test.go` ফাইলে `TestXxx(t *testing.T)` function লেখো। Go কমিউনিটির প্রচলিত ধরন table-driven test: ইনপুট আর প্রত্যাশিত ফলের একটা তালিকা, তারপর একটা লুপ।

> **বাস্তব পরিস্থিতি:** Version তুলনার function (2.10.0 কি 2.9.1-এর চেয়ে বড়) ভুল করে string তুলনা করছিল, ফলে rollout টুল পুরনো version-কে নতুন ভাবছিল। Table-driven test-এ এই কেসটা রাখলে ধরা পড়ত।

```go
// version_test.go
func TestNewer(t *testing.T) {
	tests := []struct {
		name string
		a, b string
		want bool
	}{
		{"patch", "2.4.1", "2.4.0", true},
		{"double digit", "2.10.0", "2.9.1", true},
		{"equal", "1.0.0", "1.0.0", false},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			if got := Newer(tt.a, tt.b); got != tt.want {
				t.Errorf("Newer(%q, %q) = %v, want %v", tt.a, tt.b, got, tt.want)
			}
		})
	}
}
```

```bash
# টার্মিনালে
go test ./...
go test -race -cover ./...
go vet ./...
gofmt -l .
golangci-lint run
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `[]struct{...}{...}` | নামহীন struct-এর slice: টেস্ট টেবিল |
| `t.Run(tt.name, ...)` | প্রতিটি কেস আলাদা sub-test, fail হলে নাম দেখা যায় |
| `t.Errorf` | fail চিহ্নিত করো কিন্তু বাকি টেস্ট চালিয়ে যাও |
| `%q` | quote সহ string ছাপাও |

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `go test ./...` | `./...` = all packages → বর্তমান ফোল্ডার আর ভেতরের সব package |
| `-race` | race detector → goroutine-এর data race ধরো |
| `-cover` | coverage → কোডের কত % টেস্টে চলেছে |
| `gofmt -l` | `l` = list → যেসব ফাইলের format ঠিক নেই তাদের তালিকা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Table-driven test-এর সুবিধা কী?
**উত্তর:** নতুন কেস যোগ করা মানে শুধু টেবিলে একটা লাইন; কোড পুনরাবৃত্তি নেই, আর প্রতিটি কেসের নাম দিয়ে fail স্পষ্ট বোঝা যায়।

### প্রজেক্টের গঠন ও Go-এর ভালো অভ্যাস

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **cmd/** | command folder → প্রতিটি executable-এর main package |
| **internal/** | শুধু এই module ব্যবহার করতে পারবে |
| **vendor** | dependency-এর স্থানীয় কপি |

**সহজ ভাষায়:** ছোট টুলে একটা `main.go`-ই যথেষ্ট। বড় হলে প্রচলিত গঠন: `cmd/<name>/main.go`, `internal/`-এ ভেতরের কোড। Go-এর কিছু রীতি মানলে কোড অন্যদের কাছে স্বাভাবিক লাগে।

> **বাস্তব পরিস্থিতি:** নতুন সদস্য Kubernetes বা Prometheus-এর source খুলে সাথে সাথে বুঝে ফেলে কোথায় কী আছে, কারণ সবাই একই গঠন আর রীতি মানে।

```text
ops/
├── go.mod
├── cmd/
│   └── ops/
│       └── main.go
├── internal/
│   ├── deploy/
│   └── notify/
└── Makefile
```

| অভ্যাস | কেন |
|---|---|
| error কখনো উপেক্ষা করো না (`_`-এ ফেলো না) | ভুল চুপচাপ হারিয়ে যায় |
| error-এ প্রসঙ্গ যোগ করো (`%w`) | log দেখেই কোথায় কী হলো বোঝা যায় |
| সব network call-এ timeout/context | একটা ধীর সার্ভিস পুরো টুল আটকে দিতে পারে |
| goroutine-এর সংখ্যা সীমিত রাখো | resource শেষ হওয়া ঠেকাতে |
| ছোট interface (১-২ method) | সহজে বদলানো আর টেস্ট করা যায় |
| gofmt, go vet, -race CI-তে | স্বয়ংক্রিয় মান নিয়ন্ত্রণ |
| panic শুধু সত্যিকারের অসম্ভব অবস্থায় | সাধারণ ভুলে error ফেরত দাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Go module-এ `internal` ফোল্ডারের বিশেষত্ব কী?
**উত্তর:** `internal`-এর ভেতরের package শুধু তার parent ফোল্ডারের ভেতরের কোড import করতে পারে; বাইরের প্রজেক্ট পারে না। এতে public API ছোট থাকে।

---

## ১১. সিদ্ধান্ত: বাস্তব কাজে Python না Go

একই সমস্যা দুই ভাষায় কেমন দেখায়, আর বাস্তব কাজে কীভাবে বেছে নেবে।

### একই কাজ দুই ভাষায়: URL health check

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **HTTP** | **H**yper**T**ext **T**ransfer **P**rotocol → ওয়েবের protocol |

**সহজ ভাষায়:** পাশাপাশি দেখলে পার্থক্য স্পষ্ট: Python ছোট আর দ্রুত লেখা যায়; Go একটু লম্বা, কিন্তু type নিরাপদ, দ্রুত চলে, আর একটা binary হয়ে যেকোনো জায়গায় চলে।

> **বাস্তব পরিস্থিতি:** একই health checker প্রথমে Python-এ লেখা হলো on-call engineer-দের ব্যবহারের জন্য। পরে প্রতিটি Kubernetes node-এ DaemonSet হিসেবে চালাতে হলো, তখন Go সংস্করণ বেছে নেওয়া হলো ছোট image আর কম memory-র জন্য।

```python
# Python
import sys, requests

bad = 0
for url in sys.argv[1:]:
    try:
        code = requests.get(url, timeout=5).status_code
    except requests.RequestException as e:
        code = type(e).__name__
    print(url, code)
    bad += code != 200
sys.exit(1 if bad else 0)
```

```go
// Go
func main() {
	client := &http.Client{Timeout: 5 * time.Second}
	bad := 0
	for _, url := range os.Args[1:] {
		resp, err := client.Get(url)
		if err != nil {
			fmt.Println(url, err)
			bad++
			continue
		}
		resp.Body.Close()
		fmt.Println(url, resp.StatusCode)
		if resp.StatusCode != 200 {
			bad++
		}
	}
	if bad > 0 {
		os.Exit(1)
	}
}
```

| দিক | Python সংস্করণ | Go সংস্করণ |
|---|---|---|
| লাইন | কম | কিছুটা বেশি |
| চালাতে লাগে | Python + requests ইনস্টল | শুধু binary |
| Container image | শত MB পর্যন্ত | ১০-২০ MB |
| Memory | বেশি | কম |
| Type ভুল ধরা | চলার সময় | compile-এর সময় |

### সিদ্ধান্তের টেবিল: কোন কাজে কোনটা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ETL** | **E**xtract, **T**ransform, **L**oad → ডেটা বের করা, রূপান্তর, লোড |

**সহজ ভাষায়:** নিয়ম মনে রাখো: দ্রুত লিখতে হবে, একবার বা মাঝে মাঝে চলবে, অনেক লাইব্রেরি লাগবে: **Python**। অনেক মানুষ বা মেশিনে বিতরণ করতে হবে, দীর্ঘ সময় চলবে, পারফরম্যান্স বা concurrency জরুরি, বা Kubernetes ecosystem-এর সাথে গভীর কাজ: **Go**।

> **বাস্তব পরিস্থিতি:** নিচের টেবিলটা টিমে নতুন টুল বানানোর আগে দ্রুত সিদ্ধান্ত নিতে ব্যবহার করো।

| কাজ | পছন্দ | কারণ |
|---|---|---|
| AWS/GCP/Azure রিসোর্স অটোমেশন, cost রিপোর্ট | Python | boto3 ও SDK সমৃদ্ধ, দ্রুত লেখা |
| Log বিশ্লেষণ, CSV/Excel রিপোর্ট | Python | pandas, regex, সহজ |
| Ansible module, Lambda function | Python | Ansible Python-এ, Lambda-তে জনপ্রিয় |
| Network device অটোমেশন | Python | Netmiko, NAPALM, Nornir |
| Data/ML pipeline, ETL | Python | লাইব্রেরি ইকোসিস্টেম |
| সবার জন্য internal CLI টুল | Go | একটা binary, সব OS |
| Prometheus exporter, monitoring agent | Go | কম resource, দীর্ঘ সময় চলে |
| Kubernetes operator, controller, admission webhook | Go | client-go, controller-runtime |
| High-traffic API, proxy, sidecar | Go | দ্রুত, concurrency |
| Terraform provider, kubectl/Helm plugin | Go | ওই টুলগুলো Go-তে |
| একবারের migration স্ক্রিপ্ট | Python | লিখে ফেলে দেওয়া সহজ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** কখন Bash ছেড়ে Python-এ যাবে?
**উত্তর:** স্ক্রিপ্ট ৫০-১০০ লাইনের বেশি হলে, JSON/API লাগলে, জটিল error handling বা data structure লাগলে, বা টেস্ট লেখা দরকার হলে।

**প্রশ্ন:** কখন Python ছেড়ে Go-তে যাবে?
**উত্তর:** টুল অনেক মানুষ বা মেশিনে বিতরণ করতে হলে, দীর্ঘক্ষণ চলা সার্ভিস হলে, পারফরম্যান্স/memory গুরুত্বপূর্ণ হলে, বা Kubernetes/cloud-native ecosystem-এর সাথে গভীরভাবে কাজ করলে।

---

## ১২. প্রজেক্ট আইডিয়া: শিখে হাতে-কলমে করো

পড়ে শেখা অর্ধেক, বানিয়ে শেখা পুরো। এই প্রজেক্টগুলো GitHub-এ রাখলে CV-তে দেখানোর মতো portfolio হবে, আর interview-এ বাস্তব অভিজ্ঞতা হিসেবে বলতে পারবে।

| # | প্রজেক্ট | ভাষা | লেভেল | কী করবে | কী শিখবে |
|---|---|---|---|---|---|
| ১ | Disk ও service health checker | Python | 🟢 | প্রতিটি mount-এর ব্যবহার আর কয়েকটা systemd service চেক করে, সমস্যা হলে Slack-এ জানায় | subprocess, `shutil.disk_usage`, requests, logging, cron |
| ২ | Log analyzer | Python | 🟢 | nginx access log থেকে top IP, 4xx/5xx, সবচেয়ে ধীর endpoint-এর রিপোর্ট CSV-তে | regex, Counter, csv, argparse |
| ৩ | Port scanner / TCP checker CLI | Go | 🟢 | host আর port-এর তালিকা নিয়ে goroutine দিয়ে একসাথে পরীক্ষা | `net.DialTimeout`, WaitGroup, flag package |
| ৪ | AWS cost ও tag auditor | Python | 🟡 | tag ছাড়া বা বন্ধ থাকা resource (EC2, EBS, পুরনো snapshot) খুঁজে রিপোর্ট আর dry-run সহ পরিষ্কার | boto3 paginator, IAM, dry-run নকশা |
| ৫ | Network config backup | Python | 🟡 | সব switch/router-এর config Netmiko দিয়ে নিয়ে Git-এ commit, পরিবর্তন হলে diff মেইল | Netmiko, ThreadPoolExecutor, GitPython |
| ৬ | Internal ops CLI | Go | 🟡 | Cobra দিয়ে `ops status` / `ops deploy` / `ops logs`, GitHub Actions দিয়ে সব OS-এর binary release | Cobra, cross-compile, goreleaser |
| ৭ | URL uptime monitor + /metrics | Go | 🟡 | URL-এর তালিকা প্রতি ৩০ সেকেন্ডে চেক, Prometheus metric দেয়, Grafana dashboard | worker pool, `http.Client`, Prometheus client |
| ৮ | Kubernetes operator | Go | 🔴 | নিজের CRD (যেমন Team) থেকে namespace, quota, NetworkPolicy, RBAC তৈরি | Kubebuilder, reconcile loop, envtest |
| ৯ | Kubernetes admission webhook | Go | 🔴 | যেসব pod-এ resource limit বা নির্দিষ্ট label নেই, সেগুলো আটকাও | TLS, AdmissionReview, Kubernetes API |
| ১০ | Self-healing bot | Python | 🔴 | Alertmanager webhook পেয়ে পরিচিত সমস্যায় নিজে runbook চালায় (pod restart, disk পরিষ্কার), অজানা হলে মানুষকে ডাকে | FastAPI, Kubernetes client, নিরাপদ অটোমেশন নকশা |

---

## ১৩. ইন্টারভিউ প্রশ্ন ব্যাংক

প্রতিটি কার্ডে থাকা প্রশ্নের বাইরে আরও কিছু জনপ্রিয় ও একটু গভীর প্রশ্ন, ভাষা অনুযায়ী সাজানো। উত্তর মুখস্থ না করে নিজের ভাষায় বলার অনুশীলন করো।

### 🐍 Python

**প্রশ্ন ১. Python-এ list আর generator-এর পার্থক্য কী? কখন generator?**
List সব মান একসাথে memory-তে রাখে; generator (`yield` বা `( )` comprehension) একটা একটা করে মান তৈরি করে। বড় ফাইল বা লাখো item প্রসেস করতে generator, কারণ memory কম লাগে।

**প্রশ্ন ২. Decorator কী? একটা বাস্তব উদাহরণ দাও।**
Decorator একটা function যা অন্য function-কে মোড়ায় আর আচরণ যোগ করে, `@` দিয়ে লাগানো হয়। উদাহরণ: retry decorator যা ব্যর্থ হলে function-কে ৩ বার আবার চালায়, বা সময় মাপার timer decorator।

**প্রশ্ন ৩. Shallow copy আর deep copy-এর পার্থক্য?**
Shallow copy (`copy.copy`, `list[:]`) বাইরের container কপি করে কিন্তু ভেতরের object একই থাকে; deep copy (`copy.deepcopy`) ভেতরের সবকিছুও নতুন করে কপি করে। Nested config বদলানোর আগে deep copy না করলে মূলটাও বদলে যায়।

**প্রশ্ন ৪. Mutable default argument-এর ফাঁদ কী?**
`def f(items=[])` লিখলে list একবারই তৈরি হয় আর সব call-এ শেয়ার হয়, ফলে আগের call-এর মান জমে থাকে। সমাধান: `items=None`, তারপর ভেতরে `if items is None: items = []`।

**প্রশ্ন ৫. Python স্ক্রিপ্টে secret কীভাবে সামলাবে?**
কোডে বা Git-এ কখনো না; environment variable, AWS Secrets Manager/SSM Parameter Store, Vault, বা Kubernetes Secret থেকে পড়বে; log-এ secret ছাপাবে না।

**প্রশ্ন ৬. অনেক সার্ভারে একসাথে কাজ করতে Python-এ কী ব্যবহার করবে?**
I/O-bound কাজে ThreadPoolExecutor বা asyncio, CPU-bound-এ multiprocessing; বড় পরিসরে Ansible, Fabric বা Nornir।

**প্রশ্ন ৭. requirements.txt আর virtualenv-এর ভূমিকা কী? pip-tools/poetry কেন?**
venv প্রজেক্টের পরিবেশ আলাদা রাখে, requirements.txt dependency তালিকা রাখে। pip-tools/poetry/uv সরাসরি আর পরোক্ষ সব dependency-এর নির্দিষ্ট version lock করে, ফলে সব জায়গায় একই পরিবেশ (reproducible build)।

### 🐹 Go

**প্রশ্ন ৮. Go-তে nil interface আর nil pointer সহ interface-এর পার্থক্য?**
একটা interface তখনই nil যখন type আর value দুটোই nil। কোনো nil pointer `(*MyErr)(nil)`-কে error interface-এ রাখলে interface আর nil থাকে না, ফলে `err != nil` সত্য হয়ে যায়। এটা Go-এর বিখ্যাত ফাঁদ।

**প্রশ্ন ৯. Slice-এর length আর capacity কী? append কখন নতুন array বানায়?**
Length = এখন কয়টা উপাদান, capacity = ভেতরের array-তে কতটা জায়গা আছে। append-এ capacity পেরোলে নতুন বড় array তৈরি হয়ে কপি হয়; তাই দুটো slice একই array শেয়ার করলে অপ্রত্যাশিত পরিবর্তন হতে পারে।

**প্রশ্ন ১০. Go-তে map কি concurrency-safe?**
না। একাধিক goroutine একসাথে লিখলে "concurrent map writes" দিয়ে প্রোগ্রাম crash করে। `sync.Mutex`/`RWMutex` দিয়ে রক্ষা করো বা বিশেষ ক্ষেত্রে `sync.Map`।

**প্রশ্ন ১১. Unbuffered channel দিয়ে deadlock কীভাবে হয়?**
একই goroutine-এ unbuffered channel-এ পাঠালে আর অন্য কেউ না পড়লে সে চিরকাল আটকে থাকে; সব goroutine আটকালে runtime "all goroutines are asleep - deadlock!" দেয়।

**প্রশ্ন ১২. Go garbage collector কীভাবে কাজ করে, পারফরম্যান্সে প্রভাব?**
Go-এর GC concurrent mark-and-sweep, খুব কম pause (সাধারণত মিলিসেকেন্ডের নিচে)। `GOGC` আর `GOMEMLIMIT` দিয়ে টিউন করা যায়; container-এ memory limit থাকলে `GOMEMLIMIT` সেট করা ভালো।

**প্রশ্ন ১৩. Go দিয়ে বানানো একটা সার্ভিস Kubernetes-এ OOMKilled হচ্ছে। কী দেখবে?**
pprof দিয়ে heap profile, goroutine leak আছে কিনা (goroutine সংখ্যা বাড়ছে কিনা), response body বন্ধ হচ্ছে কিনা, বড় slice ধরে রাখা হচ্ছে কিনা, আর `GOMEMLIMIT` container limit-এর একটু নিচে সেট করা।

**প্রশ্ন ১৪. Go modules-এ semantic import versioning কী?**
Major version 2 বা তার বেশি হলে import path-এ `/v2` যোগ হয় (`github.com/x/y/v2`), যাতে একই প্রজেক্টে দুটো major version পাশাপাশি থাকতে পারে আর breaking change স্পষ্ট হয়।

### ⚙️ সাধারণ অটোমেশন ও নকশা

**প্রশ্ন ১৫. Idempotent অটোমেশন বলতে কী বোঝো?**
একই স্ক্রিপ্ট বারবার চালালে একই ফল হবে, দ্বিতীয়বার অপ্রয়োজনীয় পরিবর্তন বা ক্ষতি করবে না; যেমন "user না থাকলে তৈরি করো", "user তৈরি করো" নয়। Ansible, Terraform, Kubernetes-এর মূল নীতি।

**প্রশ্ন ১৬. একটা ঝুঁকিপূর্ণ অটোমেশন (যেমন resource মোছা) নিরাপদ করবে কীভাবে?**
Dry-run default রাখা, স্পষ্ট নিশ্চিতকরণ flag, allowlist/tag দিয়ে লক্ষ্য সীমিত করা, ছোট batch-এ চালানো, সব কাজ log করা, আগে staging-এ চালানো, আর rollback পরিকল্পনা।

**প্রশ্ন ১৭. স্ক্রিপ্ট থেকে সেবা বানানোর সময় কী কী যোগ করবে?**
Structured logging, metric (Prometheus), health endpoint, config file/env, graceful shutdown, retry ও timeout, টেস্ট, CI pipeline, container image আর documentation।

**প্রশ্ন ১৮. Python আর Go দুটোতেই exit code কেন গুরুত্বপূর্ণ?**
cron, systemd, CI/CD আর Kubernetes Job শুধু exit code দেখে সফল না ব্যর্থ ঠিক করে; ব্যর্থতায় non-zero না দিলে ভুল চুপচাপ পার পেয়ে যায়।

---

<p align="center"><sub>Python & Go for DevOps · <a href="../README.md">← সব গাইড</a></sub></p>
