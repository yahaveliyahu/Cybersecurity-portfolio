# TryHackMe – Windows Fundamentals 3

This folder contains my personal notes and documentation from the **Windows Fundamentals 3** room on TryHackMe.

The room completes the Windows Fundamentals series by exploring the **built-in security features of the Windows operating system**, and how they protect a device from malware, network threats, data theft and ransomware.

---

## 🛡️ Topics Covered

- Windows Update:
  - Patch Tuesday and out-of-band urgent updates
  - Update history and Microsoft Defender definition updates
  - Forced updates and restart scheduling since Windows 10
- Windows Security and its protection areas, including status icons (green, yellow, red)
- Virus & Threat Protection:
  - Quick, Full and Custom scans
  - Quarantined and allowed threats
  - Real-time protection, cloud-delivered protection and exclusions
  - Controlled folder access and ransomware protection
- Firewall & Network Protection:
  - Domain, Private and Public firewall profiles
  - Allowing apps through the firewall
  - Windows Defender Firewall with Advanced Security (`WF.msc`)
- App & Browser Control:
  - Microsoft Defender SmartScreen
  - Exploit protection
- Device Security:
  - Core isolation and Memory Integrity
  - Trusted Platform Module (TPM)
- BitLocker Drive Encryption, with and without a TPM (USB startup key)
- Volume Shadow Copy Service (VSS), shadow copies and restore points
- Ransomware deleting shadow copies, and the importance of offline backups
- C2 (Command and Control) communication and outbound traffic monitoring
- Living Off The Land

---

## 📂 Contents

- **summary.md** – A detailed summary of the Windows security features covered in the room, observations from the lab machine, and their security relevance.
- **screenshots/** – Supporting screenshots of Windows Update history, Windows Security, firewall settings, TPM details and Shadow Copies configuration (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of the security controls built into Windows
- Familiarity with Microsoft Defender, Windows Firewall and BitLocker
- Awareness of how attackers abuse or disable built-in protections (exclusions, shadow copy deletion, Living Off The Land)
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Windows Fundamentals 1  
- Windows Fundamentals 2  
- Introduction to EDR  
- (Future) Advent of Cyber 2 – Day 23 (VSS hands-on)  
- (Future) Windows Event Logs & Sysmon
