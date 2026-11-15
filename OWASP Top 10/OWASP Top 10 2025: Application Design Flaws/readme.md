# TryHackMe – OWASP Top 10 2025: Application Design Flaws

This folder contains my personal notes and documentation from the **OWASP Top 10 2025: Application Design Flaws** room on TryHackMe.

The room covers four OWASP Top 10 2025 categories that share one root cause: **weaknesses built into an application's architecture, configuration, and design** — not single coding bugs. Each category includes a hands-on challenge against a vulnerable web app, solved from the command line.

---

## 🛡️ Topics Covered

- The four design-related OWASP Top 10 2025 categories:
  - **A02** – Security Misconfigurations
  - **A03** – Software Supply Chain Failures
  - **A04** – Cryptographic Failures
  - **A06** – Insecure Design
- How the OWASP Top 10 ranking works (prevalence + impact, not pure severity)
- Core concepts explored along the way:
  - AWS S3, buckets, and public vs private storage
  - GET vs POST and how requests carry data
  - Why ECB mode is weak; security through obscurity; Kerckhoffs's principle
  - Trust boundaries and why insecure design can't be patched
- Practical attack techniques:
  - Endpoint reconnaissance and verbose error / stack-trace exposure
  - Exploiting leftover debug code and unverified dependencies
  - Decrypting data from a hard-coded, client-side key
  - API enumeration and bypassing a false "mobile-only" assumption

---

## 📂 Contents

- **summary.md** – A detailed summary of the four categories, the concepts learned, and a walkthrough of each challenge's approach and defensive lesson.
- **screenshots/** – Supporting screenshots of the vulnerable apps, endpoints, and exploitation steps (no flags or sensitive values included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of secure-design and secure-configuration principles
- Familiarity with common web application weaknesses and how they are exploited
- Hands-on use of offensive tooling (`curl`, `openssl`, `gobuster`, `ffuf`) for enumeration and testing
- A methodical approach: systematic elimination over blind guessing
- Progress toward application security / secure development awareness

---

## 🔗 Related Rooms
- OWASP Top 10 2025: IAAA Failures
- (Future) OWASP Top 10 2025: Insecure Data Handling
