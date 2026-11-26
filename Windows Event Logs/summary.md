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

## Windows Log Types
Windows stores its logs in separate log files, each dedicated to a specific category:

| Log | What it records |
|---|---|
| **System** | Operating system events – drivers, hardware changes, system startup, services, etc |
| **Security** | Logon/logoff and other security events, based on the audit policy |
| **Application** | Events from installed applications – errors, warnings, compatibility issues, etc |
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

Unlike log files that have no built-in viewer, Windows includes **Event Viewer** – a graphical tool for viewing and searching logs.

### Overview and Summary Page
When Event Viewer opens, the main page shows:
- **Summary of Administrative Events** – How many events of each level (Critical, Error, Warning, Information, Audit Success) occurred in the last hour, 24 hours and 7 days
- **Recently Viewed Nodes** – Logs I opened recently
- **Log Summary** – Each log's size, last modification time, whether it is enabled, and its **retention policy** (for example, *Overwrite events as needed* – when the log reaches its maximum size, the oldest events are deleted to make room for new ones)

### The Three Panes
- **The Left Pane (Log Tree)**
  - **Custom Views** – Saved filters
  - **Windows Logs** – The main logs: Application, Security, etc
  - **Applications and Services Logs** – Logs of specific applications and Windows components
  - **Saved Logs** – Log files that were opened from disk
  - **Subscriptions** – Collecting events from other computers
- **Middle pane (Event list)** – All events in the selected log, with columns: Keywords, Date and Time, Source, Event ID, Task Category. Below it, a preview pane shows the details of the selected event
- **Right pane (Actions)** – Options for analysing the log: Filter Current Log, Find, Save, Create Custom View, Clear Filter, etc.

### Event Details
Double-clicking an event opens **Event Properties**, which has two tabs:
- **General** tab – rendered, human-readable data
- **Details** tab – **Friendly View** (tree) or **XML View** (the raw event structure)

When reading an event, the key fields are **who, where, when and what**: User, Computer, Logged, and the event content.

Main fields:
- **Description** – Detailed information about the activity (for example, "An account was successfully logged on")
- **Log Name** – The log file the event belongs to (e.g. Security)
- **Source** – The component that wrote the event (e.g. Microsoft Windows security auditing)
- **Event ID** – A unique identifier for a specific type of activity
- **Logged** – When the activity occurred
- **Level** – The event type (Information, Warning, Error...)
- **Task Category** – The activity category (e.g. Logon, Logoff, User Account Management)
- **Keywords** – For Security events: **Audit Success** or **Audit Failure**
- **Computer** – The machine on which the event occurred

**Important:** Event IDs are **not unique**. Event ID 4103 means "Executing Pipeline" in the PowerShell log, but can mean something completely different for another provider.  
What identifies an event is the combination of **Provider + Event ID**.

### Subject vs. Target
Many security events record two accounts:
- **Subject** – *Who* performed the action
- **New Account / Target Account** – *Who the action was performed on*

Fields inside these sections:
- **Security ID (SID)** – The account's unique identifier; Windows tracks accounts by SID, not by name
- **Account Name** – The account's name
- **Account Domain** – The computer or domain the account belongs to
- **Logon ID** – Identifies the logon session in which the action happened. The same Logon ID in different events means the actions were performed in the **same session** – this lets me connect events together

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
  **Tip:** Filtering several IDs together (e.g. `4720,4722,4724`) shows related actions in one chronological list, which makes it easier to follow an attacker's sequence of actions.
- **Create Custom View** – saved filter that can cover multiple logs
- **Connect to Another Computer** – right-click *Event Viewer (Local)*

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

---

## Hands-on Investigation

### Scenario
A critical organisation was attacked, and data was exfiltrated from a file server. The security team identified the compromised system that had access to the file server.  
My task was to find out what the attacker did on this compromised system before accessing the file server.

### Investigation Steps
1. **Account creation (4720)** – Filtering the Security log on 4720 returned 3 events, all on 6/7/2024 (12:55:56 PM, 12:56:07 PM, 12:56:27 PM).  
   Sorting by time and opening the newest event, I found the last created account under **New Account → Account Name**: **`hacked`**.
2. **Who created it** – Under **Subject → Account Name** I found the account that performed the creation: **`Administrator`** (Logon ID `0x7333A`).
3. **Account enabled (4722)** – Filtering on 4722 and matching **Target Account → Account Name** to `hacked`, I found when the account was enabled.
4. **Password reset (4724)** – Filtering on 4724 showed an event at 6/7/2024 12:56:27 PM with **Target Account: `hacked`**, performed by **Administrator** in the same session (same Logon ID `0x7333A`). So the account's password was reset.

### What This Means
This sequence is a typical **persistence** technique – the attacker creates a backdoor account to keep access to the system:
- **Create** the account (4720)
- **Enable** it (4722)
- **Set a password** they control (4724)

The matching Logon ID across the events showed that all these actions were done in the same Administrator session, which helps build the attacker's timeline.

---

