# Cyber Kill Chain – Summary

## Overview
In this room, I learned about the **Cyber Kill Chain**, a cyber security framework introduced by **Lockheed Martin in 2011** and inspired by military kill chains.  
The framework breaks a cyber attack into **seven stages**, helping organisations understand how attacks are conducted and where they can be stopped.

The core idea: if defenders can detect and block the attacker at **any** stage, the chain is broken and the attack fails. The most damage happens in the final stage, so stopping the attack earlier is always better.

---

## The Seven Stages

### 1. Reconnaissance
The attacker gathers information about the target to find weaknesses and potential entry points.

There are two types:
- **Passive reconnaissance** – No direct interaction with the target, so it makes no "noise" (OSINT)
- **Active reconnaissance** – Requires interacting with the target, so it can be detected

Examples:
- WHOIS lookups (domain owners, contact info, registration dates)
- DNS queries (revealing DNS servers and public IP addresses)
- Google Dorking – using advanced search operators to find exposed sensitive files
- Social media and website reconnaissance
- Port scanning and vulnerability scanning (active)
- Physical reconnaissance of the target's premises (active)

Countermeasures:
- Minimise public information on websites, social media, and DNS records
- Use WHOIS privacy services
- Monitor network traffic and logs to detect scanning activity

---

### 2. Weaponisation
Based on the information gathered, the attacker builds a payload tailored to the target's weaknesses. At the end of this stage, a deliverable malicious file is ready.

Examples:
- Compiling a malicious program
- Using, editing, or creating exploit code
- Exploit kits – automated platforms containing many ready-made exploits
- Microsoft Office documents with malicious macros
- Obfuscation or encryption to evade detection

Countermeasures:
- Continuous user security awareness training
- Disabling unnecessary features, software, and browser plugins
- Disabling macros or allowing only signed and trusted ones (enforced via Windows Group Policy)

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
- Physical delivery via USB drives or DVDs

Countermeasures:
- User training on phishing, social engineering, and safe browsing
- Email and web filtering
- Web Application Firewalls (WAFs)- A firewall that protects web applications. It examines the content of the request itself: what the user has entered into fields, the URL, and cookies.
- Network monitoring and patch management

---

### 4. Exploitation
The payload exploits a weakness in the target system to gain access.

Examples:
- Logging in with default or weak credentials
- Stealing passwords through phishing
- Exploiting software vulnerabilities in public-facing applications
- SQL injection and buffer overflow
- **Zero-day exploits** – exploits used before the vendor is even aware the vulnerability exists, so no patch is available

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
The attacker ensures **persistence**, so they can return to the system later without exploiting it again. The keyword of this stage is **persistence**.

Examples:
- Scheduled tasks in Windows or cron jobs in Linux
- Modifying startup scripts or configuration files
- Installing a new Windows service or Linux daemon (Software that runs in the background on a Linux system—continuously, without being opened by a user, and without a window or interface. It typically starts automatically when the computer powers on and waits to perform its function)
- Adding a local user account
- Installing malware, rootkits (Malicious software whose primary objective is to remain hidden. It grants the attacker access at the highest privilege level (root), while simultaneously concealing itself and the attacker's activities from the operating system and security tools), or **backdoors** – hidden ways into the system that bypass normal authentication
- Using LOLBins (Living-Off-the-Land Binaries) – legitimate built-in system tools
- **Web shells** – small scripts on a compromised web server that let the attacker run OS commands through a browser, hidden inside normal HTTPS traffic

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
- **Fast Flux** – A single domain is linked to hundreds or thousands of IP addresses, and the addresses change every few minutes.

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

## Practical Exercise
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
