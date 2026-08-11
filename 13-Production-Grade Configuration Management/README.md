# Production-Grade Configuration Management
## প্রডাকশন-গ্রেড কনফিগারেশন ম্যানেজমেন্ট নির্দেশিকা

সফটওয়্যার বা ব্যাকএন্ড অ্যাপ্লিকেশনের ডিএনএ (DNA) হলো **Configuration**। এটি নির্ধারণ করে বিভিন্ন এনভায়রনমেন্টে (Development, Staging, Production) একটি অ্যাপ কীভাবে আচরণ করবে।

---

##  Core Philosophy / মূল ধারণা

সাধারণত "কনফিগারেশন" বললেই সবাই ডাটাবেস পাসওয়ার্ড, সিপ্রেট কি (Secret Keys) বা JWT Secret-এর কথা ভাবেন। কিন্তু বাস্তবে কনফিগারেশন ম্যানেজমেন্ট এরচেয়েও অনেক বড় বিষয়।

> **Key Takeaway:** ব্যাকএন্ড অ্যাপ চালু হওয়া থেকে শুরু করে কীভাবে বাহ্যিক সার্ভিসের সাথে যুক্ত হবে, কোন ফিচার এনাবল (Enable) থাকবে, কোন লগে ডেটা যাবে—সবকিছু পরিচালনা করে কনফিগারেশন। এটি কেন্দ্রীয়ভাবে ম্যানেজ না করলে তৈরি হয় **Configuration Chaos**।

---

##  1. Types of Configurations / কনফিগারেশনের ধরনসমূহ

| টাইপ (Type) | বিবরণ ও বিষয়বস্তু | উদাহরণ |
| :--- | :--- | :--- |
| **Application Settings** | অ্যাপের সাধারণ মোড এবং সার্ভারের আচরণ নির্ধারণ করে। | Port, Log Level (`debug`, `info`), Timeout values, Connection Pool Size |
| **Database Config** | ডাটাবেসের সাথে কানেক্ট করার সমস্ত তথ্য। | Host, Port, Username, Password, Database Name, Timeout |
| **External Services** | থার্ড-পার্টি অ্যাপের সাথে কানেক্ট করার জন্য প্রয়োজন। | Stripe API Key (Payment), Resend/Mailchimp (Email), Clerk/Auth0 |
| **Feature Flags** | কোড পুনরায় ডেপ্লয় না করে লাইভ সিস্টেমে কোনো ফিচার অন/অফ করার উপায়। | New vs Old Checkout flow toggle, A/B Testing |
| **Infra & Security** | সিস্টেমের নিরাপত্তা ও অবকাঠামোগত কনফিগ। | JWT Secret, Session Secrets, CORS policies, SSL Keys |
| **Business Rules** | ব্যবসায়িক লজিকের নিয়ম যা পরিবর্তন হতে পারে। | Max order quantity, Daily transaction limits |

---

##  2. Configuration Sources & Storage / কনফিগ কোথায় ও কীভাবে রাখবেন

### ১. Environment Variables (`.env`)
* **ব্যবহার:** সবচেয়ে জনপ্রিয় ও সাধারণ পদ্ধতি।
* **কিভাবে কাজ করে:** প্রসেস বা অপারেটিং সিস্টেম লেভেলে ভেরিয়েবল লোড করে রাখা হয়। স্থানীয় কাজের জন্য `.env` ফাইল ব্যবহার করা হয়।

### ২. Files (`YAML`, `JSON`, `TOML`)
* **কেন ব্যবহার করবেন:** জটিল স্ট্রাকচার ও নেস্টেড কনফিগারেশন রাখার জন্য।
* **Best Choice:** `YAML` অথবা `TOML` ফাইল বেশি পছন্দনীয়, কারণ এতে মন্তব্য (Comments) লেখা যায়, যা `JSON`-এ সম্ভব নয়।

