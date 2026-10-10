# Windows Event Logs – Summary

## Overview
In this room, I explored **Windows Event Logs**: what they are, how they are stored, and how to query them using different tools and techniques.  
The focus was on how defenders (Blue Teamers) use event logs to understand system activity, detect malicious behaviour, and investigate incidents.

I worked with three tools – **Event Viewer**, **wevtutil.exe** and **Get-WinEvent** – and learned to filter events with **FilterHashtable** and **XPath** queries.  
I also learned which Windows features must be enabled to log important events, and where to find lists of event IDs worth monitoring.

Event logs are only useful if you know **what to look for** and **how to search for it**.

---

## What Are Event Logs?
Event logs record events that take place during the execution of a system. They provide an **audit trail** that helps to understand system activity and diagnose problems.

There are two main use cases:
- **IT / System administrators** – troubleshooting issues on an endpoint
- **Defenders (Blue Team)** – detecting and investigating malicious activity

Combining logs from multiple sources can reveal correlations between events that seem unrelated.  
This is where **SIEMs** (Security Information and Event Management), such as **Splunk** and **Elastic**, come into play. In a large organisation, analysts query logs from all endpoints in one place instead of connecting to each machine.

Windows is not the only operating system with a logging system. On Linux, the logging system is known as **Syslog**.

---

## How Windows Event Logs Are Stored
- Event logs are **not text files** – they are stored in a proprietary **binary format**
- File extension: **.evtx** (older format: .evt, used before Windows Vista)
- Default location: `C:\Windows\System32\winevt\Logs`
- Each event is structured internally as **XML**, which is why XML View and XPath queries work
- In file names, the `/` in a log name is replaced by `%4`  
  Example: `Microsoft-Windows-PowerShell%4Operational.evtx`

A saved .evtx file can be moved to another computer and analysed there – useful when the original machine cannot be accessed.

---

## Log Categories
| Log | What it records |
|---|---|
| **System** | Operating system events – drivers, hardware changes, services |
| **Security** | Logon/logoff and other security events, based on the audit policy |
| **Application** | Events from installed applications – errors, warnings, information |
| **Directory Service** | Active Directory changes, mainly on Domain Controllers |
| **File Replication Service** | Sharing of Group Policies and logon scripts between Domain Controllers |
| **DNS** | Domain events on DNS servers |
| **Custom** | Logs created by applications that need their own storage |

Beyond the classic logs, almost every Windows component has its own log under **Applications and Services Logs** (for example `Microsoft-Windows-PowerShell/Operational`).  
Most of them exist even if they are empty – on the lab machine there were over **1,000** log names.

---

## Event Types
Every event is classified into one of five types:

| Type | Meaning | Example |
|---|---|---|
| **Error** | Significant problem – loss of data or functionality | A service fails to load at startup |
| **Warning** | Not necessarily significant, may indicate a future problem | Low disk space |
| **Information** | Successful operation of an application, driver or service | A network driver loads successfully |
| **Success Audit** | Audited security access attempt that succeeded | A user logs on successfully |
| **Failure Audit** | Audited security access attempt that failed | A user fails to access a network drive |

The first three describe **severity**.  
The audit types describe **who tried to access what, and whether it worked** – they are recorded in the Security log.

---

## Tools for Accessing Event Logs
1. **Event Viewer** – GUI application
2. **wevtutil.exe** – command-line tool
3. **Get-WinEvent** – PowerShell cmdlet

---

## Event Viewer

### MMC and Snap-ins
Event Viewer is a **Microsoft Management Console (MMC) snap-in**.  
MMC is an empty framework for management tools – each **snap-in** adds a management tool to it. A `.msc` file is a saved console with snap-ins already loaded.

Common snap-ins:
| Snap-in | Command |
|---|---|
| Event Viewer | `eventvwr.msc` |
| Services | `services.msc` |
| Task Scheduler | `taskschd.msc` |
| Local Security Policy | `secpol.msc` |
| Group Policy Editor | `gpedit.msc` |
| Computer Management | `compmgmt.msc` |

### The Three Panes
- **Left pane** – hierarchical tree of logs and providers
- **Middle pane** – list of events and details of the selected event
- **Right pane** – Actions

### Event Columns
| Column | Meaning |
|---|---|
| **Level** | The event type (Information, Warning, Error...) |
| **Date and Time** | When the event was logged |
| **Source** | The software (provider) that logged the event |
| **Event ID** | A number that maps to a specific operation **for that source** |
| **Task Category** | The event category, defined by the source |

