# Networking Concepts – Summary

This document summarizes the concepts learned in the  
**Networking Concepts** room on TryHackMe.

The focus of this room is understanding how computer networks operate from the physical level up to application-level protocols.

---

## 🧱 OSI Model

The OSI (Open Systems Interconnection) model is a conceptual framework that describes how network communication is structured.

### Layers (Bottom → Top)

1. **Physical Layer**
   - Physical transmission of bits
   - Electrical, optical, and wireless signals
   - Ethernet cables, fiber optics, Wi-Fi frequencies

2. **Data Link Layer**
   - Communication within the same network segment
   - MAC addresses
   - Ethernet (802.3) and Wi-Fi (802.11)
   - Frames contain source and destination MAC addresses

3. **Network Layer**
   - Logical addressing and routing
   - IP, ICMP, IPSec
   - Routers forward packets based on IP addresses

4. **Transport Layer**
   - End-to-end communication between applications
   - TCP and UDP
   - Port numbers identify services

5. **Session Layer**
   - Establishes and manages sessions
   - Synchronization and recovery
   - Examples: RPC, NFS

6. **Presentation Layer**
   - Data formatting and encoding
   - Compression and encryption
   - ASCII, Unicode, MIME

7. **Application Layer**
   - User-facing protocols
   - HTTP, FTP, DNS, SMTP, POP3, IMAP

---

## 🔄 TCP/IP Model Mapping

- OSI Layers 7–5 → Application Layer
- OSI Layer 4 → Transport Layer
- OSI Layer 3 → Internet Layer
- OSI Layers 2–1 → Link Layer

---

## 🌐 IP Addressing

- IPv4 uses 32 bits divided into four octets
- Each octet ranges from 0–255
- Every host on a network must have a unique IP

### Private IP Ranges (RFC 1918)

- `10.0.0.0 - 10.255.255.255 (10/8)`
- `172.16.0.0 - 172.31.255.255 (172.16/12)`
- `192.168.0.0 - 192.168.255.255 (192.168/16)`

Private IPs cannot be routed on the public Internet and require NAT.

---

## 📡 Routing

Routers operate at the network layer and forward packets based on destination IP addresses.

Packets may pass through multiple routers before reaching their final destination.

---

## 🔗 MAC Addresses

- 6-byte hardware identifiers
- Expressed in hexadecimal
- First 3 bytes identify the vendor (OUI)
- Used only within the local network segment

---

## 🚦 TCP vs UDP

### TCP
- Connection-oriented
- Reliable delivery
- Uses three-way handshake (SYN, SYN-ACK, ACK)
- Used by HTTP, HTTPS, SMTP, IMAP, POP3

### UDP
- Connectionless
- No delivery guarantee
- Faster, low overhead
- Used by DNS, streaming, VoIP

---

## 🔢 Ports

Ports identify the receiving process on a host.

- Range: 0–65535
- Port 0 is reserved

Common ports:
- 7 – Echo
- 13 – Daytime
- 21 – FTP
- 25 – SMTP
- 80 – HTTP
- 443 – HTTPS
- 110 – POP3
- 143 – IMAP

---

## 🌍 DNS

DNS resolves human-readable domain names into IP addresses.

Example:

`tryhackme.com → IP address`

---

## 🧪 Telnet as a Networking Tool

Telnet was used to manually connect to different services running on a remote host,
demonstrating how application-layer protocols behave over TCP connections.

Examples:

### Echo Server (Port 7)

Command used:

```bash
telnet 10.64.185.63 7
```

Observed behavior:

- The server echoes back any text sent by the client

- The connection remains open until manually closed

### Daytime Server (Port 13)

Command used:

```bash
telnet 10.64.185.63 13` 
```

Observed behavior:

- The server immediately returns the current date and time

- The connection is closed automatically by the server

### Web (HTTP) Server (Port 80)

Command used:

```bash
telnet 10.64.185.63 80
```

HTTP request sent manually after connecting:

```bash
GET / HTTP/1.1
Host: telnet.thm
```

Observed behavior:

- The server responds with an HTTP response

- Example headers include:

1. `HTTP/1.1 200 OK`

2. `Content-Type: text/html`

3. `Server: lighttpd/1.4.63`

- The server closes the connection after sending the response

---

## 📦 Encapsulation

Encapsulation describes how each layer adds its own header:

1. Application data
2. TCP/UDP header → segment/datagram
3. IP header → packet
4. Ethernet/Wi-Fi header and trailer → frame

The receiving system removes headers in reverse order.

---

## 🧠 Key Takeaways

- Networking is layered for clarity and scalability
- Each layer has a distinct responsibility
- IP identifies hosts; ports identify services
- MAC addresses work only locally
- TCP and UDP serve different communication needs
- Encapsulation explains how data travels across networks

---

## 🎯 Why This Matters for Cybersecurity

Understanding networking fundamentals is critical for:

- SOC monitoring
- Traffic analysis
- Firewall rule creation
- IDS/IPS detection
- Incident response
- Understanding attacker movement across networks


