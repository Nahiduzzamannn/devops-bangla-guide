# Networking: Basic to Advanced

```bash
$ traceroute career.network-engineer
```

**বেসিক থেকে অ্যাডভান্সড — Network Engineer ইন্টারভিউ প্রস্তুতিসহ**

OSI · IP · Subnetting · Switching · Routing · Security · Wireless · Cloud · Linux, Cisco ও Windows troubleshooting কমান্ড

| | |
|---|---|
| অধ্যায় | ১৩টি + প্রশ্ন ব্যাংক |
| টপিক কার্ড | ৫৯টি |
| ইন্টারভিউ প্রশ্ন | ১৫২টিরও বেশি প্রশ্ন ও উত্তর |
| লেভেল | 🟢 বেসিক · 🟡 ইন্টারমিডিয়েট · 🔴 অ্যাডভান্সড |

---

## এই গাইড কীভাবে ব্যবহার করবে

অধ্যায়গুলো সাজানো আছে একদম শুরু থেকে: নেটওয়ার্ক কী, তারপর OSI, IP, subnetting, switching, routing, security, wireless, আর শেষে cloud ও data center।

প্রতিটি টপিক কার্ডে আছে **পূর্ণরূপ** (প্রতিটি শব্দসংক্ষেপ কোন ইংরেজি শব্দ থেকে এসেছে, বড় হাতের অক্ষরগুলো হাইলাইট করা), **সহজ ভাষায়** ব্যাখ্যা, **বাস্তব পরিস্থিতি**, টেবিল বা কমান্ড উদাহরণ, **ইন্টারভিউ প্রশ্ন ও উত্তর**, আর **প্রো টিপ**। বেসিক কার্ডগুলো ভালো করে বোঝো, ইন্টারমিডিয়েটগুলো lab-এ (Cisco Packet Tracer, GNS3, EVE-NG বা Linux VM) হাতে-কলমে করো, আর অ্যাডভান্সডগুলো বুঝিয়ে বলতে পারার মতো করে শেখো। শেষে incident playbook আর একটা প্রশ্ন ব্যাংক আছে।

> **নোট:** কমান্ড আর কনফিগারেশন ইংরেজিতে রাখা হয়েছে, কারণ যন্ত্রে ঠিক এভাবেই লিখতে হয়। কোডের ভেতরের বাংলা অংশ শুধু ব্যাখ্যার জন্য।

---

## সূচিপত্র