**Important:** Event IDs are **not unique**. Event ID 4103 means "Executing Pipeline" in the PowerShell log, but can mean something completely different for another provider.  
What identifies an event is the combination of **Provider + Event ID**.

### Event Details
- **General** tab – rendered, human-readable data
- **Details** tab – **Friendly View** (tree) or **XML View** (the raw event structure)

When reading an event, the key fields are **who, where, when and what**: User, Computer, Logged, and the event content.

### Log Properties
Right-click a log → **Properties** shows its location, size, and creation/modification dates.  
It also shows the **maximum log size** and what happens when it is reached – this is called **log rotation**:
- Overwrite events as needed (oldest first) – **Circular**
- Archive the log when full
- Do not overwrite events

The **Clear Log** button has legitimate uses, but adversaries use it to cover their tracks.

### Useful Actions
- **Open Saved Log** – open an .evtx file (used in this room with `merged.evtx`)
- **Filter Current Log** – temporary filter on the current log only
- **Create Custom View** – saved filter that can cover multiple logs
- **Connect to Another Computer** – right-click *Event Viewer (Local)*

| | Filter Current Log | Create Custom View |
|---|---|---|
| Saved? | No | Yes, under Custom Views |
| Multiple logs? | No | Yes |
| By log / By source | Greyed out | Available |

The "By log / By source" options are greyed out in Filter Current Log because the filter only applies to the current log.

In the filter window, Event IDs can be included or excluded:  
`4103,4104` (list), `5-99` (range), `-76` (exclude).

---

## wevtutil.exe
A command-line tool to query, export, archive and clear event logs, and to view information about logs and publishers.  
Its main advantage over Event Viewer is **automation** – it can be used in scripts.

Help: `wevtutil /?`, and for a specific command: `wevtutil qe /?`

### Main Commands
| Short | Long | Purpose |
|---|---|---|
| `el` | enum-logs | List log names |
| `gl` | get-log | Get log configuration |
| `sl` | set-log | Modify log configuration |
| `ep` | enum-publishers | List event publishers |
| `gp` | get-publisher | Get publisher information |
| `qe` | query-events | Query events from a log or log file |
| `gli` | get-log-info | Get log status information |
| `epl` | export-log | Export a log |
| `al` | archive-log | Archive an exported log |
| `cl` | clear-log | Clear a log |

### Options for `qe` (query-events)
| Option | Meaning |
|---|---|
| `/c:N` | Maximum number of events to read |
| `/rd:true` | Event read direction – newest events first (default is oldest first) |
| `/f:text` | Output as readable text (default is XML) |
| `/lf:true` | The source is a **path to a log file** (.evtx), not a log name |
| `/q:"..."` | Filter with an **XPath query** |

### Common Options
| Option | Meaning |
|---|---|
| `/r` | Run on a remote computer |
| `/u`, `/p` | Username and password for the remote computer |
| `/a` | Authentication type (Default, Negotiate, Kerberos, NTLM) |
| `/uni` | Display output in Unicode |

### Examples
```cmd
:: Count log names (in PowerShell)
wevtutil el | Measure-Object

:: 3 newest events from the Application log, as text
wevtutil qe Application /c:3 /rd:true /f:text

:: Query a saved file
wevtutil qe C:\Users\Administrator\Desktop\merged.evtx /lf:true /c:5 /f:text

:: XPath filter
wevtutil qe Security /q:"*[System[(EventID=4624 or EventID=4625)]]" /c:5 /rd:true /f:text
```

`query-events` can read from three sources: an **event log**, a **log file**, or a **structured query**.

### Security Note
`wevtutil cl` is commonly used by attackers to clear logs. Clearing a log leaves its own evidence:
- **1102** – the Security log was cleared
- **104** – another log (e.g. System) was cleared

---

## Get-WinEvent

### What Is a cmdlet?
A **cmdlet** is a built-in PowerShell command with a **Verb-Noun** name (Get-WinEvent, Measure-Object, Select-Object...).  
Unlike wevtutil, which returns text, cmdlets return **objects**. This makes it easy to select fields, filter, sort and count results with other cmdlets.

