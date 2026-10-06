# Active Directory Basics – Summary

## Overview
In this room, I explored the fundamentals of **Active Directory (AD)**, the backbone of most corporate Windows environments.
The focus was on understanding what AD is, the objects and structure it manages, how administrators organise and control users and machines, how policies are deployed, and how authentication works inside a Windows domain.

A **Windows domain** is a group of users and computers managed centrally by a business. Instead of configuring each computer individually, administration is centralised in a single repository called **Active Directory**, running on a server called the **Domain Controller (DC)**.

The two main advantages of a domain are **centralised identity management** (all users configured from one place) and **centralised security policy management** (policies applied across the network from one place).

---

## Active Directory Objects

AD acts as a catalogue holding all the "objects" on the network. The most important ones:

### Users
One of the most common object types. Users are **security principals** — they can be authenticated by the domain and assigned privileges over resources. They represent two kinds of entities:
- **People** – employees who need network access.
- **Services** – accounts used to run services like **IIS** (Microsoft's web server, similar to Apache/Nginx) or **MSSQL** (Microsoft's relational database, similar to PostgreSQL/MySQL). Service accounts get only the minimum privileges needed to run their service.

### Machines
Every computer that joins the domain gets a **machine account**, also a security principal. The account is a **local administrator** on its own computer and is not meant to be used by anyone except the machine itself.
- Machine account passwords are automatically rotated and are ~120 random characters.
- Naming scheme: the computer's name followed by a `$`. For example, `TOM-PC` → `TOM-PC$`, and `DC01` → `DC01$`.

### Security Groups
Used to grant permissions over resources to many users/machines at once instead of one by one. Groups are also security principals. They can contain users, machines, and even other groups.
Important default groups: **Domain Admins** (full control over the whole domain), **Server Operators** (administer DCs), **Backup Operators** (read any file, ignoring permissions), **Account Operators** (create/modify accounts), **Domain Users**, **Domain Computers**, **Domain Controllers**.

---

## Organizational Units (OUs)

An **OU** is a container used to organise objects (users, computers, groups), much like folders organise files.

- The main purpose of OUs is **applying policy** — a rule set on an OU is inherited by everything inside it. This is why OUs usually mirror the company's structure (one OU per department).
- A user can only belong to a **single OU** at a time.
- OUs can be nested (an OU inside an OU).

### OUs vs Security Groups (key distinction)
These are often confused but serve completely different purposes:
- **OUs** → organise objects and **apply policies**. A user is in only one OU.
- **Security Groups** → **grant permissions** over resources (shared folders, printers, etc.). A user can be in many groups.

So "group users so policies can be applied consistently" → the answer is an **OU**, not a group.

### Default Containers
Besides the department OUs, Windows creates several containers automatically:
- **Builtin** – default groups available to any Windows host.
- **Computers** – where any newly joined machine is placed by default.
- **Domain Controllers** – default OU holding the DCs.
- **Users** – default domain-wide users and groups.
- **Managed Service Accounts** – accounts used by services.

---

## Managing Users and OUs (hands-on)

Using **Active Directory Users and Computers (ADUC)** on the DC, I worked through cleaning up the domain to match an organisational chart.

### Creating an OU
Right-click the parent (e.g. the `THM` OU, or the `thm.local` domain root) → **New → Organizational Unit** → name it → **OK**.
I created a `Students` OU under `THM`, and later created `Workstations` and `Servers` OUs directly under `thm.local`.

### Deleting a protected OU
The department that didn't appear in the chart (**Research and Development**) had to be removed.
- By default, OUs are **protected from accidental deletion**, so the first delete attempt fails with an "insufficient privileges / protected object" error.
- Fix: enable **View → Advanced Features**, then open the OU's **Properties → Object** tab and uncheck **"Protect object from accidental deletion"**.
- Deleting the OU also deletes **everything inside it** (confirmed by the "Confirm Subtree Deletion" warning). The **"Use Delete Subtree server control"** checkbox can be left unchecked — a normal Yes is enough once protection is removed.

### Matching users to the chart
After deleting the extra OU, some departments' users didn't match the chart. The rule:
- In chart but missing in AD → **create** the user.
- In AD but not in chart → **delete** the user.
- In both → leave as is.
The chart is the source of truth.

---

## Delegation

**Delegation** grants a specific user limited control over an OU without making them a Domain Admin.
A classic use case: letting IT support **reset passwords** for low-privilege users.

Steps (delegating the **Sales** OU to **Phillip**):
1. Right-click the OU → **Delegate Control** → the wizard opens.
2. Type `Phillip` → click **Check Names**. Windows validates and autocompletes the user, showing the full identity with its **UPN** (`phillip@thm.local`). The UPN is the login identifier in email-like form (`user@domain`) and is the proof the user was resolved correctly.
3. Select the common task **"Reset user passwords and force password change at next logon"**.
4. Finish the wizard.

