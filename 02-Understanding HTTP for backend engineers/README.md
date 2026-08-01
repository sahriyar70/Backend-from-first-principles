#  Understanding HTTP for Backend Engineers

এই নোটে ব্যাকএন্ড ডেভেলপমেন্টের জন্য অত্যন্ত গুরুত্বপূর্ণ বিষয় **HTTP প্রোটোকল** এর মূল ধারণাগুলো সহজ ও বিস্তারিতভাবে সাজিয়ে উপস্থাপন করা হলো।

---

##  ১. HTTP এর মূল দুইটি ধারণা (Core Concepts)

### ১. Statelessness (স্টেটলেসনেস)
* **সংজ্ঞা:** সার্ভার পূর্ববর্তী কোনো রিকোয়েস্ট বা ইন্টারেকশনের তথ্য মনে রাখে না।
* **কিভাবে কাজ করে:** প্রতিটি HTTP রিকোয়েস্টের সাথে প্র প্রয়োজনীয় সব তথ্য (যেমন: Headers, Tokens, Credentials) পাঠাতে হয়। 
* **সুবিধা:** 
  * আর্কিটেকচার সহজ হয়।
  * **Scalability:** একাধিক সার্ভারে লোড ভাগ করে দেয়া সহজ হয়।
* **State Management:** লগইন অবস্থা বা কার্ট আইটেম মনে রাখার জন্য আমরা Cookie, Session বা Token ব্যবহার করি।

### ২. Client-Server Model
* ক্লায়েন্ট (যেমন: Browser, Mobile App) রিকোয়েস্ট পাঠায়।
* সার্ভার রিকোয়েস্ট প্রসেস করে রেসপন্স (HTML, JSON, Text ইত্যাদি) পাঠায়।
* **মূল বিষয়:** যোগাযোগ সবসময় ক্লায়েন্টই শুরু করে।

---

##  ২. ট্রান্সপোর্ট লেয়ার ও OSI Model

* HTTP অ্যাপ্লিকেশন লেয়ারের (Layer 7) প্রোটোকল।
* ডেটা আদান-প্রদানের নির্ভরযোগ্যতার জন্য HTTP নিচে **TCP (Transmission Control Protocol)** ব্যবহার করে (যা Three-way Handshake এর মাধ্যমে কানেকশন তৈরি করে)।
* HTTP এবং HTTPS এর মূল বিষয়বস্তু এক, শুধু HTTPS-এ TLS/SSL এনক্রিপশন থাকে যা সিকিউরিটি নিশ্চিত করে।

---

## ৩. HTTP এর সংস্করণসমূহ (Versions)

* **HTTP 1.0:** প্রতি রিকোয়েস্টের জন্য নতুন কানেকশন তৈরি হতো (ধীরগতি সম্পন্ন)।
* **HTTP 1.1:** Persistent Connections (Keep-Alive) চালু হয়। একই কানেকশন দিয়ে একাধিক রিকোয়েস্ট সম্ভব হয়।
* **HTTP 2.0:** Multiplexing এবং Binary Framing যোগ হয়। একই সাথে একাধিক ডেটা স্ট্রীম পাঠানো যায়।
* **HTTP 3.0:** UDP-ভিত্তিক **QUIC** প্রোটোকল ব্যবহার করে, যা latency কমায় এবং দ্রুত সংযোগ নিশ্চিত করে।

---

##  ৪. HTTP Structure (মেসেজ স্ট্রাকচার)