### Get-WinEvent vs Get-EventLog
| | Get-EventLog | Get-WinEvent |
|---|---|---|
| Classic logs (System, Security...) | Yes | Yes |
| Applications and Services Logs | No | Yes |
| .evtx files | No | Yes (`-Path`) |
| PowerShell 7 | Removed | Yes |

**Get-WinEvent replaces Get-EventLog.**

### Listing Logs and Providers
```powershell
Get-WinEvent -ListLog *                  # all logs
Get-WinEvent -ListLog *OpenSSH*          # logs matching a pattern
Get-WinEvent -ListProvider *PowerShell*  # providers and their LogLinks
```
- A **log** is where events are stored
- A **provider** is what writes the events (the "Source" in Event Viewer). `LogLinks` shows which logs it writes to

```powershell
# Number of event types a provider defines (its "catalogue", not events that happened)
(Get-WinEvent -ListProvider Microsoft-Windows-PowerShell).Events | Measure-Object
```

### Filtering: Where-Object vs FilterHashtable
```powershell
# Slow – retrieves all events, then filters
Get-WinEvent -LogName Application | Where-Object { $_.ProviderName -Match 'WLMS' }

# Fast – filters while retrieving (recommended by Microsoft)
Get-WinEvent -FilterHashtable @{ LogName='Application'; ProviderName='WLMS' }
```

Hash table syntax: `@{ <name> = <value>; <name> = <value> }`  
Begin with `@`, use braces `{}`, `=` between key and value, and `;` between pairs (not needed when each pair is on a new line).

### FilterHashtable Keys
| Key | Type | Wildcards |
|---|---|---|
| LogName | String[] | Yes |
| ProviderName | String[] | Yes |
| Path | String[] | No |
| Keywords | Long[] | No |
| ID | Int32[] | No |
| Level | Int32[] | No |
| StartTime / EndTime | DateTime | No |
| UserID | SID | No |
| Data | String[] | No |
| `<named-data>` | String[] | No |

- `[]` means multiple values are allowed – multiple values for the same key act as **OR**
- Different keys act as **AND**
- **Level** is a number: 1 = Critical, 2 = Error, 3 = Warning, 4 = Information, 5 = Verbose
- **Source** in Event Viewer = **ProviderName** in FilterHashtable
- `<named-data>` lets you filter by a field inside EventData, e.g. `TargetUserName = 'Administrator'`

Event Viewer can be used to quickly build a hash table: Log Name → `LogName`, Source → `ProviderName`, Event ID → `Id`, Level → `Level`.

### Other Useful Parameters
| Parameter | Meaning | wevtutil equivalent |
|---|---|---|
| `-MaxEvents N` | Number of events to return | `/c:N` |
| `-Oldest` | Oldest first (default is newest first) | `/rd:false` |
| `-Path` | Read from an .evtx file | `/lf:true` |
| `-FilterXPath` | XPath query | `/q:` |

### Searching for Sensitive Data
```powershell
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-PowerShell/Operational'; ID=4104} |
  Select-Object -Property Message | Select-String -Pattern 'SecureString'
```
4104 events record full script code, so passwords converted with `ConvertTo-SecureString ... -AsPlainText` can be exposed in the logs.

### PowerShell Lessons I Learned
- **`Format-Table` / `Format-List` are for display only** and must come at the end of a pipeline. `Format-Table` adds formatting objects, so `| Format-Table | Measure-Object` returned **192** instead of the real count of **188**
- **`Select-Object`** selects fields and keeps real objects
- **`Group-Object`** groups and counts – answers "what is there and how much"
- **`Sort-Object TimeCreated`** builds a timeline – answers "what happened, in what order"
- **`[datetime]'2020-12-10 08:37:00'`** – ISO date format avoids MM/DD vs DD/MM confusion
- Fields inside **EventData** are not ready-made object properties. To read them, convert the event with `[xml]$_.ToXml()` and read each `Data` element by its `Name`
- Fields from System, such as `ProcessId`, `ThreadId`, `RecordId` and `MachineName`, are ready-made properties

---

## XPath Queries
**XPath** (XML Path Language) is a W3C standard for addressing parts of an XML document. Windows Event Log supports a **subset of XPath 1.0**.  
Both **wevtutil** (`/q`) and **Get-WinEvent** (`-FilterXPath`) support it.

The easiest way to build a query is to open the event in **Details → XML View** and work down the XML tree:

```xml
<Event>
  <System>
    <Provider Name="WLMS" />
    <EventID>100</EventID>
    <Level>4</Level>
    <TimeCreated SystemTime="2020-12-15T01:09:08.940277500Z" />
    <EventRecordID>238</EventRecordID>
    <Computer>WIN-1O0UJBNP9G7</Computer>
  </System>
  <EventData>
    <Data Name="TargetUserName">System</Data>
  </EventData>
</Event>
```

### Building Rules
- A query starts with `*` or `Event`
- **Value inside a tag** → `EventID=100`
- **Value in an attribute** (`@`) → `Provider[@Name="WLMS"]`
- **EventData field** → select the Data element by its Name attribute, then compare its text:  
  `*/EventData/Data[@Name="TargetUserName"]="System"`
- **Field exists** (without checking its value) → `*/EventData/Data[@Name="ScriptBlockText"]`
- Combine conditions with `and` / `or`

### Examples
```powershell
Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=100'
Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"]'
Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=101 and */System/Provider[@Name="WLMS"]'
Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="System"' -MaxEvents 1
```
```cmd
wevtutil qe Application /q:*/System[EventID=100] /f:text /c:1
```
```
*[System[(Level <= 3) and TimeCreated[timediff(@SystemTime) <= 86400000]]]
```
The last query selects events with level ≤ 3 (Critical, Error, Warning) from the last 24 hours.

---

## Event IDs to Monitor – Resources
To monitor and hunt effectively, you need to know what to look for. Useful resources:

- **The Windows Logging Cheat Sheet** – event IDs by category (accounts, processes, log clear...). Example: **7045** in the **System** log = a new service was installed
- **Spotting the Adversary with Windows Event Log Monitoring (NSA)** – recommended events to collect, including Windows Firewall events:

| Action | Event ID | Level |
|---|---|---|
| Firewall Rule Add | 2004 | Informational |
| Firewall Rule Change | 2005 | Informational |
| Firewall Rules Deleted | 2006, 2033 | Informational |
| Firewall Failed to load Group Policy | 2009 | Error |

These are logged in `Microsoft-Windows-Windows Firewall With Advanced Security/Firewall`, not in the Security log.  
Normal users should not modify firewall rules, so any local change is worth reviewing.

- **MITRE ATT&CK** – each technique includes mitigation and detection tips

### MITRE ATT&CK
**ATT&CK** = Adversarial Tactics, Techniques, and Common Knowledge – an open knowledge base of real-world attacker behaviour.
- **Tactics** (TA...) – the attacker's goal ("why"), e.g. Persistence, Defense Evasion
- **Techniques** (T...) – how the goal is achieved, e.g. T1098
- **Sub-techniques** (T....001) – a more specific variation
- **Software** (S...) – malware and tools, e.g. Emotet = S0367

Example – **T1098 Account Manipulation**, detection events:
- **4738** – a user account was changed
- **4728** – a member was added to a security-enabled global group
- **4670** – permissions on an object were changed

**The tools in this room answer "how to search". ATT&CK and the NSA guide answer "what to search for".**

---

## Enabling Additional Logging
Some important events are **not generated by default**.

### PowerShell Logging
`Local Computer Policy > Computer Configuration > Administrative Templates > Windows Components > Windows PowerShell`

| Setting | Result |
|---|---|
| Turn on Module Logging | Event **4103** – commands and parameters |
| Turn on PowerShell Script Block Logging | Event **4104** – full script code |
| Turn on PowerShell Transcription | Text files with the full session, including output |
| Turn on Script Execution | Execution Policy – not related to logging |

These settings can also be enabled through the **Registry** (`HKLM\SOFTWARE\Policies\Microsoft\Windows\PowerShell\...`).

### Audit Process Creation (Event 4688)
Two settings are required:
1. **Audit Process Creation** (Success) – `secpol.msc` → Advanced Audit Policy Configuration → Detailed Tracking. This makes 4688 events appear
2. **Include command line in process creation events** – `gpedit.msc` → Administrative Templates → System → Audit Process Creation. This adds the **Process Command Line** field

Without the command line, an analyst only sees *that* `cmd.exe` ran. With it, they see *what* it ran.

### Basic vs Advanced Audit Policy
- **Basic** – 9 broad categories (Local Policies → Audit Policy)
- **Advanced** – about 50 detailed subcategories (Advanced Audit Policy Configuration)

The basic policy can override the advanced one. Enabling  
**"Audit: Force audit policy subcategory settings to override audit policy category settings"** (Security Options) makes the advanced policy take priority.  
`auditpol /get /category:*` shows what is actually enabled.