### Resetting a password as Phillip (PowerShell)
Phillip does **not** have rights to open ADUC at all, so the GUI path is blocked for him. The delegated permission itself exists either way — PowerShell just reaches the reset action directly without opening the locked tool.

```powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```

**Command breakdown:**
- `Set-ADAccountPassword` – changes an account's password.
  - `sophie` – the target account (`-Identity`).
  - `-Reset` – set a new password without knowing the old one (the admin/delegated path).
  - `-NewPassword (...)` – requires a **SecureString**; `Read-Host -AsSecureString` prompts for the password at runtime so it's never typed in plain text or saved in history.
  - `-Verbose` – prints a confirmation line showing the operation and target (`CN=Sophie,OU=Sales,...`). It does **not** change what the command does — only how much output is shown.
- `Set-ADUser -ChangePasswordAtLogon $true` – marks the password as temporary so the user must choose a new one at next logon. This is good security hygiene: once IT knows the temporary password, forcing a change returns the secret to the user alone. For just retrieving the flag, only the first command is needed.

**Password complexity note:** the first reset attempt failed with *"The password does not meet the length, complexity, or history requirement of the domain"* — a 6-character password was too weak. Domains require strong passwords (length + mix of upper/lower/digit/special). Re-running with a stronger password succeeded.

---

## Lesson Learned: "Change Password at Logon" + RDP

Enabling `-ChangePasswordAtLogon $true` and then trying to log in as Sophie **via RDP** caused:
> You must change your password before logging on the first time.

The login was blocked **before** any session opened, with no screen to actually change the password.

**Root cause:** this is a known limitation of **NLA (Network Level Authentication)** and the underlying **CredSSP**, which authenticate the user *before* a session starts and **do not support password changes**. Disabling NLA only on the client (`enablecredsspsupport:i:0` in the `.rdp` file) didn't help because the server **requires** NLA — it rejected the connection outright. The documented fixes are all **server-side** (disable NLA on the host, or switch the Security Layer to "RDP Security Layer"), which a normal/delegated user can't do.

**Practical resolution** (what IT actually does when the change gets stuck): go back to Phillip and clear the flag, then reset to a known password:
```powershell
Set-ADUser -ChangePasswordAtLogon $false -Identity sophie -Verbose
```
This was an important real-world takeaway: forcing a first-logon password change over RDP with NLA enforced does not work the way it does when sitting physically at the machine.

---

## Managing Computers in AD

By default, all joined machines land in the **Computers** container. Best practice is to segregate them by role so different policies can apply:
1. **Workstations** – everyday user machines; privileged users should never sign into them.
2. **Servers** – provide services to users or other servers.
3. **Domain Controllers** – the most sensitive devices; they hold the hashed passwords for every account in the environment.

Hands-on: I created `Workstations` and `Servers` OUs under `thm.local`, then **moved** machines out of `Computers` (right-click → **Move**, or drag-and-drop) — personal PCs/laptops (`PC-*`, `LPT-*`) to **Workstations**, and servers (`SRV-DB01`, `SRV-DB02`, `SVR-WEB01`) to **Servers**.

---

## Group Policy Objects (GPOs)

A **GPO** is a collection of settings applied to OUs. GPOs contain **Computer Configuration** and **User Configuration** sections, so a setting can target machines or identities.

Workflow (in the **Group Policy Management** tool, which is separate from ADUC):
1. Create a GPO under **Group Policy Objects**.
2. **Link** it to the OU(s) where it should apply (drag the GPO onto the OU).
- A GPO applies to the linked OU **and all sub-OUs** (inheritance). E.g. a GPO on the root domain also affects Sales.
- **Scope** tab shows where a GPO is linked; **Security Filtering** (default: Authenticated Users) can narrow it to specific users/computers; **Settings** tab shows the actual configuration; the **Explain** tab on each policy documents what it does.

