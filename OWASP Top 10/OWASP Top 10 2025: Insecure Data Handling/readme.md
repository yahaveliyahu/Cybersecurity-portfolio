# TryHackMe – OWASP Top 10 2025: Insecure Data Handling

This folder contains my personal notes and documentation from the **OWASP Top 10 2025: Insecure Data Handling** room on TryHackMe.

The room covers 4 entries from the **OWASP Top 10 (2025)** that relate to **application behaviour and user input**, explaining each vulnerability, how to prevent it, and then practically exploiting it to retrieve a flag.

---

## 🛡️ Topics Covered

- 4 OWASP Top 10 (2025) categories focused on insecure data handling:
  - **A04: Cryptographic Failures** – weak/homemade encryption and key management
  - **A05: Injection** – untrusted input executed as code or queries
  - **A08: Software or Data Integrity Failures** – trusting data without verification
  - **A10: Mishandling of Exceptional Conditions** – fail-open vs fail-closed, and defensive testing with staging / fault injection / chaos testing
- Core concepts learned along the way:
  - XOR encryption and why it is reversible (known-plaintext & brute-force attacks)
  - Base64 as an *encoding*, not encryption
  - Server-Side Template Injection (SSTI) and Jinja2 template engines
  - Fingerprinting a template engine with `{{7*7}}` and `{{7*'7'}}`
  - Insecure deserialization in Python (`pickle`) and the `__reduce__` method
  - Modules vs functions, and why payloads are smuggled inside serialized objects
- Hands-on exploitation of each vulnerability to capture a flag

---

## 📂 Contents

- **summary.md** – A detailed summary of each vulnerability, the exploitation steps performed, the key concepts behind them, and prevention best practices.
- **screenshots/** – Supporting screenshots of the practical web apps (XOR note-sharing, SSTI playground, pickle deserializer) and the exploitation steps.

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of common web application vulnerabilities (OWASP Top 10)
- Practical offensive skills: breaking weak crypto, SSTI leading to RCE, and insecure deserialization
- The defensive mindset behind each attack: trust boundaries, input validation, and using proven standards over homegrown solutions
- Progress toward security-oriented roles

---

## 🔗 Related Rooms
- Cryptographic Failures Module
- Injection Attacks Module / Command Injection
- Insecure Deserialisation
- Supply Chain Attack: Lottie
