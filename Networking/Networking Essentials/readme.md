# TryHackMe – Networking Essentials

This folder contains my personal notes and documentation from the **Networking Essentials** room on TryHackMe.

The room is the second in TryHackMe's four-part networking series and introduces the standard protocols and technologies that **glue a network together**: how devices configure themselves automatically, how they find each other on a local network, how packets travel across the Internet, and how many devices can share a single public IP address.

---

## 🌐 Topics Covered

- Layers and addressing: the difference between the data link layer (Ethernet, WiFi) and the Internet (IP)
- Ethernet frame structure (destination MAC, source MAC, EtherType)
- **DHCP** – automatic network configuration and the DORA process
- **ARP** – resolving IP addresses to MAC addresses on the local network
  - ARP Request / Reply structure
  - Encapsulation directly inside Ethernet frames
  - ARP cache and ARP spoofing risks
- **Routing** and common routing protocols (OSPF, EIGRP, BGP, RIP)
- **NAT** – sharing one public IP address between many private addresses
- **ICMP** and network diagnostics:
  - `ping` – connectivity testing and RTT statistics
  - `traceroute` – discovering the path to a host using TTL
- IPv4 header fields: TTL, Protocol, Identification, Flags (DF/MF) and fragmentation
- Packet analysis with **Wireshark**, **tshark** and **tcpdump**, including decoding raw bytes in hexadecimal

---

## 📂 Contents

- **summary.md** – A detailed summary of the protocols covered, packet capture analysis, and the insights gained while working through the room.
- **screenshots/** – Supporting screenshots of packet captures and command output (no flags or sensitive data included).

---

## 🎯 Purpose

This documentation is part of my **cybersecurity learning portfolio** and demonstrates:
- Understanding of core networking protocols (DHCP, ARP, NAT, ICMP)
- Understanding of how packets are encapsulated and routed across networks
- Hands-on practice reading packet captures down to the byte level
- Familiarity with common network troubleshooting tools (`ping`, `traceroute`, `tcpdump`, `tshark`)
- Awareness of security aspects of networking protocols (e.g. ARP spoofing, ICMP filtering)

---

## 🔗 Related Rooms
- Networking Concepts  
- (Future) Networking Core Protocols  
- (Future) Networking Secure Protocols
