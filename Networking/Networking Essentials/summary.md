# Networking Essentials – Summary

## Overview
In this room, I explored the protocols and technologies that "glue" a network together and make everyday Internet use possible.  
The room focused on how devices configure themselves automatically, how they find each other on a local network, how packets are routed across the Internet, and how many devices can share a single public IP address.

This is the second room in TryHackMe's four-part networking series:
- Networking Concepts
- **Networking Essentials** (this room)
- Networking Core Protocols
- Networking Secure Protocols

Topics covered:
- DHCP (Dynamic Host Configuration Protocol)
- ARP (Address Resolution Protocol)
- Routing and routing protocols
- NAT (Network Address Translation)
- ICMP – `ping` and `traceroute`

Throughout the room, I also practised reading packet captures with **Wireshark**, **tshark** and **tcpdump**, including decoding raw bytes in hexadecimal.

---

## Background: Layers and Addressing

Before diving into the protocols, it was important to clearly separate the layers involved:

| Layer | Role | Examples | Addressing |
|---|---|---|---|
| Application | What the application wants to do | HTTP, DNS | Names (e.g. google.com) |
| Transport | Communication between programs | TCP, UDP | Ports |
| Internet / Network | Routing between networks, end to end | IP, ICMP | IP addresses |
| Data Link | Delivery between two neighbouring devices | Ethernet, WiFi | MAC addresses |
| Physical | Electrical signals, light, radio waves | Cables, antennas | — |

Key points:
- **Ethernet (IEEE 802.3)** and **WiFi (IEEE 802.11)** are the two common **data link layer** technologies. WiFi is *not* the Internet – it is only a wireless way to reach the local router (which is why a device can be "connected, no Internet").
- The **Internet** is a network of networks, glued together by **IP** at the network layer. In the TCP/IP model, this layer is literally called the *Internet layer*.
- A **MAC address** is a 48-bit number (6 bytes), shown in hexadecimal, e.g. `44:df:65:d8:fe:6c`.
- The **IP address stays the same** from source to destination, while the **data link header is rebuilt at every hop** (each router strips the old frame and creates a new one for the next link).

### The Ethernet Frame Header
Every Ethernet frame starts with a 14-byte header:
- **Destination MAC** (6 bytes) – comes first so the network card can quickly decide "is this for me?"
- **Source MAC** (6 bytes)
- **Type / EtherType** (2 bytes) – tells the receiver what is inside:
  - `0x0800` – IPv4
  - `0x0806` – ARP
  - `0x86DD` – IPv6

### Interesting Observations from the Captures
- A capture of **WiFi traffic still showed "Ethernet II"** headers. In normal capture mode, the WiFi driver translates 802.11 frames into Ethernet-style frames before passing them to the operating system. Seeing the real 802.11 headers requires **Monitor Mode**.
- **Randomised MAC addresses:** the second-lowest bit of the first byte marks a *locally administered* address (created by software, not burned in by the manufacturer). For example, `5a` = `0101 1010` and `02` = `0000 0010` both have this bit set. Phones and laptops use random MACs per WiFi network to prevent tracking.
- The lowest bit of the first byte marks **multicast/broadcast** – e.g. `ff:ff:ff:ff:ff:ff` is the broadcast address.

---

## DHCP – Automatic Network Configuration

To use a network, a device needs at least:
- An **IP address** and **subnet mask**
- A **gateway** (router) to reach other networks
- A **DNS server** to resolve domain names

Servers are usually configured manually because they stay in one place and others expect to find them at fixed addresses. For laptops and phones, **DHCP** configures all of this automatically and prevents IP address conflicts.

DHCP is an application-layer protocol over **UDP**:
- Server listens on **UDP port 67**
- Client sends from **UDP port 68**

### DORA
DHCP works in four steps:
1. **Discover** – the client broadcasts, looking for a DHCP server
2. **Offer** – the server offers an available IP address and network settings
3. **Request** – the client accepts the offered address
4. **Acknowledge** – the server confirms the address is now assigned (leased) to the client

Because the client has no IP address yet, the **Discover** and **Request** messages are sent:
- from IP `0.0.0.0` to the broadcast IP `255.255.255.255`
- to the broadcast MAC `ff:ff:ff:ff:ff:ff`

All four packets share the same **Transaction ID**, which ties the exchange together.

---

## ARP – Bridging Layer 3 to Layer 2

A device usually knows the **IP address** of the host it wants to reach (e.g. the gateway from DHCP), but to build the Ethernet/WiFi frame it also needs the target's **MAC address**. **ARP** solves this.

### How ARP Works
1. **ARP Request** – sent to the broadcast MAC `ff:ff:ff:ff:ff:ff`:  
   *"Who has 192.168.66.1? Tell 192.168.66.89"*
2. **ARP Reply** – sent directly (unicast) back to the requester:  
   *"192.168.66.1 is at 44:df:65:d8:fe:6c"*

