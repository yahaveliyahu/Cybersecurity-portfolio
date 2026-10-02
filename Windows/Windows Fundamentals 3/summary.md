# Windows Fundamentals 3 – Summary

## Overview
In this room, I completed the Windows Fundamentals series by exploring the **built-in security features** of the Windows operating system.  
Windows Fundamentals 1 covered the desktop, file system, UAC, Control Panel, Settings and Task Manager. Windows Fundamentals 2 covered utilities such as System Configuration, Computer Management and Resource Monitor. This room focused on the tools that ship with Windows to keep the device protected.

The lab machine was a **Windows Server 2019** instance with no Internet access, so some screens look different from a Windows 10/11 Home or Pro device.

---

## Windows Update
**Windows Update** is Microsoft's service for delivering security updates, feature enhancements and patches for Windows and other Microsoft products, such as Microsoft Defender.

### Patch Tuesday
- Updates are typically released on the **2nd Tuesday of each month**, known as **Patch Tuesday**
- Critical updates do not have to wait: urgent patches are pushed as soon as they are ready
- The **Microsoft Security Update Guide** (msrc.microsoft.com/update-guide) lists published security updates

### Accessing Windows Update
- **Settings → Update & Security → Windows Update**
- From the Run dialog or CMD: `control /name Microsoft.WindowsUpdate`

