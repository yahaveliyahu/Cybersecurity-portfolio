# Systems as Attack Vectors – Summary

## Overview
In this room, I continued exploring the **SOC analyst** role, this time focusing on **systems as attack vectors** rather than humans.  
The room covered what systems are, why and how threat groups target them, and what a SOC analyst can do to keep an organisation secure.

**Prerequisites completed beforehand:** Junior Security Analyst room, Humans as Attack Vectors room.

---

## Definition of a System
A system is anywhere data lives and operations happen: a physical server, a lab machine, or a cloud platform like Microsoft 365.

The key idea: even with well-trained, phishing-aware users, if the systems themselves have a fragile "lock," attackers can bypass users entirely and breach the organisation directly, without anyone noticing.

Protecting systems is critical because the **blast radius** of a breach depends on what was breached:

| Breached System | Attack Value |
|---|---|
| A personal laptop of a school student | Steal Steam profile, add the PC to a botnet |
| A laptop of the bank's senior IT administrator | Get access to internal banking systems |
| A mail server of a criminal law company | Dump all mailboxes and blackmail the victim |
| A server at the heart of an industrial network | Encrypt the whole network with ransomware |
| A government website management panel | Deface the website (defacement / activism) |

---

## Attacks on Systems
In most serious attacks, the first goal is to **gain access**. What happens after depends on the attacker's motivation: stealing data, deploying ransomware, or destroying information beyond recovery. Nearly all attacks begin the same way, through one of three vectors:

