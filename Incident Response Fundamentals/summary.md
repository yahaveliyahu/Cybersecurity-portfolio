# Incident Response Fundamentals – Summary

## Overview
In this room, I explored the fundamentals of **Incident Response (IR)**: how an organisation handles a cyber security incident from its start to its end.
I learned what incidents are and how they are prioritised, the common types of incidents, the SANS and NIST incident response frameworks, the tools used for incident detection and response, the role of Playbooks, and the Incident Response Plan. I finished with a hands-on lab in which I investigated a phishing incident.

---

## Why Incident Response Matters
Just like securing a home, incident response covers both the **proactive measures** taken before an attack and the **reactive measures** taken after an attacker gets past the defences.

In the digital world, attacks on organisations are called **Cyber Security Incidents**. They are reported daily and can cost organisations thousands of dollars. Like home security, they require planning and resources to avoid huge losses.

**Incident Response** handles an incident from start to end: deploying security to prevent incidents, fighting them when they happen, and minimising their impact.

---

## Events, Alerts and Incidents

### Events
Devices run many processes:
- **Interactive processes** – actions the user performs, such as playing a game or watching a video.
- **Non-interactive processes** – run in the background without user interaction and are necessary for the device.

Every action these processes perform generates an **event**. Since many processes run all the time, events are generated in huge numbers. Some of them may point to harmful activity.

Checking all these events manually is not possible, so they are ingested into **security solutions as logs**, and the security solutions look for harmful activity in them.
The real challenge starts after the security solution points this activity out.

### Alerts
When a security solution finds an event or a group of events linked to possibly harmful activity, it triggers an **alert**. The security team then analyses the alert to decide whether it is real:

- **False Positive** – The alert points to something that looks dangerous but is not harmful.  
  *Example:* An alert on a large amount of data transferred from a system to an external IP address. The analysis found that the system was running a backup to a cloud storage service.
- **True Positive** – The alert points to something that is actually harmful.  
  *Example:* An alert on a phishing email that was confirmed to be an attempt to compromise a user's system.

### Incidents
True positive alerts are referred to as **incidents**.

---

## Incident Severity
Once an alert is classified as an incident, it is given a **severity level** based on the impact it can create. This helps the team decide which incident to handle first when several incidents happen at the same time.

- **Critical** – highest priority
- **High**
- **Medium**
- **Low**

---

## Types of Incidents

### 1. Malware Infections
Malware is a malicious program that can damage a system, network or application. Most incidents are related to malware, usually delivered through files such as documents, text files or executables.

### 2. Security Breaches
An unauthorised person gets access to confidential data. They are extremely important because many businesses rely on confidential data that must only be accessible to authorised personnel.

### 3. Data Leaks
Confidential information of an individual or organisation is exposed to unauthorised entities. Attackers use data leaks to cause reputational damage or to threaten victims and get what they want.
Unlike security breaches, data leaks can also be caused **unintentionally**, by human error or misconfiguration.

### 4. Insider Attacks
Attacks that someone inside the organisation starts on purpose.  
*Example:* A disgruntled employee infecting the whole network through a USB drive on his last day.
They are dangerous because insiders always have greater access to resources than outsiders.

### 5. Denial of Service (DoS) Attacks
The attacker floods a system, network or application with false requests until its resources are exhausted and it becomes unavailable to legitimate users. This targets **Availability**, one of the three pillars of cyber security.


### Impact Depends on the Organisation
Incident types cannot be ranked by impact, because the same incident can be disastrous for one organisation and minor for another.  
*Example:* A company might not be significantly harmed by a data breach, if the information it stores is useless to others. However, a DoS attack on its main website could cause a massive loss, because its services depend on that website.

---

## Incident Response Frameworks
Since incidents differ in nature, organisations need a **structured process** for incident response. **Incident Response Frameworks** are generic approaches to follow in any incident for an effective response.

The two widely used frameworks come from:
- **SANS** – an organisation that offers cyber security courses and certifications.
- **NIST** – an organisation that develops cyber security standards and guidelines.