### ৩. Key-Value Stores & Dedicated Cloud Services
* **পদ্ধতি:** বড় ডিস্ট্রিবিউটেড সিস্টেম বা মাইক্রোসার্ভিসে কেন্দ্রীয় সিপ্রেট ম্যানেজার ব্যবহার করা হয়।
* **জনপ্রিয় টুলস:** HashiCorp Vault, AWS Parameter Store, Azure Key Vault, Google Secret Manager, Consul।

---

##  3. Environment-based Priorities / এনভায়রনমেন্ট অনুযায়ী কনফিগ

একই অ্যাপ বিভিন্ন এনভায়রনমেন্টে ভিন্নভাবে আচরণ করা উচিত:

[ Code Base ]  ──► (Dev Config)     ──► Output: Local Dev Environment
──► (Staging Config) ──► Output: QA / Staging Environment
──► (Prod Config)    ──► Output: Production Environment

* **Development (Dev):** 
  * **মূল লক্ষ্য:** ডেভলপারের প্রোডাক্টিভিটি ও ডিবাগিং সুবিধা।
  * **কনফিগ:** `Log Level = DEBUG`, `Database Pool Size = 10`
* **Staging:** 
  * **মূল লক্ষ্য:** প্রোডাকশনের মতো পরিবেশ সিমুলেট করা, কিন্তু ক্লাউড খরচ কম রাখা।
  * **কনফিগ:** `Log Level = INFO`, `Database Pool Size = 2`
* **Production (Prod):** 
  * **মূল লক্ষ্য:** হাই পারফর্মেন্স, সিকিউরিটি এবং ১০০% রিলায়েবিলিটি।
  * **কনফিগ:** `Log Level = INFO/WARN`, `Database Pool Size = 50+`

---

##  4. Best Practices & Security Guidelines / নিরাপত্তা ও সঠিক ব্যবহারের নিয়ম

###  Never Hardcode Secrets
কখনোই সরাসরি কোডের ভেতরে ডাটাবেস পাসওয়ার্ড, API Key বা কোনো গোপন তথ্য লিখা জাবেনা না। সব কনফিগ ফাইলের বাইরে বা সিক্রেট ম্যানেজারে রাখতেহবে।

###  Encryption & Access Control
* **At Rest & In Transit:** সিক্রেট ম্যানেজারগুলো তথ্য সেভ করার সময় (At rest) এবং আদান-প্রদানের সময় (In transit) এনক্রিপ্ট করে।
* **Principle of Least Privilege:** যার যতটুকু অ্যাক্সেস প্রয়োজন তাকে ততটুকুই দিন। ফ্রন্টএন্ড ডেভলপারকে ব্যাকএন্ড ডাটাবেসের সিক্রেট দেখার অনুমতি দেওয়া যাবে না।
* **Key Rotation:** নির্দিষ্ট সময় পর পর API Key ও পাসওয়ার্ড পরিবর্তন (Rotate)  করতে হবে।

###  MUST DO: Always Validate Configs (সবচেয়ে গুরুত্বপূর্ণ)
অ্যাপ্লিকেশন চালু বা Start করার সময় প্রতিটি Environment Variable এবং Configuration ডেটা সঠিকভাবে লোড হয়েছে কি না তা **Validate** করে নিতে হবে।

* **কেন করবেন:** যদি কোনো ভেরিয়েবল মিসিং থাকে, তবে প্রডাকশনে রানটাইমে অ্যাপ ক্র্যাশ করার চেয়ে অ্যাপ চালু হওয়ার সময়েই `Fail Fast` হয়ে স্টপ হওয়া ভালো।
* **ভ্যালিডেশন টুলস:**
  * **TypeScript / Node.js:** Zod
  * **Go:** Go Validator
  * **Python:** Pydantic

```json
// উদাহরণ: Zod দিয়ে ভ্যালিডেশনের ডেমো ধারণা
const configSchema = zod.object({
  PORT: zod.number().default(8080),
  DATABASE_URL: zod.string().url(),
  JWT_SECRET: zod.string().min(32)
});