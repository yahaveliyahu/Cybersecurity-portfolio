# TryHackMe – Introduction to SIEM

This folder contains my personal notes and documentation from the **Introduction to SIEM** room on TryHackMe.

The room introduces **Security Information and Event Management (SIEM)** systems — the core tool used by SOC analysts to collect, normalize, correlate, and investigate security events across a network.

---

## 🛡️ Topics Covered

- Host-centric vs. network-centric log sources
- Challenges of working with logs without a centralized solution
- Why SIEM is needed and how it fits into the SOC workflow
- Core SIEM features:
  - Centralized Log Collection
  - Normalization (parsing & standardizing logs)
  - Correlation of events across sources
  - Real-time Alerting
  - Dashboards and Reporting
- Common log sources and ingestion methods:
  - Windows Event Viewer (Event IDs)
  - Linux log locations (`/var/log/auth.log`, `/var/log/cron`, `/var/log/httpd`, `/var/log/kern`)
  - Web server (Apache) logs
  - Agent/Forwarder, Syslog, Manual Upload, Port-Forwarding
- Detection rules — how they're built and how they trigger alerts
- Alert investigation workflow: True Positive vs. False Positive, and follow-up actions
- Hands-on walkthrough: investigating a **Potential CryptoMiner Activity** alert (`cudominer.exe`) from dashboard → event details → rule logic → classification → action

---

## 📂 Contents

- **summary.md** – A detailed summary of the concepts learned, SIEM workflows, and the hands-on alert investigation.
- **screenshots/** – Supporting screenshots of the SIEM dashboard, event logs, detection rule, and alert action screens (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of SIEM fundamentals and log correlation
- Familiarity with detection rule logic and alert triage
- Hands-on experience walking through a full alert investigation
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Defensive Security Intro
- Junior Security Analyst Intro
- Splunk: The Basics
- Incident Handling with Splunk
- Investigating with Splunk
- Investigating with ELK
- ItsyBitsy
