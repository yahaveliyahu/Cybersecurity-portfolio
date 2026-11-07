# Identity and Access Management – Summary

## Overview
In this room, I explored the fundamentals of **Identity and Access Management (IAM)**.
The focus was on how systems recognise users, verify who they are, decide what they may do, enforce those decisions, and keep a record of their actions.

These processes do not replace one another. A weakness in any single step weakens the security of the whole system, which ultimately protects the **confidentiality, integrity, and availability (CIA)** of data and resources.

---

## The IAAA Model
The room is built around four pillars of information security:

| Pillar | Question it answers | Example |
|---|---|---|
| **Identification** | Who do you *claim* to be? | Typing a username or email |
| **Authentication** | Can you *prove* it? | Entering a password, scanning a fingerprint |
| **Authorisation** | What are you *allowed* to do? | Permission to read and send email |
| **Accountability** | What did you *actually* do? | Logs recording every action |

A useful cheat sheet I relied on during the exercises:
- **Identification** = the claim
- **Authentication** = the proof
- **Authorisation** = the decision about permissions
- **Access Control** = the enforcement of that decision
- **Accountability / Logging** = the record of what happened

---

## 1. Identification
Identification is how a user, process, or system **claims** a specific identity. No proof is involved yet.

Any identifier that is **unique** in its environment can be used:
- Username (e.g. `tanderson`, `neo`)
- Email address (many websites prefer it because it is already guaranteed to be unique)
- National ID, passport, or student ID number
- Phone number

Something that is **not unique** cannot identify a person. For example, a street number on its own can belong to many buildings and many residents.

Examples:
- Typing `yoshirou@tryhackme.thm` into the login field
- Telling IT support "My name is Julianna Adrasteia"

---

## 2. Authentication
Authentication **verifies** that the user really is who they claim to be.

