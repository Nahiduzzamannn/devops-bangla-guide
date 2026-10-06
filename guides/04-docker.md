# Docker: Complete Guide

```bash
$ docker compose up -d --build
```

**Dockerfile, Image Build, Docker Compose — সব কমান্ড, বাস্তব পরিস্থিতি ও ইন্টারভিউ প্রস্তুতি**

`run` · `exec` · `logs` · `inspect` · `volume` · `network` · `prune` · `buildx` · Multi-stage build · Security · CI/CD · Troubleshooting

| | |
|---|---|
| অধ্যায় | ১১টি + Incident Playbook + প্রশ্ন ব্যাংক |
| টপিক কার্ড | ৩০টি, অপশনের অক্ষরে অক্ষরে ব্যাখ্যাসহ |
| ইন্টারভিউ প্রশ্ন | ৭১টি প্রশ্ন ও উত্তর |
| Incident Playbook | ৫টি |
| লেভেল | 🟢 বেসিক · 🟡 ইন্টারমিডিয়েট · 🔴 অ্যাডভান্সড |

---

## এই গাইড কীভাবে ব্যবহার করবে

বইটা সাজানো আছে যেভাবে বাস্তবে শিখবে সেভাবে: আগে container আসলে কী, তারপর অন্যের বানানো image চালানো, তারপর নিজের Dockerfile লিখে image build, তারপর data ও network, তারপর Docker Compose দিয়ে পুরো অ্যাপ, আর শেষে production, troubleshooting আর CI/CD।

প্রতিটি কার্ডে আছে:

- **পূর্ণরূপ** — প্রতিটি শব্দ বা সংক্ষেপ কোথা থেকে এসেছে
- **সহজ ভাষায়** ব্যাখ্যা
- **বাস্তব পরিস্থিতি** — কখন এই কমান্ড লাগবে
- **কোড** ও **অপশনের পূর্ণরূপ** (যেমন `-it` = `i` interactive + `t` tty)
- **লাইন ধরে ব্যাখ্যা**
- **ইন্টারভিউ প্রশ্ন** ও **প্রো টিপ**

অনুশীলনের জন্য Linux VM বা Docker Desktop-এ প্রতিটি কমান্ড নিজে চালাও। শেষের Incident Playbook গুলো checklist হিসেবে, আর প্রশ্ন ব্যাংক ইন্টারভিউ প্রস্তুতিতে ব্যবহার করো।

> **নোট:** কমান্ড ও কনফিগারেশন ইংরেজিতে রাখা হয়েছে, কারণ ঠিক এভাবেই লিখতে হয়। উদাহরণগুলো Docker Engine 25+ আর Docker Compose v2 (`docker compose`) অনুযায়ী।

---

## সূচিপত্র

