# TryHackMe – Cyber Kill Chain

This folder contains my personal notes and documentation from the **Cyber Kill Chain** room on TryHackMe.

The room introduces the **Cyber Kill Chain**, a framework that breaks a cyber attack into seven stages. It explains how attackers operate at each stage and how defenders can detect and break the chain before the attack succeeds.

---

## ⛓️ Topics Covered

- What the Cyber Kill Chain is and why it matters for defenders
- The seven stages of an attack:
  - Reconnaissance
  - Weaponisation
  - Delivery
  - Exploitation
  - Installation
  - Command and Control (C2)
  - Actions on Objectives
- Attack techniques at each stage, such as:
  - **Reconnaissance** - OSINT, Google Dorking, and port scanning
  - **Weaponisation** - Malicious macros and exploit kits
  - **Delivery** - Phishing, spear phishing (Targeted Phishing), smishing, USB drops, watering hole attacks and drive-by downloads
  - **Exploitation** - Zero-day exploits, known CVEs, SQL injection, XSS, and CSRF
  - **Installation** - Persistence via cron jobs, services/daemons, Windows services via sc.exe & Reg, Registry Run Keys / Startup Folder, backdoors and persistent backdoors (Metasploit / Meterpreter / metsvc), DLL injection, rootkits, web shells, LOLBins, masquerading and timestomping
  - **Command and Control** - C2 evasion techniques: DNS tunnelling, DGA, and Fast Flux
  - **Actions on Objectives** - credential theft, privilege escalation, data exfiltration, ransomware, deleting backups/shadow copies, lateral movement, and ICS manipulation
- Countermeasures for each stage, such as:
  - MFA and patch management
  - WAF, IDS/IPS, and EDR
  - Application allowlisting
  - DLP, backups, and network segmentation
  - Principle of least privilege
- Mapping attacker actions to the correct kill chain stage
- Red Team vs. Blue Team use of the framework

---

## 📂 Contents

- **summary.md** – A detailed summary of the seven stages, attack examples, countermeasures, and the stage-mapping exercises and the Target breach analysis.
- **screenshots/** – Supporting screenshot demonstrating  the Practical Exercises (no flags or sensitive data included).
  
---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of how cyber attacks progress from start to finish
- Ability to classify attacker actions by kill chain stage
- Knowledge of defensive controls that break the chain at each stage
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Defensive Security Intro
- Offensive Security Intro
- MITRE ATT&CK
- (Future) Unified Kill Chain
