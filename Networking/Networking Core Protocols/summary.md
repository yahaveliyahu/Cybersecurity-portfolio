# Networking Core Protocols – Summary

## Overview
In this room, I explored the core protocols that power everyday internet activity: **DNS**, **WHOIS**, **HTTP**, **FTP**, **SMTP**, **POP3**, and **IMAP**.  
This is the third room in TryHackMe's networking series (after Networking Concepts and Networking Essentials, and before Networking Secure Protocols).

The main goal was to understand how these protocols work **behind the graphical interfaces** — what a browser, an FTP client, or an email client actually sends over the network. I did this by talking to servers directly with `telnet`, and by examining captured traffic in Wireshark and `tshark`.

---

## 1. DNS – Resolving Names to Addresses
The **Domain Name System (DNS)** maps human-readable domain names to IP addresses, like a phone book for the internet.

- Operates at the Application Layer (Layer 7 of the OSI model)
- Uses **UDP port 53** by default, with **TCP port 53** as a fallback

### Common DNS Record Types
- **A** – maps a hostname to an IPv4 address
- **AAAA** – maps a hostname to an IPv6 address
- **CNAME** – maps a domain name to another domain name (alias)
- **MX** – specifies the mail server responsible for a domain's email

A browser looks up the **A** record to load a website, while a mail server looks up the **MX** record to deliver email.

### Practice: `nslookup`
```
nslookup www.example.com
```
- `Server: 127.0.0.53` – the local DNS stub resolver on Linux (`systemd-resolved`), which forwards the query to a real DNS server
- `#53` – the DNS port
- **Non-authoritative answer** – the answer came from an intermediate resolver or cache, not from the domain's authoritative name server (this is normal)
- The output returned both an IPv4 (A) and an IPv6 (AAAA) address

### Practice: Analysing DNS Traffic with `tshark`
```
tshark -r dns-query.pcapng -Nn
```
- `-r` reads packets from a saved capture file
- `-N n` enables network address resolution

The single `nslookup` produced **four packets**: an A query, its response, an AAAA query, and its response.  
Each query and its response share the same **Transaction ID** (e.g. `0x2e0f`), which is how the client matches answers to questions.

---

## 2. WHOIS – Who Owns a Domain
Whoever registers a domain gains the authority to set its DNS records. **WHOIS** records provide public information about the registration.

```
whois twitter.com
```

### Key WHOIS Fields
- **Registrar** – the company the domain was registered through (e.g. GoDaddy, CSC Corporate Domains)
- **Registrar IANA ID** – the registrar's official identifier
- **Creation / Updated / Expiration Date** – the domain's registration timeline
- **Abuse Contact** – where to report malicious use of the domain
- **Domain Status** – locks such as `clientTransferProhibited` and `serverDeleteProhibited`, which protect against domain hijacking
- **Registrant / Admin / Tech contacts** – owner and contact details
- **Name Servers** – the DNS servers responsible for the domain

### IANA
The **Internet Assigned Numbers Authority** manages globally unique internet identifiers:
- The DNS root zone (top-level domains like `.com` and `.il`)
- Allocation of IP address blocks to regional registries
- Protocol numbers, including well-known ports (53 for DNS, 80 for HTTP, 443 for HTTPS)

### Privacy Protection
Many domains hide the owner's details using a privacy service. For example, **Domains By Proxy** (GoDaddy's service) replaces the real registrant details with `Registration Private`.

### Security Relevance
WHOIS is a basic **OSINT** tool. The **creation date** is especially useful when analysing suspicious domains — a domain created a few days ago that imitates a bank is a strong phishing indicator.

---

## 3. HTTP – Accessing the Web
**HTTP (Hypertext Transfer Protocol)** defines how browsers communicate with web servers. It runs over TCP.

- **HTTP** – port 80 (unencrypted)
- **HTTPS** – port 443 (encrypted)
- Less common: 8080 and 8443

### Common HTTP Methods
- `GET` – retrieve data (an HTML page, an image)
- `POST` – submit new data (a form, an uploaded file)
- `PUT` – create or overwrite a resource
- `DELETE` – delete a resource

### HTTP Request and Response
Using Wireshark's **Follow HTTP Stream** (client in red, server in blue), I saw that a lot of information is exchanged that the browser never displays:

