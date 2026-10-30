# MITRE – Summary

## Overview
In this room, I explored **MITRE**, a not-for-profit organisation that conducts research and development across many domains, including cyber security.  
The focus was on MITRE's cyber security frameworks and resources: **ATT&CK**, **CAR**, **D3FEND**, and other projects such as **Caldera**, the **Adversary Emulation Library**, **AADAPT** and **ATLAS**.

Together, these resources give both red and blue teams a common way to understand adversary behaviour, build detections, and strengthen defences.

---

## TTPs – Tactics, Techniques and Procedures
ATT&CK is built around **TTPs**, which describe how an adversary operates at three levels of detail:

- **Tactic** – The adversary's goal. The "why" of an attack (e.g. Initial Access)
- **Technique** – How the adversary achieves that goal (e.g. Phishing)
- **Procedure** – The specific implementation used by a real group: the exact tools, files and commands

TTPs are more valuable for defence than **IOCs (Indicators of Compromise)**, such as IP addresses, domains or file hashes.  
IOCs are easy for attackers to change, while TTPs reflect their way of working and change slowly.

---

## The MITRE ATT&CK Framework
ATT&CK is a globally accessible knowledge base of adversary tactics and techniques based on real-world observations.

- It started in 2013, focused on Windows and on **APT groups**.
- It now covers Windows, macOS, Linux, cloud and more under the **Enterprise** matrix (The main ATT&CK matrix covers attacks on an organization’s IT environment—including computers, servers, the network, 
and the cloud. Everything I saw in the room, including Mustang Panda, APT33, and the CAR layer, was part of the Enterprise matrix), with separate matrices for **Mobile** and **ICS** (Industrial Control Systems: Factories, Electricity, Water)
Each matrix has its own tactics and techniques, because an attack on a phone or an industrial system looks different from an attack on an office computer. In practice: when someone says “ATT&CK” without further specification, they almost always mean the Enterprise matrix.
- It is used by defenders, and also by red teams to plan realistic attack simulations

### The ATT&CK Matrix
- **Columns** are tactics, ordered roughly by the stages of an attack
- **Cells** are techniques, which can be expanded to reveal **sub-techniques**
- The number in brackets next to a technique shows how many sub-techniques it has

Example:
- Tactic: **Reconnaissance**
- Technique: **Active Scanning**
- Sub-techniques: Scanning IP Blocks, Vulnerability Scanning, Wordlist Scanning

A single technique can belong to more than one tactic. For example, **Scheduled Task/Job** appears under Execution, Persistence and Privilege Escalation.

### Technique Pages
Each technique has its own page, which includes:
- A description of the technique
- Its ID, tactic(s), platforms, version and dates
- A list of sub-techniques
- **Procedure Examples** – groups, software and campaigns that used it
- **Mitigations** – ways to defend against it
- **Detection Strategies** – ways to detect it
- References

### ATT&CK IDs
Every object in ATT&CK has a unique ID with a fixed prefix:

| Prefix | Object | Example |
|---|---|---|
| `TA` | Tactic | `TA0043` – Reconnaissance |
| `T` | Technique | `T1595` – Active Scanning |
| `T….xxx` | Sub-technique | `T1595.001` – Scanning IP Blocks |
| `G` | Group | `G0129` – Mustang Panda |
| `S` | Software | `S0154` – Cobalt Strike |
| `C` | Campaign | `C0047` |
| `M` | Mitigation | `M1018` – User Account Management |
| `DET` | Detection Strategy | `DET0546` |

---

## ATT&CK in Operation

### Why ATT&CK Matters
- It provides a **standard language** and unique IDs for adversary behaviour, so data and incidents can be compared across organisations
- It bridges the gap between **threat intelligence** and **defence**: by mapping threat activity to TTPs, defenders can turn intelligence into detection logic, queries and playbooks

### Who Uses ATT&CK?
- **CTI Teams** – Map threat actor behaviour to TTPs and build threat profiles
- **SOC Analysts** – Link alerts to tactics and techniques for context and prioritisation
- **Detection Engineers** – Map SIEM/EDR rules to ATT&CK to measure detection coverage
- **Incident Responders** – Map incident timelines to tactics and techniques
- **Red & Purple Teams** – Build emulation plans based on techniques and known groups

