# OWASP Top 10 2025: Insecure Data Handling – Summary

## Overview
In this room, I explored three entries from the **OWASP Top 10 (2025)** list that relate to **application behaviour and user input**:

- **A04: Cryptographic Failures**
- **A05: Injection**
- **A08: Software or Data Integrity Failures**
- **A10: Mishandling of Exceptional Conditions**

For each one I learned what the vulnerability is, how to prevent it, and then **practised exploiting it** on a dedicated web app to retrieve a flag. At the end I also added notes on **A10: Mishandling of Exceptional Conditions**, a brand-new 2025 category that has no room yet.

A recurring theme ties all of these together: **never trust input or data blindly** — whether it's a user's text, a serialized object, or the result of a cryptographic operation, it must be treated as untrusted and verified.

---

## A04: Cryptographic Failures

### What it is
Cryptographic failures happen when sensitive data isn't adequately protected — due to missing encryption, weak or outdated algorithms (MD5, SHA1, DES), exposed keys, or unprotected data in transit. A classic example is an app **"rolling its own crypto"** instead of using proven, vetted algorithms.

### How to prevent it
- Hash passwords with strong, slow functions like **bcrypt, scrypt, or Argon2**.
- Use trusted, industry-standard encryption libraries — never homemade algorithms.
- Never embed credentials or keys in source code, config files, or repositories; use a secure key-management system.

### Practical – Weak XOR Cipher
The app was a "note sharing" service protecting three notes with the **same weak XOR cipher** and a **4-character key** (`KEY_` shown, last character unknown).

**What I learned about XOR:**
- XOR compares two bits: **same → 0, different → 1**.
- XOR is **reversible**, which is the whole weakness:
  - `plaintext XOR key = ciphertext`
  - `ciphertext XOR key = plaintext`
  - `ciphertext XOR plaintext = key`  ← this is what lets you recover the key.
- The 4-char key **repeats** over the text (`K E Y ? K E Y ? ...`), so the missing character encrypts every 4th byte.
- **Base64 is not encryption** — it's just a way to represent raw bytes as safe text. Step one is always to Base64-decode, *then* XOR.

**How I broke it (two ways):**
1. **Known-plaintext:** Note #1 is a "Meeting Reminder", so it likely starts with `Meet`. Taking the 4th ciphertext byte `0x45` XOR `'t'` (`0x74`) = `0x31` = the character `1`. I cross-checked across positions 4, 8, 12, 16 — all gave the **same** character `1`, which proves the guess.
2. **Brute force:** Only 62 possibilities for the last character (a–z, A–Z, 0–9). A short Python loop tried each one and only `1` produced readable English.

**Result:** The full key was **`KEY1`**. It decrypted all three notes at once, and note #3 revealed the flag
**Lesson:** The encryption "worked", but a short repeating key plus predictable plaintext (`Meet`, `Password`) made it trivial to break. Use AES-256-GCM with proper key management instead.

---

## A05: Injection

### What it is
Injection occurs when an app takes user input and **mishandles** it — passing it directly into a system that executes commands or queries (a database, shell, templating engine, or API) instead of treating it as data. Classic forms include **SQL Injection, Command Injection, AI Prompt Injection, and Server-Side Template Injection (SSTI)**.

### How to prevent it
- Treat all user input as **untrusted**.
- For SQL: use **prepared statements / parameterised queries**, never string concatenation.
- For OS commands: use safe APIs that don't invoke the shell.
- Apply input validation and sanitisation: escape dangerous characters, enforce strict data types, filter before processing.

### Practical – Server-Side Template Injection (SSTI)
The app (an "SSTI Playground") rendered raw user input inside a **Jinja2** template using `render_template_string` — the root cause, because input became part of the template instead of staying a value.

**Key concepts I learned:**
- A **template** is a page skeleton with placeholders like `{{ name }}` that the server fills in. **Jinja2** is a popular template engine written in **Python**; `{{ }}` means "evaluate and print what's inside".
- **`{{7*7}}`** is the detection test: if the page returns **`49`** instead of the literal text, the server is executing my input → SSTI exists.
- **Fingerprinting the engine** with `{{7*'7'}}` (number × string):
  - **Jinja2 (Python):** repeats the string → **`7777777`**.
  - **Twig (PHP):** converts the string to a number → **`49`**.
  - The app returned `7777777`, confirming Jinja2 (and the page literally said so).

**The exploit payload:**
```
{{ cycler.__init__.__globals__.os.popen('cat flag.txt').read() }}
```
- `cycler` – a built-in Jinja2 object used as an entry point.
- `.__init__.__globals__` – reaches the module's global variables, where the `os` library lives.
- `.os.popen('cat flag.txt')` – runs the shell command that prints the flag file.
- `.read()` – captures the command output so the template displays it.

**Lesson:** The app concatenated user input into the template string. The fix is to pass input only as **data** — `render_template("page.html", name=user_input)` — the template-engine equivalent of prepared statements.

---

## A08: Software or Data Integrity Failures

### What it is
These failures occur when an app relies on code, updates, or data it **assumes** is safe, without verifying its authenticity, integrity, or origin — trusting unsigned updates, loading untrusted scripts/config, or accepting binaries, templates, or serialized data without confirming they weren't altered.

