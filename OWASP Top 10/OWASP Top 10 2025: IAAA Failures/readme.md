# TryHackMe – OWASP Top 10 2025: IAAA Failures

This folder contains my personal notes and documentation from the **OWASP Top 10 2025: IAAA Failures** room on TryHackMe.

The room breaks down three of the **OWASP Top 10:2025** categories, all tied to failures in how **Identity, Authentication, Authorisation, and Accountability (IAAA)** are implemented in web applications. The theory is put into practice through hands-on challenges against a mock banking app (`bank.thm`).

---

## 🛡️ Topics Covered

- What the **OWASP Top 10** is and why it matters
- The **IAAA** model (Identity → Authentication → Authorisation → Accountability) and how each layer depends on the previous one
- The three categories covered in this room:
  - **A01: Broken Access Control**
    - IDOR (Insecure Direct Object Reference)
    - Horizontal vs. vertical privilege escalation
  - **A07: Authentication Failures**
    - Logic flaws in the login/registration flow (case-sensitivity mismatch)
    - Binding a session to the wrong account
  - **A09: Logging & Alerting Failures**
    - Investigating an attack timeline from logs / SIEM
    - Why each log field (endpoint, IP, timestamp, response code) matters
    - Security through obscurity
- Defensive fixes for each category (server-side authorisation, username normalisation, full-lifecycle logging)

---

## 📂 Contents

- **summary.md** – A detailed summary of the concepts learned, the vulnerabilities exploited in each challenge, and how to defend against them.
- **screenshots/** – Supporting screenshots of the challenges and the SIEM investigation (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of the OWASP Top 10:2025 and the IAAA model
- Ability to identify and exploit access-control and authentication flaws (offensive perspective)
- Ability to investigate an attack from logs and reason about logging/accountability gaps (defensive perspective)
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Identity and Access Management
- (Future) Application Design Flaws
- (Future) Logging for Accountability

