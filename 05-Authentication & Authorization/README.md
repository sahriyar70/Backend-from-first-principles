#  Comprehensive Guide to Authentication & Authorization

ডকুমেন্টে Authentication এবং Authorization-এর নোট

---

## ঁ 1. Basic Concepts (মৌলিক ধারণা)

| বিষয় | Authentication (AuthN) | Authorization (AuthZ) |
| :--- | :--- | :--- |
| **(সংজ্ঞা)** | ব্যবহারকারীর সত্যতা বা পরিচয় যাচাই করা (Who are you?) | সিস্টেমের নির্দিষ্ট রিসোর্সে প্রবেশের অনুমতি যাচাই করা (What can you do?) |
| **কখন ঘটে?** | সিস্টেমের ভেতরে প্রবেশের পূর্বে (First step) | পরিচয় সফলভাবে যাচাই হওয়ার পর (Second step) |
| **উদাহরণ** | Username/Password, OTP, Biometrics দিয়ে লগইন | AdminPanel এক্সেস করা, ফাইল এডিট বা ডিলিটের পারমিশন থাকা |
| **ব্যর্থতার রেসপন্স** | `401 Unauthorized` | `403 Forbidden` |

---

##  2. Types of Authentication (প্রমাণীকরণের প্রকারভেদ)

### 2.1 Password-Based Authentication
* **বর্ণনা:** সবচেয়ে সাধারণ পদ্ধতি, যেখানে ব্যবহারকারী একটি আইডি/ইমেইল এবং সিক্রেট পাসওয়ার্ড প্রদান করে।
* **নিরাপত্তা টিপস:** 
  * ডেটাবেজে কখনোই প্লেন টেক্সট (Plain text) পাসওয়ার্ড সংরক্ষণ করা যাবে না।
  * শক্তিশালী হ্যাশিং অ্যালগরিদম যেমন **`Bcrypt`**, **`Argon2`**, বা **`PBKDF2`** (with Salt) ব্যবহার করা উচিত।

### 2.2 Multi-Factor Authentication (MFA / 2FA)
নিরাপত্তা নিশ্চিত করতে একাধিক মাধ্যমের ওপর ভিত্তি করে পরিচয় নিশ্চিত করা হয়:
* **Something you know:** পাসওয়ার্ড, PIN।
* **Something you have:** OTP (SMS/Email), Authenticator App (TOTP - Time-based One-Time Password), Hardware Security Key (YubiKey)।
* **Something you are:** আঙুলের ছাপ (Fingerprint), ফেস আইডি (Face Recognition), আইরিশ স্ক্যান (Iris Scan)।

### 2.3 Passwordless Authentication
* **Magic Link:** ব্যবহারকারীর ইমেইলে সরাসরি এককালীন লগইন লিঙ্ক পাঠানো হয়।
* **OTP:** মোবাইলে বা ইমেইলে ওয়ান-টাইম পিন পাঠানো হয়।
* **Passkeys / WebAuthn:** পাবলিক-প্রাইভেট কি (Public-Private Key) আর্কিটেকচার ব্যবহার করে বায়োমেট্রিক্সের মাধ্যমে পাসওয়ার্ড ছাড়াই সুরক্ষিত লগইন করা হয়।

### 2.4 Token-Based Authentication
* ক্লায়েন্ট সার্ভিস বা API থেকে ডেটা আদান-প্রদানের জন্য সিকিউর টোকেন (যেমন: **JWT**) ব্যবহার করা হয়। (বিস্তারিত নিচে দেখুন)

### 2.5 Single Sign-On (SSO) & Federated Auth
* **SSO:** একবার লগইন করে একই ইকোসিস্টেমের একাধিক সার্ভিস বা অ্যাপ এক্সেস করার প্রক্রিয়া (যেমন: Google Workspace)।
* **OAuth 2.0 / OIDC:** থার্ড-পার্টি সার্ভিস ব্যবহার করে লগইন (e.g., *Sign in with Google*, *GitHub*, *Facebook*)।