A "Purple Team" is a collaboration between the Red Team and the Blue Team. Instead of the Red Team launching covert attacks and submitting a report only at the end, both teams work together, step by step:
1. The Red Team executes a technique—for example, creating a Scheduled Task.
2. The Blue Team immediately checks: Was it detected? Was an alert triggered?
3. If not, they work together to refine the detection rule and then run the test again.
Typically, this is not a separate, standalone team, but rather a working methodology or exercise in which both teams collaborate. The advantage: defenses are improved during the exercise itself, rather than only after it concludes.

Emulation means imitation. An emulation plan is a detailed, step-by-step guide on how to mimic a specific threat actor group. It is based on Cyber ​​Threat Intelligence (CTI) regarding the group's actions during actual attacks.
The difference compared to a standard penetration test is that in a penetration test, the tester seeks any possible way to break in. In an emulation, the team mimics a specific attacker to answer a focused question: "If Group X were to attack us, would we detect it?"

### Mapping a CTI Report
A CTI report describes an attack as a story. **Mapping** turns each stage into an ATT&CK technique ID, for example:

| Stage | Technique |
|---|---|
| Reconnaissance | `T1595` – Active Scanning |
| Initial Access | `T1190` – Exploit Public-Facing Application |
| Persistence | `T1136` – Create Account |
| Defense Evasion | `T1564` – Hide Artifacts |
| Collection | `T1560` – Archive Collected Data |

---

## ATT&CK Navigator
The **ATT&CK Navigator** is a web tool for annotating the matrix.

- It works with **layers**: the full matrix with colours, scores and comments on top
- Layers are saved as **JSON** files and can be shared or loaded from a URL
- Common uses: profiling a threat group, mapping detection coverage, and **gap analysis** (comparing what a group does with what you can detect)

---

## APT Groups and Threat Profiling

### APT Groups
**APT (Advanced Persistent Threat)** groups are organised, well-resourced attack groups, usually sponsored by a state.
- **Advanced** – Skilled teams, custom tools, sometimes zero-day exploits
- **Persistent** – They stay hidden in a network for long periods
- Their goal is usually espionage or strategic impact, unlike cybercriminals, whose goal is usually money

A group's description in ATT&CK shows which type it is (e.g. "state-sponsored", "cyber espionage" vs. "financially motivated", "ransomware").  
Naming schemes can also hint at origin. For example, in CrowdStrike's naming, **Panda** is used for China-based groups and **Spider** for cybercriminal groups.

### Group Names
- There is no central naming authority. Security vendors name groups using their own schemes, so one group can have many names
- ATT&CK picks one primary name, gives it a `G` ID, and lists the other names under **Associated Groups**
- The techniques mapped to a group are based only on **publicly reported** activity, not everything the group does
- At the time of writing, the Groups page listed 182 groups

### Example: Mustang Panda (G0129)
A China-based cyber espionage group, active since at least 2012, targeting government, diplomatic and non-governmental organisations.

Its Navigator layer highlighted the techniques it prefers:
- **Phishing** – Initial access through tailored phishing lures and decoy documents
- **Command and Scripting Interpreter** – Execution through PowerShell, cmd and scripts
- **Scheduled Task/Job** – Persistence
- **Obfuscated Files or Information** – Defence evasion
- **Process Discovery** – Discovery
- **Ingress Tool Transfer** – Downloading additional tools for command and control

Reading the highlighted techniques from left to right tells the story of the attack. For defenders, the layer works as a checklist of what to detect and defend against.

### Scenario: Aviation Sector Moving to the Cloud
As an analyst in an aviation organisation migrating to the cloud, I used the Groups page to find a relevant threat:

- **APT33 (G0064)** – A suspected Iranian group, active since at least 2013, with a particular interest in the aviation and energy sectors
- It used **Cloud Accounts** (`T1078.004`): compromised Office 365 accounts, together with the tool **Ruler**, to try to gain control of endpoints
- It also used **Password Spraying** (`T1110.003`) to gain access to accounts
- **Mitigation:** **User Account Management** (`M1018`) – Periodically review accounts and remove inactive or unnecessary ones
- **Detection:** `DET0546` – Detection of abused or compromised cloud accounts, for example sign-ins from impossible locations or unusual API activity

---

## Cyber Analytics Repository (CAR)
CAR is a knowledge base of ready-made **analytics** built around ATT&CK.