### SANS – 6 Phases (PICERL)

| Phase | Description | Example |
|---|---|---|
| **Preparation** | Building the resources needed to handle incidents: IR teams, an IR plan and security solutions | Phishing awareness training for employees |
| **Identification** | Looking for abnormal behaviour that may indicate an incident, using security solutions and techniques to monitor abnormal events | The team notices a huge amount of data being sent out from a host. Analysis shows it was compromised after a malicious file was downloaded from a phishing email attachment |
| **Containment** | Minimising the impact, e.g. isolating the victim machine or disabling compromised accounts | Isolating the host from the network so the attacker cannot move to other systems |
| **Eradication** | Removing the threat from the environment and making sure it is clean before moving to recovery | Running a deep malware scan to remove the malicious software from the host |
| **Recovery** | Recovering affected systems from backup or rebuilding them, then testing them before they are used again | Reconfiguring the compromised host and restoring the exfiltrated data from backup |
| **Lessons Learned** | Identifying and documenting gaps in the detection and analysis of the incident, to improve future response | A post-incident review meeting to analyse the root cause and improve security |

### NIST – 4 Phases
NIST's framework is similar to SANS but reduces the phases to four:

1. **Preparation**
2. **Detection and Analysis** (equivalent to SANS Identification)
3. **Containment, Eradication and Recovery** (three SANS phases combined into one)
4. **Post-Incident Activity** (equivalent to SANS Lessons Learned)

---

## Incident Response Plan
Organisations build their own IR process based on these frameworks and document it in a formal **Incident Response Plan**.
The plan is approved by senior management and defines the procedures to follow **before, during and after** an incident.

Key components include:
- Roles and responsibilities
- Incident response methodology
- Communication plan with stakeholders, including law enforcement
- Escalation path

---

## Incident Detection and Response Tools
Finding abnormal behaviour manually in the Identification (SANS) / Detection and Analysis (NIST) phase is very hard. Several security solutions help detect incidents, and some of them can also carry out other phases such as containment and eradication.

