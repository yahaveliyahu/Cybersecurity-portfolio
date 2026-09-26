# Windows Fundamentals 1 – Summary

## Overview
In this room, I explored the **fundamentals of the Windows operating system** from a security perspective.  
The focus was on understanding the Windows environment, navigating its user interface, and making changes to the system, while noting where each topic connects to cybersecurity.

The room was a general overview meant to make working with Windows more comfortable, and each topic has deeper intermediate and advanced levels covered in later modules.

**Note:** The lab machine ran **Windows Server 2019 Standard**, accessed via Remote Desktop (RDP).

---

## Windows History and Versions
Windows has been around since **1985** and is the dominant OS in both home and corporate networks. Because of this dominance, it has always been a primary target for hackers and malware writers.

Key points from the version history:
- **Windows XP** – popular for a long time; its end-of-life caused panic among organisations that had to migrate.
- **Windows Vista** – a complete overhaul, poorly received, and short-lived.
- **Windows 7** – the migration target after XP; vendors scrambled to make products compatible.
- **Windows 8.x** – short-lived, like Vista; first version aimed at touch-screen tablets.
- **Windows 10 / Windows 11** – Windows 11 is the current desktop OS, available in **Home** and **Pro** editions.
- **Windows Server 2025** – the current server version.

A notable difference between editions: **BitLocker** drive encryption is available on **Pro** but not on **Home**.

---

## Accessing the Lab (Remote Desktop / RDP)
The lab machine can be used directly in the browser or accessed via **RDP (Remote Desktop Protocol)**, Microsoft's protocol for connecting to a remote Windows desktop (port **3389**).

- Unlike SSH (command line only), RDP provides the full graphical desktop.
- On Windows, the client is `mstsc`; on Linux, tools like Remmina or `xfreerdp`.
- Connecting from a personal machine requires being on TryHackMe's network via **OpenVPN**, because lab machines use private (10.x.x.x) addresses.
- A **self-signed certificate** warning is normal in a lab and can be accepted.

**Security angle:** Exposed RDP is a common attack vector — brute-force against port 3389, vulnerabilities like **BlueKeep**, and lateral movement inside a compromised network.

---

## The File System – NTFS
Modern Windows uses **NTFS (New Technology File System)**, which replaced older systems like **FAT16/FAT32** and **HPFS**. FAT partitions are still seen today on USB drives and MicroSD cards.

NTFS is a **journaling file system** — it logs changes so the file system can repair itself after a failure, something FAT cannot do.

Advantages of NTFS over FAT:
- Supports files larger than **4GB**
- **Permissions** on files and folders
- Folder and file **compression**
- **Encryption** via EFS (and BitLocker works on NTFS volumes)

You can check a drive's file system via **right-click the drive → Properties**.

---

### NTFS Permissions
On NTFS volumes, permissions grant or deny access to files and folders:

- **Read** – view/list a folder's contents; open and read a file
- **Write** – add files/subfolders; write to a file (does **not** include read or delete)
- **Read & Execute** – Read plus running executables; inherited by files and folders
- **List Folder Contents** – like Read & Execute but inherited by **folders only** (N/A for files)
- **Modify** – read, write, execute, **and delete**
- **Full Control** – everything in Modify **plus** changing permissions and taking ownership

Viewed via **right-click → Properties → Security tab**.

**Security angle:** Overly broad permissions (e.g. a standard user with Write/Modify on a service executable running as SYSTEM) are a classic **privilege escalation** path.

---

### Alternate Data Streams (ADS)
**ADS** is an NTFS feature that lets a single file hold more than one stream of data.

- Every file has at least one main stream: `$DATA` (the visible content).
- Additional named streams (`filename:streamname`) can hold anything — text, files, even executables.
- ADS content is **hidden**: it doesn't appear in File Explorer, doesn't change the file's displayed size, and isn't shown by a plain `dir`.

**Legitimate use – Mark of the Web:** When a file is downloaded, Windows adds a `Zone.Identifier` stream recording its origin, which triggers security warnings on downloaded files.

**Security angle:** Malware writers have used ADS to hide code and data. Modern tools now scan alternate streams.

Hands-on commands I used:
- `Get-Item -Path .\file -Stream *` — list all streams of a file
- `Get-Content -Path .\file -Stream Zone.Identifier` — read a specific stream
- `dir /r` (CMD) — show alternate streams
- `Unblock-File` — remove the Zone.Identifier stream

