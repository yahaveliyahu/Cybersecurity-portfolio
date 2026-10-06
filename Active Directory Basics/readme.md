# TryHackMe – Active Directory Basics

This folder contains my personal notes and documentation from the **Active Directory Basics** room on TryHackMe.

The room introduces the fundamentals of **Active Directory (AD)**, the backbone of most corporate Windows environments, focusing on how domains are structured, how users and computers are managed, how policies are deployed, and how authentication works inside a Windows domain.

---

## 🗂️ Topics Covered

- What Active Directory and a Windows Domain are
- The role of the Domain Controller (DC)
- Core AD objects:
  - Users (people and service accounts)
  - Machines (machine accounts)
  - Security Groups
- Organizational Units (OUs) vs Security Groups
- Managing users and OUs:
  - Creating and deleting OUs
  - Accidental-deletion protection
  - Matching AD to an organisational chart
- Delegation of control (e.g. password resets) and PowerShell resets
- Managing computers (Workstations / Servers / Domain Controllers)
- Group Policy Objects (GPOs):
  - Creating and linking GPOs
  - Policy inheritance
  - GPO distribution via SYSVOL and `gpupdate /force`
- Domain authentication:
  - Kerberos (TGT, TGS, SPN, KDC)
  - NetNTLM (challenge-response)
  - SAM and NTDS.dit
- Trees, Forests and Trust relationships

---

## 📂 Contents

- **summary.md** – A detailed summary of the concepts learned, hands-on administrative workflows, and real-world notes.
- **screenshots/** – Supporting screenshots of ADUC, Group Policy Management, delegation, and PowerShell steps (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of Active Directory structure and administration
- Familiarity with user, OU, GPO and delegation management
- Awareness of Windows domain authentication protocols and their attack surfaces
- Progress toward Blue Team / SOC-oriented and AD-security roles

---

## 🔗 Related Rooms
- Windows Fundamentals
- (Future) Active Directory Hardening
- (Future) Compromising Active Directory
