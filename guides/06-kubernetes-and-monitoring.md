# Kubernetes & Monitoring

```bash
$ kubectl get pods -A && open grafana
```

**অর্কেস্ট্রেশন এবং মনিটরিং (Advanced) — Pod, Deployment, Service + Prometheus, Grafana, ELK**

- Kubernetes architecture থেকে probe, HPA, Ingress পর্যন্ত
- ১৪টি scenario ভিত্তিক হাতে-কলমে অনুশীলন (কাজ + সমাধান)
- PromQL, alert rule, Grafana dashboard, Logstash, Kibana KQL

| | |
|---|---|
| টপিক কার্ড | ৪২টি |
| হাতে-কলমে অনুশীলন | ১৪টি |
| ইন্টারভিউ প্রশ্ন | ৭০টি |
| Incident Playbook | ৬টি |
| লেভেল | 🟢 বেসিক · 🟡 ইন্টারমিডিয়েট · 🔴 অ্যাডভান্সড |

---

## এই গাইড কীভাবে ব্যবহার করবে

**Part A**-তে Kubernetes: কেন orchestration, cluster কীভাবে গঠিত, তারপর Pod, Deployment আর Service-এর ধারণা বিস্তারিত, আর শেষে ৯টি scenario ভিত্তিক অনুশীলন। **Part B**-তে Monitoring: metric, log আর trace-এর পার্থক্য, কী মাপতে হয় (Golden Signals, RED, USE, SLO), Prometheus ও PromQL, alert, Grafana dashboard, ELK Stack, আর ৫টি monitoring অনুশীলন।

অনুশীলন কার্ডে প্রথমে **কাজ** আর **পরিস্থিতি** পড়ে নিজে চেষ্টা করো, তারপর সমাধান আর লাইন ধরে ব্যাখ্যা মিলিয়ে দেখো। ল্যাপটপে ফ্রি cluster-এর জন্য **kind** বা **minikube**, monitoring-এর জন্য **kube-prometheus-stack** (Helm), আর ELK-এর জন্য **Docker Compose** ব্যবহার করো। ভুলের টেবিলে ভুল, কেন হয় আর সমাধান; আর প্রতিটি কার্ডে ইন্টারভিউ প্রশ্ন।

> **নোট:** YAML, kubectl, PromQL আর KQL ইংরেজিতে, কারণ ঠিক এভাবেই লিখতে হয়। Metric আর field-এর নাম টুলের version অনুযায়ী সামান্য আলাদা হতে পারে; Prometheus/Kibana-তে autocomplete দিয়ে যাচাই করে নিও।

---

## সূচিপত্র

**Part A: Kubernetes (Orchestration)**

