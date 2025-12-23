# Linux – Learning & Hands-On Practice

This folder contains my **Linux learning journey and hands-on practice**, based primarily on the **TryHackMe – Linux Fundamentals (Parts 1–3)** rooms, along with additional experimentation and exploration.

The documentation here reflects **practical Linux knowledge**, command-line experience, and system-level understanding that are essential for **cybersecurity, SOC, and system administration roles**

---

## 🧠 What I Know & Practiced
### 🖥️ Linux & CLI Fundamentals

- Understanding what Linux is and where it is used (servers, security, DevOps)

- Confident work in the Linux terminal (CLI)

- Navigating the filesystem and understanding directory structure

- Working with hidden files and configuration files

- Using built-in documentation (`man`, `--help`)

---

### 📁 Filesystem & File Management

- Creating, viewing, copying, moving, renaming, and deleting files/directories

- Identifying file types independently of extensions

- Understanding Linux directory roles:

1. `/etc` – system configuration

2. `/var` – logs and variable data

3. `/tmp` – temporary files

4. `/root` – root user home

---

### 🔐 Users & Permissions

- Understanding Linux file permissions (`r`, `w`, `x`)

- Interpreting permission strings (`ls -l`)

- Switching users and login environments (`su`, `su -l`)

- Understanding the role of root vs standard users

---

### ⚙️ Processes & System Internals

- Understanding Linux process structure and parent–child relationships

- Process monitoring and control:

1. Viewing running processes

2. Foreground vs background execution

3. Suspending and resuming processes

- Sending signals and safely terminating processes

- Understanding `systemd` and PID 1

---

### 🔧 Services & Startup

- Managing long-running services

- Starting, stopping, enabling, and disabling services

- Understanding how services start on boot

- Working with common services such as web servers

---

### 📜 Logs & Monitoring

- Understanding Linux logging architecture

- Working with `/var/log`

- Analyzing service logs (e.g. Apache)

- Security-related logs:

1. `syslog`

2. `fail2ban`

3. `ufw`

- Understanding log rotation and compressed logs

---

### 📅 Task Scheduling

- Automating tasks with `cron`

- Writing and editing cron jobs

- Understanding cron time syntax, wildcards, and intervals

- Using cron for maintenance and automation tasks

---

### 📦 Software & Package Management

- Installing, updating, and removing software with `apt`

- Managing repositories and third-party sources

- Understanding GPG keys and software verification

- Cleaning and maintaining system packages

---

### 🌐 Networking & File Transfer

- Downloading files via HTTP/HTTPS

- Secure file transfer over SSH

- Serving files locally using a temporary HTTP server

- Basic interaction with web endpoints from the CLI

---

### ✏️ Text Editing & Configuration

- Editing configuration files safely from the terminal

- Working with user configuration files:

1. `.bashrc`

2. `.profile`

3. `.ssh/`

---

## 🛠️ Commands & Tools Used

Some of the key commands and tools practiced include:

- `ls`, `cd`, `pwd`, `cat`, `echo`, `whoami`, `find`, `grep`

- `touch`, `mkdir`, `cp`, `mv`, `rm`, `file`

- `ps`, `top` (P, M and T), `kill`, `systemctl`

- `cron`, `crontab`

- `apt`, `add-apt-repository`

- `ssh`, `scp`, `wget`, `curl`

- `python3 -m http.server`

- `nano`, `vim`

---

## 📂 Repository Structure
linux/
├── linux-fundamentals-part1/
│   ├── summary.md
│   └── screenshots/
├── linux-fundamentals-part2/
│   ├── summary.md
│   └── screenshots/
├── linux-fundamentals-part3/
│   ├── summary.md
│   └── screenshots/
└── README.md  ← (this file)

Each subfolder contains:

- summary.md – detailed explanations of concepts learned

- screenshots/ – terminal outputs and hands-on examples

- Each subfolder includes its own README documenting the concepts, commands, and hands-on practice for that stage.

---

## 🎯 Purpose & Relevance

This Linux documentation demonstrates:

- Practical command-line proficiency

- Understanding of how Linux systems operate internally

- Experience with logs, processes, services, and permissions

---

## 🛡️ How This Knowledge Applies to SOC / Blue Team Work

The Linux skills practiced in this repository are directly applicable to **SOC** and **Blue Team** environments:

- **Log Analysis & Incident Investigation**
I can navigate and analyze Linux log files under `/var/log`, including service and security logs, to identify suspicious activity, errors, or signs of compromise.

- **Process Monitoring & Threat Detection**
Using tools like `ps`, `top`, and process signals, I can identify abnormal or malicious processes, understand their behavior, and take appropriate containment actions.

- **Service & System Awareness**
Understanding how services are managed with `systemd` allows me to verify whether critical services are running as expected or if an attacker has tampered with them.

- **User & Permission Auditing**
Knowledge of Linux users, permissions, and login environments helps detect privilege escalation, misconfigurations, or unauthorized access.

- **Secure Remote Access**
Experience with SSH and secure file transfer (`scp`) supports safe investigation and interaction with remote Linux servers in production environments.

- **Automation & Monitoring Support**
Familiarity with `cron` enables creating scheduled tasks for monitoring, maintenance, or log rotation checks.

Overall, this foundation allows me to confidently investigate Linux-based systems, respond to alerts, and support security operations in real-world environments.