### Observations on the Lab Machine
- The Windows Update settings were shown as **managed** (set by an organisation's policy; home users usually do not see this)
- No updates were available, because the machine cannot reach Microsoft's servers
- Under **View update history → Definition Updates**, two updates were installed on **5/3/2021** (US date format):
  - **KB4052623** – Microsoft Defender Antivirus antimalware platform (version 4.18.2103.7)
  - **KB2267602** – Security Intelligence Update for Microsoft Defender Antivirus (version 1.337.511.0)

Definition updates are the signature and intelligence data Defender uses to recognise threats, so they are released far more often than regular patches.

### Forced Updates
For years, users postponed or skipped updates, mostly because they require a reboot. Since **Windows 10**, updates can only be **postponed**, not ignored: they are eventually installed and the computer restarts. The *Restart required* screen lets the user schedule the restart for a convenient time.

Unpatched systems are one of the most common ways attackers get in, which is why vulnerability management relies on regular patching.

---

## Windows Security
**Windows Security** is the central place to manage the tools that protect the device and its data. It is available under **Settings → Update & Security → Windows Security → Open Windows Security**.

### Protection Areas
- **Virus & threat protection**
- **Firewall & network protection**
- **App & browser control**
- **Device security**

### Status Icons
- **Green** – the device is sufficiently protected, no recommended actions
- **Yellow** – a safety recommendation to review
- **Red** – something needs immediate attention

On the lab machine, **Virus & threat protection** was marked **red** (*Actions needed*), while the other three areas were green.

---

## Virus & Threat Protection
Divided into two parts: **Current threats** and **Virus & threat protection settings**.

### Current Threats

**Scan options:**
- **Quick scan** – checks folders where threats are commonly found
- **Full scan** – checks all files and running programs on the disk (can take over an hour)
- **Custom scan** – checks only the files and locations I choose

Any file or folder can also be scanned on demand by right-clicking it and selecting **Scan with Microsoft Defender**.

**Threat history:**
- **Last scan** – Defender scans the device automatically
- **Quarantined threats** – isolated and prevented from running, then removed periodically
- **Allowed threats** – items identified as threats that the user allowed to run (only if 100% sure)

### Virus & Threat Protection Settings
- **Real-time protection** – locates and stops malware from installing or running
- **Cloud-delivered protection** – faster protection using the latest data from the cloud
- **Automatic sample submission** – sends sample files to Microsoft to improve protection for everyone
- **Controlled folder access** – only approved, trusted apps can modify files in protected folders
- **Exclusions** – files or folders the antivirus skips, used to reduce false positives
- **Notifications** – critical alerts about the health and security of the device

Exclusions are risky: an excluded folder is a blind spot, and attackers who gain admin rights often add exclusions so their tools are not scanned.

### Updates and Ransomware Protection
- **Check for updates** – manually updates the Defender definitions
- **Ransomware protection** depends on **Controlled folder access**, which in turn requires **Real-time protection** to be enabled

### The Red Status on the Lab Machine
The item Windows was asking to turn on was **Real-time protection**. It was disabled on purpose to avoid performance issues, which is acceptable only because the VM is isolated and contains no threats. On a personal device, it should always be enabled and up to date, unless a third-party product provides the same protection.

---

## Firewall & Network Protection
A **firewall** controls what traffic is, and is not, allowed to pass through a device's **ports**. Microsoft compares it to a security guard at the door, checking the ID of everything that enters or leaves.

### Firewall Profiles
- **Domain** – networks where the machine can authenticate to a **Domain Controller** (corporate networks)
- **Private** – user-assigned profile for trusted home or private networks
- **Public** – the **default** profile, for untrusted networks such as Wi-Fi in coffee shops and airports

When connected to airport Wi-Fi, the active profile would most likely be **Public**, which applies the strictest settings because other devices on the network cannot be trusted.

Clicking a profile offers two options: turning the firewall **on/off**, and **blocking all incoming connections**. The firewall should stay enabled unless I am completely sure of what I am doing.

### Allow an App Through the Firewall
The **Allowed apps** window shows which apps may communicate through the firewall, separately for the **Private** and **Public** profiles. On the lab machine:
- **AllJoyn Router** – allowed on Private only
- **Captive Portal Flow, Cast to Device functionality, Core Networking, Cortana, Delivery Optimization** – allowed on both Private and Public
- **BranchCache** and **COM+** entries – not allowed

Some entries provide more information through the **Details** button.

### Advanced Settings (WF.msc)
**Windows Defender Firewall with Advanced Security** opens with `WF.msc` and is aimed at advanced users. It manages **Inbound Rules**, **Outbound Rules**, **Connection Security Rules** (IPsec) and **Monitoring**.

On the lab machine:
- The firewall was **on** for all three profiles
- **Private Profile is Active**
- **Inbound** connections that do not match a rule are **blocked**
- **Outbound** connections that do not match a rule are **allowed**

This default (block inbound, allow outbound) means malware already running on a machine can usually connect out freely, which is why outbound traffic monitoring matters for detecting C2 communication (C2 is an abbreviation for Command and Control. It is the communication channel between malware running on an infected computer and the attacker's server).

---

## App & Browser Control
This section manages **Microsoft Defender SmartScreen**, which protects against phishing and malware websites and applications, and against downloading potentially malicious files.

- **Check apps and files** – checks unrecognised apps and files downloaded from the web
- SmartScreen can be set to **Warn** (the lab setting), **Block** or **Off**
- **Exploit protection** – built into Windows 10 and Windows Server 2019 to protect the device against exploitation techniques

The default settings should be left as they are unless there is a clear reason to change them.

---

## Device Security

### Core Isolation
- **Memory Integrity** – prevents attacks from inserting malicious code into high-security processes

### Security Processor (TPM)
The **Trusted Platform Module (TPM)** is a hardware **secure crypto-processor** that performs cryptographic operations. It has physical security mechanisms that make it **tamper-resistant**, and malicious software cannot tamper with its security functions.

The *Security processor details* screen (from a Windows 10 device) showed:
- **Manufacturer** – Intel (INTC)
- **Specification version** – 2.0
- **Status** – Attestation: Ready, Storage: Ready

---

## BitLocker
**BitLocker Drive Encryption** protects data from theft or exposure when a computer is **lost, stolen or improperly decommissioned**. If someone removes the disk and reads it on another machine, the data is still encrypted.

- BitLocker provides the most protection with **TPM version 1.2 or later**. The TPM stores the key and releases it only if the system has not been tampered with while offline, so the user does not need to do anything at boot
- On systems **without a TPM**, BitLocker can still encrypt the OS drive, but requires a **USB startup key**: a key stored on a removable drive that must be inserted every time the computer starts or resumes from hibernation. Without the USB, the computer will not boot
- BitLocker is not available on Windows Home editions (they only get a limited *Device Encryption* feature), and it was not included in the lab VM

---

## Volume Shadow Copy Service (VSS)
A **shadow copy** is a **snapshot** (point-in-time copy) of the data on a volume. It allows going back to how files looked at that moment if they are later deleted, overwritten or corrupted.

The **Volume Shadow Copy Service (VSS)** coordinates the actions needed to create a **consistent** shadow copy. If files are copied while an application is writing to them, the copy can end up half-written. VSS coordinates with running applications so they briefly pause writes and finish open operations before the snapshot is taken, without stopping the computer.

- Shadow copies are stored in the hidden **System Volume Information** folder on each drive with protection enabled
- **Restore Points** and **System Restore** are built on shadow copies

When **System Protection** is turned on, the following can be done from **Advanced system settings**:
- Create a restore point
- Perform a system restore
- Configure restore settings
- Delete restore points

### Configuring Shadow Copies on the Lab Machine
Right-click **Local Disk (C:)** in File Explorer → **Configure Shadow Copies...**  
The window showed shadow copies **Disabled** for `C:\` (no scheduled run time and no existing copies), with buttons to **Enable**, **Create Now**, **Delete Now** and **Revert**.

### Security Perspective
Shadow copies are a way to recover files after a **ransomware** attack, and attackers know it. Many ransomware families delete them before encrypting, commonly with:

```
vssadmin delete shadows /all /quiet
```

Once they are gone, the files cannot be recovered from the machine itself. This is why an **offline or off-site backup** is essential, and why defenders treat shadow copy deletion as a strong ransomware indicator.

---

## Living Off The Land
Attackers often use **built-in Windows tools and utilities** to avoid detection, since legitimate signed binaries do not raise the same suspicion as custom malware. This tactic is called **Living Off The Land**.  
The **LOLBAS project** (lolbas-project.github.io) documents Windows binaries, scripts and libraries that can be abused this way. `vssadmin` deleting shadow copies is a good example.

---

## Key Takeaways
- Windows ships with layered built-in security: updates, antivirus, firewall, SmartScreen, exploit protection, TPM, BitLocker and shadow copies
- Regular patching (Patch Tuesday and urgent out-of-band updates) closes the vulnerabilities attackers rely on
- Real-time protection, the firewall and default security settings should stay enabled; exclusions and allowed threats create blind spots
- Firewall profiles apply different levels of trust, and public networks get the strictest rules
- TPM and BitLocker protect data at rest on lost or stolen devices
- Shadow copies help recover from ransomware, so attackers delete them, and offline backups remain essential
- Attackers abuse legitimate Windows tools (Living Off The Land), so defenders must know what normal usage looks like