- **SIEM (Security Information and Event Management)** – Collects all important logs in one central location and correlates them to identify incidents.
- **AV (Antivirus)** – Detects known malicious programs and regularly scans the system for them.
- **EDR (Endpoint Detection and Response)** – Deployed on every endpoint (a user's computer or a server) to protect it against more advanced threats. It can also **contain and eradicate** threats, not only detect them.

---

## Playbooks and Runbooks
After an incident is identified, the team must investigate how far the attack spread, prevent further damage, and remove the threat from its root. These steps differ between incident types, so ready-made instructions save a lot of time.

### Playbooks
Step-by-step guidelines for responding to a specific type of incident. They save time because the team does not need to decide what to do from scratch.

*Example – Phishing Email Playbook:*
1. Notify all stakeholders of the phishing incident
2. Determine if the email is malicious by analysing its header and body
3. Look for attachments and analyse them
4. Determine if anybody opened the attachments
5. Isolate the infected systems from the network
6. Block the email sender

### Runbooks
Detailed, step-by-step instructions for carrying out specific steps during an incident. These may vary depending on the resources available for the investigation.

---

## Hands-on Lab – Phishing Incident
### Scenario
I started an incident by downloading a malicious attachment from a phishing email, and then investigated it:
1. Find how many hosts have the same file, since a single phishing campaign often targets several employees in the same organisation.
2. Take the right action on each host, depending on whether the file was only downloaded or also executed.
3. Review the detailed timeline of events on the infected host.

### The Phishing Email
The email, from "Jeff Johnson | Finance Advisor", contained an attachment called `Payslip.pdf`. It showed clear signs of phishing:
- **Inconsistency** – The subject mentioned a pay slip for November, while the body referred to a payment for April.
- **Social engineering** – It used money as bait and pushed the user to open the attachment and "execute the steps" in it.

### Threat Vector
The **way a threat enters the organisation** – the channel the attack used to reach the victim. 

Common threat vectors:
- **Email** – a malicious attachment or link in an email
- **USB** – an infected USB drive connected to a computer
- **Web / Drive-by download** – visiting a website that downloads malware
- **Exploiting a vulnerability in an exposed service** – for example, RDP open to the internet

It is important to separate the **channel** from the **payload**: the email is the threat vector, while `Payslip.pdf` is the payload – the malware delivered through that channel.

**How I found it:** the scenario states the incident starts by downloading an attachment from a phishing email, and the email itself showed the phishing signs above.
The EDR also showed the same file on several hosts, which fits a phishing campaign sent to several employees.
In this case, the file was downloaded to **3 endpoints**:

| Host | State | Action |
|---|---|---|
| Host-ATYU | Not Executed | Quarantine |
| Host-IOPE | Not Executed | Quarantine |
| Host-HKNV | Executed | Investigate |

### Quarantine vs Investigate
- **Quarantine** – Used where the file was only downloaded. The EDR moves the file to an isolated location (accessible only to the EDR itself) where it cannot run, without deleting it.
The file disappears from its original location, so the user cannot open it by mistake. This keeps it available for analysis and allows restoring it if it turns out to be a false positive.
- **Investigate** – Used where the file was executed. Once malware has run, the host must be treated as **compromised**, because the malware may have created files, changed settings or communicated with the attacker.
Quarantining the original file is not enough.

### Infected Host Timeline
The investigation of Host-HKNV (user: Smith) showed the process tree of the attack:

`explorer.exe` → `chrome.exe` → File Downloaded → `Payslip.pdf` → executed:
- `Powershell.exe`
- Malicious DNS Query

What each step means:
1. **explorer.exe** – The Windows interface process (desktop, taskbar, Start menu). Almost every program the user opens is launched from it. A normal starting point.
2. **chrome.exe** – The user opened Chrome, probably to read the email in a webmail. Still normal behaviour.
3. **File Downloaded** – The attachment was downloaded through Chrome.
4. **Payslip.pdf** – The malicious file from the phishing email. Opening it caused two actions:
   - **Powershell.exe** – PowerShell is a command-line tool and scripting language built into Windows, able to download files, change system settings and run code. **A legitimate PDF should never launch PowerShell**, so this is the strongest sign the file is malicious.
   Attackers like PowerShell because it already exists on every Windows machine and looks legitimate – a technique called **Living off the Land** (using tools that already exist in the system instead of bringing your own).
   - **Malicious DNS Query** – **DNS** is the "phone book" of the internet: it translates a domain name (like google.com) into an IP address. A **DNS query** is the computer asking for the IP address of a domain.
   Here the malware tried to reach a malicious domain, most likely the attacker's **C2** server, to receive commands or download more parts of the malware.

What an analyst would do next in a real environment:
- **Isolate the host** from the network, because it communicated with a malicious domain.
- **Check what PowerShell ran** – the full command line – to see what was downloaded or changed.
- **Use the domain from the DNS query as an IOC** – block it across the organisation (Firewall or DNS server) and check whether other hosts contacted it.
- **Reset Smith's password** as a precaution.

---

## Key Takeaways
- Processes generate events, events are sent to security solutions as logs, and harmful activity triggers alerts
- Not every alert is an incident; only true positives become incidents
- Severity levels (Critical, High, Medium, Low) decide which incident is handled first
- Incident types include malware infections, security breaches, data leaks, insider attacks and DoS, and their impact depends on the organisation
- SANS (6 phases) and NIST (4 phases) provide a structured way to respond to incidents
- An Incident Response Plan formalises the process and is approved by senior management
- SIEM, AV and EDR support detection, and EDR can also contain and eradicate threats
- Playbooks guide the response to a specific type of incident, and Runbooks give detailed steps for carrying it out
- The threat vector is the channel of the attack (Like an email attachment), while the malicious file is the payload
- A downloaded file is quarantined, while an executed file means the host is compromised and must be investigated
- A process tree shows the full chain of an attack, and unusual parent → child relationships (a PDF launching PowerShell) are strong signs of malicious activity
