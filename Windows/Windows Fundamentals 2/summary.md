# Windows Fundamentals 2 – Summary

## Overview
In this room, I continued exploring the **Windows operating system**, building on Windows Fundamentals 1.  
The focus was on the built-in administrative utilities that can be launched from **System Configuration (MSConfig)**, and on the different ways to reach them: the MSConfig Tools tab, the Run dialog (Win + R), the Command Prompt, or the Start Menu.

The lab machine was a **Windows Server 2019** instance running on **Amazon EC2** (hostname `THM-WINFUN2`), accessed over Remote Desktop.

---

## Windows vs. Windows Server
Windows Server shares the same core as Windows 10/11, but is built to **provide services to other machines** rather than serve a single user at a desk.

Key differences:
- Server **roles** such as Active Directory, DNS, DHCP, file sharing and IIS
- Processor scheduling tuned for **background services** instead of foreground programs
- Support for many concurrent users and much more hardware
- Fewer consumer features (no Store, and no Startup tab in Task Manager)
- Optional **Server Core** mode with no GUI, managed through PowerShell

Domain Controllers running Windows Server are prime targets for attackers, since controlling one means controlling the whole organisation.

---

## System Configuration (MSConfig)
**MSConfig** (`msconfig.exe`) is the command name; **System Configuration** is the tool's display name. It is used for advanced troubleshooting, mainly of startup issues, and requires local administrator rights.

The utility has five tabs:

### 1. General
Choose what loads at boot:
- **Normal startup** – all drivers and services
- **Diagnostic startup** – basic devices and services only
- **Selective startup** – choose whether to load system services and startup items

### 2. Boot
Define boot options for the operating system, such as Safe boot, No GUI boot, Boot log and the boot menu timeout.

### 3. Services
Lists every service configured on the system, running or stopped, along with its **Manufacturer**.  
A service is a special type of application that runs in the background.  
Sorting by Manufacturer revealed **PsShutdown**, listed under *Systems Internals*.

### 4. Startup
On Windows 10/11 this tab only redirects to Task Manager. On Windows Server, neither MSConfig nor Task Manager shows startup items.  
The room's suggested way to view user-level startup items on Server is the Startup folder itself:
- `shell:startup` – current user's Startup folder
- `shell:common startup` – Startup folder for all users

The Startup folder is only one autostart location. Programs can also launch through registry **Run / RunOnce** keys, **Scheduled Tasks** and **Services**. Tools such as **Autoruns** (Sysinternals) or `Get-CimInstance Win32_StartupCommand` give a fuller picture. Attackers prefer these less obvious locations for persistence.

### 5. Tools
A launcher for many other utilities. Selecting a tool shows its **Selected command**, which can be run from the Run dialog, the Command Prompt, or the **Launch** button.

---

## Tools and Their Commands

| Tool | Command |
|---|---|
| About Windows | `winver.exe` |
| Change UAC Settings | `UserAccountControlSettings.exe` |
| Windows Troubleshooting | `C:\Windows\System32\control.exe /name Microsoft.Troubleshooting` |
| Control Panel | `control.exe` |
| Computer Management | `compmgmt.msc` |
| System Information | `msinfo32.exe` |
| Event Viewer | `eventvwr.exe` |
| Task Scheduler | `taskschd.msc` |
| Resource Monitor | `resmon.exe` |
| Performance Monitor | `perfmon.exe` |
| Task Manager | `taskmgr.exe` |
| Internet Protocol Configuration | `C:\Windows\System32\cmd.exe /k %windir%\system32\ipconfig.exe` |
| Registry Editor | `regedt32.exe` (launches `regedit.exe`) |

The Internet Protocol Configuration command opens a Command Prompt and runs `ipconfig` inside it:
- `/k` (keep) – runs the command and **keeps** the window open so the output can be read
- `/c` (close) – runs the command and closes the window
- `%windir%` – an environment variable that resolves to the Windows folder, so the command works wherever Windows is installed

`About Windows` showed the license is registered to **Windows User**, the default name when none is entered during installation.

---

## Advanced System Settings
Opened through *View advanced system settings* → **System Properties** → **Advanced** tab.

### Performance Options
**Processor scheduling** decides who gets CPU priority:
- **Programs** – the foreground window gets priority (default on Windows 10/11)
- **Background services** – CPU time is shared more evenly with services (default on Windows Server)

**Virtual memory (page file)** is disk space Windows uses as an extension of RAM when physical memory fills up. It helps prevent slowdowns and crashes. The settings show:
- The drive where the page file is stored
- Initial and maximum size
- Whether Windows manages the size automatically (recommended)

