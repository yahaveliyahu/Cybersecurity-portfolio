# TryHackMe – Networking Core Protocols

This folder contains my personal notes and documentation from the **Networking Core Protocols** room on TryHackMe.

The room is the third in TryHackMe's networking series, and it explores the core protocols behind everyday internet activity: **resolving domain names, browsing the web, transferring files, and sending and receiving email**. The focus is on how these protocols work behind the graphical interfaces, by communicating with servers directly and analysing the captured traffic.

---

## 🌐 Topics Covered

- **DNS** – resolving domain names to IP addresses
  - Record types: A, AAAA, CNAME, MX
  - Querying with `nslookup` and analysing DNS packets with `tshark`
- **WHOIS** – domain registration records, registrars, IANA, and privacy protection services
- **HTTP** – request methods, headers, and status codes
  - Sending raw HTTP requests manually with `telnet`
- **FTP** – file transfer over separate control and data connections
  - Anonymous login, Passive Mode, and ASCII vs. binary transfer
- **SMTP** – sending email, ESMTP extensions, and envelope vs. header addresses
- **POP3** – downloading email and reading server-added headers
- **IMAP** – synchronising email across devices, folders, flags, and command tags
- Status codes across protocols (HTTP, FTP, SMTP, POP3, IMAP)
- Banner grabbing and the security risk of exposed software versions
- Cleartext protocols and their encrypted alternatives
- Default port numbers for each protocol

---

## 📂 Contents

- **summary.md** – A detailed summary of the protocols covered, their commands, traffic analysis, and security implications.
- **screenshots/** – Supporting screenshots of terminal sessions (`telnet`, `ftp`, `whois`) and Wireshark traffic captures (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of how core application-layer protocols work under the hood
- Hands-on experience interacting with services manually using `telnet` and `ftp`
- Ability to analyse network traffic with Wireshark and `tshark`
- Awareness of the security risks of cleartext protocols and information disclosure through service banners
- Building a solid networking foundation for security-oriented roles

---

## 🔗 Related Rooms
- Networking Concepts
- Networking Essentials
- (Next) Networking Secure Protocols