- [০০. শুরুর আগে: Kubernetes কেন আর কীভাবে গঠিত](#০০-শুরুর-আগে-kubernetes-কেন-আর-কীভাবে-গঠিত)
- [০১. Pod: Kubernetes-এর সবচেয়ে ছোট একক](#০১-pod-kubernetes-এর-সবচেয়ে-ছোট-একক)
- [০২. Deployment, ReplicaSet, ConfigMap, Secret ও Autoscaling](#০২-deployment-replicaset-configmap-secret-ও-autoscaling)
- [০৩. Service, Ingress ও Kubernetes networking](#০৩-service-ingress-ও-kubernetes-networking)
- [০৪. Kubernetes: Scenario ভিত্তিক অনুশীলন (Lab)](#০৪-kubernetes-scenario-ভিত্তিক-অনুশীলন-lab)

**Part B: Monitoring (Prometheus, Grafana, ELK)**

- [০৫. Monitoring-এর মূল ধারণা: কী দেখব, কেন দেখব](#০৫-monitoring-এর-মূল-ধারণা-কী-দেখব-কেন-দেখব)
- [০৬. Prometheus: metric সংগ্রহ, PromQL ও Alert](#০৬-prometheus-metric-সংগ্রহ-promql-ও-alert)
- [০৭. Grafana: Dashboard ও Visualization](#০৭-grafana-dashboard-ও-visualization)
- [০৮. ELK Stack: Elasticsearch, Logstash, Kibana](#০৮-elk-stack-elasticsearch-logstash-kibana)
- [০৯. Monitoring: Scenario ভিত্তিক অনুশীলন](#০৯-monitoring-scenario-ভিত্তিক-অনুশীলন)
- [১০. বাস্তব Incident Playbook](#১০-বাস্তব-incident-playbook)
- [১১. ইন্টারভিউ প্রশ্ন ব্যাংক](#১১-ইন্টারভিউ-প্রশ্ন-ব্যাংক)

---

# Part A: Kubernetes (Orchestration)

## ০০. শুরুর আগে: Kubernetes কেন আর কীভাবে গঠিত

Docker দিয়ে একটা container চালানো সহজ। কিন্তু ৫০টা সার্ভারে ৫০০টা container চালানো, নষ্ট হলে নিজে ঠিক করা, traffic বাড়লে বাড়ানো: এই কাজটাই orchestration।

### Orchestration কী আর Kubernetes কেন

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Kubernetes / K8s** | গ্রিক: জাহাজের কান্ডারি → K আর s-এর মাঝে ৮টা অক্ষর, তাই K8s |
| **Orchestration** | অর্কেস্ট্রা পরিচালনা → অনেক container-কে একসাথে পরিচালনা |
| **CNCF** | **C**loud **N**ative **C**omputing **F**oundation → Kubernetes-এর রক্ষণাবেক্ষণকারী সংস্থা |

**সহজ ভাষায়:** Kubernetes হলো অনেকগুলো সার্ভারকে একটা বড় কম্পিউটারের মতো ব্যবহার করার সিস্টেম। তুমি শুধু বলো "আমার অ্যাপের ৩টা কপি সবসময় চলবে, port 80-এ পাওয়া যাবে"। কোন সার্ভারে চলবে, একটা মরলে নতুন চালানো, সার্ভার নষ্ট হলে অন্যটায় সরানো, traffic ভাগ করা: Kubernetes নিজে করে। একে বলে **declarative**: তুমি কাঙ্ক্ষিত অবস্থা বলো, Kubernetes সারাক্ষণ আসল অবস্থাকে সেটার সাথে মেলায় (reconcile)।

> **বাস্তব পরিস্থিতি:** একটা ফুড ডেলিভারি অ্যাপ Docker Compose-এ একটা সার্ভারে চলত। ঈদের আগে রাতে সার্ভারটা মরে গেল, পুরো অ্যাপ ২ ঘণ্টা বন্ধ। Kubernetes-এ যাওয়ার পর একটা node মরলে তার pod গুলো ১ মিনিটের মধ্যে অন্য node-এ চালু হয়, আর ইফতারের ভিড়ে HPA নিজে থেকে pod ৩ থেকে ১৫টায় বাড়ায়।

| বিষয় | Docker / Compose | Kubernetes |
|---|---|---|
| কতগুলো সার্ভার | সাধারণত একটা | অনেকগুলো node-এর cluster |
| Container মরলে | restart policy (একই সার্ভারে) | নিজে নতুন pod, দরকারে অন্য node-এ (self-healing) |
| সার্ভার মরলে | সব বন্ধ | pod অন্য node-এ সরে যায় |
| Scaling | হাতে | স্বয়ংক্রিয় (HPA), node-ও (Cluster Autoscaler) |
| Deploy | পুরনো থামিয়ে নতুন | rolling update, rollback এক কমান্ডে |
| Service discovery / load balancing | এক host-এর DNS | cluster জুড়ে Service আর DNS |
| জটিলতা | কম | বেশি, শেখার সময় লাগে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Kubernetes কী সমস্যা সমাধান করে?
**উত্তর:** অনেক মেশিনে container চালানো, scheduling, self-healing, scaling, rolling update/rollback, service discovery, load balancing, config ও secret ব্যবস্থাপনা: সব declarative ভাবে।

**প্রশ্ন:** Declarative আর imperative-এর পার্থক্য?
**উত্তর:** Imperative-এ কী করতে হবে ধাপে ধাপে বলো (`kubectl run`, `scale`)। Declarative-এ কাঙ্ক্ষিত অবস্থা YAML-এ লিখে apply করো, Kubernetes নিজে পৌঁছায় আর ধরে রাখে। Production-এ declarative (Git-এ YAML)।

### Cluster architecture: Control plane ও Worker node

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **API Server** | kube-apiserver → সব অনুরোধের একমাত্র প্রবেশদ্বার |
| **etcd** | etc + **d**istributed (`/etc` ফোল্ডার + distributed) → cluster-এর সব অবস্থা রাখার key-value database |
| **Scheduler** | kube-scheduler → নতুন pod কোন node-এ যাবে ঠিক করে |
| **Controller Manager** | kube-controller-manager → কাঙ্ক্ষিত আর আসল অবস্থা মেলানোর loop গুলো |
| **kubelet** | kube + let (ছোট) → প্রতিটি node-এর এজেন্ট, pod চালায় |
| **kube-proxy** | Service-এর network নিয়ম বসায় |

**সহজ ভাষায়:** Control plane হলো মস্তিষ্ক: API server (সবাই এর সাথে কথা বলে), etcd (স্মৃতি), scheduler (কোথায় চলবে), controller manager (মেলানোর কাজ)। Worker node হলো হাত-পা: kubelet API server থেকে নির্দেশ নিয়ে container runtime (containerd) দিয়ে pod চালায়, kube-proxy Service-এর traffic সঠিক pod-এ পাঠানোর নিয়ম বসায়। EKS/GKE/AKS-এর মতো managed সার্ভিসে control plane cloud কোম্পানি চালায়।

> **বাস্তব পরিস্থিতি:** `kubectl apply -f deploy.yaml` দিলে কী হয়: kubectl API server-কে YAML পাঠায় → API server যাচাই করে etcd-তে লেখে → Deployment controller দেখে ৩টা pod দরকার, ReplicaSet আর pod object বানায় → scheduler প্রতিটি pod-এর জন্য উপযুক্ত node ঠিক করে → সেই node-এর kubelet দেখে নতুন pod এসেছে, containerd দিয়ে image টেনে চালায় → status আবার API server-এ জানায়।

```text
           ┌────────────── Control Plane ──────────────┐
kubectl ──>│  API Server  <──>  etcd                    │
           │     ^       ^                              │
           │  Scheduler  Controller Manager             │
           └─────┼──────────────────────────────────────┘
                 │ (watch / নির্দেশ)
   ┌─────────────┼───────────────┬──────────────────┐
 Node 1        Node 2          Node 3
 kubelet       kubelet         kubelet
 kube-proxy    kube-proxy      kube-proxy
 containerd    containerd      containerd
 [pod][pod]    [pod]           [pod][pod][pod]
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `kubectl apply` দিলে ভেতরে কী কী ঘটে?
**উত্তর:** উপরের ক্রম: API server → etcd → controller (ReplicaSet/Pod তৈরি) → scheduler (node নির্বাচন) → kubelet (container চালু) → status আপডেট।

**প্রশ্ন:** etcd নষ্ট হলে কী হবে?
**উত্তর:** Cluster-এর সব অবস্থা হারাবে; চলমান container কিছুক্ষণ চলতে পারে, কিন্তু নতুন কিছু তৈরি বা বদলানো যাবে না। তাই etcd-এর নিয়মিত backup আর HA (৩/৫ সদস্য) জরুরি।

### Lab সেটআপ ও kubectl-এর মূল কমান্ড

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **kubectl** | kube control → Kubernetes-এর কমান্ড লাইন টুল |
| **kind** | **K**ubernetes **IN** **D**ocker → Docker container-এর ভেতরে ছোট cluster |
| **minikube** | mini Kubernetes → ল্যাপটপে এক-node cluster |
| **kubeconfig** | kube configuration → `~/.kube/config`: কোন cluster-এ কীভাবে যুক্ত হবে |

**সহজ ভাষায়:** অনুশীলনের জন্য ল্যাপটপে kind বা minikube দিয়ে ফ্রি cluster বানাও। kubectl-এর প্রায় সব কমান্ডের গঠন একই: `kubectl <কাজ> <resource-এর ধরন> <নাম> [অপশন]`। নিচের কমান্ডগুলো পুরো বই জুড়ে ব্যবহার হবে।

> **বাস্তব পরিস্থিতি:** নতুন DevOps engineer প্রথম দিনে cluster-এ ঢুকে বুঝতে চায় কী কী চলছে, কোন pod কোন node-এ, আর কোনটায় সমস্যা।

```bash
kind create cluster --name lab        # বা: minikube start
kubectl cluster-info
kubectl get nodes -o wide
kubectl get pods -A
kubectl get pods -n prod -l app=web -o wide
kubectl get all -n prod
kubectl describe pod web-7d9f-abcde -n prod
kubectl logs -f web-7d9f-abcde -n prod
kubectl logs web-7d9f-abcde --previous -c app
kubectl exec -it web-7d9f-abcde -- sh
kubectl apply -f deploy.yaml
kubectl delete -f deploy.yaml
kubectl get pod web-7d9f-abcde -o yaml
kubectl explain deployment.spec.strategy
kubectl get events -n prod --sort-by=.lastTimestamp
kubectl top pods -n prod
kubectl config get-contexts
kubectl config use-context prod-cluster
kubectl get pods -w
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `get pods -A` | `A` = All namespaces → সব namespace-এর |
| `-n prod` | `n` = namespace → এই namespace-এ |
| `-l app=web` | `l` = label selector → এই label যুক্তগুলো |
| `-o wide` / `-o yaml` | `o` = output → wide = বাড়তি কলাম (IP, node) · yaml = পুরো object YAML-এ |
| `logs -f` | `f` = follow → live log |
| `logs --previous` | আগের (crash করা) container-এর log |
| `logs -c app` | `c` = container → pod-এ একাধিক container থাকলে কোনটার |
| `exec -it ... -- sh` | `i` = interactive → input খোলা · `t` = tty → terminal; `--`-এর পরে container-এর কমান্ড |
| `apply -f` | `f` = file → এই YAML ফাইল প্রয়োগ করো (না থাকলে তৈরি, থাকলে আপডেট) |
| `--sort-by=.lastTimestamp` | সময় অনুযায়ী সাজাও, শেষের ঘটনা নিচে |
| `get pods -w` | `w` = watch → পরিবর্তন হলে live দেখাও |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `kubectl explain` | যেকোনো field-এর documentation টার্মিনালেই |
| `kubectl top` | CPU/memory ব্যবহার (metrics-server লাগে) |
| `config use-context` | অন্য cluster-এ সরাও; production-এ ভুল কমান্ড এড়াতে সবসময় context দেখে নাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `kubectl apply` আর `kubectl create`-এর পার্থক্য?
**উত্তর:** `create` শুধু নতুন তৈরি করে, থাকলে error। `apply` declarative: না থাকলে তৈরি, থাকলে পার্থক্য হিসাব করে আপডেট; GitOps আর বারবার চালানোর জন্য `apply`।

> 💡 **প্রো টিপ:** `alias k=kubectl` আর kubectl-এর auto-completion চালু করো: `source <(kubectl completion bash)`। CKA পরীক্ষায়ও এটা সময় বাঁচায়।

### YAML-এর গঠন, Namespace ও Label

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **apiVersion** | কোন API group আর version |
| **kind** | resource-এর ধরন (Pod, Deployment) |
| **metadata** | নাম, namespace, label |
| **spec** | specification → কাঙ্ক্ষিত অবস্থা |
| **Namespace** | cluster-এর ভেতরে আলাদা ভাগ |
| **Label / Selector** | key=value ট্যাগ / ট্যাগ দিয়ে বাছাই |

**সহজ ভাষায়:** প্রতিটি Kubernetes YAML-এর চারটা মূল অংশ: `apiVersion`, `kind`, `metadata`, `spec`। Kubernetes নিজে `status` অংশ যোগ করে (আসল অবস্থা)। Namespace দিয়ে টিম বা পরিবেশ আলাদা (dev, prod), আর resource quota দেওয়া যায়। Label হলো Kubernetes-এর আঠা: Deployment কোন pod তার নিজের, Service কোন pod-এ traffic পাঠাবে, সব label selector দিয়ে ঠিক হয়।

> **বাস্তব পরিস্থিতি:** একই cluster-এ team-a আর team-b দুই দলের অ্যাপ। দুজনেরই `web` নামে Deployment। আলাদা namespace-এ রাখায় সংঘাত নেই, আর প্রতিটি namespace-এ CPU/memory quota দেওয়া হয়েছে যাতে এক দল পুরো cluster খেয়ে না ফেলে।

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello
  namespace: dev
  labels:
    app: hello
    tier: frontend
spec:
  containers:
    - name: app
      image: nginx:1.27
```

```bash
# Namespace
kubectl create namespace dev
kubectl get pods -n dev --show-labels
kubectl label pod hello env=dev -n dev
kubectl get pods -l 'tier in (frontend,backend)' -n dev
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `apiVersion: v1` | Pod, Service, ConfigMap core group-এ, তাই শুধু `v1`; Deployment হলে `apps/v1` |
| `kind: Pod` | কোন ধরনের resource |
| `metadata.labels` | এই pod-এর ট্যাগ; selector এগুলো দিয়ে খুঁজবে |
| `spec.containers` | pod-এর ভেতরের container-এর তালিকা (YAML-এ `-` মানে list-এর একটা উপাদান) |
| `--show-labels` | সব label কলামে দেখাও |
| `-l 'tier in (...)'` | set-based selector: এই মানগুলোর যেকোনো একটা |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| YAML-এ tab ব্যবহার বা indentation ভুল | editor-এর অভ্যাস | শুধু space (২টা), editor-এ YAML extension, `kubectl apply --dry-run=client` দিয়ে যাচাই |
| সব কিছু default namespace-এ | namespace-এর কথা না জানা | প্রতিটি অ্যাপ/টিম/পরিবেশে আলাদা namespace |
| Label-এ বানান অসঙ্গতি (`app: web` বনাম `app: Web`) | হাতে লেখা | সাধারণ label convention (`app.kubernetes.io/name`), Helm template |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Label আর annotation-এর পার্থক্য?
**উত্তর:** Label বাছাই আর গোষ্ঠীবদ্ধ করার জন্য (selector-এ ব্যবহার হয়), ছোট মান। Annotation বাড়তি তথ্যের জন্য (build তথ্য, টুলের সেটিং), selector-এ ব্যবহার হয় না।

---

## ০১. Pod: Kubernetes-এর সবচেয়ে ছোট একক

Kubernetes container সরাসরি চালায় না, Pod চালায়। Pod বুঝলে বাকি সবকিছু সহজ।

### Pod কী: একসাথে থাকা container-এর ঘর

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Pod** | (তিমির দল বা মটরশুঁটির খোসা) → এক বা একাধিক container-এর দল |
| **Pod IP** | প্রতিটি pod-এর নিজস্ব IP |
| **Ephemeral** | ক্ষণস্থায়ী |

**সহজ ভাষায়:** Pod হলো এক বা একাধিক container-এর একটা ঘর, যারা একই network (একই IP, localhost দিয়ে কথা বলে) আর চাইলে একই volume শেয়ার করে। বেশিরভাগ pod-এ একটাই container। Pod ক্ষণস্থায়ী: মরলে একই pod ফেরে না, নতুন নাম আর নতুন IP নিয়ে নতুন pod আসে। তাই pod সরাসরি খুব কম বানানো হয়; Deployment-এর মতো controller বানায় আর দেখাশোনা করে।

> **বাস্তব পরিস্থিতি:** একজন developer `kubectl run` দিয়ে একটা pod বানিয়ে production-এ চালাচ্ছিল। Node reboot হলো, pod চলে গেল, আর কেউ আবার চালাল না। Deployment দিয়ে চালালে নিজে থেকে নতুন pod আসত।

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web
  labels: { app: web }
spec:
  containers:
    - name: nginx
      image: nginx:1.27
      ports:
        - containerPort: 80
```

```bash
kubectl apply -f pod.yaml
kubectl get pod web -o wide
kubectl port-forward pod/web 8080:80     # browser: localhost:8080
kubectl delete pod web
```

| Pod phase | মানে |
|---|---|
| Pending | তৈরি হয়েছে কিন্তু এখনো চলেনি: node খোঁজা হচ্ছে বা image নামছে |
| Running | অন্তত একটা container চলছে |
| Succeeded | সব container সফলভাবে শেষ (Job) |
| Failed | অন্তত একটা container ব্যর্থ হয়ে শেষ |
| Unknown | node-এর সাথে যোগাযোগ নেই |

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `port-forward pod/web 8080:80` | port forward → ল্যাপটপের 8080 থেকে pod-এর 80-তে সুড়ঙ্গ (শুধু debug-এর জন্য) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Pod আর container-এর পার্থক্য?
**উত্তর:** Container একটা চলমান process (image থেকে)। Pod হলো Kubernetes-এর ক্ষুদ্রতম একক, এক বা একাধিক container যারা network ও storage শেয়ার করে আর একসাথে একই node-এ schedule হয়।

**প্রশ্ন:** Pod কেন সরাসরি না বানিয়ে Deployment দিয়ে বানানো হয়?
**উত্তর:** Pod নিজে নিজেকে আবার চালায় না বা অন্য node-এ সরে না; Deployment/ReplicaSet সংখ্যা ধরে রাখে, নষ্ট হলে নতুন বানায়, rolling update আর rollback দেয়।

### Multi-container pod: Sidecar ও Init container

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Sidecar** | (মোটরসাইকেলের পাশের বগি) → মূল container-এর সহায়ক container |
| **Init container** | initialization container → মূল container-এর আগে চলে শেষ হয় |

**সহজ ভাষায়:** একই pod-এ একাধিক container রাখা হয় শুধু তখন, যখন তারা ঘনিষ্ঠভাবে যুক্ত। Sidecar মূল অ্যাপের পাশে থেকে সাহায্য করে: log সংগ্রহ, proxy (Istio), ফাইল sync। Init container মূল অ্যাপ চালুর আগে একবার চলে: database প্রস্তুত হওয়ার অপেক্ষা, config তৈরি, migration।

> **বাস্তব পরিস্থিতি:** একটা পুরনো অ্যাপ শুধু ফাইলে log লেখে, stdout-এ না। পাশে একটা sidecar (busybox tail বা Fluent Bit) একই volume থেকে ফাইল পড়ে stdout-এ পাঠায়, ফলে `kubectl logs` আর ELK-এ log আসে। আর init container দেখে database প্রস্তুত, তারপরই অ্যাপ চালু।

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: legacy-app
spec:
  initContainers:
    - name: wait-for-db
      image: busybox:1.36
      command: ['sh', '-c', 'until nc -z db 5432; do echo waiting; sleep 2; done']
  containers:
    - name: app
      image: acme/legacy:3.1
      volumeMounts:
        - { name: logs, mountPath: /var/log/app }
    - name: log-shipper
      image: busybox:1.36
      command: ['sh', '-c', 'tail -F /var/log/app/app.log']
      volumeMounts:
        - { name: logs, mountPath: /var/log/app }
  volumes:
    - name: logs
      emptyDir: {}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `initContainers` | মূল container-এর আগে ক্রমানুসারে চলে, সফল হলে তবেই মূল অ্যাপ শুরু |
| `until nc -z db 5432` | db service-এর 5432 port খোলা না হওয়া পর্যন্ত অপেক্ষা |
| দুটো `containers` | app আর log-shipper একই pod-এ, একই node-এ |
| `emptyDir: {}` | pod-এর জীবনকাল পর্যন্ত থাকা খালি ফোল্ডার, দুই container শেয়ার করে |
| `tail -F` | ফাইল নতুন হলেও ফলো করে stdout-এ পাঠায় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Init container আর sidecar-এর পার্থক্য?
**উত্তর:** Init container মূল অ্যাপের আগে চলে আর শেষ হয়ে যায়। Sidecar মূল অ্যাপের পাশে সারাক্ষণ চলে।

### Probe: Liveness, Readiness, Startup

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Probe** | (পরীক্ষা) → container সুস্থ কিনা kubelet-এর পরীক্ষা |
| **Liveness** | liveness probe → বেঁচে আছে কিনা; fail হলে restart |
| **Readiness** | readiness probe → traffic নিতে প্রস্তুত কিনা; fail হলে Service থেকে বাদ |
| **Startup** | startup probe → ধীরে চালু হওয়া অ্যাপকে সময় দেওয়া |

**সহজ ভাষায়:** Kubernetes শুধু process চলছে কিনা দেখে; অ্যাপ ভেতরে আটকে গেলেও process চলতে থাকে। Liveness probe fail হলে kubelet container restart করে। Readiness probe fail হলে container restart হয় না, শুধু Service সেই pod-এ traffic পাঠানো বন্ধ করে। Startup probe সফল না হওয়া পর্যন্ত বাকি দুটো চালু হয় না, তাই ধীর Java অ্যাপ চালুর সময়েই মারা পড়ে না।

> **বাস্তব পরিস্থিতি:** Rolling update-এর সময় প্রতিবার ৩০ সেকেন্ড ধরে কিছু user 502 পেত। নতুন pod Running হতেই Service traffic পাঠাচ্ছিল, অথচ অ্যাপ তখনো cache লোড করছিল। Readiness probe যোগ করার পর অ্যাপ সত্যিই প্রস্তুত হলেই traffic আসে, 502 শূন্য।

```yaml
containers:
  - name: api
    image: acme/api:2.4.1
    ports: [{ containerPort: 8000 }]
    startupProbe:
      httpGet: { path: /healthz, port: 8000 }
      failureThreshold: 30
      periodSeconds: 5
    livenessProbe:
      httpGet: { path: /healthz, port: 8000 }
      periodSeconds: 10
      failureThreshold: 3
    readinessProbe:
      httpGet: { path: /ready, port: 8000 }
      periodSeconds: 5
      failureThreshold: 2
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `httpGet: path, port` | এই path-এ HTTP GET; 200-399 উত্তর = সফল (`tcpSocket` আর `exec`-ও হয়) |
| `startupProbe failureThreshold: 30, periodSeconds: 5` | চালু হতে সর্বোচ্চ ৩০ x ৫ = ১৫০ সেকেন্ড সময় |
| `livenessProbe failureThreshold: 3` | পরপর ৩ বার fail হলে container restart |
| `readinessProbe /ready` | আলাদা endpoint: database/cache সংযোগ সহ প্রস্তুতি দেখে |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Liveness probe-এ database-ও পরীক্ষা করা | "সম্পূর্ণ health দেখা ভালো" | Liveness শুধু process নিজে সাড়া দেয় কিনা; DB সমস্যায় সব pod একসাথে restart হয়ে বিপর্যয় হয়। নির্ভরতা readiness-এ |
| Liveness-এর সময় খুব কম, ধীর অ্যাপ বারবার মরে (CrashLoop) | চালুর সময় হিসাব না করা | startupProbe যোগ করো |
| কোনো readiness probe নেই | জানা নেই | প্রতিটি সার্ভিসে readiness, rolling update নিরাপদ হয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Liveness আর readiness probe-এর পার্থক্য?
**উত্তর:** Liveness fail হলে container restart হয় (আটকে গেছে)। Readiness fail হলে restart হয় না, শুধু Service-এর endpoint থেকে সরে যায় (এখন traffic নিতে পারবে না)।

### Resource request, limit ও QoS

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **request** | resource request → নিশ্চিত বরাদ্দ; scheduler এটা দেখে node বাছে |
| **limit** | resource limit → সর্বোচ্চ সীমা |
| **QoS** | **Q**uality **o**f **S**ervice class → চাপে কে আগে বাদ পড়বে তার শ্রেণি |
| **m (millicore)** | milli-core → `1000m` = ১টা CPU core |
| **Mi / Gi** | Mebibyte / Gibibyte → memory-এর একক |

**সহজ ভাষায়:** Request হলো "আমার অন্তত এতটুকু লাগবে": scheduler শুধু সেই node-এ বসায় যেখানে এতটা খালি। Limit হলো "এর বেশি কখনো না"। CPU limit পেরোলে ধীর করা হয় (throttle); memory limit পেরোলে container মারা পড়ে (OOMKilled)। Request আর limit সমান হলে QoS **Guaranteed** (সবচেয়ে নিরাপদ), শুধু কিছু দিলে **Burstable**, কিছু না দিলে **BestEffort** (node-এ চাপ হলে আগে বাদ পড়ে)।

> **বাস্তব পরিস্থিতি:** একটা নতুন সার্ভিস কোনো request/limit ছাড়া deploy হলো। একটা memory leak-এ সে পুরো node-এর RAM খেয়ে ফেলল, ওই node-এর বাকি ২০টা pod-ও উচ্ছেদ (evict) হলো। Limit দেওয়ার পর শুধু ওই container OOMKilled হয়, অন্যরা নিরাপদ।

```yaml
containers:
  - name: api
    image: acme/api:2.4.1
    resources:
      requests:
        cpu: "250m"
        memory: "256Mi"
      limits:
        cpu: "1"
        memory: "512Mi"
```

```bash
kubectl top pods -n prod
kubectl describe node worker-1 | grep -A8 "Allocated resources"
kubectl get pod api-xyz -o jsonpath='{.status.qosClass}'
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `cpu: "250m"` | এক core-এর চার ভাগের এক ভাগ নিশ্চিত |
| `memory: "256Mi"` | ২৫৬ mebibyte নিশ্চিত |
| `limits.cpu: "1"` | সর্বোচ্চ ১ core; বেশি চাইলে throttle |
| `limits.memory: "512Mi"` | এর বেশি হলে OOMKilled |
| `Allocated resources` | node-এ মোট কত request/limit বরাদ্দ হয়েছে |
| `jsonpath='{.status.qosClass}'` | YAML-এর নির্দিষ্ট field শুধু বের করো |

| QoS | শর্ত | চাপে কী হয় |
|---|---|---|
| Guaranteed | সব container-এ request = limit (CPU ও memory) | সবার শেষে বাদ পড়ে |
| Burstable | কিছু request/limit আছে কিন্তু সমান নয় | মাঝামাঝি |
| BestEffort | কোনো request/limit নেই | সবার আগে evict হয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Request আর limit-এর পার্থক্য?
**উত্তর:** Request scheduling-এর জন্য নিশ্চিত বরাদ্দ; limit চলার সময় সর্বোচ্চ সীমা। CPU limit পেরোলে throttle, memory limit পেরোলে OOMKill।

**প্রশ্ন:** Pod Pending থাকছে "Insufficient cpu" দিয়ে। কেন?
**উত্তর:** কোনো node-এ pod-এর request পরিমাণ খালি নেই। Request কমাও (যদি বেশি দেওয়া হয়ে থাকে), node বাড়াও বা Cluster Autoscaler চালু করো, অব্যবহৃত pod সরাও।

### Pod-এর কমন error ও debug

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CrashLoopBackOff** | crash loop back-off → বারবার crash, Kubernetes ক্রমে অপেক্ষা বাড়াচ্ছে |
| **ImagePullBackOff** | image pull back-off → image নামাতে পারছে না |
| **OOMKilled** | **O**ut **O**f **M**emory killed → memory সীমা পেরিয়ে মারা গেছে |
| **Evicted** | node-এর চাপে উচ্ছেদ |

**সহজ ভাষায়:** Pod-এর সমস্যায় সবসময় একই তিন ধাপ: `kubectl get pods` (status কী), `kubectl describe pod` (নিচের Events অংশে কারণ), `kubectl logs` (`--previous` দিয়ে crash-এর আগের log)। Status দেখেই অর্ধেক বোঝা যায়।

> **বাস্তব পরিস্থিতি:** Deploy-এর পর নতুন pod গুলো CrashLoopBackOff। `kubectl logs --previous` দেখাল `Error: DATABASE_URL is not set`। নতুন version-এ variable-এর নাম বদলানো হয়েছিল কিন্তু Secret-এ পুরনো নাম ছিল।

| Status | সাধারণ কারণ | কী দেখবে / সমাধান |
|---|---|---|
| Pending | node-এ জায়গা নেই, nodeSelector/taint মেলে না, PVC bind হয়নি | describe pod-এর Events: "Insufficient memory", "0/3 nodes are available" |
| ImagePullBackOff / ErrImagePull | image নামে বা tag-এ ভুল, private registry-তে অনুমতি নেই | image নাম যাচাই, imagePullSecrets, node থেকে registry পৌঁছায় কিনা |
| CrashLoopBackOff | অ্যাপ চালু হয়ে মরে যাচ্ছে: config, secret, dependency, bug | `logs --previous`, env আর ConfigMap/Secret যাচাই |
| OOMKilled (exit 137) | memory limit পেরিয়েছে | describe-এ Last State: OOMKilled; limit বাড়াও বা leak ঠিক করো |
| CreateContainerConfigError | যে ConfigMap/Secret বা তার key উল্লেখ করা, তা নেই | `kubectl get cm/secret`, key-এর নাম |
| Running কিন্তু 0/1 Ready | readiness probe fail | describe-এ probe-এর error, অ্যাপের `/ready` |
| Evicted | node-এ disk/memory চাপ | node-এর অবস্থা, request/limit, ephemeral storage |
| Terminating-এ আটকে | finalizer বা node যোগাযোগহীন | describe, finalizer যাচাই; শেষ উপায় `--force --grace-period=0` |

```bash
kubectl get pods -n prod
kubectl describe pod api-6c8f-x2k9p -n prod           # নিচের Events দেখো
kubectl logs api-6c8f-x2k9p -n prod --previous
kubectl get pod api-6c8f-x2k9p -n prod -o jsonpath='{.status.containerStatuses[0].lastState}'
kubectl debug -it api-6c8f-x2k9p -n prod --image=nicolaka/netshoot --target=api
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `debug -it --image --target` | ephemeral debug container → চলমান pod-এ সাময়িক debug container যোগ · `target` → এই container-এর process namespace দেখো |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** CrashLoopBackOff কীভাবে troubleshoot করবে?
**উত্তর:** `kubectl describe pod` (Events, Last State, exit code), `kubectl logs --previous` (crash-এর আগের log), env/ConfigMap/Secret যাচাই, liveness probe বেশি কড়া কিনা, আর দরকারে কমান্ড বদলে (sleep) ভেতরে ঢুকে হাতে চালিয়ে দেখা।

**প্রশ্ন:** ImagePullBackOff-এর কারণ কী কী?
**উত্তর:** ভুল image নাম বা tag, private registry-তে imagePullSecret নেই বা ভুল, registry rate limit, node থেকে registry-তে network নেই, ভুল architecture।

---

## ০২. Deployment, ReplicaSet, ConfigMap, Secret ও Autoscaling

Production-এ অ্যাপ চালানোর আসল হাতিয়ার Deployment: কতগুলো কপি, কীভাবে আপডেট, ভুল হলে কীভাবে ফেরা।

### Deployment ও ReplicaSet

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Deployment** | stateless অ্যাপের কাঙ্ক্ষিত অবস্থা ও আপডেট পরিচালক |
| **ReplicaSet** | replica set → নির্দিষ্ট সংখ্যক একই রকম pod চালু রাখে |
| **replicas** | কতগুলো কপি |
| **selector** | কোন pod গুলো এর নিজের |

**সহজ ভাষায়:** Deployment হলো উপরের স্তর: তুমি বলো image, কপির সংখ্যা আর আপডেটের নিয়ম। Deployment একটা ReplicaSet বানায়, ReplicaSet ঠিক ততগুলো pod চালু রাখে। একটা pod মুছে দিলে ReplicaSet সাথে সাথে নতুন বানায়। Image বদলালে Deployment নতুন ReplicaSet বানিয়ে ধীরে ধীরে নতুনটা বাড়ায় আর পুরনোটা কমায়; পুরনো ReplicaSet রেখে দেয় rollback-এর জন্য।

> **বাস্তব পরিস্থিতি:** Black Friday-তে traffic বাড়ছে। একটা কমান্ডে web-এর কপি ৩ থেকে ১০। পরীক্ষার জন্য একজন ইচ্ছে করে একটা pod মুছে দিল: ২ সেকেন্ডের মধ্যে নতুন pod তৈরি, Service-এ কোনো বিঘ্ন নেই।

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: prod
  labels: { app: web }
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:1.27
          ports: [{ containerPort: 80 }]
          resources:
            requests: { cpu: 100m, memory: 128Mi }
            limits: { cpu: 500m, memory: 256Mi }
```

```bash
kubectl apply -f web-deploy.yaml
kubectl get deploy,rs,pods -n prod -l app=web
kubectl scale deployment web --replicas=10 -n prod
kubectl delete pod -l app=web -n prod --wait=false     # নিজে আবার তৈরি হবে
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `apiVersion: apps/v1` | Deployment `apps` group-এ |
| `replicas: 3` | সবসময় ৩টা pod চালু থাকবে |
| `selector.matchLabels` | `app=web` label যুক্ত pod গুলো এই Deployment-এর |
| `template` | নতুন pod বানানোর ছাঁচ; এর labels অবশ্যই selector-এর সাথে মিলতে হবে |
| `get deploy,rs,pods` | তিন স্তর একসাথে দেখো: Deployment → ReplicaSet → Pod |
| `scale --replicas=10` | হাতে কপির সংখ্যা বদলাও (YAML-এও বদলাও, নইলে পরের apply-এ আবার ৩) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Deployment আর ReplicaSet-এর পার্থক্য?
**উত্তর:** ReplicaSet শুধু নির্দিষ্ট সংখ্যক pod চালু রাখে। Deployment ReplicaSet পরিচালনা করে আর তার উপর rolling update, rollback, revision ইতিহাস দেয়। সরাসরি ReplicaSet প্রায় কখনো বানানো হয় না।

**প্রশ্ন:** Deployment-এর selector কি পরে বদলানো যায়?
**উত্তর:** না, `apps/v1`-এ selector অপরিবর্তনীয়; বদলাতে হলে নতুন Deployment বানাতে হয়।

### Rolling update, rollout ও rollback

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **RollingUpdate** | rolling update strategy → ধাপে ধাপে পুরনো বদলে নতুন |
| **maxSurge** | **max**imum **surge** → আপডেটের সময় সর্বোচ্চ কতগুলো বাড়তি pod |
| **maxUnavailable** | **max**imum **unavailable** → সর্বোচ্চ কতগুলো pod অনুপলব্ধ থাকতে পারে |
| **revision** | Deployment-এর সংস্করণ ইতিহাস |

**সহজ ভাষায়:** Image বদলালে Deployment নতুন pod চালু করে, সেগুলো Ready হলে পুরনো বন্ধ করে, এভাবে একটা একটা করে। maxSurge আর maxUnavailable ঠিক করে কত দ্রুত আর কত নিরাপদে। Readiness probe ছাড়া rolling update অন্ধ, কারণ Kubernetes জানবে না নতুন pod সত্যিই প্রস্তুত কিনা। সমস্যা হলে `rollout undo` দিয়ে আগের ReplicaSet-এ ফেরা যায়।

> **বাস্তব পরিস্থিতি:** v2.5 deploy-এর পর error rate বাড়ল। `kubectl rollout undo` দিয়ে ৩০ সেকেন্ডে v2.4-এ ফেরত। আর `maxUnavailable: 0` থাকায় আপডেটের সময় কখনো ৩টার কম pod চালু ছিল না।

```yaml
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  minReadySeconds: 10
  revisionHistoryLimit: 5
```

```bash
kubectl set image deployment/web web=nginx:1.27.2 -n prod
kubectl annotate deployment/web kubernetes.io/change-cause="nginx 1.27.2" -n prod
kubectl rollout status deployment/web -n prod
kubectl rollout history deployment/web -n prod
kubectl rollout undo deployment/web -n prod
kubectl rollout undo deployment/web --to-revision=3 -n prod
kubectl rollout pause deployment/web -n prod
kubectl rollout resume deployment/web -n prod
kubectl rollout restart deployment/web -n prod
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `maxSurge: 1` | আপডেটের সময় ৩+১ = সর্বোচ্চ ৪টা pod |
| `maxUnavailable: 0` | কখনো ৩-এর কম Ready pod থাকবে না (zero downtime) |
| `minReadySeconds: 10` | Ready হওয়ার পর আরও ১০ সেকেন্ড ঠিক থাকলে তবেই সফল ধরা |
| `revisionHistoryLimit: 5` | rollback-এর জন্য শেষ ৫টা পুরনো ReplicaSet রাখো |
| `set image deployment/web web=...` | web container-এর image বদলাও (প্রথম `web` = deployment, দ্বিতীয় = container-এর নাম) |
| `change-cause` annotation | rollout history-তে কারণ লেখা থাকবে |
| `rollout status` | আপডেট শেষ না হওয়া পর্যন্ত অপেক্ষা ও অগ্রগতি; CI-তে কাজের |
| `rollout undo --to-revision=3` | নির্দিষ্ট পুরনো revision-এ ফেরো |
| `rollout restart` | একই image-এ সব pod নতুন করে চালু (ConfigMap বদলের পর) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Kubernetes-এ zero-downtime deployment কীভাবে নিশ্চিত করবে?
**উত্তর:** RollingUpdate-এ `maxUnavailable: 0`, ঠিক readiness probe, অ্যাপে graceful shutdown (SIGTERM সামলানো, preStop), যথেষ্ট replicas, আর PodDisruptionBudget।

**প্রশ্ন:** Rollback কীভাবে করবে?
**উত্তর:** `kubectl rollout undo deployment/<নাম>` (দরকারে `--to-revision`)। GitOps-এ Git revert। Helm-এ `helm rollback`।

### ConfigMap ও Secret

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ConfigMap** | configuration map → সাধারণ config-এর key-value |
| **Secret** | সংবেদনশীল তথ্য (password, token) |
| **base64** | base 64 encoding → এনক্রিপশন নয়, শুধু encoding |

**সহজ ভাষায়:** Image-এ config না রেখে ConfigMap-এ রাখো, যাতে একই image dev আর prod-এ ভিন্ন config নিয়ে চলে। Secret-এ password আর key রাখো। দুটোকেই environment variable বা ফাইল হিসেবে pod-এ দেওয়া যায়। মনে রাখো, Secret শুধু base64, এনক্রিপ্টেড না: etcd encryption, RBAC আর বাইরের secret manager (External Secrets, Vault) দিয়ে সত্যিকারের নিরাপত্তা।

> **বাস্তব পরিস্থিতি:** Log level DEBUG করতে প্রতিবার নতুন image build হচ্ছিল। ConfigMap-এ `LOG_LEVEL` রেখে শুধু ConfigMap বদলে `rollout restart`: ১ মিনিটে কাজ, কোনো build ছাড়া।

```bash
kubectl create configmap api-config -n prod \
  --from-literal=LOG_LEVEL=info --from-file=app.yaml
kubectl create secret generic api-secret -n prod \
  --from-literal=DB_PASSWORD='s3cr3t'
kubectl get secret api-secret -n prod -o jsonpath='{.data.DB_PASSWORD}' | base64 -d
```

```yaml
# Deployment-এর container-এ
envFrom:
  - configMapRef: { name: api-config }
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef: { name: api-secret, key: DB_PASSWORD }
volumeMounts:
  - { name: cfg, mountPath: /etc/api, readOnly: true }
# pod spec-এ
volumes:
  - name: cfg
    configMap: { name: api-config }
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `--from-literal=KEY=VALUE` | সরাসরি key ও value দাও |
| `--from-file=app.yaml` | ফাইলের নাম key, ভেতরের লেখা value |
| `base64 -d` | `d` = decode → Secret-এর মান পড়ার জন্য decode |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `envFrom.configMapRef` | ConfigMap-এর সব key-কে environment variable বানাও |
| `valueFrom.secretKeyRef` | Secret-এর নির্দিষ্ট key একটা variable-এ |
| `volumes.configMap` + `volumeMounts` | ConfigMap-কে ফাইল হিসেবে `/etc/api`-এ; ফাইল নিজে আপডেট হয়, env variable হয় না |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| Secret YAML Git-এ commit করা | "base64 তো, গোপন" | base64 যে কেউ decode করতে পারে; Sealed Secrets, SOPS বা External Secrets |
| ConfigMap বদলে ভাবা অ্যাপ নিজে নতুন মান পাবে | env variable-এর আচরণ না জানা | env হলে `rollout restart` লাগে, বা Helm-এ checksum annotation |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** ConfigMap আর Secret-এর পার্থক্য?
**উত্তর:** ConfigMap সাধারণ config, Secret সংবেদনশীল তথ্যের জন্য; Secret base64-এ রাখা হয়, RBAC দিয়ে আলাদা নিয়ন্ত্রণ, tmpfs-এ mount হয়, etcd-এ encryption চালু করা যায়।

### HPA: স্বয়ংক্রিয় scaling ও অন্যান্য workload

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **HPA** | **H**orizontal **P**od **A**utoscaler → চাপ অনুযায়ী pod সংখ্যা বাড়ানো-কমানো |
| **metrics-server** | CPU/memory ব্যবহারের তথ্য দেয় |
| **StatefulSet** | স্থির নাম ও নিজস্ব storage সহ pod (database) |
| **DaemonSet** | প্রতিটি node-এ একটা করে pod |
| **CronJob** | নির্দিষ্ট সময়ে চলা কাজ |

**সহজ ভাষায়:** HPA প্রতি ১৫ সেকেন্ডে metric দেখে: গড় CPU লক্ষ্যের বেশি হলে pod বাড়ায়, কম হলে ধীরে কমায়। এর জন্য container-এ CPU request দেওয়া আর metrics-server থাকা বাধ্যতামূলক, কারণ ব্যবহার % হিসাব হয় request-এর তুলনায়। Deployment stateless অ্যাপের জন্য; database-এর জন্য StatefulSet, প্রতিটি node-এ agent (log/metric collector)-এর জন্য DaemonSet, একবারের কাজে Job, নিয়মিত কাজে CronJob।

> **বাস্তব পরিস্থিতি:** সন্ধ্যা ৭টায় অর্ডার বাড়লে API-এর CPU ৯০%। HPA ৩ থেকে ১২টা pod করল, রাত ১১টার পর আবার ৩। আর প্রতিটি node-এ Node Exporter আর Fluent Bit DaemonSet হিসেবে চলে, নতুন node যোগ হলে নিজে সেখানেও।

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api
  namespace: prod
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 3
  maxReplicas: 15
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

```bash
# বা এক লাইনে
kubectl autoscale deployment api --cpu-percent=70 --min=3 --max=15 -n prod
kubectl get hpa -n prod -w
```

```yaml
# CronJob: প্রতি রাত ২টায় backup
apiVersion: batch/v1
kind: CronJob
metadata: { name: db-backup }
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: OnFailure
          containers:
            - name: backup
              image: acme/pg-backup:1.2
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `scaleTargetRef` | কোন Deployment-কে scale করবে |
| `minReplicas` / `maxReplicas` | সর্বনিম্ন আর সর্বোচ্চ pod |
| `averageUtilization: 70` | সব pod-এর গড় CPU request-এর ৭০% রাখার চেষ্টা |
| `get hpa -w` | TARGETS কলামে বর্তমান/লক্ষ্য (যেমন 85%/70%) live দেখো |
| `schedule: "0 2 * * *"` | cron ফরম্যাট: প্রতিদিন রাত ২টা |
| `restartPolicy: OnFailure` | fail করলে আবার চেষ্টা |

| Workload | কখন |
|---|---|
| Deployment | stateless web/API |
| StatefulSet | database, Kafka; স্থির নাম ও নিজস্ব PVC লাগে |
| DaemonSet | প্রতিটি node-এ agent: log, metric, network plugin |
| Job | একবার চলে শেষ: migration |
| CronJob | নিয়মিত সময়ে: backup, রিপোর্ট |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** HPA কাজ করছে না, TARGETS-এ `<unknown>`। কেন?
**উত্তর:** metrics-server নেই বা কাজ করছে না, অথবা container-এ CPU request দেওয়া নেই (শতাংশ হিসাব করা যায় না)।

**প্রশ্ন:** Deployment আর StatefulSet-এর পার্থক্য?
**উত্তর:** Deployment-এর pod বিনিময়যোগ্য, এলোমেলো নাম, শেয়ার্ড বা কোনো storage নেই। StatefulSet-এর pod-এর স্থির নাম (`db-0`, `db-1`), ক্রমানুসারে চালু/বন্ধ, আর প্রতিটির নিজস্ব স্থায়ী volume।

---

## ০৩. Service, Ingress ও Kubernetes networking

Pod-এর IP বদলায়, pod আসে-যায়। তাহলে অন্যরা কীভাবে খুঁজে পাবে? উত্তর: Service।

### Service কেন আর কীভাবে কাজ করে

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Service** | pod গুলোর সামনে স্থির নাম ও IP |
| **ClusterIP** | শুধু cluster-এর ভেতরে পাওয়া যায় এমন স্থির IP |
| **Endpoints / EndpointSlice** | Service-এর পেছনের বর্তমান Ready pod IP-এর তালিকা |
| **CoreDNS** | cluster-এর DNS সার্ভার |

**সহজ ভাষায়:** Service একটা স্থির নাম (DNS) আর স্থির IP দেয়, আর label selector দিয়ে খুঁজে বের করে কোন pod গুলোতে traffic যাবে। Pod মরলে বা নতুন আসলে Service-এর endpoint তালিকা নিজে আপডেট হয়। শুধু Ready (readiness পাস) pod গুলো তালিকায় থাকে। kube-proxy প্রতিটি node-এ নিয়ম বসায় যাতে Service IP-তে আসা traffic কোনো একটা pod-এ যায় (load balancing)।

> **বাস্তব পরিস্থিতি:** Frontend-কে API-এর সাথে কথা বলতে হবে। API-এর pod তিনটা, IP বারবার বদলায়। Frontend শুধু `http://api.prod.svc.cluster.local:8000` (বা একই namespace-এ শুধু `http://api:8000`) ব্যবহার করে; কোন pod উত্তর দিচ্ছে তা জানার দরকারই নেই।

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api
  namespace: prod
spec:
  type: ClusterIP
  selector:
    app: api
  ports:
    - name: http
      port: 8000
      targetPort: 8000
```

```bash
kubectl get svc,endpoints api -n prod
kubectl get endpointslices -n prod -l kubernetes.io/service-name=api
kubectl run tmp --rm -it --image=busybox:1.36 -n prod -- wget -qO- http://api:8000/healthz

# DNS নাম: <service>.<namespace>.svc.cluster.local
# api.prod.svc.cluster.local
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `selector: app: api` | `app=api` label যুক্ত সব Ready pod-এ traffic |
| `port: 8000` | Service-এর নিজের port (ক্লায়েন্ট এখানে আসে) |
| `targetPort: 8000` | pod-এর container-এর port; আলাদা হতে পারে |
| `get svc,endpoints` | Service আর তার পেছনের pod IP; ENDPOINTS খালি মানে কোনো pod মেলেনি বা Ready নয় |
| `kubectl run tmp --rm -it ...` | সাময়িক pod থেকে ভেতর থেকে Service পরীক্ষা, শেষে মুছে যাবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Service কীভাবে জানে কোন pod-এ traffic পাঠাবে?
**উত্তর:** Label selector দিয়ে; selector-এর সাথে মেলা আর readiness পাস করা pod গুলোর IP EndpointSlice-এ রাখা হয়, kube-proxy সেগুলোর মধ্যে traffic ভাগ করে।

**প্রশ্ন:** port আর targetPort-এর পার্থক্য?
**উত্তর:** `port` হলো Service-এর port যেখানে ক্লায়েন্ট সংযোগ করে; `targetPort` হলো pod-এর container-এর port যেখানে traffic পৌঁছায়।

### Service-এর ধরন: ClusterIP, NodePort, LoadBalancer, Headless

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **NodePort** | প্রতিটি node-এর একটা port (30000-32767) দিয়ে বাইরে থেকে |
| **LoadBalancer** | cloud-এর load balancer তৈরি করে বাইরে খোলা |
| **Headless** | headless service (`clusterIP: None`) → IP ছাড়া, DNS সরাসরি pod IP দেয় |
| **ExternalName** | বাইরের নামের DNS alias |

**সহজ ভাষায়:** ClusterIP (default) শুধু ভেতরে। NodePort প্রতিটি node-এর `IP:30000-32767` port-এ খোলে, পরীক্ষা বা নিজের load balancer-এর পেছনে। LoadBalancer cloud-এ (AWS/GCP) আসল load balancer বানিয়ে public IP দেয়, প্রতিটি Service-এর জন্য আলাদা LB তাই খরচ বেশি। Headless StatefulSet-এর মতো ক্ষেত্রে প্রতিটি pod-কে আলাদা নামে খুঁজতে। অনেক HTTP সার্ভিস বাইরে খুলতে একটা Ingress ভালো।

> **বাস্তব পরিস্থিতি:** একটা dev cluster-এ দ্রুত পরীক্ষার জন্য NodePort; production-এ ২০টা সার্ভিসের জন্য ২০টা LoadBalancer দিলে মাসিক খরচ অনেক। তাই একটা Ingress controller (একটা LoadBalancer) রেখে host/path অনুযায়ী ২০টা সার্ভিসে ভাগ।

```yaml
spec:
  type: NodePort
  selector: { app: web }
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080          # http://<node-ip>:30080
---
spec:
  type: LoadBalancer           # cloud: EXTERNAL-IP পাবে
  selector: { app: web }
  ports: [{ port: 80, targetPort: 80 }]
```

```bash
kubectl expose deployment web --port=80 --target-port=80 --type=NodePort -n dev
kubectl get svc -n dev
```

| ধরন | কোথা থেকে পৌঁছানো যায় | কখন |
|---|---|---|
| ClusterIP | শুধু cluster-এর ভেতরে | ভেতরের সার্ভিস (API, DB) |
| NodePort | `node IP:30000-32767` | পরীক্ষা, on-prem-এ নিজের LB-এর পেছনে |
| LoadBalancer | cloud LB-এর public IP | একটা TCP/UDP সার্ভিস বাইরে খুলতে |
| Headless | সরাসরি pod IP (DNS) | StatefulSet, client-side load balancing |
| ExternalName | বাইরের DNS নাম | বাইরের database-কে ভেতরের নামে ডাকা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** ClusterIP, NodePort আর LoadBalancer-এর পার্থক্য?
**উত্তর:** ClusterIP শুধু ভেতরে; NodePort প্রতিটি node-এর নির্দিষ্ট port দিয়ে বাইরে; LoadBalancer cloud provider-এর load balancer দিয়ে public ঠিকানায়। LoadBalancer ভেতরে NodePort আর ClusterIP-ও তৈরি করে।

### Ingress: অনেক সার্ভিসের একটা দরজা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Ingress** | (প্রবেশ) → HTTP/HTTPS routing-এর নিয়ম |
| **Ingress Controller** | নিয়ম বাস্তবায়নকারী (NGINX, Traefik, AWS ALB) |
| **TLS** | **T**ransport **L**ayer **S**ecurity → HTTPS এনক্রিপশন |
| **cert-manager** | certificate manager → স্বয়ংক্রিয় TLS certificate |

**সহজ ভাষায়:** Ingress হলো Layer 7 নিয়ম: কোন domain আর কোন path কোন Service-এ যাবে। কিন্তু Ingress নিজে কিছু করে না; cluster-এ একটা Ingress controller (যেমন ingress-nginx) চালাতে হয়, যেটা নিয়ম পড়ে proxy হিসেবে কাজ করে। TLS certificate cert-manager দিয়ে Let's Encrypt থেকে স্বয়ংক্রিয়।

> **বাস্তব পরিস্থিতি:** `shop.example.com`-এ frontend, `shop.example.com/api`-এ API, `admin.example.com`-এ admin panel: তিনটা সার্ভিস, একটা public IP, HTTPS সহ।

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: shop
  namespace: prod
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  ingressClassName: nginx
  tls:
    - hosts: [shop.example.com]
      secretName: shop-tls
  rules:
    - host: shop.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service: { name: api, port: { number: 8000 } }
          - path: /
            pathType: Prefix
            backend:
              service: { name: web, port: { number: 80 } }
```

```bash
kubectl get ingress -n prod
kubectl describe ingress shop -n prod
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `ingressClassName: nginx` | কোন controller এই নিয়ম বাস্তবায়ন করবে |
| `tls.secretName` | certificate এই Secret-এ থাকবে (cert-manager বানাবে) |
| `host` | কোন domain-এর জন্য |
| `path: /api, pathType: Prefix` | `/api` দিয়ে শুরু সব path api service-এ |
| `backend.service` | কোন Service আর port-এ পাঠাবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Ingress আর LoadBalancer Service-এর পার্থক্য?
**উত্তর:** LoadBalancer Service Layer 4, একটা Service-এর জন্য একটা LB। Ingress Layer 7, host/path অনুযায়ী অনেক Service-কে একটা প্রবেশপথ দিয়ে route করে, TLS সামলায়; কিন্তু একটা Ingress controller লাগে।

---

## ০৪. Kubernetes: Scenario ভিত্তিক অনুশীলন (Lab)

kind বা minikube-এ নিজে করো। প্রথমে শুধু "কাজ" আর "পরিস্থিতি" পড়ে নিজে চেষ্টা করো, তারপর সমাধান মিলিয়ে দেখো। এগুলো CKA/CKAD পরীক্ষা আর বাস্তব on-call-এর মতো করে সাজানো।

### অনুশীলন ১: প্রথম অ্যাপ deploy আর browser-এ খোলা

`🟢 বেসিক`

**কাজ (Task):** `dev` namespace-এ `nginx:1.27`-এর ৩টা কপি চালাও, NodePort Service দিয়ে খোলো, আর browser বা curl দিয়ে পেজ দেখো। তারপর একটা pod মুছে দেখো কী হয়।

> **পরিস্থিতি:** তোমার প্রথম দিনের কাজ: টিম দেখতে চায় তুমি Deployment আর Service-এর মৌলিক কাজ পারো কিনা।

```bash
kubectl create namespace dev
kubectl create deployment web --image=nginx:1.27 --replicas=3 -n dev
kubectl expose deployment web --port=80 --type=NodePort -n dev
kubectl get deploy,pods,svc -n dev -o wide

# kind/minikube-এ সহজ উপায়:
kubectl port-forward svc/web 8080:80 -n dev
curl http://localhost:8080

# self-healing পরীক্ষা
kubectl delete pod -n dev $(kubectl get pod -n dev -l app=web -o name | head -1 | cut -d/ -f2)
kubectl get pods -n dev -w

# declarative রূপ বের করে রাখো (পরে Git-এ রাখতে)
kubectl create deployment web --image=nginx:1.27 --replicas=3 --dry-run=client -o yaml > web.yaml
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `create deployment ... --replicas=3` | imperative ভাবে দ্রুত Deployment তৈরি; label হবে `app=web` |
| `expose deployment web --type=NodePort` | Deployment-এর label নিয়ে Service বানাও |
| `port-forward svc/web 8080:80` | Service-এ ল্যাপটপ থেকে সুড়ঙ্গ |
| `delete pod ...` তারপর `-w` | ReplicaSet নিজে নতুন pod বানায়, ৩টা ধরে রাখে |
| `--dry-run=client -o yaml` | কিছু তৈরি না করে YAML ছাপাও; YAML লেখার দ্রুততম শুরু |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `--dry-run=client -o yaml` কেন কাজের?
**উত্তর:** হাতে YAML না লিখে সঠিক কাঠামোর YAML দ্রুত পাওয়া যায়, তারপর সম্পাদনা করে Git-এ রাখা যায়; পরীক্ষায় অনেক সময় বাঁচায়।

> 💡 **প্রো টিপ:** নিজে চেষ্টা: replicas 5 করো, তারপর image `nginx:1.27-alpine`-এ বদলে `rollout status` দেখো।

### অনুশীলন ২: Pod CrashLoopBackOff-এ

`🟢 বেসিক`

**কাজ (Task):** নিচের Deployment apply করো। Pod কেন চালু হচ্ছে না খুঁজে বের করো আর ঠিক করো।

> **পরিস্থিতি:** রাত ১টায় alert: "api pods restarting"। নতুন version deploy হয়েছে ১০ মিনিট আগে।

```yaml
# ভাঙা অবস্থা
apiVersion: apps/v1
kind: Deployment
metadata: { name: api, namespace: dev }
spec:
  replicas: 2
  selector: { matchLabels: { app: api } }
  template:
    metadata: { labels: { app: api } }
    spec:
      containers:
        - name: api
          image: busybox:1.36
          command: ['sh', '-c', 'if [ -z "$DATABASE_URL" ]; then echo "DATABASE_URL not set"; exit 1; fi; sleep 3600']
```

```bash
# খোঁজা
kubectl get pods -n dev                                  # STATUS: CrashLoopBackOff, RESTARTS বাড়ছে
kubectl describe pod -n dev -l app=api | tail -20        # Last State: Terminated, Exit Code: 1
kubectl logs -n dev deploy/api --previous                # DATABASE_URL not set

# সমাধান
kubectl create secret generic api-secret -n dev \
  --from-literal=DATABASE_URL='postgres://app:pass@db:5432/app'
kubectl set env deployment/api -n dev --from=secret/api-secret
kubectl rollout status deployment/api -n dev
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| RESTARTS বাড়ছে | container চালু হয়ে বারবার মরছে, Kubernetes প্রতিবার অপেক্ষা বাড়াচ্ছে (১০s, ২০s, ৪০s ... সর্বোচ্চ ৫ মিনিট) |
| `Exit Code: 1` | অ্যাপ নিজে error দিয়ে বেরিয়েছে (137 হলে OOM/kill) |
| `logs --previous` | crash হওয়া আগের container-এর log; বর্তমানটায় হয়তো কিছুই নেই |
| `logs deploy/api` | Deployment-এর একটা pod-এর log, pod-এর নাম না জেনেও |
| `set env ... --from=secret/...` | Secret-এর সব key-কে environment variable হিসেবে যোগ, নতুন rollout হবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** CrashLoopBackOff দেখলে প্রথম তিনটা কমান্ড কী?
**উত্তর:** `kubectl describe pod` (Events, Last State, exit code), `kubectl logs --previous`, আর `kubectl get pod -o yaml` দিয়ে env/config যাচাই।

### অনুশীলন ৩: ImagePullBackOff

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ImagePullBackOff** | image নামাতে ব্যর্থ |
| **imagePullSecrets** | private registry-এর login তথ্য |

**কাজ (Task):** একটা deploy-এর পর pod গুলো ImagePullBackOff-এ আটকে। কারণ বের করো আর ঠিক করো। দুটো ক্ষেত্র: (ক) tag-এ বানান ভুল, (খ) private registry।

> **পরিস্থিতি:** CI pipeline image push করেছে বলে দাবি করছে, কিন্তু pod চলছে না।

```bash
kubectl get pods -n dev
kubectl describe pod -n dev <pod> | grep -A5 Events
# Failed to pull image "nginx:1.27.99": not found  -> (ক) tag নেই
# pull access denied ... unauthorized             -> (খ) login নেই

# (ক) সমাধান: সঠিক tag
kubectl set image deployment/web web=nginx:1.27 -n dev

# (খ) সমাধান: registry secret বানাও আর Deployment-এ দাও
kubectl create secret docker-registry ghcr-cred -n dev \
  --docker-server=ghcr.io --docker-username=rahim --docker-password="$GHCR_TOKEN"
kubectl patch deployment web -n dev -p \
  '{"spec":{"template":{"spec":{"imagePullSecrets":[{"name":"ghcr-cred"}]}}}}'
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `describe ... Events` | "Failed to pull"-এর পরের বার্তাই কারণ বলে দেয় |
| `not found` / `manifest unknown` | নাম বা tag ভুল, বা push হয়নি |
| `unauthorized` / `denied` | private registry-তে অনুমতি নেই |
| `secret docker-registry` | registry login-এর বিশেষ ধরনের Secret |
| `kubectl patch -p '{...}'` | YAML না খুলে নির্দিষ্ট অংশ বদলাও (strategic merge) |

> 💡 **প্রো টিপ:** ভালো অভ্যাস: CI-তে image push-এর পর deploy ধাপে ঠিক সেই tag ব্যবহার করো যেটা push হয়েছে, হাতে লেখা নয়।

### অনুশীলন ৪: Service-এ traffic যাচ্ছে না

`🟡 ইন্টারমিডিয়েট`

**কাজ (Task):** নিচের Deployment আর Service apply করো। ভেতর থেকে `http://web`-এ গেলে উত্তর আসে না। দুটো ভুল খুঁজে ঠিক করো।

> **পরিস্থিতি:** Frontend টিম বলছে "web service down", অথচ সব pod Running আর Ready।

```yaml
# আগে অনুশীলন ১-এর web মুছে নাও (selector বদলানো যায় না):
# kubectl delete deploy,svc web -n dev

# ভাঙা অবস্থা
apiVersion: apps/v1
kind: Deployment
metadata: { name: web, namespace: dev }
spec:
  replicas: 2
  selector: { matchLabels: { app: web-frontend } }
  template:
    metadata: { labels: { app: web-frontend } }
    spec:
      containers:
        - { name: web, image: nginx:1.27, ports: [{ containerPort: 80 }] }
---
apiVersion: v1
kind: Service
metadata: { name: web, namespace: dev }
spec:
  selector: { app: web }
  ports: [{ port: 80, targetPort: 8080 }]
```

```bash
# খোঁজা
kubectl get endpoints web -n dev          # ENDPOINTS: <none> -> selector কোনো pod পায়নি
kubectl get pods -n dev --show-labels     # pod-এর label: app=web-frontend
kubectl describe svc web -n dev           # Selector: app=web, TargetPort: 8080

# ঠিক ১: selector মেলাও
kubectl patch svc web -n dev -p '{"spec":{"selector":{"app":"web-frontend"}}}'
kubectl get endpoints web -n dev          # এখন দুটো IP:8080

# তবুও কাজ করছে না -> ঠিক ২: nginx 80-তে শোনে, 8080-এ না
kubectl patch svc web -n dev --type=json -p '[{"op":"replace","path":"/spec/ports/0/targetPort","value":80}]'

# যাচাই
kubectl run t --rm -it --image=busybox:1.36 -n dev -- wget -qO- http://web
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `ENDPOINTS: <none>` | সবচেয়ে বড় সূত্র: Service-এর selector কোনো Ready pod-এর সাথে মেলেনি |
| `--show-labels` | pod-এর আসল label দেখে selector-এর সাথে তুলনা |
| `targetPort: 8080` | container যে port-এ শোনে না সেখানে পাঠালে connection refused |
| `--type=json -p '[...]'` | JSON patch: নির্দিষ্ট path-এর মান বদলাও (list-এর ভেতরেও) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Service কাজ না করলে কীভাবে troubleshoot করবে?
**উত্তর:** `kubectl get endpoints` (খালি হলে selector বা readiness সমস্যা), pod-এর label তুলনা, targetPort আর containerPort মেলানো, pod থেকে সরাসরি pod IP-তে পরীক্ষা, DNS (`nslookup web`), NetworkPolicy আটকাচ্ছে কিনা।

### অনুশীলন ৫: খারাপ release আর rollback

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **rollout** | নতুন version ছড়ানো |
| **revision** | সংস্করণ নম্বর |

**কাজ (Task):** web-কে `nginx:1.27` থেকে একটা ভাঙা image-এ আপডেট করো, দেখো rolling update কীভাবে আটকে যায় আর পুরনো pod গুলো চলতে থাকে। তারপর rollback করো।

> **পরিস্থিতি:** শুক্রবার বিকেলে কেউ ভুল image tag দিয়ে deploy করেছে। গ্রাহক কি প্রভাবিত হবে?

```bash
kubectl set image deployment/web web=nginx:1.27 -n dev
kubectl annotate deployment/web kubernetes.io/change-cause="stable 1.27" -n dev --overwrite
kubectl rollout status deployment/web -n dev

# ভাঙা release
kubectl set image deployment/web web=nginx:does-not-exist -n dev
kubectl annotate deployment/web kubernetes.io/change-cause="broken" -n dev --overwrite
kubectl rollout status deployment/web -n dev --timeout=60s     # আটকে থাকে
kubectl get pods -n dev -l app=web-frontend
# পুরনো pod গুলো Running থাকে, নতুনটা ImagePullBackOff

kubectl rollout history deployment/web -n dev
kubectl rollout undo deployment/web -n dev
kubectl rollout status deployment/web -n dev
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `rollout status --timeout=60s` | ৬০ সেকেন্ডে শেষ না হলে non-zero exit, CI-তে deploy fail ধরা যায় |
| পুরনো pod Running থাকে | maxUnavailable-এর কারণে নতুন pod Ready না হলে পুরনোগুলো সরানো হয় না, তাই সেবা চলতে থাকে |
| `rollout history` | CHANGE-CAUSE সহ revision তালিকা |
| `rollout undo` | আগের ভালো ReplicaSet আবার বাড়ানো হয় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Rolling update মাঝপথে আটকে গেলে গ্রাহক কি প্রভাবিত হয়?
**উত্তর:** ঠিকমতো readiness probe আর maxUnavailable থাকলে না, কারণ পুরনো pod গুলো traffic নিতে থাকে; তবে আপডেট ঝুলে থাকে, তাই rollout status timeout দিয়ে ধরা আর rollback জরুরি। `progressDeadlineSeconds` পেরোলে Deployment নিজেকে failed চিহ্নিত করে।

### অনুশীলন ৬: Pod Pending-এ আটকে

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Pending** | এখনো কোনো node-এ বসানো যায়নি |
| **taint / toleration** | node-এ দাগ / pod-এর সহনশীলতা |

**কাজ (Task):** একটা Deployment-এ memory request `64Gi` দিয়ে apply করো। Pod Pending কেন বের করো আর ঠিক করো।

> **পরিস্থিতি:** নতুন analytics সার্ভিস deploy হলো, কিন্তু pod কখনো চালু হয় না, কোনো log-ও নেই।

```bash
kubectl describe pod -n dev -l app=analytics | grep -A10 Events
# 0/3 nodes are available: 3 Insufficient memory.
kubectl describe nodes | grep -A6 "Allocatable"
kubectl top nodes

# সমাধান: বাস্তবসম্মত request
kubectl set resources deployment/analytics -n dev \
  --requests=cpu=250m,memory=512Mi --limits=cpu=1,memory=1Gi

# অন্য সম্ভাব্য বার্তা:
# node(s) didn't match Pod's node affinity/selector -> nodeSelector/label
# node(s) had untolerated taint                    -> toleration লাগবে
# pod has unbound immediate PersistentVolumeClaims -> PVC/StorageClass
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `0/3 nodes are available` | scheduler কোনো node-এ বসাতে পারেনি, পরের অংশে কারণ |
| `Allocatable` | node-এ pod-এর জন্য আসলে কত CPU/memory পাওয়া যায় |
| `set resources` | Deployment-এর request/limit বদলাও |
| Pending-এ log থাকে না | container চালুই হয়নি, তাই describe-এর Events-ই একমাত্র সূত্র |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Pod Pending থাকার কারণগুলো কী?
**উত্তর:** অপর্যাপ্ত CPU/memory (request-এর তুলনায়), nodeSelector/affinity মেলে না, node-এর taint-এর toleration নেই, PVC bind হয়নি, বা quota শেষ।

### অনুশীলন ৭: OOMKilled আর resource ঠিক করা

`🟡 ইন্টারমিডিয়েট`

**কাজ (Task):** একটা pod চালাও যেটা ইচ্ছা করে limit-এর বেশি memory নেয়। OOMKilled শনাক্ত করো আর বুঝে ঠিক করো।

> **পরিস্থিতি:** Report সার্ভিস রাতে বড় রিপোর্ট বানাতে গিয়ে বারবার restart হয়, log-এ কোনো error নেই।

```yaml
apiVersion: v1
kind: Pod
metadata: { name: memhog, namespace: dev }
spec:
  containers:
    - name: stress
      image: polinux/stress
      command: ["stress", "--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
      resources:
        limits: { memory: "100Mi" }
```

```bash
kubectl apply -f memhog.yaml
kubectl get pod memhog -n dev                           # OOMKilled, তারপর CrashLoopBackOff
kubectl describe pod memhog -n dev | grep -A4 "Last State"
# Reason: OOMKilled   Exit Code: 137
kubectl top pod -n dev

# সমাধান: প্রয়োজন মেপে limit বাড়াও (বা অ্যাপের memory ঠিক করো)
# limits: { memory: "300Mi" }   requests: { memory: "256Mi" }
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `--vm-bytes 250M` | ২৫০ MB memory নেওয়ার চেষ্টা, অথচ limit ১০০Mi |
| `Reason: OOMKilled, Exit Code: 137` | kernel memory সীমা পেরোনোয় SIGKILL দিয়েছে (128+9) |
| log-এ error নেই কেন | SIGKILL হঠাৎ মারে, অ্যাপ কিছু লেখার সুযোগই পায় না |
| Java/Node | runtime-এর heap সীমা container limit-এর নিচে রাখো (MaxRAMPercentage, max-old-space-size) |

> 💡 **প্রো টিপ:** Monitoring অংশে দেখবে কীভাবে PromQL দিয়ে OOMKilled হওয়ার আগেই সতর্কতা পাওয়া যায়।

### অনুশীলন ৮: Load বাড়লে স্বয়ংক্রিয় scale

`🔴 অ্যাডভান্সড`

**কাজ (Task):** একটা CPU-ভারী অ্যাপ deploy করো, HPA (৫০% CPU, ১-১০ pod) লাগাও, তারপর কৃত্রিম load দিয়ে দেখো pod বাড়ছে।

> **পরিস্থিতি:** মার্কেটিং টিম রাত ৮টায় flash sale ঘোষণা করবে; কতজন আসবে কেউ জানে না।

```bash
# kind-এ metrics-server লাগবে (minikube: minikube addons enable metrics-server)
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
# kind-এ: deployment-এ --kubelet-insecure-tls arg যোগ করতে হতে পারে

kubectl create deployment php -n dev --image=registry.k8s.io/hpa-example
kubectl set resources deployment php -n dev --requests=cpu=200m
kubectl expose deployment php -n dev --port=80
kubectl autoscale deployment php -n dev --cpu-percent=50 --min=1 --max=10

# অন্য terminal-এ load
kubectl run load -n dev --rm -it --image=busybox:1.36 -- \
  sh -c "while true; do wget -q -O- http://php; done"

kubectl get hpa php -n dev -w      # TARGETS 250%/50% -> REPLICAS বাড়ে
# load বন্ধ করলে ~৫ মিনিট পর ধীরে কমে (stabilization window)
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| metrics-server | HPA-এর CPU তথ্যের উৎস |
| `--requests=cpu=200m` | request ছাড়া HPA % হিসাব করতে পারে না |
| `autoscale --cpu-percent=50` | গড় CPU request-এর ৫০% রাখার লক্ষ্য |
| `while true; wget` | অবিরাম request পাঠিয়ে কৃত্রিম চাপ |
| scale down ধীরে | হঠাৎ ওঠানামা (flapping) এড়াতে HPA কমানোর আগে কিছুক্ষণ অপেক্ষা করে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** HPA কীভাবে replica সংখ্যা হিসাব করে?
**উত্তর:** `desired = ceil(current replicas x current metric / target metric)`। যেমন ২টা pod, গড় CPU ১০০%, লক্ষ্য ৫০% হলে ২ x ১০০/৫০ = ৪।

### অনুশীলন ৯: Node maintenance নিরাপদে

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **cordon** | (ঘেরাও) → node-এ নতুন pod বসানো বন্ধ |
| **drain** | (খালি করা) → node থেকে pod সরিয়ে দেওয়া |
| **PDB** | **P**od**D**isruption**B**udget → একসাথে সর্বোচ্চ কতগুলো pod বন্ধ হতে পারবে তার নিয়ম |

**কাজ (Task):** একটা worker node-এর OS আপডেট করতে হবে। সেবা বন্ধ না করে node খালি করো, কাজ শেষে ফেরত আনো।

> **পরিস্থিতি:** Security টিম জানাল সব node-এ kernel patch লাগবে আজ রাতেই।

```yaml
# web-এর জন্য PDB: সবসময় অন্তত ২টা চালু
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata: { name: web-pdb, namespace: dev }
spec:
  minAvailable: 2
  selector: { matchLabels: { app: web-frontend } }
```

```bash
kubectl cordon worker-2
kubectl drain worker-2 --ignore-daemonsets --delete-emptydir-data
# PDB মানতে গিয়ে একটা একটা করে pod সরায়, অন্য node-এ নতুন চালু হয়
# ... patch, reboot ...
kubectl uncordon worker-2
kubectl get nodes
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `cordon` | SchedulingDisabled, চলমান pod থাকে কিন্তু নতুন আসে না |
| `drain --ignore-daemonsets` | DaemonSet-এর pod থাকুক (প্রতিটি node-এই চলে) |
| `--delete-emptydir-data` | emptyDir-এর অস্থায়ী data মুছে গেলেও সরাও |
| `uncordon` | আবার নতুন pod নিতে পারবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** drain আর cordon-এর পার্থক্য?
**উত্তর:** cordon শুধু নতুন pod বসানো বন্ধ করে; drain cordon করে তারপর চলমান pod গুলো সুশৃঙ্খলভাবে উচ্ছেদ করে (PDB মেনে)।

**প্রশ্ন:** PodDisruptionBudget কী কাজে লাগে?
**উত্তর:** স্বেচ্ছায় বিঘ্নের সময় (drain, cluster upgrade) একসাথে কতগুলো pod বন্ধ হতে পারবে তা সীমিত করে, ফলে সেবা চালু থাকে।

---

# Part B: Monitoring (Prometheus, Grafana, ELK)

## ০৫. Monitoring-এর মূল ধারণা: কী দেখব, কেন দেখব

সার্ভার বা অ্যাপ ঠিক চলছে কিনা জানার তিনটা জানালা: metric, log আর trace। কোনটা কী বলে সেটা বুঝলে টুল বেছে নেওয়া সহজ।

### Metric, Log, Trace: Observability-এর তিন স্তম্ভ

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Observability** | বাইরে থেকে ভেতরের অবস্থা বোঝার ক্ষমতা |
| **Metric** | সময়ের সাথে মাপা সংখ্যা |
| **Log** | ঘটনার লিখিত বিবরণ |
| **Trace** | একটা request-এর সব সার্ভিসের মধ্য দিয়ে যাত্রা |
| **APM** | **A**pplication **P**erformance **M**onitoring → অ্যাপের পারফরম্যান্স পর্যবেক্ষণ |

**সহজ ভাষায়:** **Metric** বলে কী হচ্ছে আর কতটা (CPU ৯০%, প্রতি সেকেন্ডে ৫০টা error), কম জায়গা নেয়, alert আর graph-এর জন্য সেরা: Prometheus + Grafana। **Log** বলে ঠিক কী ঘটেছে আর কেন (কোন user, কোন error message, stack trace): ELK বা Loki। **Trace** বলে একটা request কোন কোন সার্ভিস ঘুরে কোথায় দেরি হলো: Jaeger, Tempo, OpenTelemetry। সাধারণ প্রবাহ: metric-এর alert বলে "সমস্যা আছে", log আর trace বলে "কোথায় আর কেন"।

> **বাস্তব পরিস্থিতি:** রাত ২টায় alert: checkout-এর error rate ৮% (metric, Grafana)। Kibana-তে checkout সার্ভিসের log খুলে দেখা গেল "payment gateway timeout" (log)। Trace-এ দেখা গেল payment সার্ভিস ব্যাংকের API-তে ৩০ সেকেন্ড অপেক্ষা করছে (trace)। ২০ মিনিটে মূল কারণ পাওয়া গেল।

| বিষয় | Metric | Log | Trace |
|---|---|---|---|
| প্রশ্ন | কী হচ্ছে? কতটা? | ঠিক কী ঘটল? কেন? | কোথায় দেরি/ভুল? |
| রূপ | সংখ্যা + সময় + label | লেখা (ভালো হলে JSON) | span-এর গাছ |
| খরচ | কম | বেশি (অনেক data) | মাঝারি (sampling) |
| ব্যবহার | dashboard, alert, trend | debug, audit, তদন্ত | microservice-এর latency খোঁজা |
| টুল | Prometheus, Grafana, Datadog | ELK/EFK, Loki, CloudWatch Logs | Jaeger, Tempo, Zipkin, OpenTelemetry |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Monitoring আর observability-এর পার্থক্য?
**উত্তর:** Monitoring আগে থেকে জানা প্রশ্নের উত্তর দেয় (CPU কত, সাইট চালু কিনা)। Observability নতুন অজানা প্রশ্নের উত্তর খোঁজার ক্ষমতা দেয়, metric, log আর trace একসাথে মিলিয়ে।

### কী মাপব: Golden Signals, RED, USE, SLO

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **RED** | **R**ate, **E**rrors, **D**uration → সার্ভিসের জন্য: request হার, error, সময় |
| **USE** | **U**tilization, **S**aturation, **E**rrors → resource-এর জন্য: ব্যবহার, চাপ, error |
| **SLI** | **S**ervice **L**evel **I**ndicator → মাপা সূচক (যেমন সফল request %) |
| **SLO** | **S**ervice **L**evel **O**bjective → লক্ষ্য (যেমন ৯৯.৯% সফল) |
| **SLA** | **S**ervice **L**evel **A**greement → গ্রাহকের সাথে চুক্তি, ভাঙলে জরিমানা |

**সহজ ভাষায়:** সব কিছু মাপলে আসল সংকেত হারিয়ে যায়। Google-এর চারটা Golden Signal: latency, traffic, errors, saturation। প্রতিটি সার্ভিসের জন্য RED, প্রতিটি resource (CPU, disk, network)-এর জন্য USE। তারপর ব্যবহারকারীর দৃষ্টিকোণ থেকে SLO ঠিক করো, আর alert দাও SLO ঝুঁকিতে পড়লে, শুধু CPU বেশি হলে নয়।

> **বাস্তব পরিস্থিতি:** আগে CPU ৮০% হলেই রাতে alert, অর্ধেক সময় কোনো সমস্যাই না। এখন alert শুধু তখন যখন checkout-এর ৯৯.৫% SLO ভাঙার ঝুঁকি (error rate আর p95 latency)। রাতের অপ্রয়োজনীয় alert ৭০% কমেছে।

| পদ্ধতি | কিসের জন্য | কী মাপে |
|---|---|---|
| Golden Signals | যেকোনো user-facing সিস্টেম | Latency, Traffic, Errors, Saturation |
| RED | সার্ভিস/API | Rate (req/s), Errors (error/s), Duration (p50/p95/p99) |
| USE | hardware/resource | Utilization (%), Saturation (queue/load), Errors |

| উদাহরণ | মান |
|---|---|
| SLI | সফল HTTP request (5xx নয়) / মোট request |
| SLO | ৩০ দিনে ৯৯.৯% |
| Error budget | ১০০% - ৯৯.৯% = ০.১% = ৩০ দিনে প্রায় ৪৩ মিনিট ব্যর্থতা গ্রহণযোগ্য |
| SLA | ৯৯.৫%-এর নিচে গেলে গ্রাহককে টাকা ফেরত |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** SLI, SLO আর SLA-এর পার্থক্য?
**উত্তর:** SLI হলো মাপা সংখ্যা (সফল request-এর %), SLO সেই সংখ্যার অভ্যন্তরীণ লক্ষ্য (৯৯.৯%), SLA গ্রাহকের সাথে আইনি চুক্তি যা সাধারণত SLO-এর চেয়ে ঢিলা।

**প্রশ্ন:** p95 latency মানে কী, গড় কেন যথেষ্ট না?
**উত্তর:** ৯৫% request-এর চেয়ে কম সময় লেগেছে এমন মান। গড় কয়েকটা খুব ধীর request লুকিয়ে ফেলে; ব্যবহারকারীর অভিজ্ঞতা বোঝাতে percentile ভালো।

---

## ০৬. Prometheus: metric সংগ্রহ, PromQL ও Alert

Kubernetes দুনিয়ার default monitoring সিস্টেম। CNCF-এর দ্বিতীয় graduated প্রজেক্ট (Kubernetes-এর পরেই)।

### Prometheus-এর architecture

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Prometheus** | (গ্রিক দেবতা, মানুষকে আগুন দিয়েছিলেন) → metric সংগ্রহ, সংরক্ষণ ও alert |
| **TSDB** | **T**ime **S**eries **D**ata**B**ase → সময়ভিত্তিক data-র database |
| **Exporter** | কোনো সিস্টেমের metric Prometheus ফরম্যাটে দেয় |
| **scrape** | নির্দিষ্ট সময় পরপর `/metrics` থেকে টেনে আনা |
| **Alertmanager** | alert দলবদ্ধ করে সঠিক জায়গায় পাঠায় |

**সহজ ভাষায়:** Prometheus নিজে টার্গেটদের কাছে গিয়ে প্রতি ১৫-৩০ সেকেন্ডে HTTP দিয়ে `/metrics` টেনে আনে (pull model)। টার্গেট খোঁজে service discovery দিয়ে (Kubernetes API থেকে নিজে)। Data নিজের TSDB-তে রাখে। নিয়ম অনুযায়ী alert হিসাব করে Alertmanager-কে পাঠায়, Alertmanager Slack/PagerDuty/Email-এ পাঠায়। Grafana Prometheus থেকে query করে graph দেখায়। যেসব জিনিসের নিজস্ব `/metrics` নেই তাদের জন্য exporter: node_exporter (Linux সার্ভার), kube-state-metrics (Kubernetes object-এর অবস্থা), blackbox_exporter (বাইরে থেকে URL পরীক্ষা), postgres_exporter।

> **বাস্তব পরিস্থিতি:** প্রতিটি node-এ node_exporter DaemonSet হিসেবে চলে CPU/memory/disk দেয়, kube-state-metrics দেয় কোন pod কতবার restart হলো, আর API সার্ভিস নিজে `/metrics`-এ request সংখ্যা ও latency দেয়। Prometheus সব একসাথে টেনে আনে।

```text
[node_exporter] [kube-state-metrics] [app /metrics] [blackbox]
       ^                 ^                  ^            ^
       └────── scrape (pull, প্রতি ৩০s) ────┴────────────┘
                         │
              [ Prometheus (TSDB, rules) ] ──alerts──> [ Alertmanager ] ──> Slack / PagerDuty / Email
                         ^
                         │ PromQL query
                    [ Grafana ] (dashboard)
```

```text
# /metrics দেখতে কেমন (Prometheus exposition format)
# HELP http_requests_total Total HTTP requests
# TYPE http_requests_total counter
http_requests_total{method="GET",path="/checkout",status="200"} 10423
http_requests_total{method="GET",path="/checkout",status="500"} 37
# TYPE node_memory_MemAvailable_bytes gauge
node_memory_MemAvailable_bytes 2.1474836e+09
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Prometheus-এর pull model-এর সুবিধা কী?
**উত্তর:** Prometheus নিজে ঠিক করে কখন কাকে টানবে; টার্গেট down হলে সাথে সাথে বোঝা যায় (`up == 0`); টার্গেটকে monitoring সার্ভারের ঠিকানা জানতে হয় না। স্বল্পমেয়াদি job-এর জন্য Pushgateway।

**প্রশ্ন:** Exporter কী? দুটো উদাহরণ দাও।
**উত্তর:** যে প্রোগ্রাম কোনো সিস্টেমের তথ্য Prometheus ফরম্যাটে `/metrics`-এ দেয়। node_exporter (Linux hardware/OS), kube-state-metrics (Kubernetes object-এর অবস্থা), mysqld_exporter, blackbox_exporter।

### Kubernetes-এ Prometheus ও Grafana বসানো (kube-prometheus-stack)

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Helm** | (জাহাজের হাল) → Kubernetes-এর package manager |
| **Operator** | Prometheus Operator → Prometheus-কে Kubernetes resource দিয়ে চালায় |
| **ServiceMonitor** | কোন Service থেকে metric টানবে তার নিয়ম (CRD) |
| **CRD** | **C**ustom **R**esource **D**efinition → Kubernetes-এ নতুন ধরনের resource |

**সহজ ভাষায়:** kube-prometheus-stack Helm chart একবারে Prometheus, Alertmanager, Grafana, node_exporter, kube-state-metrics আর তৈরি dashboard ও alert নিয়ম বসিয়ে দেয়। নিজের অ্যাপের metric টানতে একটা ServiceMonitor বানাও: কোন label-এর Service, কোন port, কোন path।

> **বাস্তব পরিস্থিতি:** নতুন cluster-এ monitoring চাই আজই। ১০ মিনিটে stack বসল, Grafana-তে cluster, node আর pod-এর dashboard তৈরি। তারপর নিজের API-এর জন্য একটা ServiceMonitor যোগ করায় API-এর request rate আর latency-ও দেখা যাচ্ছে।

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring --create-namespace
kubectl get pods -n monitoring
kubectl port-forward svc/monitoring-kube-prometheus-prometheus 9090:9090 -n monitoring
kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
kubectl get secret monitoring-grafana -n monitoring \
  -o jsonpath='{.data.admin-password}' | base64 -d
```

```yaml
# নিজের অ্যাপের ServiceMonitor
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: api
  namespace: prod
  labels:
    release: monitoring          # chart-এর Prometheus এই label দেখে খোঁজে
spec:
  selector:
    matchLabels: { app: api }    # এই label-এর Service
  endpoints:
    - port: http                 # Service-এর port-এর নাম
      path: /metrics
      interval: 30s
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `helm install <release> <chart>` | chart থেকে release তৈরি; `monitoring` = release-এর নাম |
| `--namespace --create-namespace` | কোন namespace-এ · না থাকলে বানাও |
| `port-forward ... 9090:9090` | Prometheus UI → browser-এ `localhost:9090` |
| `jsonpath='{.data.admin-password}'` | JSON path → Secret থেকে Grafana admin password বের করো |

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `labels.release: monitoring` | সবচেয়ে কমন ভুল এখানে: এই label না দিলে Prometheus ServiceMonitor উপেক্ষা করে |
| `selector.matchLabels` | কোন Service-এর পেছনের pod থেকে টানবে |
| `port: http` | Service-এ port-এর নাম দেওয়া থাকতে হবে (সংখ্যা নয়) |
| Prometheus UI > Status > Targets | টার্গেট UP কিনা আর error কী, এখানে যাচাই |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** ServiceMonitor বানিয়েছ কিন্তু Prometheus-এর Targets-এ দেখা যাচ্ছে না। কী দেখবে?
**উত্তর:** ServiceMonitor-এ Prometheus-এর serviceMonitorSelector-এর সাথে মেলা label (যেমন `release: monitoring`) আছে কিনা, namespace selector, selector-এর সাথে Service-এর label মেলে কিনা, Service-এ port-এর নাম ঠিক কিনা, আর অ্যাপ সত্যিই `/metrics` দিচ্ছে কিনা।

### Metric-এর ধরন ও PromQL

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **PromQL** | **Prom**etheus **Q**uery **L**anguage → Prometheus-এর query ভাষা |
| **Counter** | শুধু বাড়ে (মোট request) |
| **Gauge** | বাড়ে-কমে (memory, তাপমাত্রা) |
| **Histogram** | মানের বণ্টন bucket-এ (latency) |
| **label** | metric-এর মাত্রা: method, status, pod |

**সহজ ভাষায়:** প্রতিটি metric = নাম + label + সময় + মান। Counter থেকে সরাসরি মান অর্থহীন (শুধু বাড়তেই থাকে), তাই `rate()` দিয়ে প্রতি সেকেন্ডের হার বের করো। Gauge সরাসরি ব্যবহার করো। Histogram থেকে `histogram_quantile` দিয়ে p95/p99 latency। `sum by (label)` দিয়ে দলবদ্ধ করো। `[5m]` হলো range: শেষ ৫ মিনিটের data।

> **বাস্তব পরিস্থিতি:** ম্যানেজার জিজ্ঞেস করলেন: "checkout-এ এখন প্রতি সেকেন্ডে কতগুলো request, কত শতাংশ fail করছে, আর ৯৫% ব্যবহারকারী কত দ্রুত উত্তর পাচ্ছে?" তিনটা PromQL query-তে উত্তর।

```promql
# Request rate (প্রতি সেকেন্ডে), সার্ভিস অনুযায়ী
sum by (service) (rate(http_requests_total[5m]))

# Error rate % (5xx)
100 * sum(rate(http_requests_total{status=~"5.."}[5m]))
    / sum(rate(http_requests_total[5m]))

# p95 latency (সেকেন্ড)
histogram_quantile(0.95, sum by (le) (rate(http_request_duration_seconds_bucket[5m])))

# কোন টার্গেট down
up == 0

# Node CPU ব্যবহার %
100 * (1 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])))

# Node memory ব্যবহার %
100 * (1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)

# Disk ৪ ঘণ্টায় ভরে যাবে কিনা (ভবিষ্যদ্বাণী)
predict_linear(node_filesystem_avail_bytes{mountpoint="/"}[6h], 4*3600) < 0

# গত ১ ঘণ্টায় ৩ বারের বেশি restart হওয়া container
increase(kube_pod_container_status_restarts_total[1h]) > 3

# Pod-এর memory, limit-এর কত % (OOM-এর আগাম সতর্কতা)
sum by (namespace, pod) (container_memory_working_set_bytes{container!=""})
  / sum by (namespace, pod) (kube_pod_container_resource_limits{resource="memory"}) * 100
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `rate(x[5m])` | শেষ ৫ মিনিটে counter গড়ে প্রতি সেকেন্ডে কত বেড়েছে; counter-এ সবসময় rate/increase |
| `sum by (service)` | service label অনুযায়ী যোগ করো, বাকি label বাদ |
| `{status=~"5.."}` | `=~` মানে regex মিল: 500 থেকে 599 |
| `histogram_quantile(0.95, ... by (le))` | `le` = less than or equal, bucket-এর সীমা; এটা রাখতেই হবে |
| `up == 0` | Prometheus যে টার্গেট টানতে পারেনি |
| `mode="idle"` | CPU-এর অলস সময়; ১ - idle = ব্যবহার |
| `predict_linear(..., 4*3600) < 0` | গত ৬ ঘণ্টার প্রবণতা ধরে ৪ ঘণ্টা পরে খালি জায়গা শূন্যের নিচে যাবে কিনা |
| `increase(x[1h])` | শেষ ১ ঘণ্টায় counter মোট কত বেড়েছে |
| `container!=""` | pod স্তরের সমষ্টি সারি বাদ দিয়ে শুধু আসল container |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `rate()` আর `irate()`-এর পার্থক্য?
**উত্তর:** `rate` পুরো range-এর গড় হার, মসৃণ, alert-এর জন্য ভালো। `irate` শুধু শেষ দুটো বিন্দু দেখে, হঠাৎ পরিবর্তন ধরে, graph-এ চঞ্চল।

**প্রশ্ন:** Counter-এর কাঁচা মান কেন graph-এ দেখাবে না?
**উত্তর:** এটা শুধু বাড়ে আর process restart-এ শূন্য হয়ে যায়; অর্থপূর্ণ হলো পরিবর্তনের হার, তাই `rate()` বা `increase()`, যা restart-ও নিজে সামলায়।

**প্রশ্ন:** High cardinality সমস্যা কী?
**উত্তর:** Label-এ user ID, request ID-এর মতো অসংখ্য মান থাকলে লক্ষ লক্ষ time series তৈরি হয়, Prometheus-এর memory শেষ। Label-এ সীমিত মান রাখো।

> 💡 **প্রো টিপ:** Prometheus UI-এর Graph ট্যাবে প্রতিটি query চালিয়ে অনুশীলন করো; Grafana-তে বসানোর আগে সেখানে ঠিক করো।

### Alert rule ও Alertmanager

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **PrometheusRule** | (CRD) → alert নিয়মের Kubernetes resource |
| **for** | for duration → কতক্ষণ ধরে শর্ত সত্য থাকলে alert |
| **severity** | গুরুত্ব: critical, warning |
| **silence / inhibit** | সাময়িক চুপ / এক alert অন্যটাকে চাপা দেওয়া |

**সহজ ভাষায়:** Alert rule হলো একটা PromQL শর্ত। `for` দিয়ে বলো কতক্ষণ সত্য থাকলে তবেই alert (হঠাৎ এক মুহূর্তের spike-এ ঘুম ভাঙবে না)। Alertmanager একই ধরনের alert একসাথে করে (group), severity অনুযায়ী আলাদা জায়গায় পাঠায় (critical = PagerDuty ফোন, warning = Slack), maintenance-এর সময় silence করা যায়। প্রতিটি alert-এ runbook লিংক দাও: কী করতে হবে।

> **বাস্তব পরিস্থিতি:** রাতে একটা node-এর disk ৮৫% হলে warning Slack-এ, কিন্তু "৪ ঘণ্টায় ভরে যাবে" হলে critical, on-call engineer-এর ফোনে। Checkout-এর error rate ৫ মিনিট ধরে ৫%-এর বেশি হলে critical।

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: shop-alerts
  namespace: monitoring
  labels: { release: monitoring }
spec:
  groups:
    - name: shop.rules
      rules:
        - alert: HighErrorRate
          expr: |
            100 * sum(rate(http_requests_total{service="checkout",status=~"5.."}[5m]))
                / sum(rate(http_requests_total{service="checkout"}[5m])) > 5
          for: 5m
          labels:
            severity: critical
          annotations:
            summary: "Checkout error rate {{ $value | humanize }}%"
            runbook_url: https://wiki.acme.com/runbooks/checkout-errors
        - alert: PodRestartingTooMuch
          expr: increase(kube_pod_container_status_restarts_total[1h]) > 3
          for: 10m
          labels: { severity: warning }
          annotations:
            summary: "{{ $labels.namespace }}/{{ $labels.pod }} restarted {{ $value }} times in 1h"
```

```yaml
# Alertmanager config (অংশ)
route:
  receiver: slack-default
  group_by: [alertname, namespace]
  group_wait: 30s
  repeat_interval: 4h
  routes:
    - matchers: [ severity = "critical" ]
      receiver: pagerduty-oncall
receivers:
  - name: slack-default
    slack_configs:
      - api_url: https://hooks.slack.com/services/XXX
        channel: "#alerts"
  - name: pagerduty-oncall
    pagerduty_configs:
      - routing_key: <PAGERDUTY_KEY>
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `expr` | এই PromQL সত্য (কোনো ফল) হলে alert pending |
| `for: 5m` | ৫ মিনিট ধরে সত্য থাকলে firing; ছোট spike-এ নয় |
| `labels.severity` | Alertmanager এই label দেখে কোথায় পাঠাবে ঠিক করে |
| `{{ $value }}` / `{{ $labels.pod }}` | বার্তায় আসল মান আর label বসাও |
| `runbook_url` | on-call-কে কী করতে হবে তার লিংক |
| `group_by` | একই alertname আর namespace-এর alert একসাথে একটা বার্তায় |
| `group_wait` / `repeat_interval` | প্রথম বার্তার আগে ৩০s অপেক্ষা (দল জমতে), একই alert ৪ ঘণ্টা পরপর মনে করানো |
| `routes.matchers severity = critical` | critical গুলো PagerDuty-তে, বাকিরা default Slack-এ |

**ভুল, কেন হয় ও সমাধান**

| ভুল | কেন করে | কীভাবে ঠিক করবে |
|---|---|---|
| প্রতিটি metric-এ alert, রাতে ৫০টা alert | "সব কিছু জানা ভালো" | শুধু ব্যবহারকারীর ক্ষতি বা আসন্ন ক্ষতিতে alert (symptom-based, SLO-based) |
| `for` ছাড়া alert | জানা নেই | `for: 5m`-এর মতো সময় দাও |
| Alert-এ কী করতে হবে লেখা নেই | সময়ের অভাব | প্রতিটি alert-এ summary আর runbook |
| Monitoring সিস্টেম নিজে down হলে কেউ জানে না | কে পাহারাদারকে পাহারা দেয় | Watchdog/dead man's switch alert যা সবসময় firing, না এলে বাইরের সেবা সতর্ক করে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Alert fatigue কী আর কীভাবে কমাবে?
**উত্তর:** এত বেশি অপ্রয়োজনীয় alert যে মানুষ উপেক্ষা করতে শুরু করে। সমাধান: symptom/SLO ভিত্তিক alert, `for` duration, ঠিক threshold, grouping, severity অনুযায়ী routing, আর প্রতিটি alert actionable কিনা নিয়মিত পর্যালোচনা।

**প্রশ্ন:** Alert-এর pending আর firing অবস্থার পার্থক্য?
**উত্তর:** Pending মানে শর্ত সত্য হয়েছে কিন্তু `for` সময় পার হয়নি; firing মানে `for` সময় ধরে সত্য, Alertmanager-এ পাঠানো হয়েছে।

---

## ০৭. Grafana: Dashboard ও Visualization

Prometheus-এর data-কে মানুষের চোখে সুন্দর আর অর্থপূর্ণ graph-এ বদলায়। Loki, Elasticsearch, CloudWatch সহ অনেক উৎস একসাথে।

### Grafana: Data source, Dashboard, Panel, Variable

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Grafana** | (graph + -ana) → dashboard ও visualization প্ল্যাটফর্ম |
| **Data source** | যেখান থেকে data আসে (Prometheus, Loki) |
| **Panel** | dashboard-এর একটা graph বা সংখ্যা |
| **Variable** | template variable → dropdown দিয়ে dashboard বদলানো (`$namespace`) |

**সহজ ভাষায়:** Grafana নিজে data রাখে না; Prometheus, Loki, Elasticsearch-এর মতো data source থেকে query করে। Dashboard-এ অনেক panel থাকে: time series graph, stat (একটা বড় সংখ্যা), gauge, table। Variable দিয়ে একটা dashboard সব namespace বা সব সার্ভিসের জন্য কাজ করে। কমিউনিটির তৈরি হাজারো dashboard ID দিয়ে import করা যায়, যেমন `1860` = Node Exporter Full।

> **বাস্তব পরিস্থিতি:** On-call engineer-এর জন্য একটা "Service Overview" dashboard: উপরে dropdown-এ সার্ভিস বেছে নিলে নিচে তার request rate, error %, p95 latency, pod সংখ্যা, CPU/memory আর সাম্প্রতিক deploy-এর চিহ্ন। ৫ সেকেন্ডে বোঝা যায় সার্ভিস সুস্থ কিনা।

```text
Grafana UI ধাপ:
১. Connections > Data sources > Add > Prometheus
   URL: http://monitoring-kube-prometheus-prometheus.monitoring:9090
   (kube-prometheus-stack-এ আগে থেকেই যুক্ত)
২. Dashboards > New > Import > ID: 1860 (Node Exporter Full) > Load
৩. Dashboards > New dashboard > Add visualization
৪. Settings > Variables > New: Name: service, Type: Query,
   Query: label_values(http_requests_total, service)
৫. Panel query-তে variable ব্যবহার:
   sum(rate(http_requests_total{service="$service"}[5m]))
৬. Panel ধরন: Time series (trend), Stat (বর্তমান মান), Gauge (%), Table
৭. Thresholds: সবুজ < 1%, হলুদ < 5%, লাল >= 5%
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `label_values(metric, label)` | এই label-এর সব মান dropdown-এ |
| `$service` | dropdown-এ বেছে নেওয়া মান query-তে বসে |
| Import ID 1860 | grafana.com-এর কমিউনিটি dashboard সরাসরি আনা |
| Thresholds | মান অনুযায়ী রং, এক নজরে সমস্যা চোখে পড়ে |

| একটা ভালো সার্ভিস dashboard-এ যা থাকবে | কেন |
|---|---|
| Request rate (RED-এর R) | traffic কেমন |
| Error % (RED-এর E) | ভাঙছে কিনা |
| p50/p95/p99 latency (RED-এর D) | দ্রুত কিনা |
| Pod সংখ্যা ও restart | স্থিতিশীল কিনা |
| CPU/memory বনাম limit | saturation |
| Deploy annotation | কোনো পরিবর্তনের পর সমস্যা শুরু হলো কিনা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Prometheus থাকতে Grafana কেন লাগে?
**উত্তর:** Prometheus-এর UI সাধারণ query আর পরীক্ষার জন্য। Grafana সমৃদ্ধ dashboard, variable, অনেক data source (metric, log, trace একসাথে), শেয়ার, permission আর alerting দেয়।

> 💡 **প্রো টিপ:** Dashboard গুলোও কোডের মতো Git-এ রাখো (JSON বা Grafana provisioning/ConfigMap), যাতে হারিয়ে না যায়।

---

## ০৮. ELK Stack: Elasticsearch, Logstash, Kibana

Metric বলে "সমস্যা আছে", log বলে "ঠিক কী হয়েছে"। শত সার্ভার আর হাজার pod-এর log এক জায়গায় খোঁজার জন্য ELK।

### ELK-এর গঠন: log-এর যাত্রা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ELK** | **E**lasticsearch, **L**ogstash, **K**ibana → তিনটা টুলের প্রথম অক্ষর |
| **Elasticsearch** | elastic + search → log সংরক্ষণ ও দ্রুত খোঁজার database |
| **Logstash** | log + stash (জমা) → log সংগ্রহ, প্রক্রিয়া ও রূপান্তর |
| **Kibana** | log দেখা, খোঁজা ও dashboard-এর UI |
| **Beats / Filebeat** | হালকা log পাঠানোর agent |
| **EFK** | **E**lasticsearch, **F**luentd/**F**luent Bit, **K**ibana → Kubernetes-এ জনপ্রিয় রূপ |

**সহজ ভাষায়:** প্রতিটি সার্ভারে Filebeat (বা Kubernetes-এ Fluent Bit DaemonSet) log ফাইল পড়ে পাঠায়। Logstash সেই log-কে ভাঙে (grok দিয়ে IP, status, path আলাদা field), সমৃদ্ধ করে (geoip, সময় ঠিক করা), বাজে অংশ ফেলে দেয়। Elasticsearch সেগুলো index করে রাখে, যাতে কোটি লাইনের মধ্যে সেকেন্ডে খোঁজা যায়। Kibana দিয়ে খোঁজা, filter, graph আর dashboard।

> **বাস্তব পরিস্থিতি:** ২০টা সার্ভারে nginx চলছে। একজন গ্রাহক বলছে সকাল ১০:১৫-তে payment fail হয়েছে। আগে ২০টা সার্ভারে SSH করে grep করতে হতো। এখন Kibana-তে customer_id আর সময় দিয়ে খুঁজলে ৫ সেকেন্ডে সব সার্ভিসের সংশ্লিষ্ট log।

```text
[App / nginx log ফাইল]        [Kubernetes pod stdout]
          │                              │
       Filebeat                 Fluent Bit (DaemonSet)
          └──────────┬───────────────────┘
                     v
              [ Logstash ]       parse (grok), enrich, filter
                     v
            [ Elasticsearch ]    index, store, search
                     v
               [ Kibana ]        Discover, Visualize, Dashboard, Alert
```

| অংশ | কাজ | বিকল্প |
|---|---|---|
| Filebeat / Fluent Bit | উৎস থেকে log পাঠানো (হালকা) | Fluentd, Vector, Promtail |
| Logstash | ভারী প্রক্রিয়া: parse, enrich | Elasticsearch ingest pipeline, Fluentd |
| Elasticsearch | index ও search | OpenSearch, Loki (ভিন্ন পদ্ধতি) |
| Kibana | UI | OpenSearch Dashboards, Grafana |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** ELK stack-এর প্রতিটি অংশের কাজ কী?
**উত্তর:** Beats/Fluent Bit log পাঠায়, Logstash প্রক্রিয়া ও রূপান্তর করে, Elasticsearch index করে রাখে ও খোঁজে, Kibana দেখায় ও বিশ্লেষণ করে।

**প্রশ্ন:** Logstash লাগবেই কি?
**উত্তর:** না। সহজ ক্ষেত্রে Filebeat/Fluent Bit সরাসরি Elasticsearch-এ পাঠায় আর Elasticsearch-এর ingest pipeline parse করে; জটিল রূপান্তর বা অনেক উৎস/গন্তব্য হলে Logstash।

### Lab: Docker Compose দিয়ে ELK চালানো

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **single-node** | single-node discovery → একটা node-এর cluster (শুধু অনুশীলন) |
| **xpack.security** | X-Pack security → Elasticsearch-এর নিরাপত্তা ফিচার |

**সহজ ভাষায়:** অনুশীলনের জন্য Docker Compose-এ ছোট ELK। এখানে নিরাপত্তা বন্ধ রাখা হয়েছে সহজ করার জন্য; production-এ কখনো না (TLS আর password বাধ্যতামূলক)। Elasticsearch-এর অন্তত ২-৪ GB RAM লাগে।

> **বাস্তব পরিস্থিতি:** নিজের ল্যাপটপে nginx-এর log ELK-তে পাঠিয়ে Kibana-তে 5xx error খোঁজার অনুশীলন করতে চাও।

```yaml
# compose.yaml
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.15.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - ES_JAVA_OPTS=-Xms1g -Xmx1g
    ports: ["9200:9200"]
    volumes: [esdata:/usr/share/elasticsearch/data]

  logstash:
    image: docker.elastic.co/logstash/logstash:8.15.0
    volumes: [./logstash/pipeline:/usr/share/logstash/pipeline:ro]
    ports: ["5044:5044"]
    depends_on: [elasticsearch]

  kibana:
    image: docker.elastic.co/kibana/kibana:8.15.0
    environment: [ELASTICSEARCH_HOSTS=http://elasticsearch:9200]
    ports: ["5601:5601"]
    depends_on: [elasticsearch]

  filebeat:
    image: docker.elastic.co/beats/filebeat:8.15.0
    user: root
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - ./logs:/var/log/nginx:ro
    depends_on: [logstash]

volumes:
  esdata:
```

```bash
# চালাও
docker compose up -d
curl http://localhost:9200/_cluster/health?pretty
# Kibana: http://localhost:5601
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `discovery.type=single-node` | একা একটা node, cluster খোঁজা নয় |
| `xpack.security.enabled=false` | শুধু lab-এর জন্য password/TLS বন্ধ |
| `ES_JAVA_OPTS=-Xms1g -Xmx1g` | Java heap ১ GB (সাধারণ নিয়ম: RAM-এর অর্ধেক, ৩১ GB-এর নিচে) |
| `5044` | Logstash-এর Beats input port |
| `5601` | Kibana-এর UI port |
| `_cluster/health` | green/yellow/red: সব shard ঠিক আছে কিনা (single node-এ replica না থাকায় yellow স্বাভাবিক) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Elasticsearch cluster health green, yellow, red মানে কী?
**উত্তর:** Green: সব primary আর replica shard বরাদ্দ। Yellow: সব primary ঠিক, কিছু replica বরাদ্দ হয়নি (data আছে, redundancy কম)। Red: কিছু primary shard নেই, data অনুপলব্ধ।

### Filebeat ও Logstash pipeline: grok দিয়ে log ভাঙা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **grok** | (গভীরভাবে বোঝা) → প্যাটার্ন দিয়ে অসংগঠিত log-কে field-এ ভাঙা |
| **ECS** | **E**lastic **C**ommon **S**chema → field-এর সাধারণ নামকরণের মান |
| **index** | Elasticsearch-এ document-এর সংগ্রহ (টেবিলের মতো) |

**সহজ ভাষায়:** Nginx-এর একটা লাইন শুধু লেখা। grok সেটাকে client IP, method, path, status, bytes, user agent-এ ভাঙে, তবেই `status >= 500` দিয়ে খোঁজা বা graph বানানো যায়। `date` filter log-এর নিজের সময়কে আসল সময় হিসেবে বসায়। আরও ভালো উপায়: অ্যাপ নিজেই JSON log লিখুক, তাহলে grok-এর দরকারই নেই।

> **বাস্তব পরিস্থিতি:** Nginx-এর access log থেকে দেখতে চাই কোন দেশ থেকে কতটা traffic আর কোন path-এ সবচেয়ে বেশি 500।

```yaml
# filebeat.yml
filebeat.inputs:
  - type: filestream
    id: nginx-access
    paths: [/var/log/nginx/access.log]

output.logstash:
  hosts: ["logstash:5044"]
```

```ruby
# logstash/pipeline/nginx.conf
input {
  beats { port => 5044 }
}
filter {
  grok  { match => { "message" => "%{COMBINEDAPACHELOG}" } }
  date  { match => [ "timestamp", "dd/MMM/yyyy:HH:mm:ss Z" ] }
  geoip { source => "[source][address]" target => "[source][geo]" }
}
output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "nginx-%{+YYYY.MM.dd}"
  }
}
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `type: filestream, paths` | এই ফাইলগুলো পড়ো, কোথায় পর্যন্ত পড়া হয়েছে মনে রাখে (restart-এ আবার শুরু থেকে নয়) |
| `output.logstash` | Logstash-এ পাঠাও (সরাসরি Elasticsearch-ও হতে পারে) |
| `input { beats { port => 5044 } }` | Filebeat থেকে আসা log গ্রহণ |
| `%{COMBINEDAPACHELOG}` | তৈরি grok প্যাটার্ন: nginx/apache combined format-কে IP, verb, path, status ইত্যাদিতে ভাঙে (ECS নামে, যেমন `http.response.status_code`) |
| `date { match => ... }` | log লাইনের সময়কে `@timestamp` বানাও, নইলে Logstash-এ পৌঁছানোর সময় বসে |
| `geoip` | IP থেকে দেশ/শহর যোগ করো |
| `index => "nginx-%{+YYYY.MM.dd}"` | প্রতিদিন নতুন index, পুরনো মোছা সহজ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Grok কী আর কখন দরকার?
**উত্তর:** Regex ভিত্তিক তৈরি প্যাটার্ন দিয়ে অসংগঠিত লেখা log-কে নামযুক্ত field-এ ভাঙার Logstash filter। অ্যাপ নিজে structured (JSON) log দিলে দরকার নেই, সেটাই ভালো অভ্যাস।

> 💡 **প্রো টিপ:** অ্যাপের log সবসময় JSON-এ লেখো (time, level, message, trace_id, user_id)। পরে parse-এর ঝামেলা বাঁচে আর trace-এর সাথে মেলানো যায়।

### Kibana: খোঁজা, visualization ও alert

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **KQL** | **K**ibana **Q**uery **L**anguage → Kibana-এর সহজ খোঁজার ভাষা |
| **Discover** | log খোঁজা ও দেখার পাতা |
| **Data view** | (আগে index pattern) → কোন index গুলো দেখবে |

**সহজ ভাষায়:** প্রথমে Data view বানাও (যেমন `nginx-*`), তারপর Discover-এ সময় বেছে KQL দিয়ে খোঁজো। পছন্দের খোঁজা save করে visualization (bar, pie, map) আর dashboard বানাও। Alert rule দিয়ে নির্দিষ্ট log বেশি এলে সতর্কতা।

> **বাস্তব পরিস্থিতি:** সকাল ১০টা থেকে ১০:৩০-এর মধ্যে checkout path-এ সব 5xx error, কোন IP থেকে সবচেয়ে বেশি, আর কোন সার্ভার থেকে: তিনটা খোঁজায় উত্তর।

```text
# Kibana > Stack Management > Data Views > Create: nginx-*  (timestamp field: @timestamp)

# Discover-এ KQL উদাহরণ:
http.response.status_code >= 500
http.response.status_code >= 500 and url.original : *checkout*
source.address : "203.0.113.45"
log.level : "error" and service.name : "payment"
not http.request.method : "GET"
message : "timeout" or message : "connection refused"
kubernetes.namespace : "prod" and kubernetes.pod.name : api-*

# Visualize: Lens > bar chart > X: @timestamp, Y: count, breakdown: http.response.status_code
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `field >= 500` | সংখ্যার তুলনা |
| `field : *checkout*` | wildcard মিল |
| `and` / `or` / `not` | শর্ত জোড়া |
| `"..."` | হুবহু শব্দগুচ্ছ |
| `kubernetes.*` field | Fluent Bit/Filebeat Kubernetes metadata যোগ করলে namespace, pod, container দিয়ে খোঁজা যায় |

### Index lifecycle, retention ও Kubernetes-এ EFK / Loki

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ILM** | **I**ndex **L**ifecycle **M**anagement → পুরনো index স্বয়ংক্রিয় সরানো/মোছা |
| **shard** | index-এর টুকরো, বিভিন্ন node-এ ছড়ানো |
| **Loki** | Grafana Loki → label ভিত্তিক সস্তা log সিস্টেম |
| **Fluent Bit** | খুব হালকা log সংগ্রাহক (C-তে লেখা) |

**সহজ ভাষায়:** Log অসীম বাড়ে, আর Elasticsearch-এ disk ও RAM দামি। ILM দিয়ে নিয়ম: নতুন log দ্রুত disk-এ (hot), ৭ দিন পর সস্তায় (warm), ৩০ দিন পর মুছে দাও। Kubernetes-এ সাধারণত Fluent Bit DaemonSet প্রতিটি node-এর `/var/log/containers` পড়ে pod-এর namespace, label যোগ করে পাঠায় (EFK)। Grafana Loki পুরো লেখা index না করে শুধু label index করে, তাই অনেক সস্তা; খোঁজা একটু সীমিত, কিন্তু Grafana-তে metric আর log পাশাপাশি দেখা যায়।

> **বাস্তব পরিস্থিতি:** এক বছরের সব log রাখার কারণে Elasticsearch cluster-এর disk ভরে red হয়ে গেল, নতুন log আসা বন্ধ। ILM নিয়ম (১৪ দিন hot, ৩০ দিনে delete, audit log আলাদা index-এ ১ বছর S3 snapshot) চালুর পর খরচ ৬০% কমল।

```json
PUT _ilm/policy/logs-30d
{
  "policy": {
    "phases": {
      "hot":    { "actions": { "rollover": { "max_age": "1d", "max_primary_shard_size": "50gb" } } },
      "warm":   { "min_age": "7d",  "actions": { "shrink": { "number_of_shards": 1 } } },
      "delete": { "min_age": "30d", "actions": { "delete": {} } }
    }
  }
}
```

```bash
# Kubernetes-এ Fluent Bit
helm repo add fluent https://fluent.github.io/helm-charts
helm install fluent-bit fluent/fluent-bit -n logging --create-namespace

# Loki stack (বিকল্প)
helm repo add grafana https://grafana.github.io/helm-charts
helm install loki grafana/loki-stack -n logging
# Grafana Explore-এ LogQL: {namespace="prod", app="api"} |= "error"
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| hot / warm / delete | জীবনচক্রের ধাপ: সক্রিয় লেখা, পুরনো কম ব্যবহৃত, মুছে ফেলা |
| `rollover max_age 1d` | প্রতিদিন বা ৫০ GB হলে নতুন index শুরু |
| `min_age: 30d` + delete | ৩০ দিন পর মুছে দাও |
| Fluent Bit DaemonSet | প্রতিটি node-এ একটা, সব container-এর log সংগ্রহ |
| `{namespace="prod"} \|= "error"` | LogQL: label দিয়ে বাছাই, তারপর লেখায় "error" আছে এমন লাইন |

| বিষয় | ELK / EFK | Loki |
|---|---|---|
| Index | পুরো লেখা (full-text) | শুধু label |
| খোঁজা | শক্তিশালী, দ্রুত full-text ও aggregation | label দিয়ে, তারপর grep-এর মতো |
| খরচ | বেশি (RAM, disk) | অনেক কম (object storage) |
| UI | Kibana | Grafana (metric-এর পাশে) |
| কখন | জটিল বিশ্লেষণ, security/audit, বড় টিম | Kubernetes log, Prometheus/Grafana ব্যবহারকারী, খরচ সচেতন |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** ELK আর Loki-এর পার্থক্য?
**উত্তর:** ELK পুরো log লেখা index করে, ফলে শক্তিশালী full-text খোঁজা কিন্তু ব্যয়বহুল। Loki শুধু label index করে আর লেখা compressed রাখে, ফলে সস্তা আর সহজ, Grafana-তে metric-এর সাথে ভালো মেলে, কিন্তু জটিল খোঁজা সীমিত।

**প্রশ্ন:** Log retention কীভাবে নির্ধারণ করবে?
**উত্তর:** আইনি/compliance প্রয়োজন (audit log দীর্ঘ), debug-এর প্রয়োজন (সাধারণত ৭-৩০ দিন), খরচ; ILM দিয়ে স্বয়ংক্রিয়, দীর্ঘমেয়াদি গুলো সস্তা storage-এ (S3 snapshot)।

---

## ০৯. Monitoring: Scenario ভিত্তিক অনুশীলন

বাস্তব on-call-এর মতো সমস্যা: alert এসেছে, এখন metric আর log দিয়ে কারণ খুঁজে বের করো।

### অনুশীলন ১০: "সাইট ধীর" অভিযোগ, latency কোথায়?

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **p95** | 95th **P**ercentile → ৯৫% request-এর চেয়ে কম সময় |

**কাজ (Task):** Grafana আর PromQL দিয়ে বের করো কোন সার্ভিস ধীর, কখন থেকে, আর কোনো deploy-এর সাথে মেলে কিনা।

> **পরিস্থিতি:** Customer support বলছে দুপুর ২টা থেকে সাইট ধীর। কোনো alert আসেনি।

```promql
# ১. কোন সার্ভিসের p95 বেড়েছে
histogram_quantile(0.95, sum by (le, service) (rate(http_request_duration_seconds_bucket[5m])))

# ২. traffic কি হঠাৎ বেড়েছে?
sum by (service) (rate(http_requests_total[5m]))

# ৩. ওই সার্ভিসের pod কি CPU throttle হচ্ছে?
sum by (pod) (rate(container_cpu_cfs_throttled_periods_total{namespace="prod",container="api"}[5m]))
  / sum by (pod) (rate(container_cpu_cfs_periods_total{namespace="prod",container="api"}[5m]))

# ৪. সমস্যা শুরুর সময় deploy হয়েছিল?  (kubectl rollout history deployment/api -n prod)
changes(kube_deployment_status_observed_generation{deployment="api"}[1h])

# ৫. Kibana-তে ওই সময়ের ধীর query log:
# service.name : "api" and event.duration > 2000000000
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `by (le, service)` | প্রতিটি সার্ভিসের আলাদা p95 |
| `cfs_throttled / cfs_periods` | কত শতাংশ সময় CPU limit-এর কারণে আটকে ছিল; বেশি হলে limit বাড়াও |
| `changes(...observed_generation...)` | এই সময়ে Deployment-এর spec কতবার বদলেছে (deploy হয়েছে কিনা) |
| `event.duration` (ন্যানোসেকেন্ড) | ECS-এ সময়কাল; ২ সেকেন্ডের বেশি লাগা request |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** "সাইট ধীর" অভিযোগে কীভাবে এগোবে?
**উত্তর:** Scope ঠিক করো (কোন পেজ/সার্ভিস, কবে থেকে), RED metric দেখো (traffic বেড়েছে কিনা, error, latency কোন সার্ভিসে), resource saturation (CPU throttle, memory, DB connection), সাম্প্রতিক পরিবর্তন (deploy, config), তারপর log/trace দিয়ে নির্দিষ্ট ধীর request।

### অনুশীলন ১১: OOMKilled হওয়ার আগেই সতর্কতা

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **working set** | working set memory → container-এর সক্রিয় memory, OOM-এর হিসাবে এটাই ধরা হয় |

**কাজ (Task):** এমন একটা alert rule লেখো যেটা কোনো container memory limit-এর ৯০% পেরোলে ১০ মিনিট পর warning দেয়, আর একটা যেটা OOMKilled হলেই জানায়।

> **পরিস্থিতি:** Report সার্ভিস প্রতি রাতে OOMKilled হয়, কেউ সকালে জানতে পারে।

```yaml
- alert: ContainerMemoryNearLimit
  expr: |
    sum by (namespace, pod, container) (container_memory_working_set_bytes{container!=""})
      / sum by (namespace, pod, container) (kube_pod_container_resource_limits{resource="memory"}) > 0.9
  for: 10m
  labels: { severity: warning }
  annotations:
    summary: "{{ $labels.namespace }}/{{ $labels.pod }} ({{ $labels.container }}) memory > 90% of limit"

- alert: ContainerOOMKilled
  expr: |
    increase(kube_pod_container_status_restarts_total[10m]) > 0
      and on (namespace, pod, container)
    kube_pod_container_status_last_terminated_reason{reason="OOMKilled"} == 1
  labels: { severity: warning }
  annotations:
    summary: "{{ $labels.pod }} was OOMKilled"
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `container_memory_working_set_bytes` | cAdvisor থেকে আসা container-এর আসল memory |
| `kube_pod_container_resource_limits` | kube-state-metrics থেকে limit-এর মান |
| `> 0.9` ও `for: 10m` | ১০ মিনিট ধরে ৯০%-এর বেশি |
| `and on (namespace, pod, container)` | দুটো শর্ত একই container-এর জন্য সত্য হলে |
| `last_terminated_reason{reason="OOMKilled"}` | শেষবার থামার কারণ OOM |

### অনুশীলন ১২: Kibana দিয়ে হঠাৎ 5xx-এর কারণ

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **5xx** | HTTP 500-599 → সার্ভারের দিকের error |

**কাজ (Task):** Grafana-তে 5xx বেড়েছে দেখা গেল। Kibana দিয়ে বের করো কোন endpoint, কোন pod, আর error message কী।

> **পরিস্থিতি:** বিকেল ৪:১০-এ checkout-এর error rate ০.২% থেকে ৬%। Alert এসেছে।

```text
# Kibana Discover, সময়: 16:05 - 16:30

# ১. কোন path
http.response.status_code >= 500
# বাম পাশে url.original field-এ ক্লিক -> top values

# ২. অ্যাপের error log, ওই সার্ভিস
service.name : "checkout" and log.level : "error"
# message-এর top values: "redis: connection pool exhausted"

# ৩. কোন pod গুলো
service.name : "checkout" and message : "connection pool exhausted"
# kubernetes.pod.name-এর top values -> সব pod, তাই Redis-এর দিকে সমস্যা

# ৪. Grafana-তে মিলিয়ে দেখো
redis_connected_clients
rate(redis_rejected_connections_total[5m])
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| field-এ ক্লিক → top values | Kibana Discover-এর দ্রুত বিশ্লেষণ: কোন মান সবচেয়ে বেশি |
| সব pod-এ একই error | সমস্যা একটা pod-এ নয়, সাধারণ dependency (Redis)-তে |
| metric + log মিলিয়ে | log কারণ বলল, metric নিশ্চিত করল আর পরিমাণ দেখাল |

### অনুশীলন ১৩: Disk ভরার আগে ভবিষ্যদ্বাণী

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **predict_linear** | predict linear → সরল রেখায় ভবিষ্যৎ মান অনুমান |

**কাজ (Task):** Node-এর disk ৪ ঘণ্টার মধ্যে ভরে যাবে এমন হলে critical alert, আর ৮৫%-এ warning লেখো।

> **পরিস্থিতি:** আগের মাসে রাত ৩টায় log-এর কারণে একটা node-এর disk ভরে সব pod evict হয়েছিল।

```yaml
- alert: NodeDiskWillFillIn4h
  expr: |
    predict_linear(node_filesystem_avail_bytes{fstype!~"tmpfs|overlay",mountpoint="/"}[6h], 4*3600) < 0
      and node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"} < 0.4
  for: 30m
  labels: { severity: critical }

- alert: NodeDiskUsageHigh
  expr: |
    100 * (1 - node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) > 85
  for: 15m
  labels: { severity: warning }
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `predict_linear(...[6h], 4*3600)` | গত ৬ ঘণ্টার প্রবণতা থেকে ৪ ঘণ্টা পরের মান |
| `fstype!~"tmpfs\|overlay"` | অস্থায়ী filesystem বাদ |
| `and ... < 0.4` | শুধু তখন যখন খালি জায়গা ৪০%-এর কম, নইলে ছোট ওঠানামায় ভুল alert |
| `for: 30m` | প্রবণতা টানা ৩০ মিনিট থাকলে |

### অনুশীলন ১৪: বাইরে থেকে website পর্যবেক্ষণ

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **blackbox_exporter** | বাইরে থেকে HTTP/TCP/ICMP পরীক্ষা |
| **Synthetic monitoring** | কৃত্রিম ব্যবহারকারীর মতো নিয়মিত পরীক্ষা |

**কাজ (Task):** blackbox_exporter দিয়ে `https://shop.example.com` প্রতি মিনিটে পরীক্ষা করো, সাইট down বা certificate ১৪ দিনের মধ্যে শেষ হলে alert দাও।

> **পরিস্থিতি:** সব ভেতরের metric সবুজ, কিন্তু DNS-এর ভুলে বাইরে থেকে সাইট খুলছিল না, কেউ টের পায়নি।

```bash
helm install blackbox prometheus-community/prometheus-blackbox-exporter -n monitoring
```

```yaml
# Probe (Prometheus Operator CRD)
apiVersion: monitoring.coreos.com/v1
kind: Probe
metadata: { name: shop-site, namespace: monitoring, labels: { release: monitoring } }
spec:
  interval: 60s
  module: http_2xx
  prober:
    url: blackbox-prometheus-blackbox-exporter.monitoring:9115
  targets:
    staticConfig:
      static: [https://shop.example.com]
```

```promql
# Alerts
probe_success == 0                                           # সাইট down
(probe_ssl_earliest_cert_expiry - time()) / 86400 < 14       # cert ১৪ দিনের কম
```

**লাইন ধরে ব্যাখ্যা**

| কোড / অংশ | মানে |
|---|---|
| `module: http_2xx` | HTTP request করে 2xx উত্তর আশা করো |
| `prober.url` | blackbox exporter-এর ঠিকানা |
| `probe_success` | ১ = সফল, ০ = ব্যর্থ |
| `probe_ssl_earliest_cert_expiry - time()` | certificate শেষ হতে কত সেকেন্ড বাকি; ÷ 86400 = দিন |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Whitebox আর blackbox monitoring-এর পার্থক্য?
**উত্তর:** Whitebox ভেতরের metric (অ্যাপ নিজে কী বলছে)। Blackbox বাইরে থেকে ব্যবহারকারীর মতো পরীক্ষা (সাইট খুলছে কিনা)। দুটোই দরকার, কারণ ভেতরে সব ঠিক থাকলেও DNS, certificate বা load balancer-এর জন্য বাইরে থেকে ভাঙতে পারে।

---

## ১০. বাস্তব Incident Playbook

Kubernetes আর monitoring-এর সবচেয়ে কমন সমস্যায় ধাপে ধাপে কী করবে। ইন্টারভিউতে "X কাজ করছে না, কী করবে?" প্রশ্নেও এভাবে উত্তর দিও। বাস্তব কাজেও checklist হিসেবে ব্যবহার করো।

### 🚨 Pod CrashLoopBackOff

1. `kubectl get pods -n <ns>` → RESTARTS, STATUS
2. `kubectl describe pod <pod>` → Last State (Exit Code: 1 = অ্যাপ, 137 = OOM/kill), Events
3. `kubectl logs <pod> --previous` → crash-এর আগের log
4. env/ConfigMap/Secret ঠিক আছে? `kubectl get pod <pod> -o yaml`
5. liveness probe বেশি কড়া? startupProbe লাগবে?
6. সাম্প্রতিক deploy হলে: `kubectl rollout undo deployment/<name>`

### 🚨 Service-এ পৌঁছানো যাচ্ছে না

1. `kubectl get endpoints <svc>` → খালি? তাহলে selector বা readiness
2. `kubectl get pods --show-labels` → label মেলে?
3. targetPort == containerPort?
4. ভেতর থেকে: `kubectl run t --rm -it --image=busybox:1.36 -- wget -qO- http://<svc>:<port>`
5. DNS: `nslookup <svc>.<ns>.svc.cluster.local`
6. NetworkPolicy আটকাচ্ছে? `kubectl get networkpolicy -n <ns>`
7. Ingress হলে: ingressClassName, host, path, controller-এর log

### 🚨 Node NotReady

1. `kubectl get nodes ; kubectl describe node <node>` → Conditions (MemoryPressure, DiskPressure, Ready)
2. Node-এ SSH: `systemctl status kubelet ; journalctl -u kubelet -n 100`
3. Disk/memory: `df -h ; free -h`
4. Container runtime: `systemctl status containerd`
5. Network/certificate সমস্যা kubelet log-এ
6. ঠিক না হলে: cordon + drain, node বদলাও (cloud-এ autoscaling group)

### 🚨 Prometheus-এ target DOWN বা metric নেই

1. Prometheus UI > Status > Targets → error message দেখো
2. ServiceMonitor-এ release label আর selector ঠিক?
3. Service-এ port-এর নাম মেলে?
4. pod থেকে: `kubectl port-forward pod/<pod> 8080 ; curl localhost:8080/metrics`
5. NetworkPolicy Prometheus-এর traffic আটকাচ্ছে?
6. Prometheus-এর নিজের log: `kubectl logs -n monitoring prometheus-...-0`

### 🚨 Kibana-তে log আসছে না

1. Elasticsearch সুস্থ? `curl es:9200/_cluster/health`; disk watermark পেরিয়েছে কিনা (read-only index)
2. Index তৈরি হচ্ছে? `curl es:9200/_cat/indices?v`
3. Logstash/Fluent Bit-এর log-এ error (grok fail, connection refused)
4. Filebeat/Fluent Bit সঠিক path পড়ছে? permission?
5. Kibana data view-এর index pattern আর সময় সীমা ঠিক?
6. সময় অঞ্চল/`@timestamp` ভুল হলে log ভবিষ্যৎ বা অতীতে চলে যায়

### 🚨 Alert আসছে কিন্তু কেউ পাচ্ছে না / অতিরিক্ত alert

1. Prometheus UI > Alerts: firing কিনা
2. Alertmanager UI: alert পৌঁছেছে? silence আছে? route কোন receiver-এ?
3. receiver config (Slack webhook, PagerDuty key) আর Alertmanager log
4. অতিরিক্ত হলে: `for` duration, threshold, group_by, inhibit rule পর্যালোচনা
5. প্রতিটি alert actionable কিনা আর runbook আছে কিনা যাচাই

---

## ১১. ইন্টারভিউ প্রশ্ন ব্যাংক

প্রতিটি অধ্যায়ের কার্ডে থাকা প্রশ্নের বাইরে আরও কিছু জনপ্রিয় প্রশ্ন, লেভেল অনুযায়ী সাজানো। উত্তর মুখস্থ না করে নিজের ভাষায় বলার অনুশীলন করো।

### 🟢 বেসিক লেভেল

**প্রশ্ন ১. Pod, Deployment আর Service-এর সম্পর্ক কী?**
Pod অ্যাপ চালায় (এক বা একাধিক container)। Deployment (ReplicaSet-এর মাধ্যমে) নির্দিষ্ট সংখ্যক pod চালু রাখে, আপডেট ও rollback করে। Service label selector দিয়ে সেই pod গুলোর সামনে একটা স্থির নাম/IP দেয় আর traffic ভাগ করে।

**প্রশ্ন ২. Kubernetes-এর মূল component গুলো বলো।**
Control plane: kube-apiserver, etcd, kube-scheduler, kube-controller-manager (cloud-controller-manager)। Node: kubelet, kube-proxy, container runtime (containerd)।

**প্রশ্ন ৩. Namespace কী আর কেন?**
Cluster-এর ভেতরে logical ভাগ, যাতে টিম/পরিবেশ আলাদা থাকে, একই নাম ব্যবহার করা যায়, আর quota, RBAC, NetworkPolicy প্রতি namespace-এ দেওয়া যায়।

**প্রশ্ন ৪. Prometheus আর Grafana-এর পার্থক্য?**
Prometheus metric সংগ্রহ, সংরক্ষণ, query আর alert করে। Grafana সেই (আর অন্য উৎসের) data দিয়ে dashboard আর visualization বানায়।

**প্রশ্ন ৫. ELK stack কী?**
Elasticsearch (log সংরক্ষণ ও খোঁজা), Logstash (log সংগ্রহ ও রূপান্তর), Kibana (দেখা ও বিশ্লেষণ); সাথে Beats/Filebeat log পাঠায়।

### 🟡 ইন্টারমিডিয়েট লেভেল

**প্রশ্ন ৬. Liveness, readiness আর startup probe-এর পার্থক্য?**
Liveness fail = container restart। Readiness fail = Service থেকে বাদ, restart নয়। Startup সফল না হওয়া পর্যন্ত বাকি দুটো বন্ধ রাখে, ধীর চালু হওয়া অ্যাপের জন্য।

**প্রশ্ন ৭. Kubernetes-এ rolling update কীভাবে কাজ করে আর rollback কীভাবে?**
Deployment নতুন ReplicaSet বানিয়ে maxSurge/maxUnavailable মেনে ধীরে ধীরে নতুন pod বাড়ায় আর পুরনো কমায়, readiness দেখে। Rollback: `kubectl rollout undo`, যা আগের ReplicaSet আবার বাড়ায়।

**প্রশ্ন ৮. ClusterIP, NodePort, LoadBalancer আর Ingress-এর পার্থক্য?**
ClusterIP শুধু ভেতরে; NodePort প্রতিটি node-এর port দিয়ে; LoadBalancer cloud LB দিয়ে public; Ingress Layer 7 host/path routing অনেক সার্ভিসের জন্য একটা প্রবেশপথে, controller লাগে।

**প্রশ্ন ৯. Request আর limit না দিলে কী সমস্যা?**
Scheduler ঠিকমতো জায়গা হিসাব করতে পারে না, একটা pod পুরো node খেয়ে ফেলতে পারে, pod BestEffort হয়ে চাপে আগে evict হয়, আর HPA CPU % হিসাব করতে পারে না।

**প্রশ্ন ১০. Counter, gauge আর histogram-এর পার্থক্য PromQL-এ কীভাবে ব্যবহার করবে?**
Counter শুধু বাড়ে, `rate()`/`increase()` দিয়ে ব্যবহার। Gauge সরাসরি মান। Histogram-এর bucket থেকে `histogram_quantile` দিয়ে percentile latency।

**প্রশ্ন ১১. ConfigMap বদলালে pod-এ কী হয়?**
Volume হিসেবে mount করা থাকলে ফাইল কিছুক্ষণ পর নিজে আপডেট হয় (অ্যাপকে আবার পড়তে হবে)। Environment variable হিসেবে থাকলে বদলায় না; `rollout restart` লাগে।

### 🔴 অ্যাডভান্সড লেভেল

**প্রশ্ন ১২. `kubectl apply` দিলে ভেতরে কী কী ঘটে, ধাপে ধাপে বলো।**
kubectl → API server (authentication, authorization, admission controller) → etcd-এ লেখা → controller (Deployment → ReplicaSet → Pod object) → scheduler node বাছে (filter + score) → kubelet container runtime দিয়ে চালায়, CNI network দেয় → status API server-এ।

**প্রশ্ন ১৩. Prometheus-কে কীভাবে scale ও দীর্ঘমেয়াদি data-র জন্য বাড়াবে?**
Prometheus একক node-এর জন্য তৈরি; HA-এর জন্য দুটো একই config-এ, দীর্ঘমেয়াদি আর বহু-cluster-এর জন্য Thanos, Cortex/Mimir বা VictoriaMetrics (remote_write, object storage), আর high cardinality নিয়ন্ত্রণ।

**প্রশ্ন ১৪. SLO ভিত্তিক alerting (burn rate) কী?**
Error budget কত দ্রুত খরচ হচ্ছে তা দেখে alert: দ্রুত burn (যেমন ১ ঘণ্টায় ২% budget) হলে page, ধীর burn হলে ticket। এতে ছোট spike-এ ঘুম ভাঙে না কিন্তু আসল সমস্যা দ্রুত ধরা পড়ে।

**প্রশ্ন ১৫. Elasticsearch cluster-এর disk ভরে গেলে কী হয় আর কীভাবে ঠেকাবে?**
Flood-stage watermark (default ৯৫%) পেরোলে index read-only হয়ে যায়, নতুন log বন্ধ। ঠেকাতে ILM retention, rollover, সঠিক shard আকার, disk monitoring, আর পুরনো data snapshot করে মুছে ফেলা।

**প্রশ্ন ১৬. Metric, log আর trace কীভাবে একসাথে যুক্ত করবে?**
সব জায়গায় একই label/field (service, namespace, pod) আর log-এ trace_id রাখো; OpenTelemetry দিয়ে instrument করো; Grafana-তে exemplar ও data link দিয়ে metric থেকে trace, trace থেকে log-এ লাফ দেওয়া যায়।

**প্রশ্ন ১৭. Kubernetes-এ একটা সার্ভিস মাঝে মাঝে ধীর হয়ে যায়। কোথায় কোথায় খুঁজবে?**
CPU throttling (limit কম), GC pause, node-এর noisy neighbor, DNS latency (ndots, CoreDNS চাপ), connection pool, downstream সার্ভিস (trace দিয়ে), HPA-এর দেরিতে scale, আর নির্দিষ্ট pod/node-এ সীমিত কিনা (metric by pod/node)।

---

<p align="center"><sub>Kubernetes & Monitoring · <a href="../README.md">← সব গাইড</a></sub></p>
