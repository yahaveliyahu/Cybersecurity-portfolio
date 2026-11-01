# Cyber Kill Chain – Summary

## Overview
In this room, I learned about the **Cyber Kill Chain**, a cyber security framework introduced by **Lockheed Martin in 2011** and inspired by military kill chains.  
The framework breaks a cyber attack into **seven stages**, helping organisations understand how attacks are conducted and where they can be stopped.

The core idea: if defenders can detect and block the attacker at **any** stage, the chain is broken and the attack fails. To succeed, an adversary must pass through **all** phases, so the earlier the chain is broken, the less damage is done — the most damage happens in the final stage.

Why it matters: understanding the Kill Chain helps defend against ransomware, security breaches, and **APTs** (Advanced Persistent Threats), and helps identify missing security controls. It is especially useful for roles such as **SOC Analyst, Security Researcher, Threat Hunter, and Incident Responder**, who need to recognise intrusion attempts and understand the attacker's goals.

---

## The Seven Stages

### 1. Reconnaissance
The attacker gathers information about the target to find weaknesses and potential entry points.

There are two types:
- **Passive reconnaissance** – No direct interaction with the target, so it makes no "noise" (OSINT)
- **Active reconnaissance** – Requires interacting with the target, so it can be detected

**OSINT (Open-Source Intelligence)** – gathering insight from publicly available information. Public sources include: search engines, print/online media, social media, forums and blogs, public record databases, and WHOIS/technical data.

Examples / techniques:
- WHOIS lookups (domain owners, contact info, registration dates)
- DNS queries (revealing DNS servers and public IP addresses)
- Google Dorking – using advanced search operators to find exposed sensitive files
- Social media and website reconnaissance
- Email harvesting – obtaining email addresses from public/paid/free services, used to launch phishing attacks
- Port scanning and vulnerability scanning (active)
- Physical reconnaissance of the target's premises (active)

Recon / OSINT tools:
- theHarvester – command-line tool (Kali) that gathers emails, names, subdomains, IPs, and URLs from many public sources (mostly passive)
- Hunter.io – web service focused on a domain's email addresses and the organisation's email format (e.g. `{first}.{last}@`)
- OSINT Framework – a web directory of OSINT tools organised by category
- Shodan – a search engine for internet-connected **devices and services** (open ports, software versions, IoT, exposed databases, ICS/SCADA). Passive: it already scanned the internet, so querying it leaves no trace on the target

Countermeasures:
- Minimise public information on websites, social media, and DNS records (and do OSINT on yourself to see what's exposed)
- Use WHOIS privacy services 
- Monitor network traffic and logs to detect scanning activity

---

### 2. Weaponisation
Based on the information gathered, the attacker turns raw intel into an attack tool — crafting malware/exploits into a payload tailored to the target's weaknesses. At the end of this stage, a deliverable malicious file is ready.

Key terminology:
- Malware – software designed to damage, disrupt, or gain unauthorised access to a computer
- Exploit – a program/code that takes advantage of a vulnerability or flaw
- Payload – the malicious code the attacker runs on the system

Where the "weapon" comes from: attackers use automated tools, buy ready-made payloads on the Dark Web, or (for sophisticated / nation-sponsored APTs) write custom malware to be unique and evade detection.

Examples / tactics:
- Compiling a malicious program
- Using, editing, or creating exploit code
- Exploit kits – automated platforms containing many ready-made exploits
- Microsoft Office documents with malicious macros
- Setting up C2 infrastructure in advance
- Embedding a backdoor to bypass security mechanisms
- Tailoring phishing templates or OAuth-consent apps to look legitimate
- Obfuscation or encryption to evade detection

OAuth – a protocol that lets an app get limited access to your account on another service without your password (e.g. "Sign in with Google"). In a consent phishing attack, the attacker builds a malicious app with a legitimate-looking name; the victim approves it on a real Google/Microsoft consent screen, and the attacker's app receives an access token. Dangerous because no password is stolen (so changing it doesn't help) and MFA doesn't block it.

Countermeasures:
- Continuous user security awareness training
- Disabling unnecessary features, software, and browser plugins
- Disabling macros or allowing only signed and trusted ones (enforced via Windows Group Policy)
- Restricting who can approve third-party OAuth apps

---

### 3. Delivery
The attacker sends the weaponised payload to the target, choosing the method based on the reconnaissance results.

Examples:
- Phishing and spear phishing emails (e.g., `invoice.pdf.exe` disguised as a PDF)
- Malicious web links (using domain spoofing and URL shortening)
- File-sharing platforms
- Malvertising – malicious ads on legitimate websites
- **Smishing** – SMS phishing with malicious links
- Social engineering
- **USB drops** – leaving infected USB drives in public places, or mailing them to a company with its logo while posing as a customer. Exploits curiosity/trust
- **Watering hole attacks** – compromising a website the target group frequently visits