## Key Event IDs
| Event ID | Log | Meaning | When did he register? |  
|---|---|---|---|
| 104 | System | A log was cleared | When a log other than Security (for example System or Application) is cleared, via the Clear Log button in Event Viewer or with wevtutil cl |
| 2004 | Firewall | rule added | rule was added |
| 2005 | Firewall | rule changed | rule was changed |
| 2006 | Firewall | rule deleted | One rule was deleted. The event displays the name of the deleted rule, so it is clear exactly what was removed |
| 2033 | Firewall | all rules were deleted | All rules were deleted from the firewall settings at once. This happens, for example, when resetting the firewall to its default settings |
| 400 | PowerShell | PowerShell engine started (check EngineVersion) | Every time a PowerShell session starts, for example when powershell.exe is opened or a script is run. An EngineVersion of 2.0 can indicate a downgrade attack to avoid newer logging | 
| 800 | PowerShell | Pipeline execution details | When a PowerShell command (pipeline) is executed, mainly when Module Logging is enabled |
| 4103 | PowerShell | Module logging | When a command from a PowerShell module runs and Module Logging is enabled (via Group Policy) |
| 4104 | PowerShell | Script block logging | When a block of PowerShell code runs and Script Block Logging is enabled. In PowerShell 5 and later, code that looks suspicious is logged automatically even when it is not enabled |
| 1100 | Security | Event logging service shut down | When the Event Log service stops. This is normal during a system shutdown; at any other time it may mean someone stopped logging on purpose |
| 1101 | Security | Audit events dropped | When security events could not be recorded, for example when too many events arrived at once and the queue overflowed. Some activity is missing from the log |
| 1102 | Security | Security log was cleared | When the Security log is cleared, via the Clear Log button in Event Viewer or with wevtutil cl Security |
| 4608 | Security | Windows is starting up | During system boot |
| 4624 | Security | A user account successfully logged in | On every successful logon, whether local, over the network, via RDP, by unlocking the computer, or by a service. The Logon Type field shows which |
| 4625 | Security | A user account failed to log in | On every failed logon, for example a wrong password, a username that does not exist, or a disabled account. Many in a short time can indicate brute force |
| 4634 | Security | A user account successfully logged off | When a logon session ends. It shares the same Logon ID as the matching **4624** |
| 4688 | Security | A new process was created | Every time a new process starts, when Audit Process Creation is enabled |
| 4719 | Security | Audit policy was changed | When the audit policy is changed, for example with auditpol or in secpol.msc. An attacker may do this to stop certain events from being logged |
| 4720 | Security | A user account was created | When a new user account is created, for example with net user <name> <password> /add or in Local Users and Groups |
| 4722 | Security | A user account was enabled | When a disabled account is enabled. It also appears right after **4720** when a new account is created |
| 4724 | Security | Attempt to reset an account's password | When someone sets a new password for another account without knowing the old one. It also appears right after **4720** when an account is created with a password |
| 4725 | Security | A user account was disabled | When an account is disabled, so it can no longer log on, but it still exists |
| 4726 | Security |A user account was deleted | When an account is deleted completely |
| 4738 | Security | A user account was changed | When account properties change, for example "password never expires" or the account expiry date. It also appears right after **4720** when an account is created |
| 4798 | Security | A user's local group membership was enumerated | When a process lists which local groups a user belongs to, for example with net user <name> or by opening the user in Local Users and Groups |
| 4799 | Security | A local group membership was enumerated | When a process lists the members of a local group, for example with net localgroup administrators. System processes also trigger it regularly, so it is noisy |
| 4672 | Security | Special privileges assigned to new logon | Immediately following event **4624**, when an account with such privileges connects. In practice, this is typically an Administrator account or a system account, such as SYSTEM |
| 4800 | Security | The computer was locked | When the user locks the computer, either via Win + L or through an automatic lock after a period of inactivity |
| 4801 | Security | The computer has been unlocked | When the user returns and enters a password to unlock |
| 6005 | System | Event log service started | at every system boot, when the Event Log service starts, so it marks a startup |
| 6008 | System | previous shutdown was unexpected | at boot, when the previous shutdown was not clean, for example a power cut, a crash or holding the power button |
| 7045 | System | A new service was installed | When a new service is installed, for example with sc create, by a software installer, or by tools such as PsExec. A common sign of persistence |

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
- Windows separates its logs into categories, and the **Security** log is the most important one for investigations
- Event Viewer provides a graphical way to view, sort and filter logs
- wevtutil and Get-WinEvent are good for automation and large datasets
- Account creation, enabling and password reset in a short period of time can indicate an attacker setting up **persistence**
- FilterHashtable and XPath filter events efficiently while retrieving them
- Important logging (PowerShell, process command lines) must be **enabled** – visibility gaps are what attackers exploit
- Attackers try to blind defenders by clearing logs, disabling logging, or downgrading PowerShell – and each of these leaves its own evidence
- Knowing **what to look for** (NSA guide, MITRE ATT&CK, cheat sheets) is as important as knowing how to search
- This room is a foundation for Windows Internals, Sysmon and SIEM tools such as Splunk