### 1. Human-Led Attacks
Users are often the ones who unintentionally start the attack:
- Inserting a malicious USB found in public (e.g. a **RubberDucky**, which runs malware commands the instant it's plugged in)
- Downloading malware from pirated software
- Reusing a weak password across services

**81% of breaches involve stolen or breached passwords** — a striking reminder that credential hygiene is a system-security issue, not just a human-awareness one.

### 2. Vulnerabilities
Every piece of software can have security flaws.
- In 2024, **over 40,000** software vulnerabilities were published, and **more than 300** were actively exploited in major attacks.
- IT administrators often add to the risk themselves with weak passwords and unrestricted access.

### 3. Supply Chain
Every app on a PC depends on thousands of libraries. If a threat actor compromises one library or app and pushes a malicious update, **every downstream user is compromised at once**.
- Famous examples: **SolarWinds** and **3CX**, which affected thousands of companies.
- Even **TryHackMe itself** was once hit through a supply chain issue in **Lottie Player**, a library used for room animations.

This vector is especially hard to defend against because you can't fully control every piece of software running on your laptops, servers, and web apps.

---

## Software Vulnerabilities (Deep Dive)

- Vulnerabilities can hide for years: **Shellshock**, a major Linux vulnerability, existed since 1992 but wasn't discovered until 2014.
- If attackers find a flaw before defenders do, it's called a **zero-day** — the most dangerous scenario, since there's no patch yet.
- Once a vulnerability becomes public, it's assigned a **CVE (Common Vulnerabilities and Exposures)** number. From that moment, it's a race: attackers build exploits while defenders rush to patch.

**Windows CVE Timeline (one critical CVE almost every year):**

| CVE | Name | Description |
|---|---|---|
| CVE-2017-0144 | EternalBlue | Critical SMB vulnerability, leaked from the NSA, used to spread WannaCry |
| CVE-2019-0708 | BlueKeep | Wormable RDP vulnerability |
| CVE-2020-1472 | Zerologon | Netlogon flaw allowing full Active Directory domain takeover |
| CVE-2021-34527 | PrintNightmare | Critical flaw in the Print Spooler service |
| CVE-2022-30190 | Follina | Critical MS Office flaw (exploitable via MSDT, no macros needed) |
| CVE-2023-24880 | — | A zero-day security bypass (SmartScreen bypass) |

### Responding to Vulnerabilities
The real fix is always **a patch** from the vendor. Until then, survive the exposure window by:
- Restricting access to the system to only trusted IPs
- Applying temporary mitigations provided by the vendor
- Blocking known attack patterns on IPS or WAF

---

## Misconfigurations
Unlike vulnerabilities, a misconfiguration isn't a bug in the software — it's a **mistake in how the system was set up**, often for convenience (e.g. using "1111" instead of a real password).

Real-world examples:
- A weak **"123456"** password exposed chat logs for 64 million McDonald's job applicants
- A **misconfigured AWS cloud** environment led to a breach of 106 million bank customers
- Improperly configured **smart fridges** were silently recruited into a botnet

**Example progression of a misconfigured database breach:**
1. Install the latest SQL database — no vulnerabilities or misconfigurations yet
2. Put customer data in the DB — the database now stores sensitive data
3. Set the password to "1111" — **first misconfiguration: weak password**
4. Disable the firewall for the DB — **second misconfiguration: unrestricted access**
5. Wait a few days — threat actors steal the data

### Responding to Misconfigurations
Misconfigurations don't need a software update — just a better setup. As a SOC analyst you often only spot them *after* they're exploited, but proactive measures exist too:
- **Penetration Testing** – hire ethical hackers to simulate an attack and report flaws
- **Vulnerability Scans** – automated tools that detect default passwords or outdated software
- **Configuration Audits** – manual reviews against best practices like CIS benchmarks

---

## Mitigation Strategy
Attackers are opportunists — they'll take the easiest path, whether that's manipulating a person or exploiting a flaw in a system. Defense needs equal effort on both fronts, combining **Mitigation** and **Detection**.

| Mitigation | Description |
|---|---|
| Patch Management | Tracking and patching vulnerable systems significantly reduces the chance of a successful attack |
| Training for IT | An IT team aware of misconfiguration risks is less likely to leave systems unprotected |
| Network Protection | Restricting access to trusted people or IP addresses makes a system much harder to breach |
| Antivirus Protection | A good antivirus can stop or detect many different attacks, same as on the human side |

---

## Practice: Security Dashboard Lab
The lab presented a simulated **TryHackMe Security Dashboard** with two parts: handling **live security alerts** ("Systems at Risk") and building a **Remediation Plan**.

### Systems at Risk — 4 scenarios handled
1. **HQ-MAIL-02 (Exchange server):** Affected by CVE-2024-49040 and Internet-exposed — the correct response targets the actual vulnerability (patching/updating Exchange), not just symptoms like passwords.
2. **Corporate website (WordPress):** Admin panel brute-forced, homepage defaced with malware links and gambling ads — restoring from a backup alone doesn't fix the root cause; the weak/guessable admin credential needs to be replaced.
3. **Threat Intelligence alert (Cisco firewall):** A neighboring company was hit with ransomware via an unpatched Cisco firewall — the lesson is proactive patch auditing of all firewalls, not replacing the vendor or shutting protection down entirely.
4. **LPT-01518 (designer's laptop):** A trusted 3D design app suddenly started running malicious CMD commands right after an update — this pattern (trusted software + recent update + sudden malicious behavior) is a textbook **supply chain attack**, not user error or a standalone app vulnerability.

### Remediation Plan
Out of the available hardening actions, the four selected as most beneficial were:
1. **Secure Password Policy** — the only real defense against brute-force attacks
2. **Security Training for IT** — a well-informed IT team is less likely to leave systems unprotected
3. **Patch Management Policy** — organized patching is a major step in reducing exploitation risk
4. **Antivirus Protection** — a simple, effective response to common threats like data stealers or USB worms

---

## Key Takeaways
- Nearly every serious attack starts with gaining access to a system, through humans, vulnerabilities, or the supply chain
- Vulnerabilities are software bugs fixed by vendor patches; misconfigurations are setup mistakes fixed by better configuration — the response differs for each
- Zero-days are especially dangerous because no patch exists yet; survival depends on monitoring and temporary mitigations
- Supply chain attacks are hard to fully prevent since you don't control every dependency — vigilance for unusual behavior after updates is key
- Effective defense treats human and system attack surfaces with equal seriousness, combining patching, training, network restrictions, and antivirus protection
