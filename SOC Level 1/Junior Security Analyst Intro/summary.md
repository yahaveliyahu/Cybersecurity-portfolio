# Security Analyst Journey – Summary

## Overview
In this room, I explored what it's like to work as a **Junior Security Analyst (SOC Level 1 Analyst)**, following a simulated day in a Security Operations Center (SOC).

The room walked through the daily responsibilities of the role, introduced the team members who make up a SOC, and finished with a hands-on simulation of investigating and responding to a real alert — from detection all the way to blocking it on the firewall.

---

## Daily Duties of a Junior Security Analyst
As a SOC Level 1 Analyst working in a 24/7 SOC team, the typical daily responsibilities include:
- Monitoring and investigating various security alerts
- Participating in SOC brainstorms and workshops
- Cooperating with other teams to keep the company safe
- Constantly learning about new attacks and defenses

Looking ahead, common challenges a Junior Analyst may face include:
- Detecting and preventing a data-stealer infection on a coworker's laptop
- Analyzing and stopping a phishing campaign targeting the finance team
- Participating in larger incidents, such as a full-scale ransomware attack
- Teaming up with teammates to build detection rules and automations
- Understanding how the company operates beyond just the cyber side

---

## The SOC Team
A SOC is made up of multiple roles working together, each supporting the analyst in a different way:
- **Senior Analyst** – helps junior analysts when something is unclear and handles complex cases after the initial analysis
- **SOC Engineer** – maintains security tools and configures alerts, but doesn't work shifts or analyze alerts directly
- **SOC Manager** – reports SOC results to top management and keeps the team on track
- **Incident Responder** – called in on demand, mainly during major incidents

---

## Hands-On Simulation: A Day in the Life
The room included a simulated SIEM dashboard with live alerts, walking through the full workflow of handling a security event:

### 1. SIEM Dashboard
Reviewed a list of alerts sorted by severity. Two **Critical** alerts stood out:
- A successful SSH login from a suspicious IP address
- Multiple unauthorized login attempts to port 22 from the same IP

### 2. IP Hunter
Investigated the suspicious IP using a reputation/location lookup tool, similar to real-world tools like AbuseIPDB or Cisco Talos Intelligence. The IP was confirmed **malicious**, linked to 4 prior cyberattacks, and tagged with categories including Port Scan, C2 Server, and PlugX malware.

### 3. Escalation
Since a *successful* (not just failed) authentication attempt occurred, this was treated as a real incident and escalated to the **SOC Team Lead** — the correct person in charge of the team — rather than to unrelated roles like a Security Architect, Python Developer, or Sales Executive.

### 4. Firewall
With the SOC Team Lead analyzing the alert further, I got permission to add the malicious IP to the **Firewall Block List** along with a comment describing the reason, stopping further access from that address.

---

## Key Takeaways
- A Junior Security Analyst's core job is monitoring alerts, investigating suspicious activity, and escalating real incidents
- A SOC is a team effort — analysts, engineers, managers, and incident responders each play a distinct role
- Investigating an alert typically follows a clear workflow: detect → verify (IP reputation) → escalate → contain (firewall block)
- Not every alert carries equal weight — severity (e.g., a successful malicious login vs. a failed one) determines urgency and escalation path
- Escalating to the right person matters just as much as escalating at all