### GPO Distribution — SYSVOL
GPOs are distributed through a network share called **SYSVOL**, stored on the DC (`C:\Windows\SYSVOL\sysvol\`). All domain machines sync from it periodically.
Changes can take up to ~2 hours to propagate; to force an immediate update on a machine:
```powershell
gpupdate /force
```

### GPOs I configured

**1. Restrict Access to Control Panel**
- Location: **User Configuration → Policies → Administrative Templates → Control Panel**.
- Enabled **"Prohibit access to Control Panel and PC settings"**.
- Linked to **Marketing**, **Management** and **Sales** OUs (not IT, so IT users keep access).

**2. Auto Lock Screen**
- Location: **Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options**.
- Set **"Interactive logon: Machine inactivity limit"** to **300** (value is in **seconds** = 5 minutes).
- Linked to the **root domain** `thm.local`, so all computers inherit it. (OUs containing only users ignore the Computer Configuration part.)

**Verification:** logging in via RDP as `THM\Mark` (`M4rk3t1ng.21`), the Control Panel was blocked by the administrator, confirming the GPO applied.

---

## Authentication in a Windows Domain

All domain credentials are stored on the DC. When a user authenticates to a service, the service checks with the DC. Two protocols exist:
- **Kerberos** – the **default** in any recent Windows domain.
- **NetNTLM** – legacy, kept for compatibility (so: *is NetNTLM the preferred default?* → **no**).

### Kerberos (ticket-based)
1. The user sends their username + a timestamp encrypted with a key derived from their password to the **KDC** (Key Distribution Center, on the DC).
2. The KDC returns a **TGT** (Ticket Granting Ticket) + a **Session Key**. The TGT is encrypted with the **krbtgt** account's hash, so the user can't read it.
3. To reach a service, the user sends the TGT + an **SPN** (Service Principal Name, identifying the target service) to request a **TGS** (Ticket Granting Service) ticket.
4. The KDC returns a TGS + Service Session Key. The TGS is encrypted with the **Service Owner's** hash.
5. The user presents the TGS to the service, which decrypts it with its own account hash and validates access.

**SPN (Service Principal Name):** a unique identifier mapping a service instance to the account that runs it, e.g. `MSSQLSvc/DB01.thm.local:1433`. It's the label that says "this service runs under this account". Because any authenticated user can request a TGS for any SPN, service accounts are a target for **Kerberoasting** — requesting the ticket and cracking the encrypted part offline to recover the service account's password. Strong service-account passwords (or gMSA) defend against this.

### NetNTLM (challenge-response)
1. Client requests authentication from the server.
2. Server sends a random **challenge**.
3. Client combines its **NTLM hash** with the challenge to produce a **response** and sends it back.
4. Server forwards challenge + response to the DC.
5. DC recomputes the response from the challenge and compares; result sent back.
6. Server relays the result to the client.
The password/hash is never sent over the network.

**Local accounts exception & SAM:** for a **local** account, the server verifies the response itself without contacting the DC, because the password hash is stored locally in the **SAM (Security Account Manager)** — a local database (`C:\Windows\System32\config\SAM`) holding local accounts and their password hashes.
- The SAM holds only **local** accounts. Domain account hashes live in **NTDS.dit** on the DC (the much bigger target, holding every domain user's hash).
- Security relevance: with SYSTEM/admin access, attackers can extract SAM hashes (e.g. Mimikatz, secretsdump) for offline cracking or **Pass-the-Hash**.

---

## Trees, Forests and Trusts

As networks grow, a single domain isn't enough:
- **Tree** – multiple domains sharing the same namespace, e.g. root `thm.local` with subdomains `uk.thm.local` and `us.thm.local`. Each has its own DC, users and policies, giving partitioned control. The **Enterprise Admins** group grants admin rights over all domains in the enterprise, while each domain keeps its own **Domain Admins**.
- **Forest** – the union of several trees with **different** namespaces (e.g. merging `thm.local` and `mht.local` after an acquisition).

### Trust Relationships
Trusts let a user in one domain access resources in another.
- **One-way trust:** if Domain AAA trusts Domain BBB, users in BBB can be authorised on AAA. The **trust direction is opposite to the access direction**.
- **Two-way trust:** both domains authorise each other's users; this is the default when joining domains under a tree or forest.
- A trust does **not** automatically grant access — it only enables you to authorise users across domains; you still decide what is actually permitted.

---

## Key Takeaways
- AD centralises identity and policy management; the DC is the server running it.
- **OUs** organise objects and apply **policies** (one OU per user); **Groups** grant **permissions** (many groups per user).
- Protected OUs need Advanced Features + unchecking the protection flag before deletion; deleting an OU deletes its contents.
- **Delegation** gives limited control (e.g. password resets) without Domain Admin rights.
- **GPOs** are created, then linked to OUs; they inherit down to sub-OUs and are distributed via **SYSVOL** (`gpupdate /force` to sync now).
- Kerberos is the modern default; NetNTLM is legacy. SPN, SAM and NTDS.dit are all relevant attack surfaces (Kerberoasting, hash extraction, Pass-the-Hash).
- Trees share a namespace, forests join different namespaces, and trusts (one-way or two-way) control cross-domain access — with access direction opposite to trust direction.
- Real-world lesson: forcing a first-logon password change over RDP fails when the server enforces NLA/CredSSP.