### Request Message Body:
GET /index.html HTTP/1.1
Host: [www.example.com](https://www.example.com)
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en;q=0.9

1. **Method & Path:** (যেমন: `GET /api/user`)
2. **HTTP Version:** (যেমন: `HTTP/1.1`)
3. **Headers:** Key-Value আকারে অতিরিক্ত তথ্য (যেমন: `Host`, `Authorization`).
4. **Blank Line:** হেডার ও বডির মাঝে ফাঁকা লাইন।
5. **Request Body:** ক্লায়েন্ট থেকে পাঠানো ডেটা (JSON/Form Data).

### Response Message Body:
HTTP/1.1 200 OK
Date: Sat, 01 Aug 2026 10:00:00 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
Content-Length: 1256
Cache-Control: max-age=3600

1. **Status Code & Message:** (যেমন: `200 OK`)
2. **Response Headers:** (যেমন: `Content-Type`, `Cache-Control`)
3. **Response Body:** সার্ভার থেকে পাঠানো ডেটা।

---

##  ৫. HTTP Headers

হেডার হলো পার্সেলের ওপর লেখা ঠিকানার মতো metadata।


* **Request Headers:** `User-Agent`, `Authorization`, `Accept`.
* **General Headers:** `Date`, `Cache-Control`, `Connection`.
* **Representation Headers:** `Content-Type`, `Content-Length`, `Content-Encoding`.
* **Security Headers:** `HSTS`, `Content-Security-Policy (CSP)`, `X-Frame-Options`.

---

## ৬. HTTP Methods (মেথডসমূহ)

| Method | ব্যবহার | Idempotent? |
| :--- | :--- | :---: |
| **GET** | ডেটা রিড্রাইভ/ফেচ করা | Yes |
| **POST** | নতুন ডেটা তৈরি করা | No |
| **PUT** | পুরো রিসোর্স প্রতিস্থাপন/আপডেট | Yes |
| **PATCH** | আংশিক আপডেট | No |
| **DELETE** | রিসোর্স মুছে ফেলা | Yes |
| **OPTIONS** | সার্ভারের সাপোর্ট ক্ষমতা জানতে | Yes |

* **Idempotent:** একই রিকোয়েস্ট যতবারই পাঠানো হোক, সার্ভারে ফলাফল সবসময় একই থাকবে।

---

## ৭. CORS (Cross-Origin Resource Sharing)

ব্রাউজারের **Same-Origin Policy**-র কারণে এক ডোমেইন থেকে অন্য ডোমেইনে রিকোয়েস্ট পাঠালে ব্লকিং আটকানোর মেকানিজমই হলো CORS।

1. **Simple Request:** `GET`/`POST` দিয়ে সাধারণ হেডারে রিকোয়েস্ট যায়, সার্ভার `Access-Control-Allow-Origin` রেসপন্স পাঠালে ব্রাউজার অনুমতি দেয়।
2. **Preflight Request:** জটিল রিকোয়েস্টে (যেমন: JSON বডি, `PUT`/`DELETE` মেথড, `Authorization` হেডার) মূল রিকোয়েস্টের আগে ব্রাউজার একটি `OPTIONS` রিকোয়েস্ট পাঠিয়ে সার্ভারের অনুমতি যাচাই করে।

---

## ৮. HTTP Status Codes

* **1xx (Informational):** `100 Continue`
* **2xx (Success):**
  * `200 OK` (সফল)
  * `201 Created` (নতুন রিসোর্স তৈরি হয়েছে)
  * `204 No Content` (সফল, কিন্তু কোনো বডি নেই)
* **3xx (Redirection):**
  * `301 Moved Permanently`
  * `302 Found (Temporary)`
  * `304 Not Modified` (ক্যাশ ব্যবহার করার জন্য)
* **4xx (Client Errors):**
  * `400 Bad Request`
  * `401 Unauthorized` (অথেন্টিকেশন প্রয়োজন)
  * `403 Forbidden` (অনুমতি নেই)
  * `404 Not Found`
  * `409 Conflict`
  * `429 Too Many Requests` (Rate Limit)
* **5xx (Server Errors):**
  * `500 Internal Server Error`
  * `502 Bad Gateway`
  * `503 Service Unavailable`
  * `504 Gateway Timeout`

---

##  ৯. Caching & Optimization

* **Caching Headers:** `Cache-Control: max-age=...`, `ETag`, `Last-Modified`.
* **Conditional Requests:** `If-None-Match` বা `If-Modified-Since` পাঠিয়ে সার্ভারে ডেটা পরিবর্তন হয়েছে কিনা যাচাই করা হয় (পরিবর্তন না হলে `304 Not Modified` ফেরত আসে)।
* **Compression:** `Accept-Encoding: gzip` ব্যবহার করে সার্ভার থেকে বড় ফাইল ছোট/কমপ্রেস করে দ্রুত ক্লায়েন্টে পাঠানো যায়।
* **Multipart & Streaming:**
  * বড় ফাইল আপলোডের জন্য **Multipart Form Data** (Boundary ভিত্তিক) ব্যবহার করা হয়।
  * বড় ডেটা বা রেসপন্সের জন্য **Chunked Transfer / Event Stream (`text/event-stream`)** ব্যবহার করা হয়।