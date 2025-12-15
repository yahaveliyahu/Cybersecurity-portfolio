# TryHackMe – Linux Fundamentals – Part 1 (Summary)

In this room, I started my journey into **Linux**!

This document summarizes what I learned and practiced in the **Linux Fundamentals Part 1** room on TryHackMe.
The room focuses on understanding the Linux operating system, working with the terminal, navigating the filesystem, and using basic shell commands and operators.

---

## 🐧 Introduction to Linux

Linux is a lightweight and powerful operating system widely used in:
- Servers and cloud infrastructure
- Cybersecurity environments
- Embedded systems and IoT
- Android devices and supercomputers

Unlike Windows or macOS, many Linux systems operate **without a graphical user interface (GUI)** and rely heavily on the **terminal** for interaction.

---

## 💻 Working with the Terminal

The terminal is a text-based interface used to interact directly with the operating system.
Through the terminal, I learned how to:
- Navigate directories
- View files and folders
- Create files
- Output and redirect command results

Understanding the terminal is a fundamental skill for system administration and cybersecurity.

---

## 📂 Filesystem Navigation Commands

### ls — List Directory Contents
Displays files and folders in the current directory.

## bash
ls

This helped me understand what exists before navigating further.

---

### cd — Change Directory
Used to move between directories.

**bash**

cd Documents
cd /home/ubuntu/Documents

---

### pwd — Print Working Directory
Displays the full absolute path of the current directory.

```bash
pwd

This is useful for:
- Knowing exactly where you are in the filesystem
- Navigating using absolute paths

---

### cat — View File Contents
Outputs the contents of a file directly to the terminal.

# bash
cat welcome

---

### ✍️ Creating Files & Output Redirection

### echo Command
Used to print text to the terminal or redirect it to a file.

# bash
echo hey

---

### Output Redirection Operator >
Redirects command output into a file (overwrites existing content).

# bash
echo hey > welcome

If the file already exists, its contents are replaced.

---

### Append Operator >>
Adds output to the end of a file instead of overwriting it.

# bash
echo hello >> welcome

This results in:
hey
hello

---

### ⚙️ Shell Operators

### && — Conditional Command Execution
Runs the second command only if the first command succeeds.

# bash
command1 && command2

This is useful for chaining dependent commands safely.

---

### & — Run Command in Background
Executes a command in the background, allowing continued use of the terminal.

# bash
long_running_command &

This is helpful when working with time-consuming processes.

---

### 🧠 Key Takeaways

- Linux relies heavily on terminal-based interaction

- Understanding filesystem navigation is essential

- Output redirection is a powerful way to manipulate files

- Shell operators allow better command control and automation

- These skills form the foundation for later cybersecurity and system-level tasks

  ---

### 🎯 Why This Matters for Cybersecurity

These Linux fundamentals are critical for:

- Penetration testing

- Server administration

- Log analysis

- Script automation

- Working with security tools in real environments

Mastering these basics is a necessary step toward more advanced Linux and security concepts.