### Authentication Factors
The three main factors:
1. **Something you know** – passwords, passphrases, PINs, unlock patterns
2. **Something you have** – a phone receiving an SMS code, a hardware security key (For example, Titan (Google's security key) is a physical hardware device used for two-step (2FA) or multi-step authentication.)
3. **Something you are** – biometrics: fingerprint, face, retina, voice

Two less common factors:
- **Somewhere you are** – logical or physical location
- **Something you do** – behaviour

### Notes on the Factors
- A drawn **pattern** is still "something you know". It is just a memorised PIN in visual form.
- A **passphrase** (e.g. "Eleven White Horses") is long and hard to crack, yet easy to remember.
- **Biometrics** are convenient, but a leaked fingerprint cannot be changed like a password, and readers can sometimes be fooled. That is why biometrics are usually paired with another factor.
- In "username + password", only the password is the authentication factor. The username is identification.
- In a voicemail system, calling your home number is not a factor (anyone can dial it). The secret PIN is the only proof.

### Multi-Factor Authentication (MFA)
MFA combines **two or more different factors**, so compromising one is not enough. **2FA** is the specific case of exactly two factors.

Classic examples:
- **ATM**: the card (have) + PIN (know)
- **Home safe**: a physical key (have) + PIN code (know)

---

## 3. Authorisation and Access Control
These two are closely related but **not the same**:

- **Authorisation** – *decides* the policy: what a user should be allowed to access and do
- **Access Control** – *enforces* that policy in practice

Examples:
- A policy granting management **read-only** access to accounting data → **Authorisation** (a decision)
- A hotel key card that opens only your own room → **Access Control** (enforcement)
- A mail server that lets you read your own mailbox but blocks you from colleagues' inboxes → **Access Control**

### Linux Example: passwd vs shadow
```
-rw-r--r-- 1 root root   2969 Mar 8 12:29 passwd
-rw-r----- 1 root shadow 1477 Mar 8 12:29 shadow
```
- `passwd` – readable by everyone
- `shadow` – readable only by root and the `shadow` group, because it stores the **password hashes**

The operating system checks these permissions every time someone tries to open a file. This restriction imposed by the OS is **Access Control**.

---

## 4. Accountability and Logging
Accountability holds users responsible for their actions **after** they have been authenticated and authorised.
It depends on **auditing**, and auditing depends on **logging**.

Example: having write access does not mean you should change things freely. You should only make changes the task requires, and logging is what enforces this, since every change is traced back to you.

### Logging
Logging records user actions, system events, and errors: **who** accessed **what**, and **when**.

It is essential for:
- Detecting suspicious activity (e.g. repeated failed logins)
- Incident response
- Forensic investigations
- Regulatory compliance

Examples from the exercises:
- `unix_chkpwd: password check failed for the user (yoshirou)` – a failed login attempt while the user was at lunch
- `gkr-pam: unlocked login keyring` on a day the user was on vacation – evidence of an intruder

### Protecting the Logs
- Logs should be **tamper-proof**, so an attacker cannot delete or alter them to hide their actions
- **Log forwarding** – sending logs to a separate, dedicated logging server or cloud service
- **SIEM** – aggregates logs from many sources, correlates events, detects threats, and alerts the security team

---

## Identity Management (IdM) vs IAM
- **IdM** – manages digital identities: user provisioning (creating and managing accounts), authentication, authorisation, and access rights in a central database
- **IAM** – a broader concept that includes IdM plus access control, identity governance, compliance management, and monitoring of user activity

IAM systems use technologies such as **RBAC**, **MFA**, and **SSO**, and manage the full identity lifecycle: onboarding, offboarding, and access revocation.\
In effect, IAM ensures that **only authorised people access sensitive data, and every access is recorded**.

### Regulatory Requirements
Regulatory requirements are laws and standards an organisation **must** follow when handling sensitive data. Failing to comply can lead to heavy fines, lawsuits, or losing the right to operate in a field.

---

## Attacks Against Authentication
The room showed why organisations should use **existing, peer-tested protocols** instead of inventing their own.

### The Problem with Naive Schemes
- **Shared secret phrase** – an eavesdropper hears it once and can reuse it
- **Cleartext password over the network** – anyone capturing traffic learns the credentials
- **Password encrypted with a fixed shared key** – the attacker cannot read the password, but the encrypted value is **always the same**

### Replay Attack
The attacker captures the encrypted response and simply **sends it again** later. They never need to decrypt it, because the server sees a valid response and accepts it.

### Defences
Make every response **unique**, so an old one becomes useless:
- **Timestamps** – include the current time, so a response is valid only briefly (requires synchronised clocks)
- **Nonce / challenge-response** – the server sends a random value each time, and the response must include it
- **Sequence numbers** – repeated values are rejected

---

## Access Control Models

### 1. Discretionary Access Control (DAC)
The **resource owner** decides who gets access and adds users individually.
- Example: sharing a graduation photo album with specific family members
- Simple and fully owner-controlled, but hard to scale to many users or changing roles

### 2. Role-Based Access Control (RBAC)
Access is granted based on the user's **role or group**.
- Example: accountants get access to the accounting books, but not to R&D documents
- Changing a user's role only requires moving them between groups, which makes maintenance easy and efficient

### 3. Mandatory Access Control (MAC)
The **system** enforces strict, security-first policies, and users have very limited abilities (they cannot install software or change file permissions).
- Used for highly classified data and special-purpose systems
- Linux implementations: **AppArmor** (Debian, Ubuntu) and **SELinux** (Red Hat, Fedora)

Why are AppArmor and SELinux needed?\
In standard Linux, permissions operate according to the DAC (Discretionary Access Control) model: every file has an owner,
and that owner decides who is permitted to read, write, or execute it (such as the `rw-r--r--` permissions we saw for the `passwd` file).
The issue is that any program runs with the full set of permissions belonging to the user who launched it. If an attacker compromises a web server running as `root`,
they gain access to virtually the entire system.\
MAC adds an additional layer: the operating system enforces strict rules regarding what each program is permitted to do—rules that cannot be bypassed even by `root` or the file owners.
Consequently, even if a program is compromised, the potential damage is limited strictly to the actions the program is authorized to perform.

**AppArmor**\
It operates based on file paths. Each program has a profile that specifies which files and directories it is permitted to read, write, or execute.\
Example: A web server's profile might dictate that it is only allowed to read from `/var/www/`. If it attempts to read `/etc/shadow`, access will be blocked—even if it is running as `root`.\
Advantage: Relatively simple to learn and configure.

**SELinux (Security-Enhanced Linux)**\
It operates based on labels. Every file, process, and port is assigned a label known as a "security context," and the policy dictates which labels are permitted to access others.\
Example: A process with the label `httpd_t` (a web server) is permitted to access only files with the label `httpd_sys_content_t`. Even if a file is moved to a different directory, its label remains attached to it.\
Advantage: More precise and robust. Disadvantage: More complex to configure and maintain.

---

## Single Sign-On (SSO)
With SSO, the user authenticates **once** with a central identity provider and gains access to all connected services (email, file shares, printers, etc.) without logging in to each one separately.

SSO solves the problem of many separate passwords: password fatigue, password reuse, and weak passwords.

### Advantages
- **One strong password** – much easier than remembering many
- **Easier MFA** – configured once instead of per service
- **Simpler support** – password resets are limited to one account
- **Efficiency** – no repeated logins

### Trade-off
SSO is a **single point of failure**. If the one SSO account is compromised, every connected service is exposed. That is why SSO is usually combined with **MFA**.

---

## Key Takeaways
- **Identification** is a claim, and **authentication** is the proof of that claim
- Authentication relies on **something you know, have, or are**, and MFA combines several of these
- **Authorisation** decides the policy, while **access control** enforces it
- **Accountability** depends on secure, tamper-proof **logging**, often centralised in a **SIEM**
- Home-made authentication protocols are vulnerable, for example to **replay attacks**, so tested protocols should be used
- **DAC, RBAC, and MAC** offer different balances between flexibility and security
- **SSO** improves usability and security, but should be protected with MFA
