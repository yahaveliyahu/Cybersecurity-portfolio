# SOC Fundamentals – Summary

## Overview
In this room, I explored the fundamentals of a **Security Operations Center (SOC)**, a dedicated facility operated by a specialised security team.

The focus was on how a SOC continuously monitors an organisation's network and resources, identifies suspicious activity, and responds before it causes damage. The team works 24 hours a day, seven days a week, and its effectiveness rests on three pillars working together: **People**, **Process**, and **Technology**.

---

## Learning Objectives
- Building a baseline for SOC (Security Operations Center)
- Detection and response in SOC
- The role of People, Processes, and Technology
- Practical exercise

---

## Purpose and Components

The main focus of the SOC team is to keep **Detection** and **Response** intact. Security solutions integrate the whole company's network and systems into one centralised location, enabling continuous monitoring.

### Detection
- **Detect vulnerabilities** – weaknesses in a device's software (OS or programs) that an attacker could exploit
- **Detect unauthorized activity** – e.g. an attacker using a stolen username/password to log in; clues like geographic location can help spot this
- **Detect policy violations** – breaches of a company's security policy, such as downloading pirated files or sending confidential data insecurely
- **Detect intrusions** – unauthorised access to systems and networks, whether through exploiting a web application or a user visiting a malicious site

### Response
- **Support the incident response** – minimising the impact of a detected incident and performing root cause analysis, working alongside the incident response team

---

## The Three Pillars of a SOC

### 1. People
Even with automation, the **People** in a SOC remain essential. Security solutions can generate huge numbers of alerts ("noise") — much like a fire brigade getting many alarms at once that turn out to be nothing more than smoke from cooking. Without people to filter this noise, effort and resources are wasted on irrelevant issues.

The SOC team's roles (reporting up to a CISO) are:
- **SOC Analyst (Level 1)** – first responders to any detection; perform basic alert triage to determine if it's harmful and report it through proper channels
- **SOC Analyst (Level 2)** – dive deeper into escalated detections, correlating data from multiple sources
- **SOC Analyst (Level 3)** – experienced professionals who proactively hunt for threat indicators and support incident response (containment, eradication, recovery)
- **Security Engineer** – deploys and configures the security solutions the analysts rely on
- **Detection Engineer** – builds the detection rules/logic behind the security solutions
- **SOC Manager** – manages the team's processes and reports the SOC's security posture to the CISO

### 2. Process
Each role follows its own processes. The two key ones covered were:

**Alert Triage** – the first response to any alert, focused on analysing it to determine severity and priority. This means answering the **5 Ws**:
- **What** – what activity triggered the alert
- **When** – when it happened
- **Where** – where it was detected
- **Who** – who it affected
- **Why** – the underlying reason for the activity

**Reporting** – harmful alerts are escalated as tickets to higher-level analysts, with the report covering all 5 Ws, a thorough analysis, and screenshots as evidence.

**Incident Response and Forensics** – for highly malicious, critical detections, higher-level teams initiate a full incident response, sometimes including forensic analysis to determine the incident's root cause from system/network artefacts.

### 3. Technology
Security solutions minimise the manual effort needed to detect and respond to threats across every device and application in the network:

- **SIEM** (Security Information and Event Management) – collects logs from log sources, applies detection rules, and correlates events to raise alerts. Modern SIEMs add user behaviour analytics and threat intelligence. *Note: SIEM only provides the Detection capability.*
- **EDR** (Endpoint Detection and Response) – gives real-time and historical visibility at the endpoint level, with automated response capability
- **Firewall** – a barrier between internal and external networks; filters unauthorised traffic and has some detection rules of its own

Other solutions mentioned: Antivirus, EPP, IDS/IPS, XDR, and SOAR. The right mix depends on the organisation's threat surface and available resources.

---

## Practical Exercise
This task put the People/Process/Technology theory into practice as a **Level 1 Analyst**.

**Scenario:** An alert reported port scanning activity from host `10.0.0.8`. Using the SIEM solution, I reviewed the associated logs to answer the 5 Ws.

**Investigation findings:**
- The logs showed a single source host (`10.0.0.8`, NESSUS) hitting one destination (`10.0.0.3`, JOE PC) across many different destination ports in a short window — the classic signature of a **port scan**
- The vulnerability assessment team had already notified the SOC that they would be running this scan from that host, making the activity **intended** rather than malicious
- No evidence of a response being sent back from the target IP to the scanner needed to be checked separately in the logs to fully close out the "Why" analysis

**Conclusion:** Since the activity was expected, authorised, and carried out by a known internal tool, the alert was classified as a **False Positive** — the SIEM correctly flagged suspicious-looking traffic, but investigation confirmed it was not an actual security incident.

---

## Key Takeaways
- A SOC's core purpose is **Detection and Response**, built on the pillars of People, Process, and Technology
- Human analysts are essential for filtering the "noise" that automated tools generate
- Alert triage always starts with answering the **5 Ws**
- SOC roles are tiered (L1 → L2 → L3) by depth of investigation and experience
- Not every detected alert is a real incident — distinguishing **True Positive** from **False Positive** is a core Level 1 Analyst skill