- [০০. শুরুর আগে: নেটওয়ার্কের একদম গোড়ার কথা](#০০-শুরুর-আগে-নেটওয়ার্কের-একদম-গোড়ার-কথা)
- [০১. OSI ও TCP/IP মডেল](#০১-osi-ও-tcpip-মডেল)
- [০২. IPv4 Addressing](#০২-ipv4-addressing)
- [০৩. Subnetting (ধাপে ধাপে)](#০৩-subnetting-ধাপে-ধাপে)
- [০৪. IPv6](#০৪-ipv6)
- [০৫. Layer 2: Ethernet, MAC, Switching, VLAN, STP](#০৫-layer-2-ethernet-mac-switching-vlan-stp)
- [০৬. Transport Layer: TCP, UDP ও Port](#০৬-transport-layer-tcp-udp-ও-port)
- [০৭. জরুরি নেটওয়ার্ক সার্ভিস: DNS, DHCP, NAT, NTP, ICMP, HTTP/TLS](#০৭-জরুরি-নেটওয়ার্ক-সার্ভিস-dns-dhcp-nat-ntp-icmp-httptls)
- [০৮. Routing: Static, Dynamic, OSPF, EIGRP, BGP](#০৮-routing-static-dynamic-ospf-eigrp-bgp)
- [০৯. নেটওয়ার্ক নিরাপত্তা](#০৯-নেটওয়ার্ক-নিরাপত্তা)
- [১০. Wireless (WiFi)](#১০-wireless-wifi)
- [১১. Advanced: QoS, MPLS, SD-WAN, Load Balancing, Cloud ও Data Center](#১১-advanced-qos-mpls-sd-wan-load-balancing-cloud-ও-data-center)
- [১২. Troubleshooting পদ্ধতি ও টুল](#১২-troubleshooting-পদ্ধতি-ও-টুল)
- [১৩. বাস্তব Incident Playbook](#১৩-বাস্তব-incident-playbook)
- [১৪. ইন্টারভিউ প্রশ্ন ব্যাংক](#১৪-ইন্টারভিউ-প্রশ্ন-ব্যাংক)

---

## ০০. শুরুর আগে: নেটওয়ার্কের একদম গোড়ার কথা

একেবারে নতুন হলে এখান থেকে শুরু করো; এখানের শব্দগুলো পুরো বই জুড়ে বারবার আসবে।

### নেটওয়ার্ক কী

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Network** | যুক্ত যন্ত্রের জাল |
| **Node / Host** | নেটওয়ার্কে যুক্ত যেকোনো যন্ত্র |

**সহজ ভাষায়:** দুই বা তার বেশি যন্ত্র (কম্পিউটার, ফোন, সার্ভার, প্রিন্টার) যখন তার (cable) বা বেতার (wireless) দিয়ে যুক্ত হয়ে তথ্য আদান-প্রদান করে, সেটাই নেটওয়ার্ক। ইন্টারনেট হলো পৃথিবীর লক্ষ লক্ষ নেটওয়ার্কের একটা বিশাল নেটওয়ার্ক।

> **বাস্তব পরিস্থিতি:** অফিসে ২০টা কম্পিউটার একটা প্রিন্টার শেয়ার করে আর সবাই একই ফাইল সার্ভারে ফাইল রাখে; এটা ছোট একটা নেটওয়ার্ক। প্রতিটি কম্পিউটার একটা switch-এ যুক্ত, switch একটা router-এ, router ইন্টারনেটে।

| শব্দ | মানে |
|---|---|
| Host | নেটওয়ার্কে যুক্ত যে যন্ত্রের IP আছে (PC, server, phone) |
| Client | যে সেবা চায় (তোমার browser) |
| Server | যে সেবা দেয় (web server, DNS server) |
| Protocol | যোগাযোগের নিয়ম; দুজন একই ভাষায় কথা না বললে বুঝবে না, যন্ত্রের ক্ষেত্রেও তাই (HTTP, TCP, IP) |
| Packet | তথ্যকে ছোট ছোট টুকরোয় ভাগ করে পাঠানো হয়, প্রতিটি টুকরো একটা packet |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Network আর Internet-এর পার্থক্য কী?
**উত্তর:** Network হলো যেকোনো যুক্ত যন্ত্রের দল (যেমন অফিসের LAN)। Internet হলো সারা বিশ্বের অসংখ্য নেটওয়ার্ক একসাথে যুক্ত হয়ে তৈরি সবচেয়ে বড় নেটওয়ার্ক।

> 💡 **প্রো টিপ:** Interview-এ শুরুতেই এমন সহজ প্রশ্ন আসে; সংজ্ঞার সাথে একটা ছোট বাস্তব উদাহরণ দিলে ভালো ছাপ পড়ে।

### Bit, Byte, Bandwidth, Throughput, Latency

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **bps** | **b**its **p**er **s**econd → প্রতি সেকেন্ডে কত bit |
| **Mbps** | **M**ega **b**its **p**er **s**econd → প্রতি সেকেন্ডে ১০ লাখ bit |
| **MB/s** | **M**ega **B**ytes per second → প্রতি সেকেন্ডে মেগাবাইট |
| **RTT** | **R**ound **T**rip **T**ime → যাওয়া-আসার মোট সময় |

**সহজ ভাষায়:** ১ byte = ৮ bit। নেটওয়ার্কের গতি মাপা হয় bit-এ (Mbps), ফাইলের আকার মাপা হয় byte-এ (MB)। তাই 100 Mbps লাইনে সর্বোচ্চ ডাউনলোড প্রায় 12.5 MB/s। Bandwidth হলো রাস্তার প্রস্থ (সর্বোচ্চ ক্ষমতা), throughput হলো আসলে কত গাড়ি যাচ্ছে (বাস্তব গতি), latency হলো এক মাথা থেকে আরেক মাথায় যেতে কত সময় লাগে।

> **বাস্তব পরিস্থিতি:** গ্রাহক অভিযোগ করছে: "100 Mbps কিনেছি কিন্তু ডাউনলোড 12 MB/s-এর বেশি হয় না।" তুমি বুঝিয়ে দিলে এটাই স্বাভাবিক, কারণ 100 ÷ 8 = 12.5। আরেক গ্রাহকের গতি ঠিক আছে কিন্তু online game-এ lag হয়: সমস্যা bandwidth নয়, latency আর jitter।

| শব্দ | সহজ উপমা | মাপার একক |
|---|---|---|
| Bandwidth | রাস্তা কয় লেনের | Mbps, Gbps |
| Throughput | আসলে কত গাড়ি যাচ্ছে | Mbps |
| Latency | এক শহর থেকে আরেক শহরে যেতে সময় | ms (মিলিসেকেন্ড) |
| Jitter | প্রতিবার যেতে সময়ের ওঠানামা | ms |
| Packet loss | পথে হারিয়ে যাওয়া গাড়ি | % |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Bandwidth আর latency-এর পার্থক্য কী?
**উত্তর:** Bandwidth হলো প্রতি সেকেন্ডে সর্বোচ্চ কত data পাঠানো যায়; latency হলো একটা packet গন্তব্যে পৌঁছাতে কত সময় লাগে। বেশি bandwidth মানেই কম latency নয়; satellite link-এ bandwidth বেশি কিন্তু latency অনেক।

> 💡 **প্রো টিপ:** Voice/video call আর gaming-এর জন্য latency ও jitter বেশি গুরুত্বপূর্ণ, বড় ফাইল ডাউনলোডের জন্য bandwidth।

### নেটওয়ার্কের ধরন: PAN, LAN, MAN, WAN

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **PAN** | **P**ersonal **A**rea **N**etwork → ব্যক্তিগত নেটওয়ার্ক |
| **LAN** | **L**ocal **A**rea **N**etwork → স্থানীয় নেটওয়ার্ক |
| **MAN** | **M**etropolitan **A**rea **N**etwork → শহরব্যাপী নেটওয়ার্ক |
| **WAN** | **W**ide **A**rea **N**etwork → বিস্তৃত এলাকার নেটওয়ার্ক |
| **WLAN** | **W**ireless **LAN** → বেতার LAN |

**সহজ ভাষায়:** নেটওয়ার্ককে আকার অনুযায়ী ভাগ করা হয়: PAN = কয়েক মিটার (Bluetooth earbuds), LAN = একটা বাড়ি/অফিস/ভবন, MAN = একটা শহর, WAN = শহর বা দেশ জুড়ে।

> **বাস্তব পরিস্থিতি:** একটা ব্যাংকের ঢাকা হেড অফিসের ভেতরে LAN, ঢাকা শহরের ২০টা শাখা যুক্ত ISP-এর fiber দিয়ে (MAN), আর চট্টগ্রাম-সিলেটের শাখা যুক্ত WAN/MPLS দিয়ে।

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** LAN আর WAN-এর মূল পার্থক্য?
**উত্তর:** LAN ছোট এলাকায়, নিজের মালিকানায়, দ্রুত আর সস্তা। WAN বড় এলাকা জুড়ে, সাধারণত ISP বা telecom-এর লাইন ভাড়া নিয়ে, গতি কম আর খরচ বেশি।

### Topology: যন্ত্রগুলো কীভাবে সাজানো

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Topology** | নেটওয়ার্কের নকশা বা বিন্যাস |

**সহজ ভাষায়:** Bus (একটা তারে সবাই), Star (সবাই একটা কেন্দ্রীয় switch-এ), Ring (বৃত্তাকারে), Mesh (সবাই সবার সাথে), Hybrid (মিশ্র)। আজকের অফিস LAN প্রায় সবসময় Star, আর data center ও ISP-এর core-এ partial Mesh।

> **বাস্তব পরিস্থিতি:** অফিসে একটা switch নষ্ট হলে শুধু সেই switch-এর লোকজন অফলাইন হয়, বাকিরা না: Star topology-র বৈশিষ্ট্য। Data center-এ দুটো core switch আর প্রতিটি rack switch দুটোর সাথেই যুক্ত, যাতে একটা ভাঙলেও চলে: এটা redundancy-র জন্য partial mesh।

| Topology | সুবিধা | অসুবিধা |
|---|---|---|
| Bus | সস্তা, সহজ | মূল তার কাটলে সব বন্ধ |
| Star | সহজ ম্যানেজ, একটা তার নষ্ট হলে শুধু একজন | কেন্দ্রের switch নষ্ট হলে সবাই বন্ধ |
| Ring | নির্দিষ্ট পথ | একটা ভাঙলে পুরো ring সমস্যায় (dual ring ছাড়া) |
| Full Mesh | সর্বোচ্চ redundancy | অনেক তার, অনেক খরচ: n(n-1)/2 link |
| Hybrid | প্রয়োজন মতো মিশ্রণ | নকশা জটিল |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Full mesh-এ ৫টা router থাকলে কয়টা link লাগবে?
**উত্তর:** n(n-1)/2 = 5×4/2 = 10টা link।

### নেটওয়ার্কের যন্ত্রপাতি

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **NIC** | **N**etwork **I**nterface **C**ard → নেটওয়ার্ক কার্ড |
| **AP** | **A**ccess **P**oint → WiFi প্রবেশবিন্দু |
| **Modem** | **Mo**dulator-**Dem**odulator → সংকেত রূপান্তরকারী |
| **ONT/ONU** | **O**ptical **N**etwork **T**erminal/**U**nit → fiber-এর modem |
| **LB** | **L**oad **B**alancer → ভার বণ্টনকারী |

**সহজ ভাষায়:** প্রতিটি যন্ত্র নির্দিষ্ট স্তরে কাজ করে। Hub বোকা: যা পায় সবাইকে পাঠায়। Switch বুদ্ধিমান: MAC address দেখে শুধু সঠিক port-এ পাঠায়। Router আলাদা নেটওয়ার্কগুলোকে যুক্ত করে, IP দেখে পথ ঠিক করে।

> **বাস্তব পরিস্থিতি:** নতুন অফিস সাজাচ্ছ: ISP-এর fiber ONT-তে আসে, সেখান থেকে router/firewall, তারপর একটা 48-port switch, switch থেকে প্রতিটি ডেস্কে তার আর ছাদে কয়েকটা WiFi AP।

| যন্ত্র | OSI Layer | কী দেখে কাজ করে | কাজ |
|---|---|---|---|
| Hub | 1 (Physical) | কিছুই না | সব port-এ সব পাঠায় (এখন অচল) |
| Switch | 2 (Data Link) | MAC address | একই নেটওয়ার্কের যন্ত্র যুক্ত করে |
| Router | 3 (Network) | IP address | আলাদা নেটওয়ার্ক যুক্ত করে, পথ বের করে |
| L3 Switch | 2 + 3 | MAC ও IP | switch যেটা VLAN-এর মধ্যে routing-ও পারে |
| Firewall | 3-7 | IP, port, application | traffic অনুমতি দেয় বা আটকায় |
| Access Point | 2 | MAC | WiFi ডিভাইসকে তারের নেটওয়ার্কে যুক্ত করে |
| Load Balancer | 4 বা 7 | port বা HTTP | অনেক server-এর মধ্যে traffic ভাগ করে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Hub, Switch আর Router-এর পার্থক্য কী?
**উত্তর:** Hub (Layer 1) সব port-এ সব data পাঠায়, তাই collision আর নিরাপত্তা সমস্যা। Switch (Layer 2) MAC address table দেখে শুধু নির্দিষ্ট port-এ পাঠায়। Router (Layer 3) IP address দেখে আলাদা নেটওয়ার্কের মধ্যে packet পাঠায় আর broadcast আটকায়।

**প্রশ্ন:** Switch কি broadcast আটকায়?
**উত্তর:** না; switch broadcast সব port-এ পাঠায় (একই VLAN-এ)। Router বা VLAN broadcast domain ভাগ করে।

> 💡 **প্রো টিপ:** মনে রাখো: switch প্রতিটি port-কে আলাদা collision domain বানায়, router প্রতিটি interface-কে আলাদা broadcast domain বানায়।

### Cable ও মাধ্যম: Copper, Fiber, Wireless

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **UTP** | **U**nshielded **T**wisted **P**air → আবরণহীন প্যাঁচানো জোড়া তার |
| **STP** (cable) | **S**hielded **T**wisted **P**air → আবরণযুক্ত প্যাঁচানো তার |
| **SMF** | **S**ingle-**M**ode **F**iber → একক-মোড ফাইবার |
| **MMF** | **M**ulti-**M**ode **F**iber → বহু-মোড ফাইবার |
| **RJ45** | **R**egistered **J**ack 45 → ইথারনেট তারের মাথা |
| **SFP** | **S**mall **F**orm-factor **P**luggable → fiber/copper-এর ছোট মডিউল |

**সহজ ভাষায়:** Copper (UTP) সস্তা, ১০০ মিটার পর্যন্ত চলে; Cat5e = 1 Gbps, Cat6 = 1 Gbps (৫৫ মিটারে 10 Gbps), Cat6a = 10 Gbps ১০০ মিটারে। Fiber আলো দিয়ে data পাঠায়: বিদ্যুৎ-হস্তক্ষেপ মুক্ত, অনেক দূর যায়। MMF কম দূরত্বে (কয়েকশ মিটার, ভবনের ভেতরে), SMF অনেক দূরে (কিলোমিটার, শহরের মধ্যে)।

> **বাস্তব পরিস্থিতি:** দুটো ভবনের মধ্যে ৪০০ মিটার দূরত্ব; UTP ১০০ মিটারের বেশি চলবে না, তাই fiber বসাতে হবে। আবার একটা কারখানায় বড় মোটরের পাশ দিয়ে তার যাচ্ছে আর বারবার error আসছে: সেখানে shielded cable বা fiber সমাধান।

| বিষয় | ব্যাখ্যা |
|---|---|
| Straight-through | দুই মাথায় একই wiring; ভিন্ন যন্ত্র যুক্ত করতে (PC থেকে switch) |
| Crossover | দুই মাথায় উল্টো wiring; একই রকম যন্ত্র যুক্ত করতে (switch থেকে switch)। আধুনিক যন্ত্রে Auto-MDIX থাকায় এখন প্রায় দরকার হয় না |
| Half duplex | একসময়ে একদিকে (walkie-talkie) |
| Full duplex | একসাথে দুদিকে (ফোন কল); আধুনিক switch সবসময় full duplex |
| Duplex mismatch | একপাশে full, অন্যপাশে half: ধীর গতি আর অনেক error-এর সাধারণ কারণ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Cat6 cable সর্বোচ্চ কত মিটার?
**উত্তর:** Ethernet-এর জন্য ১০০ মিটার (৯০ মিটার স্থায়ী + ১০ মিটার patch)। 10 Gbps চাইলে Cat6-এ প্রায় ৫৫ মিটার, Cat6a-তে ১০০ মিটার।

**প্রশ্ন:** Single-mode আর multi-mode fiber-এর পার্থক্য?
**উত্তর:** Single-mode-এর core সরু, laser ব্যবহার করে, অনেক কিলোমিটার যায়, দামি। Multi-mode-এর core মোটা, LED/VCSEL ব্যবহার করে, কয়েকশ মিটার যায়, সস্তা।

---

## ০১. OSI ও TCP/IP মডেল

নেটওয়ার্কের সব আলোচনা আর troubleshooting এই স্তরগুলো ধরে চলে; interview-এ প্রায় ১০০% আসে।

### OSI মডেলের ৭টি স্তর

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **OSI** | **O**pen **S**ystems **I**nterconnection → উন্মুক্ত সিস্টেম আন্তঃসংযোগ মডেল |
| **ISO** | **I**nternational **O**rganization for **S**tandardization → যে সংস্থা মডেলটি বানিয়েছে |
| **PDU** | **P**rotocol **D**ata **U**nit → প্রতিটি স্তরে data-র নাম |

**সহজ ভাষায়:** OSI হলো নেটওয়ার্ক যোগাযোগকে ৭টি স্তরে ভাগ করে বোঝার একটা ধারণাগত মডেল। প্রতিটি স্তর নির্দিষ্ট কাজ করে আর শুধু উপরের ও নিচের স্তরের সাথে কথা বলে। মনে রাখার কৌশল (উপর থেকে নিচে): **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing।

> **বাস্তব পরিস্থিতি:** User বলছে "ওয়েবসাইট খুলছে না"। তুমি স্তর ধরে যাচাই করো: তার লাগানো আছে তো (L1)? link light জ্বলছে? IP পেয়েছে (L3)? port 443 খোলা (L4)? HTTP-তে কী error দিচ্ছে (L7)? এভাবে কোন স্তরে সমস্যা, দ্রুত ধরা যায়।

| নং | স্তর | কাজ | PDU | উদাহরণ |
|---|---|---|---|---|
| 7 | Application | user-এর অ্যাপের সাথে নেটওয়ার্কের সংযোগ | Data | HTTP, DNS, SMTP, SSH |
| 6 | Presentation | format, encryption, compression | Data | TLS/SSL, JPEG, ASCII |
| 5 | Session | session শুরু, বজায় রাখা, শেষ করা | Data | NetBIOS, RPC |
| 4 | Transport | end-to-end যোগাযোগ, port, নির্ভরযোগ্যতা | Segment (TCP) / Datagram (UDP) | TCP, UDP |
| 3 | Network | logical address (IP) আর routing | Packet | IP, ICMP, OSPF |
| 2 | Data Link | একই নেটওয়ার্কে frame পাঠানো, MAC | Frame | Ethernet, ARP, VLAN, Switch |
| 1 | Physical | bit-কে বিদ্যুৎ/আলো/তরঙ্গে রূপান্তর | Bits | Cable, Hub, connector |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** OSI মডেলের ৭টি স্তর বলো আর প্রতিটির একটা করে protocol বলো।
**উত্তর:** উপরের টেবিলটাই উত্তর। নিচ থেকে বলা অভ্যাস করো: Physical, Data Link, Network, Transport, Session, Presentation, Application।

**প্রশ্ন:** কোন স্তরে router, switch আর hub কাজ করে?
**উত্তর:** Router Layer 3, switch Layer 2, hub Layer 1।

**প্রশ্ন:** TCP কোন স্তরে, IP কোন স্তরে?
**উত্তর:** TCP Layer 4 (Transport), IP Layer 3 (Network)।

> 💡 **প্রো টিপ:** নিচ থেকে উপরে মনে রাখার বাক্য: **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way।

### Encapsulation ও De-encapsulation

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Header** | সামনে জোড়া তথ্য |
| **Trailer** | পেছনে জোড়া তথ্য |
| **FCS** | **F**rame **C**heck **S**equence → frame-এ error আছে কিনা পরীক্ষার সংখ্যা |

**সহজ ভাষায়:** পাঠানোর সময় data উপর থেকে নিচে নামে আর প্রতিটি স্তর নিজের header যোগ করে, যেন চিঠিকে খামের ভেতরে, সেই খামকে আরেক বড় খামে ভরা; একে encapsulation বলে। গ্রহণকারী নিচ থেকে উপরে খাম খোলে: de-encapsulation।

> **বাস্তব পরিস্থিতি:** tcpdump বা Wireshark-এ একটা packet খুললে তুমি ঠিক এই স্তরগুলো দেখবে: Ethernet header (MAC), তার ভেতরে IP header, তার ভেতরে TCP header, তার ভেতরে HTTP data।

```text
[ Ethernet Header | IP Header | TCP Header | HTTP Data | FCS ]
    L2 (MAC)         L3 (IP)    L4 (Port)    L7 (Data)   L2 trailer
```

| স্তর | কী যোগ হয় |
|---|---|
| Transport (L4) | source ও destination port, sequence number |
| Network (L3) | source ও destination IP, TTL |
| Data Link (L2) | source ও destination MAC, আর শেষে FCS |
| Physical (L1) | সব কিছু bit হয়ে তারে যায় |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Router যখন packet forward করে, কোন header বদলায়?
**উত্তর:** প্রতিটি hop-এ Layer 2 header (source/destination MAC) বদলায়, কিন্তু Layer 3-এর source/destination IP একই থাকে (NAT না থাকলে); আর TTL এক কমে।

> 💡 **প্রো টিপ:** শেষের প্রশ্নটা খুব জনপ্রিয় "trick question"। MAC hop-by-hop, IP end-to-end।

### TCP/IP মডেল ও OSI-এর সাথে তুলনা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **TCP/IP** | **T**ransmission **C**ontrol **P**rotocol / **I**nternet **P**rotocol → ইন্টারনেটের আসল protocol স্যুট |
| **DoD model** | **D**epartment **o**f **D**efense model → মার্কিন প্রতিরক্ষা বিভাগের মডেল |

**সহজ ভাষায়:** OSI হলো শেখানোর মডেল, আর বাস্তবে ইন্টারনেট চলে TCP/IP মডেলে। এতে ৪টি স্তর (কোথাও ৫টি দেখানো হয়); OSI-এর উপরের ৩টি স্তর এখানে মিলে একটা Application স্তর।

> **বাস্তব পরিস্থিতি:** Developer বলছে "নেটওয়ার্ক ঠিক আছে, ping যাচ্ছে"। কিন্তু ping হলো Layer 3 (ICMP)। অ্যাপ চলে Layer 4/7-এ, তাই port বন্ধ থাকলে ping চললেও অ্যাপ চলবে না। স্তরের ধারণা থাকলে এই ভুল বোঝাবুঝি এড়ানো যায়।

| TCP/IP স্তর | OSI-তে সমান স্তর | Protocol |
|---|---|---|
| Application | 7, 6, 5 | HTTP, DNS, SSH, FTP, SMTP |
| Transport | 4 | TCP, UDP |
| Internet | 3 | IP, ICMP, ARP* |
| Network Access (Link) | 2, 1 | Ethernet, WiFi |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** OSI আর TCP/IP মডেলের পার্থক্য?
**উত্তর:** OSI ৭ স্তরের তাত্ত্বিক reference মডেল; TCP/IP ৪ স্তরের বাস্তব মডেল যেটা দিয়ে ইন্টারনেট চলে। TCP/IP-এ Session ও Presentation আলাদা নেই, Application-এর ভেতরে।

> 💡 **প্রো টিপ:** *ARP-কে কেউ Layer 2, কেউ Layer 2.5 বা 3 বলে। Interview-এ বলো "ARP Layer 2 আর 3-এর মাঝে কাজ করে, IP থেকে MAC বের করে"।

---

## ০২. IPv4 Addressing

প্রতিটি যন্ত্রের নেটওয়ার্ক ঠিকানা; এটা না বুঝলে subnetting, routing, firewall কিছুই বোঝা যাবে না।

### IPv4 address-এর গঠন

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **IP** | **I**nternet **P**rotocol → ইন্টারনেট প্রোটোকল |
| **IPv4** | **I**nternet **P**rotocol **v**ersion **4** → IP-এর ৪র্থ সংস্করণ |
| **Octet** | (৮ bit) → আট bit-এর একটা অংশ |

**সহজ ভাষায়:** IPv4 address হলো 32-bit-এর একটা সংখ্যা, যেটা পড়ার সুবিধার জন্য ৪টা ভাগে (octet) লেখা হয়, প্রতিটি 0 থেকে 255; যেমন `192.168.1.10`। এর একটা অংশ নেটওয়ার্ক বোঝায় (কোন এলাকা), বাকিটা host (কোন বাড়ি)। কোন অংশ কোনটা, সেটা ঠিক করে subnet mask।

> **বাস্তব পরিস্থিতি:** নতুন সার্ভারে IP দিচ্ছ: `192.168.1.50/24`। এর মানে প্রথম ২৪ bit (`192.168.1`) নেটওয়ার্ক, শেষের 8 bit (`.50`) host। একই নেটওয়ার্কের অন্য যন্ত্রের সাথে সরাসরি কথা বলবে, বাইরের জন্য gateway লাগবে।

```text
 192     .168     .1       .10
 11000000.10101000.00000001.00001010
 |<------ network (24 bit) ------>|<host>|
 মোট ২^৩২ = প্রায় ৪৩০ কোটি address

 Binary bit-এর মান (বাম থেকে ডানে)
 128  64  32  16  8  4  2  1
 যেমন 192 = 128 + 64 = 11000000
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IPv4 address কত bit-এর?
**উত্তর:** 32 bit, ৪টা octet, প্রতিটি 8 bit, মান 0-255।

### Subnet mask, CIDR, Network/Broadcast address

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CIDR** | **C**lassless **I**nter-**D**omain **R**outing → class ছাড়া address ভাগের পদ্ধতি |
| **/24** | prefix length → কতগুলো bit network-এর |

**সহজ ভাষায়:** Subnet mask বলে দেয় IP-এর কতটা network অংশ। `255.255.255.0` মানে প্রথম ২৪ bit network; একে সংক্ষেপে `/24` লেখা হয় (CIDR notation)। প্রতিটি subnet-এ প্রথম address হলো **network address** (নেটওয়ার্কের নাম, কোনো যন্ত্রকে দেওয়া যায় না), শেষ address **broadcast** (সবাইকে একসাথে পাঠানোর ঠিকানা); মাঝেরগুলো host-কে দেওয়া যায়।

> **বাস্তব পরিস্থিতি:** Firewall rule লিখছ `10.0.5.0/24`-কে অনুমতি দিতে। জানতে হবে এতে কোন IP থেকে কোন IP পড়ে: `10.0.5.1` থেকে `10.0.5.254`। ভুল করে `/16` লিখলে পুরো `10.0.x.x` খুলে যাবে, বড় নিরাপত্তা ঝুঁকি।

| CIDR | Subnet mask | মোট address | ব্যবহারযোগ্য host |
|---|---|---|---|
| /30 | 255.255.255.252 | 4 | 2 (router-to-router link) |
| /29 | 255.255.255.248 | 8 | 6 |
| /28 | 255.255.255.240 | 16 | 14 |
| /27 | 255.255.255.224 | 32 | 30 |
| /26 | 255.255.255.192 | 64 | 62 |
| /25 | 255.255.255.128 | 128 | 126 |
| /24 | 255.255.255.0 | 256 | 254 |
| /16 | 255.255.0.0 | 65,536 | 65,534 |
| /8 | 255.0.0.0 | 16,777,216 | 16,777,214 |

**সূত্র:** ব্যবহারযোগ্য host = 2^(host bit) - 2। -2 কারণ network আর broadcast address বাদ।

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** /24-এ কয়টা host বসানো যায়?
**উত্তর:** 2^8 - 2 = 254।

**প্রশ্ন:** /31 কেন ব্যবহার হয়?
**উত্তর:** RFC 3021 অনুযায়ী point-to-point link-এ /31-এ network/broadcast লাগে না, তাই ২টা address-ই ব্যবহার করা যায়; IP বাঁচে।

> 💡 **প্রো টিপ:** ক্লাউডে (AWS) প্রতিটি subnet থেকে ৫টা IP রিজার্ভ থাকে, তাই /24-এ 251টা পাবে, 254 নয়।

### IP Class, Private ও Public IP

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **RFC 1918** | **R**equest **F**or **C**omments 1918 → private IP নির্ধারণকারী মান |
| **NAT** | **N**etwork **A**ddress **T**ranslation → ঠিকানা রূপান্তর |
| **CGNAT** | **C**arrier-**G**rade **NAT** → ISP স্তরের NAT |

**সহজ ভাষায়:** পুরনো দিনে IP-কে class-এ ভাগ করা হতো (A, B, C, D, E)। এখন CIDR চলে, তবে interview-এ class জিজ্ঞেস করে। Private IP শুধু ভেতরের নেটওয়ার্কে চলে, ইন্টারনেটে route হয় না; অসংখ্য অফিস একই private IP ব্যবহার করতে পারে। Public IP পুরো পৃথিবীতে অনন্য, ISP থেকে কিনতে হয়।

> **বাস্তব পরিস্থিতি:** বাসার router তোমার ফোনকে `192.168.0.105` দেয় (private)। ইন্টারনেটে গেলে router NAT করে নিজের public IP (যেমন `103.x.x.x`) দিয়ে বের হয়। বাংলাদেশের অনেক ISP আবার CGNAT ব্যবহার করে, তাই router-এর WAN-এ `100.64.x.x` দেখলে বুঝবে তোমার নিজস্ব public IP নেই, ফলে বাইরে থেকে port forward কাজ করবে না।

| Class | প্রথম octet | Default mask | ব্যবহার |
|---|---|---|---|
| A | 1 - 126 | /8 | বড় নেটওয়ার্ক |
| B | 128 - 191 | /16 | মাঝারি |
| C | 192 - 223 | /24 | ছোট |
| D | 224 - 239 | - | Multicast |
| E | 240 - 255 | - | পরীক্ষামূলক |

| বিশেষ রেঞ্জ | কী |
|---|---|
| `10.0.0.0/8` | Private (Class A) |
| `172.16.0.0/12` (172.16 - 172.31) | Private (Class B) |
| `192.168.0.0/16` | Private (Class C) |
| `127.0.0.0/8` | Loopback, নিজেকে নিজে (`127.0.0.1` = localhost) |
| `169.254.0.0/16` | APIPA / link-local: DHCP থেকে IP না পেলে যন্ত্র নিজে নেয় |
| `100.64.0.0/10` | CGNAT, ISP-এর shared address |
| `0.0.0.0` | "যেকোনো address" বা default route |
| `255.255.255.255` | Limited broadcast |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Private IP range গুলো বলো।
**উত্তর:** `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` (RFC 1918)।

**প্রশ্ন:** PC-তে `169.254.x.x` IP দেখলে কী বুঝবে?
**উত্তর:** PC DHCP server থেকে IP পায়নি, তাই নিজে APIPA address নিয়েছে। DHCP server, cable, VLAN বা DHCP relay পরীক্ষা করো।

**প্রশ্ন:** 127 কেন Class A-তে নেই?
**উত্তর:** `127.0.0.0/8` loopback-এর জন্য সংরক্ষিত, তাই Class A হিসাবে 1-126।

> 💡 **প্রো টিপ:** 169.254 দেখলেই "DHCP সমস্যা" বলে দেওয়া একটা ক্লাসিক interview উত্তর।

### Unicast, Broadcast, Multicast, Anycast

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Unicast** | uni (এক) + cast → একজনকে পাঠানো |
| **Broadcast** | broad + cast → সবাইকে পাঠানো |
| **Multicast** | multi + cast → নির্দিষ্ট দলকে পাঠানো |
| **Anycast** | any + cast → সবচেয়ে কাছের একজনকে |

**সহজ ভাষায়:** Unicast = একজন থেকে একজন (সাধারণ ওয়েব ব্রাউজিং)। Broadcast = একজন থেকে নেটওয়ার্কের সবাই (ARP, DHCP Discover)। Multicast = একজন থেকে যারা যোগ দিয়েছে তারা (IPTV, OSPF update)। Anycast = একই IP অনেক জায়গায়, তুমি সবচেয়ে কাছেরটায় যাও (Google DNS `8.8.8.8`, Cloudflare `1.1.1.1`, CDN)।

> **বাস্তব পরিস্থিতি:** ঢাকা থেকে `8.8.8.8`-এ ping করলে latency মাত্র কয়েক ms, অথচ Google-এর মূল অফিস আমেরিকায়। কারণ `8.8.8.8` anycast: একই IP সিঙ্গাপুর বা কাছের কোনো data center-ও announce করে, তুমি সেখানে পৌঁছাও।

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IPv6-এ broadcast আছে কি?
**উত্তর:** না; IPv6-এ broadcast নেই, তার বদলে multicast (যেমন `ff02::1` = সব node) ব্যবহার হয়।

---

## ০৩. Subnetting (ধাপে ধাপে)

Network engineer interview-এর সবচেয়ে নিশ্চিত প্রশ্ন। খাতা কলম নিয়ে অনুশীলন করো, যতক্ষণ না ৩০ সেকেন্ডে উত্তর দিতে পারো।

### Subnetting কেন ও কীভাবে: Magic Number পদ্ধতি

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Subnet** | sub-network → বড় নেটওয়ার্কের ছোট ভাগ |
| **Block size** | (magic number) → প্রতিটি subnet-এর আকার |

**সহজ ভাষায়:** একটা বড় নেটওয়ার্ককে ছোট ছোট ভাগে ভাঙা হলো subnetting। সুবিধা: broadcast কমে, নিরাপত্তার জন্য বিভাগ আলাদা করা যায়, IP নষ্ট হয় না। সবচেয়ে দ্রুত পদ্ধতি: **block size (magic number) = 256 - শেষ যে octet-এর mask 255 না, তার মান**।

> **বাস্তব পরিস্থিতি:** কোম্পানি তোমাকে `192.168.10.0/24` দিয়েছে। HR, Finance, IT আর Guest WiFi-কে আলাদা করতে হবে, প্রতিটিতে সর্বোচ্চ ৫০টা যন্ত্র। ৫০ host-এর জন্য ৬ bit লাগে (2^6 - 2 = 62), তাই /26; চারটা /26 subnet পাবে।

```text
উদাহরণ: 192.168.10.0/26
১) /26 = 255.255.255.192
২) Block size = 256 - 192 = 64
৩) Subnet গুলো 64 করে বাড়ে:

Subnet    Network          First host       Last host        Broadcast
HR        192.168.10.0     192.168.10.1     192.168.10.62    192.168.10.63
Finance   192.168.10.64    192.168.10.65    192.168.10.126   192.168.10.127
IT        192.168.10.128   192.168.10.129   192.168.10.190   192.168.10.191
Guest     192.168.10.192   192.168.10.193   192.168.10.254   192.168.10.255
```

| ধাপ | কী করবে |
|---|---|
| ১. কত host লাগবে | 2^n - 2 >= host সংখ্যা, এমন সবচেয়ে ছোট n (host bit) বের করো |
| ২. prefix | prefix = 32 - n |
| ৩. block size | 256 - mask-এর আকর্ষণীয় octet |
| ৪. network address | 0 থেকে block size করে বাড়াও |
| ৫. broadcast | পরের network - 1 |
| ৬. host range | network + 1 থেকে broadcast - 1 |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `172.16.35.123/20` কোন subnet-এ? network, broadcast, host range বলো।
**উত্তর:** /20 = 255.255.240.0, তৃতীয় octet-এ block = 256 - 240 = 16। 35 পড়ে 32 আর 48-এর মাঝে। Network `172.16.32.0`, broadcast `172.16.47.255`, host `172.16.32.1` থেকে `172.16.47.254`।

**প্রশ্ন:** ২০০টা host-এর জন্য কোন prefix?
**উত্তর:** 2^8 - 2 = 254 >= 200, তাই 8 host bit, /24।

**প্রশ্ন:** `10.0.0.0/8`-কে /12-তে ভাগ করলে কয়টা subnet?
**উত্তর:** 2^(12-8) = 16টা।

> 💡 **প্রো টিপ:** নিয়ম: subnet সংখ্যা = 2^(ধার করা bit), host সংখ্যা = 2^(বাকি host bit) - 2।

### VLSM: প্রয়োজন মতো আকারের subnet

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **VLSM** | **V**ariable **L**ength **S**ubnet **M**ask → পরিবর্তনশীল দৈর্ঘ্যের subnet mask |

**সহজ ভাষায়:** সব বিভাগের জন্য একই আকারের subnet দিলে IP নষ্ট হয়। VLSM-এ প্রতিটি বিভাগকে তার প্রয়োজন মতো আকার দেওয়া হয়। নিয়ম: সবচেয়ে বড় প্রয়োজন আগে বসাও, তারপর ছোটগুলো।

> **বাস্তব পরিস্থিতি:** `192.168.20.0/24` থেকে দিতে হবে: Sales 100 host, Dev 50 host, Admin 20 host, আর দুটো router link (প্রতিটিতে 2 host)।

```text
বড় থেকে ছোট সাজিয়ে:
Sales  100 host -> /25 (126) -> 192.168.20.0   - .127
Dev     50 host -> /26 (62)  -> 192.168.20.128 - .191
Admin   20 host -> /27 (30)  -> 192.168.20.192 - .223
Link1    2 host -> /30 (2)   -> 192.168.20.224 - .227
Link2    2 host -> /30 (2)   -> 192.168.20.228 - .231
বাকি 192.168.20.232 - .255 ভবিষ্যতের জন্য
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** VLSM আর FLSM-এর পার্থক্য?
**উত্তর:** FLSM (Fixed Length)-এ সব subnet সমান আকারের; VLSM-এ প্রয়োজন অনুযায়ী আলাদা আকারের, তাই IP অপচয় কম। VLSM-এর জন্য classless routing protocol (OSPF, EIGRP, RIPv2) লাগে।

### Supernetting / Route Summarization

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Summarization** | route summarization → অনেক route-কে একটা বড় route-এ সংক্ষেপ |

**সহজ ভাষায়:** অনেকগুলো পাশাপাশি নেটওয়ার্ককে একটা বড় prefix দিয়ে প্রকাশ করা। এতে routing table ছোট হয়, router দ্রুত কাজ করে, আর একটা ছোট নেটওয়ার্কের ওঠানামায় পুরো নেটওয়ার্কে update ছড়ায় না।

> **বাস্তব পরিস্থিতি:** শাখা অফিসে `10.1.0.0/24`, `10.1.1.0/24`, `10.1.2.0/24`, `10.1.3.0/24` আছে। Head office-এর router-এ চারটা আলাদা route না দিয়ে একটা দাও: `10.1.0.0/22`।

```text
10.1.0.0 = 00001010.00000001.000000|00.0
10.1.1.0 = 00001010.00000001.000000|01.0
10.1.2.0 = 00001010.00000001.000000|10.0
10.1.3.0 = 00001010.00000001.000000|11.0
মিল আছে প্রথম 22 bit -> summary 10.1.0.0/22
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** `192.168.4.0/24` থেকে `192.168.7.0/24`-এর summary কী?
**উত্তর:** 4 থেকে 7 = চারটা নেটওয়ার্ক = 2 bit, তাই /22; 4 সংখ্যাটা 4-এর গুণিতক বলে summary `192.168.4.0/22`।

---

## ০৪. IPv6

IPv4 address শেষ হয়ে গেছে; মোবাইল নেটওয়ার্ক, ক্লাউড আর বড় ISP এখন IPv6 চালাচ্ছে, তাই interview-এ বেসিক প্রশ্ন আসবেই।

### IPv6-এর গঠন ও সংক্ষেপে লেখার নিয়ম

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **IPv6** | **I**nternet **P**rotocol **v**ersion **6** → IP-এর ৬ষ্ঠ সংস্করণ |
| **Hextet** | hexadecimal + tet → ১৬ bit-এর একটা অংশ |

**সহজ ভাষায়:** IPv6 address 128 bit-এর, ৮টা ভাগে (hextet) hexadecimal-এ লেখা, কোলন দিয়ে আলাদা। মোট address 2^128, প্রায় অসীম। লেখা ছোট করার দুটো নিয়ম আছে: প্রতিটি ভাগের শুরুর শূন্য বাদ দেওয়া যায়, আর পরপর সব-শূন্য ভাগগুলোকে একবার `::` দিয়ে লেখা যায়।

> **বাস্তব পরিস্থিতি:** ক্লাউডে একটা load balancer-এর IPv6 address দেখছ `2001:0db8:0000:0000:0000:ff00:0042:8329`। Firewall rule-এ ছোট করে লিখবে `2001:db8::ff00:42:8329`।

```text
পুরো : 2001:0db8:0000:0000:0000:ff00:0042:8329
ধাপ ১: 2001:db8:0:0:0:ff00:42:8329   (শুরুর শূন্য বাদ)
ধাপ ২: 2001:db8::ff00:42:8329        (পরপর শূন্য = ::)
ভুল : 2001::25de::cade               (:: দুবার দেওয়া যায় না)
```

| ধরন | Prefix | IPv4-এ সমতুল্য |
|---|---|---|
| Global Unicast | `2000::/3` | Public IP |
| Link-Local | `fe80::/10` | 169.254 (একই link-এ, route হয় না) |
| Unique Local (ULA) | `fc00::/7` (বাস্তবে `fd00::/8`) | Private IP |
| Multicast | `ff00::/8` | Multicast / broadcast-এর বদলে |
| Loopback | `::1` | 127.0.0.1 |
| Unspecified | `::` | 0.0.0.0 |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IPv6 address কত bit-এর আর সংক্ষেপে লেখার নিয়ম কী?
**উত্তর:** 128 bit। প্রতিটি hextet-এর শুরুর শূন্য বাদ দেওয়া যায়, আর পরপর শূন্য hextet একবারই `::` দিয়ে লেখা যায়।

**প্রশ্ন:** IPv6-এ ARP-এর বদলে কী?
**উত্তর:** NDP (Neighbor Discovery Protocol), যেটা ICMPv6 ব্যবহার করে।

### SLAAC, DHCPv6, EUI-64 ও Dual Stack

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SLAAC** | **S**tate**l**ess **A**ddress **A**uto**c**onfiguration → server ছাড়া নিজে নিজে address বানানো |
| **DHCPv6** | **D**ynamic **H**ost **C**onfiguration **P**rotocol for **IPv6** → IPv6-এর DHCP |
| **EUI-64** | **E**xtended **U**nique **I**dentifier 64-bit → MAC থেকে ৬৪-bit ID বানানোর পদ্ধতি |
| **NDP** | **N**eighbor **D**iscovery **P**rotocol → প্রতিবেশী খোঁজার protocol |
| **RA** | **R**outer **A**dvertisement → router-এর ঘোষণা |

**সহজ ভাষায়:** IPv6-এ যন্ত্র router-এর Router Advertisement থেকে prefix (প্রথম 64 bit) জেনে নিজে বাকি 64 bit বানিয়ে নেয় (SLAAC), DHCP server ছাড়াই। বাকি অংশ MAC থেকে (EUI-64) বা এলোমেলো (privacy address) হতে পারে। Dual stack মানে একই যন্ত্রে IPv4 আর IPv6 দুটোই চালু।

> **বাস্তব পরিস্থিতি:** মোবাইল অপারেটরের নেটওয়ার্কে তোমার ফোন IPv6 পায় কোনো DHCP ছাড়া, router-এর RA থেকে। অফিসে migration-এর সময় dual stack চালানো হয়, যাতে পুরনো IPv4-only সার্ভিসও চলে আর নতুনগুলো IPv6-এ।

| কৌশল | কাজ |
|---|---|
| Dual Stack | একসাথে IPv4 ও IPv6 চালাও; সবচেয়ে প্রচলিত |
| Tunneling (6in4) | IPv6 packet-কে IPv4-এর ভেতরে ভরে পাঠাও |
| NAT64 / DNS64 | IPv6-only যন্ত্রকে IPv4 সার্ভারে পৌঁছাতে দাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IPv6-এ subnet-এর আদর্শ আকার কত?
**উত্তর:** /64; SLAAC কাজ করার জন্য host অংশ 64 bit হতে হয়।

**প্রশ্ন:** IPv4 থেকে IPv6-এ যাওয়ার কৌশলগুলো কী?
**উত্তর:** Dual stack, tunneling আর translation (NAT64)।

---

## ০৫. Layer 2: Ethernet, MAC, Switching, VLAN, STP

অফিস ও data center-এর ভেতরের যোগাযোগ এই স্তরে; CCNA স্তরের interview-এর বড় অংশ এখান থেকে।

### MAC address ও Ethernet frame

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **MAC** | **M**edia **A**ccess **C**ontrol → মাধ্যমে প্রবেশ নিয়ন্ত্রণ, যন্ত্রের hardware ঠিকানা |
| **OUI** | **O**rganizationally **U**nique **I**dentifier → নির্মাতা কোম্পানির পরিচয় |
| **MTU** | **M**aximum **T**ransmission **U**nit → এক frame-এ সর্বোচ্চ data |

**সহজ ভাষায়:** MAC address হলো network card-এর 48-bit hardware ঠিকানা, hexadecimal-এ লেখা (যেমন `00:1A:2B:3C:4D:5E`)। প্রথম 24 bit নির্মাতা (OUI), শেষ 24 bit card-এর নিজস্ব নম্বর। MAC শুধু একই local নেটওয়ার্কের ভেতরে কাজে লাগে; router পার হলে বদলে যায়। IP হলো চিঠির ঠিকানা, MAC হলো পরের পোস্ট অফিস কোনটা।

> **বাস্তব পরিস্থিতি:** অফিসের WiFi-তে MAC filtering চালু; নতুন ল্যাপটপ যুক্ত হচ্ছে না। তুমি ল্যাপটপের MAC বের করে whitelist-এ যোগ করলে। খেয়াল রেখো, আধুনিক ফোন প্রতিটি WiFi-তে random MAC দেয়, তাই MAC filtering নির্ভরযোগ্য নিরাপত্তা নয়।

```text
Ethernet frame:
[ Preamble | Dest MAC | Src MAC | Type (0x0800=IPv4) | Data (46-1500 byte) | FCS ]
ffff.ffff.ffff = broadcast MAC (সবাই)
```

| কমান্ড | কী দেখায় |
|---|---|
| `ip link show` (Linux) | নিজের interface-এর MAC (link/ether) |
| `ipconfig /all` (Windows) | Physical Address = MAC |
| `show mac address-table` (Cisco) | switch কোন MAC কোন port-এ শিখেছে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** MAC আর IP-এর পার্থক্য?
**উত্তর:** MAC 48-bit, hardware-এ, Layer 2, শুধু local নেটওয়ার্কে কাজে লাগে। IP 32/128-bit, logical, Layer 3, end-to-end routing-এ কাজে লাগে।

**প্রশ্ন:** Ethernet-এর default MTU কত?
**উত্তর:** 1500 byte। Data center-এ jumbo frame 9000 byte পর্যন্ত।

### Switch কীভাবে MAC শেখে ও frame পাঠায়

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CAM table** | **C**ontent **A**ddressable **M**emory table → MAC address table |
| **Flooding** | সব port-এ পাঠানো |

**সহজ ভাষায়:** Switch প্রতিটি আগত frame-এর source MAC আর কোন port থেকে এসেছে সেটা টেবিলে লিখে রাখে (**learning**)। Destination MAC টেবিলে থাকলে শুধু সেই port-এ পাঠায় (**forwarding**)। না থাকলে বা broadcast হলে সব port-এ পাঠায় (**flooding**)। একই port-এ পাঠানোর দরকার হলে ফেলে দেয় (**filtering**)। Entry সাধারণত ৩০০ সেকেন্ড পর মুছে যায় (**aging**)।

> **বাস্তব পরিস্থিতি:** একটা PC এক port থেকে অন্য port-এ সরানো হলো, কিছুক্ষণ সংযোগ সমস্যা হলো; কারণ switch-এর টেবিলে পুরনো port লেখা ছিল। PC নতুন port থেকে কিছু পাঠালেই switch নতুন port শিখে নেয়।

| কমান্ড (Cisco) | অক্ষরের মানে |
|---|---|
| `show mac address-table` | show = দেখাও, mac address-table = MAC-এর টেবিল |
| `show mac address-table interface gi0/1` | gi = GigabitEthernet, 0/1 = slot 0, port 1 |
| `clear mac address-table dynamic` | dynamic (স্বয়ংক্রিয় শেখা) entry মুছে দাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Switch unknown unicast পেলে কী করে?
**উত্তর:** Destination MAC টেবিলে না থাকলে, যে port থেকে এসেছে সেটা বাদে একই VLAN-এর সব port-এ flood করে।

### ARP: IP থেকে MAC বের করা

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ARP** | **A**ddress **R**esolution **P**rotocol → ঠিকানা সমাধানের protocol |
| **GARP** | **G**ratuitous **ARP** → কেউ না চাইতেই নিজে ঘোষণা |

**সহজ ভাষায়:** PC জানে কোন IP-তে পাঠাতে হবে, কিন্তু frame বানাতে MAC লাগে। তাই broadcast করে জিজ্ঞেস করে "Who has 192.168.1.1? Tell 192.168.1.10"। যার IP, সে unicast-এ নিজের MAC জানায়; উত্তর কিছুক্ষণ ARP cache-এ থাকে। গন্তব্য অন্য নেটওয়ার্কে হলে PC গন্তব্যের নয়, default gateway-এর MAC খোঁজে।

> **বাস্তব পরিস্থিতি:** দুটো যন্ত্রে ভুল করে একই IP দেওয়া হয়েছে (IP conflict)। সংযোগ একবার আসে একবার যায়; `arp -a`-তে দেখলে ওই IP-এর MAC বারবার বদলাচ্ছে: এটাই প্রমাণ। আবার server failover-এর পর নতুন server gratuitous ARP পাঠায় যাতে সবাই নতুন MAC শিখে নেয়।

| কমান্ড | অক্ষরের মানে / কাজ |
|---|---|
| `ip neigh` (Linux) | neigh = neighbour, ARP টেবিল দেখাও |
| `arp -a` (Windows/Linux) | a = all, সব ARP entry |
| `arp -d *` (Windows) | d = delete, ARP cache মুছে দাও |
| `show ip arp` (Cisco) | router-এর ARP টেবিল |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** ARP request broadcast না unicast? reply?
**উত্তর:** Request broadcast (`ff:ff:ff:ff:ff:ff`), reply unicast।

**প্রশ্ন:** অন্য নেটওয়ার্কের host-এর জন্য PC কার MAC খোঁজে?
**উত্তর:** নিজের default gateway-এর MAC।

**প্রশ্ন:** Gratuitous ARP কী কাজে লাগে?
**উত্তর:** IP conflict ধরা, আর failover-এর পর সবাইকে নতুন MAC জানানো (HSRP/VRRP, cluster)।

### VLAN ও Trunk (802.1Q)

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **VLAN** | **V**irtual **L**ocal **A**rea **N**etwork → ভার্চুয়াল LAN |
| **802.1Q** | IEEE 802.1Q (dot1q) → VLAN tag-এর মান |
| **VID** | **V**LAN **ID**entifier → VLAN নম্বর |
| **IEEE** | **I**nstitute of **E**lectrical and **E**lectronics **E**ngineers → মান নির্ধারণকারী সংস্থা |

**সহজ ভাষায়:** VLAN দিয়ে একটা physical switch-কে কয়েকটা আলাদা logical নেটওয়ার্কে ভাগ করা যায়; প্রতিটি VLAN আলাদা broadcast domain। এক VLAN-এর যন্ত্র অন্য VLAN-এর সাথে router ছাড়া কথা বলতে পারে না। **Access port** একটা VLAN বহন করে (PC-এর জন্য)। **Trunk port** অনেক VLAN বহন করে (switch থেকে switch বা router), প্রতিটি frame-এ 802.1Q tag (৪ byte) লাগিয়ে বোঝায় কোন VLAN-এর।

> **বাস্তব পরিস্থিতি:** একই ভবনে HR আর Guest WiFi একই switch-এ। নিরাপত্তার জন্য HR-কে VLAN 10, Guest-কে VLAN 99-তে রাখলে Guest-রা HR-এর কম্পিউটার দেখতেই পাবে না। দুই তলার switch-এর মাঝে trunk দিয়ে দুই VLAN-ই পাঠানো হয়।

```text
Switch(config)# vlan 10
Switch(config-vlan)# name HR
Switch(config)# interface gi0/5
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config)# interface gi0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20,99
Switch# show vlan brief
Switch# show interfaces trunk
```

| শব্দ | মানে |
|---|---|
| `switchport mode access` | এই port একটা VLAN-এর জন্য (PC/printer) |
| `switchport mode trunk` | এই port অনেক VLAN বহন করবে (tag সহ) |
| `allowed vlan` | trunk-এ শুধু এই VLAN গুলো যাবে |
| Native VLAN | trunk-এ যে VLAN tag ছাড়া যায় (default VLAN 1) |
| `show vlan brief` | brief = সংক্ষেপে, কোন port কোন VLAN-এ |
| VLAN রেঞ্জ | 1-4094 ব্যবহারযোগ্য (12 bit VID); 1 default, 1002-1005 পুরনো সংরক্ষিত |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Access port আর trunk port-এর পার্থক্য?
**উত্তর:** Access port একটা VLAN, tag ছাড়া, end device-এর জন্য। Trunk port অনেক VLAN, 802.1Q tag সহ, switch/router-এর মধ্যে।

**প্রশ্ন:** Native VLAN কী আর কেন বদলানো উচিত?
**উত্তর:** Trunk-এ যে VLAN-এর traffic untagged যায়; default VLAN 1। VLAN hopping attack এড়াতে অব্যবহৃত একটা VLAN-এ বদলানো ভালো।

**প্রশ্ন:** সর্বোচ্চ কয়টা VLAN?
**উত্তর:** 802.1Q-এ VLAN ID 12 bit, তাই 4096-এর মধ্যে 1-4094 ব্যবহারযোগ্য।

### Inter-VLAN Routing

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SVI** | **S**witched **V**irtual **I**nterface → switch-এর ভেতরে VLAN-এর ভার্চুয়াল interface |
| **ROAS** | **R**outer **O**n **A** **S**tick → একটা লাঠিতে router |

**সহজ ভাষায়:** আলাদা VLAN একে অপরের সাথে কথা বলতে Layer 3 লাগে। দুটো উপায়: Router-on-a-stick (একটা router-এর একটা physical port-কে sub-interface-এ ভাগ করে প্রতিটি VLAN-এর gateway বানানো) বা Layer 3 switch-এ SVI (`interface vlan 10`) বানিয়ে switch নিজেই routing করে। বড় নেটওয়ার্কে L3 switch ভালো, কারণ দ্রুত।

> **বাস্তব পরিস্থিতি:** HR (VLAN 10)-এর কম্পিউটার IT (VLAN 20)-এর ফাইল সার্ভারে যেতে পারছে না। দেখা গেল core switch-এ `interface vlan 20` তৈরি হয়নি, আর `ip routing` চালু নেই।

```text
! Layer 3 switch-এ SVI
Switch(config)# ip routing
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

! Router-on-a-stick
Router(config)# interface gi0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

| শব্দ | মানে |
|---|---|
| `ip routing` | L3 switch-এ routing চালু করো |
| `interface vlan 10` | VLAN 10-এর SVI, এটাই ওই VLAN-এর gateway |
| `no shutdown` | interface চালু করো (Cisco-তে router interface default বন্ধ থাকে) |
| `gi0/0.10` | `.10` = sub-interface, VLAN 10-এর জন্য |
| `encapsulation dot1Q 10` | এই sub-interface VLAN 10-এর tag বুঝবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Inter-VLAN routing-এর উপায় কী কী?
**উত্তর:** Router-on-a-stick (sub-interface + 802.1Q), Layer 3 switch-এ SVI, আর পুরনো পদ্ধতিতে প্রতিটি VLAN-এর জন্য আলাদা router port।

### STP: Switching loop প্রতিরোধ

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **STP** | **S**panning **T**ree **P**rotocol → শাখা-বিস্তৃত গাছ protocol |
| **RSTP** | **R**apid **S**panning **T**ree **P**rotocol (802.1w) → দ্রুত STP |
| **BPDU** | **B**ridge **P**rotocol **D**ata **U**nit → STP-এর বার্তা |
| **BID** | **B**ridge **ID** → switch-এর পরিচয় = priority + MAC |
| **PVST+** | **P**er-**V**LAN **S**panning **T**ree **P**lus → প্রতি VLAN-এ আলাদা STP (Cisco) |

**সহজ ভাষায়:** Redundancy-র জন্য switch গুলোর মধ্যে একাধিক পথ রাখা হয়; কিন্তু Layer 2 frame-এ TTL নেই, তাই loop হলে broadcast অনন্তকাল ঘুরতে থাকে (broadcast storm) আর কয়েক সেকেন্ডে পুরো নেটওয়ার্ক অচল। STP প্রতিটি loop-এর একটা port block করে দেয়, মূল পথ ভাঙলে সেটা খুলে দেয়। প্রথমে সবচেয়ে কম Bridge ID-এর switch root bridge নির্বাচিত হয়, তারপর বাকিরা root-এ যাওয়ার সবচেয়ে কম খরচের পথ রাখে।

> **বাস্তব পরিস্থিতি:** কেউ মিটিং রুমে একটা ছোট unmanaged switch-এর দুটো port একই তারের দুই মাথায় লাগিয়ে দিয়েছে। পুরো ফ্লোরের নেটওয়ার্ক বসে গেছে, switch-এর সব light পাগলের মতো জ্বলছে। STP চালু থাকলে আর access port-এ BPDU Guard থাকলে ওই port সাথে সাথে বন্ধ হয়ে যেত।

| STP port state (802.1D) | কাজ |
|---|---|
| Blocking | data পাঠায় না, শুধু BPDU শোনে (loop এড়াতে) |
| Listening | root ও পথ নির্বাচন, ১৫ সেকেন্ড |
| Learning | MAC শেখে, data পাঠায় না, ১৫ সেকেন্ড |
| Forwarding | স্বাভাবিক কাজ |
| Disabled | admin বন্ধ |

| বৈশিষ্ট্য | কাজ |
|---|---|
| Root bridge | সবচেয়ে কম Bridge ID (default priority 32768 + MAC)। Priority কমিয়ে নিজে ঠিক করে দাও |
| Port roles | Root port (root-এর দিকে সেরা পথ), Designated port (segment-এর সেরা), Blocked/Alternate |
| PortFast | PC-এর port সরাসরি forwarding-এ যায়, ৩০ সেকেন্ড অপেক্ষা নেই |
| BPDU Guard | PortFast port-এ BPDU এলে (মানে কেউ switch লাগিয়েছে) port বন্ধ করে দাও |
| RSTP | কয়েক সেকেন্ডে converge, পুরনো STP-তে ৩০-৫০ সেকেন্ড লাগত |

```text
Switch(config)# spanning-tree vlan 10 priority 4096
Switch(config-if)# spanning-tree portfast
Switch(config-if)# spanning-tree bpduguard enable
Switch# show spanning-tree vlan 10
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** STP কেন দরকার?
**উত্তর:** Layer 2-এ redundant link থাকলে broadcast storm, MAC table instability আর duplicate frame হয়। STP একটা loop-free পথ রাখে, বাকি port block করে।

**প্রশ্ন:** Root bridge কীভাবে নির্বাচিত হয়?
**উত্তর:** সবচেয়ে কম Bridge ID (priority + MAC)। Priority সমান হলে সবচেয়ে কম MAC।

**প্রশ্ন:** PortFast আর BPDU Guard কেন একসাথে ব্যবহার হয়?
**উত্তর:** PortFast end device-এর port দ্রুত চালু করে; BPDU Guard নিশ্চিত করে ওই port-এ কেউ switch লাগালে port বন্ধ হবে, loop হবে না।

> 💡 **প্রো টিপ:** নিজের core switch-কে root bridge বানাতে priority কমিয়ে দাও; নইলে সবচেয়ে পুরনো (কম MAC) কোনো সস্তা switch root হয়ে বসতে পারে।

### EtherChannel (Link Aggregation)

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **LACP** | **L**ink **A**ggregation **C**ontrol **P**rotocol (802.3ad) → link একত্রীকরণের open standard |
| **PAgP** | **P**ort **Ag**gregation **P**rotocol → Cisco-এর নিজস্ব |
| **LAG** | **L**ink **A**ggregation **G**roup → একত্রিত link-এর দল |

**সহজ ভাষায়:** কয়েকটা physical link-কে একটা logical link বানানো; ফলে bandwidth যোগ হয় (দুটো 10G = 20G), একটা তার কাটলেও চলে, আর STP পুরোটাকে একটা link ধরে, তাই কোনো port block হয় না। একটা flow সাধারণত একটা link-ই ব্যবহার করে (hash অনুযায়ী)।

> **বাস্তব পরিস্থিতি:** Core আর distribution switch-এর মাঝে একটা 10G link প্রায়ই ভরে যাচ্ছে; দ্বিতীয় তার লাগালে STP সেটা block করবে। LACP দিয়ে দুটোকে একসাথে Port-channel বানালে দুটোই কাজ করবে।

```text
Switch(config)# interface range gi0/1 - 2
Switch(config-if-range)# channel-group 1 mode active
Switch# show etherchannel summary
```

| mode | মানে |
|---|---|
| active | LACP, নিজে থেকে আলোচনা শুরু করে |
| passive | LACP, অন্যপক্ষ শুরু করলে যোগ দেয় (দুপাশে passive হলে হবে না) |
| desirable / auto | PAgP-এর সমতুল্য |
| on | কোনো আলোচনা নেই, জোর করে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** দুটো 1G link দিয়ে EtherChannel করলে একটা ফাইল কপি কি 2G গতি পাবে?
**উত্তর:** সাধারণত না; একটা flow hash অনুযায়ী একটা link-এ যায়, তাই সর্বোচ্চ 1G। অনেক flow মিলে মোট 2G ব্যবহার হয়।

---

## ০৬. Transport Layer: TCP, UDP ও Port

কোন অ্যাপ কোন port-এ, TCP কীভাবে নির্ভরযোগ্যতা দেয়: firewall, load balancer আর troubleshooting-এর ভিত্তি।

### TCP বনাম UDP

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **TCP** | **T**ransmission **C**ontrol **P**rotocol → নিয়ন্ত্রিত, নির্ভরযোগ্য প্রেরণ |
| **UDP** | **U**ser **D**atagram **P**rotocol → সরল, দ্রুত প্রেরণ |

**সহজ ভাষায়:** TCP connection-oriented: আগে সংযোগ স্থাপন করে, প্রতিটি অংশ পৌঁছানো নিশ্চিত করে (ACK), হারালে আবার পাঠায়, ক্রম ঠিক রাখে। UDP connectionless: শুধু পাঠিয়ে দেয়, পৌঁছাল কিনা দেখে না; তাই UDP দ্রুত আর হালকা।

> **বাস্তব পরিস্থিতি:** ব্যাংকের লেনদেন বা ফাইল ডাউনলোডে একটা byte-ও হারানো চলবে না: TCP। Video call-এ একটা ফ্রেম হারালে আবার পাঠানোর মানে নেই, সময় চলে গেছে: UDP। DNS প্রশ্ন ছোট আর দ্রুত দরকার: UDP 53 (বড় উত্তর বা zone transfer-এ TCP 53)।

| বিষয় | TCP | UDP |
|---|---|---|
| সংযোগ | আগে handshake | নেই |
| নির্ভরযোগ্যতা | ACK, retransmission | নেই |
| ক্রম | sequence number দিয়ে ঠিক রাখে | রাখে না |
| গতি | তুলনামূলক ধীর | দ্রুত |
| Header | 20-60 byte | 8 byte |
| Flow/congestion control | আছে | নেই |
| ব্যবহার | HTTP(S), SSH, FTP, SMTP, DB | DNS, DHCP, VoIP, video, gaming, NTP, SNMP |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** TCP আর UDP-এর পার্থক্য কী? প্রতিটির উদাহরণ দাও।
**উত্তর:** টেবিলটাই উত্তর; উদাহরণ: TCP = HTTPS, SSH; UDP = DNS, VoIP।

**প্রশ্ন:** DNS কোন protocol ব্যবহার করে?
**উত্তর:** সাধারণ query-তে UDP 53, বড় response আর zone transfer-এ TCP 53।

**প্রশ্ন:** QUIC / HTTP/3 কী?
**উত্তর:** Google-এর তৈরি UDP ভিত্তিক transport, TCP-এর মতো নির্ভরযোগ্যতা আর TLS 1.3 একসাথে, কম latency-তে।

### TCP 3-way handshake ও connection বন্ধ

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SYN** | **Syn**chronize → সংযোগ শুরুর অনুরোধ |
| **ACK** | **Ack**nowledgement → প্রাপ্তি স্বীকার |
| **FIN** | **Fin**ish → শেষ করো |
| **RST** | **R**e**s**e**t** → জোর করে বন্ধ |
| **ISN** | **I**nitial **S**equence **N**umber → শুরুর ক্রমিক নম্বর |

**সহজ ভাষায়:** TCP সংযোগ শুরু হয় তিন ধাপে: client SYN পাঠায়, server SYN-ACK দেয়, client ACK দেয়। বন্ধ হয় চার ধাপে: FIN, ACK, FIN, ACK। হঠাৎ বন্ধ করতে RST পাঠানো হয়।

> **বাস্তব পরিস্থিতি:** Firewall-এর পেছনের সার্ভারে connection timeout হচ্ছে। tcpdump চালিয়ে দেখলে client-এর SYN আসছে কিন্তু SYN-ACK যাচ্ছে না: সার্ভার listen করছে না বা local firewall drop করছে। আবার SYN পাঠাতেই RST ফেরত এলে বুঝবে port-এ কেউ নেই (connection refused)।

```text
Client                              Server
  | ---- SYN (seq=x) -----------------> |
  | <--- SYN-ACK (seq=y, ack=x+1) ----- |
  | ---- ACK (ack=y+1) ---------------> |
           সংযোগ স্থাপিত (ESTABLISHED)

বন্ধ: FIN -> ACK -> FIN -> ACK (যে আগে বন্ধ করে সে TIME_WAIT-এ থাকে)
```

| TCP flag | পূর্ণরূপ ও কাজ |
|---|---|
| SYN | Synchronize: সংযোগ শুরু |
| ACK | Acknowledgement: পেয়েছি |
| FIN | Finish: আর পাঠাব না |
| RST | Reset: সংযোগ বাতিল |
| PSH | Push: এখনই অ্যাপকে দাও |
| URG | Urgent: জরুরি data |

| TCP state | মানে |
|---|---|
| LISTEN | server অপেক্ষা করছে |
| SYN_SENT / SYN_RECV | handshake চলছে |
| ESTABLISHED | সংযোগ চালু |
| TIME_WAIT | বন্ধের পর কিছুক্ষণ অপেক্ষা, দেরিতে আসা packet সামলাতে |
| CLOSE_WAIT | অন্যপক্ষ বন্ধ করেছে, কিন্তু আমার অ্যাপ এখনও close করেনি (অ্যাপের bug-এর লক্ষণ) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** TCP 3-way handshake ব্যাখ্যা করো।
**উত্তর:** SYN, SYN-ACK, ACK। উদ্দেশ্য: দুপক্ষের initial sequence number বিনিময় আর দুপক্ষ প্রস্তুত কিনা নিশ্চিত করা।

**প্রশ্ন:** অনেক CLOSE_WAIT দেখলে কী বুঝবে?
**উত্তর:** অ্যাপ্লিকেশন remote পক্ষের বন্ধ করা connection নিজে close করছে না; সাধারণত কোডে connection leak।

**প্রশ্ন:** SYN flood attack কী?
**উত্তর:** Attacker অনেক SYN পাঠায় কিন্তু শেষ ACK দেয় না, server-এর half-open connection queue ভরে যায়। প্রতিরোধ: SYN cookies, rate limiting, DDoS protection।

### Port নম্বর: কোন সার্ভিস কোন port-এ

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Port** | একই IP-তে আলাদা অ্যাপ চেনার নম্বর |
| **Socket** | IP + Port + Protocol → একটা সংযোগের পূর্ণ ঠিকানা |

**সহজ ভাষায়:** IP ঠিক করে কোন যন্ত্র, port ঠিক করে সেই যন্ত্রের কোন অ্যাপ। Port 0-65535। 0-1023 well-known (সাধারণ সার্ভিস), 1024-49151 registered, 49152-65535 dynamic/ephemeral (client-এর অস্থায়ী port)।

> **বাস্তব পরিস্থিতি:** নতুন web server বসিয়েছ কিন্তু বাইরে থেকে খুলছে না। প্রথম প্রশ্ন: firewall/security group-এ 80 আর 443 খোলা কি? SSH-এর জন্য 22 শুধু অফিসের IP থেকে খোলা রাখো। এই port গুলো মুখস্থ না থাকলে প্রতিটি rule-এর আগে খুঁজতে হবে।

| Port | Protocol | সার্ভিস (পূর্ণরূপ) |
|---|---|---|
| 20, 21 | TCP | FTP (File Transfer Protocol): 21 control, 20 data |
| 22 | TCP | SSH (Secure Shell), SFTP, SCP |
| 23 | TCP | Telnet (এনক্রিপশন নেই, ব্যবহার করো না) |
| 25 / 587 | TCP | SMTP (Simple Mail Transfer Protocol), 587 = submission |
| 53 | UDP/TCP | DNS (Domain Name System) |
| 67 / 68 | UDP | DHCP server / client |
| 69 | UDP | TFTP (Trivial FTP), যন্ত্রের firmware/config |
| 80 | TCP | HTTP (HyperText Transfer Protocol) |
| 110 / 995 | TCP | POP3 (Post Office Protocol v3) / SSL সহ |
| 123 | UDP | NTP (Network Time Protocol) |
| 143 / 993 | TCP | IMAP (Internet Message Access Protocol) / SSL সহ |
| 161 / 162 | UDP | SNMP (Simple Network Management Protocol) / trap |
| 179 | TCP | BGP (Border Gateway Protocol) |
| 389 / 636 | TCP | LDAP (Lightweight Directory Access Protocol) / LDAPS |
| 443 | TCP (UDP = QUIC) | HTTPS (HTTP Secure) |
| 445 | TCP | SMB (Server Message Block), Windows file share |
| 514 | UDP | Syslog |
| 1812 / 1813 | UDP | RADIUS auth / accounting |
| 3306 / 5432 | TCP | MySQL / PostgreSQL |
| 3389 | TCP | RDP (Remote Desktop Protocol) |
| 49 | TCP | TACACS+ |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** SSH, HTTPS, DNS, RDP-এর port কত?
**উত্তর:** 22, 443, 53, 3389।

**প্রশ্ন:** Ephemeral port কী?
**উত্তর:** Client যখন সংযোগ করে, OS তাকে একটা অস্থায়ী উঁচু port দেয় (Linux-এ সাধারণত 32768-60999); response সেই port-এ ফেরত আসে।

**প্রশ্ন:** OSPF-এর port কত?
**উত্তর:** OSPF port ব্যবহার করে না; এটা সরাসরি IP-এর উপর protocol number 89। একইভাবে ICMP protocol 1, TCP 6, UDP 17।

> 💡 **প্রো টিপ:** শেষ প্রশ্নটা একটা জনপ্রিয় ফাঁদ: সবকিছুর port থাকে না।

### TCP Windowing, Flow ও Congestion control

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **RWND** | **R**eceive **W**i**nd**ow → গ্রহণকারী কতটা নিতে পারবে |
| **CWND** | **C**ongestion **W**i**nd**ow → নেটওয়ার্ক কতটা নিতে পারবে বলে অনুমান |
| **MSS** | **M**aximum **S**egment **S**ize → এক segment-এ সর্বোচ্চ data |
| **BDP** | **B**andwidth-**D**elay **P**roduct → পথে একসাথে কতটা data থাকতে পারে |

**সহজ ভাষায়:** TCP প্রতিটি byte-এর জন্য আলাদা ACK-এর অপেক্ষা করে না; একটা window পরিমাণ data একসাথে পাঠায়। Flow control (receive window) নিশ্চিত করে গ্রহণকারী যেন ডুবে না যায়। Congestion control (slow start, congestion avoidance) নিশ্চিত করে নেটওয়ার্ক যেন জ্যামে না পড়ে; packet loss হলে window ছোট হয়।

> **বাস্তব পরিস্থিতি:** ঢাকা থেকে আমেরিকার সার্ভারে 1 Gbps লাইনেও একটা ফাইল কপি মাত্র 50 Mbps-এ চলছে। কারণ latency 250 ms আর TCP window ছোট। Throughput ≈ window / RTT। Window scaling ঠিক থাকলে বা একসাথে কয়েকটা connection ব্যবহার করলে (`iperf3 -P`) গতি বাড়ে।

```text
Throughput (সর্বোচ্চ) ≈ Window size / RTT
64 KB window, 250 ms RTT -> 65535*8 / 0.25 ≈ 2 Mbps
BDP = 1 Gbps x 0.25 s = 31 MB window দরকার
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Flow control আর congestion control-এর পার্থক্য?
**উত্তর:** Flow control গ্রহণকারীকে রক্ষা করে (receive window)। Congestion control নেটওয়ার্ককে রক্ষা করে (congestion window, slow start)।

**প্রশ্ন:** MTU আর MSS-এর পার্থক্য?
**উত্তর:** MTU = Layer 2 frame-এ সর্বোচ্চ IP packet (1500)। MSS = TCP data অংশ = MTU - IP header (20) - TCP header (20) = 1460।

---

## ০৭. জরুরি নেটওয়ার্ক সার্ভিস: DNS, DHCP, NAT, NTP, ICMP, HTTP/TLS

এই সার্ভিসগুলো ছাড়া ইন্টারনেট চলে না; সবচেয়ে বেশি incident এদের ঘিরেই হয়।

### DNS: নাম থেকে IP

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **DNS** | **D**omain **N**ame **S**ystem → ডোমেইন নাম ব্যবস্থা |
| **FQDN** | **F**ully **Q**ualified **D**omain **N**ame → পূর্ণ ডোমেইন নাম |
| **TTL** | **T**ime **T**o **L**ive → কতক্ষণ cache-এ থাকবে |
| **TLD** | **T**op-**L**evel **D**omain → `.com`, `.bd`-এর মতো শেষ অংশ |
| **NS** | **N**ame **S**erver → নাম সার্ভার |

**সহজ ভাষায়:** মানুষ নাম মনে রাখে (google.com), যন্ত্র IP চেনে; DNS হলো ইন্টারনেটের ফোনবুক। তোমার কম্পিউটার প্রথমে নিজের cache আর hosts ফাইল দেখে, তারপর recursive resolver (ISP বা 8.8.8.8)-কে জিজ্ঞেস করে। Resolver ধাপে ধাপে root server, তারপর TLD (.com) server, তারপর ওই domain-এর authoritative server থেকে উত্তর এনে দেয়।

> **বাস্তব পরিস্থিতি:** কোম্পানির ওয়েবসাইট নতুন সার্ভারে নিয়ে A record বদলেছ; কিছু user নতুনটা পাচ্ছে, কিছু পুরনোটা। কারণ পুরনো record-এর TTL ছিল ২৪ ঘণ্টা, resolver গুলো cache রেখে দিয়েছে। শিক্ষা: migration-এর আগে TTL কমিয়ে নাও।

| Record | পূর্ণরূপ | কাজ |
|---|---|---|
| A | Address | নাম থেকে IPv4 |
| AAAA | quad-A | নাম থেকে IPv6 |
| CNAME | Canonical Name | একটা নামকে অন্য নামের alias (www → example.com) |
| MX | Mail eXchange | এই domain-এর mail server |
| NS | Name Server | এই domain-এর authoritative server কারা |
| TXT | Text | যাচাই আর email নিরাপত্তা (SPF, DKIM, DMARC) |
| PTR | Pointer | উল্টো: IP থেকে নাম (reverse DNS) |
| SOA | Start Of Authority | zone-এর মূল তথ্য, serial নম্বর |
| SRV | Service | কোন সার্ভিস কোন host:port-এ |

```bash
dig example.com +short
dig @8.8.8.8 example.com MX
dig -x 8.8.8.8                  # -x = reverse lookup (PTR)
nslookup example.com
ipconfig /flushdns              # Windows DNS cache মোছো
resolvectl flush-caches         # Linux (systemd-resolved)
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** DNS resolution-এর ধাপগুলো বলো।
**উত্তর:** Browser/OS cache, hosts ফাইল, recursive resolver, root server, TLD server, authoritative server, উত্তর cache হয়ে ফেরত।

**প্রশ্ন:** Recursive আর iterative query-এর পার্থক্য?
**উত্তর:** Recursive: client বলে "পুরো উত্তর এনে দাও" (client থেকে resolver)। Iterative: server বলে "আমি জানি না, ওকে জিজ্ঞেস করো" (resolver থেকে root/TLD)।

**প্রশ্ন:** CNAME root domain (example.com)-এ দেওয়া যায় কি?
**উত্তর:** সাধারণ নিয়মে না, কারণ root-এ SOA/NS record থাকে আর CNAME অন্য record-এর সাথে থাকতে পারে না। অনেক provider ALIAS/ANAME দিয়ে এটা সমাধান করে।

> 💡 **প্রো টিপ:** "It's always DNS" কথাটা নেটওয়ার্ক জগতে কৌতুক হলেও প্রায়ই সত্য। Troubleshooting-এ DNS সবসময় আগে যাচাই করো।

### DHCP: স্বয়ংক্রিয় IP প্রদান (DORA)

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **DHCP** | **D**ynamic **H**ost **C**onfiguration **P**rotocol → গতিশীল host কনফিগারেশন protocol |
| **DORA** | **D**iscover, **O**ffer, **R**equest, **A**cknowledge → DHCP-এর ৪ ধাপ |
| **Lease** | IP ভাড়ার মেয়াদ |

**সহজ ভাষায়:** DHCP যন্ত্রকে স্বয়ংক্রিয়ভাবে IP, subnet mask, default gateway, DNS server আর lease সময় দেয়। চার ধাপ: Discover (client broadcast-এ খোঁজে), Offer (server একটা IP প্রস্তাব করে), Request (client বলে আমি এটা চাই), Acknowledge (server নিশ্চিত করে)।

> **বাস্তব পরিস্থিতি:** নতুন ফ্লোরের সব PC-তে `169.254.x.x` আসছে। ওই ফ্লোরের VLAN আলাদা, আর DHCP server অন্য VLAN-এ। Broadcast router পার হয় না, তাই router/L3 switch-এ DHCP relay (`ip helper-address`) সেট করতে হবে।

```text
Client                              DHCP Server
  | -- DISCOVER (broadcast) ------------> |
  | <--------------- OFFER -------------- |
  | -- REQUEST (broadcast) -------------> |
  | <--------------- ACK ---------------- |

Router(config-if)# ip helper-address 10.0.0.5     ! DHCP relay
ipconfig /release && ipconfig /renew              (Windows)
sudo dhclient -r eth0 && sudo dhclient eth0       (Linux: -r = release)
```

| বিষয় | মানে |
|---|---|
| Scope / Pool | কোন রেঞ্জ থেকে IP দেবে |
| Exclusion | রেঞ্জের যে IP দেওয়া যাবে না (server, printer-এর static IP) |
| Reservation | নির্দিষ্ট MAC সবসময় একই IP পাবে |
| Lease time | কতদিনের জন্য; অর্ধেক সময়ে (T1 = 50%) client নবায়ন চায় |
| `ip helper-address` | broadcast DHCP-কে unicast করে অন্য নেটওয়ার্কের server-এ পাঠায় (relay) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** DORA ব্যাখ্যা করো।
**উত্তর:** Discover, Offer, Request, Acknowledge। Discover আর Request broadcast, কারণ client-এর তখনও IP নেই।

**প্রশ্ন:** DHCP server অন্য subnet-এ থাকলে কী করবে?
**উত্তর:** Router বা L3 switch-এ DHCP relay agent (Cisco-তে `ip helper-address`) সেট করবে।

**প্রশ্ন:** DHCP কোন port ব্যবহার করে?
**উত্তর:** UDP 67 (server) আর 68 (client)।

### NAT ও PAT

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **NAT** | **N**etwork **A**ddress **T**ranslation → নেটওয়ার্ক ঠিকানা রূপান্তর |
| **PAT** | **P**ort **A**ddress **T**ranslation → port দিয়ে ঠিকানা রূপান্তর (NAT overload) |
| **SNAT / DNAT** | **S**ource NAT / **D**estination NAT → উৎস / গন্তব্য ঠিকানা বদল |

**সহজ ভাষায়:** Private IP ইন্টারনেটে যেতে পারে না। NAT router ভেতরের private IP-কে নিজের public IP দিয়ে বদলে দেয়, আর একটা টেবিলে মনে রাখে কে কোন সংযোগ করেছে, যাতে উত্তর ঠিক জনের কাছে ফেরত যায়। PAT-এ হাজার হাজার যন্ত্র একটা public IP শেয়ার করে, আলাদা port নম্বর দিয়ে চেনা হয়। Port forwarding (DNAT) উল্টো কাজ: বাইরের নির্দিষ্ট port-কে ভেতরের নির্দিষ্ট সার্ভারে পাঠায়।

> **বাস্তব পরিস্থিতি:** অফিসের ২০০ জন একটা public IP দিয়ে ইন্টারনেট চালায় (PAT)। আবার অফিসের CCTV NVR বাইরে থেকে দেখতে router-এ port 8443-এর forward দিলে NVR-এর `192.168.1.50:443`-এ (DNAT)। ক্লাউডে private subnet-এর সার্ভার update নামাতে NAT Gateway ব্যবহার করে।

| ধরন | কাজ |
|---|---|
| Static NAT | একটা private IP সবসময় একটা public IP (১:১), বাইরে থেকে পৌঁছানো যায় |
| Dynamic NAT | public IP-এর pool থেকে যে খালি পায় |
| PAT / Overload | অনেক private IP একটা public IP, port দিয়ে আলাদা (সবচেয়ে প্রচলিত) |
| Port forwarding | বাইরের port → ভেতরের IP:port |

```text
Inside local 192.168.1.10:51000 ---> Inside global 103.10.20.30:40001 ---> 8.8.8.8:53
Inside local 192.168.1.11:51000 ---> Inside global 103.10.20.30:40002
Router# show ip nat translations
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** NAT আর PAT-এর পার্থক্য?
**উত্তর:** NAT IP বদলায় (সাধারণত ১:১ বা pool)। PAT IP আর port দুটোই বদলায়, তাই অনেক যন্ত্র একটা public IP শেয়ার করতে পারে।

**প্রশ্ন:** Inside local, inside global কী?
**উত্তর:** Inside local = ভেতরের যন্ত্রের আসল private IP। Inside global = বাইরে থেকে যে public IP দেখা যায়।

**প্রশ্ন:** NAT-এর অসুবিধা কী?
**উত্তর:** End-to-end সংযোগ ভাঙে, বাইরে থেকে সরাসরি পৌঁছানো যায় না, কিছু protocol (SIP, IPsec)-এ বাড়তি সমাধান (NAT-T, ALG) লাগে।

### ICMP, ping ও traceroute কীভাবে কাজ করে

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ICMP** | **I**nternet **C**ontrol **M**essage **P**rotocol → ইন্টারনেট নিয়ন্ত্রণ বার্তা protocol |
| **TTL** | **T**ime **T**o **L**ive → packet কতগুলো router পার হতে পারবে |

**সহজ ভাষায়:** ICMP হলো নেটওয়ার্কের বার্তা ও error জানানোর protocol (IP protocol 1)। ping পাঠায় Echo Request (type 8), উত্তর আসে Echo Reply (type 0)। traceroute TTL 1, 2, 3 এভাবে বাড়িয়ে packet পাঠায়; প্রতিটি router TTL 0 হলে packet ফেলে "Time Exceeded" (type 11) ফেরত দেয়, এভাবে পথের প্রতিটি router চেনা যায়।

> **বাস্তব পরিস্থিতি:** Server টিম বলছে "ping যাচ্ছে না, তাই সার্ভার down"। কিন্তু curl দিয়ে সাইট খুলছে; কারণ firewall ICMP ব্লক করেছে। আবার পুরো ICMP ব্লক করলে Path MTU Discovery ভেঙে যায় (Fragmentation Needed বার্তা আসে না), ফলে বড় packet হারিয়ে যায় আর অদ্ভুত সমস্যা হয়।

| ICMP type | মানে |
|---|---|
| 0 | Echo Reply (ping-এর উত্তর) |
| 3 | Destination Unreachable (code 3 = port unreachable, code 4 = fragmentation needed) |
| 8 | Echo Request (ping) |
| 11 | Time Exceeded (TTL শেষ, traceroute-এ ব্যবহার) |
| 5 | Redirect |

```bash
ping -c 4 8.8.8.8                # c = count
ping -s 1472 -M do 8.8.8.8       # s = size, M do = Don't Fragment (MTU পরীক্ষা)
traceroute -n 8.8.8.8            # n = numeric, নাম খুঁজবে না
tracert 8.8.8.8                  # Windows
mtr -rwc 50 8.8.8.8              # r = report, w = wide, c = count
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** traceroute কীভাবে কাজ করে?
**উত্তর:** TTL এক এক করে বাড়িয়ে packet পাঠায়; প্রতিটি hop-এর router TTL শেষ হলে ICMP Time Exceeded পাঠায়, তা থেকে সেই router-এর IP জানা যায়।

**প্রশ্ন:** Linux আর Windows traceroute-এর পার্থক্য?
**উত্তর:** Linux traceroute default-এ UDP পাঠায়, Windows tracert ICMP Echo পাঠায়।

**প্রশ্ন:** ping 1472 byte দিয়ে MTU পরীক্ষা কেন?
**উত্তর:** 1472 data + 8 ICMP header + 20 IP header = 1500 (Ethernet MTU)।

### NTP: সময় মেলানো

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **NTP** | **N**etwork **T**ime **P**rotocol → নেটওয়ার্ক সময় protocol |
| **Stratum** | (স্তর) → সঠিক উৎস থেকে কত ধাপ দূরে |

**সহজ ভাষায়:** সব যন্ত্রের ঘড়ি মেলাতে NTP (UDP 123) ব্যবহার হয়। Stratum 0 হলো atomic clock/GPS, stratum 1 সরাসরি তাদের সাথে যুক্ত server, এভাবে নিচে নামে। সময় না মিললে log মেলানো যায় না, আর TLS certificate, Kerberos, 2FA-এর মতো নিরাপত্তা ব্যবস্থা ভেঙে পড়ে।

> **বাস্তব পরিস্থিতি:** Active Directory-তে কিছু PC login করতে পারছে না। দেখা গেল ওদের ঘড়ি ৭ মিনিট পিছিয়ে, আর Kerberos ৫ মিনিটের বেশি পার্থক্য মানে না। NTP ঠিক করতেই সমাধান।

```bash
timedatectl
chronyc tracking
show ntp status            ! Cisco
w32tm /query /status       ! Windows
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** NTP কোন port, আর কেন জরুরি?
**উত্তর:** UDP 123। Log correlation, certificate validity, Kerberos authentication, distributed system-এর জন্য সঠিক সময় অপরিহার্য।

### HTTP, HTTPS ও TLS handshake

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **HTTP** | **H**yper**T**ext **T**ransfer **P**rotocol → ওয়েবের protocol |
| **HTTPS** | HTTP **S**ecure → এনক্রিপ্টেড HTTP |
| **TLS** | **T**ransport **L**ayer **S**ecurity → পরিবহন স্তরের নিরাপত্তা |
| **SSL** | **S**ecure **S**ockets **L**ayer → TLS-এর পুরনো নাম |
| **CA** | **C**ertificate **A**uthority → certificate প্রদানকারী বিশ্বস্ত সংস্থা |
| **SNI** | **S**erver **N**ame **I**ndication → কোন domain চাই জানানো |

**সহজ ভাষায়:** HTTP হলো request-response protocol: browser GET/POST পাঠায়, server status code সহ উত্তর দেয়। HTTPS = HTTP + TLS। TLS handshake-এ server তার certificate দেখায় (পরিচয় প্রমাণ), দুপক্ষ মিলে একটা গোপন session key তৈরি করে, তারপর সব data এনক্রিপ্টেড যায়। TLS 1.3-এ handshake মাত্র ১ round trip।

> **বাস্তব পরিস্থিতি:** Load balancer-এর পেছনে সাইট মাঝে মাঝে 502 দিচ্ছে। 502 মানে load balancer পেছনের সার্ভার থেকে খারাপ/কোনো উত্তর পায়নি: সমস্যা backend-এ। আর 504 মানে backend সময়মতো উত্তর দেয়নি। Status code ঠিক বুঝলে অর্ধেক troubleshooting হয়ে যায়।

| Status | মানে |
|---|---|
| 200 OK | সফল |
| 301 / 302 | স্থায়ী / অস্থায়ী redirect |
| 304 | Not Modified, cache ব্যবহার করো |
| 400 | Bad Request, client-এর অনুরোধ ভুল |
| 401 | Unauthorized, login লাগবে |
| 403 | Forbidden, অনুমতি নেই |
| 404 | Not Found |
| 429 | Too Many Requests, rate limit |
| 500 | Internal Server Error, অ্যাপে bug |
| 502 | Bad Gateway, proxy/LB পেছন থেকে খারাপ উত্তর পেয়েছে |
| 503 | Service Unavailable, overload বা maintenance |
| 504 | Gateway Timeout, পেছনের সার্ভার দেরি করেছে |

| HTTP method | কাজ |
|---|---|
| GET | তথ্য আনো |
| POST | নতুন তথ্য পাঠাও |
| PUT / PATCH | পুরো / আংশিক আপডেট |
| DELETE | মোছো |
| HEAD | শুধু header |
| OPTIONS | কী কী অনুমোদিত (CORS) |

```bash
curl -v https://example.com      # v = verbose, TLS handshake দেখাও
curl -I https://example.com      # I = শুধু header
openssl s_client -connect example.com:443 -servername example.com
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** HTTP আর HTTPS-এর পার্থক্য?
**উত্তর:** HTTPS-এ TLS দিয়ে data এনক্রিপ্টেড, সার্ভারের পরিচয় certificate দিয়ে যাচাই হয় আর data পরিবর্তন ধরা পড়ে; port 80 বনাম 443।

**প্রশ্ন:** TLS handshake সংক্ষেপে বলো।
**উত্তর:** Client Hello (সমর্থিত cipher, SNI), Server Hello + certificate, key exchange (ECDHE), দুপক্ষ session key বানায়, Finished, তারপর এনক্রিপ্টেড data।

**প্রশ্ন:** 502 আর 504-এর পার্থক্য?
**উত্তর:** 502 = gateway পেছনের সার্ভার থেকে অবৈধ উত্তর পেয়েছে বা সংযোগ ভেঙেছে; 504 = পেছনের সার্ভার নির্দিষ্ট সময়ে উত্তর দেয়নি।

**প্রশ্ন:** Symmetric আর asymmetric encryption TLS-এ কোথায় ব্যবহার হয়?
**উত্তর:** Handshake-এ asymmetric (public/private key) পরিচয় আর key exchange-এর জন্য; তারপর data এনক্রিপশনে symmetric (AES), কারণ দ্রুত।

### অন্যান্য সার্ভিস: SNMP, Syslog, FTP/SFTP, Email

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **SNMP** | **S**imple **N**etwork **M**anagement **P**rotocol → সহজ নেটওয়ার্ক ব্যবস্থাপনা protocol |
| **MIB** | **M**anagement **I**nformation **B**ase → যন্ত্রের তথ্যের কাঠামো |
| **OID** | **O**bject **ID**entifier → প্রতিটি তথ্যের নম্বর |
| **SFTP** | **S**SH **F**ile **T**ransfer **P**rotocol → SSH-এর মাধ্যমে ফাইল আদানপ্রদান |
| **FTPS** | FTP **S**ecure → TLS সহ FTP |

**সহজ ভাষায়:** SNMP দিয়ে monitoring system (Zabbix, PRTG, LibreNMS) router/switch থেকে CPU, interface traffic, error গুলো পড়ে (UDP 161), আর যন্ত্র নিজে সমস্যা হলে trap পাঠায় (UDP 162)। Syslog দিয়ে সব যন্ত্র তাদের log একটা কেন্দ্রীয় server-এ পাঠায় (UDP 514)। Email পাঠাতে SMTP, পড়তে IMAP (server-এ রাখে) বা POP3 (নামিয়ে নেয়)।

> **বাস্তব পরিস্থিতি:** Core switch-এর একটা uplink রাতে ১০ মিনিট down হয়েছিল কিন্তু কেউ জানে না। SNMP trap আর syslog কেন্দ্রীয়ভাবে সংগ্রহ করলে ঠিক সময় আর কারণ জানা যেত, alert-ও আসত।

| বিষয় | ব্যাখ্যা |
|---|---|
| SNMPv1/v2c | community string (প্রায় password) সাধারণ লেখায় যায়, অনিরাপদ |
| SNMPv3 | authentication আর encryption আছে, এটা ব্যবহার করো |
| Syslog severity | 0 Emergency, 1 Alert, 2 Critical, 3 Error, 4 Warning, 5 Notice, 6 Informational, 7 Debug |
| FTP | এনক্রিপশন নেই, active/passive mode-এর কারণে firewall-এ ঝামেলা |
| SFTP | SSH (port 22)-এর উপর, নিরাপদ, একটা port |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** SNMP-এর version গুলোর পার্থক্য?
**উত্তর:** v1/v2c community string দিয়ে চলে, এনক্রিপশন নেই; v3-তে username ভিত্তিক authentication আর encryption আছে।

**প্রশ্ন:** IMAP আর POP3-এর পার্থক্য?
**উত্তর:** IMAP mail server-এ রেখে সব ডিভাইসে sync করে; POP3 সাধারণত নামিয়ে server থেকে মুছে দেয়।

**প্রশ্ন:** Syslog severity 0 আর 7 কী?
**উত্তর:** 0 = Emergency (সবচেয়ে গুরুতর), 7 = Debug।

---

## ০৮. Routing: Static, Dynamic, OSPF, EIGRP, BGP

Router কীভাবে পথ বেছে নেয়; network engineer পদের interview-এর সবচেয়ে গভীর অংশ।

### Routing table, Default gateway ও Longest Prefix Match

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **RIB** | **R**outing **I**nformation **B**ase → routing table |
| **FIB** | **F**orwarding **I**nformation **B**ase → দ্রুত forward করার জন্য hardware টেবিল |
| **LPM** | **L**ongest **P**refix **M**atch → সবচেয়ে লম্বা prefix-এর মিল |
| **Next hop** | পরের router |

**সহজ ভাষায়:** Routing table-এ লেখা থাকে কোন নেটওয়ার্কে যেতে কোন next hop বা interface দিয়ে যেতে হবে। Packet এলে router গন্তব্য IP-এর সাথে সবচেয়ে নির্দিষ্ট (সবচেয়ে লম্বা prefix) route টা বেছে নেয়। কোনো মিল না পেলে default route (`0.0.0.0/0`)-এ পাঠায়। PC-র default gateway হলো নিজের নেটওয়ার্কের বাইরে যাওয়ার দরজা।

> **বাস্তব পরিস্থিতি:** Router-এ দুটো route আছে: `10.0.0.0/8` যায় ISP-A দিয়ে, `10.1.2.0/24` যায় VPN দিয়ে। `10.1.2.50`-এর packet কোন দিকে যাবে? /24 বেশি নির্দিষ্ট, তাই VPN দিয়ে। এই নিয়ম না জানলে route যোগ করে অবাক হবে কেন traffic অন্যদিকে যাচ্ছে।

```text
Router# show ip route
C   192.168.1.0/24 is directly connected, Gi0/0
S   10.20.0.0/16 [1/0] via 192.168.1.254
O   172.16.5.0/24 [110/20] via 10.0.0.2, Gi0/1
S*  0.0.0.0/0 [1/0] via 203.0.113.1
    [110/20] = [Administrative Distance / Metric]

ip route        (Linux)
route print     (Windows)
```

| Code | মানে |
|---|---|
| C | Connected: সরাসরি যুক্ত নেটওয়ার্ক |
| L | Local: router-এর নিজের IP |
| S | Static: হাতে দেওয়া |
| S* | Static default route |
| O | OSPF থেকে শেখা |
| D | EIGRP (DUAL থেকে D) |
| B | BGP |
| R | RIP |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Longest prefix match কী?
**উত্তর:** একাধিক route মিললে router সবচেয়ে লম্বা subnet mask (সবচেয়ে নির্দিষ্ট)-এর route বেছে নেয়।

**প্রশ্ন:** Default route কী আর কোথায় ব্যবহার হয়?
**উত্তর:** `0.0.0.0/0`, যখন কোনো নির্দিষ্ট route মেলে না; সাধারণত edge router-এ ISP-এর দিকে।

**প্রশ্ন:** Routing আর forwarding-এর পার্থক্য?
**উত্তর:** Routing = পথ শিখে টেবিল বানানো (control plane)। Forwarding = প্রতিটি packet-কে টেবিল দেখে সঠিক interface-এ পাঠানো (data plane)।

### Static route বনাম Dynamic routing

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **IGP** | **I**nterior **G**ateway **P**rotocol → একটা প্রতিষ্ঠানের ভেতরের routing protocol |
| **EGP** | **E**xterior **G**ateway **P**rotocol → প্রতিষ্ঠানের মধ্যে (ইন্টারনেট) routing protocol |

**সহজ ভাষায়:** Static route হাতে লেখা: সহজ, নিরাপদ, কোনো CPU খরচ নেই, কিন্তু পথ ভাঙলে নিজে বদলায় না। Dynamic routing protocol-এ router গুলো নিজেরা কথা বলে route শেখে আর সমস্যা হলে বিকল্প পথ বেছে নেয়। ছোট নেটওয়ার্ক বা ISP-এর দিকে default route-এ static; অনেক শাখা আর redundant পথে dynamic।

> **বাস্তব পরিস্থিতি:** ৩টা শাখা অফিস, প্রতিটিতে একটা পথ: static route যথেষ্ট। ৫০টা শাখা, প্রতিটিতে MPLS আর ব্যাকআপ ইন্টারনেট VPN: হাতে route রাখা অসম্ভব, OSPF বা BGP লাগবে যাতে MPLS ভাঙলে নিজে থেকে VPN-এ চলে যায়।

```text
Router(config)# ip route 10.20.0.0 255.255.0.0 192.168.1.254
Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
Router(config)# ip route 0.0.0.0 0.0.0.0 198.51.100.1 250   ! floating static (backup)
sudo ip route add 10.20.0.0/16 via 192.168.1.254           (Linux)
```

| শব্দ | মানে |
|---|---|
| `ip route নেটওয়ার্ক mask next-hop` | এই নেটওয়ার্কে যেতে এই next hop |
| `250` (শেষের সংখ্যা) | administrative distance বাড়িয়ে backup বানানো (floating static route) |
| `add ... via` | Linux-এ route যোগ, via = next hop |

| ধরন | উদাহরণ | পদ্ধতি |
|---|---|---|
| Distance vector | RIP | প্রতিবেশীর কথা শুনে, "গুজব দিয়ে routing" |
| Link state | OSPF, IS-IS | পুরো নেটওয়ার্কের মানচিত্র বানায় |
| Advanced distance vector | EIGRP | দুইয়ের মিশ্রণ |
| Path vector | BGP | AS-এর তালিকা দেখে, policy ভিত্তিক |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Floating static route কী?
**উত্তর:** বেশি administrative distance দেওয়া static route, যেটা মূল route (dynamic বা অন্য static) চলে গেলে তবেই ব্যবহার হয়; ব্যাকআপ লিংকের জন্য।

**প্রশ্ন:** Distance vector আর link state-এর পার্থক্য?
**উত্তর:** Distance vector প্রতিবেশীর কাছ থেকে দূরত্ব আর দিক শোনে, পুরো নকশা জানে না, converge ধীর। Link state-এ প্রতিটি router পুরো topology-র মানচিত্র রাখে, SPF algorithm দিয়ে পথ বের করে, দ্রুত converge করে।

### Administrative Distance ও Metric

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **AD** | **A**dministrative **D**istance → route-এর উৎসের বিশ্বাসযোগ্যতা |
| **Metric** | একই protocol-এর মধ্যে পথের খরচ |

**সহজ ভাষায়:** একই নেটওয়ার্কে যাওয়ার route যদি দুটো আলাদা protocol থেকে আসে, router কম AD যার, সেটা বিশ্বাস করে। একই protocol থেকে একাধিক পথ এলে কম metric যার সেটা নেয়। আগে প্রশ্ন হয় "কার কথা বিশ্বাস করব" (AD), তারপর "কোন পথ ছোট" (metric)।

> **বাস্তব পরিস্থিতি:** একই নেটওয়ার্ক OSPF আর EIGRP দুটো থেকেই শেখা হচ্ছে। Routing table-এ শুধু EIGRP (90)-এর route দেখাচ্ছে, OSPF (110)-এর নয়। এটা bug নয়, AD-এর নিয়ম।

| Route-এর উৎস | Default AD (Cisco) |
|---|---|
| Connected | 0 |
| Static | 1 |
| eBGP | 20 |
| EIGRP (internal) | 90 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| EIGRP (external) | 170 |
| iBGP | 200 |
| Unknown / অবিশ্বস্ত | 255 (ব্যবহার হবে না) |

| Protocol | Metric |
|---|---|
| RIP | Hop count (সর্বোচ্চ 15, 16 = unreachable) |
| OSPF | Cost = reference bandwidth / interface bandwidth |
| EIGRP | Bandwidth আর delay (default) |
| BGP | Path attributes (AS path, local pref ...) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** OSPF আর EIGRP দুটোতেই একই route থাকলে কোনটা নেওয়া হবে?
**উত্তর:** EIGRP, কারণ AD 90 < OSPF 110।

**প্রশ্ন:** AD আর metric-এর পার্থক্য?
**উত্তর:** AD আলাদা protocol-এর মধ্যে তুলনা করে; metric একই protocol-এর একাধিক পথের মধ্যে।

> 💡 **প্রো টিপ:** AD টেবিলটা মুখস্থ রাখো, প্রায় প্রতিটি routing interview-এ আসে।

### OSPF

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **OSPF** | **O**pen **S**hortest **P**ath **F**irst → উন্মুক্ত সংক্ষিপ্ততম পথ আগে |
| **LSA** | **L**ink **S**tate **A**dvertisement → link-এর অবস্থার ঘোষণা |
| **LSDB** | **L**ink **S**tate **D**ata**B**ase → পুরো topology-র তথ্যভাণ্ডার |
| **SPF** | **S**hortest **P**ath **F**irst (Dijkstra) → সংক্ষিপ্ততম পথের algorithm |
| **DR / BDR** | **D**esignated **R**outer / **B**ackup **DR** → নির্বাচিত প্রতিনিধি router |
| **ABR / ASBR** | **A**rea **B**order **R**outer / **AS** **B**oundary **R**outer → area / বাইরের সীমানার router |

**সহজ ভাষায়:** OSPF একটা open standard link-state IGP। প্রতিটি router LSA আদান-প্রদান করে পুরো area-র মানচিত্র (LSDB) বানায়, তারপর Dijkstra algorithm দিয়ে সবচেয়ে কম cost-এর পথ বের করে। বড় নেটওয়ার্ককে area-তে ভাগ করা হয়; area 0 (backbone)-এর সাথে সব area যুক্ত থাকতে হয়। Ethernet-এর মতো multi-access নেটওয়ার্কে DR/BDR নির্বাচিত হয়, যাতে সবাই সবার সাথে না, শুধু DR-এর সাথে update বিনিময় করে।

> **বাস্তব পরিস্থিতি:** দুটো router-এর OSPF neighbor হচ্ছে না, ExStart-এ আটকে আছে; কারণ দুপাশে MTU আলাদা (একপাশে 1500, অন্যপাশে 9000)। আরেকবার neighbor-ই দেখা যাচ্ছে না: দুপাশের hello/dead timer বা area নম্বর বা subnet মেলেনি।

```text
Router(config)# router ospf 1
Router(config-router)# router-id 1.1.1.1
Router(config-router)# network 10.0.0.0 0.0.0.255 area 0
Router(config-router)# passive-interface gi0/2
Router# show ip ospf neighbor
Router# show ip ospf interface brief
Router# show ip route ospf
```

| শব্দ | মানে |
|---|---|
| `router ospf 1` | 1 = process ID, শুধু এই router-এর জন্য, অন্যপাশে মিলতে হবে না |
| `router-id` | router-এর পরিচয় (IP-এর মতো লেখা) |
| `network ... 0.0.0.255 area 0` | এই রেঞ্জের interface-এ OSPF চালাও; 0.0.0.255 = wildcard mask |
| `passive-interface` | এই interface-এ hello পাঠাবে না (user-এর দিকের port) |
| Cost | 100 Mbps / interface bandwidth (default), তাই 1G আর 10G দুটোরই cost 1; reference bandwidth বাড়াতে হয় |

| Neighbor state | মানে |
|---|---|
| Down | কোনো hello পায়নি |
| Init | hello পেয়েছে, কিন্তু অন্যপাশ আমাকে দেখেনি |
| 2-Way | দুপাশে দেখা হয়েছে (DROther-দের মধ্যে এখানেই থামে) |
| ExStart / Exchange | কে আগে, তারপর database-এর সারাংশ বিনিময় |
| Loading | অনুপস্থিত LSA চাওয়া |
| Full | সম্পূর্ণ sync, স্বাস্থ্যকর |

| Neighbor হওয়ার শর্ত | |
|---|---|
| একই | area ID, subnet ও mask, hello/dead timer (Ethernet-এ 10/40 সেকেন্ড), authentication, stub flag, MTU (ExStart-এর জন্য) |
| আলাদা | router ID |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** OSPF neighbor হতে কী কী মিলতে হয়?
**উত্তর:** Area ID, subnet/mask, hello ও dead interval, authentication, stub area flag; আর Full হতে MTU। Router ID আলাদা হতে হবে।

**প্রশ্ন:** DR/BDR কেন আর কীভাবে নির্বাচন হয়?
**উত্তর:** Multi-access নেটওয়ার্কে LSA বন্যা কমাতে। সর্বোচ্চ OSPF priority (default 1), সমান হলে সর্বোচ্চ router ID। Priority 0 মানে কখনো DR হবে না; নির্বাচন preemptive নয়।

**প্রশ্ন:** OSPF area 0 কেন দরকার?
**উত্তর:** এটা backbone; inter-area traffic area 0 দিয়ে যায়, এতে loop এড়ানো যায়। সব area-কে area 0-এর সাথে যুক্ত থাকতে হয় (বা virtual link)।

**প্রশ্ন:** OSPF কোন protocol/port ব্যবহার করে?
**উত্তর:** IP protocol 89, multicast `224.0.0.5` (সব OSPF router) আর `224.0.0.6` (DR/BDR)।

### EIGRP

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **EIGRP** | **E**nhanced **I**nterior **G**ateway **R**outing **P**rotocol → উন্নত অভ্যন্তরীণ routing protocol |
| **DUAL** | **D**iffusing **U**pdate **Al**gorithm → EIGRP-এর loop-free পথ নির্বাচনের algorithm |
| **FD / RD (AD)** | **F**easible **D**istance / **R**eported (**A**dvertised) **D**istance → আমার মোট দূরত্ব / প্রতিবেশীর দূরত্ব |
| **FS** | **F**easible **S**uccessor → আগে থেকে প্রস্তুত ব্যাকআপ পথ |

**সহজ ভাষায়:** EIGRP Cisco তৈরি করেছে (এখন আংশিকভাবে open)। এটা advanced distance vector: সেরা পথকে successor বলে, আর শর্ত পূরণ করা ব্যাকআপ পথকে feasible successor হিসেবে আগেই topology table-এ রেখে দেয়; মূল পথ ভাঙলে প্রায় সাথে সাথে ব্যাকআপে চলে যায়। Unequal-cost load balancing (variance)-এর সুবিধাও আছে।

> **বাস্তব পরিস্থিতি:** পুরো Cisco নির্ভর একটা ব্যাংকের শাখা নেটওয়ার্কে EIGRP চলে। একটা WAN link কাটলে প্রায় মিলিসেকেন্ডে traffic ব্যাকআপে চলে যায়, কারণ feasible successor আগেই তৈরি ছিল।

```text
Router(config)# router eigrp 100
Router(config-router)# network 10.0.0.0 0.0.255.255
Router# show ip eigrp neighbors
Router# show ip eigrp topology
```

| শব্দ | মানে |
|---|---|
| `100` | AS নম্বর, neighbor-দের মধ্যে একই হতে হবে (OSPF process ID-এর বিপরীত) |
| Successor | সেরা পথ, routing table-এ যায় |
| Feasibility condition | প্রতিবেশীর RD < আমার বর্তমান FD হলে সেই প্রতিবেশী FS হতে পারে (loop নেই নিশ্চিত) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Successor আর feasible successor-এর পার্থক্য?
**উত্তর:** Successor = সর্বনিম্ন FD-এর সেরা পথ। Feasible successor = feasibility condition পূরণ করা ব্যাকআপ পথ, যেটা তাৎক্ষণিক ব্যবহার করা যায়।

**প্রশ্ন:** EIGRP-এর AD কত?
**উত্তর:** Internal 90, external 170, summary 5।

### BGP: ইন্টারনেটের routing protocol

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **BGP** | **B**order **G**ateway **P**rotocol → সীমানা gateway protocol |
| **AS / ASN** | **A**utonomous **S**ystem / AS **N**umber → একটা প্রতিষ্ঠানের নিজস্ব নেটওয়ার্ক ও তার নম্বর |
| **eBGP / iBGP** | external / internal BGP → আলাদা AS / একই AS-এর মধ্যে BGP |
| **MED** | **M**ulti-**E**xit **D**iscriminator → প্রতিবেশী AS-কে পছন্দের প্রবেশপথ জানানো |
| **IXP** | **I**nternet e**X**change **P**oint → ISP-দের traffic বিনিময়ের স্থান |

**সহজ ভাষায়:** BGP হলো path vector protocol যেটা ইন্টারনেটের হাজারো AS (ISP, বড় কোম্পানি, ক্লাউড)-কে যুক্ত রাখে। এটা সবচেয়ে দ্রুত পথ খোঁজে না, policy অনুযায়ী পথ বেছে নেয় (কোন ISP সস্তা, কোন পথ পছন্দ)। TCP 179-এ neighbor হয়, আর loop এড়ায় AS_PATH দেখে: নিজের AS নম্বর পথে দেখলে route গ্রহণ করে না।

> **বাস্তব পরিস্থিতি:** কোম্পানির দুটো ISP আছে (multihoming)। নিজের public prefix দুটো ISP-তেই BGP দিয়ে announce করছ। ISP-A সস্তা, তাই বাইরে যাওয়ার traffic local preference বাড়িয়ে ISP-A দিয়ে পাঠাচ্ছ; ISP-B-তে AS path prepend করে ভেতরে আসা traffic-ও ISP-A দিয়ে আনছ। ২০২১ সালে Facebook-এর বিশাল outage হয়েছিল BGP-তে তাদের route প্রত্যাহার হয়ে যাওয়ায়।

```text
Router(config)# router bgp 65001
Router(config-router)# neighbor 203.0.113.1 remote-as 64500
Router(config-router)# network 198.51.100.0 mask 255.255.255.0
Router# show ip bgp summary
Router# show ip bgp
```

| Best path নির্বাচন (Cisco, ক্রম অনুযায়ী) | বেশি না কম ভালো |
|---|---|
| 1. Weight (Cisco নিজস্ব, local) | বেশি |
| 2. Local Preference (পুরো AS-এ, বাইরে যাওয়ার পথ) | বেশি |
| 3. Locally originated | নিজের তৈরি route |
| 4. AS_PATH length | ছোট |
| 5. Origin (IGP < EGP < incomplete) | IGP ভালো |
| 6. MED (প্রতিবেশীকে প্রবেশপথ জানানো) | কম |
| 7. eBGP over iBGP | eBGP ভালো |
| 8. IGP metric to next hop | কম |
| শেষে | সবচেয়ে পুরনো route, সর্বনিম্ন router ID |

| BGP state | মানে |
|---|---|
| Idle | শুরু হয়নি |
| Connect / Active | TCP সংযোগের চেষ্টা (Active-এ আটকে থাকা = TCP হচ্ছে না) |
| OpenSent / OpenConfirm | OPEN বার্তা বিনিময় |
| Established | neighbor সফল, route আদান-প্রদান চলছে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** BGP কেন distance vector নয়, path vector?
**উত্তর:** এটা শুধু দূরত্ব নয়, পুরো AS পথের তালিকা (AS_PATH) বহন করে; সেটা দিয়ে loop প্রতিরোধ আর policy নির্ধারণ করে।

**প্রশ্ন:** eBGP আর iBGP-এর পার্থক্য?
**উত্তর:** eBGP আলাদা AS-এর মধ্যে (AD 20), সাধারণত সরাসরি যুক্ত। iBGP একই AS-এর ভেতরে (AD 200); loop এড়াতে iBGP থেকে শেখা route অন্য iBGP peer-কে পাঠায় না, তাই full mesh বা route reflector লাগে।

**প্রশ্ন:** Neighbor Active state-এ আটকে থাকলে কী সমস্যা?
**উত্তর:** TCP 179 সংযোগ হচ্ছে না: ভুল neighbor IP, routing নেই, firewall/ACL 179 আটকাচ্ছে, বা update-source/ebgp-multihop দরকার।

**প্রশ্ন:** Private ASN রেঞ্জ কী?
**উত্তর:** 64512-65534 (16-bit) আর 4200000000-4294967294 (32-bit)।

### FHRP: Gateway-এর redundancy (HSRP, VRRP, GLBP)

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **FHRP** | **F**irst **H**op **R**edundancy **P**rotocol → প্রথম hop-এর ব্যাকআপ protocol |
| **HSRP** | **H**ot **S**tandby **R**outer **P**rotocol → Cisco-এর গরম-প্রস্তুত ব্যাকআপ router |
| **VRRP** | **V**irtual **R**outer **R**edundancy **P**rotocol → open standard ভার্চুয়াল router |
| **GLBP** | **G**ateway **L**oad **B**alancing **P**rotocol → Cisco-এর load balance সহ gateway |
| **VIP** | **V**irtual **IP** → ভার্চুয়াল IP |

**সহজ ভাষায়:** PC-তে একটাই default gateway দেওয়া যায়; সেই router নষ্ট হলে সবাই অফলাইন। FHRP দুটো router-কে মিলে একটা ভার্চুয়াল IP আর ভার্চুয়াল MAC বানায়; PC-র gateway হয় সেই VIP। Active router নষ্ট হলে standby কয়েক সেকেন্ডে VIP নিয়ে নেয়, PC কিছু টেরই পায় না।

> **বাস্তব পরিস্থিতি:** ডেটা সেন্টারের দুটো core switch, HSRP-তে VLAN 10-এর gateway `10.10.10.1`। Core-1-এর maintenance-এর সময় traffic নিজে থেকেই Core-2-তে চলে গেল, কোনো সার্ভারের config বদলাতে হয়নি।

```text
Switch(config-if)# standby 10 ip 10.10.10.1
Switch(config-if)# standby 10 priority 110
Switch(config-if)# standby 10 preempt
Switch# show standby brief
```

| বিষয় | HSRP | VRRP | GLBP |
|---|---|---|---|
| মালিক | Cisco | Open standard (RFC) | Cisco |
| ভূমিকা | Active / Standby | Master / Backup | AVG / AVF |
| Load balancing | না (প্রতি group-এ) | না (প্রতি group-এ) | হ্যাঁ, একাধিক router একসাথে |
| Default priority | 100 | 100 | 100 |

| শব্দ | মানে |
|---|---|
| `standby 10` | HSRP group 10 |
| `priority 110` | বেশি priority = active হবে |
| `preempt` | বেশি priority-এর router ফিরে এলে আবার active হবে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** HSRP আর VRRP-এর পার্থক্য?
**উত্তর:** HSRP Cisco নিজস্ব, Active/Standby; VRRP open standard, Master/Backup, আর VRRP-এ VIP সরাসরি একটা router-এর আসল IP-ও হতে পারে।

**প্রশ্ন:** Preempt কী?
**উত্তর:** বেশি priority-এর router পুনরায় সচল হলে active ভূমিকা ফিরিয়ে নেওয়ার অনুমতি। Default-এ HSRP-তে বন্ধ, VRRP-তে চালু।

---

## ০৯. নেটওয়ার্ক নিরাপত্তা

Firewall, ACL, VPN আর Layer 2 আক্রমণ ও তার প্রতিরোধ; security প্রশ্ন এখন প্রতিটি network interview-এ আসে।

### Firewall: Stateless বনাম Stateful, NGFW, DMZ

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **NGFW** | **N**ext-**G**eneration **F**ire**W**all → পরবর্তী প্রজন্মের firewall |
| **DMZ** | **D**e**M**ilitarized **Z**one → নিরপেক্ষ এলাকা |
| **WAF** | **W**eb **A**pplication **F**irewall → ওয়েব অ্যাপের firewall |
| **IDS / IPS** | **I**ntrusion **D**etection / **P**revention **S**ystem → অনুপ্রবেশ শনাক্ত / প্রতিরোধ ব্যবস্থা |
| **UTM** | **U**nified **T**hreat **M**anagement → সব নিরাপত্তা একসাথে |

**সহজ ভাষায়:** Firewall নিয়ম অনুযায়ী traffic অনুমতি দেয় বা আটকায়। Stateless firewall (সাধারণ ACL) প্রতিটি packet আলাদা দেখে, তাই ফিরতি traffic-এর জন্যও আলাদা নিয়ম লাগে। Stateful firewall connection-এর অবস্থা মনে রাখে: ভেতর থেকে শুরু হওয়া সংযোগের উত্তর নিজে থেকেই আসতে দেয়। NGFW অ্যাপ্লিকেশন চিনতে পারে (যেমন Facebook বনাম YouTube), IPS আর URL filtering-ও করে। DMZ হলো ভেতরের নেটওয়ার্ক আর ইন্টারনেটের মাঝে আলাদা এলাকা, যেখানে public সার্ভার রাখা হয়।

> **বাস্তব পরিস্থিতি:** কোম্পানির web server আর mail server DMZ-তে রাখা। ইন্টারনেট থেকে শুধু DMZ-এর 443/25-এ আসা যায়; DMZ থেকে ভেতরের নেটওয়ার্কে শুধু database port-এ নির্দিষ্ট সার্ভারে যাওয়া যায়। Web server hack হলেও attacker সরাসরি অফিস নেটওয়ার্কে ঢুকতে পারবে না।

```text
     Internet
        |
   [ Firewall ]----- DMZ (web, mail, VPN)
        |
   Inside LAN (user, database)
```

| বিষয় | ব্যাখ্যা |
|---|---|
| Implicit deny | কোনো নিয়মে না মিললে শেষে সব আটকানো হয় |
| নিয়মের ক্রম | উপর থেকে নিচে, প্রথম মিলেই থামে, তাই নির্দিষ্ট নিয়ম আগে |
| IDS বনাম IPS | IDS শুধু শনাক্ত করে সতর্ক করে; IPS পথে বসে আক্রমণ আটকায় |
| Security Group (AWS) | stateful, শুধু allow নিয়ম |
| NACL (AWS) | stateless, allow ও deny, subnet স্তরে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Stateful আর stateless firewall-এর পার্থক্য?
**উত্তর:** Stateful connection table রাখে আর ফিরতি traffic নিজে অনুমতি দেয়; stateless প্রতিটি packet আলাদা বিচার করে, ফিরতি traffic-এর জন্য আলাদা নিয়ম লাগে।

**প্রশ্ন:** DMZ কী এবং কেন?
**উত্তর:** Public-facing সার্ভারের জন্য আলাদা নেটওয়ার্ক অংশ, যাতে সেগুলো আপোস হলেও ভেতরের নেটওয়ার্ক সুরক্ষিত থাকে।

**প্রশ্ন:** IDS আর IPS-এর পার্থক্য?
**উত্তর:** IDS passive, শনাক্ত করে alert দেয়; IPS inline, সাথে সাথে আটকায়।

### ACL ও Wildcard mask

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ACL** | **A**ccess **C**ontrol **L**ist → প্রবেশ নিয়ন্ত্রণ তালিকা |
| **ACE** | **A**ccess **C**ontrol **E**ntry → তালিকার প্রতিটি নিয়ম |

**সহজ ভাষায়:** ACL router/switch-এর interface-এ বসিয়ে traffic ছাঁকা হয়। Standard ACL শুধু source IP দেখে, তাই গন্তব্যের কাছে বসাতে হয়। Extended ACL source, destination, protocol, port সব দেখে, তাই উৎসের কাছে বসানো হয় যাতে অপ্রয়োজনীয় traffic পথে না চলে। প্রতিটি ACL-এর শেষে অদৃশ্য "deny any" থাকে। Cisco ACL-এ subnet mask-এর বদলে wildcard mask লাগে: subnet mask-এর উল্টো (0 = মিলতে হবে, 1 = যা খুশি)।

> **বাস্তব পরিস্থিতি:** Guest WiFi (`192.168.99.0/24`) যেন অফিসের সার্ভার নেটওয়ার্কে (`10.0.0.0/8`) না যেতে পারে কিন্তু ইন্টারনেট পায়। Guest VLAN-এর interface-এ inbound extended ACL বসালে।

```text
Router(config)# ip access-list extended GUEST-IN
Router(config-ext-nacl)# deny   ip  192.168.99.0 0.0.0.255 10.0.0.0 0.255.255.255
Router(config-ext-nacl)# permit udp 192.168.99.0 0.0.0.255 any eq 53
Router(config-ext-nacl)# permit tcp 192.168.99.0 0.0.0.255 any eq 443
Router(config-ext-nacl)# deny   ip  any any log
Router(config)# interface vlan 99
Router(config-if)# ip access-group GUEST-IN in
Router# show access-lists
```

| শব্দ | মানে |
|---|---|
| `extended` | source, destination, protocol, port সব দেখবে |
| `0.0.0.255` | wildcard: শেষ octet যা খুশি = /24 |
| `any` | যেকোনো IP (0.0.0.0 255.255.255.255) |
| `host 10.1.1.5` | শুধু একটা IP (wildcard 0.0.0.0) |
| `eq 443` | equal: port ঠিক 443 |
| `log` | মিললে log-এ লেখো |
| `access-group ... in` | interface-এ ঢোকার পথে ACL বসাও |
| Wildcard হিসাব | 255.255.255.255 - subnet mask। /26 (255.255.255.192) = 0.0.0.63 |

| ধরন | নম্বর রেঞ্জ | কোথায় বসাবে |
|---|---|---|
| Standard | 1-99, 1300-1999 | গন্তব্যের কাছে |
| Extended | 100-199, 2000-2699 | উৎসের কাছে |
| Named | নাম দিয়ে | দুটোই হতে পারে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Standard আর extended ACL-এর পার্থক্য আর কোথায় বসাবে?
**উত্তর:** Standard শুধু source IP, গন্তব্যের কাছে। Extended source, destination, protocol, port, উৎসের কাছে।

**প্রশ্ন:** ACL-এর শেষে কী থাকে?
**উত্তর:** অদৃশ্য implicit "deny any"। তাই অন্তত একটা permit না থাকলে সব আটকে যাবে।

**প্রশ্ন:** /27-এর wildcard mask কত?
**উত্তর:** 255.255.255.224-এর উল্টো = 0.0.0.31।

### VPN ও IPsec

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **VPN** | **V**irtual **P**rivate **N**etwork → ভার্চুয়াল ব্যক্তিগত নেটওয়ার্ক |
| **IPsec** | **IP** **Sec**urity → IP স্তরের নিরাপত্তা স্যুট |
| **IKE** | **I**nternet **K**ey **E**xchange → key বিনিময়ের protocol |
| **ESP** | **E**ncapsulating **S**ecurity **P**ayload → এনক্রিপ্টেড payload (IP protocol 50) |
| **AH** | **A**uthentication **H**eader → শুধু যাচাই, এনক্রিপশন নেই (protocol 51) |
| **SA** | **S**ecurity **A**ssociation → দুপক্ষের নিরাপত্তা চুক্তি |
| **NAT-T** | NAT **T**raversal → NAT-এর ভেতর দিয়ে IPsec (UDP 4500) |
| **PFS** | **P**erfect **F**orward **S**ecrecy → পুরনো key ফাঁস হলেও পুরনো data নিরাপদ |

**সহজ ভাষায়:** VPN ইন্টারনেটের উপর দিয়ে একটা এনক্রিপ্টেড সুড়ঙ্গ বানায়। Site-to-site VPN দুটো অফিসের নেটওয়ার্ক যুক্ত করে (সাধারণত IPsec)। Remote access VPN একজন user-কে অফিসে যুক্ত করে (SSL VPN, IPsec, WireGuard)। IPsec দুই ধাপে কাজ করে: IKE Phase 1-এ দুপক্ষ পরিচয় যাচাই করে একটা নিরাপদ management চ্যানেল বানায়, Phase 2-তে আসল data এনক্রিপ্ট করার SA বানায়।

> **বাস্তব পরিস্থিতি:** ঢাকা অফিস আর AWS VPC-র মধ্যে site-to-site VPN চালু হচ্ছে না। Log-এ দেখা গেল Phase 1 সফল, Phase 2 fail: দুপাশের encryption domain (local/remote subnet) মেলেনি। আরেকবার Phase 1-ই হচ্ছে না: pre-shared key বা IKE version বা proposal (AES/SHA/DH group) মেলেনি, বা UDP 500/4500 আটকানো।

| বিষয় | ব্যাখ্যা |
|---|---|
| IKE Phase 1 | পরিচয় যাচাই (PSK বা certificate), DH key exchange, IKE SA তৈরি; UDP 500 |
| IKE Phase 2 | IPsec SA, কোন subnet এনক্রিপ্ট হবে (proxy ID / traffic selector) |
| Tunnel mode | পুরো IP packet এনক্রিপ্ট করে নতুন header দেয় (site-to-site) |
| Transport mode | শুধু payload এনক্রিপ্ট, আসল header থাকে (host-to-host) |
| Split tunneling | শুধু অফিসের traffic VPN দিয়ে, বাকি ইন্টারনেট সরাসরি |
| WireGuard | আধুনিক, হালকা, দ্রুত VPN (UDP) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** IPsec-এর দুটো phase কী কী?
**উত্তর:** Phase 1: IKE SA, পরিচয় যাচাই ও নিরাপদ চ্যানেল। Phase 2: IPsec SA, আসল data এনক্রিপশনের নিয়ম।

**প্রশ্ন:** ESP আর AH-এর পার্থক্য?
**উত্তর:** ESP encryption ও integrity দুটোই দেয়; AH শুধু integrity ও authentication, encryption নেই, আর NAT-এর সাথে কাজ করে না।

**প্রশ্ন:** Site-to-site আর remote access VPN-এর পার্থক্য?
**উত্তর:** Site-to-site দুটো নেটওয়ার্ক স্থায়ীভাবে যুক্ত করে (gateway থেকে gateway)। Remote access একজন user-এর ডিভাইস থেকে অফিসে।

### Layer 2 আক্রমণ ও প্রতিরোধ

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **MITM** | **M**an-**I**n-**T**he-**M**iddle → মাঝখানে বসা আক্রমণকারী |
| **DAI** | **D**ynamic **A**RP **I**nspection → ARP যাচাই |
| **DDoS** | **D**istributed **D**enial **o**f **S**ervice → বিতরণকৃত সেবা-বিঘ্ন আক্রমণ |
| **CAM** | **C**ontent **A**ddressable **M**emory → MAC টেবিল |

**সহজ ভাষায়:** ভেতরের নেটওয়ার্কে বসে আক্রমণকারী অনেক কিছু করতে পারে। Switch-এর নিরাপত্তা ফিচার দিয়ে এগুলো আটকাতে হয়।

> **বাস্তব পরিস্থিতি:** অফিসে একজন অসৎ কর্মী ARP spoofing টুল চালিয়ে নিজেকে gateway দাবি করছে, সবার traffic তার ল্যাপটপ দিয়ে যাচ্ছে। Switch-এ DHCP snooping আর Dynamic ARP Inspection চালু থাকলে ভুয়া ARP সাথে সাথে বাদ পড়ত।

| আক্রমণ | কী হয় | প্রতিরোধ |
|---|---|---|
| MAC flooding | ভুয়া MAC দিয়ে switch-এর টেবিল ভরিয়ে দেয়, switch তখন hub-এর মতো সবাইকে সব পাঠায় | Port security (প্রতি port-এ MAC সীমা) |
| ARP spoofing / poisoning | নিজেকে gateway বলে দাবি করে traffic চুরি (MITM) | Dynamic ARP Inspection |
| Rogue DHCP | ভুয়া DHCP server ভুল gateway/DNS দেয় | DHCP snooping (শুধু trusted port থেকে Offer) |
| DHCP starvation | সব IP ধার নিয়ে pool খালি করে | DHCP snooping rate limit, port security |
| VLAN hopping | double tagging বা DTP দিয়ে অন্য VLAN-এ ঢোকা | DTP বন্ধ (`switchport nonegotiate`), native VLAN অব্যবহৃত |
| STP attack | নিজেকে root bridge বানানো | BPDU Guard, Root Guard |
| DDoS | অসংখ্য উৎস থেকে traffic দিয়ে সেবা বন্ধ | ISP/cloud scrubbing, rate limit, CDN, anycast |

```text
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security violation restrict
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10
Switch(config-if)# ip dhcp snooping trust      ! শুধু uplink/DHCP server port
Switch(config)# ip arp inspection vlan 10
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** DHCP snooping কী আর DAI কীভাবে এর উপর নির্ভর করে?
**উত্তর:** DHCP snooping untrusted port থেকে DHCP server-এর বার্তা আটকায় আর একটা binding table (IP-MAC-port) বানায়; DAI সেই টেবিল দেখে ভুয়া ARP বাদ দেয়।

**প্রশ্ন:** Port security violation mode গুলো কী?
**উত্তর:** Protect (চুপচাপ ফেলে দেয়), Restrict (ফেলে দেয় আর log/counter), Shutdown (port err-disable, default)।

### AAA, RADIUS, TACACS+ ও 802.1X

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **AAA** | **A**uthentication, **A**uthorization, **A**ccounting → পরিচয় যাচাই, অনুমতি, হিসাব রাখা |
| **RADIUS** | **R**emote **A**uthentication **D**ial-**I**n **U**ser **S**ervice → দূরবর্তী যাচাই সেবা |
| **TACACS+** | **T**erminal **A**ccess **C**ontroller **A**ccess-**C**ontrol **S**ystem **Plus** → Cisco-এর যন্ত্র-প্রশাসনের যাচাই সেবা |
| **802.1X** | IEEE 802.1X → port ভিত্তিক নেটওয়ার্ক প্রবেশ নিয়ন্ত্রণ |
| **EAP** | **E**xtensible **A**uthentication **P**rotocol → সম্প্রসারণযোগ্য যাচাই protocol |
| **NAC** | **N**etwork **A**ccess **C**ontrol → নেটওয়ার্ক প্রবেশ নিয়ন্ত্রণ |

**সহজ ভাষায়:** AAA মানে কে তুমি (authentication), কী করতে পারবে (authorization), কী করেছ তার রেকর্ড (accounting)। প্রতিটি router-এ আলাদা user না রেখে কেন্দ্রীয় RADIUS/TACACS+ server ব্যবহার করা হয়। 802.1X দিয়ে কেউ অফিসের তারে বা WiFi-তে যুক্ত হলেই নেটওয়ার্ক পায় না, আগে username/certificate দিয়ে যাচাই হতে হয়।

> **বাস্তব পরিস্থিতি:** একজন network engineer চাকরি ছাড়লেন। ২০০টা switch-এ গিয়ে তার account মুছতে হলে দিন লেগে যাবে। TACACS+ থাকলে কেন্দ্রীয়ভাবে একবার disable করলেই হয়, আর কে কখন কোন কমান্ড দিয়েছিল তার accounting log-ও আছে।

| বিষয় | RADIUS | TACACS+ |
|---|---|---|
| মান | Open standard | Cisco (এখন RFC 8907) |
| Transport | UDP 1812/1813 | TCP 49 |
| Encryption | শুধু password | পুরো payload |
| AAA আলাদা | Authentication + authorization একসাথে | তিনটে আলাদা |
| কোথায় ভালো | Network access (WiFi, VPN, 802.1X) | Device administration (কমান্ড-স্তরের অনুমতি) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** RADIUS আর TACACS+-এর পার্থক্য?
**উত্তর:** টেবিলটাই উত্তর। সংক্ষেপে: RADIUS UDP, শুধু password এনক্রিপ্ট, user-এর নেটওয়ার্ক প্রবেশে; TACACS+ TCP, পুরো এনক্রিপ্ট, যন্ত্রের admin কমান্ড নিয়ন্ত্রণে।

**প্রশ্ন:** 802.1X-এর তিনটি অংশ কী?
**উত্তর:** Supplicant (user-এর ডিভাইস), Authenticator (switch/AP), Authentication server (RADIUS)।

---

## ১০. Wireless (WiFi)

প্রায় প্রতিটি অফিস আর বাসায় WiFi। Interview-এ বেসিক স্ট্যান্ডার্ড, band আর নিরাপত্তা জিজ্ঞেস করে।

### WiFi স্ট্যান্ডার্ড, Band ও Channel

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **WLAN** | **W**ireless **L**ocal **A**rea **N**etwork → বেতার LAN |
| **SSID** | **S**ervice **S**et **ID**entifier → WiFi-এর নাম |
| **BSSID** | **B**asic **S**ervice **S**et **ID**entifier → প্রতিটি AP radio-র MAC |
| **RSSI** | **R**eceived **S**ignal **S**trength **I**ndicator → সংকেতের শক্তি |
| **SNR** | **S**ignal-to-**N**oise **R**atio → সংকেত বনাম গোলমালের অনুপাত |
| **MU-MIMO** | **M**ulti-**U**ser **M**ultiple-**I**nput **M**ultiple-**O**utput → একসাথে অনেক user-কে আলাদা stream |

**সহজ ভাষায়:** WiFi-এর মান IEEE 802.11। 2.4 GHz দূরে যায়, দেয়াল ভেদ করে, কিন্তু ভিড় বেশি আর মাত্র ৩টা non-overlapping channel (1, 6, 11)। 5 GHz দ্রুত, অনেক channel, কিন্তু দূরত্ব কম। 6 GHz (WiFi 6E/7) আরও পরিষ্কার, আরও কম দূরত্ব।

> **বাস্তব পরিস্থিতি:** অফিসের একটা অংশে WiFi ধীর আর বারবার কেটে যায়। Survey করে দেখা গেল পাশাপাশি তিনটা AP সবাই 2.4 GHz-এর channel 6-এ, একে অপরকে বাধা দিচ্ছে (co-channel interference)। Channel 1, 6, 11-এ ভাগ করে আর বেশিরভাগ ক্লায়েন্টকে 5 GHz-এ ঠেলে (band steering) সমাধান।

| মান | নাম | Band | সর্বোচ্চ তাত্ত্বিক গতি |
|---|---|---|---|
| 802.11b | - | 2.4 GHz | 11 Mbps |
| 802.11a | - | 5 GHz | 54 Mbps |
| 802.11g | - | 2.4 GHz | 54 Mbps |
| 802.11n | WiFi 4 | 2.4 / 5 GHz | 600 Mbps |
| 802.11ac | WiFi 5 | 5 GHz | প্রায় 3.5-6.9 Gbps |
| 802.11ax | WiFi 6 / 6E | 2.4 / 5 / 6 GHz | প্রায় 9.6 Gbps |
| 802.11be | WiFi 7 | 2.4 / 5 / 6 GHz | প্রায় 46 Gbps |

| বিষয় | ব্যাখ্যা |
|---|---|
| RSSI | -30 dBm খুব ভালো, -67 dBm voice/video-র জন্য ন্যূনতম, -80-এর নিচে খারাপ |
| Autonomous AP | প্রতিটি AP আলাদাভাবে configure |
| Controller-based (WLC) | কেন্দ্রীয় controller সব AP চালায়, roaming সহজ |
| Cloud-managed | Meraki, Aruba Central, UniFi-এর মতো cloud থেকে |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** 2.4 GHz আর 5 GHz-এর পার্থক্য?
**উত্তর:** 2.4 GHz দূরে যায় ও দেয়াল ভেদ করে কিন্তু ভিড় বেশি আর ধীর; 5 GHz দ্রুত, বেশি channel, কিন্তু কম দূরত্ব।

**প্রশ্ন:** 2.4 GHz-এ non-overlapping channel কোনগুলো?
**উত্তর:** 1, 6 আর 11।

### WiFi নিরাপত্তা: WPA2, WPA3, Enterprise

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **WEP** | **W**ired **E**quivalent **P**rivacy → পুরনো, ভাঙা নিরাপত্তা |
| **WPA** | **W**iFi **P**rotected **A**ccess → WiFi সুরক্ষিত প্রবেশ |
| **PSK** | **P**re-**S**hared **K**ey → সবার একই password |
| **SAE** | **S**imultaneous **A**uthentication of **E**quals → WPA3-এর নিরাপদ handshake |
| **AES** | **A**dvanced **E**ncryption **S**tandard → উন্নত এনক্রিপশন মান |

**সহজ ভাষায়:** WEP আর WPA (TKIP) আর ব্যবহার করা উচিত নয়। WPA2-Personal (PSK)-এ সবার একই password, ছোট অফিস বা বাসার জন্য। WPA2/WPA3-Enterprise-এ প্রতিটি user-এর নিজের username বা certificate, 802.1X আর RADIUS দিয়ে যাচাই। WPA3 dictionary attack-এর বিরুদ্ধে SAE ব্যবহার করে আরও নিরাপদ।

> **বাস্তব পরিস্থিতি:** অফিসের WiFi password কেউ চলে যাওয়ার পর সবার জন্য বদলাতে হয়, আর অনেকে password অন্যকে দিয়ে দেয়। WPA2-Enterprise-এ গেলে প্রত্যেকে নিজের AD account দিয়ে যুক্ত হয়; কেউ চলে গেলে শুধু তার account বন্ধ করলেই হয়।

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** WPA2-Personal আর WPA2-Enterprise-এর পার্থক্য?
**উত্তর:** Personal-এ সবার জন্য একটা pre-shared key। Enterprise-এ 802.1X/RADIUS দিয়ে প্রতিজনের আলাদা পরিচয়, ফলে আলাদা নিয়ন্ত্রণ আর হিসাব রাখা যায়।

---

## ১১. Advanced: QoS, MPLS, SD-WAN, Load Balancing, Cloud ও Data Center

Senior বা DevOps/Cloud Network পদের interview-এ এই বিষয়গুলো আলাদা করে দেয় কে শুধু CCNA পড়েছে আর কে বাস্তবে কাজ করেছে।

### QoS: জরুরি traffic-কে অগ্রাধিকার

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **QoS** | **Q**uality **o**f **S**ervice → সেবার মান |
| **DSCP** | **D**ifferentiated **S**ervices **C**ode **P**oint → IP header-এ অগ্রাধিকারের চিহ্ন |
| **CoS** | **C**lass **o**f **S**ervice → Layer 2 (802.1Q)-এ অগ্রাধিকার |
| **EF** | **E**xpedited **F**orwarding (DSCP 46) → দ্রুততম শ্রেণি, voice-এর জন্য |
| **LLQ** | **L**ow **L**atency **Q**ueuing → কম latency-র queue |

**সহজ ভাষায়:** লাইন ভরে গেলে কোন traffic আগে যাবে, কোনটা অপেক্ষা করবে বা ফেলে দেওয়া হবে, QoS তা ঠিক করে। ধাপ: classification (চেনা), marking (DSCP দিয়ে চিহ্ন দেওয়া), queuing (আলাদা লাইনে দাঁড় করানো), policing/shaping (গতি সীমিত করা)। QoS bandwidth বাড়ায় না, শুধু ভাগ করে।

> **বাস্তব পরিস্থিতি:** শাখা অফিসে কেউ বড় ফাইল upload করলেই সবার IP phone-এ কথা ভেঙে যায়। Voice traffic-কে DSCP EF দিয়ে চিহ্নিত করে priority queue-তে দিলে, ফাইল upload একটু ধীর হলেও কল পরিষ্কার থাকে।

| ধারণা | মানে |
|---|---|
| Policing | সীমার বেশি traffic ফেলে দাও (বা নিচে নামাও) |
| Shaping | সীমার বেশি traffic buffer-এ রেখে পরে পাঠাও (মসৃণ) |
| Voice-এর লক্ষ্য | latency < 150 ms একদিকে, jitter < 30 ms, loss < 1% |
| Trust boundary | কোন জায়গা থেকে DSCP চিহ্ন বিশ্বাস করবে (সাধারণত IP phone বা access switch) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Policing আর shaping-এর পার্থক্য?
**উত্তর:** Policing অতিরিক্ত traffic সাথে সাথে ফেলে দেয় বা remark করে। Shaping buffer-এ রেখে দেরিতে পাঠায়, ফলে loss কম কিন্তু delay বাড়ে।

**প্রশ্ন:** Voice traffic-এর DSCP মান কত?
**উত্তর:** EF, DSCP 46।

### MPLS ও SD-WAN

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **MPLS** | **M**ulti-**P**rotocol **L**abel **S**witching → label দিয়ে দ্রুত packet পাঠানো |
| **LSP** | **L**abel **S**witched **P**ath → label-এর পথ |
| **PE / P / CE** | **P**rovider **E**dge / **P**rovider / **C**ustomer **E**dge → ISP-এর প্রান্ত / ভেতরের / গ্রাহকের router |
| **VRF** | **V**irtual **R**outing and **F**orwarding → একই router-এ আলাদা আলাদা routing table |
| **SD-WAN** | **S**oftware-**D**efined **W**ide **A**rea **N**etwork → software নিয়ন্ত্রিত WAN |

**সহজ ভাষায়:** MPLS-এ ISP প্রতিটি packet-এর সামনে একটা ছোট label লাগায়; পরের router গুলো IP না দেখে শুধু label দেখে দ্রুত পাঠায়। ISP একই নেটওয়ার্কে অনেক গ্রাহককে VRF দিয়ে আলাদা রাখে (MPLS L3VPN)। নির্ভরযোগ্য, SLA আছে, কিন্তু দামি। SD-WAN একাধিক সস্তা লিংক (broadband, 4G, MPLS) একসাথে ব্যবহার করে, কেন্দ্রীয় controller থেকে policy দেয় আর অ্যাপ অনুযায়ী সেরা লিংক বেছে নেয়।

> **বাস্তব পরিস্থিতি:** একটা চেইন সুপারশপের ১০০ শাখা; আগে সব শাখায় দামি MPLS ছিল। SD-WAN-এ গিয়ে প্রতিটি শাখায় একটা broadband আর একটা 4G লাগাল; POS আর voice জায়গামতো ভালো লিংকে যায়, একটা লিংক গেলে অন্যটায় চলে যায়, খরচ অনেক কম।

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** MPLS কোন স্তরে কাজ করে?
**উত্তর:** Layer 2 আর 3-এর মাঝে, তাই একে Layer 2.5 বলা হয়।

**প্রশ্ন:** VRF কী?
**উত্তর:** একই physical router-এ একাধিক আলাদা routing table, যাতে আলাদা গ্রাহক বা বিভাগের একই IP রেঞ্জও সংঘাত ছাড়া চলে।

**প্রশ্ন:** SD-WAN-এর সুবিধা কী?
**উত্তর:** সস্তা লিংক ব্যবহার, অ্যাপ-ভিত্তিক পথ নির্বাচন, কেন্দ্রীয় ব্যবস্থাপনা, দ্রুত নতুন শাখা চালু (zero-touch provisioning)।

### Load Balancing: L4 বনাম L7

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **LB** | **L**oad **B**alancer → ভার বণ্টনকারী |
| **L4** | **L**ayer **4** (Transport) → IP ও port দেখে |
| **L7** | **L**ayer **7** (Application) → HTTP path, header, cookie দেখে |
| **VIP** | **V**irtual **IP** → LB-এর সামনের IP |
| **SSL offload** | SSL/TLS offload → LB-তে এনক্রিপশন খোলা |

**সহজ ভাষায়:** Load balancer অনেকগুলো সার্ভারের সামনে বসে traffic ভাগ করে দেয়, আর health check দিয়ে নষ্ট সার্ভারকে বাদ দেয়। L4 LB শুধু IP/port দেখে, খুব দ্রুত। L7 LB HTTP বোঝে: `/api` এক দলের সার্ভারে, `/images` আরেক দলে পাঠাতে পারে, TLS খুলতে পারে, header যোগ করতে পারে।

> **বাস্তব পরিস্থিতি:** Black Friday-তে সাইটে ১০ গুণ traffic। LB-এর পেছনে auto-scaling দিয়ে ৩টা থেকে ২০টা সার্ভার হলো; একটা সার্ভারের health check fail করায় LB নিজেই তাকে বাদ দিল, user কিছুই টের পেল না।

| Algorithm | কাজ |
|---|---|
| Round robin | পালাক্রমে একের পর এক |
| Least connections | যার সংযোগ সবচেয়ে কম |
| Weighted | শক্তিশালী সার্ভার বেশি ভাগ পায় |
| IP hash / sticky session | একই user সবসময় একই সার্ভারে |

| উদাহরণ | ধরন |
|---|---|
| AWS NLB | L4 |
| AWS ALB, Nginx, HAProxy, Envoy | L7 (HAProxy L4-ও পারে) |
| F5 BIG-IP, Citrix ADC | L4-L7 hardware/virtual |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** L4 আর L7 load balancer-এর পার্থক্য?
**উত্তর:** L4 IP আর port দেখে সিদ্ধান্ত নেয়, দ্রুত, content বোঝে না। L7 HTTP URL, header, cookie দেখে সিদ্ধান্ত নেয়; routing, TLS termination, WAF করতে পারে।

**প্রশ্ন:** Sticky session কী আর এর সমস্যা কী?
**উত্তর:** একই user-কে একই backend-এ পাঠানো (cookie বা IP দিয়ে)। সমস্যা: load অসমান হয় আর সেই সার্ভার পড়লে session হারায়। ভালো সমাধান হলো session-কে Redis-এর মতো shared store-এ রাখা।

### Cloud networking: VPC, Subnet, Security Group

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **VPC** | **V**irtual **P**rivate **C**loud → ক্লাউডে নিজের আলাদা নেটওয়ার্ক |
| **IGW** | **I**nternet **G**ate**W**ay → VPC-র ইন্টারনেট দরজা |
| **NAT GW** | NAT **G**ate**W**ay → private subnet-কে বাইরে যেতে দেয় |
| **SG** | **S**ecurity **G**roup → instance স্তরের stateful firewall |
| **NACL** | **N**etwork **A**ccess **C**ontrol **L**ist → subnet স্তরের stateless firewall |
| **TGW** | **T**ransit **G**ate**W**ay → অনেক VPC যুক্ত করার কেন্দ্র |

**সহজ ভাষায়:** ক্লাউডে VPC হলো তোমার নিজের data center-এর মতো আলাদা নেটওয়ার্ক। ভেতরে public subnet (route table-এ internet gateway আছে) আর private subnet (বাইরে যেতে NAT gateway দিয়ে, বাইরে থেকে আসা যায় না) রাখা হয়। নিরাপত্তা দুই স্তরে: Security Group আর NACL।

> **বাস্তব পরিস্থিতি:** একটা নতুন EC2-তে SSH হচ্ছে না। পরীক্ষার ক্রম: instance কি public subnet-এ আর public IP আছে? subnet-এর route table-এ `0.0.0.0/0 -> igw` আছে? Security Group-এ 22 তোমার IP থেকে খোলা? NACL-এ inbound 22 আর outbound ephemeral port (1024-65535) খোলা? NACL stateless বলে ফিরতি port ভুলে যাওয়া খুব কমন ভুল।

```text
VPC 10.0.0.0/16
├─ Public subnet  10.0.1.0/24 -> route 0.0.0.0/0 -> Internet Gateway  (ALB, bastion, NAT GW)
└─ Private subnet 10.0.2.0/24 -> route 0.0.0.0/0 -> NAT Gateway       (app server, database)
```

| বিষয় | Security Group | NACL |
|---|---|---|
| স্তর | Instance (ENI) | Subnet |
| State | Stateful | Stateless |
| নিয়ম | শুধু allow | allow ও deny, নম্বর ক্রমে |
| ফিরতি traffic | নিজে থেকে অনুমোদিত | আলাদা নিয়ম লাগে |

| সংযোগ | কাজ |
|---|---|
| VPC Peering | দুটো VPC সরাসরি, transitive নয় |
| Transit Gateway | অনেক VPC ও on-prem-এর hub |
| Site-to-Site VPN | অফিস থেকে VPC, ইন্টারনেট দিয়ে IPsec |
| Direct Connect / ExpressRoute | অফিস থেকে ক্লাউডে ডেডিকেটেড লাইন |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Public আর private subnet-এর পার্থক্য?
**উত্তর:** Public subnet-এর route table-এ Internet Gateway-এর route আছে; private subnet-এ নেই, বাইরে যেতে NAT Gateway লাগে।

**প্রশ্ন:** Security Group আর NACL-এর পার্থক্য?
**উত্তর:** SG instance স্তরের, stateful, শুধু allow। NACL subnet স্তরের, stateless, allow/deny, ক্রমানুসারে।

**প্রশ্ন:** VPC peering কি transitive?
**উত্তর:** না; A-B আর B-C peering থাকলেও A থেকে C-তে যাওয়া যায় না; সেজন্য Transit Gateway লাগে।

### CDN, Anycast ও Data center fabric (VXLAN, Spine-Leaf)

`🔴 অ্যাডভান্সড`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **CDN** | **C**ontent **D**elivery **N**etwork → কাছের সার্ভার থেকে content দেওয়ার নেটওয়ার্ক |
| **PoP** | **P**oint **o**f **P**resence → CDN-এর স্থানীয় উপস্থিতি |
| **VXLAN** | **V**irtual e**X**tensible **LAN** → Layer 3-এর উপর দিয়ে Layer 2 বিস্তার |
| **VNI** | **V**XLAN **N**etwork **I**dentifier → VXLAN-এর ২৪-bit নম্বর |
| **VTEP** | **V**XLAN **T**unnel **E**nd**P**oint → VXLAN সুড়ঙ্গের প্রান্ত |
| **EVPN** | **E**thernet **VPN** → VXLAN-এর control plane (BGP দিয়ে) |
| **ECMP** | **E**qual-**C**ost **M**ulti-**P**ath → সমান খরচের অনেক পথ একসাথে |

**সহজ ভাষায়:** CDN (Cloudflare, Akamai, CloudFront) পৃথিবীর নানা শহরে cache সার্ভার রাখে, user সবচেয়ে কাছের PoP থেকে content পায়, ফলে দ্রুত আর মূল সার্ভারে চাপ কম। আধুনিক data center-এ পুরনো ৩-স্তরের নকশার বদলে spine-leaf নকশা ব্যবহার হয়: প্রতিটি leaf সব spine-এর সাথে যুক্ত, যেকোনো দুই সার্ভারের মধ্যে সমান দূরত্ব, ECMP দিয়ে সব পথ ব্যবহার। VLAN-এর 4094 সীমা পেরোতে VXLAN ব্যবহার হয় (১.৬ কোটি নেটওয়ার্ক), যা Layer 2 frame-কে UDP (port 4789)-তে ভরে Layer 3-এর উপর দিয়ে পাঠায়।

> **বাস্তব পরিস্থিতি:** বাংলাদেশি একটা ই-কমার্স সাইটের মূল সার্ভার সিঙ্গাপুরে; ছবি লোড হতে অনেক সময় লাগে। CDN চালু করার পর ছবি ঢাকা বা কাছের PoP থেকে আসছে, পেজ লোড অর্ধেক সময়ে।

```text
      [Spine1]        [Spine2]
       /  |  \        /  |  \
  [Leaf1] [Leaf2] [Leaf3]     <- প্রতিটি leaf সব spine-এ যুক্ত
  servers servers servers
```

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Spine-leaf নকশার সুবিধা কী?
**উত্তর:** যেকোনো দুই leaf-এর মধ্যে সবসময় দুই hop, predictable latency, ECMP দিয়ে সব link ব্যবহার, STP-এর block নেই, সহজে বাড়ানো যায় (নতুন leaf/spine যোগ)।

**প্রশ্ন:** VXLAN কেন দরকার?
**উত্তর:** VLAN মাত্র ৪০৯৪টা, আর Layer 2 ডোমেইন data center জুড়ে ছড়াতে STP-এর সমস্যা। VXLAN ২৪-bit VNI দিয়ে ১.৬ কোটি segment দেয় আর Layer 3 underlay-এর উপর দিয়ে চলে।

**প্রশ্ন:** CDN কীভাবে user-কে কাছের সার্ভারে পাঠায়?
**উত্তর:** DNS ভিত্তিক (user-এর resolver দেখে কাছের PoP-এর IP দেয়) বা anycast (একই IP সব PoP থেকে announce, BGP নিজেই কাছেরটায় পাঠায়)।

---

## ১২. Troubleshooting পদ্ধতি ও টুল

Interview-এ প্রায়ই বলে "X কাজ করছে না, তুমি কী করবে"; পদ্ধতিগতভাবে উত্তর দিতে পারাটাই সবচেয়ে বড় দক্ষতা।

### Troubleshooting পদ্ধতি: স্তর ধরে এগোও

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **Bottom-up** | নিচের স্তর থেকে উপরে |
| **Divide and conquer** | মাঝ থেকে শুরু করে দুভাগ |

**সহজ ভাষায়:** অনুমান করে এলোমেলো কিছু বদলানোর বদলে পদ্ধতি মানো: সমস্যা সংজ্ঞায়িত করো (কে, কোথায়, কবে থেকে, কী বদলেছে), তথ্য সংগ্রহ করো, স্তর ধরে পরীক্ষা করো, একবারে একটাই পরিবর্তন করো, ফলাফল যাচাই করো, লিখে রাখো। Bottom-up (Physical থেকে উপরে) নতুন বা অপরিচিত সমস্যায় ভালো; divide and conquer (ping দিয়ে Layer 3 থেকে শুরু) অভিজ্ঞদের দ্রুত পদ্ধতি।

> **বাস্তব পরিস্থিতি:** Interview প্রশ্ন: "একজন user বলছে ইন্টারনেট নেই।" ভালো উত্তর: আগে জিজ্ঞেস করব শুধু তার নাকি সবার, কবে থেকে; তারপর তার IP আছে কিনা (`ipconfig`), gateway ping হয় কিনা, `8.8.8.8` ping হয় কিনা, google.com resolve হয় কিনা। প্রতিটি ধাপ একটা স্তর বাদ দেয়: IP না থাকলে DHCP/cable, gateway না গেলে local নেটওয়ার্ক, 8.8.8.8 গেলে কিন্তু নাম না গেলে DNS।

| ধাপ | পরীক্ষা | ব্যর্থ হলে সম্ভাব্য কারণ |
|---|---|---|
| L1 | Cable, link light, interface up? | তার, port, NIC, duplex mismatch |
| L2 | সঠিক VLAN? MAC শেখা হচ্ছে? ARP পূরণ? | ভুল VLAN, STP block, port security |
| L3 | IP/mask/gateway ঠিক? gateway ping? | DHCP, ভুল subnet, routing, ACL |
| DNS | nslookup / dig কাজ করে? | DNS server, ভুল record, cache |
| L4 | port খোলা? (nc, Test-NetConnection) | firewall, service চলছে না, ভুল bind |
| L7 | অ্যাপ কী error দিচ্ছে? (curl -v) | certificate, authentication, অ্যাপ bug |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** User বলছে ইন্টারনেট কাজ করছে না; তুমি কীভাবে troubleshoot করবে?
**উত্তর:** উপরের ধাপগুলো ক্রমে বলো: scope ঠিক করা, IP config, gateway ping, বাইরের IP ping, DNS, তারপর অ্যাপ; প্রতিটি ধাপে কী বাদ পড়ল সেটা বলো।

**প্রশ্ন:** একটা সমস্যা সমাধানের পর কী করবে?
**উত্তর:** সমাধান যাচাই করবে, user-এর কাছ থেকে নিশ্চিত হবে, মূল কারণ আর সমাধান document করবে, যাতে আবার না হয় (monitoring/alert যোগ)।

> 💡 **প্রো টিপ:** উত্তরে শুধু কমান্ডের নাম না বলে প্রতিটি ধাপের ফল থেকে কী সিদ্ধান্ত নেবে সেটা বলো; এটাই অভিজ্ঞতা বোঝায়।

### Linux নেটওয়ার্ক কমান্ড

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ip** | Internet Protocol tool → IP, route, neighbor ম্যানেজ |
| **ss** | **S**ocket **S**tatistics → socket-এর তথ্য |
| **dig** | **D**omain **I**nformation **G**roper → DNS খোঁজা |
| **mtr** | **M**y **T**race**R**oute → traceroute + ping |
| **nc** | **N**et**C**at → network-এর cat |
| **iperf3** | **I**nternet **Perf**ormance 3 → নেটওয়ার্ক গতি পরীক্ষা |
| **ethtool** | **Eth**ernet **tool** → network card-এর তথ্য |

**সহজ ভাষায়:** সার্ভার, ক্লাউড আর DevOps-এর কাজে নেটওয়ার্ক পরীক্ষার মূল Linux টুল। প্রতিটি কমান্ডের অপশনের অক্ষরগুলো নিচে ভেঙে দেওয়া আছে।

> **বাস্তব পরিস্থিতি:** দুটো সার্ভারের মধ্যে ফাইল কপি ধীর। iperf3 দিয়ে আসল network throughput মাপলে 940 Mbps (1G লাইনের জন্য ঠিক); তাহলে সমস্যা নেটওয়ার্ক নয়, disk বা অ্যাপ। আবার ethtool দেখাল একটা সার্ভারের link 100Mb/s Half: duplex/তারের সমস্যা।

```bash
ip -br a                          # সব interface আর IP সংক্ষেপে
ip r                              # routing table
ip route get 8.8.8.8              # এই IP-তে কোন পথে যাবে
ip neigh                          # ARP টেবিল
ss -tulnp                         # কোন port-এ কে listen করছে
ping -c 4 10.0.0.1
traceroute -n 8.8.8.8
mtr -rwc 100 8.8.8.8
dig +short example.com
dig @1.1.1.1 example.com A
curl -vI https://example.com
nc -zv db.local 5432
sudo tcpdump -i eth0 -nn port 443
sudo nmap -sV -p 22,80,443 10.0.0.5
iperf3 -s                         # সার্ভার পাশে
iperf3 -c 10.0.0.5 -P 4 -t 30     # ক্লায়েন্ট পাশে
ethtool eth0                      # speed, duplex, link
ethtool -S eth0                   # error counter
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `ip -br a` | `br` = brief → সংক্ষেপে · `a` = address → IP ঠিকানা |
| `ip r` / `ip neigh` | `r` = route → routing table · `neigh` = neighbour → ARP/NDP টেবিল |
| `ss -tulnp` | `t` = TCP · `u` = UDP · `l` = listening → শুধু অপেক্ষারত · `n` = numeric → port সংখ্যায় · `p` = process → কোন প্রোগ্রাম |
| `ping -c` | `c` = count → কতবার |
| `traceroute -n` | `n` = numeric → IP-কে নামে বদলাবে না |
| `mtr -rwc` | `r` = report → রিপোর্ট · `w` = wide → পুরো নাম · `c` = count → কতবার |
| `dig +short` / `@server` | `+short` → শুধু উত্তর · `@` = at server → এই DNS server-কে |
| `curl -vI` | `v` = verbose → বিস্তারিত · `I` = head → শুধু header |
| `nc -zv` | `z` = zero I/O → শুধু port পরীক্ষা · `v` = verbose → ফল দেখাও |
| `tcpdump -i -nn` | `i` = interface → কোন card · `n` = numeric host → IP নামে নয় · `n` = numeric port → port নামে নয় |
| `nmap -sV -p` | `s` = scan · `V` = Version → সফটওয়্যার version · `p` = ports → কোন port |
| `iperf3 -s` / `-c` | `s` = server mode · `c` = client mode |
| `iperf3 -P -t` | `P` = Parallel → একসাথে কয়টা stream · `t` = time → কত সেকেন্ড |
| `ethtool -S` | `S` = Statistics → NIC-এর পরিসংখ্যান (error, drop) |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** কোন port-এ কোন প্রসেস listen করছে কীভাবে দেখবে?
**উত্তর:** `ss -tulnp` (বা পুরনো `netstat -tulnp`, বা `lsof -i :PORT`)।

**প্রশ্ন:** দুটো সার্ভারের মধ্যে আসল network throughput কীভাবে মাপবে?
**উত্তর:** iperf3: একপাশে `iperf3 -s`, অন্যপাশে `iperf3 -c IP`। Disk বা অ্যাপের প্রভাব ছাড়া শুধু নেটওয়ার্ক মাপে।

### Cisco IOS show ও troubleshooting কমান্ড

`🟡 ইন্টারমিডিয়েট`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **IOS** | **I**nternetwork **O**perating **S**ystem → Cisco router/switch-এর OS |
| **CDP** | **C**isco **D**iscovery **P**rotocol → পাশের Cisco যন্ত্র খোঁজা |
| **LLDP** | **L**ink **L**ayer **D**iscovery **P**rotocol → সব কোম্পানির জন্য open standard প্রতিবেশী খোঁজা |
| **NVRAM** | **N**on-**V**olatile **RAM** → বন্ধ হলেও মুছে যায় না এমন memory |

**সহজ ভাষায়:** Cisco যন্ত্রে কয়েকটা mode আছে: user EXEC (`>`), privileged EXEC (`#`, `enable` দিয়ে), global config (`(config)#`), interface config (`(config-if)#`)। running-config হলো এখন চলমান (RAM), startup-config হলো সংরক্ষিত (NVRAM); save না করলে reboot-এ পরিবর্তন হারাবে।

> **বাস্তব পরিস্থিতি:** রাতে একটা switch reboot হয়ে আগের দিনের সব VLAN পরিবর্তন হারিয়ে গেছে; কারণ কেউ `copy running-config startup-config` (বা `write memory`) করেনি। আবার একটা port-এ user ধীরগতি পাচ্ছে: `show interfaces` দেখাল অনেক CRC error, তার বদলাতেই ঠিক।

```text
Router> enable
Router# show ip interface brief
Router# show interfaces gi0/1
Router# show running-config
Router# show ip route
Router# show ip protocols
Router# show cdp neighbors detail
Router# show lldp neighbors
Switch# show vlan brief
Switch# show interfaces trunk
Switch# show mac address-table
Switch# show spanning-tree
Switch# show interfaces status err-disabled
Router# ping 10.0.0.2 source gi0/0
Router# traceroute 8.8.8.8
Router# copy running-config startup-config
```

| কমান্ড | কী দেখায় / অক্ষরের মানে |
|---|---|
| `enable` | privileged mode-এ যাও (# prompt) |
| `show ip interface brief` | brief = সংক্ষেপে; প্রতিটি interface-এর IP আর Status/Protocol (up/up হওয়া উচিত) |
| `show interfaces gi0/1` | gi = GigabitEthernet; error, CRC, duplex, speed, input/output rate |
| `show running-config` | running = এখন চলমান config (RAM-এ) |
| `show ip protocols` | কোন routing protocol চলছে, কোন নেটওয়ার্ক |
| `show cdp neighbors detail` | CDP = Cisco Discovery Protocol; পাশের যন্ত্র, তার IP আর port |
| `show interfaces status err-disabled` | err-disabled = error-এর কারণে বন্ধ port (port security, BPDU guard) |
| `ping ... source gi0/0` | source = নির্দিষ্ট interface-এর IP থেকে ping |
| `copy running-config startup-config` | চলমান config-কে স্থায়ীভাবে (NVRAM-এ) save করো |

| Status / Protocol | মানে |
|---|---|
| up / up | সব ঠিক |
| down / down | Layer 1 সমস্যা: তার নেই, অন্যপাশ বন্ধ |
| up / down | Layer 2 সমস্যা: encapsulation, keepalive, clock rate মিলছে না |
| administratively down / down | কেউ shutdown দিয়েছে; `no shutdown` দাও |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** "up/down" দেখলে কোন স্তরে সমস্যা?
**উত্তর:** Physical (Layer 1) ঠিক আছে, কিন্তু Data link (Layer 2) protocol মিলছে না, যেমন encapsulation mismatch।

**প্রশ্ন:** running-config আর startup-config-এর পার্থক্য?
**উত্তর:** running-config RAM-এ চলমান, startup-config NVRAM-এ সংরক্ষিত, boot-এর সময় লোড হয়; `copy run start` দিয়ে save করতে হয়।

**প্রশ্ন:** CDP আর LLDP-এর পার্থক্য?
**উত্তর:** CDP Cisco নিজস্ব, LLDP (IEEE 802.1AB) সব vendor-এর জন্য open standard। দুটোই পাশের যন্ত্র চিনতে সাহায্য করে।

### Windows নেটওয়ার্ক কমান্ড

`🟢 বেসিক`

**পূর্ণরূপ**

| শব্দ | মানে |
|---|---|
| **ipconfig** | IP configuration → IP সেটিং দেখাও |
| **tracert** | trace route → পথ অনুসরণ |
| **netstat** | network statistics → নেটওয়ার্ক পরিসংখ্যান |
| **nslookup** | **N**ame **S**erver **lookup** → DNS খোঁজা |

**সহজ ভাষায়:** অফিসের helpdesk ও desktop support-এ Windows কমান্ড সবচেয়ে বেশি লাগে। PowerShell-এর `Test-NetConnection` দিয়ে port-ও পরীক্ষা করা যায়, telnet লাগে না।

> **বাস্তব পরিস্থিতি:** একজন user-এর ল্যাপটপ অফিসের নতুন ওয়েবসাইট খুলছে না, অন্য সবার খুলছে। `ipconfig /displaydns` দেখাল পুরনো IP cache-এ আছে; `ipconfig /flushdns` দিতেই ঠিক।

```text
ipconfig /all
ipconfig /release
ipconfig /renew
ipconfig /flushdns
ipconfig /displaydns
ping -t 8.8.8.8
tracert -d 8.8.8.8
pathping 8.8.8.8
nslookup example.com 8.8.8.8
netstat -ano
arp -a
route print
Test-NetConnection example.com -Port 443
```

**অপশনের পূর্ণরূপ (অক্ষরে অক্ষরে)**

| অপশন | অক্ষর = পূর্ণরূপ → মানে |
|---|---|
| `ipconfig /all` | all → সব তথ্য: MAC, DHCP server, DNS, lease |
| `/release` `/renew` | release → IP ছেড়ে দাও · renew → নতুন IP চাও |
| `/flushdns` `/displaydns` | flush DNS → DNS cache মোছো · display DNS → cache দেখাও |
| `ping -t` | `t` = till stopped → Ctrl+C না দেওয়া পর্যন্ত চলবে |
| `tracert -d` | `d` = do not resolve → IP-কে নামে বদলাবে না (দ্রুত) |
| `netstat -ano` | `a` = all → সব connection ও listening port · `n` = numeric → সংখ্যায় · `o` = owner PID → কোন process ID |
| `arp -a` | `a` = all → সব ARP entry |
| `Test-NetConnection -Port` | এই TCP port খোলা কিনা পরীক্ষা |

**ইন্টারভিউ প্রশ্ন ও উত্তর**

**প্রশ্ন:** Windows-এ কোন প্রোগ্রাম port 443 ব্যবহার করছে কীভাবে বের করবে?
**উত্তর:** `netstat -ano | findstr :443` দিয়ে PID বের করো, তারপর Task Manager বা `tasklist /fi "PID eq <PID>"` দিয়ে প্রোগ্রাম।

**প্রশ্ন:** pathping কী?
**উত্তর:** tracert আর ping-এর মিশ্রণ: পথের প্রতিটি hop-এ কিছুক্ষণ ধরে packet loss মাপে।

---

## ১৩. বাস্তব Incident Playbook

ইন্টারভিউয়ের "X কাজ করছে না, কী করবে?" প্রশ্নের জন্য ধাপে ধাপে উত্তর; বাস্তব কাজেও checklist হিসেবে ব্যবহার করো।

### 🚨 ওয়েবসাইট খুলছে না

1. শুধু একজনের নাকি সবার? অন্য নেটওয়ার্ক (মোবাইল ডেটা) থেকে খোলে?
2. DNS: `dig +short site.com` → IP আসছে? ঠিক IP?
3. Reachability: `ping` / `mtr IP` → পথে কোথায় থামছে?
4. Port: `nc -zv IP 443` (বা Test-NetConnection) → timeout = firewall, refused = service বন্ধ
5. TLS/HTTP: `curl -v https://site.com` → certificate error? 5xx?
6. Server পাশে: `ss -tlnp | grep 443`, `systemctl status nginx`, log দেখো
7. সার্ভারে packet আসছে কিনা: `sudo tcpdump -i any -nn port 443`

### 🚨 নেটওয়ার্ক ধীর

1. কোথায় ধীর: LAN, WAN, ইন্টারনেট, নাকি নির্দিষ্ট অ্যাপ?
2. Latency/loss: `mtr -rwc 100 target` → কোন hop থেকে loss শুরু?
3. Throughput: `iperf3 -c server -P 4` → লাইনের ক্ষমতা পাচ্ছে?
4. Interface: `show interfaces` / `ethtool -S eth0` → CRC, drop, duplex mismatch?
5. Utilization: SNMP graph, লিংক ভরা? কে বেশি খাচ্ছে (NetFlow)?
6. WiFi হলে: RSSI, channel interference, অনেক client একটা AP-তে?
7. প্রয়োজনে QoS, link upgrade, বা traffic নিয়ন্ত্রণ

### 🚨 PC IP পাচ্ছে না (169.254.x.x)

1. তার ও link light, switch port up কিনা (`show interfaces status`)
2. সঠিক VLAN-এ আছে? (`show vlan brief`)
3. DHCP server চালু আর pool-এ IP খালি আছে?
4. DHCP server অন্য subnet-এ হলে router-এ `ip helper-address` আছে?
5. DHCP snooping চালু থাকলে uplink port trusted কিনা
6. Port security বা 802.1X আটকাচ্ছে কিনা (err-disabled?)
7. `ipconfig /release && ipconfig /renew` দিয়ে আবার চেষ্টা

### 🚨 এক VLAN অন্য VLAN-এর সাথে কথা বলতে পারছে না

1. দুই VLAN-এর gateway (SVI বা sub-interface) আছে আর up/up?
2. L3 switch-এ `ip routing` চালু?
3. Trunk-এ দুটো VLAN-ই allowed? (`show interfaces trunk`)
4. Host-এর gateway আর subnet mask ঠিক?
5. মাঝে কোনো ACL বা firewall নিয়ম আটকাচ্ছে? (`show access-lists`, hit counter)
6. গন্তব্য host-এর নিজের firewall (Windows firewall) ICMP/port আটকাচ্ছে কিনা

### 🚨 Site-to-site VPN down

1. দুপাশের public IP-তে reachability আছে? UDP 500/4500 আর ESP (50) খোলা?
2. Phase 1: PSK, IKE version, encryption/hash/DH group, lifetime মেলে?
3. Phase 2: local/remote subnet (proxy ID) দুপাশে উল্টো করে মেলে?
4. NAT exemption: VPN traffic-কে ভুল করে NAT করা হচ্ছে কিনা
5. Routing: remote subnet-এর route tunnel দিয়ে যাচ্ছে?
6. Log দেখো: কোন phase-এ fail, কী error

### 🚨 BGP/OSPF neighbor উঠছে না

- **OSPF:** area, subnet/mask, hello/dead timer, authentication, MTU (ExStart-এ আটকালে), passive-interface ভুল করে দেওয়া?
- **BGP:** neighbor IP আর remote-as ঠিক? TCP 179-এ reachability (ping, ACL)? loopback দিয়ে peering হলে update-source আর eBGP হলে ebgp-multihop?
- **দুই ক্ষেত্রেই:** `show ip ospf neighbor` / `show ip bgp summary`, debug সাবধানে, log দেখো

---

## ১৪. ইন্টারভিউ প্রশ্ন ব্যাংক

প্রতিটি অধ্যায়ের কার্ডে থাকা প্রশ্নের বাইরে আরও কিছু জনপ্রিয় প্রশ্ন, লেভেল অনুযায়ী সাজানো। উত্তর মুখস্থ না করে নিজের ভাষায় বলার অনুশীলন করো।

### 🟢 বেসিক লেভেল

**প্রশ্ন ১. ব্রাউজারে google.com লিখে Enter দিলে কী কী ঘটে?**
১) Browser cache, OS cache আর hosts ফাইলে IP খুঁজে না পেলে DNS resolver-কে জিজ্ঞেস করে (UDP 53), resolver root → .com → google-এর authoritative server থেকে IP আনে। ২) গন্তব্য অন্য নেটওয়ার্কে, তাই PC ARP দিয়ে default gateway-এর MAC বের করে। ৩) TCP 3-way handshake (SYN, SYN-ACK, ACK) port 443-এ। ৪) TLS handshake: certificate যাচাই, session key তৈরি। ৫) HTTP GET request এনক্রিপ্টেড যায়; পথে router গুলো routing table দেখে forward করে, NAT router private IP-কে public IP-তে বদলায়। ৬) Server (প্রায়ই CDN/load balancer) HTTP response দেয়। ৭) Browser HTML parse করে, CSS/JS/ছবির জন্য আরও request পাঠায়, পেজ দেখায়।

**প্রশ্ন ২. Collision domain আর broadcast domain-এর পার্থক্য?**
Collision domain হলো যে অংশে একসাথে দুজন পাঠালে সংঘর্ষ হয়; hub পুরোটা একটা domain, switch-এর প্রতিটি port আলাদা। Broadcast domain হলো যে অংশে একটা broadcast সবাই পায়; router আর VLAN একে ভাগ করে।

**প্রশ্ন ৩. Default gateway কী? না থাকলে কী হবে?**
নিজের subnet-এর বাইরে যাওয়ার জন্য যে router-এ packet পাঠানো হয়। না থাকলে বা ভুল হলে একই subnet-এ কথা চলবে, কিন্তু বাইরের নেটওয়ার্ক বা ইন্টারনেটে যাওয়া যাবে না।

**প্রশ্ন ৪. Static IP আর dynamic IP-এর পার্থক্য? কোথায় কোনটা?**
Static হাতে দেওয়া, বদলায় না: server, printer, router, firewall। Dynamic DHCP থেকে আসে: user-এর PC, ফোন। Server-এ DHCP reservation-ও ব্যবহার হয়।

**প্রশ্ন ৫. Loopback address কী আর কেন ব্যবহার হয়?**
`127.0.0.1` (IPv6-এ `::1`), যন্ত্রের নিজের সাথে নিজের যোগাযোগ, নেটওয়ার্ক কার্ড ছাড়াই TCP/IP stack পরীক্ষা। Router-এ loopback interface ভিন্ন জিনিস: কখনো down হয় না এমন একটা logical interface, router ID আর management-এর জন্য।

**প্রশ্ন ৬. Hub, switch আর router-এর মধ্যে কোনটা broadcast আটকায়?**
শুধু router (আর VLAN দিয়ে switch-এ broadcast domain ভাগ)। Hub আর switch broadcast সব port-এ পাঠায়।

### 🟡 ইন্টারমিডিয়েট লেভেল

**প্রশ্ন ৭. Ping কাজ করছে কিন্তু ওয়েবসাইট খুলছে না; সম্ভাব্য কারণ?**
Layer 3 ঠিক আছে, তাই উপরের স্তরে সমস্যা: DNS ভুল বা কাজ করছে না, port 443 firewall-এ বন্ধ, web service চলছে না, proxy সমস্যা, TLS certificate error, বা MTU সমস্যা (ছোট ping যায়, বড় packet আটকে যায়)।

**প্রশ্ন ৮. একই subnet-এর দুটো PC একে অপরকে ping করতে পারছে না; কী দেখবে?**
দুটোর IP/mask সত্যিই একই subnet-এ কিনা, একই VLAN-এ কিনা, cable/port up কিনা, Windows firewall ICMP আটকাচ্ছে কিনা, IP conflict, port security বা private VLAN, ARP হচ্ছে কিনা (`arp -a`)।

**প্রশ্ন ৯. Trunk আর access port-এ VLAN mismatch হলে কী হয়?**
Native VLAN mismatch হলে CDP warning দেয় আর untagged traffic ভুল VLAN-এ চলে যায় (VLAN leak)। Access port ভুল VLAN-এ থাকলে host DHCP থেকে ভুল subnet-এর IP পায় বা কিছুই পায় না।

**প্রশ্ন ১০. Subnet mask ভুল দিলে কী সমস্যা হতে পারে?**
Host ভুল বুঝবে কে local আর কে remote। Mask বড় হলে (যেমন /24-এর জায়গায় /16) remote host-কে local ভেবে ARP করবে, gateway-তে পাঠাবে না, ফলে যোগাযোগ ব্যর্থ; ছোট হলে local host-কে remote ভেবে gateway-তে পাঠাবে।

**প্রশ্ন ১১. Proxy আর reverse proxy-এর পার্থক্য?**
Forward proxy ক্লায়েন্টের পক্ষে বাইরে যায় (অফিসের web filter)। Reverse proxy সার্ভারের সামনে বসে ক্লায়েন্টের অনুরোধ গ্রহণ করে পেছনের সার্ভারে পাঠায় (Nginx, load balancer, CDN)।

**প্রশ্ন ১২. Unicast flooding কেন হয়?**
Switch-এর MAC টেবিলে গন্তব্যের MAC নেই (aging, asymmetric routing, টেবিল ভরা), তাই একই VLAN-এর সব port-এ পাঠায়।

**প্রশ্ন ১৩. Port security-তে violation হলে port err-disabled হয়েছে; কীভাবে ঠিক করবে?**
কারণ খুঁজে ঠিক করবে (অনুমোদিত ডিভাইস নিশ্চিত করা), তারপর interface-এ `shutdown` আর `no shutdown` দেবে। স্বয়ংক্রিয় করতে `errdisable recovery cause psecure-violation`।

### 🔴 অ্যাডভান্সড লেভেল

**প্রশ্ন ১৪. OSPF আর BGP-এর মূল পার্থক্য? কোথায় কোনটা?**
OSPF IGP, link-state, একটা প্রতিষ্ঠানের ভেতরে দ্রুত converge আর সবচেয়ে কম cost খোঁজে। BGP EGP, path vector, AS-এর মধ্যে (ইন্টারনেট, multihoming, বড় data center), policy ভিত্তিক, লাখো route সামলাতে পারে, ধীর কিন্তু স্থিতিশীল।

**প্রশ্ন ১৫. Asymmetric routing কী আর কেন সমস্যা?**
যাওয়ার পথ আর ফেরার পথ আলাদা। সাধারণ routing-এ ঠিক থাকে, কিন্তু stateful firewall বা NAT-এর জন্য সমস্যা: firewall যাওয়ার পথের SYN দেখেনি বলে ফিরতি packet ফেলে দেয়।

**প্রশ্ন ১৬. MTU সমস্যা কীভাবে চিনবে আর সমাধান করবে?**
লক্ষণ: ছোট request কাজ করে, বড় পেজ/ফাইল আটকে যায়, VPN বা tunnel-এ বেশি দেখা যায়। `ping -M do -s 1472` (Linux) বা `ping -f -l 1472` (Windows) দিয়ে সর্বোচ্চ সাইজ বের করো। সমাধান: interface MTU কমানো, TCP MSS clamping (`ip tcp adjust-mss 1360`), আর ICMP type 3 code 4 আটকানো বন্ধ করা।

**প্রশ্ন ১৭. Route flapping কী আর কীভাবে কমানো যায়?**
কোনো route বারবার up/down হওয়া (অস্থির link), ফলে বারবার update ছড়ায়, CPU খায়। BGP route dampening, link-এর মূল সমস্যা ঠিক করা, summarization, আর IGP-তে timer ঠিক করা।

**প্রশ্ন ১৮. Control plane, data plane আর management plane কী?**
Control plane: routing protocol, পথ শেখা (OSPF, BGP)। Data plane: আসল packet forward করা (hardware/FIB)। Management plane: যন্ত্র পরিচালনা (SSH, SNMP)। SDN-এ control plane কেন্দ্রীয় controller-এ সরানো হয়।

**প্রশ্ন ১৯. SDN কী?**
Software-Defined Networking: control plane-কে switch/router থেকে আলাদা করে কেন্দ্রীয় controller-এ নেওয়া, API দিয়ে পুরো নেটওয়ার্ক programmatic ভাবে নিয়ন্ত্রণ। উদাহরণ: Cisco ACI, VMware NSX, SD-WAN।

**প্রশ্ন ২০. Network automation-এ কী কী টুল জানো?**
Ansible (agentless, playbook দিয়ে অনেক switch/router-এ config), Python (Netmiko, NAPALM, Nornir), Terraform (ক্লাউড নেটওয়ার্ক), REST API/NETCONF/RESTCONF, আর config-কে Git-এ রাখা (network as code)।

**প্রশ্ন ২১. High availability নেটওয়ার্ক কীভাবে নকশা করবে?**
প্রতিটি স্তরে redundancy: দুটো ISP (BGP), দুটো firewall (HA pair), দুটো core switch (FHRP/stacking/vPC), দ্বৈত uplink (EtherChannel), দ্বৈত power, দ্রুত convergence protocol (RSTP, BFD), monitoring আর পরিকল্পিত failover পরীক্ষা।

---

<p align="center"><sub>Networking: Basic to Advanced · <a href="../README.md">← সব গাইড</a></sub></p>
