#  System Architecture & Caching Mechanics

This repository contains structured notes and technical breakdowns on **Caching Mechanics, System Design Patterns, and In-Memory Data Stores** based on the video tutorial *"13. Caching, the secret behind it all"* by **Sriniously**.

---

##  Executive Summary
**Caching (ক্যাশিং)** হলো আধুনিক সিস্টেম আর্কিটেকচারের একটি গুরুত্বপূর্ণ অপ্টিমাইজেশন টেকনিক। এর মূল উদ্দেশ্য হলো ডাটাবেস বা প্রাইমারি সোর্স থেকে বারবার ডাটা প্রসেস না করে, ঘন ঘন ব্যবহৃত ডাটার একটি অংশ (Subset) দ্রুত এক্সেসযোগ্য মেমোরিতে (যেমন: RAM বা Edge Node) সেভ রাখা। এর ফলে **Latency (সময়/বিলম্ব)** এবং **Computational Overhead (সার্ভারের পরিশ্রম)** উভয়ই নাটকীয়ভাবে কমে যায়।

---

## 📖 Table of Contents
1. [Core Definition & Technical Concept](#1-core-definition--technical-concept)
2. [Real-World Industry Case Studies](#2-real-world-industry-case-studies)
3. [The Three Levels of Caching](#3-the-three-levels-of-caching)
4. [Deep Dive: Network-Level Caching](#4-deep-dive-network-level-caching)
5. [Deep Dive: Hardware & Memory Architecture](#5-deep-dive-hardware--memory-architecture)
6. [Backend Caching Strategies](#6-backend-caching-strategies)
7. [Cache Eviction Policies](#7-cache-eviction-policies)
8. [Practical Backend Use Cases](#8-practical-backend-use-cases)

---

## 1. Core Definition & Technical Concept

- **Plain English (সহজ ভাষায়):** ক্যাশিং হলো এমন একটি মেকানিজম বা প্রযুক্তি, যার মাধ্যমে কোনো নির্দিষ্ট কাজ করার সময় এবং সার্ভারের শক্তি/পরিশ্রম কমানো যায়।
- **Technical Definition (কারিগরি সংজ্ঞায়):** মূল ডাটাবেস (Primary Storage) থেকে ডাটার পুরো অংশ না নিয়ে, ব্যবহারের ওপর নির্ভর করে ডাটার একটি নির্দিষ্ট ছোট অংশ (Subset) দ্রুত এক্সেসযোগ্য লোকেশনে সেভ রাখাকে ক্যাশিং বলে।

### Key Metrics Handled (মূল বিষয়সমূহ):
- **Cache Hit (ক্যাশ হিট):** ইউজার যে ডাটা চাচ্ছেন তা যদি ক্যাশ মেমোরিতে পাওয়া যায়। এতে ক্লায়েন্ট সাথে সাথে ইনস্ট্যান্ট রেসপন্স পায়।
- **Cache Miss (ক্যাশ মিস):** ইউজার যে ডাটা চাচ্ছেন তা ক্যাশে না থাকা। এই ক্ষেত্রে মেইন ডাটাবেস থেকে ডাটা ফেচ বা প্রসেস করে ক্যাশে সেভ করা হয় এবং ক্লায়েন্টকে পাঠানো হয়।

---

## 2. Real-World Industry Case Studies

###  A. Google Search (Compute Avoidance - অতিরিক্ত গণনা এড়ানো)
- **Problem (সমস্যা):** প্রতিদিন লাখ লাখ ইউজার গুগলে *"weather today"* বা একই জাতীয় তথ্য অনুসন্ধান করেন। প্রতিবার সার্চের জন্য বিলিয়ন বিলিয়ন ওয়েব পেজ ক্রল, ইনডেক্স এবং র‍্যাঙ্ক করা কম্পিউটেশনালি অত্যন্ত ব্যয়বহুল (High Latency & Heavy Server Load)।
- **Solution (সমাধান):** গুগল একটি **Distributed In-Memory Cache System** ব্যবহার করে। বহুল ব্যবহৃত অনুসন্ধানের ফলাফল ক্যাশে সংরক্ষণ করা থাকে, যা *Cache Hit* হলে মূল সার্চ অ্যালগরিদম না চালিয়েই তৎক্ষণাৎ ইউজারকে দেখিয়ে দেওয়া হয়।

###  B. Netflix / Content Delivery (Large File Distribution - বিশাল ফাইল স্ট্রিম করা)
- **Problem (সমস্যা):** গ্লোবালি কোটি কোটি ইউজারের কাছে Terabytes পরিমাণ 4K/1080p ভিডিও ডাটা মার্কিন যুক্তরাষ্ট্রের মূল সার্ভার থেকে সরাসরি স্ট্রিম করলে মারাত্মক Buffering এবং High Latency তৈরি হবে।
- **Solution (সমাধান):** নেটিফ্লিক্স **CDN Edge Locations (Points of Presence - PoPs)** ব্যবহার করে। ভিডিওগুলো এনকোড করে ইউজারের ভৌগোলিক অবস্থানের সবচেয়ে কাছের সার্ভারে (যেমন: ইন্ডিয়া বা ইউরোপের PoP) ক্যাশ করে রাখা হয়, যাতে দ্রুত ও কম বাফারিংয়ে ভিডিও প্লে হয়।

###  C. X / Twitter (Heavy Computation Aggregation - ভারী হিসেব সংরক্ষণ)
- **Problem (সমস্যা):** "Trending Topics" নির্ধারণ করতে রিয়েল-টাইমে কোটি কোটি টুইট বিশ্লেষণ এবং ভারী Machine Learning অ্যালগরিদম চালাতে হয়। প্রতিবার কোনো ইউজার ট্রেন্ডিং ট্যাব রিফ্রেশ করলে নতুন করে এই হিসেব করলে সার্ভার ক্র্যাশ করবে।
- **Solution (সমাধান):** সিস্টেমটি কয়েক মিনিট পরপর পুরো ডাটার ওপর অ্যালগরিদম চালিয়ে ট্রেন্ডের রেজাল্টটি **Redis**-এর মতো ইন-মেমোরি ডাটাবেসে ক্যাশ করে রাখে। ইউজাররা ক্যাশ থেকেই সেই ডাটা সরাসরি দেখতে পান।

---

## 3. The Three Levels of Caching

ব্যাকএন্ড ইঞ্জিনিয়ার হিসেবে কাজের ক্ষেত্রে ক্যাশিং প্রধানত তিনটি স্তরে দেখা যায়:

| Layer (স্তর) | Examples / Sub-components (উদাহরণ) | Primary Role (প্রধান ভূমিকা) |
| :--- | :--- | :--- |
| **Network Level** | CDN, DNS Resolvers | ভৌগোলিক দূরত্ব, নেটওয়ার্ক হপ এবং ISP লেভেলের রেসপন্স টাইম কমায়। |
| **Hardware Level** | L1, L2, L3 CPU Caches, RAM | সিপিইউ ইন্সট্রাকশন এবং ডাটা প্রসেসিং গতি বৃদ্ধি করে। |
| **Software/Backend** | Redis, Memcached, AWS ElastiCache | অ্যাপ্লিকেশনের ডাটাবেস ক্যুয়েরি, সেশন এবং API রেসপন্স দ্রুত ক্যাশ করে। |

---

## 4. Deep Dive: Network-Level Caching

###  A. CDN (Content Delivery Network) Workflow
1. ইউজার ব্রাউজারে কোনো স্ট্যাটিক ফাইল (Image/Video/JS/CSS) চাওয়ার জন্য URL লেখেন।
2. CDN DNS সার্ভিস সেই রিকোয়েস্টটিকে ইউজারের নিকটবর্তী **Point of Presence (PoP)** বা **Edge Server**-এ পাঠায়।
3. **If Cache Hit:** এজ সার্ভারে ফাইলটি থাকলে সেখান থেকেই সরাসরি ক্লায়েন্টকে ফেরত পাঠানো হয়।
4. **If Cache Miss:** এজ সার্ভার মূল **Origin Server** থেকে ডাটা এনে নির্দিষ্ট **TTL (Time to Live)** মেয়াদে নিজের ক্যাশে রাখে এবং ইউজারকে ফেরত দেয়।

###  B. DNS Resolution Hierarchy (ডোমেইন নেম ক্যাশিং)
ডোমেইন নেম (`example.com`) থেকে IP Address রেজোলিউশনের জটিল প্রসেসটি সহজ করতে বহুতল ক্যাশিং ব্যবহৃত হয়:
1. **OS Level Cache:** প্রথমে অপারেটিং সিস্টেম নিজের স্থানীয় ডিকশনারিতে IP খুঁজে দেখে।
2. **Browser Level Cache:** আধুনিক ব্রাউজারগুলো (Chrome, Firefox) নিজস্ব DNS ক্যাশ বজায় রাখে।
3. **Recursive Resolver Cache:** ইউজারের ISP বা Public DNS (যেমন: Google `8.8.8.8`, Cloudflare `1.1.1.1`) নিজের স্থানীয় ক্যাশে আইপি সন্ধান করে।
4. **Root / TLD / Authoritative Name Servers:** আগের ধাপগুলোতে ক্যাশ মিস হলেই কেবল এই মূল ডোমেইন সার্ভারগুলোতে ক্রমান্বয়ে অনুসন্ধান করা হয়।

---

## 5. Deep Dive: Hardware & Memory Architecture 

[ CPU Core ] <---> [ L1 / L2 Cache ] <---> [ L3 Cache (Shared) ]
|
[ RAM (Main Memory) ]  <-- In-Memory Caches (Redis)
|
[ SSD / HDD (Disk Storage) ]  <-- Persistent Databases  

###  A. CPU Cache Hierarchy (L1, L2, L3)
* **L1 Cache:** সিপিইউ কোর-এর সবচেয়ে কাছে থাকে; স্টোরেজ অনেক কম কিন্তু এক্সেস স্পিড সবচেয়ে বেশি।
* **L2 Cache:** L1-এর চেয়ে একটু বড়, সিপিইউ-এর দ্বিতীয় স্তরের দ্রুত ডাটা প্রসেসিং সামলায়।
* **L3 Cache:** মাল্টি-কোর সিপিইউ-এর মধ্যে শেয়ার্ড মেমোরি হিসেবে কাজ করে।

###  B. RAM (Random Access Memory) vs. Secondary Storage (HDD/SSD)
* **Main Memory (RAM):** 
  * মেকানিক্যাল কোনো চলন্ত পার্টস নেই; সরাসরি ইলেকট্রিক্যাল সিগন্যালের মাধ্যমে মেমোরি এড্রেস থেকে ইনস্ট্যান্ট ডাটা রিড করতে পারে ($O(1)$ Complexity)।
  * **Trade-off (সীমাবদ্ধতা):** এটি Volatile (বিদ্যুৎ চলে গেলে ডাটা মুছে যায়) এবং সেকেন্ডারি ডিস্কের তুলনায় প্রতি জিবি স্পেস অত্যন্ত ব্যয়বহুল।
* **Secondary Storage (Disk):** 
  * নন-ভোলাটাইল স্টোরেজ (SSD/HDD); বিদ্যুৎ না থাকলেও ডাটা স্থায়ী থাকে, কিন্তু রিড/রাইট ল্যাটেন্সি অনেক বেশি।
* **In-Memory Stores (Redis / Memcached):** 
  * রিড ও রাইট অপারেশন সরাসরি RAM-এ পরিচালনা করে, যার ফলে মেমোরি ল্যাটেন্সি একদম কম হয়। স্থায়ী রাখার জন্য ব্যাকগ্রাউন্ডে ডিস্কে ব্যাকআপ/স্ন্যাপশট ফাইল তৈরি করে।

---

## 6. Backend Caching Strategies

###  A. Lazy Caching (Cache-Aside Pattern)
- অ্যাপ্লিকেশন রিকোয়েস্ট পেলে প্রথমে ক্যাশ চেক করে।
- **Cache Hit:** ক্যাশ থেকে সরাসরি ডাটা রিটার্ন করে।
- **Cache Miss:** মেইন ডাটাবেস থেকে ডাটা রিড করে ক্যাশে সেভ করে, তারপর ইউজারকে রিটার্ন করে।
- **Pros (সুবিধা):** শুধু ব্যবহৃত ডাটাই ক্যাশে স্থান পায়; ক্যাশ সার্ভার ডাউন হলেও ডাটাবেস থেকে কাজ চলে।
- **Cons (অসুবিধা):** প্রথমবার ডাটা রিড করার সময় ক্যাশ মিসজনিত কারণে বাড়তি সময় (Latency) লাগে; অনেক সময় ক্যাশে পুরোনো ডাটা থেকে যাওয়ার ঝুঁকি থাকে।

###  B. Write-Through Caching
- ডাটাবেসে কোনো পরিবর্তন (Insert/Update) করার সাথে সাথেই একই এক্সিকিউশন সাইকেলে ক্যাশ মেমোরিও আপডেট করা হয়।
- **Pros (সুবিধা):** ক্যাশের ডাটা সবসময় আপ-টু-ডেট ও ফ্রেশ থাকে; ক্যাশ মিস হওয়ার সুযোগ কম।
- **Cons (অসুবিধা):** প্রতিবার রাইট অপারেশনে বাড়তি সময় ও অতিরিক্ত প্রসেসিং লাগে।

---

## 7. Cache Eviction Policies

RAM-এর ক্যাপাসিটি সীমিত হওয়ায় মেমোরি ফুল হয়ে গেলে পুরাতন ডাটা ডিলিট করার নীতি নির্ধারণ করা হয়:

- **LRU (Least Recently Used):** যে ডাটাটি সবচেয়ে বেশি সময় ধরে ব্যবহার করা হয়নি (সবচেয়ে পুরোনো এক্সেস টাইম), সেটিকে মুছে নতুন ডাটা রাখা হয়।
- **LFU (Least Frequently Used):** যে ডাটাটি এক্সেস করার মোট সংখ্যা বা কাউন্টার সবচেয়ে কম, সেটিকে মেমোরি থেকে সরিয়ে দেওয়া হয়।
- **TTL (Time to Live):** ডাটা সেভ করার সময় একটি নির্দিষ্ট মেয়াদ (যেমন: ১০ মিনিট) সেট করা থাকে; মেয়াদ শেষ হলে মেমোরি থেকে অটোমেটিক মুছে যায়।
- **No Eviction:** মেমোরি ফুল হলে কোনো ডাটা ডিলিট করে না, বরং নতুন রাইট রিকোয়েস্টে Out of Memory এরর দেখায়।

---

## 8. Practical Backend Use Cases

### 1 Database Query Caching (ভারী ক্যুয়েরি এড়ানো)
যেসব জটিল SQL ক্যুয়েরিতে অনেকগুলো `JOIN` ও Aggregation থাকে (যেমন: Amazon Product Details, User Profiles), সেগুলোর ফলাফল নির্দিষ্ট TTL সহ Redis-এ ক্যাশ করে রাখা উচিত, যাতে মেইন ডাটাবেসের ওপর সিপিইউ লোড না পড়ে।

### 2 Session Storage (ইউজার সেশন রাখা)
ইউজার লগইন করার পর তৈরি হওয়া সেশন টোকেন বা JWT ডাটা Redis-এ রাখা হয়। প্রতিবার API রিকোয়েস্টে ডাটাবেসে কোয়েরি না চালিয়ে RAM থেকে মাইক্রোসেকেন্ডে সেশন ভ্যালিডেশন নিশ্চিত করা যায়।

### 3 Third-Party API Caching (এক্সটার্নাল API রেসপন্স)
থার্ড-পার্টি সার্ভিস (যেমন: Weather API, Currency Converter) ঘন ঘন কল করলে অতিরিক্ত বিলিং আসে অথবা Rate Limit শেষ হয়ে যায়। ওই ডাটা নির্দিষ্ট সময় পর্যন্ত স্থানীয় ক্যাশে সংরক্ষণ করে ব্যাকএন্ড খরচ কমায়।

### 4 API Rate Limiting (ট্রাফিক নিয়ন্ত্রণ)
বট বা অতিরিক্ত ক্ষতিকারক রিকোয়েস্ট থামানোর জন্য মিডলওয়্যারে ইউজারের IP (`X-Forwarded-For` হেডার) ধরে Redis-এ রিকোয়েস্ট কাউন্টার চালানো হয়। যেমন: ১ মিনিটে ৫০টির বেশি রিকোয়েস্ট আসলে সরাসরি `429 Too Many Requests` এরর রেসপন্স পাঠিয়ে ডাটাবেসকে রক্ষা করা যায়।

---

## 🔗 References
- **Video Source:** [13. Caching, the secret behind it all (Sriniously)](https://youtu.be/estH64OkwxU)
- **Topics:** Distributed Systems, System Design, Caching Patterns, Redis Architecture