I confirmed this live: a downloaded README had a `:$DATA` stream (1925 bytes) plus a hidden `Zone.Identifier` stream (54 bytes) showing `ZoneId=3` (Internet) and `HostUrl=https://github.com/`.

---

## The Windows Folder and Environment Variables
The Windows OS lives in `C:\Windows`, but it doesn't have to be on the C drive — this is why **environment variables** exist.

- The system variable for the Windows directory is **`%windir%`**.
- Environment variables store information about the OS environment (paths, number of processors, temp folder locations, etc.).
- Other examples: `%USERPROFILE%`, `%TEMP%`, `%PATH%`.

Inside `C:\Windows` is the **System32** folder, which holds files critical to the OS. Deleting files here can make Windows inoperable, so it must be handled with extreme caution. Many tools in the Windows Fundamentals series live in System32.

**Security angle:** `PATH` is a target for **PATH hijacking**, where an attacker gets a malicious program run in place of a legitimate one.

---

## User Accounts, Groups, and lusrmgr
Local Windows accounts are one of two types:
- **Administrator** – can make system-level changes (add/remove users, install programs, modify settings).
- **Standard User** – limited to their own files/folders; cannot make system-level changes.

Each account gets a profile folder under `C:\Users` (e.g. `C:\Users\Max`), created on first login.

Accounts and groups can be managed with **`lusrmgr.msc`** (Local Users and Groups), opened via **Run**.

**Built-in accounts** seen on the system:
- **Administrator** – built-in account for administering the computer/domain
- **Guest** – built-in account for guest access (disabled by default)
- **DefaultAccount** – system-managed account for internal scenarios
- **WDAGUtilityAccount** – used by Windows Defender Application Guard

### Groups
A **group** is a collection of users that share the same permissions. When a user is added to a group, they **inherit** that group's permissions, and a user can belong to multiple groups. Important built-in groups include **Administrators**, **Users**, **Guests**, and **Remote Desktop Users**.

An account's group memberships can be checked via **Properties → Member Of**, or with `net user USERNAME` (see the **Local Group Memberships** line).

**Security angle:** After compromising an account, attackers often try to add it to **Administrators** for privilege escalation, so checking membership of sensitive groups is a key part of investigating a breach.

---

## User Account Control (UAC)
Most home users run as local administrators, which is risky — malware runs in the context of the logged-in user, so an admin session gives malware admin power.

**UAC**, introduced with Windows Vista, mitigates this:
- Even when an administrator is logged in, the session does **not** run with elevated privileges by default.
- When an action needs higher privileges (e.g. installing a program), the user is **prompted to confirm**.
- **Note:** UAC does **not** apply to the built-in administrator account by default.

Hands-on demonstration: Logged in as a standard user (`tryhackmebilly`, whose password was stored in the account's **Description** field), the installer showed a **shield icon** and triggered a **UAC prompt** demanding an administrator password. Without the password, the install failed — showing that standard users cannot install software on their own.

---

## Settings vs Control Panel
Two primary places to change the system:
- **Control Panel** – the traditional location for more complex settings and actions (printers, uninstalling programs, network adapters).
- **Settings** – introduced in Windows 8, now the primary location for changes; touch-friendly.

Notes:
- Installed applications can be viewed via **Control Panel → Programs → Programs and Features** (names, publishers, versions).
- The two menus overlap — starting in Settings can lead into a Control Panel window (e.g. **Network & Internet → Change adapter options**).
- When unsure which to use, **search from the Start menu**.
- Setting Control Panel view to **Small icons** sorts items alphabetically.

---

## Task Manager
The **Task Manager** shows currently running applications and processes, plus resource use (CPU, RAM) under **Performance**.

- Opened by right-clicking the taskbar, or directly with the shortcut **`Ctrl+Shift+Esc`**.
- Opens in **Simple View**; **More details** expands it to the full view.
- (`Ctrl+Alt+Del` only opens a security screen from which Task Manager can be chosen — not a direct shortcut.)

---

## Key Takeaways
- Windows' dominance makes it a constant target, so understanding it is foundational for security work.
- **NTFS** underpins Windows security features — permissions, encryption, and ADS.
- **ADS** is a legitimate feature (Mark of the Web) that doubles as a hiding place for malware.
- **Environment variables** make the system portable but introduce risks like PATH hijacking.
- **Users, groups, and UAC** together enforce the principle of least privilege; misconfigured permissions and group memberships enable privilege escalation.
- Knowing the built-in tools (`lusrmgr.msc`, `net user`, Control Panel, Task Manager, PowerShell) is essential for both administration and investigation.