---

## Practical Exercise – Command Line Process Auditing
I followed Microsoft's "Try This" exercise:
1. Enabled Audit Process Creation and the "Force audit policy subcategory" setting
2. Created and ran a `.bat` script (mkdir, copy from an admin share, start a .vbs file, del)
3. Enabled command line process auditing
4. Ran the script again and compared the 4688 events

What I found:
- The `copy` from `\\192.168.1.254\c$` and the `start` of the VBS file failed – the remote computer does not exist in the lab, and the script also had a path mismatch
- `mkdir`, `copy` and `del` are internal `cmd.exe` commands, so they do not create their own 4688 events
- The command line was **already being recorded** before I changed anything:  
  `gpedit` showed **Not configured**, but the registry value `ProcessCreationIncludeCmdLine_Enabled` was **1**.  
  The value had been written directly to the Registry when the machine was prepared, so gpedit did not reflect it
- I found my script's event by searching for `script.bat` with **Find**:  
  `explorer.exe → cmd.exe /c "C:\Users\Administrator\Desktop\script.bat"`

**Lesson:** the GUI does not always reflect the real system state – the Registry does. An attacker can change security settings directly in the Registry.

---

## Putting Theory into Practice – merged.evtx Scenarios
All questions were answered from the saved file `C:\Users\Administrator\Desktop\merged.evtx` (77,524 events from multiple logs and computers).

### Scenario 1 – PowerShell Logging
**PowerShell Downgrade Attack:** an attacker runs `powershell -Version 2`, a version without Script Block Logging, Module Logging or AMSI. ATT&CK: **T1562.010**.  
Detection: event **400** in the *Windows PowerShell* log with **EngineVersion=2.0**.

```powershell
Get-WinEvent -Path C:\Users\Administrator\Desktop\merged.evtx -FilterXPath '*/System/EventID=400' |
  Where-Object { $_.Message -like '*EngineVersion=2*' } | Format-List Message
```
- Event ID: **400**
- Time: **12/18/2020 7:50:33 AM**
- `PreviousEngineState=None` → `NewEngineState=Available` means the engine started

### Scenario 2 – Log Clearing
I first ran a **general check** of all events written by the event log service:
```powershell
Get-WinEvent -FilterHashtable @{ Path = '...\merged.evtx'; ProviderName = 'Microsoft-Windows-Eventlog' } |
  Select-Object TimeCreated, Id, RecordId, Message
```
- Only one clear event: **104** – *The System log file was cleared*
- Event Record ID: **27736**
- Computer name: **PC01.example.corp** (from `MachineName` – the event came from a different computer than the lab machine)

### Scenario 3 – Emotet
**Emotet** started as a banking trojan and became a malware loader spread through phishing emails with Word macros.  
The macro runs an obfuscated PowerShell **downloader**, which downloads and runs Emotet itself.

```powershell
Get-WinEvent -Path ...\merged.evtx -FilterXPath '*/System/EventID=4104 and */EventData/Data[@Name="ScriptBlockText"]' |
  Sort-Object TimeCreated | Select-Object -First 1 | Format-List TimeCreated, Message
```
- First variable: **$Va5w3n8**
- Time: **8/25/2020 10:09:28 PM**
- Execution Process ID: **6620** (`ProcessId` – the PowerShell process that wrote the event)

Many 4104 events are noise (`prompt`, `Set-StrictMode ... $_`). The real payload had to be identified first.

Obfuscation techniques in the payload:
- String splitting: `('ne'+'w-'+'item')`
- Backticks inside words: ``SecURi`T`ypRO`T`oCOL``
- Mixed casing: `eNV:teMP\WOrd`
- Characters by number: `[CHAr]92` = `\`
- Format operator: `-F`
- Junk variables that are never used

What the script does: creates `%TEMP%\Word\2019\`, enables TLS, loops over a list of URLs, downloads an `.exe`, and runs it if it is larger than 28,315 bytes.  
In the lab version, the downloaded file is overwritten with `calc.exe` before execution – the sample was neutralised.

**Reading the log is safe; running the code is not.** Malware is analysed statically, or dynamically only in an isolated sandbox.

### Scenario 4 – Group Enumeration
`net localgroup administrators` runs `net.exe`, which launches **net1.exe** to do the work. ATT&CK: **T1069.001**.

General check first:
```powershell
Get-WinEvent -Path ...\merged.evtx | Where-Object { $_.Message -like '*net1.exe*' } |
  Group-Object Id | Select-Object Count, Name
