#  Complete REST API Design Guide

> **Source Video:** [11. Complete REST API Design](https://www.youtube.com/watch?v=RG6q57DwV8Y)  
> **Channel:** Sriniously

---

##  Executive Overview (সারসংক্ষেপ)

API (Application Programming Interface) ডিজাইন করা একজন Backend Engineer-এর অন্যতম প্রধান দায়িত্ব । এই গাইডটিতে RESTful API ডিজাইনের মৌলিক নীতি, ইতিহাস, বেস্ট প্র্যাকটিস এবং ইন্ডাস্ট্রি স্ট্যান্ডার্ড সম্পর্কে আলোচনা করা হয়েছে, যার মাধ্যমে সহজেই Scalable এবং Intuitive API তৈরি করা সম্ভব ।

যে কোনো কোড (Node.js, Go, Python ইত্যাদি) লেখার আগেই OpenAPI/Swagger, Postman, বা Insomnia-র মতো ডিজাইনিং টুলস ব্যবহার করে API-এর Interface আগে **ডিজাইন** করে নেওয়া উচিত ।

---

##  History of the Web & REST (ইতিহাস)

* **১৯৯০ (Worldwide Web):** Tim Berners-Lee বিশ্বব্যাপী তথ্য শেয়ার করার জন্য 'Worldwide Web' প্রজেক্ট শুরু করেন ।
* **মূল ৩টি আবিষ্কার:**
  1. **URI** (Uniform Resource Identifier) 
  2. **HTTP** (Hypertext Transfer Protocol) 
  3. **HTML** (Hypertext Markup Language) 
* **HTTP 1.1 Standardization:** পরবর্তীতে Roy Fielding এবং Tim Berners-Lee একসাথে কাজ করে Web-এর Scalability বাড়ানোর জন্য HTTP 1.1 স্ট্যান্ডার্ড তৈরি করেন ।
* **২০০০ (REST):** Roy Fielding তাঁর PhD থিসিসে **REST** (*Representational State Transfer*) আর্কিটেকচার পেস করেন ।

---

##  What is REST? (REST কী?)

REST হলো এমন একটি Architectural Style যা **Resource** এবং তার **State** (অবস্থা)-এর ওপর ভিত্তি করে কাজ করে ।

* **State:** কোনো একটি নির্দিষ্ট Resource-এর বর্তমান অবস্থা বা Properties ।
* **Transfer:** Client এবং Server-এর মধ্যে Resource-এর এই State আদান-প্রদান করাকে State Transfer বলা হয় ।

---

##  REST API Design Best Practices (ডিজাইন গাইডলাইন)

### ১. Naming & Resource Path Conventions
* **Use Plural Nouns:** Resource Path সব সময় বহুবচন (Plural) হওয়া উচিত (যেমন: `/users`, `/organizations`), একবচন নয় ।
* **Hierarchical Relationships:** সম্পর্ক প্রকাশ করতে Hierarchical Path ব্যবহার করুন:
  * `GET /organizations/{org_id}/members`
* **Avoid Abbreviations:** সংক্ষিপ্ত রূপ এড়িয়ে স্পষ্ট শব্দ ব্যবহার করুন (যেমন: `desc`-এর বদলে `description`) ।

---

### ২. HTTP Methods & Usage

| Action / Purpose | HTTP Method | Example Path |
| :--- | :--- | :--- |
| **Fetch Resource(s)** | `GET` | `GET /users` |
| **Create Resource** | `POST` | `POST /users` |
| **Update Resource (Partial)** | `PATCH` | `PATCH /users/{id}` |
| **Replace Resource (Full)** | `PUT` | `PUT /users/{id}` |
| **Delete Resource** | `DELETE` | `DELETE /users/{id}` |
| **Custom / Non-CRUD Actions** | `POST` / Action Path | `POST /orders/{id}/cancel`  |

---

### ৩. Sane Defaults & Input Validation
* **Sane Defaults:** অপশনাল ফিল্ডের ক্ষেত্রে (যেমন: `status`), Client কোনো মান না পাঠালে Server থেকে ডিফল্ট মান (যেমন: `status: "active"`) সেট করে দেওয়া উচিত ।
* **Intuitive Payloads:** Request এবং Response-এর Payload সহজ, স্পষ্ট ও অনুমেয় হতে হবে [02:01:11]।

---

## API Development Workflow (ওয়ার্কফ্লো)

1. **Design First:** কোডিং শুরুর আগে **Swagger/OpenAPI**, **Postman** বা **Insomnia** দিয়ে API স্কেচ করা উচিত[02:02:27]।
2. **Review & Iterate:** Client/Consumer-এর দৃষ্টিকোণ থেকে API-এর সাবলীলতা যাচাই করা উচিত [02:02:34]।
3. **Implementation:** এরপর আপনার পছন্দের প্রোগ্রামিং ল্যাঙ্গুয়েজ বা ফ্রেমওয়ার্কে কোডিং শুরু করুন [02:02:55]।

---
*Created based on notes from [Sriniously's REST API Design Video](https://www.youtube.com/watch?v=RG6q57DwV8Y).*