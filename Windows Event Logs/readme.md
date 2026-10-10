# TryHackMe – Windows Event Logs

This folder contains my personal notes and documentation from the **Windows Event Logs** room on TryHackMe.

The room introduces **Windows Event Logs** – what they are, how they are stored, and how to query them using different tools and techniques – with a focus on how defenders use event logs to detect and investigate malicious activity.

---

## 🛡️ Topics Covered

- What event logs are and why they matter to defenders
- How Windows Event Logs are stored (.evtx binary format, `C:\Windows\System32\winevt\Logs`)
- Log categories (System, Security, Application and more) and the five event types
- Tools for accessing event logs:
  - Event Viewer (MMC snap-in)
  - wevtutil.exe (command line)
  - Get-WinEvent (PowerShell cmdlet)
- Filtering events with:
  - Filter Current Log and Custom Views
  - FilterHashtable
  - XPath queries
- Resources for choosing event IDs to monitor:
  - The Windows Logging Cheat Sheet
  - NSA – Spotting the Adversary with Windows Event Log Monitoring
  - MITRE ATT&CK
- Enabling additional logging:
  - PowerShell Module Logging (4103) and Script Block Logging (4104)
  - Audit Process Creation (4688) and command line process auditing
  - Basic vs Advanced Audit Policy
- Practical scenarios on a saved log file (`merged.evtx`):
  - Detecting a PowerShell downgrade attack (400)
  - Detecting log clearing (104)
  - Finding an obfuscated Emotet PowerShell payload (4104)
  - Detecting local group enumeration with net1.exe (4799)

---

## 📂 Contents

- **summary.md** – A detailed summary of the concepts learned, tools and commands used, scenario investigations, key event IDs, and lessons learned.
- **screenshots/** – Supporting screenshots of Event Viewer, PowerShell queries, Group Policy settings and investigation results (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of Windows Event Logs and their role in security monitoring
- Hands-on log analysis with Event Viewer, wevtutil and PowerShell
- Building efficient queries with FilterHashtable and XPath
- Investigating suspicious activity and separating it from normal system behaviour
- Mapping detections to MITRE ATT&CK techniques
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Defensive Security Intro
- Splunk: The Basics
- Introduction to SIEM  
- (Future) MITRE  
- (Future) Windows Internals  
- (Future) Sysmon  