```
- **4799** – a security-enabled local group membership was enumerated (1 event)
- **4798** – a user's local group membership was enumerated (2 events)

Details from the 4799 event: User **IEUser**, Group **Administrators**, Caller **C:\Windows\System32\net1.exe**
- Group Security ID: **S-1-5-32-544** (Administrators has the same SID on every Windows computer)
- Event ID: **4799**

---

## Extra Investigation – Event 1101
During the log-clearing check, I found event **1101** – *Audit events have been dropped by the transport* – at 12/10/2020 8:39 AM. This means security events were lost.

Investigation steps:
1. **Time window** around the event with `StartTime` / `EndTime` – a burst of events at 8:39, nothing before or after
2. **Grouping and counting** with `Group-Object ProviderName, Id` – the burst was a **system boot**: Kernel-General 12, Kernel-Boot, EventLog 6005, Security 4608, 77 × Service Control Manager 7036
3. **Previous shutdown was unexpected**: EventLog **6008** and Kernel-Power **41**. This matched the missing **1100** event before that boot. VMware components showed it was a virtual machine
4. **Process check** of the eleven 4688 events – all were standard boot processes from System32 with the correct parents:
```
smss.exe
├── smss.exe → csrss.exe, wininit.exe → services.exe, lsass.exe
└── smss.exe → csrss.exe, winlogon.exe
```
`autochk.exe` (disk check) also confirmed the unclean shutdown.

**Conclusion:** event 1101 was caused by the load of a boot after an unexpected shutdown, not by an attack.

---

## Key Event IDs
| Event ID | Log | Meaning |
|---|---|---|
| 104 | System | A log was cleared |
| 400 | Windows PowerShell | PowerShell engine started (check EngineVersion) |
| 800 | Windows PowerShell | Pipeline execution details |
| 1100 | Security | Event logging service shut down |
| 1101 | Security | Audit events dropped |
| 1102 | Security | Security log was cleared |
| 4103 | PowerShell/Operational | Module logging – command executed |
| 4104 | PowerShell/Operational | Script block logging – full code |
| 4608 | Security | Windows is starting up |
| 4624 / 4625 | Security | Successful / failed logon |
| 4688 | Security | A new process was created |
| 4719 | Security | Audit policy was changed |
| 4720 | Security | A user account was created |
| 4724 | Security | Attempt to reset an account's password |
| 4738 | Security | A user account was changed |
| 4798 | Security | A user's local group membership was enumerated |
| 4799 | Security | A local group membership was enumerated |
| 6005 / 6008 | System | Event log service started / previous shutdown was unexpected |
| 7045 | System | A new service was installed |
| 2004–2006, 2033 | Firewall | Firewall rule added / changed / deleted |

---

## Lessons Learned
- **Check the data source first.** `merged.evtx` combines many logs, so a question about PowerShell/Operational must be filtered to that provider
- **The room was updated, but some answers were not.** When the data does not match the question, go back to the context of the task
- **General checks before narrow ones.** Searching only for 104 and 1102 would have missed event 1101
- **Event IDs are not unique** – always combine Provider + Event ID
- **Live logs change and can be overwritten** (Circular mode) – analyse a fixed saved file
- **Event Viewer sorts by seconds only** – the exact order is in `SystemTime` and `EventRecordID`
- **Record IDs are counted per log** – in a merged file, combine RecordId with Event ID
- **The GUI can be misleading** – gpedit showed "Not configured" while the Registry value was enabled
- **Never run malware code** – read and deobfuscate it instead

---

## Key Takeaways
- Event logs are a core data source for detection and investigation
- Event Viewer is good for exploring; wevtutil and Get-WinEvent are good for automation and large datasets
- FilterHashtable and XPath filter events efficiently while retrieving them
- Important logging (PowerShell, process command lines) must be **enabled** – visibility gaps are what attackers exploit
- Attackers try to blind defenders by clearing logs, disabling logging, or downgrading PowerShell – and each of these leaves its own evidence
- Knowing **what to look for** (NSA guide, MITRE ATT&CK, cheat sheets) is as important as knowing how to search
- This room is a foundation for Windows Internals, Sysmon and SIEM tools such as Splunk