### How to prevent it
- Establish **trust boundaries** — never auto-trust external code or data.
- Use **cryptographic checks** (checksums, digital signatures) for updates.
- Ensure only trusted sources can modify critical artefacts.
- Extend integrity and trust boundaries into the build pipeline (**CI/CD**).

### Practical – Insecure Deserialization (Python pickle)
The app accepted **Base64-encoded pickle data** and deserialized it with **no integrity verification**.

**Key concepts I learned:**
- **Serialization** = "freezing" an object into bytes to store or send it; **deserialization** = "thawing" it back into a live object. **Pickle** is Python's library for this.
- The danger: during `pickle.loads()`, pickle can **execute code**, so a crafted pickle runs attacker code on the server.
- **`__reduce__`** is a special method pickle calls during deserialization. It returns a tuple `(function, args)`, and pickle promises to run `function(*args)` on unpickling — the hook the attack abuses.
- A **module** is a file of ready-made code you import (e.g. `subprocess`, which runs external commands). A **function** inside it (`check_output`) runs a command **and returns its output** — exactly what's needed to get the flag back to the screen.
- **Why Base64 and not plain code:** the app doesn't run Python source — it runs `pickle.loads` on bytes. The exploit must be smuggled *inside* a pickle object, and Base64 just makes the raw pickle bytes pasteable (it's encoding, not encryption).

**The payload I generated:**
```python
import pickle, base64, subprocess

class ReadFlag:
    def __reduce__(self):
        return (subprocess.check_output, (['cat', 'flag.txt'],))

print(base64.b64encode(pickle.dumps(ReadFlag())).decode())
```
Inspecting the bytes (via `pickletools.dis`) showed the words `subprocess`, `check_output`, `cat`, `flag.txt` sitting in plain text, ending with the `REDUCE` opcode — the instruction that calls `subprocess.check_output(['cat','flag.txt'])` on unpickling.

**Result:** Pasting the Base64 string and clicking **Deserialize** ran the command and displayed the flag.

**Lesson:** The app trusted external serialized data without any signature or hash check.\
**The fix:** never deserialize untrusted data; use safe formats (JSON), verify signatures, whitelist allowed types, or use a restricted/sandboxed unpickler.

---

## A10: Mishandling of Exceptional Conditions

### What it is
A **brand-new 2025 category** (no TryHackMe room yet). It means an application **fails to prevent, detect, or respond properly to unusual or unexpected situations** — bad input, a service timing out, a null value, a crash. It reframes what used to be seen as "code quality" bugs as genuine **security risks**, because when software doesn't know what to do on failure, it often lands in an **insecure state**.

### The three failure stages
- **Prevention:** input isn't validated, so an abnormal state occurs (e.g. an out-of-range value causing an overflow).
- **Detection:** the code doesn't notice something broke (ignoring a return code, swallowing a generic exception without logging).
- **Response:** the error is detected but handled badly (raw stack trace shown, no cleanup, defaulting to an insecure state).

### The core weakness: Fail-Open vs Fail-Closed
- **Fail-Open:** when a check fails, the system defaults to **allowing** access/continuing — dangerous.
- **Fail-Closed:** when something fails, the system **denies** access or safely halts — the "deny by default" principle.

*Analogy:* a guard whose computer crashes. Fail-open = "let everyone in." Fail-closed = "no one gets in until it's fixed." Attackers deliberately **trigger the failure** to see if the system falls open.

### How attackers exploit it
- **Authorization bypass** by forcing a security check to fail open.
- **Login during an outage** — assuming a user is valid when auth is unreachable.
- **Data leaks via verbose errors** (DB errors exposing table names → precise SQL injection).
- **Denial of service** via forced crashes (null pointer exceptions) or resource exhaustion.
- **Logic bugs from silent errors** — skipped permission or audit checks.
- **Double-spending / fund theft** when a multi-step transaction only partly completes.

### How to prevent it
- Adopt a consistent error-handling policy (user-facing vs internal logs).
- Catch and handle exceptions where they occur; clean up on failure.
- **Fail closed** — deny by default on timeouts, unavailable services, or unexpected values.
- Never expose sensitive info in errors; log privately, scrub secrets.
- Use global exception handling, and **roll back** partial transactions.
- Rate-limit error-prone operations; validate input; monitor and alert.
- **Test failure paths** with fault injection / chaos testing in staging.

*Terms:* **staging** = a safe near-copy of production with no real users, for risky tests. **Fault injection** = deliberately inserting one specific fault to see how the system reacts. **Chaos testing** = broadly crashing random components (e.g. Netflix's "Chaos Monkey") to prove overall resilience.

---

## Key Takeaways
- **Insecure data handling** across crypto, injection, and deserialization all share one root cause: **trusting input or data without verification**.
- **Cryptographic failures:** short/repeating keys and homemade crypto are easily broken — XOR is fully reversible, and Base64 is encoding, not security.
- **Injection:** user input must stay **data**, never become executable code or a template; SSTI can escalate to full remote code execution.
- **Software/Data Integrity failures:** deserializing untrusted data (like Python pickle) can execute arbitrary code via `__reduce__`.
- **A10:** security depends not only on normal behaviour but on **how the system behaves when things break** — always **fail closed**.
- The defensive mindset throughout: **establish trust boundaries, validate everything, and prefer proven standards over homegrown solutions.**
  