---

##  3. Token-Based Auth & Session-Based Auth Comparison

### 3.1 Session-Based Auth (Stateful)
1. ইউজার ক্রেডেনশিয়াল প্রদান করে লগইন করে।
2. সার্ভার ইউজারকে মেমোরি বা ডেটাবেজে (e.g., Redis) একটি **Session ID** তৈরি করে এবং ব্রাউজারে `HttpOnly Cookie` হিসেবে সেটি পাঠায়।
3. পরবর্তী প্রতিটি রিকোয়েস্টে ব্রাউজার কুকির মাধ্যমে Session ID পাঠায় এবং সার্ভার সেটি ডেটাবেজের সাথে মিলিয়ে দেখে।
* **সুবিধা:** সহজেই যেকোনো ইউজার সেশন রিভোক/বাতিল করা যায়।
* **অসুবিধা:** স্কেলেবিলিটির ক্ষেত্রে সমস্যা তৈরি হতে পারে (Distributed Server-এ সেশন শেয়ার করতে হয়)।

### 3.2 Token-Based Auth (Stateless - JWT)
1. ইউজার সফলভাবে লগইন করলে সার্ভার একটি সিকিউর **JSON Web Token (JWT)** জেনারেট করে ক্লায়েন্টকে দেয়।
2. ক্লায়েন্ট পরবর্তী সকল API রিকোয়েস্টে Headers-এ টোকেনটি পাঠায় (`Authorization: Bearer <Token>`)।
3. সার্ভার টোকেনের সিগনেচার (Signature) যাচাই করেই অনুমতি প্রদান করে, কোনো ডেটাবেজ লুক-আপের প্রয়োজন হয় না।

---

##  4. JSON Web Token (JWT) Deep Dive

JWT ৩টি অংশে বিভক্ত থাকে যা ডট (`.`) দ্বারা পৃথক করা হয়: **`Header.Payload.Signature`**

1. **Header:** অ্যালগরিদম এবং টোকেনের টাইপ ধারণ করে (e.g., `{"alg": "HS256", "typ": "JWT"}`)।
2. **Payload:** ইউজারের ইনফরমেশন ও ক্লেমস (Claims) ধারণ করে (e.g., `userId`, `role`, `exp` - expiration time)।
3. **Signature:** হেডার, পে-লোড এবং সার্ভারের সিক্রেট কি (Secret Key) একসাথে মিলিয়ে অ্যালগরিদম প্রয়োগ করে সিগনেচার তৈরি হয়, যা নিশ্চিত করে টোকেনটি কোথাও ট্যাম্পার/পরিবর্তন করা হয়নি।

>  **মনে রাখতেহবে:** JWT পে-লোড কিন্তু এনক্রিপ্টেড থাকে না (Base64 encoded)। তাই JWT-এর ভেতর কখনো সংবেদনশীল তথ্য (যেমন: পাসওয়ার্ড, ক্রেডিট কার্ড নম্বর) রাখা যাবে না।

---

## 5. OAuth 2.0 & OpenID Connect (OIDC)

### 5.1 OAuth 2.0 (Authorization Framework)
OAuth 2.0 পরিচয় প্রমাণের (Authentication) জন্য নয়, এটি মূলত **অনুমোদন (Authorization)** এর জন্য ব্যবহৃত হয়। এর মাধ্যমে এক সার্ভিস অন্য সার্ভিসকে ইউজারের ডেটা ব্যবহারের অনুমতি দেয় (পাসওয়ার্ড শেয়ার ছাড়াই)।
* **প্রসেস (Authorization Code Flow):**
  1. User -> Application-এ "Login with Google"-এ ক্লিক করে।
  2. Application -> Google-এর Authorization Server-এ রিডাইরেক্ট করে।
  3. User -> গুগলকে পারমিশন/কনসেন্ট প্রদান করে।
  4. Authorization Server -> অ্যাপকে একটি **Authorization Code** ফেরত পাঠায়।
  5. Application Server -> এই কোড এবং Client Secret পাঠিয়ে **Access Token** সংগ্রহ করে।

