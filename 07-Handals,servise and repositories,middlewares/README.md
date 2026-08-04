#  Backend Architecture: Controllers, Services, Repositories, Middlewares & Request Context



---

##  Executive Overview
একটি Scalable, Maintainable এবং Clean Backend System তৈরি করার জন্য Codebase-কে নির্দিষ্ট Architecture Pattern মেনে ভাগ করা অত্যন্ত জরুরি। এই নোটটিতে Request Life Cycle-এর মূল উপাদানগুলো যেমন: Controller/Handler, Service Layer, Repository Layer, Middleware এবং Request Context-এর কাজ ও দায়িত্ব ব্যাখ্যা করা হয়েছে।

---

## 1.  Controller / Handler Layer
Controller বা Handler হলো ব্যাকএন্ডে রিকোয়েস্ট আসার পর প্রথম এন্ট্রি পয়েন্ট।

###  Key Responsibilities:
* **Request Binding / Deserialization:**
  * Client থেকে আসা HTTP Request (JSON/Query Param/Body) রিসিভ করা এবং Programming Language-এর Native Data Format-এ (যেমন: Go Struct, Python Dict, JS Object) রূপান্তর করা ।
  * Parsing ব্যর্থ হলে **`400 Bad Request`** রেসপন্স পাঠানো হয়।
* **Validation & Transformation:**
  * incoming ডেটা ভ্যালিডেট করা (সব প্রয়োজনীয় ফিল্ড আছে কিনা, ডেটা টাইপ ঠিক আছে কিনা) ।
  * অপশনাল ফিল্ডে Default Value সেট করা (Data Transformation) ।
* **Service Layer Call:**
  * ভ্যালিডেটেড ডেটা Service Layer-এ পাস করা।
* **HTTP Response Handling:**
  * সার্ভিস লেয়ার থেকে ডেটা ফেরত আসার পর সঠিক HTTP Status Code (যেমন: `200 OK`, `201 Created`, `204 No Content`, `500 Internal Error`) সহ Client-কে রেসপন্স তৈরি করে পাঠানো ।

>  **Principle:** Controller শুধুমাত্র HTTP Protocol (Request/Response) সম্পর্কিত বিষয়াদি হ্যান্ডেল করবে, কোনো Business Logic বা Database Query চালাবে না।

---

## 2.  Service Layer (Business Logic)
Service Layer হলো অ্যাপ্লিকেশনের মূল মস্তিষ্ক বা Business Logic Executer।

###  Key Responsibilities:
* **Pure Business Logic:**
  * বিজনেস লজিক প্রসেস করা (যেমন: হিসাব-নিকাশ করা, Email পাঠানো, Third-party API Call করা, Notification পাঠানো) ।
* **Orchestration:**
  * প্রয়োজনে একের অধিক Repository Method বা External Service ডেকে ডেটা প্রসেস বা Merge করা ।
* **HTTP Decoupled:**
  * সার্ভিস লেয়ারের কোনো ধারণা থাকে না কোন HTTP Protocol থেকে রিকোয়েস্ট এসেছে। এটি সাধারণ Function-এর মতো ইনপুট নেয় এবং আউটপুট রিটার্ন করে ।

---

## 3.  Repository Layer (Database Layer)
Repository Layer শুধুমাত্র ডেটাবেসের সাথে লেনদেন বা Communication-এর কাজ করে।

###  Key Responsibilities:
* **Database Operations:**
  * Database Query তৈরি ও এক্সিকিউট করা (CRUD Operations: Create, Read, Update, Delete) ।
* **Single Responsibility:**
  * প্রতিটি Repository Method নির্দিষ্ট একটি মাত্র কাজ করবে (যেমন: `getAllBooks()`, `getBookById(id)`) ।
* **Data Retrieval:**
  * Database থেকে প্রাপ্ত কাঁচা ডেটা সার্ভিস লেয়ারে রিটার্ন করা।

---

##  Summary of Request Flow
```text
Client ──(HTTP Request)──> Middleware ──> Controller/Handler ──> Service Layer ──> Repository Layer ──> Database
                                                                                                            │
Client <──(HTTP Response)── Controller/Handler <── Service Layer <── Repository Layer <─────────────────────┘