An **analytic** is detection logic: a definition of what to look for in log data to detect a specific adversary behaviour.  
An analytic is implemented as a rule or query in a SIEM, and that rule produces alerts.

Each CAR analytic includes:
- A **hypothesis** explaining the idea behind it
- The **information domain** (e.g. Host, Network)
- The **analytic type** (e.g. Situational Awareness)
- The ATT&CK techniques and tactics it detects, and its **level of coverage**
- A **data model** reference – which data is needed
- **Implementations** – pseudocode and queries for tools such as Splunk, LogPoint, EQL or Sigma
- Sometimes **unit tests** to validate that the analytic works

Event Query Language (EQL) - a query language developed by Elastic for analysing event-based data. It allows analysts to identify relationships between events and detect patterns such as parent-child processes or multi-step attacks.
LogPoint - a commercial SIEM system, similar to Splunk. It collects logs from across the organization into a single location, allowing for data searching and the definition of detection rules.
Sigma - not a system, but rather an open format for writing detection rules that is independent of any specific SIEM. 
The problem it solves is this: if you write a rule in Splunk and then switch to LogPoint, you would normally have to rewrite it. With Sigma, you write the rule once, and a conversion tool then transforms it into a query for whichever SIEM you choose—whether it’s Splunk, Elastic, LogPoint, or others.

### Example: CAR-2020-09-001 – Scheduled Task - FileAccess
- Windows stores scheduled tasks as files in `C:\Windows\System32\Tasks` and `C:\Windows\Tasks`
- The analytic looks for files created in these folders by any process other than `svchost.exe`, which is the normal Task Scheduler service
- It uses **Sysmon Event ID 11** (FileCreate)
- It detects `T1053.005` – Scheduled Task, with a **Low** level of coverage, so more analytics are needed for full coverage

### Example: CAR-2019-07-001 – Access Permission Modification
- Detects changes to file and directory permissions (`T1222`)
- Tactic: **Defense Evasion**
- Analytic type: **Situational Awareness** – it gives visibility into activity, and results need to be reviewed by an analyst

### CAR Analytics List and Coverage Layer
- The **Analytics List** shows all analytics with their ID, technique, implementations and platforms
- Most analytics target **Windows**
- Some techniques have several analytics, each catching a different way of performing the technique
- CAR's **Navigator layer** shows that it covers only part of the matrix. Discovery is well covered, while Initial Access has almost no coverage

### CAR and ATT&CK Are Separate
MITRE keeps ATT&CK (describing threats) separate from CAR (possible ways to detect them), because each organisation should choose the defences that fit its own environment.

---

## MITRE D3FEND
With ATT&CK, I learned how attacks happen. With **D3FEND**, I learned how to stop them.

**D3FEND** (Detection, Denial, and Disruption Framework Empowering Network Defense) is a knowledge graph of defensive techniques. It provides a common language for describing how security controls work.

- Its matrix is divided into seven tactics: **Model, Harden, Detect, Isolate, Deceive, Evict, Restore**
- D3FEND IDs start with `D3-`

### Digital Artifacts
Each defensive technique is linked to **digital artifacts** (such as passwords, credentials or network traffic), with a type of relationship (e.g. regenerates, hardens, analyzes).  
D3FEND connects defensive techniques to ATT&CK techniques that affect the same artifacts.

### Example: Credential Rotation (D3-CRO)
- Regularly changing passwords, API keys and certificates, so stolen credentials stop working
- Relationships: **regenerates** Certificate and Credential, **hardens** Credential, **use-limits** Password
- Sub-techniques: One-time Password, Certificate Rotation, Password Rotation
- Related attacks include Password Spraying, OS Credential Dumping and Golden Ticket
- Considerations: rotation must be managed carefully to avoid service interruption, and frequent password changes can lead to weak passwords

### Example: User Behavior Analysis (D3-UBA)
- Detects anomalies by comparing user activity to a baseline of normal behaviour
- Sub-technique **User Geolocation Logon Pattern Analysis** (`D3-UGLPA`) – Compares the location of logon attempts to the user's normal locations
- It relies on analysing **Network Traffic**
- It helps detect attackers using stolen credentials, such as compromised cloud accounts

---

## Other MITRE Projects

