# Introduction to EDR – Summary

## Overview
In this room, I learned about **Endpoint Detection and Response (EDR)** — one of the core security solutions used in a SOC. The room covered why EDR goes beyond what a traditional Antivirus (AV) can do, how an EDR is architected, what telemetry it collects, its detection and response capabilities, and finished with a hands-on triage exercise on a simulated EDR console.

---

## Why Do We Need an EDR If We Already Have an AV?
Both AV and EDR aim to protect the endpoint, but at very different levels:

- **AV** works like a passport check at an airport — it matches incoming files/activity against a database of known threats (signatures). If there's no match, it lets the activity through, even if it's actually malicious.
- **EDR** works like security officers inside the airport, constantly watching cameras and sensors. It doesn't just check identity once — it continuously monitors **behavior**, so it can catch a threat that AV let in.

### Attack Chain Walkthrough: AV vs EDR
The room compared how each solution handles the same 6-step attack:

1. User receives a phishing email with a malicious macro-embedded Word document
2. User downloads and opens the document
3. The macro silently spawns PowerShell
4. An obfuscated PowerShell command downloads a second-stage payload
5. The payload is injected into a legitimate `svchost.exe`
6. The attacker gains remote access

| Step | AV | EDR |
|---|---|---|
| 1–2 | Does nothing — no signature match, `winword.exe` is legitimate | Logs the download, records execution |
| 3–4 | Doesn't detect unsigned/obfuscated macros or PowerShell | Flags the unusual `winword.exe` → `PowerShell.exe` relationship and the obfuscated script |
| 5 | No memory-injection visibility | Detects process injection into `svchost.exe` |
| 6 | No network-level visibility | Flags the unexpected outbound connection |
| **Final** | **May mark the activity as clean** | Generates a full alert chain for the analyst |

This showed why an AV alone can miss an advanced attack end-to-end, while an EDR builds the complete picture from behavior.

---

## EDR Architecture: Agents & Console
- **Agents (sensors)** are deployed on every endpoint. They're the "eyes and ears" of the EDR — constantly watching activity and sending detailed telemetry to the console in real time. They can also do basic signature/behavior detection locally.
- **The Console** is the brain — it correlates all the data coming from the agents, runs it through complex logic and machine learning, matches it against threat intelligence, and connects the dots into detections (alerts). It also gives a centralized, holistic dashboard across all endpoints.

---

## Visibility
Visibility is one of the things that sets EDR apart from other endpoint solutions. It presents collected data (process, registry, file/folder modifications, user actions, and more) in a structured way — including a full **process tree**, so an analyst can see exactly which processes spawned which, and drill into the network connections, registry changes, and file changes tied to each one.

---

## Telemetry Collected
Telemetry is essentially the "black box" of an endpoint — everything needed for detection and investigation. Key types include:

- **Process Executions/Terminations** – tracks parent-child relationships to catch suspicious process chains
- **Network Connections** – monitors for C2 traffic, unusual ports, exfiltration, or lateral movement
- **Command Line Activity** – captures CMD/PowerShell commands, including obfuscated scripts an AV would miss
- **File and Folder Modifications** – tracks data staging, ransomware behavior, dropped files
- **Registry Modifications** – the registry holds a system's configuration, so it's a common target during malicious activity (e.g. persistence)

Individually, these actions can look harmless — it's the combination, seen through detailed telemetry, that reveals the real story.

---

## Detection Techniques
- **Behavioral Detection** – flags unusual behavior rather than relying on signatures (e.g. `winword.exe` spawning PowerShell)
- **Anomaly Detection** – EDR learns a baseline for each endpoint and flags deviations (e.g. an unusual auto-start registry key change); can produce false positives, but gives enough context for an analyst to judge legitimacy
- **IOC Matching** – matches activity (file hashes, IPs, domains) against threat intelligence feeds
- **MITRE ATT&CK Mapping** – every flagged activity is mapped to a specific Tactic and Technique (e.g. Persistence → Scheduled Task/Job), helping analysts understand the attack stage
- **Machine Learning** – trained on large datasets of normal vs. malicious behavior, useful for catching fileless and multi-stage attacks where no single action looks malicious on its own

---

## Response Capabilities
Once something is detected, EDR gives analysts several response options (automated or manual):

- **Isolate Host** – disconnects an infected endpoint from the network to stop lateral movement
- **Terminate Process** – kills a malicious process without isolating the whole host (useful for business-critical machines)
- **Quarantine** – moves a malicious file to an isolated location so it can't execute, pending review
- **Remote Access** – lets analysts remotely access an endpoint's shell (e.g. CrowdStrike Falcon RTR) to run commands/scripts and investigate deeper
- **Artefacts Collection** – remotely pulls forensic data such as memory dumps, event logs, folder contents, and registry hives for investigation or legal reporting

---

## Hands-On: Triage in a Simulated EDR
As the final task, I triaged detections on a simulated EDR dashboard (53 active hosts, multiple new detections flagged via Anomaly, Behavior, and Threat Intel sources).

### 1. Initial Access via Malicious Office Document (High – DESKTOP-HR01, user alice.thomas)
- `invoice.docm` was opened via `WINWORD.EXE`, which spawned `CMD.EXE`, which triggered `cURL.EXE` to download a payload from an external domain
- The payload (`install.exe`) was written to `C:\Users\Public\` but **never executed** — consistent with malware *staging* rather than a completed compromise
- IOCs: dropped binary, C2 domain, external IP, and file hash — the binary and the original document were both quarantined, and the C2 domain was blocked

### 2. Credential Dumping via LSASS Memory Access (High – WIN-ENG-LAPTOP03, user haris.khan)
- An unsigned binary (`syncsvc.exe`) launched from a Temp directory, accessed `lsass.exe` memory, and wrote a credential dump to disk — classic credential-dumping behavior
- Mapped to **MITRE ATT&CK T1003.001 (OS Credential Dumping: LSASS Memory)**
- It also attempted to exfiltrate the dump to an external domain and tried to set a registry Run-key for persistence — the outbound exfiltration attempt was blocked by the EDR/firewall

### 3. Execution from AppData Directory (Medium – DESKTOP-DEV01, user daniel.richards)
- An unsigned binary (`UpdateAgent.exe`) ran from a non-standard location (`AppData\Roaming`) and made an outbound HTTP connection — a pattern often seen with droppers/staging malware
- However, the connection went to an **internal** IP, and threat intel identified the binary as a known internal IT utility tool — a good example of why context matters before treating every anomaly as malicious

---

## Key Takeaways
- An AV alone can be blind to an entire attack chain if no single step matches a known signature — EDR closes that gap by watching **behavior**, not just identity
- EDR's power comes from combining rich telemetry (processes, network, command-line, files, registry) with behavioral analysis, anomaly detection, IOC matching, ML, and MITRE mapping
- Agents feed raw telemetry; the console is where correlation and detection actually happen
- Not every detection is a true positive — an analyst needs to check threat intel context (e.g. the "known internal IT utility tool" case) before escalating
- Response isn't one-size-fits-all: isolating a host, killing a process, or quarantining a file are different tools for different levels of risk
- Investigating a real alert means reconstructing the full process chain and cross-referencing IOCs (hashes, domains, IPs, registry keys) to understand what actually happened
