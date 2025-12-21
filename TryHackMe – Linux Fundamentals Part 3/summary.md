# Linux Fundamentals Part 3 – Summary

This document summarizes what I learned and practiced in the  
**Linux Fundamentals Part 3** room on TryHackMe.

This part focuses on **processes, services, logs, scheduling, repositories, and secure file transfer**, providing insight into how Linux systems operate at runtime.

---

## 🔄 How Linux Processes Start

When a Linux system boots, the first user-space process that starts is:

- **systemd (PID 1)**

`systemd` is responsible for:
- Starting system services
- Managing background processes
- Handling boot-time execution

All other processes are **child processes** of `systemd`, either directly or indirectly.

---

## 🧱 Namespaces & Process Isolation

Linux uses **namespaces** to isolate processes and system resources such as:
- CPU
- Memory
- Process visibility

This isolation improves security and is the foundation of:
- Containers
- Sandboxing
- Process separation

Processes can only see other processes within the same namespace.

---

## 👀 Viewing Processes

### ps
Displays processes running in the current terminal session.

```bash
ps
```

### ps aux

Displays all processes on the system, including system services.

```bash
ps aux
```

Important columns:

- `PID` – process ID

- `USER` – process owner

- `STAT` – process state

- `COMMAND` – executed command

Each execution of `ps` creates a new process with a new PID.

---

## 📊 Real-Time Monitoring with top

```bash
top
```

Provides a live view of:

- CPU usage

- Memory usage

- Running processes

- System load

This is commonly used for troubleshooting and incident response.

---

## 🔪 Managing Processes & Signals
Processes are controlled using **signals**:

- `SIGTERM` – graceful termination

- `SIGKILL` – forced termination

- `SIGSTOP` – suspend process

```bash
kill PID
kill -9 PID
```

---

## ⏯️ Foreground & Background Processes
### Run in background

```bash
command &
```

### Suspend a process

```bash
Ctrl + Z
```

### Resume in foreground

```bash
fg
```

These techniques allow multitasking in a single terminal.

---

## ⚙️ Services & systemctl
Linux services are background programs managed by systemd.

```bash
systemctl start apache2
systemctl stop apache2
systemctl enable apache2
systemctl disable apache2
```

- `start` – run now

- `enable` – run automatically on boot

---

## 📜 Logs & System Monitoring
System and service logs are stored in:

```bash
/var/log
```

Common logs include:

- Apache: `access.log`, `error.log`

- `syslog`

- `fail2ban.log`

- `ufw.log`

### Log Rotation

Older logs are:

- Renamed (`.log.1`)

- Compressed (`.gz`)

to prevent disk exhaustion.

---

## ⏰ Scheduling Tasks with cron
The **cron** service runs scheduled tasks automatically.

### Editing cron jobs

```bash
crontab -e
```

### Cron format

```bash
MIN HOUR DOM MON DOW COMMAND
```

Example:

```bash
0 */12 * * * cp -R /home/user/Documents /var/backups/
```

Runs every 12 hours.

Wildcards (`*`) mean “any value”.

---

## 📦 Software Repositories & GPG Keys
Linux uses repositories to distribute software.

When adding third-party repositories:

- GPG keys verify software integrity

- Only trusted repositories are allowed

```bash
add-apt-repository
apt update
apt install
```

---

## 🌐 File Transfer & Networking
### wget

Downloads files over HTTP.

```bash
wget http://IP:8000/file.txt
```

### curl

Used to interact with web services and APIs.

### scp

Secure file transfer over SSH.

```bash
scp user@IP:/path/file.txt local.txt
```

### Python HTTP Server

```bash
python3 -m http.server
```

Quick way to serve files over the network.

---

## ✏️ Text Editing
### nano

Simple terminal text editor.

- `Ctrl + O` – save

- `Ctrl + X` – exit

### vim

Advanced editor available on almost all Linux systems.

---

## 🧠 Key Takeaways

- `systemd` is the core process manager

- Logs are essential for monitoring and security analysis

- Cron automates repetitive tasks

- Processes can be controlled via signals

- Linux heavily relies on services and background processes

- Secure software installation depends on trusted repositories

- File transfer and networking tools are fundamental skills

---

## 🎯 Why This Matters for Cybersecurity

Understanding processes, services, logs, and scheduling is critical for:

- SOC monitoring

- Incident response

- Threat hunting

- Server hardening

- Detecting malicious persistence

- Understanding attacker behavior