### Adversary Emulation Library 
- What it is: A library of emulation plans hosted on GitHub by MITRE’s Center for Threat-Informed Defense. These plans are based on known adversary behaviors and are designed to allow Red Teams to manually emulate a specific threat group, enabling the testing of defenses from the perspective of the threat.
There are two types:
1. Full Emulation: A comprehensive emulation of a specific group, covering the entire lifecycle from initial intrusion to data exfiltration.
2. Micro Emulation: A focused emulation of behaviors common to multiple groups. These plans cover a small number of specific techniques.
- What each plan contains: An intelligence summary of the group (who they target, how, and why) and the scope of their activity. All information is sourced from public data and mapped to the ATT&CK framework, followed by a detailed, step-by-step scenario.

### Caldera
- What it is: A free, open-source security platform built on the ATT&CK framework, designed to automate adversary emulation, assist manual Red Teams, and facilitate automated incident response.
- How it works: It consists of two components: a core system—featuring an asynchronous C2 server with a REST API and a web interface—and plugins that extend its functionality, such as agents, reports, and collections of TTPs. In essence, you deploy an agent on machines within your network, and the server sends it commands that emulate ATT&CK techniques. It follows the same structure as a C2 server, but is used for legitimate purposes.
- Primary use: The team creates a profile of a specific adversary and executes it within the network to identify the organization's vulnerabilities. This helps test defenses and train Blue Teams to detect specific threats.
- It should be run only in secure and isolated environments, because Caldera is actually a genuine offensive tool. This has three implications:
1. The server controls the computers on which the agents are installed. Caldera is structured exactly like an attacker's infrastructure: it features a Command and Control (C2) server, with agents installed on network machines that execute any command sent by the server. If a real-world attacker were to compromise the Caldera server, they would gain immediate access to a fully operational C2 server already connected to all those machines.
2. Caldera’s own security mechanisms are not robust enough to be relied upon.
MITRE itself states this: Caldera is a research and testing tool, not a product designed to withstand actual attacks.
3. The techniques it executes are real.
Caldera does not merely "simulate" actions; it actually carries out the commands—such as extracting passwords from memory, creating scheduled tasks, or exfiltrating files.

### AADAPT
- **Adversarial Actions in Digital Asset Payment Technologies**
- What it is: A comprehensive knowledge base of attacker tactics and techniques targeting digital asset management systems, derived from real-world attacks, observations, and vulnerabilities. It is structured according to the ATT&CK model, and its tactics and techniques complement those found in ATT&CK.
- Useful detail: Techniques marked with an ampersand (&) indicate an overlap with ATT&CK.
SOCRadar
- Identifiers: Begin with `ADT`, such as `ADT3025` – Blockchain data scraping.

### ATLAS
- **Adversarial Threat Landscape for Artificial-Intelligence Systems**
- What it is: A knowledge base of attacker tactics, techniques, and case studies targeting AI and machine learning systems.
- What makes it unique: It includes techniques not found in the ATT&CK framework.
- Case studies: Real-world attacks mapped to specific techniques. For example, in `AML.CS0046`, researchers embedded a prompt injection into a PDF file; when a user asked "Claude Computer Use" to work with that file, the malicious instruction was triggered.
- Identifiers: They begin with the prefix `AML`: `AML.T` for techniques, `AML.TA` for tactics, and `AML.CS` for case studies.

---

## How the Frameworks Work Together

| Resource | Question it answers |
|---|---|
| **ATT&CK** | How do attackers operate? |
| **ATT&CK Navigator** | What should we focus on, and where are our gaps? |
| **CAR** | How do we detect these techniques in our logs? |
| **D3FEND** | Which defensive techniques stop or limit these attacks? |
| **Adversary Emulation Library** | How exactly do we emulate a specific group? |
| **Caldera** | How do we run the emulation and test our defences? |
| **AADAPT / ATLAS** | How do attackers target digital assets and AI systems? |

---

## Key Takeaways
- ATT&CK gives the security community a common language and unique IDs for adversary behaviour
- TTPs describe how attackers work and are harder for them to change than IOCs
- Mapping CTI to ATT&CK turns threat reports into actionable detection and defence
- Group profiles and the Navigator help focus defences on the threats most relevant to the organisation
- CAR provides ready-made detection logic, D3FEND provides defensive countermeasures, and emulation tools test that the defences actually work
- MITRE's frameworks continue to expand into new areas such as cloud, digital assets and AI
