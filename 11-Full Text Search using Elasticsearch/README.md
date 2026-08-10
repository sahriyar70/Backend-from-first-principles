#  Full Text Search using Elasticsearch

> **Source Video:** [15. Full text search using Elasticsearch for blazingly fast search](https://www.youtube.com/watch?v=7_sovzAhRSM)  
> **Channel:** Sriniously

---

##  Executive Overview (সারসংক্ষেপ)

একটি বড় স্কেলের অ্যাপ্লিকেশনে (যেমন: E-commerce, Blogging Platform) প্রথাগত Relational Database (যেমন: PostgreSQL/MySQL)-এ `LIKE` বা `ILIKE` কোয়েরি দিয়ে সার্চ করলে ডাটা বাড়ার সাথে সাথে কোয়েরি পারফরম্যান্স প্রচণ্ড ধীরগতির হয়ে পড়ে । 

এই সমস্যার সমাধান, টাইপো হ্যান্ডলিং (Typo Tolerance), এবং প্রাসঙ্গিক সার্চ রেজাল্ট (Relevance Scoring) নিশ্চিত করতে **Full-Text Search Engines** (যেমন: **Elasticsearch**) ব্যবহার করা হয় ।

---

##  Why Traditional Database Search Fails (কেন RDBMS সার্চ ধীরগতির?)

একটি Relational Database-কে লাইব্রেরিয়ানের (Librarian) সাথে তুলনা করা যায় :
* **Sequential Scan:** ডাটাবেসে `ILIKE '%keyword%'` টাইপ কোয়েরি চালালে ডাটাবেসকে প্রতিটি Row বা Document লাইন বাই লাইন চেক করতে হয়, যাকে Full Table Scan বলে ।
* **No Relevance Concept:** প্রথাগত ডাটাবেসের নির্দিষ্ট কোনো Relevance Ranking বা Scoring থাকে না; ফলে সার্চের জন্য সবচেয়ে দরকারী ফলটি উপরে না-ও আসতে পারে ।
* **Scalability Bottleneck:** মিলিয়ন বা বিলিয়ন রেকর্ডের ক্ষেত্রে RDBMS-এর মেলাতে কয়েক সেকেন্ড থেকে মিনিট সময় লেগে যেতে পারে ।

---

##  The Core Solution: Inverted Index (ইনভার্টেড ইনডেক্স)

Elasticsearch এবং অন্যান্য Full-Text Search ইঞ্জিনের ভিত্তি হলো **Inverted Index** । 

* **How it Works:** সাধারণ ইনডেক্সে document $\rightarrow$ words ম্যাপিং থাকে। কিন্তু **Inverted Index**-এ শব্দগুলোকে (Words/Terms) কী (Key) হিসেবে রেখে সেগুলোর বিপরী‌তে কোন কোন ডকুমেন্টে এবং কোন কোন পজিশনে শব্দটি আছে তার ম্যাপিং রাখা হয় ।
* **Underlying Technology:** Elasticsearch-এর ভেতরে মূলত **Apache Lucene** লাইব্রেরি ব্যবহার করা হয়, যা এই Inverted Index টেকনোলজি পরিচালনা করে ।

---

##  Key Features of Elasticsearch

### 1. Relevance Scoring (BM25 Algorithm)
Elasticsearch প্রতিটি সার্চ রেজাল্টের জন্য **Relevance Score** গণনা করে :
* **Term Frequency (TF):** কোনো ডকুমেন্টে শব্দটি কতবার এসেছে ।
* **Document Frequency (DF):** পুরো ডাটাবেসের সবগুলো ডকুমেন্টের মধ্যে শব্দটি কতটা কমন বা রেয়ার।
* **Field Boosting:** যদি খোঁজা শব্দটি ডকুমেন্টের Content-এর চেয়ে **Title**-এ পাওয়া যায়, তবে তাকে বেশি প্রফেশনাল ও প্রাসঙ্গিক ধরে Score বাড়িয়ে দেওয়া যায় ।

### 2. Typo Tolerance & Context Understanding
ব্যবহারকারী ভুল স্পেলিং (যেমন: `treading` দিয়ে `trending` বা `laptp` দিয়ে `laptop`) টাইপ করলেও সার্চ ইঞ্জিন টেক্সট প্যাটার্ন ও কনটেক্সট বুঝে সঠিক রেজাল্ট দিয়ে দেয় ।

### 3. Autocomplete / Type-ahead Support
Google বা Amazon-এর মতো ব্যবহারকারী টাইপ করা মাত্রই দ্রুত পরামর্শ দেওয়ার জন্য টাইপ-অ্যাহেড (Type-ahead) ফিচার সহজে ইমপ্লিমেন্ট করা যায় ।

---

##  Benchmark Comparison: PostgreSQL vs Elasticsearch

ভিডিওর ডেমোতে ৫০,০০০ (50k) রিভিউ ডাটার ওপর পরীক্ষা চালিয়ে দেখা যায় :

| Search Criteria | PostgreSQL (`ILIKE`) | Elasticsearch |
| :--- | :--- | :--- |
| **Simple Search Execution Time** | ~3,000ms – 7,500ms (3–7.5s) | **~500ms – 1,000ms (0.5–1s)**  |
| **Pattern Matching Method** | Row-by-Row Scan | Inverted Index Lookup |
| **Typo Tolerance** |  No |  Yes |
| **Relevance Ranking** |  No |  Yes |

---

##  Common Use Cases & Recommendations

1. **Log Management (ELK Stack):** Elasticsearch-কে **Elasticsearch, Logstash, Kibana (ELK Stack)** সিস্টেমে বিশাল বড় সাইজের Server Log বিশ্লেষণ ও খোঁজার কাজে ব্যাপকভাবে ব্যবহার করা হয় ।
2. **E-commerce & Content Platforms:** প্রোডাক্ট সার্চ, ব্লগ পোস্ট সার্চ, বা ফিল্টারিং ফিচার তৈরিতে ব্যবহার করা হয়।
3. **Alternative for Small Projects:** ছোটখাটো কাজের জন্য আলাদা Elasticsearch ক্লাস্টার সেটআপ না করে **PostgreSQL-এর ইনবিল্ট Full-Text Search** ফিচারও ব্যবহার করা যেতে পারে ।

---