The result is stored in the **ARP cache**, so the question does not need to be repeated for every packet (viewable with `arp -a` or `ip neigh`).

ARP only works **inside the local network** – a device never ARPs for a remote server, only for the next hop (usually the router).

### Encapsulation
ARP is **not** carried inside IP or UDP. It is placed **directly inside the Ethernet frame** (EtherType `0x0806`). It has to sit below IP, because its job is to make IP delivery possible in the first place. ARP is sometimes described as "layer 2.5" – what matters is that it translates **layer 3 addresses to layer 2 addresses**.

### ARP Message Structure (28 bytes)
| Field | Size | Example (Reply) |
|---|---|---|
| Hardware Type | 2 | 1 = Ethernet |
| Protocol Type | 2 | `0x0800` = IPv4 |
| Hardware Size | 1 | 6 |
| Protocol Size | 1 | 4 |
| Opcode | 2 | 1 = Request, 2 = Reply |
| Sender MAC | 6 | `44:df:65:d8:fe:6c` |
| Sender IP | 4 | 192.168.66.1 |
| Target MAC | 6 | `cc:5e:f8:02:21:a7` (all zeros in a Request) |
| Target IP | 4 | 192.168.66.89 |

Full frame size: 14 (Ethernet header) + 28 (ARP) = **42 bytes**.

### tshark vs tcpdump
Both tools read the same capture, but display it differently:
- `tshark -r arp.pcapng -Nn` – shows MAC addresses, relative time and the full frame length (42)
- `tcpdump -r arp.pcapng -n -v` – shows wall-clock time, the ARP message length only (28), and hides the Ethernet header unless `-e` is added

Note: `-n` in tcpdump **disables** name resolution, while `-N` with a flag in tshark **enables** specific types of resolution.

### Security Note
ARP has **no authentication**. Any device on the local network can send a fake ARP Reply and redirect traffic through itself – this is **ARP Spoofing / ARP Poisoning**, a common basis for Man-in-the-Middle attacks.

---

## Routing

The Internet consists of millions of routers, and there is usually more than one path between two hosts. Routers use **routing protocols** to learn the network and choose the best link for each packet:

- **OSPF (Open Shortest Path First)** – routers share information about the state of their links, so each router builds a complete map of the network and calculates the most efficient paths.
- **EIGRP (Enhanced Interior Gateway Routing Protocol)** – a Cisco proprietary protocol; routers share reachable networks and the cost (bandwidth, delay) of each route.
- **BGP (Border Gateway Protocol)** – the primary routing protocol of the Internet; used between different networks (such as ISPs) to exchange routing information.
- **RIP (Routing Information Protocol)** – a simple protocol for small networks; chooses routes with the fewest hops.

---

## NAT – Sharing One Public IP Address

IPv4 supports about four billion addresses, which is not enough for every connected device. **NAT** allows many devices with **private IP addresses** to access the Internet through a **single public IP address**.

### How NAT Works
The router keeps a **translation table** that maps each internal connection to an external one:

| Internal Network | External Network |
|---|---|
| 192.168.0.129, 15401 | 212.3.4.5, 19273 |
| 192.168.0.137, 27912 | 212.3.4.5, 32759 |
| 192.168.0.112, 38192 | 212.3.4.5, 41251 |

- Outgoing packets: the router replaces the private IP and port with its public IP and a new port.
- Returning packets: the router looks up the table and translates back to the correct internal device.
- The web server only ever sees `212.3.4.5`.

Unlike plain routing, NAT requires the router to **track every active connection**.

### The Limit
Since all connections share the same public IP, they are distinguished only by the **port number**. The TCP port field is **16 bits** → 2¹⁶ = 65,536 ports, so even with unlimited processing power, a NAT router with one public IP can maintain roughly **65,000 simultaneous connections**.

---

## IP Header Fields Worth Knowing

While decoding the captures, I learned the meaning of several IPv4 header fields:

- **Version + IHL** – e.g. `45` = IPv4 with a 20-byte header (5 × 4 bytes)
- **Total Length** – size of the whole IP packet
- **TTL (Time To Live)** – decremented by 1 at every router; the packet is dropped when it reaches 0. Common starting values are 64 (Linux) and 128 (Windows).
- **Protocol** – what is inside: 1 = ICMP, 6 = TCP, 17 = UDP
- **Source / Destination IP**