Drive-by download – malware downloaded without the user's knowledge, just from visiting a page — either by exploiting a browser/plugin flaw automatically, or by tricking the user (e.g. a fake "update your browser" pop-up). Watering hole is how victims are brought to the page; drive-by download is what happens to them there.

Countermeasures:
- User training on phishing, social engineering, and safe browsing
- Email and web filtering
- Web Application Firewalls (WAFs)- A firewall that protects web applications. It examines the content of the request itself: what the user has entered into fields, the URL, and cookies.
- Network monitoring and patch management

---

### 4. Exploitation
The attacker's code executes on the target, taking advantage of a weakness, to gain access.

Examples:
- Malicious macro execution (e.g. delivered via phishing, runs ransomware on open)
- Logging in with default or weak credentials
- Stealing passwords through phishing
- Exploiting software vulnerabilities in public-facing applications
- SQL injection and buffer overflow
- **Zero-day exploits** – exploits used before the vendor is even aware the vulnerability exists, so no patch is available
- **Known CVEs** – exploiting unpatched, publicly documented vulnerabilities

CVE (Common Vulnerabilities and Exposures) – a unique ID for a known vulnerability (e.g. CVE-2021-44228 = Log4Shell), managed by MITRE. Each usually has a CVSS severity score (0–10). A CVE is known (usually patched); a zero-day is unknown (no patch). Publishing a CVE helps defenders patch but also tells attackers what to target — a race between patching and exploitation (e.g. WannaCry exploited EternalBlue (Cyber attack tool) months after a fix existed).

Countermeasures:
- Enforcing strong password requirements
- **Multi-Factor Authentication (MFA)** – prevents access even with valid credentials
- Patch management and vulnerability scanning
- Intrusion Prevention Systems (IPS)
- WAFs to block SQL injection, XSS, and CSRF

**SQL Injection**  
The attacker inserts SQL code into an input field, and the application runs it as part of its database query.  
Example: entering `admin' --` as a username closes the string and comments out the password check, allowing login without a password.  
Defence: prepared statements (parameterised queries), WAF, least privilege for the database user.

**XSS (Cross-Site Scripting)**  
The attacker injects malicious JavaScript into a legitimate website, which then runs in other users' browsers.  
Example: posting a comment containing `<script>` code that sends visitors' cookies to the attacker's server.  
Defence: input validation and output escaping, WAF.

**CSRF (Cross-Site Request Forgery)**  
The attacker tricks a logged-in user's browser into sending an unwanted request to a trusted site.  
Example: a malicious page loads a hidden link to `bank.com/transfer?to=attacker`, and the browser sends it with the user's session cookie.  
Defence: CSRF tokens, which a malicious site cannot know.

---

### 5. Installation
The attacker ensures **persistence**, so they can return to the system later without exploiting it again. The keyword of this stage is **persistence**. A **backdoor** (also called an access point) is a hidden way back in that bypasses normal authentication; a **persistent backdoor** survives reboots.

