# 🔗 Shortener URL App
### Secure, Unpredictable, and High-Performance URL Shortening

A robust URL shortening service built with **Node.js** and **TypeScript**. This project goes beyond simple hashing by implementing a **Feistel Cipher** to ensure that database IDs are obfuscated into non-sequential, unpredictable short codes, preventing ID enumeration attacks.

---

## 🚀 Features
* **Unpredictable Short Codes:** Uses a Feistel Cipher permutation before Base62 encoding.
* **Strict Validation:** Schema-based validation using **Zod**.
* **Security First:** URL sanitization and protocol whitelisting (HTTP/HTTPS only).
* **Persistence:** High-performance data mapping with **PostgreSQL** and **Prisma ORM**.
* **Clean Architecture:** Built with TypeScript for type safety and maintainability.

---

## 🛠️ Tech Stack
| Category | Technology |
| :--- | :--- |
| **Language** | TypeScript / Node.js |
| **Database** | PostgreSQL |
| **ORM** | Prisma |
| **Validation** | Zod |
| **Security** | `sanitize-url`, `Feistel Cipher` logic |

---

## 🧠 Core Logic: The Generation Pipeline
Most shorteners use sequential IDs (1, 2, 3...), making them easy to scrape. This app implements a **Feistel Cipher** to transform the sequential ID into a pseudo-random integer before encoding it.

### The Workflow
1.  **DB ID Generation:** Prisma generates a unique sequential BigInt.
2.  **Feistel Permutation:** The ID passes through a Feistel network to become obfuscated.
3.  **Base62 Encoding:** The obfuscated integer is converted into a URL-friendly string (A-Z, a-z, 0-9).



**Example:**
* **Original ID:** `1001`
* **After Feistel:** `918274561`
* **Final Base62:** `k9F3d`
* **Result:** `https://short.ly/k9F3d`

---

## 🛡️ Security & Validation
To prevent malicious use and "Link Injection," every URL goes through a multi-stage gatekeeper:

* **Format Check:** Ensures the input is a valid URI.
* **Scheme Whitelist:** Only `http://` and `https://` are permitted.
* **Length Constraints:** Maximum 2048 characters to prevent database bloat.
* **Sanitization:** Uses `sanitize-url` to strip dangerous XSS or script payloads.

---

## 📖 How to Use This Repository

### 1. Prerequisites
* Node.js (v18+)
* PostgreSQL instance
* npm or yarn

### 2. Installation
```bash
# Clone the repository
git clone https://github.com/youruser/shortener-url-app.git

# Install dependencies
npm install

# Set up environment variables (.env)
DATABASE_URL="postgresql://user:password@localhost:5432/shortener_db"
FEISTEL_KEY="your-secret-key"
```

### 3. Database Setup
```bash
npx prisma migrate dev --name init
npx prisma generate
```

### 4. Running the App
```bash
npm run dev
```

---

## 🔌 API Reference

### Create a Short URL
`POST /v1/shorten`
| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `originalUrl` | `string` | Yes | The long URL to be shortened |
| `alias` | `string` | No | A custom slug (optional) |

**Response:**
```json
{
  "shortUrl": "https://short.ly/k9F3d",
  "originalUrl": "https://example.com/very-long-link",
  "expiresAt": "2026-12-31T23:59:59Z"
}
```

### Redirect
`GET /:shortCode`
* Redirects with a `301 Moved Permanently` status to the original destination.

---

## 🏗️ Implementation Challenges
* **Integer Range Management:** Handling the conversion of large Postgres BigInts through the Feistel Cipher without losing precision or causing overflows in JavaScript.
* **Collision Handling:** Ensuring that custom "Aliases" provided by users do not collide with the generated short codes from the Feistel pipeline.
* **Database Atomicity:** Guaranteeing that the sequence increment and the URL mapping happen within a single transaction to maintain data integrity.

---

## 📈 Future Improvements
* **Analytics Dashboard:** Track click counts, referrers, and geographic data.
* **Redis Caching:** Implement a caching layer for "Hot Links" to reduce database read latency to <10ms.
* **Link Expiration:** Allow users to set a TTL (Time-To-Live) for their links.
* **Rate Limiting:** Implement middleware to prevent API abuse and bot-spamming.

---
**Author:** Eugy0722 / Yujjin 
**License:** MIT
