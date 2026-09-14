# Humans as Attack Vectors – Summary

## Overview
In this room, I explored how attackers target **humans**, the weakest element in cyber security, and how a **SOC (Security Operations Center)** analyst can help detect and prevent these attacks.

Defending against threats involves two key tasks: **Mitigation** (preventing or reducing the chance and impact of attacks) and **Detection** (catching the advanced attacks that slip through mitigation). Both are essential, and a good SOC analyst needs to understand both sides.

---

## The Human Element
A computer network can be compared to a fortress with strong walls and gates. Instead of breaching those walls, an attacker can simply trick a human "gatekeeper" into opening the door for them. Humans are often the weakest link in cyber security, and the ones who help threat actors the most, whether knowingly or not.

### Why Humans Are Targeted
Humans are targeted because of the **access** they can provide, to websites, mailboxes, or databases. Some examples of what attackers hope to achieve:
- Breaching an HR manager's Google account → stealing and selling the employee database
- Tricking a wealthy person into running malware → hijacking their web banking session
- Breaching an IT administrator's VPN account → accessing the heart of the corporate network
- Tricking a government worker into sharing secrets → simplifying future attacks

---

## Attacks on Humans
Attacks targeting humans rely on **social engineering**: manipulating the victim into helping the attacker by exploiting human psychology rather than technical flaws. For the tactic to succeed, it needs to be:
- **Trustworthy** – the attacker must appear legitimate so the victim trusts them
- **Emotional** – the attack must trigger feelings like urgency, fear, or curiosity

### Phishing Attacks
Phishing is the most common form of social engineering, with an estimated 3.4 billion malicious emails sent daily. A typical phishing email pretends to be a trusted service (like a password manager or a tax authority), creates urgency, and leads the victim to a fake login page or a malicious attachment.

### Malware Downloads
Attackers also trick users into downloading and installing malware themselves, for example through fake software update pages, fake CAPTCHA verification steps that actually run malicious commands, and techniques like SEO poisoning or malicious QR codes to increase reach.

### Deepfakes and Impersonation
AI-generated video and audio are increasingly used to impersonate family members, colleagues, or corporate partners. In one real case, a finance worker was tricked into wiring $25 million after a deepfake video call impersonating their boss. Even without deepfakes, attackers succeed by simply impersonating IT support or executives over the phone to gain account access.

Other social engineering methods include USB drop campaigns, physical attacks, insider threats, and fake job offers.

---

## Defending Humans: Mitigation Measures
As a SOC analyst, my main task is to **detect and investigate** attacks. But knowing key **mitigation** measures helps prevent common threats before they ever reach the SOC:

| Mitigation | Description |
|---|---|
| Anti-phishing solution | Blocks phishing emails before users even see them |
| Antivirus / EDR solution | Prevents humans from running malware on corporate hosts |
| "Trust but verify" principle | Teaches employees how to detect deepfakes and verify suspicious requests from "CEO" or "IT" |
| Security awareness training | Teaches employees to detect phishing, reinforced through phishing simulations |

The general flow: mitigation layers (anti-phishing tools, employee training) filter out most attacks, and the SOC team handles whatever slips through.

---

## Practice: SOC Analyst Simulation
I practiced these concepts using TryHackMe's Security Dashboard, acting as a SOC analyst who reviews chat messages, SIEM alerts, and updates the company's security policy.

### Case Reviews
1. **Suspicious software request** – A new employee's antivirus blocked a "Setup.exe" downloaded from an unofficial freeware site. Verdict: quarantine the file and direct the employee to the official installer, rather than adding an exclusion.
2. **Suspicious email attachment** – An email impersonating Stripe asked a Finance Director to open a password-protected archive. Verdict: block the email and investigate as phishing, since the domain and request pattern were suspicious.
3. **Unusual password reset request** – IT support received a call claiming to be the CEO, made at an odd hour from a hidden number, asking for a Gmail password reset. Verdict: disable the account until the login is confirmed directly with the CEO, since he could not be reached to verify.
4. **Anomalous login location** – An HR assistant's Microsoft 365 account logged in from a different city than usual, right after visiting a suspicious lookalike Microsoft login domain. Verdict: disable the account until the activity can be confirmed, since the browsing history strongly suggested credential phishing.

### Rebuilding the Security Policy
I selected four policies to strengthen the organization's defenses:
- **Security Awareness Program** – quarterly training for employees on detecting and reporting phishing
- **Anti-Phishing Solution** – a tool to detect and automatically block most phishing emails
- **Antivirus Solution** – protection against malicious attachments and malware downloads
- **Access Management Policy** – documented verification steps for requests like password resets, making it harder for impersonation and deepfakes to succeed

---

## Key Takeaways
- Humans are often the weakest link in cyber security because of the access they hold
- Social engineering succeeds by appearing trustworthy and triggering emotional reactions
- Phishing, malware downloads, and deepfake/impersonation attacks are common ways attackers target humans
- Defending humans requires both **mitigation** (tools and training that stop attacks early) and **detection** (SOC investigation of what slips through)
- Combining anti-phishing tools, antivirus/EDR, security awareness training, and clear verification policies significantly strengthens an organization's human layer of defense
