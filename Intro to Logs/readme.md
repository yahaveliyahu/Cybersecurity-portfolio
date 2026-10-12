# TryHackMe – Intro to Logs

This folder contains my personal notes and documentation from the **Intro to Logs** room on TryHackMe.

The room introduces **logs as a historical record of system activity**, and how they are collected, managed and analysed to detect and investigate malicious activity. It is built around a scenario in which a web server of SwiftSpend Financial is under constant attack, and the goal is to configure logging and analyse the collected logs to uncover what the adversary is doing.

---

## 🪵 Topics Covered

- What logs are and what a log entry contains
- Common log types (application, security, system, network, web server and more)
- Log formats:
  - Semi-structured (Syslog, Windows EVTX)
  - Structured (CSV, JSON, W3C ELF, XML)
  - Unstructured (NCSA Common and Combined Log Format)
- Log standards (CEE, OWASP Logging Cheat Sheet, Syslog, NIST SP 800-92)
- Log collection and time synchronisation with NTP
- Log management, centralisation and forwarding to a SIEM
- Log storage, retention (Hot / Warm / Cold) and deletion
- The log analysis process: Parsing, Normalisation, Sorting, Classification, Enrichment, Correlation, Visualisation and Reporting
- Hands-on practice with Linux tools:
  - **rsyslog** – routing sshd messages to a dedicated log file
  - **logrotate** – automated rotation, compression and SHA-256 integrity hashing
  - **awk, sed, grep, sort, uniq** – normalising and consolidating logs from multiple sources
  - **Log Viewer** – browsing raw and consolidated logs

---

## 🔍 Investigation Highlights

By correlating several log sources, I traced the activity of a single adversary IP:
- **Directory brute force** against the web server (nginx access log)
- **SSH brute force** attempts against a non-existent user (sshd log)
- **Persistence** through a root cron job running a reverse shell every minute (cron log)

---

## 📂 Contents

- **summary.md** – A detailed summary of the concepts learned, the practical configurations with explanations, and the investigation findings.
- **screenshots/** – Supporting screenshots of the terminal configurations, log outputs and the Log Viewer (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of log types, formats and the log analysis lifecycle
- Hands-on experience configuring log collection and rotation on Linux
- Ability to normalise and correlate logs from multiple sources
- Practical incident investigation using command-line tools
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Splunk: The Basics
- Introduction to EDR
- Introduction to SIEM
- (Future) Log Operations
- (Future) Intrusion Detection and Prevention Systems (IDPS)
- (Future) Investigating with ELK 101
- (Future) Wazuh, Snort
