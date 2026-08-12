#  Logging, Monitoring, and Observability Notes

> **Source Video:** [18. Logging, Monitoring and Observability (Sriniously)](http://www.youtube.com/watch?v=5PEuwgLOQQM)

---

##  Overview (সংক্ষিপ্ত পরিচিতি)

আধুনিক সিস্টেম ডিজাইন (Distributed Systems) এবং ব্যাকএন্ড আর্কিটেকচারে **Logging, Monitoring, এবং Observability** অত্যন্ত গুরুত্বপূর্ণ তিনটি ধারণা। ব্যাকএন্ড অ্যাপ্লিকেশনগুলো যখন একাধিক সার্ভার, রিজিয়ন বা ক্লাউডে রান করে, তখন সিস্টেমে কী ঘটছে তা ট্র্যাক করা এবং কোনো সমস্যা হলে তা দ্রুত চিহ্নিত করার জন্য এই প্র্যাকটিসগুলো প্রয়োগ করা হয়। 

---

##  The Three Pillars of Observability (অবজারভেবিলিটির তিনটি মূল স্তম্ভ)

Observability-র ৩টি মূল ভিত্তি রয়েছে, যাদের সংক্ষেপে **LMT** বলা হয়:

┌────────────────────────┐
              │     Observability      │
              └───────────┬────────────┘
                          │
     ┌────────────────────┼────────────────────┐
     ▼                    ▼                    ▼
┌───────────┐        ┌───────────┐        ┌───────────┐
│   Logs    │        │  Metrics  │        │  Traces   │
│ (Events)  │        │ (Patterns)│        │(Transactions)│
└───────────┘        └───────────┘        └───────────┘

1. **Logs (What happened):** সিস্টেমে কখন কী ঘটেছে তার বিস্তারিত রেকর্ড। 
2. **Metrics (Patterns & Trends):** নির্দিষ্ট সময়জুড়ে সিস্টেমের রিয়েল-টাইম পারফরম্যান্স ও স্বাস্থ্যের পরিসংখ্যানগত তথ্য। 
3. **Traces (Component Interactions):** একটি রিকোয়েস্ট সিস্টেমের কোন কোন লেয়ার বা সার্ভিস (Handler, Service, DB) পার হয়ে প্রসেস হয়েছে তার এন্ড-টু-এন্ড ট্র্যাকিং। 

---

##  Core Concepts Breakdown (মূল বিষয়সমূহের আলোচনা)

### 1. Logging (লগিং) 
অ্যাপ্লিকেশন চলাকালীন ঘটে যাওয়া সমস্ত গুরুত্বপূর্ণ ইভেন্টকে মেটাডেটা (যেমন: User ID, IP, Latency, Request ID) সহ রেকর্ড করে রাখাকে Logging বলে।

* **Log Levels (লগ লেভেলসমূহ):** 
  * `Debug`: ডেভেলপমেন্ট এনভায়রনমেন্টে বাগ ফিক্স করার জন্য বিস্তারিত তথ্য রেকর্ড করতে ব্যবহৃত হয়।
  * `Info`: সফল অপারেশন বা সাধারণ বিহেভিয়ার (যেমন: User Logged In, To-Do Created)।
  * `Warn`: বড় কোনো এরর নয়, তবে অস্বাভাবিক কোনো ঘটনা (যেমন: ভুল পাসওয়ার্ড দিয়ে লগইনের চেষ্টা)।
  * `Error`: ডেটাবেজ বা কোড লেভেলে কোনো অপারেশন ব্যর্থ হলে এটি ব্যবহার করা হয়।
  * `Fatal`: মারাত্মক এরর, যার ফলে অ্যাপ্লিকেশন বন্ধ/রিস্টার্ট হতে বাধ্য হয়।

* **Structured vs Unstructured Logging:** 
  * **Unstructured (Console/Text):** ডেভেলপমেন্ট মোডে ডেভেলপারের পড়ার সুবিধার জন্য কালারফুল প্লেইন টেক্সট হিসেবে ব্যবহৃত হয়।
  * **Structured (JSON Format):** প্রোডাকশন মোডে ব্যবহৃত হয়, যেন Grafana, Loki বা New Relic-এর মতো টুল সহজেই লগ ফিল্টার ও পার্স করতে পারে।

---

### 2. Monitoring (মনিটরিং) 
সিস্টেমের স্বাস্থ্য (Health) ও পারফরম্যান্সের ওপর ক্রমাগত নজর রাখা। এটি মূলত **Historical Data** এবং **Metrics** প্রদান করে।

* **Common Metrics:** CPU Usage, Memory Usage, Active DB Connections, Requests Per Second (RPS), Error Rate ইত্যাদি। 
* **সী সীমাবদ্ধতা:** মনিটরিং আপনাকে কেবল জানাবে যে সিস্টেমে **কোনো সমস্যা হয়েছে** (যেমন: Error Rate > 80%), কিন্তু **কেন বা কোথায় সমস্যা হয়েছে** তা কেবল মনিটরিং দিয়ে বের করা কঠিন। 

---

### 3. Observability (অবজারভেবিলিটি) 
বাহ্যিক আউটপুট (Logs, Metrics, Traces) দেখে সিস্টেমের অভ্যন্তরীণ অবস্থা (Internal State) নিখুঁতভাবে নির্ধারণ করার ক্ষমতাকে Observability বলা হয়।

* এটি আপনাকে জানায় **কী সমস্যা হয়েছে এবং কেন বা কোথায় হয়েছে**। 
* **OpenTelemetry (OTel):** এটি একটি ওপেন স্ট্যান্ডার্ড ইন্টারফেস ও SDK ইকোসিস্টেম, যা যেকোনো ভাষায় লেখা কোডকে Instrumentation (তথ্য সংগ্রহের জন্য প্রস্তুত) করতে সাহায্য করে। 

---

##  Real-World Debugging Workflow (বাস্তব ক্ষেত্রে কীভাবে কাজ করে)

একজন ডেভেলপার প্রোডাকশন সিস্টেমে কোনো ইস্যু ফিক্স করার সময় এই ফ্লো অনুসরণ করেন: 

$$\text{Alert (Slack/Email)} \longrightarrow \text{Metrics (New Relic/Grafana)} \longrightarrow \text{Logs (Error details)} \longrightarrow \text{Traces (Exact code layer failure)}$$

1. **Alerting:** সিস্টেমের Error Rate ৮০% পার হলে Slack বা Webhook-এ এলার্ট আসে।
2. **Metrics:** গ্রাফে দেখা যায় কোন নির্দিষ্ট টাইমে এরর রেট বেড়ে গেছে।
3. **Logs:** গ্রাফ থেকে স্পেসিফিক ৫০০০/৫০০ স্ট্যাটাস কোডের লগ দেখা হয়।
4. **Traces:** লগের Span ID ধরে দেখা যায় রিকোয়েস্টটি `Middleware` $\rightarrow$ `Service` $\rightarrow$ `Database` কোথায় গিয়ে ফেইল করেছে।

---

##  Popular Tools & Infrastructure (ব্যবহারযোগ্য টুলস)

* **Open-Source Stack (Grafana Stack):**
  * Dashboard & Visualization: **Grafana** 
  * Metrics: **Prometheus** 
  * Logs: **Loki / Promtail** 
  * Traces: **Jaeger** 
* **Proprietary All-in-One Solutions:**
  * **New Relic** (ভিডিওতে গো-ল্যাং অ্যাপে ইন্টিগ্রেট করে দেখানো হয়েছে) 
  * **Datadog** 

---

##  Key Takeaways for Developers

* **Logging** লিখুন অর্থপূর্ণ মেটাডেটা সহ।
* **Development** এনভায়রনমেন্টে readable টেক্সট এবং **Production** এনভায়রনমেন্টে JSON ফরম্যাটে লগ রেন্ডার করুন।
* কোড লেভেলে **Context Pass** করে Transaction/Tracing আইডি ধরে রাখুন যেন সার্ভিস টু সার্ভিস রিকোয়েস্ট ট্র্যাক করা যায়।
* Observability একটি টিম এফোর্ট—ডেভেলপারকে কোডে Instrumentation করতে হয় এবং DevOps টিমকে এর মনিটরিং পাইপলাইন কনফিগার করতে হয়। 