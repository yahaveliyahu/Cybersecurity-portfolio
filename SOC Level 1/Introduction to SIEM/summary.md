# Introduction to SIEM – Summary

## Overview
In this room, I explored the fundamentals of **SIEM (Security Information and Event Management)** — the core security solution used by SOC analysts to monitor, detect, and investigate threats.

The room walked through why individual, scattered logs aren't enough on their own, how a SIEM solves that by centralizing and correlating data from across a network, and finished with a hands-on walkthrough of investigating a real alert from start to finish.

---

## Learning Objectives
- Understand the different types of log sources
- Identify the limitations of working with isolated logs
- Recognize the importance of a SIEM solution
- Explore the features of a SIEM solution
- Learn various types of log sources and their ingestion into a SIEM
- Understand the process behind alerting and alert analysis

---

## Key Areas

### 1. Log Sources: Host-Centric vs. Network-Centric
Every device in a network (endpoints, servers, routers, etc.) continuously generates logs — a trail of activity useful for spotting malicious behavior or general troubleshooting. These log sources fall into two categories:

**Host-Centric Log Sources** — events that occur on or relate to a specific host (Windows, Linux, servers):
- A user accessing a file
- A user attempting to authenticate
- Process execution activity
- A process adding/editing/deleting a registry key or value
- PowerShell execution

**Network-Centric Log Sources** — events generated when hosts communicate with each other or the internet (firewalls, IDS/IPS, routers):
- SSH connections
- A file accessed via FTP
- Web traffic
- A user accessing company resources through VPN
- Network file sharing activity

---

### 2. The Problem: "Answers Nowhere"
Even with all these logs being generated, analyzing them manually is far from simple. Key challenges include:

- **Numerous Log Sources** — hundreds of events per second, scattered across many devices
- **No Centralization** — analysts would need to connect to each source individually (SSH, RDP, etc.) to investigate
- **Limited Context** — a single log rarely tells the full story; correlating logs across sources can reveal things like lateral movement that look harmless in isolation
- **Limited Analysis** — the sheer volume of logs makes manual review to catch abnormal activity nearly impossible
- **Format Issues** — different sources log in different formats, making cross-source analysis harder

---

### 3. Why SIEM?
A SIEM solves these problems by collecting logs from all log sources, standardizing their format, correlating them, and detecting malicious activity through detection rules. Its core loop:

1. Collect data from sources
2. Aggregate data
3. Discover and detect threats
4. Identify breaches and investigate alerts

---

### 4. Core Features of a SIEM
- **Centralized Log Collection** — logs from endpoints, servers, firewalls, etc. are pulled (via agents/APIs) into one place, removing the need to check each machine individually
- **Normalization of Logs** — raw logs come in different formats. *Parsing* breaks a log into fields; *Normalization* converts logs from different sources into one consistent format
- **Correlation of Logs** — individual events can look harmless alone, but correlated together can reveal an attack. Example: a user logging in via VPN from a new IP, accessing shared documents, running a PowerShell script, and then making an outbound connection — each step looks fine on its own, but together they point to potential data exfiltration from compromised VPN credentials
- **Real-Time Alerting** — built-in and custom detection rules trigger alerts when their conditions are met
- **Dashboards and Reporting** — summarized, actionable views of the environment: alert highlights, system/health notifications, failed login attempts, events ingested, rules triggered, top domains visited, etc.

---

### 5. Log Sources and Ingestion
**Windows** — every event is recorded and assigned a unique Event ID, viewable through **Event Viewer** (Windows Logs: Application, Security, Setup, System, Forwarded Events). These are forwarded to the SIEM for centralized monitoring.

**Linux** — common log locations:
- `/var/log/httpd` — HTTP request/response and error logs
- `/var/log/cron` — cron job events
- `/var/log/auth.log` and `/var/log/secure` — authentication logs
- `/var/log/kern` — kernel-related events

**Web Server** — Apache-related logs are commonly found at `/var/log/apache` or `/var/log/httpd`, and are critical for spotting web attack attempts.

**Ingestion Methods:**
1. **Agent / Forwarder** — a lightweight tool installed on the endpoint that captures and forwards logs to the SIEM
2. **Syslog** — a widely used protocol for sending real-time log data to a centralized destination
3. **Manual Upload** — some SIEMs (Splunk, ELK) allow uploading offline data for quick analysis
4. **Port-Forwarding** — the SIEM listens on a configured port while endpoints forward data to it

---

### 6. Detection Rules
Detection rules are logical expressions that trigger alerts when their conditions are met. Examples covered:

- 5 failed login attempts in 10 seconds → **Multiple Failed Login Attempts**
- A successful login right after multiple failures → **Successful Login After Multiple Login Attempts**
- A USB device being plugged in (if restricted by policy)
- Outbound traffic exceeding a set threshold (e.g., 25 MB) → potential **data exfiltration**

Two worked examples:
- **Event Log Clearing:** `Log_Source = WinEventLog AND EventID = 104` → alert *"Event Log Cleared"* (adversaries often clear logs post-exploitation to cover their tracks)
- **Whoami Detection:** `Log_Source = WinEventLog AND EventCode = 4688 AND NewProcessName contains "whoami"` → alert *"WHOAMI command Execution DETECTED"* (Event ID 4688 = process creation, commonly abused after privilege escalation)

This reinforced why normalized, consistent field-value pairs across logs are essential — rules depend on being able to reliably match specific fields.

---

### 7. Alert Investigation
Analysts spend most of their monitoring time on dashboards. Once an alert fires, they examine the related events, check which rule conditions were met, and classify the alert:

- **False Positive** → may require **tuning** the rule to prevent recurrence
- **True Positive** → requires further investigation, which may include:
  - Contacting the asset owner about the activity
  - Isolating the infected host
  - Blocking the suspicious IP

---

## Hands-On Exercise: Investigating a CryptoMiner Alert
The room ended with a practical walkthrough tying all of this together:

1. On the SIEM dashboard, a **"Potential CryptoMiner Activity"** alert popped up, pointing to a suspicious entry in the Process Name table: **`cudominer.exe`**, with a count of 1 — standing out against normal processes like `chrome.exe`, `cmd.exe`, and `svchost.exe`
2. Clicking through to the triggering event (Event ID **4688**, Log Source **WindowsEventLogs**) showed the full record: the process **`C:\Users\Chris\temp\cudominer.exe`** was executed by user **Chris** on host **HR_02**
3. Checking the detection rule behind the alert:
   > `Alert "Potential CryptoMiner Activity" IF EventID = 4688 AND Log_Source = WindowsEventLogs AND ProcessName = (*miner* OR *crypt*)`
   
   The process name matched on the substring **"miner"** (`cudo**miner**.exe`)
4. Classified this as a **True Positive** — the process name itself indicates unauthorized cryptomining, not a coincidental match on a legitimate process, and unauthorized miners can indicate compromise and abuse organizational resources
5. Took the corresponding action: **"True positive and isolate the host"**

This exercise mirrored the full analyst workflow: dashboard → alert → event details → rule logic → classification → action.

---

## Key Takeaways
- Logs alone aren't enough — without centralization and correlation, individual events lose their context
- A SIEM's value comes from four things: collection, normalization, correlation, and alerting
- Detection rules rely on specific fields (Event ID, process name, log source, etc.) matching defined conditions
- Not every alert is malicious — the analyst's job is to correctly classify True vs. False Positives and act accordingly
- Investigating an alert means working backward from the trigger to the raw event to the rule logic, to understand exactly *why* it fired
