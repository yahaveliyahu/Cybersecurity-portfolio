# OWASP Top 10 2025: IAAA Failures – Summary

## Overview
In this room, I explored three of the **OWASP Top 10:2025** categories, all tied together by failures in **IAAA** — how an application handles **Identity, Authentication, Authorisation, and Accountability** — four interdependent layers:
who you are → proving your identity → what you are permitted to do → and documenting who did what.

**What is the OWASP Top 10?**\
**OWASP** (Open Worldwide Application Security Project) is a non-profit organization focused on application security.
Its "Top 10"—a list updated every few years—compiles the ten most critical and prevalent security risks found in web applications. Rather than a list of specific "bugs," it consists of categories of vulnerabilities.
It serves as an industry-standard reference for developers, penetration testers, and security professionals, helping them determine where to focus their efforts first.

The categories covered were:
- **A01: Broken Access Control**
- **A07: Authentication Failures**
- **A09: Logging & Alerting Failures**

Each hands-on challenge ran against a mock banking app (`bank.thm`).

---

## What is IAAA?
IAAA is a simple model for how users and their actions are verified. The four items are layered — you can't skip a level, and if an earlier one isn't enforced, the later ones can't be trusted either.

- **Identity** – the unique account that represents a person or service (e.g., user ID / email)
- **Authentication** – proving that identity (passwords, OTP, passkeys)
- **Authorisation** – what that identity is allowed to do
- **Accountability** – recording and alerting on *who did what, when, and from where*

Weaknesses in any of these let attackers access other users' data or gain privileges they shouldn't have.

---

## A01: Broken Access Control
### What it is:
Access control (authorization) determines what each user is permitted to do and see. "Broken" means the server fails to properly enforce these permissions for every request.

### The problem:
The server places too much trust in the client—it assumes that if you have sent a request for a specific resource, you are authorized to access it, without independently verifying that the resource actually belongs to you.

### The vulnerability being exploited:
**IDOR** (Insecure Direct Object Reference)
The classic case: if changing an ID in the URL (`?id=7 → ?id=6`) lets you see or edit someone else's data.

In the challenge, the account page used a direct ID in the URL:
```
https://bank.thm/accounts?id=5
```
Simply incrementing the `id` value (1–10) returned **other users' account details** — name, account number, and balance — with no check that the logged-in user actually owns that account.
For example, `id=5` returned David Wilson's account. Cycling through his IDs reveals that this is not the account in question because his account only has $23,450.00 and not a $1,000,000.

### Horizontal vs. Vertical Privilege Escalation
I learned to distinguish the two outcomes of broken access control:
- **Horizontal privilege escalation** – staying at the *same role* (a normal user) but reaching *another user's* data.
- **Vertical privilege escalation** – jumping *up* to a higher role, e.g., performing admin-only actions.

Since the IDOR here only let me view other normal users' accounts (same privilege level), this is **horizontal privilege escalation**.

### How to protect/prevent:
- Enforce authentication **and** authorisation checks server-side, on every request
- Verify the authenticated user actually has permission for the requested resource
- Use indirect references or encrypted tokens instead of predictable, sequential IDs
- Log and monitor access attempts to sensitive resources

---

## A07: Authentication Failures
### What it is:
Authentication is the stage where a user proves their identity (e.g., via password, OTP, or passkey). A failure here means the application is unable to reliably verify the identity or correctly link it to the appropriate account.

### The problem:
Logical flaws in the registration or login process, or weak management of identities and sessions, can allow an attacker to log in as another user. Common issues include username enumeration, weak passwords without account lockout or rate-limiting, and insecure handling of sessions or cookies.

### The exploited vulnerability:
**A logical flaw involving case-sensitivity:**
We registered using the username `aDmiN` (mixed case). The registration process checked for uniqueness in a case-sensitive manner—treating it as a new,
distinct username—whereas the login process searched for the account in a case-insensitive manner.
The result: authentication succeeded using a password we created, yet the system loaded the dashboard belonging to the *actual* `admin` account.
This occurred because the password verification and the account lookup were two separate operations with differing behaviors, causing the session to be linked to the wrong account.