### Startup and Recovery
When Windows hits a critical error such as a **Blue Screen of Death**, it can write a **crash dump**: a snapshot of memory at the moment of the crash, saved by default to `%SystemRoot%\MEMORY.DMP`. Administrators analyse it (for example with WinDbg) to find which driver or process caused the failure.

Dump types, from smallest to largest:
- **None** – nothing is saved
- **Small memory dump (256 KB)** – stop code, loaded drivers and basic process information
- **Kernel memory dump** – kernel and driver memory only
- **Automatic memory dump** – a kernel dump with the page file sized automatically (modern default)
- **Complete memory dump** – the entire contents of RAM

The dump is first written into the page file, so a page file that is too small or disabled can prevent it from being saved.  
From a security perspective, memory dumps can contain passwords, encryption keys and fileless malware. They are valuable for forensics (e.g. **Volatility**), and attackers target them too, most notably by dumping `lsass.exe` to extract credentials.

---

## User Account Control (UAC)
UAC settings (`UserAccountControlSettings.exe`) are controlled by a slider with four levels:
- **Always notify** – highest security; notifies for apps and user changes, and dims the desktop (Secure Desktop)
- **Notify for apps** – notifies only when apps make changes (default)
- **Notify without dimming** – same as above, without dimming the screen
- **Never notify** – notifications off (not recommended)

---

## Computer Management (compmgmt)
Computer Management has three main sections: **System Tools**, **Storage**, and **Services and Applications**.

### Task Scheduler
Creates and manages tasks that run automatically: at a set time, on a schedule, at logon/logoff, or once.  
Example from the lab: **npcapwatchdog** runs **at system startup** to make sure the Npcap service is configured to start at boot.  
Scheduled tasks are also a common attacker persistence mechanism, so they are worth reviewing during an investigation.

### Event Viewer
Shows the audit trail of activity on the computer, used for troubleshooting and investigation. It has three panes: log providers (left), events overview (middle) and actions (right).

The five event types:
- **Error** – a significant problem, such as a service failing to load
- **Warning** – not necessarily significant, but may indicate a future problem
- **Information** – a successful operation of an application, driver or service
- **Success Audit** – a successful audited security access attempt, such as a logon
- **Failure Audit** – a failed audited security access attempt

The standard logs under *Windows Logs*:
- **Application** – events logged by applications
- **Security** – logon attempts and resource access (file creation, opening, deletion)
- **System** – events from system components, such as driver failures
- **Custom logs** – logs created by individual applications

### Shared Folders
- **Shares** – folders others can connect to over the network
- **Sessions** – users currently connected to the shares
- **Open Files** – files and folders the connected users are accessing

