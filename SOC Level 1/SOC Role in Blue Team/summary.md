# SOC Role in Blue Team – Summary

## Overview
In this room, I explored where a **SOC L1 analyst** role fits within a company's overall security structure.  
While the *Junior Security Analyst Intro* room introduced the SOC L1 role itself, this room focused on the bigger picture: how the Blue Team is organised, who oversees it, what other security departments exist, and what a realistic career path looks like starting from SOC L1.

---

## Security Hierarchy
Every company has different cyber security priorities — law firms care about the privacy of legal documents, factories about the availability of production lines, hospitals about patient safety. This means every organisation builds its own security team structure, but a common high-level hierarchy looks like this:

- **Executives** (CEO / CFO / Company owner) – focus on global business objectives
- **Security Leadership** (CISO, or CTO/CIO if no CISO exists) – lead the company-wide IT or security program
- **Security Managers** (SOC Manager, Red Team Lead) – manage a single team or department like the SOC
- **Technical roles** (Analyst, Engineer, Red Teamer) – perform hands-on technical tasks like log analysis

Executives usually don't manage technical security themselves, which is why they hire a CISO (or similar role) who understands business needs and builds the right security departments underneath them.

### Security Departments
In small companies, the IT department often handles security directly, or a generic "Information Security" team does everything. Larger companies split this into dedicated departments overseen by a CISO:

- **Red Team** – offensive security experts, pentesters, and ethical hackers who look for security issues
- **GRC Team** – specialists managing policies and ensuring compliance with regulations like PCI DSS
- **Blue Team** – defensive security experts such as SOC analysts, engineers, and incident responders

---

## Meet the Blue Team
The Blue Team is responsible for defensive security — constantly monitoring for attacks and responding to them quickly. Depending on the company's size and sector, it can range from 3 to 50 members across several departments.

### Security Operations Center (SOC)
The SOC is the first line of defense and usually operates 24/7. Its responsibilities include:
- Creating detection rules
- Investigating security alerts
- Collecting and monitoring logs
- Cooperating with the IT team
- Writing summary reports

A typical SOC is composed of:
- **L1 Analysts** – junior members who triage alerts and escalate complex cases to L2
- **L2 Analysts** – experienced members who investigate more advanced attacks
- **Engineers** – experts in configuring security tools like EDR or SIEM
- **Manager** – oversees the whole SOC team

### Cyber Incident Response Team (CIRT)
When SOC expertise isn't enough or an incident escalates, the "cyber firefighters" — CIRT, also known as CSIRT or CERT — get called in. They:
- Are usually called on-demand
- Support the SOC with deeper analysis
- Handle critical incidents
- Identify hidden threats
- Perform deep forensics
- Recover breached systems

Real-world CIRT examples include **JPCERT** (Japan's national CERT), **Mandiant** (private global incident response), and **AWS CIRT** (investigates security incidents for AWS customers).

### Specialized Defensive Roles
Larger companies, tech-focused startups, and government agencies often need narrower, more specialized Blue Team roles that require deep topic knowledge:
- **Digital Forensics Analyst** – uncovers hidden threats in disk and memory
- **Threat Intelligence Analyst** – gathers data about emerging threat groups
- **AppSec Engineer** – maintains a secure software development lifecycle
- **AI Researcher** – studies AI threats and how to defend against them

Other roles supporting the Blue Team shown in the room include DevSecOps, Penetration Tester, GRC Auditor, and Threat Analyst.

---

### Internal SOC vs MSSP
Not every organisation runs its own SOC — many rely on a **Managed Security Services Provider (MSSP)** instead. Both are valid entry points into the field, but they differ significantly:

| Topic | Internal SOC | MSSP |
|---|---|---|
| Scenario Example | Work in a bank's SOC team, protecting the bank's own systems | Work for a global MSSP protecting sixty customers across Europe |
| Working Pace | Usually calm shifts without heavy time pressure | Shifts often start from a queue of urgent alerts to analyze |
| Security Tools | Work with a few tools, but need to know them very well | Must work across roughly sixty diverse security tools and platforms |
| Incident Practice | May only see one or two major cyber attacks per year | Deal with attacks and breaches weekly, learning constantly |

---

## Final Challenge
The final task put me in the shoes of a CISO at "TrySecureMe," a multinational company facing seven simultaneous incidents. The challenge was to drag and drop the correct security role — such as SOC L1 Analyst, Penetration Tester, CERT Lead, or GRC Auditor — onto each scenario, from triaging a SIEM firewall brute-force alert to responding to ransomware, auditing PCI DSS compliance, and analyzing the tactics of an active threat group (FIN7). Successfully matching every role to its incident completed the room and captured the flag.

---

## Key Takeaways
- The Blue Team sits within a broader security hierarchy alongside the Red Team and GRC Team, all overseen by a CISO
- The SOC is the Blue Team's first line of defense, structured around L1/L2 analysts, engineers, and a manager
- CIRT/CSIRT/CERT teams step in for critical incidents the SOC can't fully handle alone
- Specialized roles (forensics, threat intel, AppSec, AI research) branch off from core SOC experience
- Choosing between an internal SOC and an MSSP shapes the pace and variety of day-to-day work