- [০০. শুরুর আগে: Container আসলে কী](#০০-শুরুর-আগে-container-আসলে-কী)
- [০১. Container চালানো ও পরিচালনা](#০১-container-চালানো-ও-পরিচালনা)
- [০২. Image ম্যানেজমেন্ট ও Registry](#০২-image-ম্যানেজমেন্ট-ও-registry)
- [০৩. Dockerfile লেখা: প্রতিটি instruction](#০৩-dockerfile-লেখা-প্রতিটি-instruction)
- [০৪. Image build: docker build ও buildx](#০৪-image-build-docker-build-ও-buildx)
- [০৫. Storage: Volume, Bind mount, tmpfs](#০৫-storage-volume-bind-mount-tmpfs)
- [০৬. Docker Networking](#০৬-docker-networking)
- [০৭. Docker Compose: মাল্টি-কন্টেইনার অ্যাপ](#০৭-docker-compose-মাল্টি-কন্টেইনার-অ্যাপ)
- [০৮. Production: resource limit, logging ও security](#০৮-production-resource-limit-logging-ও-security)
- [০৯. পরিষ্কার ও Troubleshooting](#০৯-পরিষ্কার-ও-troubleshooting)
- [১০. CI/CD-তে Docker](#১০-cicd-তে-docker)
- [১১. বাস্তব Incident Playbook](#১১-বাস্তব-incident-playbook)
- [১২. ইন্টারভিউ প্রশ্ন ব্যাংক](#১২-ইন্টারভিউ-প্রশ্ন-ব্যাংক)

---

## ০০. শুরুর আগে: Container আসলে কী

Docker কমান্ড মুখস্থ করার আগে বোঝো ভেতরে কী হচ্ছে। তাহলে প্রতিটি কমান্ড কেন আছে সেটা নিজেই বুঝে যাবে।

### Container, Image, Registry: মূল ধারণা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Docker** | ডক শ্রমিক → জাহাজে container ওঠানো-নামানোর শ্রমিক থেকে নাম |
| **Image** | অ্যাপ চালানোর জন্য সবকিছুসহ একটা প্যাকেজ (টেমপ্লেট) |
| **Container** | image থেকে চালু হওয়া জীবন্ত প্রক্রিয়া |
| **Registry** | image রাখার ভাণ্ডার (Docker Hub, ECR) |
| **OCI** | **O**pen **C**ontainer **I**nitiative → container এর খোলা মান (standard) |

**সহজ ভাষায়:** Image হলো রান্নার রেসিপি আর সব উপকরণ একটা বাক্সে; container হলো সেই রেসিপি দিয়ে রান্না হওয়া খাবার। একটা image থেকে যত খুশি container চালানো যায়। Image-এ থাকে অ্যাপের কোড, runtime (Node, Python, Java), লাইব্রেরি আর OS-এর প্রয়োজনীয় ফাইল, ফলে "আমার মেশিনে তো চলে" সমস্যা আর থাকে না। Registry হলো image-এর GitHub: সেখানে push করো, অন্য সার্ভার থেকে pull করো।

> **বাস্তব পরিস্থিতি:** Developer-এর ল্যাপটপে Python 3.12, সার্ভারে 3.8; অ্যাপ সার্ভারে ভেঙে যায়। Docker image-এ Python 3.12 সহ সব কিছু থাকলে ল্যাপটপ, CI আর production সবখানে হুবহু একই পরিবেশ।

```text
Dockerfile --(docker build)--> Image --(docker push)--> Registry
                                 |                          |
                            (docker run)              (docker pull)
                                 v                          v
                             Container              অন্য সার্ভারে Image -> Container
```

| শব্দ | সহজ উপমা |
|---|---|
| Dockerfile | রেসিপি (কীভাবে বানাতে হবে) |
| Image | রান্নার প্যাকেট, বদলানো যায় না (read-only) |
| Container | রান্না করা খাবার; চলমান, নিজের লেখার স্তর আছে |
| Registry | দোকান, যেখানে প্যাকেট রাখা ও কেনা হয় |
| Tag | প্যাকেটের সংস্করণ লেবেল (`nginx:1.27`) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Image আর container-এর পার্থক্য কী?
**উত্তর:** Image একটা read-only টেমপ্লেট (layer-এর স্তূপ)। Container হলো image-এর উপর একটা লেখার স্তর যোগ করে চালানো একটা process। একটা image থেকে অনেক container চলে।

### Container বনাম Virtual Machine

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **VM** | **V**irtual **M**achine → ভার্চুয়াল কম্পিউটার |
| **Hypervisor** | VM চালানোর সফটওয়্যার |
| **namespace** | process-কে আলাদা জগৎ দেখানো (PID, network, mount) |
| **cgroup** | **C**ontrol **Group** → CPU/memory সীমা দেওয়া |

**সহজ ভাষায়:** VM-এ প্রতিটির নিজের পুরো OS (kernel সহ) চলে, তাই ভারী (GB) আর চালু হতে মিনিট লাগে। Container host-এর kernel শেয়ার করে, শুধু অ্যাপ আর তার লাইব্রেরি থাকে, তাই হালকা (MB) আর সেকেন্ডে চালু। Container আসলে Linux-এর সাধারণ process, যাকে namespace দিয়ে আলাদা করা আর cgroup দিয়ে সীমিত করা হয়েছে।

> **বাস্তব পরিস্থিতি:** একটা সার্ভারে আগে ৫টা VM চলত, প্রতিটিতে একটা ছোট সার্ভিস। Container-এ গিয়ে একই সার্ভারে ৪০টা সার্ভিস চলছে, deploy সময় মিনিট থেকে সেকেন্ডে নেমে এসেছে।

| বিষয় | Virtual Machine | Container |
|---|---|---|
| OS | প্রতিটির নিজের পুরো OS ও kernel | host-এর kernel শেয়ার করে |
| আকার | GB | MB |
| চালু হতে | মিনিট | সেকেন্ড বা তার কম |
| Isolation | খুব শক্ত (hardware স্তরে) | kernel স্তরে, তুলনামূলক কম |
| ঘনত্ব | একটা সার্ভারে কয়েকটা | একটা সার্ভারে অনেকগুলো |
| ভিন্ন OS | Linux host-এ Windows VM চলে | Linux container-এর জন্য Linux kernel লাগে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Container কি VM-এর চেয়ে কম নিরাপদ?
**উত্তর:** Isolation তুলনামূলক দুর্বল, কারণ সব container একই kernel শেয়ার করে; kernel-এ দুর্বলতা থাকলে container থেকে host-এ পৌঁছানো সম্ভব। তাই non-root user, capability কমানো, seccomp, আর অতি সংবেদনশীল কাজে gVisor/Kata বা VM ব্যবহার করা হয়।

**প্রশ্ন:** Mac/Windows-এ Linux container কীভাবে চলে?
**উত্তর:** Docker Desktop একটা ছোট Linux VM চালায়, container সেই VM-এর kernel-এ চলে।

### Docker architecture ও ইনস্টল

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **dockerd** | **Docker D**aemon → পেছনে চলা Docker সার্ভিস |
| **CLI** | **C**ommand **L**ine **I**nterface → `docker` কমান্ড |
| **containerd** | **Container D**aemon → container-এর জীবনচক্র চালায় |
| **runc** | **Run C**ontainer → আসলে container তৈরি করে (OCI runtime) |
| **socket** | UNIX socket → `/var/run/docker.sock`, CLI আর daemon-এর যোগাযোগের পথ |

**সহজ ভাষায়:** তুমি `docker` কমান্ড লেখো (client), সেটা `/var/run/docker.sock` দিয়ে `dockerd`-কে বলে, `dockerd` কাজটা `containerd`-কে দেয়, `containerd` `runc` দিয়ে আসল container বানায়। `docker.sock`-এ যার অ্যাক্সেস আছে সে কার্যত host-এর root, তাই `docker` group-এ সাবধানে user যোগ করবে।

> **বাস্তব পরিস্থিতি:** নতুন Ubuntu সার্ভারে Docker বসিয়ে প্রতিবার `sudo docker` লিখতে হচ্ছে। Deploy user-কে `docker` group-এ যোগ করে আবার login করলে `sudo` লাগবে না।

```bash
curl -fsSL https://get.docker.com | sudo sh   # official install script
sudo usermod -aG docker $USER                 # sudo ছাড়া docker
newgrp docker                                 # বা logout/login
docker version
docker info
sudo systemctl enable --now docker
docker run hello-world
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `curl -fsSL` | `f` = fail → HTTP error-এ fail · `s` = silent → চুপচাপ · `S` = show error → তবু error দেখাও · `L` = location → redirect মানো |
| `usermod -aG docker` | `a` = append → আগের group রেখে যোগ · `G` = Groups → এই group-এ |
| `systemctl enable --now` | `enable` → boot-এ চালু · `now` → এখনই চালু |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `docker version` | client আর server (daemon) দুটোর version; server অংশ না এলে daemon চলছে না বা অনুমতি নেই |
| `docker info` | কতগুলো container/image, storage driver, cgroup version, registry ইত্যাদি |
| `docker run hello-world` | সব ঠিকমতো কাজ করছে কিনা যাচাই করার ছোট image |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `docker` group-এ user যোগ করার নিরাপত্তা ঝুঁকি কী?
**উত্তর:** `docker` group-এর সদস্য `-v /:/host` দিয়ে container চালিয়ে পুরো host-এর ফাইলে root হিসেবে লিখতে পারে, অর্থাৎ কার্যত root। তাই শুধু বিশ্বস্ত user, অথবা rootless Docker ব্যবহার।

**প্রশ্ন:** "Cannot connect to the Docker daemon" error-এর কারণ কী?
**উত্তর:** Daemon চলছে না (`systemctl status docker`), বা user-এর `docker.sock`-এ অনুমতি নেই (`docker` group-এ নেই, বা group যোগের পর নতুন login হয়নি)।

---

## ০১. Container চালানো ও পরিচালনা

প্রতিদিন সবচেয়ে বেশি লাগবে এই কমান্ডগুলো: চালানো, দেখা, log পড়া, ভেতরে ঢোকা, থামানো।

### docker run: container চালানো

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **run** | image থেকে নতুন container তৈরি করে চালাও |
| **port publish** | host-এর port-কে container-এর port-এ জোড়া |

**সহজ ভাষায়:** `docker run` আসলে চারটা কাজ একসাথে করে: image না থাকলে pull, container তৈরি (create), চালু (start), আর terminal যুক্ত। অপশন দিয়ে ঠিক করা হয় পেছনে চলবে কিনা, কোন port খোলা হবে, কোন environment variable, কোন volume, আর নাম কী।

> **বাস্তব পরিস্থিতি:** টেস্টের জন্য দ্রুত একটা PostgreSQL দরকার। কিছু ইনস্টল না করে একটা কমান্ডে database চালু, port 5432 খোলা, password দেওয়া, data volume-এ সংরক্ষিত, আর সার্ভার reboot হলেও নিজে চালু হবে।

```bash
docker run -d \
  --name pg \
  -p 5432:5432 \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  --restart unless-stopped \
  postgres:16

docker run -it --rm ubuntu:24.04 bash                    # সাময়িক shell, বের হলে মুছে যাবে
docker run --rm -v "$PWD":/app -w /app node:20 npm test
docker run -d -p 8080:80 nginx:1.27                      # browser-এ http://localhost:8080
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `-d` | `d` = detach → পেছনে চালাও, terminal ফেরত দাও |
| `--name pg` | container-এর নাম (না দিলে এলোমেলো নাম হয়) |
| `-p 5432:5432` | `p` = publish → `host_port:container_port`, বাইরে থেকে পৌঁছানো যাবে |
| `-e` | `e` = env → environment variable দাও |
| `-v pgdata:/var/lib/...` | `v` = volume → `volume_নাম:container_path`, data টিকে থাকবে |
| `--restart unless-stopped` | restart policy → crash বা reboot-এ নিজে চালু, হাতে stop করলে না |
| `-it` | `i` = interactive → keyboard input খোলা রাখো · `t` = tty → terminal দাও (দুটো মিলে shell) |
| `--rm` | `rm` = remove → container থামলে নিজে মুছে যাবে |
| `-w /app` | `w` = workdir → container-এর ভেতরে কোন ফোল্ডারে কমান্ড চলবে |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `postgres:16` | `image-এর নাম:tag`; tag না দিলে `:latest` ধরা হয় |
| `\` (লাইনের শেষে) | লম্বা কমান্ড পরের লাইনে চলছে |
| `"$PWD":/app` | বর্তমান ফোল্ডার container-এর `/app`-এ যুক্ত (bind mount) |
| `bash` / `npm test` | image-এর default কমান্ডের বদলে এটা চালাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `docker run` আর `docker start`-এর পার্থক্য?
**উত্তর:** `run` নতুন container তৈরি করে চালায়; `start` আগে থেকে থাকা থেমে থাকা container আবার চালু করে।

**প্রশ্ন:** `-p 8080:80`-এ কোনটা host আর কোনটা container port?
**উত্তর:** বামে host (8080), ডানে container (80)। `-p 127.0.0.1:8080:80` দিলে শুধু localhost থেকে পাওয়া যাবে।

> 💡 **প্রো টিপ:** Production-এ কখনো `:latest` tag ব্যবহার করো না; নির্দিষ্ট version দাও, নইলে কবে কোন version চলছে বোঝা যাবে না।

### ps, logs, exec, inspect, stats: দেখা ও ঢোকা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ps** | **P**rocess **S**tatus → চলমান container-এর তালিকা |
| **exec** | **Exec**ute → চলমান container-এ কমান্ড চালাও |
| **inspect** | বিস্তারিত JSON তথ্য |

**সহজ ভাষায়:** Container চালানোর পর জানতে হয় চলছে কিনা, কী log দিচ্ছে, ভেতরে কী আছে, কত resource খাচ্ছে। এই কমান্ডগুলো সেই কাজের।

> **বাস্তব পরিস্থিতি:** API container বারবার restart হচ্ছে। `docker ps -a`-তে STATUS দেখাচ্ছে `Exited (1) 5 seconds ago`। `docker logs`-এ দেখা গেল `DATABASE_URL` পাওয়া যাচ্ছে না। `docker inspect` দিয়ে environment দেখে বোঝা গেল variable-এর নামে বানান ভুল।

```bash
docker ps
docker ps -a
docker ps -q
docker logs -f --tail 100 api
docker logs --since 10m -t api
docker exec -it api sh
docker exec api env
docker inspect api
docker inspect -f '{{.State.Status}} {{.State.ExitCode}}' api
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' api
docker stats --no-stream
docker top api
docker cp api:/app/logs/error.log ./error.log
docker port api
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `ps -a` | `a` = all → থেমে থাকা সহ সব container |
| `ps -q` | `q` = quiet → শুধু container ID (অন্য কমান্ডে পাঠাতে) |
| `logs -f` | `f` = follow → নতুন log live দেখাও |
| `--tail 100` | শুধু শেষ ১০০ লাইন |
| `--since 10m` | শেষ ১০ মিনিটের log |
| `-t` | `t` = timestamps → প্রতিটি লাইনে সময় |
| `exec -it` | `i` = interactive → input খোলা · `t` = tty → terminal |
| `inspect -f` | `f` = format → Go template দিয়ে শুধু দরকারি অংশ বের করো |
| `stats --no-stream` | একবার দেখিয়ে থামো, live না |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `docker exec -it api sh` | চলমান container-এর ভেতরে shell খোলো (bash না থাকলে sh) |
| `docker exec api env` | container-এর environment variable দেখাও |
| `{{.State.ExitCode}}` | শেষবার কোন exit code দিয়ে থেমেছিল |
| `docker top api` | container-এর ভেতরের process |
| `docker cp api:/path ./` | container থেকে host-এ ফাইল কপি (উল্টোটাও হয়) |
| `docker port api` | কোন port কোথায় publish হয়েছে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Container চলে না, সাথে সাথে বন্ধ হয়ে যায়। কীভাবে খুঁজবে?
**উত্তর:** `docker ps -a`-তে exit code দেখো, `docker logs` দেখো, `docker inspect`-এ State আর Config দেখো; দরকার হলে `docker run -it --entrypoint sh image` দিয়ে ভেতরে ঢুকে হাতে কমান্ড চালাও।

**প্রশ্ন:** `exec` আর `attach`-এর পার্থক্য?
**উত্তর:** `exec` container-এ নতুন process (যেমন shell) চালায়; `attach` মূল process (PID 1)-এর input/output-এ যুক্ত হয়, সেখানে Ctrl+C দিলে container থেমে যেতে পারে।

### stop, start, restart, kill, rm: জীবনচক্র

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **stop** | SIGTERM দিয়ে ভদ্রভাবে থামাও |
| **kill** | সাথে সাথে SIGKILL |
| **rm** | **R**e**m**ove → container মুছে ফেলো |
| **SIGTERM** | **Sig**nal **Term**inate → বন্ধ হওয়ার অনুরোধ |

**সহজ ভাষায়:** `docker stop` প্রথমে SIGTERM পাঠায়, ১০ সেকেন্ড অপেক্ষা করে, না থামলে SIGKILL। অ্যাপ SIGTERM ধরে কাজ শেষ করে বন্ধ হলে data নষ্ট হয় না। `kill` সাথে সাথে মেরে ফেলে। `rm` দিয়ে থেমে থাকা container মুছতে হয়; চলমান হলে `-f`।

> **বাস্তব পরিস্থিতি:** Database container-এ `docker kill` দিলে চলমান transaction মাঝপথে কাটা পড়ে। `docker stop -t 60` দিলে ৬০ সেকেন্ড পর্যন্ত সময় পায় ঠিকমতো বন্ধ হতে।

```bash
docker stop api
docker stop -t 60 pg
docker start api
docker restart api
docker kill api
docker rm api
docker rm -f api
docker rm $(docker ps -aq -f status=exited)
docker pause api && docker unpause api
docker update --restart unless-stopped --memory 512m api
docker rename old_name new_name
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `stop -t 60` | `t` = time → SIGKILL-এর আগে কত সেকেন্ড অপেক্ষা |
| `rm -f` | `f` = force → চলমান হলেও থামিয়ে মুছো |
| `ps -aq -f status=exited` | `a` = all → সব · `q` = quiet → শুধু ID · `f` = filter → শুধু exited গুলো |
| `update --memory` | চলমান container-এর memory সীমা বদলাও |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `$( ... )` | ভেতরের কমান্ডের output (ID তালিকা) `rm`-এর argument হয় |
| `pause` / `unpause` | process গুলো সাময়িক জমিয়ে রাখো (cgroup freezer), মুছে না |
| `docker update` | নতুন করে না বানিয়েই restart policy বা resource সীমা বদলাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `docker stop` আর `docker kill`-এর পার্থক্য?
**উত্তর:** `stop` প্রথমে SIGTERM, grace period (default ১০ সেকেন্ড) পর SIGKILL; `kill` সরাসরি SIGKILL (বা `-s` দিয়ে অন্য signal)।

**প্রশ্ন:** Container-এ restart policy গুলো কী কী?
**উত্তর:** `no` (default), `on-failure[:n]` (non-zero exit-এ), `always` (সবসময়, হাতে থামালেও daemon restart-এ চালু), `unless-stopped` (`always`-এর মতো, কিন্তু হাতে থামালে আর চালু হয় না)।

---

## ০২. Image ম্যানেজমেন্ট ও Registry

Image আনা, নাম দেওয়া, registry-তে পাঠানো, আর পুরনোগুলো পরিষ্কার করা।

### pull, images, tag, push, login

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **pull** | registry থেকে image নামাও |
| **push** | registry-তে image পাঠাও |
| **tag** | image-এর নাম ও সংস্করণ লেবেল |
| **ECR** | **E**lastic **C**ontainer **R**egistry → AWS-এর registry |
| **GHCR** | **G**it**H**ub **C**ontainer **R**egistry → GitHub-এর registry |

**সহজ ভাষায়:** Image-এর পুরো নাম: `registry/namespace/repository:tag`, যেমন `ghcr.io/acme/api:2.4.1`। Registry না লিখলে Docker Hub (`docker.io`) ধরা হয়। একই image-এর অনেকগুলো tag থাকতে পারে। Push করার আগে সেই registry-তে login আর সঠিক নামে tag করতে হয়।

> **বাস্তব পরিস্থিতি:** CI-তে বানানো image-কে git commit-এর hash আর version দুটো tag দিয়ে AWS ECR-এ push করতে হবে, যাতে production-এ ঠিক কোন commit চলছে বোঝা যায় আর rollback সহজ হয়।

```bash
docker pull nginx:1.27-alpine
docker images
docker image ls --filter reference='acme/*'
docker tag api:latest ghcr.io/acme/api:2.4.1
docker tag api:latest ghcr.io/acme/api:$(git rev-parse --short HEAD)
echo $GITHUB_TOKEN | docker login ghcr.io -u rahim --password-stdin
docker push ghcr.io/acme/api:2.4.1

# AWS ECR
aws ecr get-login-password --region ap-southeast-1 \
  | docker login --username AWS --password-stdin 123456789012.dkr.ecr.ap-southeast-1.amazonaws.com

docker image inspect nginx:1.27-alpine --format '{{.Architecture}} {{.Size}}'
docker pull --platform linux/arm64 nginx:1.27
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `image ls --filter` | শর্ত দিয়ে বাছাই |
| `login -u` | `u` = username → ব্যবহারকারীর নাম |
| `--password-stdin` | password from standard input → password pipe দিয়ে দাও, কমান্ড ইতিহাসে দেখা যাবে না |
| `--platform linux/arm64` | কোন OS/CPU-এর image |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `nginx:1.27-alpine` | `alpine` = ছোট Alpine Linux ভিত্তিক সংস্করণ |
| `git rev-parse --short HEAD` | বর্তমান commit-এর ছোট hash, tag হিসেবে |
| `docker tag উৎস নতুন_নাম` | একই image-এ নতুন নাম যোগ, কপি হয় না |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Tag আর digest-এর পার্থক্য?
**উত্তর:** Tag (`api:2.4.1`) বদলানো যায়, একই tag পরে অন্য image-কে নির্দেশ করতে পারে। Digest (`api@sha256:...`) image-এর content-এর hash, কখনো বদলায় না; production-এ সবচেয়ে নির্ভরযোগ্য।

**প্রশ্ন:** `--password-stdin` কেন?
**উত্তর:** `-p` দিয়ে password দিলে shell history আর process list-এ দেখা যায়।

### rmi, prune, history, save/load

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **rmi** | **R**e**m**ove **I**mage → image মুছো |
| **prune** | ছাঁটাই → অব্যবহৃত জিনিস একসাথে মুছো |
| **dangling** | ঝুলন্ত → নাম/tag হারানো image (`<none>`) |

**সহজ ভাষায়:** বারবার build করলে পুরনো image আর `<none>` image জমে disk ভরে যায়। `prune` দিয়ে পরিষ্কার করো। `history` দিয়ে দেখো কোন layer কত বড়। Internet ছাড়া সার্ভারে image নিতে `save` দিয়ে tar ফাইল বানিয়ে `load` করো।

> **বাস্তব পরিস্থিতি:** CI runner-এর disk ১০০% ভরা, build fail। `docker system df` দেখাল ৮০ GB পুরনো image আর build cache। `docker image prune -a` আর `builder prune` দিয়ে খালি করা হলো, আর একটা cron বসানো হলো।

```bash
docker rmi nginx:1.25
docker image prune
docker image prune -a --filter "until=168h"
docker image history api:2.4.1
docker save -o api.tar api:2.4.1
scp api.tar offline-server:/tmp/
docker load -i /tmp/api.tar
docker system df
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `image prune -a` | `a` = all → কোনো container ব্যবহার করছে না এমন সব image (শুধু dangling না) |
| `--filter until=168h` | ১৬৮ ঘণ্টা (৭ দিন)-এর বেশি পুরনো |
| `save -o` | `o` = output → কোন ফাইলে লেখা হবে |
| `load -i` | `i` = input → কোন ফাইল থেকে পড়বে |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `image history` | প্রতিটি layer কোন Dockerfile লাইন থেকে আর কত বড় |
| `system df` | image, container, volume, build cache কতটা জায়গা নিচ্ছে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Dangling image কী?
**উত্তর:** নতুন build একই tag নিয়ে নেওয়ায় পুরনো image-এর আর কোনো নাম নেই, তালিকায় `<none>:<none>` দেখায়। `docker image prune` এগুলো মোছে।

**প্রশ্ন:** `docker save` আর `docker export`-এর পার্থক্য?
**উত্তর:** `save` একটা image-কে সব layer, tag আর metadata সহ tar বানায় (`load` দিয়ে ফেরত)। `export` একটা container-এর filesystem-কে একটা চ্যাপ্টা tar বানায়, layer আর history ছাড়া (`import` দিয়ে ফেরত)।

---

## ০৩. Dockerfile লেখা: প্রতিটি instruction

Dockerfile হলো image বানানোর রেসিপি। প্রতিটি লাইন একটা instruction, আর বেশিরভাগ instruction একটা নতুন layer তৈরি করে।

### Dockerfile-এর মূল instruction গুলো

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **FROM** | কোন base image থেকে শুরু |
| **RUN** | build-এর সময় কমান্ড চালাও |
| **COPY** | host থেকে image-এ ফাইল কপি |
| **WORKDIR** | **Work**ing **Dir**ectory → কাজের ফোল্ডার |
| **CMD** | **C**o**m**man**d** → container চালু হলে default কমান্ড |
| **EXPOSE** | কোন port-এ অ্যাপ শোনে (শুধু তথ্য) |

**সহজ ভাষায়:** প্রতিটি Dockerfile `FROM` দিয়ে শুরু হয়। তারপর `WORKDIR` দিয়ে ফোল্ডার ঠিক করো, `COPY` দিয়ে ফাইল আনো, `RUN` দিয়ে dependency ইনস্টল করো, আর শেষে `CMD` দিয়ে বলো container চালু হলে কী চলবে। `RUN` চলে build-এর সময় (একবার), `CMD` চলে প্রতিবার container চালু হওয়ার সময়।

> **বাস্তব পরিস্থিতি:** একটা Python Flask API-কে container-এ নিতে হবে। Developer-এর মেশিনে যা চলে, CI আর production-এও যেন ঠিক তাই চলে।

```dockerfile
# Dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENV PORT=8000
EXPOSE 8000
RUN useradd --create-home appuser
USER appuser
CMD ["gunicorn", "-b", "0.0.0.0:8000", "app:app"]
```

| Instruction | কী করে | কখন চলে |
|---|---|---|
| `FROM python:3.12-slim` | এই image-এর উপর বানাও; `slim` = ছোট Debian সংস্করণ | build |
| `WORKDIR /app` | ফোল্ডার তৈরি করে সেখানে যাও; পরের সব কমান্ড এখানে | build (আর runtime-এ শুরুর ফোল্ডার) |
| `COPY requirements.txt .` | শুধু dependency ফাইল আগে কপি (cache-এর জন্য) | build |
| `RUN pip install ...` | dependency ইনস্টল, নতুন layer তৈরি | build |
| `COPY . .` | বাকি সব কোড (বর্তমান ফোল্ডার → `/app`) | build |
| `ENV PORT=8000` | environment variable, build ও runtime দুই জায়গায় থাকে | build + runtime |
| `EXPOSE 8000` | শুধু তথ্য: অ্যাপ 8000-এ শোনে; আসলে port খোলে না, `-p` লাগে | তথ্য |
| `USER appuser` | পরের কমান্ড আর container root ছাড়া এই user হিসেবে চলবে | build + runtime |
| `CMD [...]` | container চালু হলে এটা চলবে; `docker run`-এ অন্য কমান্ড দিলে বদলে যায় | runtime |

```bash
docker build -t flask-api:1.0 .
docker run -d -p 8000:8000 flask-api:1.0
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `RUN` আর `CMD`-এর পার্থক্য?
**উত্তর:** `RUN` image build-এর সময় চলে আর ফলাফল layer-এ সংরক্ষিত হয় (যেমন package install)। `CMD` build-এ চলে না; container চালু হওয়ার সময় default কমান্ড হিসেবে চলে।

**প্রশ্ন:** `EXPOSE` দিলেই কি port খুলে যায়?
**উত্তর:** না। `EXPOSE` শুধু documentation আর `-P` (সব exposed port random host port-এ)-এর জন্য। আসলে পৌঁছাতে `docker run -p` লাগে।

> 💡 **প্রো টিপ:** `COPY . .`-এর আগে `requirements.txt` আলাদা কপি করার কারণ পরের কার্ডে (layer cache) বোঝানো আছে।

### CMD বনাম ENTRYPOINT, exec form বনাম shell form

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ENTRYPOINT** | container-এর মূল executable |
| **PID 1** | **P**rocess **ID** 1 → container-এর প্রথম process |
| **exec form** | `["..."]` JSON array → shell ছাড়া সরাসরি চলে |

**সহজ ভাষায়:** `ENTRYPOINT` ঠিক করে container আসলে কোন প্রোগ্রাম; `CMD` তার default argument দেয়। `docker run image arg` দিলে `CMD` বদলায়, `ENTRYPOINT` থাকে। Exec form (`["node", "server.js"]`)-এ অ্যাপ নিজেই PID 1 হয় আর SIGTERM পায়। Shell form (`node server.js`)-এ `/bin/sh -c` PID 1 হয়, অ্যাপ SIGTERM পায় না, ফলে `docker stop`-এ ১০ সেকেন্ড অপেক্ষার পর জোর করে মরে।

> **বাস্তব পরিস্থিতি:** Kubernetes-এ প্রতিটি deploy-এ pod বন্ধ হতে ৩০ সেকেন্ড লাগছিল আর চলমান request কেটে যাচ্ছিল। কারণ Dockerfile-এ `CMD npm start` (shell form) ছিল, অ্যাপ SIGTERM পাচ্ছিল না। `CMD ["node", "server.js"]` করতেই সেকেন্ডে সুন্দরভাবে বন্ধ হতে লাগল।

```dockerfile
# একটা CLI টুলের image
ENTRYPOINT ["aws"]
CMD ["--version"]
# docker run awscli                      -> aws --version
# docker run awscli s3 ls                -> aws s3 ls (CMD বদলাল)
# docker run --entrypoint sh -it awscli  -> ENTRYPOINT বদলে shell

# ভালো (exec form)
CMD ["node", "server.js"]

# খারাপ (shell form, SIGTERM অ্যাপ পায় না)
CMD node server.js

# shell-এর কিছু লাগলে exec দিয়ে PID 1 হস্তান্তর
ENTRYPOINT ["/docker-entrypoint.sh"]
# docker-entrypoint.sh-এর শেষ লাইন: exec "$@"
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `ENTRYPOINT ["aws"]` | এই container সবসময় `aws` প্রোগ্রাম |
| `CMD ["--version"]` | কিছু না দিলে default argument |
| `--entrypoint sh` | debug-এর জন্য ENTRYPOINT বদলাও |
| `exec "$@"` | script নিজেকে মূল অ্যাপ দিয়ে প্রতিস্থাপন করে, ফলে অ্যাপ PID 1 আর signal পায় |
| `tini` / `--init` | PID 1 হিসেবে ছোট init; zombie process পরিষ্কার আর signal পাঠায় (`docker run --init`) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `CMD` আর `ENTRYPOINT`-এর পার্থক্য?
**উত্তর:** `ENTRYPOINT` মূল executable, `run`-এর argument-এর সাথে বদলায় না (`--entrypoint` ছাড়া)। `CMD` default কমান্ড বা `ENTRYPOINT`-এর default argument; `run`-এ argument দিলে বদলে যায়।

**প্রশ্ন:** Exec form কেন ভালো?
**উত্তর:** অ্যাপ সরাসরি PID 1 হয়, SIGTERM পায়, graceful shutdown হয়; shell form-এ `/bin/sh` মাঝখানে থাকে আর signal পাঠায় না।

### ARG, ENV, COPY বনাম ADD, LABEL, HEALTHCHECK

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ARG** | **Arg**ument → শুধু build-এর সময়ের variable |
| **ENV** | **Env**ironment → build ও runtime-এর variable |
| **ADD** | COPY + URL ডাউনলোড + tar খোলা |
| **LABEL** | image-এ metadata |
| **HEALTHCHECK** | container সুস্থ কিনা পরীক্ষার কমান্ড |

**সহজ ভাষায়:** `ARG` দিয়ে build-এর সময় মান দেওয়া যায় (`--build-arg`), কিন্তু চলমান container-এ থাকে না। `ENV` container চলার সময়ও থাকে। `COPY` সাধারণ কপি; `ADD` বাড়তি জাদু করে (URL, tar), তাই অপ্রত্যাশিত, `COPY`-ই পছন্দ। `HEALTHCHECK` দিলে `docker ps`-এ healthy/unhealthy দেখায়, আর Compose-এ `depends_on`-এ ব্যবহার করা যায়।

> **বাস্তব পরিস্থিতি:** একই Dockerfile থেকে Node 18 আর Node 20 দুই সংস্করণের image বানাতে হবে, image-এ git commit লেখা থাকবে, আর অ্যাপ সাড়া না দিলে unhealthy দেখাবে।

```dockerfile
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-alpine
ARG GIT_SHA=unknown
LABEL org.opencontainers.image.revision=$GIT_SHA \
      org.opencontainers.image.source="https://github.com/acme/web"
ENV NODE_ENV=production
WORKDIR /app
COPY --chown=node:node package*.json ./
RUN npm ci --omit=dev
COPY --chown=node:node . .
USER node
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "server.js"]

# build:
# docker build --build-arg NODE_VERSION=18 --build-arg GIT_SHA=$(git rev-parse --short HEAD) -t web:node18 .
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `ARG NODE_VERSION=20` (FROM-এর আগে) | FROM-এর image নির্বাচনে variable; default 20 |
| `${NODE_VERSION}` | ARG-এর মান বসাও |
| `LABEL org.opencontainers...` | OCI মানের metadata: কোন commit, কোন repo |
| `COPY --chown=node:node` | কপির সময়ই owner ঠিক করো, আলাদা chown layer লাগে না |
| `package*.json` | `package.json` আর `package-lock.json` দুটোই |
| `npm ci --omit=dev` | lock ফাইল হুবহু মেনে ইনস্টল, dev dependency বাদ |
| `HEALTHCHECK --interval --timeout --start-period --retries` | প্রতি ৩০ সেকেন্ডে, ৩ সেকেন্ডের মধ্যে উত্তর চাই, শুরুতে ১০ সেকেন্ড ছাড়, পরপর ৩ বার fail হলে unhealthy |
| `wget -qO- ... \|\| exit 1` | `q` = quiet, `O-` = output স্ক্রিনে; fail হলে `exit 1` মানে unhealthy |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `ARG` আর `ENV`-এর পার্থক্য?
**উত্তর:** `ARG` শুধু build-এর সময় থাকে, চলমান container-এ থাকে না। `ENV` image-এ সংরক্ষিত হয়, container চলার সময় থাকে।

**প্রশ্ন:** `ARG` দিয়ে secret পাঠানো কেন বিপজ্জনক?
**উত্তর:** `ARG`-এর মান `docker history` আর image metadata-তে দেখা যায়। Secret-এর জন্য BuildKit-এর `--secret` mount ব্যবহার করো।

**প্রশ্ন:** `COPY` আর `ADD`-এর পার্থক্য?
**উত্তর:** `ADD` URL থেকে ডাউনলোড আর local tar নিজে খুলতে পারে; `COPY` শুধু কপি করে। স্পষ্টতার জন্য `COPY` ব্যবহার করো।

### .dockerignore ও layer cache

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **layer** | স্তর → প্রতিটি instruction-এর ফলাফল |
| **cache** | build cache → আগের build-এর layer আবার ব্যবহার |
| **build context** | build-এর সময় daemon-কে পাঠানো ফোল্ডার |

**সহজ ভাষায়:** Docker প্রতিটি instruction-এর layer cache করে। কোনো লাইনের input না বদলালে আগেরটা ব্যবহার হয়; কিন্তু একটা লাইন বদলালে তার পরের সব লাইন আবার চলে। তাই যা কম বদলায় (dependency) আগে, যা বেশি বদলায় (কোড) পরে রাখো। `.dockerignore` দিয়ে `node_modules`, `.git`, secret ফাইল build context থেকে বাদ দাও, নইলে build ধীর হয় আর secret image-এ ঢুকে যেতে পারে।

> **বাস্তব পরিস্থিতি:** প্রতিবার কোডের এক লাইন বদলালেও build-এ ৫ মিনিট লাগছিল, কারণ `COPY . .` আগে ছিল আর তারপর `npm install`, ফলে প্রতিবার সব dependency নতুন করে নামছিল। `package.json` আগে কপি করে `npm ci` করার পর কোড কপি করায় build ২০ সেকেন্ডে নেমে এল।

```dockerfile
# খারাপ ক্রম: কোড বদলালেই npm ci আবার চলে
COPY . .
RUN npm ci

# ভালো ক্রম: package.json না বদলালে npm ci cache থেকে
COPY package*.json ./
RUN npm ci
COPY . .
```

```gitignore
# .dockerignore
.git
node_modules
*.log
.env
Dockerfile
docker-compose*.yml
coverage/
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `.git` | পুরো Git ইতিহাস image-এ দরকার নেই, আকার বাড়ায় |
| `node_modules` | host-এর module (অন্য OS-এর) কপি হবে না, container-এ নতুন করে ইনস্টল |
| `.env` | secret যেন কখনো image-এ না ঢোকে |
| `*.log` / `coverage/` | অপ্রয়োজনীয় ফাইল |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Docker layer caching কীভাবে কাজ করে?
**উত্তর:** প্রতিটি instruction আর তার input (COPY-এর ক্ষেত্রে ফাইলের checksum) আগের মতো হলে cache থেকে layer নেওয়া হয়। কোনো একটা miss হলে তার পরের সব layer নতুন করে বানানো হয়।

**প্রশ্ন:** `.dockerignore` না দিলে কী সমস্যা?
**উত্তর:** Build context বড় হয় (ধীর build), অপ্রয়োজনীয় ফাইল ঢোকে, cache বারবার ভাঙে, আর `.env` বা key-এর মতো secret image-এ চলে যেতে পারে।

### Multi-stage build: ছোট ও নিরাপদ image

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **multi-stage build** | একাধিক `FROM`, শেষ ধাপে শুধু দরকারি জিনিস |
| **distroless** | distro + less → shell ও package manager ছাড়া ক্ষুদ্র base image |
| **alpine** | Alpine Linux → খুব ছোট Linux distro (musl libc) |

**সহজ ভাষায়:** Build করতে compiler, dev dependency, source লাগে, কিন্তু চালাতে লাগে শুধু ফলাফল। Multi-stage-এ প্রথম stage-এ build হয়, শেষ stage-এ শুধু তৈরি জিনিস কপি হয়। ফলে image অনেক ছোট, দ্রুত pull হয়, আর আক্রমণের জায়গা কম।

> **বাস্তব পরিস্থিতি:** একটা React frontend-এর image ১.২ GB ছিল কারণ `node_modules` সহ সব ছিল। Multi-stage-এ Node দিয়ে build করে শুধু `dist` ফোল্ডার nginx image-এ কপি করায় image ২৫ MB।

```dockerfile
# ---- Stage 1: build ----
FROM node:20-alpine AS build
WORKDIR /src
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build            # তৈরি হয় /src/dist

# ---- Stage 2: serve ----
FROM nginx:1.27-alpine
COPY --from=build /src/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

```dockerfile
# Go-এর ক্ষেত্রে
FROM golang:1.23 AS build
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -ldflags="-s -w" -o /app ./cmd/api

FROM gcr.io/distroless/static-debian12:nonroot
COPY --from=build /app /app
ENTRYPOINT ["/app"]
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `FROM ... AS build` | এই stage-এর নাম `build` |
| `COPY --from=build` | আগের stage থেকে নির্দিষ্ট ফাইল আনো |
| `nginx:1.27-alpine` | চূড়ান্ত image-এ শুধু nginx আর static ফাইল |
| `distroless/static ...:nonroot` | shell নেই, root নেই, শুধু binary চালানোর মতো |
| `docker build --target build .` | শুধু একটা নির্দিষ্ট stage পর্যন্ত build (টেস্ট বা debug-এর জন্য) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Multi-stage build-এর সুবিধা কী?
**উত্তর:** চূড়ান্ত image-এ compiler, source আর build টুল থাকে না: ছোট আকার, দ্রুত deploy, কম vulnerability, একটা Dockerfile-এ পুরো build প্রক্রিয়া।

**প্রশ্ন:** Alpine image-এর সম্ভাব্য সমস্যা কী?
**উত্তর:** Alpine musl libc ব্যবহার করে, glibc না; কিছু binary বা Python wheel কাজ করে না বা নতুন করে compile করতে হয়, DNS আচরণেও পার্থক্য আছে। এমন হলে `debian-slim` বা distroless ভালো।

### Dockerfile best practices ও security

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CVE** | **C**ommon **V**ulnerabilities and **E**xposures → পরিচিত নিরাপত্তা দুর্বলতার তালিকা |
| **SBOM** | **S**oftware **B**ill **O**f **M**aterials → image-এ কোন কোন package আছে তার তালিকা |
| **Trivy** | vulnerability scanner |

**সহজ ভাষায়:** ভালো image ছোট, নির্দিষ্ট version-এ বাঁধা, root ছাড়া চলে, secret ছাড়া, আর নিয়মিত scan করা। `RUN` কমান্ডগুলো জুড়ে দাও আর একই লাইনে cache পরিষ্কার করো, নইলে মুছে ফেলা ফাইলও আগের layer-এ থেকে যায়।

> **বাস্তব পরিস্থিতি:** Security audit-এ ধরা পড়ল production image-এ ২০০+ critical CVE, root হিসেবে চলছে, আর একটা layer-এ AWS key আছে (পরে `rm` করা হলেও history-তে রয়ে গেছে)। এই নিয়মগুলো মেনে নতুন করে লেখা হলো আর CI-তে Trivy scan বাধ্যতামূলক করা হলো।

```dockerfile
# ভালো: এক RUN-এ install + পরিষ্কার
RUN apt-get update \
 && apt-get install -y --no-install-recommends curl ca-certificates \
 && rm -rf /var/lib/apt/lists/*

# BuildKit cache mount: dependency cache image-এ ঢোকে না, কিন্তু build দ্রুত
RUN --mount=type=cache,target=/root/.cache/pip pip install -r requirements.txt

# BuildKit secret: token image-এ থাকে না
RUN --mount=type=secret,id=npmrc,target=/root/.npmrc npm ci
# docker build --secret id=npmrc,src=$HOME/.npmrc .
```

```bash
# scan
trivy image --severity HIGH,CRITICAL api:2.4.1
docker scout cves api:2.4.1
```

| নিয়ম | কেন |
|---|---|
| নির্দিষ্ট tag (`python:3.12.6-slim`), `:latest` নয় | পুনরুৎপাদনযোগ্য build, হঠাৎ বদল নেই |
| ছোট base (slim, alpine, distroless) | কম আকার, কম CVE |
| `USER` দিয়ে non-root | container ভাঙলেও host-এ কম ক্ষতি |
| `COPY`, `ADD` নয় | অপ্রত্যাশিত আচরণ নেই |
| RUN জুড়ে দাও, একই লাইনে cache মোছো | কম layer, ছোট image |
| secret কখনো ENV/ARG/COPY-তে না | history আর layer-এ থেকে যায়; `--secret` ব্যবহার |
| কম বদলানো লাইন আগে | cache ভালো কাজ করে |
| HEALTHCHECK আর exec form CMD | সুস্থতা পরীক্ষা ও সঠিক shutdown |
| CI-তে scan (Trivy/Scout) আর নিয়মিত rebuild | নতুন CVE-এর patch পাওয়া |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Image-এ একবার ঢোকানো secret পরের RUN-এ মুছে দিলে কি নিরাপদ?
**উত্তর:** না। আগের layer-এ ফাইলটা থেকেই যায়, `docker save` বা history দিয়ে বের করা যায়। Secret কখনো layer-এ ঢোকানো যাবে না; BuildKit secret mount বা runtime-এ পাঠাও।

**প্রশ্ন:** Image-এর আকার কীভাবে কমাবে?
**উত্তর:** ছোট base image, multi-stage build, `.dockerignore`, RUN জুড়ে দিয়ে একই লাইনে cache মোছা, `--no-install-recommends`, dev dependency বাদ, আর `docker history` দিয়ে বড় layer খোঁজা।

---

## ০৪. Image build: docker build ও buildx

Dockerfile থেকে image বানানো, নাম দেওয়া, আলাদা পরিবেশের জন্য পরিবর্তন, আর একসাথে amd64 ও arm64 image।

### docker build-এর অপশন

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **build** | Dockerfile থেকে image বানাও |
| **BuildKit** | Docker-এর আধুনিক build ইঞ্জিন (এখন default) |
| **context** | build context → শেষের `.` = এই ফোল্ডার |

**সহজ ভাষায়:** `docker build -t নাম:tag .`-এর শেষের ডট খুব গুরুত্বপূর্ণ: এটা build context, অর্থাৎ `COPY` কোন ফোল্ডার থেকে ফাইল নেবে। Dockerfile অন্য নামে বা অন্য জায়গায় থাকলে `-f` দিয়ে বলো।

> **বাস্তব পরিস্থিতি:** একই repo-তে `Dockerfile` (production) আর `Dockerfile.dev` আছে। CI-তে commit hash সহ tag, cache ছাড়া পরিষ্কার build, আর প্রয়োজনে শুধু test stage পর্যন্ত build করতে হবে।

```bash
docker build -t api:2.4.1 .
docker build -t api:2.4.1 -t api:latest .
docker build -f Dockerfile.dev -t api:dev .
docker build --build-arg GIT_SHA=$(git rev-parse --short HEAD) -t api:ci .
docker build --no-cache --pull -t api:2.4.1 .
docker build --target test -t api:test .
docker build --progress=plain -t api:debug .
docker build -t api:2.4.1 ./services/api
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `-t` | `t` = tag → image-এর `নাম:tag` (একাধিক `-t` দেওয়া যায়) |
| `-f` | `f` = file → কোন Dockerfile |
| `--build-arg` | build argument → Dockerfile-এর ARG-এ মান দাও |
| `--no-cache` | সব layer নতুন করে বানাও |
| `--pull` | base image-এর সর্বশেষ সংস্করণ টেনে নাও |
| `--target` | target stage → multi-stage-এর এই stage পর্যন্ত |
| `--progress=plain` | সব output বিস্তারিত দেখাও (debug-এর জন্য) |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| শেষের `.` | build context: বর্তমান ফোল্ডার |
| `./services/api` | context অন্য ফোল্ডার, monorepo-তে কাজে লাগে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Build context কী আর কেন বড় হলে সমস্যা?
**উত্তর:** শেষে দেওয়া ফোল্ডারের সব ফাইল (ignore বাদে) Docker daemon-এ পাঠানো হয়; বড় হলে build ধীর হয় আর অপ্রয়োজনীয় ফাইল image-এ যাওয়ার ঝুঁকি।

**প্রশ্ন:** `--no-cache` কখন দরকার?
**উত্তর:** Base image বা apt থেকে security update জোর করে নিতে, বা cache-এর কারণে অদ্ভুত build সমস্যা হলে। সাধারণত `--pull`-এর সাথে নিয়মিত rebuild-এ।

### buildx: multi-platform (amd64 + arm64)

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **buildx** | build e**X**tended → BuildKit-এর উন্নত build CLI |
| **amd64 / arm64** | AMD 64-bit / ARM 64-bit → Intel/AMD বনাম ARM (Apple M-series, AWS Graviton) |
| **QEMU** | **Q**uick **EMU**lator → অন্য CPU-এর অনুকরণ |
| **manifest list** | একই tag-এর অধীনে একাধিক platform-এর image |

**সহজ ভাষায়:** Apple M1/M2/M3 ল্যাপটপে build করা image arm64; সেটা Intel সার্ভারে চালালে `exec format error`। buildx দিয়ে একসাথে দুটো platform-এর image বানিয়ে একই tag-এ push করা যায়; যে মেশিন pull করে সে নিজের platform-এরটা নেয়।

> **বাস্তব পরিস্থিতি:** কোম্পানি খরচ কমাতে AWS Graviton (arm64)-এ যাচ্ছে, কিন্তু কিছু সার্ভার এখনও x86। একই image tag দুই ধরনের সার্ভারেই চলতে হবে।

```bash
docker buildx create --name multi --use
docker buildx inspect --bootstrap
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t ghcr.io/acme/api:2.4.1 \
  --push .
docker buildx imagetools inspect ghcr.io/acme/api:2.4.1

# cache registry-তে রাখা (CI-তে দ্রুত build)
docker buildx build \
  --cache-from type=registry,ref=ghcr.io/acme/api:cache \
  --cache-to type=registry,ref=ghcr.io/acme/api:cache,mode=max \
  -t ghcr.io/acme/api:2.4.1 --push .
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `buildx create --use` | এই builder-কে default বানাও |
| `inspect --bootstrap` | builder চালু করে প্রস্তুত করো |
| `--platform` | কোন কোন OS/CPU-এর জন্য build |
| `--push` | build শেষে সরাসরি registry-তে পাঠাও (multi-platform image local-এ load হয় না) |
| `--cache-from` / `--cache-to` | কোথা থেকে cache পড়বে / কোথায় cache লিখবে |
| `mode=max` | maximum → মাঝের stage-এর layer-ও cache-এ রাখো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `exec format error` কেন আসে?
**উত্তর:** Image বা binary অন্য CPU architecture-এর জন্য তৈরি (যেমন arm64 image x86 সার্ভারে)। সঠিক `--platform`-এ build করো বা multi-platform image বানাও।

---

## ০৫. Storage: Volume, Bind mount, tmpfs

Container মুছলে তার ভেতরের data-ও মুছে যায়। Database, upload, log টিকিয়ে রাখতে storage বুঝতেই হবে।

### Volume বনাম Bind mount বনাম tmpfs

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **volume** | Docker পরিচালিত স্থায়ী storage |
| **bind mount** | host-এর নির্দিষ্ট ফোল্ডার সরাসরি যুক্ত |
| **tmpfs** | **T**e**mp**orary **F**ile **S**ystem → শুধু RAM-এ, container থামলে মুছে যায় |

**সহজ ভাষায়:** Container-এর নিজের লেখার স্তর অস্থায়ী। Volume হলো Docker-এর নিজের জায়গায় (`/var/lib/docker/volumes`) রাখা storage, container মুছলেও থাকে: database-এর জন্য সেরা। Bind mount host-এর একটা নির্দিষ্ট path সরাসরি দেখায়: development-এ কোড বদলালে সাথে সাথে container-এ দেখা যায়, বা host-এর config ফাইল দিতে। tmpfs RAM-এ: অস্থায়ী, দ্রুত, disk-এ কিছু লেখে না।

> **বাস্তব পরিস্থিতি:** Developer `docker rm` দিয়ে PostgreSQL container মুছে নতুন version চালাল, আর সব data হারিয়ে গেল কারণ কোনো volume ছিল না। আবার development-এ প্রতিবার কোড বদলালে image rebuild করতে হচ্ছিল; bind mount দিয়ে কোড ফোল্ডার যুক্ত করায় hot-reload চলতে লাগল।

```bash
# Named volume (database)
docker volume create pgdata
docker run -d --name pg -v pgdata:/var/lib/postgresql/data postgres:16

# Bind mount (development / config)
docker run -d -v "$PWD"/src:/app/src -p 3000:3000 web:dev
docker run -d -v /etc/myapp/nginx.conf:/etc/nginx/nginx.conf:ro nginx:1.27

# --mount: লম্বা কিন্তু স্পষ্ট রূপ
docker run -d --mount type=volume,source=pgdata,target=/var/lib/postgresql/data postgres:16
docker run -d --mount type=tmpfs,target=/tmp,tmpfs-size=64m api:2.4.1

docker volume ls
docker volume inspect pgdata
docker volume rm pgdata
docker volume prune
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `-v নাম:path` | `v` = volume → নাম দিলে named volume, `/` দিয়ে শুরু path দিলে bind mount |
| `:ro` | `ro` = read-only → container শুধু পড়তে পারবে, লিখতে না |
| `--mount type=...` | `type` → volume, bind বা tmpfs · `source` → কোন volume/path · `target` → container-এর কোথায় |

| ধরন | কোথায় থাকে | কখন ব্যবহার |
|---|---|---|
| Named volume | Docker-এর এলাকা (`/var/lib/docker/volumes`) | database, স্থায়ী data, production |
| Bind mount | host-এর যেকোনো path | development-এ কোড, host-এর config/certificate |
| tmpfs | RAM | অস্থায়ী, সংবেদনশীল ফাইল, দ্রুত cache |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Volume আর bind mount-এর পার্থক্য?
**উত্তর:** Volume Docker পরিচালনা করে, path নিয়ে ভাবতে হয় না, backup/driver সুবিধা, Mac/Windows-এ দ্রুত। Bind mount host-এর নির্দিষ্ট path-এর উপর নির্ভর করে, host-এর ফাইল সরাসরি বদলায়, development-এ উপযোগী।

**প্রশ্ন:** `docker rm` করলে volume কি মুছে যায়?
**উত্তর:** না, named volume থেকে যায়। `docker rm -v` দিলে শুধু ওই container-এর anonymous volume মোছে; named volume মুছতে `docker volume rm` লাগে।

> 💡 **প্রো টিপ:** Bind mount-এ permission সমস্যা খুব কমন: container-এর user (UID) আর host ফোল্ডারের owner না মিললে `Permission denied`। UID মিলিয়ে নাও বা `--user` দাও।

### Volume backup ও restore

`🟡 ইন্টারমিডিয়েট`

**সহজ ভাষায়:** Volume-এর data backup নিতে একটা অস্থায়ী container চালাও যেটা volume আর একটা host ফোল্ডার দুটোই mount করে, তারপর tar দিয়ে সংকুচিত করে। Database হলে আগে নিজস্ব টুল (`pg_dump`) ব্যবহার করা নিরাপদ, কারণ চলমান DB-এর ফাইল কপি অসম্পূর্ণ হতে পারে।

> **বাস্তব পরিস্থিতি:** সার্ভার বদলানোর আগে `uploads` volume-এর পুরো data নতুন সার্ভারে নিতে হবে।

```bash
# backup
docker run --rm -v uploads:/data:ro -v "$PWD":/backup alpine \
  tar czf /backup/uploads-$(date +%F).tar.gz -C /data .

# restore (নতুন সার্ভারে)
docker volume create uploads
docker run --rm -v uploads:/data -v "$PWD":/backup alpine \
  tar xzf /backup/uploads-2026-10-06.tar.gz -C /data

# PostgreSQL-এর ঠিক উপায়
docker exec pg pg_dump -U postgres appdb | gzip > appdb-$(date +%F).sql.gz
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `tar czf` | `c` = create → archive বানাও · `z` = gzip → সংকুচিত · `f` = file → ফাইলের নাম |
| `tar xzf` | `x` = extract → খোলো · `z` = gzip · `f` = file → কোন ফাইল |
| `-C /data` | `C` = Change directory → এই ফোল্ডারে গিয়ে কাজ করো |
| `pg_dump -U` | `U` = User → database user |

---

## ০৬. Docker Networking

Container রা একে অপরকে কীভাবে খুঁজে পায়, বাইরে থেকে কীভাবে পৌঁছানো যায়।

### Network driver: bridge, host, none ও DNS

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **bridge** | host-এর ভেতরে ভার্চুয়াল switch |
| **host** | host network → container সরাসরি host-এর network ব্যবহার করে |
| **none** | কোনো network নেই |
| **overlay** | একাধিক host জুড়ে network (Swarm) |
| **embedded DNS** | `127.0.0.11` → Docker-এর ভেতরের DNS |

**সহজ ভাষায়:** Default bridge network-এ container গুলো IP দিয়ে কথা বলতে পারে কিন্তু নাম দিয়ে না। নিজে বানানো (user-defined) bridge network-এ Docker-এর ভেতরের DNS থাকে, তাই container-এর নাম দিয়েই পৌঁছানো যায় (`http://api:8000`)। Compose নিজে থেকে এমন network বানায়। Host network-এ port mapping লাগে না (Linux-এ), কিন্তু isolation নেই।

> **বাস্তব পরিস্থিতি:** Web container `localhost:5432` দিয়ে database-এ যেতে চাইছে আর `connection refused` পাচ্ছে। কারণ container-এর ভেতরে localhost মানে সেই container নিজে, database নয়। একই user-defined network-এ রেখে host হিসেবে `db` (container-এর নাম) দিতেই কাজ করল।

```bash
docker network create appnet
docker run -d --name db --network appnet -e POSTGRES_PASSWORD=secret postgres:16
docker run -d --name api --network appnet -e DB_HOST=db -p 8000:8000 api:2.4.1
docker exec api getent hosts db          # নাম -> IP
docker network ls
docker network inspect appnet
docker network connect appnet old-container
docker network disconnect appnet old-container
docker run --rm --network container:api nicolaka/netshoot ss -tlnp

# host-এর সার্ভিসে container থেকে যাওয়া
docker run --add-host=host.docker.internal:host-gateway alpine ping host.docker.internal
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--network appnet` | এই network-এ যুক্ত করো |
| `network connect` / `disconnect` | চলমান container-কে যুক্ত / বিচ্ছিন্ন করো |
| `--network container:api` | api container-এর network namespace শেয়ার (debug-এর জন্য) |
| `--add-host` | `/etc/hosts`-এ নাম যোগ; `host-gateway` = host-এর IP |

| Driver | কখন |
|---|---|
| bridge (user-defined) | এক host-এ অ্যাপের container গুলো; নাম দিয়ে DNS |
| host | সর্বোচ্চ network পারফরম্যান্স বা অনেক port; isolation দরকার নেই |
| none | network একদম দরকার নেই, নিরাপত্তা |
| overlay | Docker Swarm-এ একাধিক host জুড়ে |
| macvlan | container-কে LAN-এ নিজের MAC/IP সহ আলাদা যন্ত্রের মতো দেখানো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Container-এর ভেতরে localhost কী বোঝায়?
**উত্তর:** সেই container নিজেকে। অন্য container-এ যেতে container/service-এর নাম, আর host-এ যেতে `host.docker.internal` বা host-এর IP।

**প্রশ্ন:** Default bridge আর user-defined bridge-এর পার্থক্য?
**উত্তর:** User-defined bridge-এ স্বয়ংক্রিয় নাম-ভিত্তিক DNS, ভালো isolation, আর চলমান অবস্থায় যুক্ত/বিচ্ছিন্ন করা যায়; default bridge-এ নাম দিয়ে খোঁজা যায় না (পুরনো `--link` ছাড়া)।

**প্রশ্ন:** `-p 8080:80` কীভাবে কাজ করে?
**উত্তর:** Docker host-এ iptables NAT নিয়ম (DNAT) বসায়, host-এর 8080-এ আসা traffic container-এর IP:80-তে পাঠায়। এজন্য ufw চালু থাকলেও Docker-এর publish করা port বাইরে খোলা থাকতে পারে, সাবধান।

---

## ০৭. Docker Compose: মাল্টি-কন্টেইনার অ্যাপ

বাস্তব অ্যাপে web, API, database, cache, queue একসাথে চলে। প্রত্যেকটা আলাদা `docker run` দিয়ে চালানো অসম্ভব; Compose একটা YAML ফাইলে সব বর্ণনা করে একটা কমান্ডে চালায়।

### Compose ফাইলের গঠন

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Compose** | Docker Compose → একাধিক container একসাথে সাজানো |
| **YAML** | **Y**AML **A**in't **M**arkup **L**anguage → config লেখার ফরম্যাট |
| **service** | একটা অ্যাপের অংশ (web, db), এক বা একাধিক container |

**সহজ ভাষায়:** `compose.yaml` (বা `docker-compose.yml`)-এ services, volumes, networks লেখা হয়। প্রতিটি service-এর জন্য image বা build, ports, environment, volumes, depends_on ইত্যাদি। Compose নিজে একটা network বানায় আর প্রতিটি service-কে তার নামে DNS দেয়। এখনকার কমান্ড `docker compose` (মাঝে space); পুরনো `docker-compose` (হাইফেন) আর `version:` লাইন দুটোই অপ্রচলিত।

> **বাস্তব পরিস্থিতি:** নতুন developer join করেছে। আগে README-এর ২০ ধাপ মেনে Postgres, Redis, API, frontend ইনস্টল করতে পুরো দিন লাগত। এখন `git clone`, তারপর `docker compose up`: ৫ মিনিটে পুরো stack চালু।

```yaml
# compose.yaml
services:
  web:
    build: ./web
    ports:
      - "80:80"
    depends_on:
      - api

  api:
    build:
      context: ./api
      dockerfile: Dockerfile
    environment:
      DATABASE_URL: postgres://app:${DB_PASSWORD}@db:5432/app
      REDIS_URL: redis://cache:6379
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    restart: unless-stopped

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: app
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d app"]
      interval: 5s
      timeout: 3s
      retries: 10

  cache:
    image: redis:7-alpine

volumes:
  pgdata:
```

| Key | মানে |
|---|---|
| `services:` | সব অংশের তালিকা; প্রতিটির নাম (web, api, db) নেটওয়ার্কে hostname-ও |
| `build: ./web` | এই ফোল্ডারের Dockerfile থেকে image বানাও |
| `build.context` / `dockerfile` | context ফোল্ডার আর Dockerfile-এর নাম আলাদা করে |
| `image: postgres:16` | তৈরি image ব্যবহার করো |
| `ports: "80:80"` | host:container; quote দাও যাতে YAML সংখ্যা ভুল না বোঝে |
| `environment:` | environment variable; `${DB_PASSWORD}` আসে `.env` ফাইল বা shell থেকে |
| `env_file: .env` | ফাইলের সব variable container-এ দাও |
| `depends_on` + `condition: service_healthy` | db সুস্থ (healthcheck pass) হওয়ার পরেই api চালু |
| `restart: unless-stopped` | crash হলে নিজে চালু |
| `volumes: pgdata:/var/...` | named volume, নিচের top-level `volumes:`-এ ঘোষণা |
| `healthcheck:` | db প্রস্তুত কিনা পরীক্ষা (`pg_isready`) |
| `@db:5432` | service-এর নাম `db`-ই hostname, localhost নয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `depends_on` কি নিশ্চিত করে যে database প্রস্তুত?
**উত্তর:** শুধু `depends_on` (short form) কেবল চালুর ক্রম ঠিক করে, প্রস্তুতি না। প্রস্তুতি নিশ্চিত করতে db-তে healthcheck আর api-তে `condition: service_healthy` দিতে হয়; তবুও অ্যাপে retry রাখা ভালো।

**প্রশ্ন:** Compose-এ service গুলো একে অপরকে কীভাবে খুঁজে পায়?
**উত্তর:** Compose প্রতিটি প্রজেক্টের জন্য একটা default network বানায়, আর service-এর নামকে DNS নাম হিসেবে দেয় (`db`, `cache`)।

> 💡 **প্রো টিপ:** Secret (`.env`) Git-এ commit করো না; `.env.example` রাখো যাতে বোঝা যায় কী কী variable লাগবে।

### Compose কমান্ড

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **up** | তৈরি করে চালু করো |
| **down** | থামিয়ে container ও network মোছো |
| **project** | compose project → একটা compose ফাইলের পুরো stack, নাম default-এ ফোল্ডারের নাম |

**সহজ ভাষায়:** সব কমান্ড `compose.yaml` যে ফোল্ডারে আছে সেখান থেকে চালাও (বা `-f` দিয়ে ফাইল দাও)। Service-এর নাম দিয়ে নির্দিষ্ট অংশে কাজ করা যায়।

> **বাস্তব পরিস্থিতি:** API-তে বাগ ঠিক করে শুধু api আবার build করে চালু করতে হবে, বাকি stack না থামিয়ে। তারপর api-এর log দেখা আর database-এ ঢুকে query চালানো।

```bash
docker compose up -d
docker compose up -d --build api
docker compose ps
docker compose logs -f api
docker compose logs --tail 50 db
docker compose exec db psql -U app -d app
docker compose run --rm api python manage.py migrate
docker compose restart api
docker compose stop
docker compose down
docker compose down -v
docker compose pull
docker compose build --no-cache
docker compose config
docker compose up -d --scale worker=3
docker compose -f compose.yaml -f compose.prod.yaml up -d
docker compose -p shop-staging up -d
docker compose top
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `up -d` | `d` = detach → পেছনে চালাও |
| `up --build` | চালুর আগে image আবার build করো |
| `logs -f` | `f` = follow → live log |
| `--tail 50` | শেষ ৫০ লাইন |
| `exec db psql` | execute → চলমান db service-এ কমান্ড চালাও |
| `run --rm api ...` | একবারের কাজের জন্য নতুন container; `rm` = remove → শেষে মোছো |
| `down -v` | `v` = volumes → named volume-ও মুছবে, data হারাবে! |
| `config` | সব variable বসিয়ে চূড়ান্ত YAML দেখাও, ভুল ধরতে |
| `--scale worker=3` | এই service-এর ৩টা container চালাও |
| `-f a -f b` | `f` = file → একাধিক ফাইল মিলিয়ে, পরেরটা আগেরটার উপর override করে |
| `-p` | `p` = project name → প্রজেক্টের নাম, একই ফাইল আলাদা নামে একাধিক stack |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `docker compose down` আর `stop`-এর পার্থক্য?
**উত্তর:** `stop` শুধু container থামায়, সব কিছু থাকে। `down` container আর network মুছে দেয়; `-v` দিলে volume-ও মুছে data হারায়।

**প্রশ্ন:** Compose-এ `exec` আর `run`-এর পার্থক্য?
**উত্তর:** `exec` চলমান container-এ কমান্ড চালায়। `run` একই service-এর config দিয়ে নতুন একবারের container বানায় (migration, test-এর জন্য)।

**প্রশ্ন:** `--scale` কেন কখনো কাজ করে না?
**উত্তর:** Service-এ `container_name` বা নির্দিষ্ট host port (`"8000:8000"`) থাকলে একাধিক container একই নাম/port নিতে পারে না।

### Development workflow: override, hot reload, profiles

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **override** | `compose.override.yaml` → স্বয়ংক্রিয়ভাবে মেশানো ফাইল |
| **profile** | নির্দিষ্ট অবস্থায় চালানো service-এর দল |
| **watch** | Compose watch → ফাইল বদলালে নিজে sync/rebuild |

**সহজ ভাষায়:** `compose.override.yaml` থাকলে `docker compose up` নিজে থেকেই সেটা মিশিয়ে নেয়: সেখানে development-এর জিনিস (bind mount, debug port) রাখো, আর production-এর জন্য আলাদা ফাইল। Profile দিয়ে মাঝে মাঝে লাগে এমন service (pgadmin, mailhog) আলাদা রাখা যায়। Compose watch কোড বদলালে container-এ sync বা rebuild করে।

> **বাস্তব পরিস্থিতি:** সবাই একই `compose.yaml` চায়, কিন্তু developer রা চায় কোড বদলালে সাথে সাথে reload আর DB দেখার জন্য pgadmin; production-এ এগুলো লাগবে না।

```yaml
# compose.override.yaml (শুধু development, নিজে মেশে)
services:
  api:
    volumes:
      - ./api:/app
    environment:
      DEBUG: "1"
    command: ["uvicorn", "main:app", "--host", "0.0.0.0", "--reload"]

  pgadmin:
    image: dpage/pgadmin4
    profiles: ["tools"]
    ports: ["5050:80"]

# compose.yaml-এ watch (Compose 2.22+)
#  api:
#    develop:
#      watch:
#        - action: sync
#          path: ./api
#          target: /app
#        - action: rebuild
#          path: ./api/requirements.txt
```

```bash
docker compose up -d                                         # override নিজে মেশে
docker compose --profile tools up -d                         # pgadmin সহ
docker compose watch
docker compose -f compose.yaml -f compose.prod.yaml up -d    # production, override বাদ
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `volumes: ./api:/app` | bind mount: host-এর কোড সরাসরি container-এ |
| `command:` | image-এর CMD বদলে development-এর কমান্ড (`--reload`) |
| `profiles: ["tools"]` | এই service শুধু `--profile tools` দিলে চলবে |
| `action: sync` | ফাইল বদলালে container-এ কপি করো |
| `action: rebuild` | এই ফাইল বদলালে image আবার build করো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `compose.override.yaml` কীভাবে কাজ করে?
**উত্তর:** `-f` না দিলে Compose `compose.yaml` আর `compose.override.yaml` দুটো পড়ে মিশিয়ে নেয়; পরের ফাইলের মান আগেরটার উপর বসে (list গুলো কিছু ক্ষেত্রে যোগ হয়)।

### পূর্ণ উদাহরণ: Nginx + API + Worker + Postgres + Redis

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **reverse proxy** | সামনে বসে request পেছনের সার্ভিসে পাঠায় |
| **worker** | background worker → পেছনে job প্রসেস করে (Celery, Sidekiq) |

**সহজ ভাষায়:** বাস্তবের মতো একটা ছোট production-স্টাইল stack: শুধু nginx বাইরে খোলা, বাকিগুলো ভেতরের network-এ; database শুধু backend network-এ; resource সীমা, log rotation, healthcheck আর restart নীতি সহ। একটা VM-এ ছোট প্রজেক্টের জন্য এটা যথেষ্ট; বড় হলে Kubernetes।

> **বাস্তব পরিস্থিতি:** একটা স্টার্টআপ একটা ৪ GB VPS-এ পুরো প্রোডাক্ট চালাবে। নিরাপত্তার জন্য database বাইরে থেকে পৌঁছানো যাবে না, log disk ভরাবে না, আর কোনো container crash করলে নিজে চালু হবে।

```yaml
x-logging: &default-logging
  driver: json-file
  options:
    max-size: "10m"
    max-file: "3"

services:
  nginx:
    image: nginx:1.27-alpine
    ports: ["80:80", "443:443"]
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./certs:/etc/nginx/certs:ro
    depends_on: [api]
    networks: [frontend]
    restart: unless-stopped
    logging: *default-logging

  api:
    image: ghcr.io/acme/api:${API_TAG:-2.4.1}
    env_file: .env
    networks: [frontend, backend]
    depends_on:
      db: { condition: service_healthy }
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:8000/health"]
      interval: 15s
    deploy:
      resources:
        limits: { cpus: "1.0", memory: 512M }
    restart: unless-stopped
    logging: *default-logging

  worker:
    image: ghcr.io/acme/api:${API_TAG:-2.4.1}
    command: ["celery", "-A", "app", "worker", "-l", "info"]
    env_file: .env
    networks: [backend]
    restart: unless-stopped

  db:
    image: postgres:16
    env_file: .env.db
    volumes: [pgdata:/var/lib/postgresql/data]
    networks: [backend]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER}"]
      interval: 5s
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    command: ["redis-server", "--appendonly", "yes"]
    volumes: [redisdata:/data]
    networks: [backend]

networks:
  frontend:
  backend:
    internal: true

volumes:
  pgdata:
  redisdata:
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `x-logging: &default-logging` | `x-` দিয়ে শুরু = extension; `&` = YAML anchor, একবার লিখে বারবার ব্যবহার |
| `logging: *default-logging` | `*` = anchor-এর মান বসাও; log ১০ MB-এর ৩টা ফাইলে সীমিত |
| `${API_TAG:-2.4.1}` | `API_TAG` না থাকলে `2.4.1` |
| `networks: [frontend, backend]` | api দুই network-এ: nginx ও db দুজনের সাথে কথা বলে |
| `internal: true` | backend network থেকে বাইরে (internet) যাওয়া যাবে না, ভেতরেও কেউ ঢুকবে না |
| `deploy.resources.limits` | CPU আর memory সীমা (`docker compose`-এ কার্যকর) |
| `$${POSTGRES_USER}` | `$$` = আক্ষরিক `$`, যাতে Compose না বসিয়ে container-এর shell বসায় |
| `--appendonly yes` | Redis data disk-এ লিখে রাখবে (AOF), restart-এ হারাবে না |
| একই image দুই service-এ | api আর worker একই কোড, শুধু command আলাদা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Compose কি production-এ ব্যবহারযোগ্য?
**উত্তর:** একটা সার্ভারে ছোট/মাঝারি প্রজেক্টের জন্য হ্যাঁ (restart policy, healthcheck, limit, log rotation সহ)। কিন্তু একাধিক সার্ভার, auto-scaling, rolling update, self-healing লাগলে Kubernetes বা ECS ভালো।

**প্রশ্ন:** Docker Compose আর Kubernetes-এর পার্থক্য?
**উত্তর:** Compose এক host-এ একাধিক container চালানোর সহজ টুল। Kubernetes অনেক node-এর cluster-এ scheduling, scaling, self-healing, service discovery, rolling deploy করে।

---

## ০৮. Production: resource limit, logging ও security

ল্যাপটপে যা চলে, production-এ সেটা সীমা, log নিয়ন্ত্রণ আর নিরাপত্তা ছাড়া চালালে একদিন বিপদ হবে।

### CPU/Memory সীমা ও OOM

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **OOM** | **O**ut **O**f **M**emory → memory শেষ |
| **cgroup** | **C**ontrol **Group** → resource সীমা দেওয়ার kernel ব্যবস্থা |
| **exit 137** | 128 + 9 (SIGKILL) → জোর করে মেরে ফেলা হয়েছে |

**সহজ ভাষায়:** সীমা না দিলে একটা container পুরো সার্ভারের memory খেয়ে অন্য সব কিছু বসিয়ে দিতে পারে। `--memory` দিলে সীমা পার হলে kernel ওই container-কে মেরে ফেলে (exit 137, `OOMKilled true`)। `--cpus` দিয়ে কতগুলো CPU core-এর সমান ব্যবহার করতে পারবে ঠিক হয়।

> **বাস্তব পরিস্থিতি:** Java API-তে memory leak হয়ে পুরো সার্ভার hang করল, database-ও মরল। `--memory 1g` দেওয়ার পর শুধু API container restart হয়, বাকি সব চলতে থাকে, আর alert থেকে leak-এর কথা জানা গেল।

```bash
docker run -d --name api --memory 1g --memory-swap 1g --cpus 1.5 --pids-limit 200 api:2.4.1
docker stats --no-stream
docker inspect -f '{{.State.OOMKilled}} {{.State.ExitCode}}' api
docker update --memory 2g --memory-swap 2g api
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--memory 1g` | memory limit → সর্বোচ্চ RAM |
| `--memory-swap 1g` | memory + swap → memory-এর সমান দিলে swap ব্যবহার করবে না |
| `--cpus 1.5` | দেড়টা core-এর সমান সময় |
| `--pids-limit 200` | process ID limit → সর্বোচ্চ ২০০টা process (fork bomb ঠেকাতে) |

| Exit code | মানে |
|---|---|
| 0 | স্বাভাবিকভাবে শেষ |
| 1 | অ্যাপের সাধারণ error |
| 125 | `docker run` নিজেই fail (ভুল অপশন) |
| 126 | কমান্ড চালানো যায় না (permission) |
| 127 | কমান্ড পাওয়া যায়নি (ভুল path, shell নেই) |
| 137 | SIGKILL (128+9): OOM বা `docker kill` |
| 143 | SIGTERM (128+15): `docker stop`-এ ঠিকমতো বন্ধ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Container exit code 137 দিলে কী বুঝবে?
**উত্তর:** SIGKILL পেয়েছে। `docker inspect`-এ `OOMKilled true` হলে memory সীমা পেরিয়েছে; না হলে কেউ kill করেছে বা stop-এর grace period শেষে মারা হয়েছে।

**প্রশ্ন:** Java/Node container-এ memory সীমা দিলে কী খেয়াল রাখবে?
**উত্তর:** Runtime যেন container-এর সীমা বোঝে: Java-তে `-XX:MaxRAMPercentage`, Node-এ `--max-old-space-size` সীমার নিচে রাখা, নইলে heap সীমা পেরিয়ে OOM।

### Logging driver ও log rotation

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **json-file** | default logging driver: log JSON ফাইলে |
| **journald** | journal daemon → systemd journal-এ log |
| **stdout / stderr** | **St**andar**d Out**put / **St**andar**d Err**or → container-এর log এখানে লিখতে হয় |

**সহজ ভাষায়:** Container-এর অ্যাপ log ফাইলে না লিখে stdout/stderr-এ লিখবে; Docker সেটা ধরে। Default json-file driver-এ কোনো সীমা নেই, তাই কয়েক সপ্তাহে GB জমে disk ভরে যায়। `daemon.json`-এ বা প্রতিটি container-এ `max-size` আর `max-file` দাও। কেন্দ্রীয়ভাবে log নিতে fluentd/Loki/CloudWatch-এর মতো driver বা agent।

> **বাস্তব পরিস্থিতি:** রাত ৩টায় সার্ভার down: disk ১০০%। `/var/lib/docker/containers/*/…-json.log` একাই ৪০ GB। `daemon.json`-এ log rotation দিয়ে docker restart আর container গুলো recreate করে সমাধান।

```jsonc
// /etc/docker/daemon.json
{
  "log-driver": "json-file",
  "log-opts": { "max-size": "10m", "max-file": "3" }
}
```

```bash
sudo systemctl restart docker      # নতুন container-এ প্রযোজ্য
docker run -d --log-opt max-size=10m --log-opt max-file=3 api:2.4.1
docker run -d --log-driver awslogs --log-opt awslogs-group=/prod/api --log-opt awslogs-region=ap-southeast-1 api:2.4.1
du -sh /var/lib/docker/containers/*/*-json.log | sort -h | tail
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--log-opt max-size=10m` | maximum size → একটা log ফাইল সর্বোচ্চ ১০ MB |
| `max-file=3` | maximum files → সর্বোচ্চ ৩টা ফাইল ঘুরিয়ে রাখো |
| `--log-driver awslogs` | logging driver → log সরাসরি CloudWatch-এ পাঠাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Container-এ অ্যাপ কেন ফাইলে log না লিখে stdout-এ লিখবে?
**উত্তর:** Docker/Kubernetes stdout সংগ্রহ করে `docker logs`, `kubectl logs` আর কেন্দ্রীয় log সিস্টেমে পাঠায়; ফাইলে লিখলে container মুছলে হারায়, rotation নিজে সামলাতে হয়।

**প্রশ্ন:** `daemon.json` বদলালে পুরনো container-এ প্রযোজ্য হয়?
**উত্তর:** না, log setting container তৈরির সময় বসে; পুরনোগুলো recreate করতে হয়।

### Container security hardening

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **capability** | Linux capability → root-এর ক্ষমতার ছোট ছোট ভাগ |
| **seccomp** | **Sec**ure **Comp**uting mode → অনুমোদিত system call সীমিত করা |
| **rootless** | rootless Docker → daemon নিজেই root ছাড়া চলে |
| **privileged** | প্রায় পুরো host-এর ক্ষমতা পাওয়া container (বিপজ্জনক) |

**সহজ ভাষায়:** Container-এ root মানে host-এর kernel-এর সাথে root হিসেবে কথা বলা। তাই non-root user, read-only filesystem, অপ্রয়োজনীয় capability বাদ, privilege বাড়ানো বন্ধ। `docker.sock` কখনো সাধারণ container-এ mount করো না, আর `--privileged` শুধু একান্ত দরকারে।

> **বাস্তব পরিস্থিতি:** একটা web অ্যাপে RCE দুর্বলতা দিয়ে attacker container-এ ঢুকল। Container read-only, non-root, সব capability বাদ আর `no-new-privileges` থাকায় সে কিছু ইনস্টল বা ফাইল বদলাতে পারল না, host-এও পৌঁছাতে পারল না।

```bash
docker run -d --name api \
  --user 10001:10001 \
  --read-only --tmpfs /tmp \
  --cap-drop ALL --cap-add NET_BIND_SERVICE \
  --security-opt no-new-privileges:true \
  -p 8080:8080 api:2.4.1

# কখনো না (প্রয়োজন ছাড়া)
# docker run --privileged ...
# docker run -v /var/run/docker.sock:/var/run/docker.sock ...
```

```yaml
# Compose-এ secret (ফাইল হিসেবে, env-এ নয়)
services:
  api:
    secrets: [db_password]      # -> /run/secrets/db_password
secrets:
  db_password:
    file: ./secrets/db_password.txt
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--user 10001:10001` | UID:GID, root ছাড়া চালাও |
| `--read-only` | read-only rootfs → container-এর filesystem-এ লেখা যাবে না |
| `--tmpfs /tmp` | temporary filesystem → শুধু `/tmp`-এ RAM ভিত্তিক লেখার জায়গা |
| `--cap-drop ALL` | capability drop → সব বিশেষ ক্ষমতা বাদ |
| `--cap-add NET_BIND_SERVICE` | capability add → শুধু ১০২৪-এর নিচের port-এ bind করার ক্ষমতা ফিরিয়ে দাও |
| `--security-opt no-new-privileges` | setuid দিয়েও ক্ষমতা বাড়ানো যাবে না |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `docker.sock`-কে container-এ mount করা কেন বিপজ্জনক?
**উত্তর:** ওই container Docker API দিয়ে host-এর root হিসেবে নতুন privileged container চালাতে পারে, অর্থাৎ পুরো host-এর নিয়ন্ত্রণ পায়।

**প্রশ্ন:** `--privileged` কী করে?
**উত্তর:** সব capability দেয়, সব device দেখায়, seccomp/AppArmor সীমা তুলে দেয়; কার্যত container-এর isolation প্রায় শেষ।

---

## ০৯. পরিষ্কার ও Troubleshooting

Disk ভরে যাওয়া, container চালু না হওয়া, connect না হওয়া: সবচেয়ে কমন সমস্যা আর তার সমাধান।

### system df ও prune: disk পরিষ্কার

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **df** | **D**isk **F**ree → কত জায়গা নিচ্ছে |
| **prune** | অব্যবহৃত জিনিস ছাঁটাই |
| **build cache** | build-এর জমা layer |

**সহজ ভাষায়:** Docker-এর জিনিস জমতে থাকে: থেমে থাকা container, পুরনো image, অব্যবহৃত volume, build cache। `system df` দেখায় কোনটা কত; `prune` দিয়ে আলাদা আলাদা বা একসাথে মোছা যায়। Volume prune-এ data হারাতে পারে, তাই খুব সাবধানে।

> **বাস্তব পরিস্থিতি:** CI runner-এ প্রতি সপ্তাহে disk ভরে যায়। একটা cron বসানো হলো যেটা ৭ দিনের পুরনো image, container আর build cache মোছে, কিন্তু volume ছোঁয় না।

```bash
docker system df
docker system df -v
docker container prune
docker image prune -a --filter "until=168h"
docker builder prune --filter "until=168h"
docker volume prune                  # সাবধান: অব্যবহৃত volume-এর data যাবে
docker network prune
docker system prune                  # container, network, dangling image, cache
docker system prune -a --volumes     # প্রায় সব কিছু! শুধু নিশ্চিত হলে

# cron: প্রতি রবিবার রাত ৩টায়
0 3 * * 0 docker system prune -af --filter "until=168h"
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `system df -v` | `v` = verbose → প্রতিটি image/volume আলাদা করে |
| `prune -a` | `a` = all → শুধু dangling না, কোনো container ব্যবহার করছে না এমন সব image |
| `-f` | `f` = force → নিশ্চিতকরণ প্রশ্ন ছাড়া |
| `--volumes` | অব্যবহৃত volume-ও মুছবে |
| `--filter until=168h` | ১৬৮ ঘণ্টার বেশি পুরনো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `docker system prune -a --volumes` কেন বিপজ্জনক?
**উত্তর:** এটা থেমে থাকা container-এর volume সহ সব অব্যবহৃত volume মুছে দেয়; থামানো database-এর data চিরতরে চলে যেতে পারে।

### কমন error ও সমাধান

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **EADDRINUSE** | **E**rror **Addr**ess **In Use** → port আগে থেকে ব্যবহৃত |

**সহজ ভাষায়:** নিচের টেবিলে বাস্তবে সবচেয়ে বেশি দেখা Docker error, কারণ আর সমাধান। ইন্টারভিউতে "এই error পেলে কী করবে" ধরনের প্রশ্ন এখান থেকেই আসে।

> **বাস্তব পরিস্থিতি:** নতুন developer প্রথম দিনেই তিনটা error পেল: port already allocated, docker.sock permission denied, আর exec format error। এই টেবিল দেখে ১০ মিনিটে সব ঠিক।

| Error / লক্ষণ | সম্ভাব্য কারণ | সমাধান |
|---|---|---|
| `port is already allocated` | host-এর ওই port অন্য container/প্রোগ্রাম নিয়েছে | `ss -tlnp \| grep 8080`, `docker ps`; অন্য host port দাও (`-p 8081:80`) |
| `permission denied ... docker.sock` | user `docker` group-এ নেই | `sudo usermod -aG docker $USER`, তারপর নতুন login |
| Container চালু হয়েই `Exited (0)` | মূল process শেষ হয়ে গেছে (যেমন CMD bash বা background-এ চালানো daemon) | foreground-এ চলা কমান্ড দাও (`nginx -g 'daemon off;'`) |
| `Exited (1)` | অ্যাপের error | `docker logs` দেখো |
| `Exited (137)`, `OOMKilled true` | memory সীমা পেরিয়েছে | memory বাড়াও বা leak ঠিক করো |
| `exec format error` | ভুল CPU architecture-এর image, বা script-এ shebang নেই/CRLF | `--platform` দিয়ে build, buildx multi-arch, shebang ও LF line ending |
| `exec: "bash": not found` | image-এ bash নেই (alpine/distroless) | `sh` ব্যবহার করো, বা debug image |
| `no space left on device` | image/log/volume-এ disk ভরা | `docker system df`, prune, log rotation |
| Connection refused to `localhost:5432` | container-এর localhost = নিজে | service নাম (`db`) ব্যবহার করো, একই network |
| `pull access denied` / `manifest unknown` | ভুল নাম/tag, private repo-তে login নেই | নাম যাচাই, `docker login` |
| `toomanyrequests: rate limit` | Docker Hub-এর anonymous pull সীমা | login করো, নিজস্ব mirror/registry |
| Bind mount-এ `Permission denied` | container user-এর UID আর host ফোল্ডারের owner আলাদা | `chown` বা `--user` মেলাও |
| `COPY failed: file not found` | ফাইল build context-এর বাইরে বা `.dockerignore`-এ বাদ | context আর `.dockerignore` যাচাই |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Container চালু হয়েই exit 0 দিয়ে বন্ধ হয়ে যায়, কেন?
**উত্তর:** Container ততক্ষণ চলে যতক্ষণ PID 1 চলে। মূল কমান্ড কাজ শেষ করে বেরিয়ে গেলে (বা daemon হয়ে background-এ গেলে) container-ও শেষ। Foreground-এ চলা process দিতে হবে।

### Debug কৌশল: ভেতরে ঢোকা ও network পরীক্ষা

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **netshoot** | **Net**work trouble**shoot**ing image → সব network টুলসহ debug image |
| **nsenter** | **N**ame**s**pace **Enter** → process-এর namespace-এ ঢোকা |

**সহজ ভাষায়:** Production image-এ shell বা টুল থাকে না (distroless)। তখন পাশে একটা debug container চালিয়ে লক্ষ্য container-এর network বা PID namespace শেয়ার করো। Image-এর ভেতর কী আছে দেখতে entrypoint বদলে shell খোলো।

> **বাস্তব পরিস্থিতি:** Distroless API container বাইরের payment API-তে পৌঁছাতে পারছে না। Container-এ curl নেই। netshoot-কে একই network namespace-এ চালিয়ে দেখা গেল DNS কাজ করছে কিন্তু 443 timeout: সার্ভারের outbound firewall-এর সমস্যা।

```bash
# লক্ষ্য container-এর network namespace-এ debug টুল
docker run --rm -it --network container:api nicolaka/netshoot
# ভেতরে: dig payments.example.com ; curl -v https://payments.example.com ; ss -tnp

# PID namespace শেয়ার করে process দেখা
docker run --rm -it --pid container:api --network container:api nicolaka/netshoot ps aux

# image-এর ভেতর ঘুরে দেখা (CMD/ENTRYPOINT বদলে)
docker run --rm -it --entrypoint sh api:2.4.1

# host থেকে nsenter
PID=$(docker inspect -f '{{.State.Pid}}' api)
sudo nsenter -t $PID -n ss -tlnp

docker events --since 30m
docker diff api
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--network container:api` | api-এর network শেয়ার |
| `--pid container:api` | PID namespace → api-এর process দেখো |
| `--entrypoint sh` | image-এর ENTRYPOINT বদলে shell |
| `nsenter -t -n` | `t` = target → কোন PID · `n` = network → network namespace-এ ঢোকো |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `docker events` | Docker-এর ঘটনা (start, die, oom, kill) সময় অনুযায়ী |
| `docker diff api` | image-এর তুলনায় container-এ কোন ফাইল যোগ (A), বদল (C), মোছা (D) |

---

## ১০. CI/CD-তে Docker

প্রতিটি commit-এ স্বয়ংক্রিয়ভাবে image build, scan, tag আর registry-তে push।

### GitHub Actions দিয়ে build, scan ও push

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CI/CD** | **C**ontinuous **I**ntegration / **C**ontinuous **D**elivery → অবিরাম একীকরণ / সরবরাহ |
| **GHCR** | **G**it**H**ub **C**ontainer **R**egistry → GitHub-এর registry |
| **SHA** | **S**ecure **H**ash **A**lgorithm (commit hash) → commit-এর অনন্য চিহ্ন |

**সহজ ভাষায়:** Pipeline-এ: code checkout, buildx সেটআপ, registry login, cache সহ build, Trivy scan (critical পেলে fail), তারপর commit SHA আর version tag দিয়ে push। Deploy ধাপ সেই নির্দিষ্ট tag (বা digest) ব্যবহার করে, যাতে ঠিক কী deploy হলো জানা থাকে আর rollback সহজ।

> **বাস্তব পরিস্থিতি:** আগে একজন engineer ল্যাপটপে build করে push করত; কোন কোড image-এ গেল কেউ জানত না। এখন main-এ merge হলেই pipeline image বানায়, scan করে, `ghcr.io/acme/api:<sha>` push করে, আর staging-এ deploy করে।

```yaml
# .github/workflows/docker.yml
name: docker
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v6
        with:
          context: .
          load: true
          tags: ghcr.io/acme/api:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
      - uses: aquasecurity/trivy-action@master
        with:
          image-ref: ghcr.io/acme/api:${{ github.sha }}
          severity: CRITICAL
          exit-code: "1"
      - run: docker push ghcr.io/acme/api:${{ github.sha }}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `on: push: branches: [main]` | main-এ push হলে চলবে |
| `permissions: packages: write` | GHCR-এ push করার অনুমতি |
| `setup-buildx-action` | BuildKit/buildx প্রস্তুত |
| `secrets.GITHUB_TOKEN` | GitHub-এর দেওয়া অস্থায়ী token, কোডে password লাগে না |
| `load: true` | build করা image local-এ রাখো, যাতে scan করা যায় |
| `tags: ...:${{ github.sha }}` | commit hash দিয়ে tag, প্রতিটি image অনন্য ও খুঁজে পাওয়া যায় |
| `cache-from/to: type=gha` | GitHub Actions cache-এ layer রাখো, পরের build দ্রুত |
| `trivy-action exit-code: 1` | CRITICAL vulnerability পেলে pipeline fail |
| `docker push` | scan পাস হলে তবেই registry-তে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Image tag-এর জন্য ভালো কৌশল কী?
**উত্তর:** অপরিবর্তনীয় tag: git commit SHA আর semantic version (`2.4.1`); `latest` শুধু সুবিধার জন্য, deploy-এ কখনো না। Production-এ digest দিয়ে pin করা আরও নিরাপদ।

**প্রশ্ন:** CI-তে Docker build দ্রুত করার উপায়?
**উত্তর:** Layer ক্রম ঠিক রাখা, `.dockerignore`, BuildKit cache mount, remote cache (gha/registry), ছোট base image, আর multi-stage।

---

## ১১. বাস্তব Incident Playbook

Docker-এর সবচেয়ে কমন সমস্যায় ধাপে ধাপে কী করবে। ইন্টারভিউতে "X কাজ করছে না, কী করবে?" প্রশ্নেও এভাবে উত্তর দিও। বাস্তব কাজেও checklist হিসেবে ব্যবহার করো।

### 🚨 Container বারবার restart হচ্ছে

1. `docker ps -a` → STATUS আর exit code দেখো
2. `docker logs --tail 100 <name>` → অ্যাপ কী error দিচ্ছে
3. `docker inspect -f '{{.State.OOMKilled}} {{.State.ExitCode}}' <name>` → 137 + OOMKilled = memory সীমা; 1 = অ্যাপ error; 127 = কমান্ড পাওয়া যায়নি
4. Environment ঠিক আছে? `docker inspect -f '{{.Config.Env}}' <name>`
5. Dependency (DB) প্রস্তুত? healthcheck আর `depends_on` condition
6. হাতে চালিয়ে দেখো: `docker run -it --rm --entrypoint sh <image>`

### 🚨 Web অ্যাপ browser থেকে খুলছে না

1. Container চলছে? `docker ps`
2. Port publish হয়েছে? `docker port <name>` (PORTS কলামে `0.0.0.0:8080->80/tcp`)
3. অ্যাপ container-এর ভেতরে `0.0.0.0`-এ শুনছে, `127.0.0.1`-এ না? `docker exec <name> ss -tlnp` (বা netshoot দিয়ে)
4. Host থেকে: `curl -v http://localhost:8080`
5. বাইরে থেকে না গেলে: cloud security group / firewall-এ port খোলা?
6. Reverse proxy থাকলে তার log আর upstream নাম

### 🚨 Container থেকে database-এ connect হচ্ছে না

1. Host নাম `localhost` দেওয়া হয়নি তো? service/container-এর নাম দাও (`db`)
2. দুটো একই network-এ? `docker network inspect <net>`
3. DNS: `docker exec api getent hosts db`
4. Port: `docker exec api nc -zv db 5432` (বা netshoot)
5. db সত্যিই প্রস্তুত? `docker logs db`, healthcheck status
6. Credential আর `DATABASE_URL` ঠিক? `.env` লোড হচ্ছে?

### 🚨 Disk ভরে গেছে

1. `df -h ; docker system df`
2. বড় log: `du -sh /var/lib/docker/containers/*/*-json.log | sort -h | tail`
3. পুরনো image/container/cache: `docker image prune -a ; docker builder prune`
4. Volume সাবধানে: `docker volume ls -f dangling=true`, যাচাই করে মোছো
5. স্থায়ী সমাধান: `daemon.json`-এ log rotation, নিয়মিত prune cron

### 🚨 Image build খুব ধীর বা বড়

1. `docker build --progress=plain` → কোন ধাপে সময় যাচ্ছে
2. `.dockerignore` আছে? build context কত বড়?
3. `COPY . .` কি dependency install-এর আগে? ক্রম ঠিক করো
4. `RUN --mount=type=cache` দিয়ে package cache
5. `docker history <image>` → কোন layer বড়
6. Multi-stage আর ছোট base image (slim/alpine/distroless)

---

## ১২. ইন্টারভিউ প্রশ্ন ব্যাংক

প্রতিটি অধ্যায়ের কার্ডে থাকা প্রশ্নের বাইরে আরও কিছু জনপ্রিয় প্রশ্ন, লেভেল অনুযায়ী সাজানো। উত্তর মুখস্থ না করে নিজের ভাষায় বলার অনুশীলন করো।

### 🟢 বেসিক লেভেল

**প্রশ্ন ১. Docker কী আর কেন ব্যবহার করা হয়?**
Docker অ্যাপ আর তার সব dependency-কে একটা image-এ প্যাকেজ করে container হিসেবে চালানোর প্ল্যাটফর্ম। সুবিধা: সব পরিবেশে একই আচরণ, দ্রুত deploy, হালকা, ঘন করে চালানো যায়, microservice আর CI/CD সহজ।

**প্রশ্ন ২. Dockerfile-এ সবচেয়ে বেশি ব্যবহৃত instruction গুলো বলো।**
`FROM`, `WORKDIR`, `COPY`, `RUN`, `ENV`, `ARG`, `EXPOSE`, `USER`, `CMD`, `ENTRYPOINT`, `HEALTHCHECK`, `LABEL`।

**প্রশ্ন ৩. `docker run -d -p 8080:80 nginx` কী করে?**
nginx image না থাকলে নামায়, নতুন container পেছনে (`-d`) চালায়, host-এর 8080 port-কে container-এর 80 port-এ যুক্ত করে।

**প্রশ্ন ৪. Docker Compose কেন ব্যবহার করো?**
একাধিক container (web, api, db, cache)-এর config একটা YAML ফাইলে রেখে একটা কমান্ডে চালানো, থামানো, network আর volume সহ; development আর ছোট deployment-এর জন্য সহজ।

**প্রশ্ন ৫. Container-এর data কীভাবে টিকিয়ে রাখবে?**
Named volume (database-এর জন্য) বা bind mount (host ফোল্ডার)। Container-এর নিজের লেখার স্তর container মুছলে হারিয়ে যায়।

### 🟡 ইন্টারমিডিয়েট লেভেল

**প্রশ্ন ৬. Image layer কী আর এটা কেন গুরুত্বপূর্ণ?**
প্রতিটি filesystem-বদলানো instruction (`RUN`, `COPY`, `ADD`) একটা read-only layer তৈরি করে; image হলো layer-এর স্তূপ। Layer শেয়ার হয় (disk ও download বাঁচে) আর cache হয় (দ্রুত build)। তাই instruction-এর ক্রম আর আকার গুরুত্বপূর্ণ।

**প্রশ্ন ৭. Container-এ PID 1-এর বিশেষত্ব কী?**
PID 1 signal-এর default handler পায় না আর orphan zombie process পরিষ্কারের দায়িত্ব তার। অ্যাপ PID 1 হলে SIGTERM নিজে সামলাতে হয়; না পারলে `--init` (tini) ব্যবহার। Shell form CMD-এ shell PID 1 হয়ে signal আটকে দেয়।

**প্রশ্ন ৮. Docker-এ secret কীভাবে সামলাবে?**
Image-এ কখনো না (ENV/ARG/COPY নয়)। Build-এ BuildKit `--secret`, runtime-এ Docker/Compose secrets (`/run/secrets`), environment variable (কম নিরাপদ), বা বাইরের secret manager (Vault, AWS Secrets Manager)।

**প্রশ্ন ৯. Healthcheck আর depends_on condition কীভাবে কাজ করে?**
`HEALTHCHECK` কমান্ড নিয়মিত চালিয়ে container-কে healthy/unhealthy চিহ্নিত করে। Compose-এ `depends_on: condition: service_healthy` দিলে নির্ভরশীল service তখনই চালু হয় যখন dependency healthy।

**প্রশ্ন ১০. Docker-এ কতভাবে network হয় আর container গুলো কীভাবে একে অপরকে খোঁজে?**
bridge (default ও user-defined), host, none, overlay, macvlan। User-defined bridge আর Compose network-এ embedded DNS (`127.0.0.11`) থাকে, container/service-এর নাম দিয়ে খোঁজা যায়।

**প্রশ্ন ১১. Image-এর আকার কীভাবে কমাবে?**
Multi-stage build, ছোট base (slim/alpine/distroless), `.dockerignore`, RUN জুড়ে একই লাইনে cache মোছা, `--no-install-recommends`, dev dependency বাদ, `docker history` দিয়ে বড় layer খোঁজা।

### 🔴 অ্যাডভান্সড লেভেল

**প্রশ্ন ১২. Docker কীভাবে isolation দেয়? namespace আর cgroup-এর ভূমিকা কী?**
Namespace প্রতিটি container-কে আলাদা দৃশ্য দেয় (PID, NET, MNT, UTS, IPC, USER), যেন নিজের আলাদা সিস্টেম। Cgroup CPU, memory, I/O, process সংখ্যার সীমা আর হিসাব রাখে। সাথে capability, seccomp, AppArmor/SELinux অতিরিক্ত নিরাপত্তা দেয়।

**প্রশ্ন ১৩. Union/overlay filesystem কী?**
OverlayFS কয়েকটা read-only layer-এর উপর একটা writable layer বসিয়ে একটা একক filesystem দেখায়। Container-এ ফাইল বদলালে copy-on-write হয়: ফাইলটা উপরের writable layer-এ কপি হয়ে বদলায়, নিচের image অপরিবর্তিত থাকে।

**প্রশ্ন ১৪. Rootless Docker আর user namespace remapping কী?**
Rootless mode-এ Docker daemon আর container দুটোই সাধারণ user হিসেবে চলে। `userns-remap`-এ container-এর root (UID 0) host-এ একটা উঁচু unprivileged UID-তে map হয়; container থেকে বেরোতে পারলেও host-এ root হয় না।

**প্রশ্ন ১৫. Docker আর containerd/CRI-O-এর সম্পর্ক কী? Kubernetes কি Docker ছাড়া চলে?**
Docker নিজে containerd ব্যবহার করে। Kubernetes 1.24 থেকে dockershim সরানো হয়েছে, সরাসরি CRI runtime (containerd, CRI-O) ব্যবহার করে। Docker দিয়ে বানানো image OCI মানের, তাই Kubernetes-এ কোনো পরিবর্তন ছাড়াই চলে।

**প্রশ্ন ১৬. Production-এ zero-downtime deploy Docker দিয়ে কীভাবে করবে?**
একক host-এ: নতুন version-এর container পাশাপাশি চালিয়ে healthcheck pass হলে reverse proxy (nginx/Traefik) সেদিকে ঘোরাও, তারপর পুরনোটা graceful stop। বড় পরিসরে orchestrator (Kubernetes rolling update, ECS) ব্যবহার।

**প্রশ্ন ১৭. Supply chain security-তে image-এর জন্য কী করবে?**
বিশ্বস্ত ও নির্দিষ্ট base image, digest দিয়ে pin, CI-তে vulnerability scan, SBOM তৈরি (syft / `docker sbom`), image signing (cosign/Notation) আর deploy-এর সময় signature যাচাই, নিয়মিত rebuild।

---

<p align="center"><sub>Docker: Complete Guide · <a href="../README.md">← সব গাইড</a></sub></p>
