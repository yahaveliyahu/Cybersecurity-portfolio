# TryHackMe – Linux Fundamentals Part 3

This folder contains my personal documentation and learning summary from the
**Linux Fundamentals Part 3** room on TryHackMe.

This part focuses on **how Linux systems actually operate behind the scenes**, including process management, services, logs, scheduled tasks, repositories, and secure file handling.

---

## 🧠 What I Learned
- How Linux processes start and how they are structured using parent–child relationships

- The role of `systemd` as the first user-space process (PID 1)

- Viewing, monitoring, and controlling running processes

- Understanding foreground and background processes

- Sending signals to processes and terminating them safely

- How system services work and how they start on boot

- Scheduling recurring tasks using `cron` and `crontab`

- Understanding cron time syntax, wildcards, and intervals

- How Linux manages system and application logs

- Analyzing Apache web server access and error logs

- Understanding log rotation and compressed log files

- Managing software packages and repositories

- Adding third-party repositories and verifying software using GPG keys

- Removing software and cleaning system packages

- Secure file transfer using SSH-based tools

- Serving files locally using a temporary HTTP server

- Working with hidden files and user configuration files

---

## 🛠️ Commands & Concepts Covered
### 🧩 Process Management

- `ps`, `ps aux` – viewing running processes and system-wide activity

- `top` – real-time monitoring of CPU and memory usage

- `kill` – sending signals to processes

- `SIGTERM`, `SIGKILL`, `SIGSTOP` – graceful termination, forced kill, and suspension

- `&` - run processes in the background

- `Ctrl + Z`, `fg` – suspend and resume foreground processes

---

### 🔧 Services & systemd

- `systemctl start service` – start a service manually

- `systemctl stop service` – stop a running service

- `systemctl enable service` – start a service automatically on boot

- `systemctl disable service` – prevent a service from starting on boot

- Understanding long-running services such as web servers and firewalls

---

### 📜 Logs

- `/var/log` – central directory for system and service logs

- Apache logs:

 1. `access.log` – records every HTTP request

 2. `error.log` – records server errors and misconfigurations

- Security-related logs:

1. `syslog` – general system messages

2. `fail2ban.log` – brute-force detection and bans

3. `ufw.log` – firewall activity

- Log rotation

`.log.1`, `.gz` – rotated and compressed logs

---

### 📅 Scheduling

- `cron` - background scheduling service

- `crontab -e` – editing user cron jobs

- Cron time fields (minute, hour, day, month, weekday)

- Wildcards (`*`) and step values (`*/12`)

- Automating backups and maintenance tasks

---

### 📦 Software & Repository Management

- `apt update`, `apt install`, `apt remove`

- `add-apt-repository` – managing software sources

- `/etc/apt/sources.list.d/` – third-party repository configuration

- `GPG keys` – verifying software authenticity

- Installing software not included in default repositories

- Safely removing software and repositories

### 🌐 Networking & File Transfer

- `wget` – downloading files over HTTP/HTTPS

- `curl` – interacting with web services and endpoints

- `scp` – secure file transfer over SSH

- `python3 -m http.server` – creating a temporary local web server

### ✏️ Text Editing & Configuration

- `nano` – basic terminal text editor

- Editing configuration files safely

- Understanding hidden files:

1. `.bashrc`

2. `.profile`

3. `.ssh/`

- Using `ls -a` to reveal hidden files

---

## 📂 Repository Structure

- **summary.md** – Detailed explanations of all concepts learned

- **screenshots/** – Terminal outputs and hands-on examples from the room

---

## 📸 Screenshots

- Viewing running processes using `ps` and `top`

- Managing background and foreground processes

- Apache access and error log analysis

- Inspecting `/var/log` and rotated log files

- Creating and editing cron jobs with `crontab -e`

- Managing services using `systemctl`

- Adding and removing software repositories

- Installing third-party software using GPG verification

- Transferring files securely with `scp`

- Serving files using a Python HTTP server

- Listing hidden configuration files with `ls -a`

---

## 🎯 Purpose

This documentation demonstrates hands-on Linux experience and a deeper understanding of how Linux systems function internally.

These skills are essential for:

- Cybersecurity (SOC / Blue Team / Pentesting)

- Linux system administration

- Incident response and log analysis

- Monitoring production systems

- Understanding attacker activity on Linux hosts

- Managing real-world Linux servers