### 5.2 OpenID Connect (OIDC)
* OAuth 2.0 এর ওপরে তৈরি করা **Authentication Layer**।
* OIDC ব্যবহারে Access Token এর পাশাপাশি একটি **ID Token** (JWT ফরম্যাটে) পাওয়া যায়, যা ইউজার আইডি এবং প্রোফাইল তথ্য ধারণ করে।

---

##  6. Authorization Strategies (অনুমোদন কৌশলসমূহ)

### 6.1 Role-Based Access Control (RBAC)
* ইউজারের **ভূমিকা বা রোল (Role)** এর ওপর নির্ভর করে এক্সেস দেওয়া হয়।
* **উদাহরণ:** Admin, Manager, Editor, User।
* **প্রয়োগ:** Admin চাইলে ইউজার ডিলিট করতে পারবে, কিন্তু সাধারণ User কেবল ভিউ করতে পারবে।

### 6.2 Attribute-Based Access Control (ABAC)
* ডায়নামিক ও ফ্লেক্সিবল নীতি। ইউজারের বিভিন্ন এট্রিবিউট, রিসোর্স, এবং পরিবেশের ওপর ভিত্তি করে অনুমতি দেওয়া হয়।
* **উদাহরণ:** "শুধুমাত্র IT ডিপার্টমেন্টের কর্মীরা সোমবার থেকে শুক্রবার সকাল ৯টা-৫টার মধ্যে ফাইল রিড করতে পারবে।"

### 6.3 Permission-Based / Fine-Grained Access Control
* সরাসরি নির্দিষ্ট পারমিশন বরাদ্দ দেওয়া (e.g., `posts:create`, `posts:delete`, `users:read`)।

---

##  7. Best Security Practices (নিরাপত্তা টিপস)

1. **HTTPS :** সবসময় TLS/SSL ব্যবহার করা উচিত যেন ট্রানজিটের সময় ডেটা লিক না হয়।
2. **Secure Token Storage:** 
   * LocalStorage/SessionStorage-এ টোকেন রাখাজাবে না (XSS আক্রমণের ঝুঁকি থাকে)।
   * সেশন বা রিফ্রেশ টোকেন **`SameSite=Strict/Lax; HttpOnly; Secure`** কুকিতে রাখা ভালো।
3. **Access Token & Refresh Token Pattern:**
   * **Access Token:** সংক্ষিপ্ত জীবনকাল (Short-lived, e.g., 15 mins)।
   * **Refresh Token:** দীর্ঘ জীবনকাল (Long-lived, e.g., 7 days)। এক্সেস টোকেন মেয়াদকীর্ণ হলে রিফ্রেশ টোকেন দিয়ে নতুন এক্সেস টোকেন ইস্যু করা হয়।
4. **Rate Limiting & Brute Force Protection:** লগইন এবং সেন্সিটিভ রুটে রিকোয়েস্ট সীমা নির্ধারণ করা (e.g., `express-rate-limit`) এবং IP ব্লকিং ব্যবহার করা।
5. **CSRF (Cross-Site Request Forgery) Protection:** সেশন ভিত্তিক ও কুকি ব্যবহারের ক্ষেত্রে CSRF টোকেন ব্যবহার করা।

---

##  8. Common Terminology Summary

* **Identity Provider (IdP):** যে সিস্টেম ইউজার পরিচয় ম্যানেজ করে (e.g., Auth0, Firebase Auth, Keycloak, Okta)।
* **Bearer Token:** যে এই টোকেন ধারণ করবে, সে-ই এক্সেস পাবে।
* **Salt:** পাসওয়ার্ড হ্যাশিং করার আগে তার সাথে যুক্ত করা র‍্যান্ডম স্ট্রিং, যা Rainbow Table Attack প্রতিরোধ করে।
* **Pepper:** পাসওয়ার্ড হ্যাশিংয়ের জন্য ব্যবহৃত সার্ভার-সাইড সিক্রেট কি।