**Request headers** include `Host`, `User-Agent` (browser and OS), `Accept`, and `Accept-Encoding`.  
**Response headers** include `Server` (e.g. `nginx/1.18.0 (Ubuntu)`), `Content-Type`, `Last-Modified`, and `ETag`.

An empty line separates the headers from the body (the HTML).

### Practice: Talking HTTP with `telnet`
```
telnet MACHINE_IP 80
GET /flag.html HTTP/1.1
Host: MACHINE_IP

```
- Port **80** must be specified — without it, telnet connects to port 23 (the remote login service)
- The **empty line** at the end tells the server the request is complete
- The hidden flag was inside the raw HTML, not visible in the rendered page

---

## 4. FTP – Transferring Files
**FTP (File Transfer Protocol)** is designed specifically for transferring files and can be faster than HTTP for this purpose.

### Common FTP Commands
- `USER` – username
- `PASS` – password
- `RETR` – download a file from the server
- `STOR` – upload a file to the server

### Two Separate Connections
- **Control connection** – TCP port 21, for commands and responses
- **Data connection** – a new connection for each transfer (directory listing or file)

In **Passive Mode**, the server tells the client which port to connect to:
- `227 Entering Passive Mode (10,10,41,192,134,10)` → IP `10.10.41.192`, port `134 × 256 + 10 = 34314`
- `229 Entering Extended Passive Mode (|||49361|)` → the newer format, which gives the port directly

### Client Commands vs. Protocol Commands
The `ftp` client translates user-friendly commands into protocol commands:

| Typed in client | Sent to server |
|---|---|
| `ls` | `PASV` + `LIST` |
| `get file.txt` | `PASV` + `RETR file.txt` |
| `type ascii` | `TYPE A` |
| `quit` | `QUIT` |

In Wireshark, the control stream does **not** contain the file contents — those travel in separate TCP streams.

### Transfer Modes
- **Binary** – transfers the file exactly as it is (default)
- **ASCII** – adjusts line endings for text files between operating systems

### Practice: Anonymous FTP
```
ftp MACHINE_IP
```
- Logged in with username `anonymous` and an empty password
- Listed the files with `ls`, downloaded `flag.txt` with `get flag.txt`, and read it with `cat flag.txt`

An FTP server that allows **anonymous login** is an important security finding.

---

## 5. SMTP – Sending Email
**SMTP (Simple Mail Transfer Protocol)** defines how a mail client sends email to a server, and how mail servers pass email between each other. It listens on **TCP port 25**.

### SMTP Commands
- `HELO` / `EHLO` – start the session
- `MAIL FROM` – sender's address
- `RCPT TO` – recipient's address
- `DATA` – start the email content
- `.` on a line by itself – end of the message
- `QUIT` – end the session

### ESMTP
**Extended SMTP** adds extensions to the original protocol. When the client sends `EHLO` instead of `HELO`, the server lists the extensions it supports, such as:
- `STARTTLS` – upgrade the connection to an encrypted one
- `AUTH` – authentication with username and password
- `SIZE`, `8BITMIME`, `PIPELINING`

### Envelope vs. Headers
- **Envelope** (`MAIL FROM`, `RCPT TO`) – used by servers to deliver the message
- **Headers** (`From:`, `To:`, `Subject:`) – what the recipient sees in their mail client

Nothing forces these to match. On a server without authentication, any sender address can be written — this is the basis of **email spoofing**. Mechanisms such as **SPF**, **DKIM**, and **DMARC** were developed to counter it.

---

## 6. POP3 – Receiving Email
**POP3 (Post Office Protocol v3)** lets a client download email from its mailbox on the server. It listens on **TCP port 110**.

If SMTP is handing a letter to the post office, POP3 is checking your mailbox.

### POP3 Commands
- `USER <username>` / `PASS <password>` – log in
- `STAT` – number of messages and total size
- `LIST` – each message and its size
- `RETR <number>` – retrieve a message
- `DELE <number>` – mark a message for deletion
- `QUIT` – end the session and apply changes

POP3 has no numeric codes — responses are only **`+OK`** (success) or **`-ERR`** (error).

### Server-Added Headers
The retrieved message contained headers added by the server, not by the sender:
- `Return-path` and `Envelope-to` – the envelope addresses
- `Delivery-date` – when the message was delivered
- `Received` – the path the message took, including the **real sender IP**

The `Received` header is a key tool in phishing investigations, because it reveals the true origin even when `From:` is forged.

