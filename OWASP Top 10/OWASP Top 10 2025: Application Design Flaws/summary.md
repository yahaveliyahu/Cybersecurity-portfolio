# OWASP Top 10 2025: Application Design Flaws – Summary

## Overview
In this room, I explored four of the **OWASP Top 10 2025** categories that all share a common root cause: **weak foundations in architecture and system design**.
The focus was on failures that are *built into* how an application is configured, assembled, and designed — not single coding bugs — and on how an attacker finds and exploits them.

Each category came with a hands-on challenge on a vulnerable web app, which I solved from the command line (mainly `curl`, plus `openssl`, `gobuster`, and `ffuf`).
The categories covered:
1. **AS02** – Security Misconfigurations
2. **AS03** – Software Supply Chain Failures
3. **AS04** – Cryptographic Failures
4. **AS06** – Insecure Design

---

## AS02 – Security Misconfigurations

### What it is
Misconfigurations happen when systems, servers, or applications are deployed with **unsafe defaults, incomplete settings, or exposed services**. These are not code bugs but mistakes in how the environment is set up. A single open admin panel, exposed storage bucket, or over-permissive setting can compromise the whole system.

### Real-world example
In 2017, **Uber** exposed a backup **AWS S3 bucket** containing sensitive driver and rider data because the bucket was set to **public**. Attackers downloaded the data directly, **without needing any credentials**. The code wasn't broken — the *permission configuration* was.

- **S3 (Simple Storage Service)** – Amazon's cloud file-storage service.
- **Bucket** – a storage container inside S3 (like a top-level folder in Drive); each file inside it is an **object**. A bucket can be **private** (default, needs credentials) or **public** (anyone with the URL can read it).
- Other providers use the same idea: **Google Cloud Storage** (bucket), **Azure** (Blob Container).
- "Open S3 bucket" is one of the most common findings in cloud audits. Defenses: **Block Public Access**, **Least Privilege**, encryption, and access logging.

### Challenge (`:5002` – User Management API)
The hint — "developers left too many traces" — pointed at **stack traces** (verbose error messages).
- The API doc page exposed one endpoint: `GET /api/user/<user_id>`, noting "**User ID must be numeric**".
- `curl .../api/user/123` returned a clean JSON user (normal behavior).
- Sending a **non-numeric** ID broke the input handling, and the server returned a detailed **stack trace** that revealed the flag.

**Lesson:** this is a **Verbose Error Message** misconfiguration — the app was set to show full stack traces to the user instead of a generic message, and it didn't validate input.\
**Fix:** catch the exception, return a generic error, keep full traces in server-side logs only, and disable debug mode in production.

---

## AS03 – Software Supply Chain Failures

### What it is
Supply chain failures happen when an app relies on **components, libraries, services, or models** that are compromised, outdated, or unverified. The weakness isn't in your own code — it's in what you **depend on**. One bad dependency can compromise the whole system.

### Real-world example
The **2021 SolarWinds Orion** compromise: attackers inserted malicious code into a **trusted software update**, which thousands of organisations auto-installed. The flaw was in the **build, verification, and distribution process**, not in SolarWinds' core logic.

### Challenge (`:5003` – Data Processing Service)
The hint named an outdated `lib/vulnerable_utils.py`. The room provided the **source code** (`app.py`), so instead of guessing I read it:
- It imported from a **local unverified library** (the code even said so in a comment).
- The `/api/process` endpoint had a **leftover debug backdoor**:
  ```python
  if data == 'debug':
      return jsonify(debug_info())
  ```
- Sending `{"data":"debug"}` via POST triggered `debug_info()`, which exposed the flag.

**Lesson:** two combined failures — (1) an **unverified third-party component**, and (2) **debug code left in production** (plus `debug=True` in Flask).\
**Fix:** verify and audit dependencies, remove debug code and backdoors before deploying, and separate dev/prod environments.

---

## AS04 – Cryptographic Failures

### What it is
Cryptographic failures happen when encryption is used **incorrectly or not at all** — weak algorithms, hard-coded keys, poor key handling, or sensitive data left unencrypted. The result: data that should be private becomes accessible.

- **Why ECB mode is weak** – A block cipher encrypts data in fixed 16-byte blocks. In **ECB** mode each block is encrypted independently, so **identical input blocks always produce identical output blocks**. This leaks patterns in the data (the classic "encrypted penguin image" where the shape is still visible). Good modes (AES-GCM, CBC with a random IV) add randomness/chaining so the output looks fully random.
- **Security through obscurity ≠ security** – Hiding a feature in the UI ("decryption unavailable", `encryptionEnabled: false`) doesn't protect anything if the ciphertext, the key, and the algorithm are all still shipped to the client.
-  **Kerckhoffs's principle:** a system must stay secure even when everything about it is known, except the key.
- You can only read ciphertext by **decrypting** it, which needs the **key** — the attack worked *only because the key leaked*, not because AES was broken.