### Identification and Flags – Fragmentation
Every link has a maximum frame size – the **MTU** (usually 1500 bytes for Ethernet). If a packet is too large for a link, it may be split into fragments:
- **Identification** – a number shared by all fragments of the same original packet, so the receiver can reassemble them
- **Flags:**
  - **DF (Don't Fragment)** – if set, routers must not fragment the packet; they drop it and send back an ICMP "Fragmentation Needed" message (Type 3, Code 4). This is the basis of **Path MTU Discovery**.
  - **MF (More Fragments)** – set on every fragment except the last
- **Fragment Offset** – where the fragment belongs in the original packet (in 8-byte units)

---

## ICMP – Diagnostics and Error Reporting

**ICMP** is used for network diagnostics and error reporting. Unlike ARP, it is carried **inside IP** (Protocol = 1), because it has to travel between networks. It has no ports.

Common ICMP types:
- **Type 8** – Echo Request
- **Type 0** – Echo Reply
- **Type 3** – Destination Unreachable
- **Type 11** – Time Exceeded

### ping
`ping` tests whether a host is reachable and measures the **round-trip time (RTT)**.

```
ping 192.168.11.1 -c 4
```
- `-c 4` – send four requests and stop (on Linux, ping runs until Ctrl+C by default)

What I learned from the output:
- `56(84) bytes of data` – 56 bytes of payload; 84 bytes for the full IP packet (20 IP + 8 ICMP + 56)
- `64 bytes from ...` – the size of the ICMP reply (8 header + 56 payload)
- `icmp_seq` – the sequence number of each request
- `ttl=63` – the target is one router away (it likely started at 64)
- `time` – the RTT of each request; large variation (**jitter**) is typical for WiFi
- `Jitter` is the instability in the arrival times of packets. That is, not how long it takes for a packet to arrive (that's `latency`), but how much that time varies from packet to packet.
- Summary: packets sent/received, **packet loss**, and RTT **min/avg/max/mdev** (mdev is effectively the standard deviation)

Inside the Echo Request/Reply:
- **Identifier** and **Sequence Number** match each reply to its request (this replaces ports)
- Wireshark shows them in both **BE** and **LE** byte order, because the sending OS chooses the byte order
- The Linux ping payload contains a **16-byte timestamp** and **40 bytes of padding data**
- The reply copies the payload **exactly**, which lets ping calculate the RTT

No reply does not necessarily mean the host is down – many hosts and firewalls block ICMP.

Why is there a payload?
The payload here is not intended to pass anything to the other side. It is intended to serve the test itself. The other side does not read it or do anything with it, it just returns it as is. The sender is the only one who uses it:
- The `timestamp` allows you to calculate how long the round trip took (the time= printed in each line of the ping).
- The `padding` allows you to check that the packet came back unchanged, meaning that the connection did not disrupt the information on the way.
It also has another use: you can increase the payload (with ping -s 1400 for example) to check how the network handles large packets. This is useful when small packets go through but large ones get stuck.

### traceroute
`traceroute` (Linux) / `tracert` (Windows) discovers the routers between my host and a target, using the **TTL** field:
1. Send a packet with TTL = 1 → the first router drops it and replies with **ICMP Time Exceeded (Type 11)**, revealing its address
2. Send with TTL = 2 → the second router reveals itself
3. Continue until the target itself replies

Details:
- On Linux, traceroute sends **UDP** probes to high ports by default; the target replies with **ICMP Port Unreachable**, which marks the end. `tracert` on Windows uses ICMP Echo Requests (Linux equivalent: `traceroute -I`).
- Three probes are sent per hop, so each line shows **three RTT values**.
- `* * *` means a router did not respond – it may drop the probe silently or block ICMP – but traffic still passes through it.
- Multiple addresses on one line indicate **load balancing** between parallel routers.
- RTT values per hop are not always increasing, because routers give generating ICMP messages a **low priority**. The overall trend is what matters.

Reading the route to `example.com`:
- Hops 1–2: home router (`_gateway`) and the ISP modem
- Hops 3–8: inside the ISP – private addresses (`10.x`, `172.16.x`) and **CGNAT** space (`100.64.0.0/10`)
- Hops 9–11: Europe (Marseille, Paris) – Level3 handing over to NTT
- Hops 12–15: USA (Newark, New York) – the big RTT jump reflects crossing the Atlantic
- Hops 16–17: the destination, hosted on the Edgecast CDN

Router hostnames often encode the interface, router number, city code and country, which makes it possible to follow the route geographically.

---

## Summary of the Four Protocols

| Need | Protocol |
|---|---|
| Find another host's MAC address | **ARP** |
| Get an IP address, gateway and DNS server automatically | **DHCP** |
| Test connectivity ("ping pong") and discover routes | **ICMP** |
| Let many private IPs share one public IP | **NAT** |

---

## Key Takeaways
- Ethernet and WiFi move frames between neighbouring devices; IP moves packets across networks
- The IP address stays the same end to end, while the data link header is rebuilt at every hop
- DHCP configures devices automatically using the DORA process
- ARP translates IP addresses to MAC addresses and is carried directly inside Ethernet frames
- Routing protocols such as OSPF, EIGRP, BGP and RIP decide how packets travel across networks
- NAT lets many devices share one public IP, limited by the number of available ports
- ICMP powers `ping` and `traceroute`, and the TTL field is what makes `traceroute` possible
- Reading packet captures down to the raw bytes builds a much deeper understanding of how protocols really work
