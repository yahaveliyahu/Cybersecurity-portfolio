# TryHackMe – Windows Fundamentals 2

This folder contains my personal notes and documentation from the **Windows Fundamentals 2** room on TryHackMe.

The room continues the exploration of the **Windows operating system**, focusing on the built-in administrative utilities available through **System Configuration (MSConfig)** and the different ways to launch them. The lab environment was a **Windows Server 2019** machine accessed via Remote Desktop.

---

## 🪟 Topics Covered

- Differences between Windows client editions and Windows Server
- System Configuration (MSConfig) and its five tabs:
  - General, Boot, Services, Startup, Tools
- Startup items on Windows Server and other autostart locations (Run keys, Scheduled Tasks, Services)
- Advanced System Settings:
  - Processor scheduling and virtual memory (page file)
  - Startup and Recovery and crash dump types
- User Account Control (UAC) levels
- Computer Management (compmgmt):
  - Task Scheduler
  - Event Viewer (event types and standard logs)
  - Shared Folders (administrative and custom shares)
  - Performance Monitor, Device Manager, Disk Management
  - Services and their startup types, WMI
- System Information (msinfo32) and Environment Variables
- Resource Monitor (resmon), svchost.exe and process masquerading
- Command Prompt utilities:
  - `hostname`, `whoami`, `ipconfig`, `netstat`, `net`
- Windows Registry and the Registry Editor
- Related concepts explored along the way:
  - SMB, Sysinternals and PsTools, Npcap and Nmap

---

## 📂 Contents

- **summary.md** – A detailed summary of the utilities covered, their commands, and their relevance to security investigations.
- **screenshots/** – Supporting screenshots of MSConfig, Computer Management, System Information, Resource Monitor, the Registry Editor and Command Prompt output (no credentials or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Familiarity with core Windows administration and troubleshooting tools
- Ability to gather system, network and process information from both the GUI and the command line
- Awareness of how attackers abuse legitimate Windows features (persistence, masquerading, lateral movement)
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Windows Fundamentals 1  
- (Future) Windows Fundamentals 3  
- (Future) Windows Event Logs  
- (Future) Nmap
