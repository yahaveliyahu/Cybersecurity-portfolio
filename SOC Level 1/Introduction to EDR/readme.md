# TryHackMe – Introduction to EDR

This folder contains my personal notes and documentation from the **Introduction to EDR** room on TryHackMe.

The room introduces **Endpoint Detection and Response (EDR)** as a core SOC security solution, covering how it differs from traditional Antivirus, its architecture and telemetry, its detection and response capabilities, and a hands-on triage exercise on a simulated EDR console.

---

## 🛡️ Topics Covered

- Why EDR is needed beyond a traditional Antivirus (AV vs EDR, signature-based vs behavior-based detection)
- Full attack-chain walkthrough comparing AV's response to EDR's response at each stage
- EDR architecture: agents (sensors) and the central console
- Visibility features, including process trees
- Telemetry collected from endpoints:
  - Process executions and terminations
  - Network connections
  - Command line activity
  - File and folder modifications
  - Registry modifications
- Advanced detection techniques:
  - Behavioral Detection
  - Anomaly Detection
  - IOC Matching
  - MITRE ATT&CK Mapping
  - Machine Learning Algorithms
- Response capabilities: host isolation, process termination, quarantine, remote access, artefacts collection
- Hands-on triage of detections on a simulated EDR console (malicious document, credential dumping via LSASS, execution from AppData)

---

## 📂 Contents

- **summary.md** – A detailed summary of EDR's architecture, telemetry, detection/response capabilities, and the triage workflow demonstrated in the room.
- **screenshots/** – Supporting screenshots of the EDR dashboard, process tree/graph, detections list, and the triage investigation (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of how EDR complements and goes beyond traditional Antivirus
- Familiarity with EDR architecture, telemetry, and detection logic
- Ability to triage a detection using process chains, IOCs, and threat intel context
- Progress toward Blue Team / SOC-oriented roles

---

## 🔗 Related Rooms
- Defensive Security Intro
- SOC Role in Blue Team
- Systems as Attack Vectors
- (Future) SIEM
- (Future) Incident Response & SIEM Labs
