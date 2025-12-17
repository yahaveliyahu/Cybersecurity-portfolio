# TryHackMe – Linux Fundamentals Part 2 (Summary)

This document summarizes what I learned and practiced in the **Linux Fundamentals Part 2** room on TryHackMe.
This part expands on basic Linux usage and focuses on file management, permissions awareness, documentation usage, hidden files, and remote access using SSH.

---

## 📁 Viewing Files and Hidden Files

### ls — List Directory Contents
By default, the **ls** command lists visible files and directories in the current directory.

```bash
ls
```

Hidden files are **not displayed** by default.

### ls -a / --all — Show Hidden Files
Hidden files and directories in Linux start with a dot (**.**).

```bash
ls -a
```

This displays:

- Hidden configuration files (e.g. **.bashrc**, **.profile**)

- Hidden folders

- Special directories such as **.** (current directory) and **..** (parent directory)

Hidden files are typically **used for user and system configuration**, not for security.

---

## 📘 Using Documentation in Linux

### man — Manual Pages
Linux provides built-in documentation for almost every command using the **man** command.

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

Most commands also support a **--help** flag.

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

The **mv** command is used both to:

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

## ⚙️ Important System Directories

### /etc — System Configuration

The **/etc** directory contains system-wide configuration files.

Notable files:

- **/etc/passwd** — user accounts

- **/etc/shadow** — encrypted passwords

- **/etc/sudoers** — sudo permissions

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

- Linux documentation is accessed using **man** and **--help**

- Files and directories are managed using simple but powerful commands

- File extensions do not determine file type

- SSH is the primary method for accessing remote Linux systems securely

---

## 🎯 Why This Matters for Cybersecurity

These skills are essential for:

- Penetration testing

- SOC and Blue Team work

- Log analysis

- Remote system administration

- Working with security tools and servers