---

## 7. IMAP – Synchronising Email
**IMAP (Internet Message Access Protocol)** keeps email **on the server** and synchronises it across multiple devices. It listens on **TCP port 143**.

### POP3 vs. IMAP
| | POP3 | IMAP |
|---|---|---|
| Messages | Usually downloaded and deleted | Kept on the server |
| Folders | Single mailbox | Multiple folders |
| Flags | None | Read, flagged, deleted, etc. |
| Multiple devices | Hard to sync | Fully synchronised |

### IMAP Commands
- `LOGIN <username> <password>` – authenticate
- `SELECT <mailbox>` – choose a folder (e.g. `inbox`)
- `FETCH <number> <item>` – e.g. `FETCH 4 body[]` retrieves the full fourth message
- `MOVE` / `COPY` – move or copy messages to another folder
- `LOGOUT` – end the session

### Tags
Each client command starts with a **tag** (`A`, `B`, `C`, `D`), and the server returns the same tag in its final response. Lines starting with `*` are additional (untagged) information.  
Results are `OK`, `NO`, or `BAD`.

The server's `CAPABILITY` list (like `STARTTLS`, `AUTH=PLAIN`, `IDLE`) shows what it supports. `{445}` in a `FETCH` response tells the client exactly how many bytes follow.

---

## Status Codes Across Protocols
HTTP, FTP, and SMTP use three-digit codes, where the **first digit** indicates the type of response:

| First digit | Meaning |
|---|---|
| **2xx** | Success (`200` in HTTP, `250` in SMTP, `226`/`230` in FTP) |
| **3xx** | SMTP/FTP: waiting for more input (`354`, `331`); HTTP: redirection |
| **4xx** | SMTP/FTP: temporary failure; HTTP: client error (e.g. `404`) |
| **5xx** | SMTP/FTP: permanent failure; HTTP: server error |

POP3 uses `+OK` / `-ERR`, and IMAP uses tagged `OK` / `NO` / `BAD`.

---

## Banner Grabbing
Many servers reveal information about themselves when a connection opens:
- `nginx/1.18.0 (Ubuntu)` – web server
- `vsFTPd 3.0.5` – FTP server
- `Exim 4.95 Ubuntu` – SMTP server
- `Dovecot (Ubuntu)` – POP3/IMAP server

Collecting this information is called **Banner Grabbing**, and it is part of the reconnaissance phase.  
Knowing the exact software version lets an attacker search for known vulnerabilities (**CVEs**) in databases such as NVD and Exploit-DB (`searchsploit`).

**Defence:** keep software up to date. Hiding version banners makes reconnaissance harder, but it is only an extra layer (security through obscurity), not a replacement for patching.

---

## Cleartext Protocols – The Main Security Lesson
Every protocol in this room sends data **unencrypted** in its basic form.  
In the Wireshark captures, the passwords in the POP3 `PASS` and IMAP `LOGIN` lines were visible and had to be blurred in the screenshots.

Anyone capturing the traffic can read:
- Usernames and passwords
- Commands and server responses
- Full web pages, files, and email contents

The encrypted alternatives:
- HTTP → **HTTPS** (443)
- FTP → **SFTP** / **FTPS**
- SMTP → **STARTTLS**
- POP3 → **POP3S** (995)
- IMAP → **IMAPS** (993)
- Telnet → **SSH** (22)

---

## Default Ports Summary

| Protocol | Transport Protocol | Default Port |
|---|---|---|
| TELNET | TCP | 23 |
| DNS | UDP or TCP | 53 |
| HTTP | TCP | 80 |
| HTTPS | TCP | 443 |
| FTP | TCP | 21 |
| SMTP | TCP | 25 |
| POP3 | TCP | 110 |
| IMAP | TCP | 143 |

---

## Key Takeaways
- Text-based protocols are simply **commands and responses** following agreed rules — clients just hide them behind a friendly interface
- `telnet` can talk to any TCP service directly, which is useful for troubleshooting and learning
- Wireshark's **Follow Stream** reveals the full exchange between client and server
- DNS and WHOIS are fundamental reconnaissance and investigation tools
- Server banners leak software versions that can be matched to known vulnerabilities
- Email can be spoofed, but headers like `Received` reveal its real origin
- Cleartext protocols expose credentials and data — encryption is essential, which leads to the next room: **Networking Secure Protocols**