Shares on the lab machine:
- **ADMIN$** → `C:\Windows` – remote administration share, used by tools such as PsExec
- **C$** → `C:\` – default administrative share of the whole drive
- **IPC$** – not a real folder; a channel for inter-process communication between machines (historically abused through anonymous *null sessions*)
- **sh4r3dF0Ld3r** → inside the Administrator profile – a manually created share pointing to a hidden folder

A share whose name ends in `$` is hidden from network browsing, but anyone who knows its name can still reach it. Manually created shares are among the first things a penetration tester checks, because they often contain sensitive files or have overly broad permissions.

### Other System Tools
- **Local Users and Groups** – `lusrmgr.msc`, covered in Windows Fundamentals 1
- **Performance Monitor (perfmon)** – real-time or logged performance data, local or remote
- **Device Manager** – view, configure and disable hardware

### Storage
- **Disk Management** – set up drives, extend or shrink partitions, assign drive letters
- **Windows Server Backup** – available on Server editions

### Services and Applications
**Services** shows each service's display name and status. Its Properties window reveals the actual service name, the executable path and the **Startup type**:
- **Automatic** – starts at boot
- **Manual** – starts only when triggered by a user or process
- **Disabled** – does not run

**WMI Control** configures Windows Management Instrumentation, which lets scripts (VBScript, PowerShell) manage Windows machines locally and remotely. The `WMIC` command-line tool is deprecated, and PowerShell has replaced it.

---

## System Information (msinfo32)
Displays a comprehensive, read-only view of the hardware, components and software environment.

- **System Summary** – OS, version, system name (`THM-WINFUN2`), manufacturer (Amazon EC2), processor, BIOS and more
- **Hardware Resources** – low-level resource assignments, not aimed at average users
- **Components** – installed hardware devices
- **Software Environment** – drivers, environment variables, network connections, running tasks, services and startup programs

The search bar at the bottom finds fields across categories. Searching *IP Address* under Components → Network → Adapter showed:
- **Microsoft Kernel Debug Network Adapter (kdnic)** – a virtual adapter for remote kernel debugging, normally inactive
- **Intel 82574L Gigabit Network Connection** – a NIC emulated by the virtualisation platform
- **Find Next** moves between adapters until the active one with an IP address is found

Each adapter lists its IP address, subnet, default gateway, DHCP details, MAC address and driver.

---

## Environment Variables
Environment variables are **name–value pairs** stored by the OS that any program can read. They let programs find system information without hardcoding it.

- **User variables** apply to a single user
- **System variables** apply to every user and need admin rights to change
- When both define the same variable (e.g. `Path`), Windows combines them

Notable variables:
- **ComSpec** – the command interpreter, `%SystemRoot%\system32\cmd.exe`
- **Path** – folders Windows searches for executables, in order
- **PATHEXT** – extensions treated as executable, which is why `notepad` works without `.exe`
- **TEMP / TMP** – temporary files folder
- **windir / SystemRoot** – the Windows installation folder
- **NUMBER_OF_PROCESSORS, OS, PROCESSOR_ARCHITECTURE** – basic system details

Reading them:
- CMD: `echo %TEMP%`, or `set` for all variables
- PowerShell: `$env:TEMP`, or `Get-ChildItem Env:`

Security relevance:
- **PATH hijacking** – a writable folder early in `Path` lets an attacker plant a malicious file named like a legitimate command
- **TEMP folders** – writable by every user, so malware often drops and runs files there
- **ComSpec** – an unusual value could redirect command execution to a malicious file
- **Reconnaissance** – `set` quickly reveals the username, hostname, architecture and interesting folders

---

## Resource Monitor (resmon)
Shows real-time, per-process usage of **CPU, Memory, Disk and Network**, with a matching tab for each and live graphs in the right-hand pane.

Key details:
- **PID** – unique ID of each running process
- **Status** – `Running` or `Suspended` (modern apps like `SearchUI.exe` are suspended when idle)
- **Threads** – how many tasks the process runs in parallel
- **Hard Faults** – data Windows had to fetch from the page file on disk
- Ticking a process filters every tab to show only its activity (files, network connections, etc.)

Processes seen on the lab machine:
- **System (PID 4)** – the Windows kernel
- **dwm.exe** – draws windows and visual effects
- **perfmon.exe** – Resource Monitor itself
- **amazon-ssm-agent.exe** – Amazon agent, revealing the machine runs on AWS
- **svchost.exe** – appears many times (see below)

### svchost.exe (Service Host)
Many Windows services are written as **DLLs**, which cannot run on their own. `svchost.exe` is a generic host process that loads and runs them.

- Older Windows versions grouped related services into shared svchost instances (e.g. `netsvcs`, `LocalServiceNetworkRestricted`)
- Since Windows 10 version 1703, on machines with more than 3.5 GB of RAM, each service typically runs in its own instance
- Separation improves **stability**, **security** (isolation and least privilege) and **diagnostics**
- `tasklist /svc /fi "imagename eq svchost.exe"` shows which services each instance hosts

### Process Masquerading
Any running malware must appear as a process, so attackers disguise it as a legitimate one. `svchost.exe` is a favourite because one more instance does not stand out.

Common tricks:
- Look-alike names: `svch0st.exe`, `scvhost.exe`, `lsasss.exe`
- The exact legitimate name, running from the wrong folder (e.g. `AppData\Temp` instead of `System32`)
- Official-sounding names that do not exist in Windows

How to verify a process:
- **File location** – real system processes live in `C:\Windows\System32`
- **Digital signature** – Microsoft binaries are signed
- **Parent process** – a genuine `svchost.exe` is started by `services.exe` (visible in **Process Explorer**)
- **VirusTotal** – check the file or its hash

Other warning signs: connections to unknown IPs, unexpected listening ports, constant 100% CPU (cryptominers) or heavy disk writes (ransomware).

---

## Command Prompt (cmd)
Before the GUI, the command line was the only way to interact with the OS. It is still powerful for gathering system information.

### Basic Commands
- `hostname` – prints the computer name (`THM-WINFUN2`)
- `whoami` – prints the logged-in user (`thm-winfun2\administrator`)
- `ipconfig` – IP address, subnet mask and default gateway
- `ipconfig /all` – detailed information, including MAC address, DHCP and DNS servers
- `<command> /?` – shows the help manual for a command
- `cls` – clears the screen

### netstat
Displays protocol statistics and current TCP/IP connections.

Output columns: **Proto**, **Local Address**, **Foreign Address**, **State**.  
On the lab machine, it showed my own Remote Desktop session: a connection on local port **3389** in the `ESTABLISHED` state.

| Flag | Purpose |
|---|---|
| `-a` | All connections and listening ports |
| `-n` | Addresses and ports as numbers (faster, no DNS lookups) |
| `-o` | Owning process ID (PID) of each connection |
| `-b` | Executable that created each connection (requires admin) |
| `-f` | Fully qualified domain names for foreign addresses |
| `-p proto` | Filter by protocol (TCP, UDP) |
| `-s` | Per-protocol statistics |
| `-e` | Ethernet (adapter) statistics |
| `-r` | Routing table |
| `-t` | Connection offload state |
| `-x` | NetworkDirect connections |
| `interval` | Repeat every X seconds until Ctrl+C |

The most useful combinations are `netstat -ano` and, with admin rights, `netstat -anob`.

Connection states: **LISTENING**, **ESTABLISHED**, **TIME_WAIT**, **CLOSE_WAIT**, **SYN_SENT**.  
For defenders, an unknown `ESTABLISHED` connection may indicate C2 communication, and an unknown `LISTENING` port may indicate a backdoor.

### net
Manages network resources through sub-commands such as `user`, `localgroup`, `share`, `session`, `use`, `start` and `stop`.
- `/?` does not work with `net`; use `net help` instead
- Example: `net help user` – `net user` creates and modifies user accounts, and lists them when run without switches

The full command reference is available at ss64.com.

---

## Windows Registry
The Registry is a **central hierarchical database** holding the configuration Windows constantly references: user profiles, installed applications and file associations, folder and icon settings, hardware and ports in use.

It is viewed and edited with the **Registry Editor** (`regedit`, or `regedt32.exe` as listed in MSConfig). In old Windows NT versions these were separate tools; today `regedt32` simply launches `regedit`.

Top-level keys:
- **HKEY_CLASSES_ROOT** – file associations and COM objects
- **HKEY_CURRENT_USER** – settings of the logged-in user
- **HKEY_LOCAL_MACHINE** – machine-wide settings
- **HKEY_USERS** – settings of all user profiles
- **HKEY_CURRENT_CONFIG** – the current hardware profile

Editing the registry can break normal operation, so it is meant for advanced users. It is also a key persistence location for attackers (e.g. `Run` keys).

---

## Related Concepts Explored

### SMB (Server Message Block)
The Windows protocol for sharing files, folders and printers over the network, on **port 445**.
- SMBv1 is outdated and insecure; SMBv2/v3 are modern (v3 supports encryption); **Samba** implements SMB on Linux
- **EternalBlue**, an SMBv1 vulnerability, was used by **WannaCry** in 2017
- Attackers use SMB for lateral movement and enumerate shares with tools like `smbclient` and `enum4linux`

### Sysinternals and PsTools
**Sysinternals** is a suite of free advanced Windows tools, acquired by Microsoft in 2006 (Process Explorer, Autoruns and more).  
**PsTools** is its set of command-line tools for local and remote administration:
- **PsExec** – run commands on remote machines
- **PsShutdown** – shut down, restart, lock or log off local and remote machines
- **PsList / PsKill** – list and kill processes
- **PsInfo, PsLoggedOn, PsService, PsLogList, PsPasswd**

PsShutdown examples:
- `psshutdown -r -t 0` – restart now
- `psshutdown \\PC-NAME -s -t 0 -u user -p pass` – shut down a remote machine
- `psshutdown -a` – abort a scheduled shutdown

It cannot power on a machine (that requires **Wake-on-LAN**). Remote use needs admin rights, SMB access and the `ADMIN$` share. The built-in `shutdown /r /m \\PC-NAME` and PowerShell `Restart-Computer` do the same job.

PsExec is a classic **dual-use tool**: administrators rely on it, and attackers use it for lateral movement because it is signed by Microsoft. Defenders look for the `PSEXESVC` service being installed.

### Npcap and Nmap
- **Npcap** – a packet capture driver that lets tools see raw network traffic; it replaced WinPcap, and its Linux/macOS counterpart is libpcap. It is required by **Wireshark** and **Nmap** on Windows
- **Nmap** – a network scanner that discovers live hosts, open ports, service versions and operating systems

Common Nmap scans:
- `nmap <target>` – top 1,000 ports
- `nmap -p- <target>` – all 65,535 ports
- `nmap -sV <target>` – service versions
- `nmap -O <target>` – OS detection
- `nmap -sn <subnet>` – host discovery only

Scanning is usually the first step of reconnaissance. It must only be done on systems I have explicit permission to scan.

---

## Key Takeaways
- MSConfig is a central launcher for many Windows administration tools, and each tool can also be opened directly by its command
- Windows Server differs from Windows client editions in its roles, defaults and missing consumer features
- Startup items, scheduled tasks, services and registry keys are all places where programs (and attackers) achieve persistence
- Event Viewer, Resource Monitor, netstat and msinfo32 provide the visibility needed for troubleshooting and incident response
- Many legitimate admin tools (PsExec, SMB shares, memory dumps) are also abused by attackers, so defenders must know what normal looks like
