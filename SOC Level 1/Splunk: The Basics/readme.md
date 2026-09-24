# TryHackMe – Splunk: The Basics

This folder contains my personal notes and documentation from the **Splunk: The Basics** room on TryHackMe.

The room introduces **Splunk**, one of the leading **SIEM** solutions, focusing on its core components, its interface, how logs are ingested, and how to search and analyse them using SPL.

---

## 🔍 Topics Covered

- What Splunk is and its role as a SIEM
- Splunk core components:
  - Forwarder
  - Indexer
  - Search Head
- Navigating the Splunk interface (Splunk Bar, Apps Panel, Explore Splunk, Home Dashboard)
- Data source categories supported by Splunk
- Data ingestion methods (Upload, Monitor, Forward)
- The five-step data upload process
- Creating and using indexes
- Hands-on log analysis of VPN logs using **SPL (Search Processing Language)**:
  - Parsing JSON fields with `spath`
  - Filtering events with `search`
  - Summarising results with `stats`

---

## 📂 Contents

- **summary.md** – A detailed summary of Splunk's architecture, data ingestion workflow, and the SPL queries used to investigate VPN logs.
- **screenshots/** – Supporting screenshots of the Splunk interface, the data upload wizard, index creation, and SPL search results (no sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of SIEM architecture and log ingestion
- Hands-on experience with Splunk Enterprise
- Ability to write basic SPL queries for log analysis and investigation
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Introduction to SIEM  
- (Future) Splunk: Exploring SPL  
- (Future) Incident Handling with Splunk  
- (Future) Investigating With Splunk
