# Complete Database Guide with PostgreSQL
## Database Best Practices (ডাটাবেজ গাইডলাইন)

### ১. Fundamentals & Storage (ডাটাবেজ মূলনীতি)
* **Data Persistence:** সিস্টেম বন্ধ বা ক্র্যাশ করলেও ডেটা যেন স্থায়ীভাবে সংরক্ষিত থাকে তা নিশ্চিত করা ।
* **RAM vs Disk:** Caching (যেমন: Redis) ব্যবহৃত হয় Primary Memory বা RAM-এ, অন্যদিকে মূল Database (যেমন: Postgres) স্থায়ী Disk-এ ডেটা সেভ করে ।

---

### ২. RDBMS vs NoSQL (ডাটাবেজের প্রকারভেদ)
1. **Relational (RDBMS):** নির্দিষ্ট Schema এবং ACID Properties নিশ্চিত করে (যেমন: PostgreSQL, MySQL) ।
2. **Non-Relational (NoSQL):** ফ্লেক্সিবল Schema বা Document-ভিত্তিক ডেটা সংরক্ষণে ব্যবহৃত হয় (যেমন: MongoDB) ]।

---

### ৩. Why PostgreSQL? (কেন পোস্টগ্রেস ব্যবহার করবেন)
* **Native JSON Support:** `JSONB` ফিল্ডের সাহায্যে Schema ছাড়াই ফ্লেক্সিবল ডেটা সংরক্ষণ করা সম্ভব ।
* **Scalability & Extensions:** জটিল প্রজেক্টের জন্য PostGIS, pgvector সহ বিশাল এক্সটেনশন সিস্টেম রয়েছে ।

---

### ৪. Advanced Features (উন্নত কৌশলসমূহ)
* **Database Migrations:** স্কিমার পরিবর্তন সঠিকভাবে ট্রাক করতে Migration Script ব্যবহার করা উচিত ।
* **Indexing:** দ্রুত ডেটা খুঁজে পাওয়ার জন্য বারবার কোয়েরি করা কলামে Index ব্যবহার করা উচিত ।
* **Triggers & Functions:** ডেটাবেজে কোনো রো আপডেট হলে স্বয়ংক্রিয়ভাবে `updated_at` টাইমস্ট্যাম্প আপডেট করার কাজে ব্যবহৃত হয় ।
* **Parameterized Queries:** SQL Injection আক্রমণ প্রতিরোধে সবসময় প্যারামিটারের মাধ্যমে ইনপুট পাস করা উচিত ।

---

