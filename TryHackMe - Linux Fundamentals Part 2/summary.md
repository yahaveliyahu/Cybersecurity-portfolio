# TryHackMe – Linux Fundamentals Part 2 (Summary)

This document summarizes what I learned and practiced in the **Linux Fundamentals Part 2** room on TryHackMe.

This part expands on basic Linux usage and focuses on:

- File and directory management

- Hidden files and permissions
  
- Switching users
  
- Important Linux system directories
  
- Using Linux documentation
  
- Remote access using SSH
  
---

## 📁 Viewing Files and Hidden Files

### ls — List Directory Contents
By default, the `ls` command lists visible files and directories in the current directory.

```bash
ls
```

Hidden files are **not displayed** by default.

### ls -a / --all — Show Hidden Files
Hidden files and directories in Linux start with a dot (`.`).

```bash
ls -a
```

This displays:

- Hidden configuration files (e.g. `.bashrc`, `.profile`)

- Hidden folders

- Special directories such as: `.` (current directory) and `..` (parent directory)

Hidden files are typically **used for user and system configuration**, not for security.

---

## 📘 Using Documentation in Linux

### man — Manual Pages
Linux provides built-in documentation for almost every command using the `man` command.

```bash
man ls
```

Manual pages include:

- NAME

- SYNOPSIS

- DESCRIPTION

- OPTIONS

This is the primary way professionals learn and understand commands in depth.

### --help — Quick Help

Most commands also support a `--help` flag.

```bash
ls --help
```

This provides a short summary of available options and usage examples.

---

## 📂 Creating Files and Directories

### touch — Create an Empty File

```bash
touch note
```

Creates an empty file.
It does **not** add any content.

### mkdir — Create a Directory

```bash
mkdir mydirectory
```

Creates a new directory.
Directories are special file types in Linux.

---

## 📄 Copying, Moving, and Renaming Files

### cp — Copy Files

```bash
cp note note2
```

Creates a copy of the file with the same contents.

To copy directories recursively:

```bash
cp -R folder1 folder2
```

### mv — Move or Rename Files

```bash
mv note2 note3
```

The `mv` command is used both to:

- Rename files

- Move files between directories

---

## ❌ Removing Files and Directories

### rm — Remove Files

```bash
rm note
```

Deletes a file permanently.

### rm -R — Remove Directories

```bash
rm -R mydirectory
```

Deletes a directory and all its contents recursively.

⚠️ **There is no undo or recycle bin in Linux CLI.**

---

## 🧠 Identifying File Types

### file — Determine File Contents
File extensions are not reliable in Linux.

```bash
file note
```

Example output:

```bash
ASCII text
```

This command inspects the actual file content to determine its type.

---

## 🔐 Understanding File Permissions

Using the long listing format shows file permissions:

```bash
ls -l
```

Example permission string:

```bash
-rwxr-xr--
```

### Permission Structure
- First character:

- `-` regular file

- `d` directory

- Next three groups define permissions for:

1. Owner

2. Group

3. Others

Each group may include:

- `r` — read

- `w` — write

- `x` — execute

### Permission Meanings
- **Read** — view file contents or list directory contents

- **Write** — modify files or directories

- **Execute** — run a file or access a directory

Permissions determine **who can access what** on a Linux system.

---

## 📏 Human-Readable File Sizes

By default, file sizes are displayed in bytes.

Using the `-h` option makes sizes easier to read:

```bash
ls -lh
```

This displays sizes using:

- K (kilobytes)

- M (megabytes)

- G (gigabytes)

---

## 👥 Switching Between Users

### su — Switch User

```bash
su user2
```

Switches to another user **without fully loading their environment**.
The working directory usually remains unchanged.

### su -l / --login — Full Login Shell

```bash
su -l user2
```

Simulates a real login:

- Switches to the user’s home directory

- Loads environment variables

- Applies user-specific configuration

This behavior is similar to logging in via SSH.

---

## 🏠 /root — Root User Home Directory

Unlike regular users whose home directories are under `/home/username`,
the **root user** has a dedicated home directory:

```bash
/root
```

Access to this directory is restricted and usually requires root privileges.

---

## 📦 /var — Variable Data Directory

The `/var` directory stores data that changes frequently.

Common contents:

- `/var/log` — system and service log files

- Application runtime data

- Temporary operational files

Log analysis often focuses heavily on **/var/log**.

---

## ⏳ /tmp — Temporary Directory

The `/tmp` directory is used for temporary files.

Key characteristics:

- Writable by all users

- Contents are cleared after reboot

- Used for short-term data storage

In penetration testing, `/tmp` is often used to store:

- Enumeration scripts

- Temporary tools

- Payloads

---

## /etc — System Configuration

The `/etc` directory contains system-wide configuration files.

Notable files:

- `/etc/passwd` — user accounts

- `/etc/shadow` — encrypted passwords

- `/etc/sudoers` — sudo permissions

This directory is critical for system security and administration.

---

## 🌐 Remote Access with SSH

### What is SSH?

**SSH (Secure Shell)** is an encrypted protocol used to access remote Linux machines.

It allows users to:

- Log into remote systems

- Execute commands securely

- Manage servers over a network

### SSH Connection Example

```bash
ssh user@IP
```

SSH is the standard method for:

- TryHackMe machines

- Servers

- Cloud infrastructure

- Cybersecurity environments

---

## 🧠 Key Takeaways

- Hidden files are configuration files, not protected files

- Linux documentation is accessed using `man` and `--help`

- File permissions control access to files and directories

- File extensions do not determine file type

- SSH is the primary method for accessing remote Linux systems securely

---

## 🎯 Why This Matters for Cybersecurity

These skills are essential for:

- Penetration testing

- SOC and Blue Team work

- Log analysis

- Server administration

- Remote system administration

- Working with security tools and servers