Examples:
- Scheduled tasks in Windows or cron jobs in Linux
- Modifying startup scripts or configuration files
- Installing a new Windows service or Linux daemon (Software that runs in the background on a Linux system—continuously, without being opened by a user, and without a window or interface. It typically starts automatically when the computer powers on and waits to perform its function)
- Adding a local user account
- Installing malware, rootkits (Malicious software whose primary objective is to remain hidden. It grants the attacker access at the highest privilege level (root), while simultaneously concealing itself and the attacker's activities from the operating system and security tools), or **backdoors** – hidden ways into the system that bypass normal authentication
- Using LOLBins (Living-Off-the-Land Binaries) – legitimate built-in system tools (e.g. PowerShell, certutil, sc.exe, reg) abused by attackers, so they blend in and leave no new files
- Web shells – small scripts planted on a compromised web server that let the attacker run OS commands through a browser, hidden inside normal HTTPS traffic
- Registry Run Keys - These are keys in the Registry where any entries listed are automatically executed upon logon.
- Masquerading – giving the malicious file/service a legitimate-looking name
- Timestomping – modifying a file's timestamps so it looks old/legitimate

Key tools & concepts:
- Metasploit – the best-known penetration-testing framework (comes with Kali). A huge library of ready-made exploits and payloads.
- Meterpreter – Metasploit's advanced in-memory payload that gives the attacker an interactive remote shell (list processes, screenshots, keylogging, file transfer).
- metsvc is a persistent backdoor from Metasploit that installs Meterpreter as a Windows service on the victim's machine. Essentially, it is the tool that transforms temporary Meterpreter access into persistent access that survives a system reboot.
- DLL injection – forcing a legitimate running process to load a malicious DLL (a shared code library in Windows). The code then runs inside the legitimate process, with its permissions and under its name — good for evasion.

Countermeasures:
- Monitoring new processes and services, including their parent processes
- Endpoint Detection and Response (EDR)
- Regular system audits against a secure baseline
- **Application allowlisting** – only approved applications are allowed to run

---

### 6. Command and Control (C2)
The attacker establishes a covert communication channel between the compromised system and their own infrastructure to control it remotely.

Examples:
- Using common protocols (HTTP, HTTPS, DNS, SMTP) to blend in with legitimate traffic
- Encrypted channels such as HTTPS to hide from monitoring tools
- DNS tunnelling – hiding data inside DNS requests
- Social media platforms and legitimate cloud services (Dropbox, Google Docs)
- **DGA (Domain Generation Algorithms)** – generating thousands of domains, of which only a few are registered
- **Fast Flux** – A single domain is linked to hundreds or thousands of IP addresses, and the addresses change every few minutes (compromised devices act as proxies in front of the hidden C2 server).

DGA swaps the domain; Fast Flux keeps the domain and swaps the IP addresses — both make takedown harder.

Countermeasures:
- Network monitoring with firewalls, IDS, and IPS
- Monitoring DNS traffic for unusually long queries or suspicious volumes
- Web traffic monitoring and content filtering
- Encryption inspection for HTTPS traffic
- Honeypots to detect and study attacker behaviour

---

### 7. Actions on Objectives
The attacker carries out their original goal.

Examples:
- **Data exfiltration** – stealing sensitive files (common in espionage)
- **Ransomware** – encrypting files and demanding payment for the decryption key
- Deleting backups and shadow copies (Shadow Copy = a Microsoft technology that creates backup copies/snapshots of files or volumes) — done so victims can't restore
- Destructive attacks – deleting or corrupting data
- Financial theft through fraudulent transfers
- Keylogging to capture passwords and sensitive input
- **Lateral movement** – compromising other systems on the network
- Manipulating Industrial Control Systems (ICS)

Countermeasures:
- Data Loss Prevention (DLP)
- Reliable backup and recovery plans (key against ransomware)
- Network segmentation to stop lateral movement
- Strict access controls and the **principle of least privilege** – giving each user only the access they need
- User activity monitoring and EDR

---

## Practical Exercise 1
The room included a drag-and-drop exercise where I matched attacker actions to their stages:

| Action | Stage |
|---|---|
| DNS query, Google Dorking | Reconnaissance |
| Compile a malicious program | Weaponisation |
| Smishing, Publish a malicious web link | Delivery |
| Exploit public-facing application, Log in with default credentials | Exploitation |
| Add a malicious cron job in Linux, Add a local account | Installation |
| Use HTTP / HTTPS for communication | Command and Control |
| Data exfiltration, Keylogging | Actions on Objectives |

A helpful way to classify each action is to ask: **"What is the attacker trying to achieve?"**
- Learn about the target → Reconnaissance
- Build a tool → Weaponisation
- Send it → Delivery
- Get in → Exploitation
- Stay in → Installation
- Control remotely → C2
- Achieve the goal → Actions on Objectives

---
## Practical Exercise 2
The exercise maps the attack onto the Kill Chain using MITRE ATT&CK techniques — notably, the icons were shown with no stage names and out of order, forcing identification by meaning, not position.

| Stage | 	Item (MITRE technique) | Why | 
|---|---|---|
| Exploitation | powershell | Running code on the system |
| Delivery | spearphishing attachment | transmit the malicious attachment sent to a supplier (Fazio Mechanical) to steal its access |
| Weaponization | exploit public-facing application | The exploit against Target’s internet-facing supplier portal is the weapon prepared to breach the internal network |
| Installation | dynamic linker hijacking | Loading a malicious library in place of a legitimate one — persistence (akin to DLL injection) |
| Command and Control | fallback channels | Backup C2 channels used if the primary is blocked |
| Actions on Objectives | data from local system | Collecting the card data from POS systems — the final goal |

---

## Red Team vs. Blue Team Perspective
- **Blue Team** – Detects and blocks attacker actions at each stage to break the chain
- **Red Team / Penetration Testers** – Follow the same stages as real adversaries to simulate realistic attacks and test whether existing security controls work

---

## Key Takeaways
- An attack is a sequence of seven stages, not a single event
- Breaking the chain at any stage stops the attack
- The earlier the attack is stopped, the less damage is done
- Each stage has its own attacker techniques and matching defences
- Persistence (Installation) and covert communication (C2) let attackers stay hidden for long periods
- Defence relies on layered controls: user training, MFA, patching, monitoring, EDR, segmentation, and least privilege