**Key insight I learned:** I never had to discover the admin's password. By registering a look-alike username, I created my *own* credentials that the app then wrongly bound to the admin identity — a "side door" in.

### How to fix it:
- **Normalise** usernames to one canonical form (e.g., lowercase) **before both** the registration uniqueness check and the login lookup, so `admin`, `aDmiN`, and `ADMIN` collapse to one identity
- Enforce a **unique index** on that canonical form
- Rate-limit / lock out repeated login attempts (to defend against brute-force attacks).
- Rotate the session upon any change to the password or permissions.

---

## A09: Logging & Alerting Failures
### What it is:
when an app doesn't record or alert on security-relevant events, defenders can't detect or investigate attacks. Good logging underpins **accountability**.\
**Accountability** — the ability to prove who did what, when, and from where. This relies on logging and alerting regarding security-relevant events.

### The problem:
When an application fails to log or alert on significant events, defenders cannot detect or investigate an attack. Common failures include: a lack of logging for authentication events, vague logs, no alerts for brute-force attempts or permission changes, insufficient log retention periods, or logs stored in a location accessible to an attacker for modification.

### The exploited vulnerability:
**The investigation (SIEM dashboard)**\
The SIEM dashboard for `bank.thm` summarised the activity:
- **Total Logs:** 40
- **Suspicious Activity:** 7
- **Breached:** 2

Drilling into the logs revealed a clear attack timeline, all from the same IP — **`203.0.113.45`** (a documentation/test range):

| Time | Method | URL | Response | Meaning |
|------|--------|-----|----------|---------|
| 08:42:09 → 08:42:25 | POST | `/login` | **401 Unauthorized** (×5) | Repeated failed logins — **brute force** |
| 08:42:29 | POST | `/login?user=admin&pass=Password123` | **200 OK** | **Successful login** — credentials cracked (and exposed in plaintext in the URL) |
| 08:42:31 | GET | `/supersecretadminstuff` | **200 OK** | **Accessed the admin area** successfully |

**What action did the attacker try to do, and on which endpoint?**
The attacker tried to **access a restricted admin area**, reaching the endpoint **`/supersecretadminstuff`** with a **GET** request — and the **200 OK** means it *succeeded* instead of being blocked with a `403`/`401`.

**What an endpoint is:** a specific address on the server you can send a request to. If the domain (`bank.thm`) is the building, each endpoint (`/login`, `/account`, `/supersecretadminstuff`) is a specific *door* leading somewhere different. In a log it's the **Request URL** — the part after the domain, starting with `/`.
**What `/supersecretadminstuff` means:** an admin-only "door." Its very name shows the problem — hiding a sensitive area behind a hard-to-guess name is **security through obscurity**, not real security. Once the name is known or guessed, nothing stops access. Real protection must be a **server-side authorisation check on every request**.
**Why each log field matters:** the endpoint tells us *what* was targeted, the IP *where from*, the username *who*, the timestamp *when*, and the response code whether it was an *attempt or a breach*. Remove the Request URL and you'd only know "a successful GET happened" — never that the admin area was exposed. That gap is exactly the "insufficient logging" failure.

### How to fix it:
- Log the full authentication lifecycle: failures, successes, password/2FA/role changes, and admin actions
- Centralise logs **off-host** with proper retention, so an attacker can't tamper with them
- Alert on anomalies — brute-force bursts, privilege elevation, access to sensitive endpoints

---

## Key Takeaways
- **A01 Broken Access Control** – Enforce server-side authorisation checks on *every* request; never trust a client-supplied ID.
- **A07 Authentication Failures** – Canonicalise identity ((A technical process in which information with several different representations is converted into a single, standard, and uniform form) before uniqueness and lookup checks; rate-limit/lock out brute force; rotate sessions on credential/privilege changes.
- **A09 Logging & Alerting Failures** – Log the full auth lifecycle, centralise logs off-host with retention, and alert on anomalies.
- IAAA is **layered** — a failure at one level (identity, auth, authz, accountability) undermines everything built on top of it.