### Challenge (`:5004` – Secure Document Viewer)
- The home page exposed the **ciphertext** (Base64) and loaded a client-side script: `<script src="/static/js/decrypt.js">`.
- `curl`-ing that JS file (still accessible despite the "feature unavailable" message) revealed everything **hard-coded in the client**:
  ```javascript
  const SECRET_KEY = "my-secret-key-16";   // 16 chars = 128-bit
  const ENCRYPTION_MODE = "ECB";
  const KEY_SIZE = 128;
  ```
- I decrypted it with `openssl` (converting the key to hex first with `xxd`):
  ```
  echo "<ciphertext>" | openssl enc -d -aes-128-ecb -K 6d792d7365637265742d6b65792d3136 -base64 -A
  ```
  
**Lesson:** the encryption itself (AES) was fine — the failure was **key management**: the key was hard-coded and shipped to the browser, plus a **weak mode (ECB)**.\
**Fix:** never put keys on the client, decrypt server-side, use a key-management service (AWS KMS / Azure Key Vault / Vault), and use AES-GCM.

---

## AS06 – Insecure Design

### What it is
Insecure design means flawed logic or architecture is built into the system **from the start** — skipped threat modelling, no design reviews, or wrong assumptions about how the system will be used. **You can't patch an insecure design**; fixing it means rethinking the system's **trust boundaries**.

### Real-world example
**Clubhouse** assumed users would only interact through its **mobile app**, so its backend API had **no proper authentication**. Anyone could query user data, room info, and even "private" conversations directly. The "private conversation" promise collapsed. I also read a full write-up of Clubhouse's issues (plaintext transfer of user/room IDs to a third party, shadow-profiles from contact harvesting) — the same case study illustrates both **Cryptographic Failures** (no encryption in transit) and **Insecure Design** (no API auth), depending on which failure you focus on.

### Challenge (`:5005` – SecureChat) — the hardest one
The hint — "Have they assumed that only mobile devices can access it?" — pointed at a false **"mobile only"** assumption. My path (including the wrong turns, which taught the most):
1. Home page said "designed **exclusively for mobile devices**" with a download button.
2. Spoofing a mobile **User-Agent** (`curl -A "...iPhone..."`) on `/` returned the same page → the home page doesn't check it.
3. Enumerating endpoints found `/api/users` (which **leaked all three users**: `admin`, `user1`, `user2`) and `/api/messages/all` (returned `"User not found"`).
4. I got stuck trying to pass a user to `/api/messages/all` **every possible way** — query params, header names, cookies — and used **ffuf** to brute-force **2588 options per method**. All failed. This *proved* (not guessed) that the user wasn't passed via param/header/cookie.
5. The breakthrough: fuzzing the **path** itself — `ffuf .../api/messages/FUZZ` — found that **`/api/messages/admin` returns 200**. The structure was `/api/messages/<username>`, the username as part of the **path**.
6. `curl .../api/messages/admin` returned the admin's private messages containing the flag.

**Lesson:** two stacked **Insecure Design** flaws — (1) **no authentication** on the API (it returns *any* user's private messages just by naming them), and (2) a false assumption that "only the mobile app will call this API", so the "mobile only" page was just visual decoration over a wide-open backend. Plus **user enumeration** via `/api/users`. 
**Fix:** authenticate every endpoint, authorise users to see only their own data, never trust the client, and apply least privilege.

### Methodology lesson (biggest takeaway of the room)
When manual guessing kept failing, switching to **systematic fuzzing** (ffuf) was what cracked it. The "empty" scans weren't failures — each one **eliminated a whole direction with certainty** and shrank the search space. The solution came from scanning the **right place** (the path) rather than fighting the wrong endpoint. This is exactly how real penetration testing works: not endless guessing, but systematic elimination until the right path remains.

---

## Tools & Techniques I practiced
- **`curl`** – mapping endpoints, sending GET/POST, setting headers (`-H`), User-Agent (`-A`), cookies (`-b`), and JSON bodies (`-d`).
- **`openssl enc`** – decrypting AES-128-ECB from Base64 ciphertext.
- **`xxd -p`** – converting a text key to the hex format openssl expects.
- **`gobuster` / `ffuf`** – directory and parameter brute-forcing, with auto-calibration (`-ac`) to filter constant responses.
- **Reading source code** when available, instead of guessing blindly.

---

## Key Takeaways
- All four categories share one root cause: **security bolted on at the end instead of designed in from the start**.
- **Treat defaults with suspicion** (AS02), **treat every dependency as a risk** (AS03), **manage keys properly and use strong modes** (AS04), and **never trust the client or skip authentication** (AS06).
- In every challenge, nothing was truly "hacked" — the data was already exposed, and the skill was **reconnaissance + knowing where to look**.
- The ranking is an **awareness guide**, not an absolute severity scale.
- **Systematic elimination beats endless guessing** — and a scan that finds "nothing" is still a result.
- Get the design right early, and you avoid a long future of preventable incidents.
