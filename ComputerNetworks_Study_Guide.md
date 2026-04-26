# Computer Networks Interview Study Guide
### For Fresh Graduate Software Engineering Interviews

---

> **How to Use This Guide**
> - Read each section once to understand the concept and the analogy.
> - Focus heavily on sections 2, 4, 6, 7, and 10 — these are asked in almost every interview.
> - Review the interview questions and key takeaways before your interview.
> - Use the Final Revision Cheat Sheet the morning of your interview.

---

## Table of Contents

1. [Networking Fundamentals](#1-networking-fundamentals)
2. [OSI Model](#2-osi-model)
3. [TCP/IP Model](#3-tcpip-model)
4. [TCP vs UDP](#4-tcp-vs-udp)
5. [IP Addressing](#5-ip-addressing)
6. [DNS](#6-dns)
7. [HTTP vs HTTPS](#7-http-vs-https)
8. [Common HTTP Methods](#8-common-http-methods)
9. [Cookies, Sessions, and JWT](#9-cookies-sessions-and-jwt)
10. [Three-Way Handshake and Four-Way Termination](#10-three-way-handshake-and-four-way-termination)
11. [Ports and Sockets](#11-ports-and-sockets)
12. [REST API Networking Concepts](#12-rest-api-networking-concepts)
13. [Frequently Asked Interview Questions](#13-frequently-asked-interview-questions)
14. [Common Mistakes](#14-common-mistakes)
15. [Final Revision Cheat Sheet](#15-final-revision-cheat-sheet)

---

## 1. Networking Fundamentals

### What is a Computer Network?

A **computer network** is a collection of **two or more devices connected together** so they can share data and resources.

**Simple analogy:** Think of roads connecting cities. Each city is a computer, and the roads are the network cables (or wireless signals). Cars carrying goods are the data packets travelling between cities.

**Why networks exist:** Without networks, you would need to physically carry a USB drive to share a file. Networks make communication instant and automatic.

---

### Why Are Networks Needed?

| Need | Example |
|---|---|
| **Resource Sharing** | Multiple employees share one printer |
| **Data Communication** | Sending emails, WhatsApp messages |
| **Data Storage & Access** | Cloud storage (Google Drive, OneDrive) |
| **Internet Access** | Browsing websites from any device |
| **Collaboration** | Google Docs — multiple people edit at once |
| **Cost Reduction** | Share expensive hardware (servers, printers) |

---

### Types of Networks

#### LAN — Local Area Network
- Covers a **small area** like a home, office, school, or building.
- **High speed**, typically 100 Mbps to 10 Gbps.
- **Example:** All computers in your university computer lab connected to a single switch.

#### MAN — Metropolitan Area Network
- Covers a **city or large campus**.
- Larger than LAN, smaller than WAN.
- **Example:** A university connecting all its campuses across a city.

#### WAN — Wide Area Network
- Covers **large geographical areas** — countries, continents.
- The **Internet** is the largest WAN in the world.
- **Example:** A company's offices in Karachi, Lahore, and Islamabad connected together.

#### Network Type Comparison

| Feature | LAN | MAN | WAN |
|---|---|---|---|
| Coverage | Room / Building | City / Campus | Country / World |
| Speed | Very Fast | Fast | Slower (depends on link) |
| Cost | Low | Medium | High |
| Example | Home Wi-Fi | City ISP network | The Internet |

---

### Basic Networking Devices

#### Router
- Connects **different networks** together and directs traffic between them.
- Assigns IP addresses to devices in your home network (via DHCP).
- **Analogy:** A post office — it reads the destination address and routes the package to the correct city.
- **Example:** Your home Wi-Fi router connects your home network to the Internet.

#### Switch
- Connects **devices within the same network** (LAN).
- Sends data only to the specific device it is addressed to (unlike a hub).
- **Analogy:** An internal office mail system — delivers letters to the exact desk, not the whole office.
- **Example:** The network switch in a school computer lab that connects all computers.

#### Hub
- Like a switch, but **broadcasts data to ALL connected devices** — every device receives every packet, even if it's not for them.
- Old technology — almost never used today (replaced by switches).
- **Analogy:** A loudspeaker in an office — everyone hears every announcement, even if it's not for them.

#### Modem
- Converts **digital data to analog signals** for transmission over telephone lines, and vice versa.
- "Modem" stands for **Mo**dulator-**Dem**odulator.
- **Analogy:** A translator — converts your computer's language (digital) into a format the phone line (analog) understands, and back again.
- **Example:** The device your ISP gives you to connect your router to the Internet.

#### Device Summary

| Device | Connects | Works at Layer | Smart? |
|---|---|---|---|
| Hub | Devices in same LAN | Physical (Layer 1) | No — broadcasts to all |
| Switch | Devices in same LAN | Data Link (Layer 2) | Yes — sends to correct device |
| Router | Different networks | Network (Layer 3) | Yes — routes between networks |
| Modem | Your network to ISP | Physical (Layer 1) | No — just converts signals |

---

### Interview Questions — Networking Fundamentals

**Q: What is the difference between a router and a switch?**
> A switch connects devices within the same network (LAN) and sends data to the correct device using MAC addresses. A router connects different networks together and routes data between them using IP addresses. Your home network uses both: a switch for connecting your devices and a router to connect your home to the Internet.

**Q: What is the difference between a hub and a switch?**
> A hub broadcasts all incoming data to every connected device — inefficient and insecure. A switch is intelligent — it sends data only to the specific device that the data is addressed to, using MAC address tables. Switches are universally preferred over hubs today.

---

> **Key Takeaways — Section 1**
> - A network = connected devices that can share data.
> - LAN = local (building), MAN = city, WAN = country/world (Internet is a WAN).
> - Router = connects networks. Switch = connects devices in the same network. Hub = old, broadcasts to all. Modem = converts signals.

---

## 2. OSI Model

### What is the OSI Model?

The **OSI (Open Systems Interconnection) Model** is a conceptual framework that describes **how data travels from one computer to another over a network**, divided into **7 layers**.

**Why it matters for interviews:** Almost every networking interview asks about OSI layers. It is the foundation for understanding how all networking protocols work.

**Simple analogy:** Think of sending a letter internationally.
- You write the letter (application layer).
- You put it in an envelope and address it (multiple layers of wrapping and addressing).
- It travels via courier, truck, plane, and local delivery (physical transmission).
- The receiver opens it layer by layer until they get to the letter.

The OSI model describes each step of this process for computer data.

---

### The 7 OSI Layers

```
Sender Side                                    Receiver Side
(Data goes DOWN)                               (Data goes UP)

┌─────────────────────────┐     ┌─────────────────────────┐
│  7. Application Layer   │     │  7. Application Layer   │
│  (HTTP, FTP, DNS, SMTP) │     │  (HTTP, FTP, DNS, SMTP) │
├─────────────────────────┤     ├─────────────────────────┤
│  6. Presentation Layer  │     │  6. Presentation Layer  │
│  (Encryption, Encoding) │     │  (Decryption, Decoding) │
├─────────────────────────┤     ├─────────────────────────┤
│  5. Session Layer       │     │  5. Session Layer       │
│  (Sessions, Auth)       │     │  (Sessions, Auth)       │
├─────────────────────────┤     ├─────────────────────────┤
│  4. Transport Layer     │     │  4. Transport Layer     │
│  (TCP, UDP, Ports)      │     │  (TCP, UDP, Ports)      │
├─────────────────────────┤     ├─────────────────────────┤
│  3. Network Layer       │     │  3. Network Layer       │
│  (IP, Routing)          │     │  (IP, Routing)          │
├─────────────────────────┤     ├─────────────────────────┤
│  2. Data Link Layer     │     │  2. Data Link Layer     │
│  (MAC, Frames, Switches)│     │  (MAC, Frames, Switches)│
├─────────────────────────┤     ├─────────────────────────┤
│  1. Physical Layer      │     │  1. Physical Layer      │
│  (Cables, Signals, Bits)│     │  (Cables, Signals, Bits)│
└─────────────────────────┘     └─────────────────────────┘
          │                               ▲
          └─── Physical transmission ─────┘
               (cables, Wi-Fi signals)
```

---

### Layer-by-Layer Explanation

#### Layer 7 — Application Layer
- **What it does:** The layer that users and applications interact with directly. Provides network services to applications.
- **Protocols:** HTTP, HTTPS, FTP, SMTP, DNS, SSH
- **Data unit:** Message / Data
- **Real-world example:** When you open Chrome and type a URL, Chrome uses HTTP at the application layer.
- **Key point:** This is NOT the application itself — it is the networking interface that the application uses.

#### Layer 6 — Presentation Layer
- **What it does:** Translates data into a format the application can understand. Handles **encryption, decryption, compression, and encoding**.
- **Examples:** SSL/TLS encryption, JPEG/PNG encoding, ASCII/Unicode encoding
- **Data unit:** Data
- **Real-world example:** When your browser encrypts data before sending it over HTTPS, that's the Presentation Layer.
- **Analogy:** A translator — converts the language so both sides understand each other.

#### Layer 5 — Session Layer
- **What it does:** Establishes, manages, and terminates **communication sessions** between applications.
- **Responsibilities:** Session establishment, maintenance, authentication, reconnection after failure
- **Examples:** NetBIOS, RPC (Remote Procedure Call)
- **Data unit:** Data
- **Real-world example:** When you log into a website and your session stays active across multiple pages.

#### Layer 4 — Transport Layer
- **What it does:** Provides **end-to-end communication** between devices. Handles segmentation, flow control, and error correction.
- **Protocols:** **TCP** (reliable) and **UDP** (fast, unreliable)
- **Data unit:** **Segment** (TCP) / **Datagram** (UDP)
- **Real-world example:** TCP ensures a downloaded file arrives complete and in order.
- **Key concepts:** Port numbers, multiplexing (sending multiple streams over one connection)

#### Layer 3 — Network Layer
- **What it does:** Handles **logical addressing (IP addresses)** and **routing** — finding the best path to the destination.
- **Protocols:** IP (IPv4, IPv6), ICMP (used by `ping`), routing protocols
- **Device:** Router
- **Data unit:** **Packet**
- **Real-world example:** When you send data across the Internet, routers use IP addresses to forward your packets hop by hop.

#### Layer 2 — Data Link Layer
- **What it does:** Handles **physical addressing (MAC addresses)** and error detection within the same network (LAN).
- **Protocols:** Ethernet, Wi-Fi (IEEE 802.11)
- **Device:** Switch
- **Data unit:** **Frame**
- **Real-world example:** When your data travels from your laptop to the Wi-Fi router on the same network, the Data Link Layer manages this using MAC addresses.
- **MAC address:** A unique hardware address burned into every network interface card (e.g., `AA:BB:CC:DD:EE:FF`).

#### Layer 1 — Physical Layer
- **What it does:** Transmits raw **bits (0s and 1s)** over a physical medium (cables, fiber optics, radio waves).
- **Examples:** Ethernet cables, fiber optic cables, Wi-Fi radio signals, USB
- **Device:** Hub, cable, repeater
- **Data unit:** **Bit**
- **Real-world example:** The actual network cable connecting your computer to the switch.

---

### Data Units Per Layer (Encapsulation)

As data travels DOWN the OSI stack on the sender's side, each layer adds its own header (and sometimes trailer). This is called **encapsulation**.

```
Layer 7: Application  → Data
Layer 6: Presentation → Data
Layer 5: Session      → Data
Layer 4: Transport    → [TCP/UDP Header] + Data       = Segment
Layer 3: Network      → [IP Header] + Segment         = Packet
Layer 2: Data Link    → [Frame Header] + Packet + [Frame Trailer] = Frame
Layer 1: Physical     → Bits (electrical signals)
```

On the receiver's side, each layer **removes its header** (de-encapsulation) until the original data reaches the application.

---

### OSI Layer Mnemonic

**To remember layers 7 → 1 (top to bottom):**
> **"All People Seem To Need Data Processing"**
> - **A**ll → Application
> - **P**eople → Presentation
> - **S**eem → Session
> - **T**o → Transport
> - **N**eed → Network
> - **D**ata → Data Link
> - **P**rocessing → Physical

**To remember layers 1 → 7 (bottom to top):**
> **"Please Do Not Throw Sausage Pizza Away"**
> - **P**lease → Physical
> - **D**o → Data Link
> - **N**ot → Network
> - **T**hrow → Transport
> - **S**ausage → Session
> - **P**izza → Presentation
> - **A**way → Application

---

### OSI Layer Quick Reference

| Layer | Name | Protocol Examples | Device | Data Unit |
|---|---|---|---|---|
| 7 | Application | HTTP, FTP, DNS, SMTP, SSH | — | Message |
| 6 | Presentation | SSL/TLS, JPEG, ASCII | — | Data |
| 5 | Session | NetBIOS, RPC | — | Data |
| 4 | Transport | TCP, UDP | — | Segment / Datagram |
| 3 | Network | IP, ICMP | Router | Packet |
| 2 | Data Link | Ethernet, Wi-Fi | Switch | Frame |
| 1 | Physical | Cables, Radio waves | Hub, Cable | Bit |

---

### Interview Questions — OSI Model

**Q: What are the 7 layers of the OSI model?**
> Physical, Data Link, Network, Transport, Session, Presentation, Application (layers 1 to 7). Use the mnemonic "Please Do Not Throw Sausage Pizza Away."

**Q: At which OSI layer does a router work?**
> Layer 3 — the Network Layer. Routers use IP addresses (logical addresses) to route packets between networks.

**Q: What is the difference between Layer 2 and Layer 3 addressing?**
> Layer 2 (Data Link) uses MAC addresses — physical hardware addresses used within the same local network. Layer 3 (Network) uses IP addresses — logical addresses used for routing across different networks. MAC addresses are fixed; IP addresses can be assigned dynamically.

**Q: What is encapsulation?**
> Encapsulation is the process of wrapping data with headers at each OSI layer as it travels down the stack. Each layer adds its own information (source/destination address, error check, etc.). The receiver de-encapsulates by removing each header layer by layer.

---

> **Key Takeaways — Section 2**
> - OSI has 7 layers: Physical → Data Link → Network → Transport → Session → Presentation → Application.
> - Mnemonic: "Please Do Not Throw Sausage Pizza Away" (bottom to top).
> - Key layers for interviews: Layer 3 (IP/Routing), Layer 4 (TCP/UDP), Layer 7 (HTTP/DNS).
> - Encapsulation = adding headers going down. De-encapsulation = removing headers going up.
> - Data units: Bit → Frame → Packet → Segment → Data.

---

## 3. TCP/IP Model

### What is the TCP/IP Model?

The **TCP/IP model** (also called the Internet Model) is the **practical model** used by the real Internet. It was developed before the OSI model and has **4 layers** instead of 7.

While the OSI model is a theoretical reference, the **TCP/IP model is what actually runs the Internet**.

---

### The 4 TCP/IP Layers

| Layer | Name | Protocols | Corresponds to OSI |
|---|---|---|---|
| 4 | **Application** | HTTP, HTTPS, FTP, DNS, SMTP, SSH | OSI 5 + 6 + 7 |
| 3 | **Transport** | TCP, UDP | OSI 4 |
| 2 | **Internet** | IP, ICMP, ARP | OSI 3 |
| 1 | **Network Access** | Ethernet, Wi-Fi | OSI 1 + 2 |

---

### OSI vs TCP/IP Model Side-by-Side

```
OSI Model (7 layers)          TCP/IP Model (4 layers)
┌─────────────────────┐       ┌─────────────────────────┐
│  7. Application     │       │                         │
├─────────────────────┤       │  4. Application Layer   │
│  6. Presentation    │       │  (HTTP, DNS, FTP, SMTP) │
├─────────────────────┤       │                         │
│  5. Session         │       └─────────────────────────┘
├─────────────────────┤       ┌─────────────────────────┐
│  4. Transport       │       │  3. Transport Layer     │
│  (TCP, UDP)         │       │  (TCP, UDP)             │
├─────────────────────┤       └─────────────────────────┘
│  3. Network         │       ┌─────────────────────────┐
│  (IP, Routing)      │       │  2. Internet Layer      │
├─────────────────────┤       │  (IP, ICMP, ARP)        │
│  2. Data Link       │       └─────────────────────────┘
├─────────────────────┤       ┌─────────────────────────┐
│  1. Physical        │       │  1. Network Access      │
└─────────────────────┘       │  (Ethernet, Wi-Fi)      │
                               └─────────────────────────┘
```

---

### Why TCP/IP is Used in Practice

| Reason | Explanation |
|---|---|
| **Simplicity** | 4 layers are easier to implement than 7 |
| **Proven** | Designed for and proven by the real Internet |
| **Flexible** | Does not mandate specific protocols at each layer |
| **Universal** | Every device connected to the Internet uses TCP/IP |
| **Open standard** | Freely available — no vendor lock-in |

The OSI model is used as a **teaching and troubleshooting reference**. The TCP/IP model is what actually runs on your computer and every device on the Internet.

---

### Interview Questions — TCP/IP Model

**Q: What is the difference between the OSI model and the TCP/IP model?**
> The OSI model has 7 layers and is a theoretical reference framework for understanding and designing networks. The TCP/IP model has 4 layers and is the practical model that the real Internet uses. The OSI model separates Session and Presentation layers explicitly; TCP/IP combines them into the Application layer.

**Q: What layer does HTTP operate at in the TCP/IP model?**
> HTTP operates at the Application layer (Layer 4) of the TCP/IP model, which corresponds to the Application, Presentation, and Session layers (Layers 5, 6, 7) of the OSI model.

---

> **Key Takeaways — Section 3**
> - TCP/IP has 4 layers: Network Access, Internet, Transport, Application.
> - OSI has 7 layers — used for teaching and troubleshooting; TCP/IP runs the actual Internet.
> - TCP/IP Application layer = OSI layers 5 + 6 + 7 combined.
> - Both models have identical Transport and Network layers.

---

## 4. TCP vs UDP

### What is TCP?

**TCP (Transmission Control Protocol)** is a **connection-oriented** protocol that guarantees **reliable, ordered, error-checked** delivery of data.

Before sending data, TCP establishes a connection (three-way handshake). It confirms every packet arrives, resends lost packets, and ensures data arrives in order.

**Analogy:** TCP is like a **registered courier service** — you get a confirmation receipt, the package is tracked, and if it gets lost, it is resent. Reliable but slightly slower.

---

### What is UDP?

**UDP (User Datagram Protocol)** is a **connectionless** protocol that sends data **as fast as possible** without guaranteeing delivery, order, or error checking.

There is no handshake, no acknowledgment, no resending. Data is fired and forgotten.

**Analogy:** UDP is like **sending a postcard** — you send it and hope it arrives. No confirmation, no tracking. Fast but unreliable.

---

### TCP vs UDP Full Comparison

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake required) | Connectionless (no handshake) |
| Reliability | Guaranteed delivery | No guarantee |
| Order | Packets arrive in order | Packets may arrive out of order |
| Error checking | Yes — retransmits lost packets | Minimal — drops bad packets |
| Speed | Slower (overhead for reliability) | Faster (less overhead) |
| Flow control | Yes | No |
| Congestion control | Yes | No |
| Header size | 20 bytes | 8 bytes |
| Use case | File transfer, web browsing, email | Video streaming, gaming, DNS, VoIP |
| Protocol examples | HTTP, HTTPS, FTP, SMTP, SSH | DNS, DHCP, VoIP, Video calls |

---

### When to Use TCP

Use TCP when **data accuracy is critical** and losing any part of the data is not acceptable:
- **Web browsing (HTTP/HTTPS)** — You need the complete web page
- **File downloads (FTP)** — A partially downloaded file is useless
- **Email (SMTP)** — Emails must arrive complete
- **Database queries** — Data must be accurate and complete
- **SSH (remote login)** — Every keystroke must arrive correctly

---

### When to Use UDP

Use UDP when **speed is more important than perfect reliability**:
- **Video streaming (YouTube, Netflix)** — A slightly glitchy frame is better than buffering
- **Online gaming** — Real-time position updates must be fast; old packets are useless anyway
- **VoIP / Video calls (Zoom, WhatsApp calls)** — A tiny audio gap is better than a delayed call
- **DNS lookups** — Small, quick queries; easy to retry if lost
- **Live broadcasting** — Real-time matters more than perfection

---

### Interview Questions — TCP vs UDP

**Q: What is the main difference between TCP and UDP?**
> TCP is connection-oriented and reliable — it guarantees that all data arrives completely and in order, using acknowledgments and retransmission. UDP is connectionless and unreliable — it sends data as fast as possible without guaranteeing delivery or order. Use TCP when data accuracy matters (web, file transfer), UDP when speed matters more than perfection (gaming, video calls).

**Q: Why does DNS use UDP instead of TCP?**
> DNS queries are very small (a single request and a single response) and need to be fast. If a DNS query is lost, the client simply resends it — so the overhead of TCP's three-way handshake for such a small exchange is not worth it. UDP makes DNS lookups faster. (Note: DNS uses TCP for larger responses, like zone transfers.)

**Q: Can you lose data with UDP? Is it always a problem?**
> Yes, you can lose data with UDP. But it is not always a problem. In video streaming, if one video frame is lost, the next frame arrives quickly and the viewer barely notices a glitch. In online gaming, old position updates are irrelevant — only the latest position matters. UDP's speed benefit outweighs the occasional data loss in these scenarios.

---

> **Key Takeaways — Section 4**
> - TCP = reliable, ordered, connection-oriented. UDP = fast, connectionless, no guarantee.
> - TCP analogy = registered courier (confirmation). UDP analogy = postcard (no confirmation).
> - TCP for: web, email, file transfer. UDP for: gaming, video/audio streaming, DNS.
> - UDP header is only 8 bytes vs TCP's 20 bytes — much less overhead.

---

## 5. IP Addressing

### What is an IP Address?

An **IP (Internet Protocol) address** is a unique **numerical label** assigned to every device on a network to identify it and enable communication.

**Analogy:** An IP address is like your **home address** — it uniquely identifies where you live so mail (data) can be delivered to you and only you.

---

### IPv4

**IPv4 (Internet Protocol version 4)** is the most widely used IP addressing scheme.

- Format: **4 numbers separated by dots**, each between 0 and 255.
- Example: `192.168.1.1`, `10.0.0.1`, `172.217.20.46` (Google)
- Each number is called an **octet** (8 bits).
- Total address length: **32 bits** = 2³² = ~4.3 billion unique addresses.

```
IPv4 Address: 192   .  168   .   1   .   1
              8 bits  8 bits  8 bits  8 bits
              ─────────────────────────────
              Total: 32 bits
```

**Problem with IPv4:** 4.3 billion addresses seemed like a lot in the 1980s. Today, with billions of smartphones, IoT devices, and computers, IPv4 addresses are nearly exhausted.

---

### IPv6

**IPv6** was created to solve the IPv4 exhaustion problem.

- Format: **8 groups of 4 hexadecimal digits**, separated by colons.
- Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- Total address length: **128 bits** = 2¹²⁸ = 340 undecillion addresses (practically unlimited).
- Leading zeros can be omitted. Consecutive zero groups can be replaced with `::`.

```
Shortened IPv6: 2001:db8:85a3::8a2e:370:7334
```

**IPv6 vs IPv4:**

| Feature | IPv4 | IPv6 |
|---|---|---|
| Address length | 32 bits | 128 bits |
| Format | Decimal (0-255) with dots | Hexadecimal with colons |
| Total addresses | ~4.3 billion | ~340 undecillion |
| Example | `192.168.1.1` | `2001:db8::1` |
| Header size | 20 bytes | 40 bytes |
| NAT required? | Yes (due to scarcity) | No (enough addresses) |

---

### Public vs Private IP Addresses

#### Public IP Address
- **Assigned by your ISP**. Visible to the entire Internet.
- Your router has one public IP — the outside world sees this address.
- **Example:** When you visit google.com, Google's server sees your public IP.

#### Private IP Address
- Used **inside your home or office network only**. Not routable on the Internet.
- Multiple devices share the same public IP (via NAT — Network Address Translation).
- **Examples of private ranges:**

| Range | Example | Used For |
|---|---|---|
| `10.0.0.0 – 10.255.255.255` | `10.0.0.5` | Large organizations |
| `172.16.0.0 – 172.31.255.255` | `172.16.0.10` | Medium organizations |
| `192.168.0.0 – 192.168.255.255` | `192.168.1.5` | Home/small office |

**Analogy:** A public IP is your building's street address. Private IPs are apartment numbers inside the building. The postman (Internet) delivers to the building (public IP), and the building reception (router/NAT) forwards it to the correct apartment (private IP).

---

### Static vs Dynamic IP

| Feature | Static IP | Dynamic IP |
|---|---|---|
| Changes? | Never — fixed permanently | Changes periodically (assigned by DHCP) |
| Assigned by | Network admin manually | DHCP server automatically |
| Cost | Usually costs more | Included in standard service |
| Use case | Servers, hosting, printers | Home users, mobile devices |
| Example | A web server needs the same IP forever | Your home router's public IP can change daily |

**DHCP (Dynamic Host Configuration Protocol):** The protocol that automatically assigns IP addresses to devices on a network. When you connect to Wi-Fi, DHCP gives your phone an IP address automatically.

---

### Loopback Address

- `127.0.0.1` (IPv4) or `::1` (IPv6) — the **loopback address**, also called **localhost**.
- A packet sent to `127.0.0.1` loops back to the same machine — never leaves the computer.
- **Used by developers** to test servers running on their own machine.
- `localhost:3000` means "connect to port 3000 on my own computer."

---

### Interview Questions — IP Addressing

**Q: What is the difference between a public and a private IP address?**
> A public IP is globally unique and routable on the Internet — assigned by your ISP and visible to external servers. A private IP is used within a local network only (home, office) and is not routable on the Internet. NAT (Network Address Translation) allows multiple devices with private IPs to share one public IP when accessing the Internet.

**Q: Why was IPv6 created?**
> IPv4 uses 32-bit addresses providing only ~4.3 billion unique addresses, which are now nearly exhausted due to the massive growth of Internet-connected devices. IPv6 uses 128-bit addresses, providing 340 undecillion unique addresses — effectively unlimited for the foreseeable future.

**Q: What is `127.0.0.1`?**
> It is the loopback address, also called localhost. Data sent to this address is routed back to the same machine without going through the network. Developers use it to test applications running locally (e.g., `http://localhost:8080`).

---

> **Key Takeaways — Section 5**
> - IPv4: 32 bits, 4.3 billion addresses (nearly exhausted). IPv6: 128 bits, practically unlimited.
> - Public IP = visible to Internet (assigned by ISP). Private IP = inside your network only.
> - Static IP = fixed (servers). Dynamic IP = changes (assigned by DHCP, used by home users).
> - `127.0.0.1` = localhost = loopback address = your own machine.

---

## 6. DNS

### What is DNS?

**DNS (Domain Name System)** is the **phone book of the Internet**. It translates human-readable domain names (like `google.com`) into machine-readable IP addresses (like `142.250.190.14`).

**Why DNS exists:** Computers communicate using IP addresses (numbers). Humans remember names. DNS bridges this gap — you type `youtube.com` and DNS tells your computer the IP address to connect to.

**Analogy:** DNS is like asking a receptionist "What's the phone number for Alice?" The receptionist looks it up and gives you `+92-300-1234567`. You didn't need to remember the number — you just knew the name.

---

### DNS Lookup Process — Step by Step

```
You type: www.google.com in your browser

Step 1: Check Browser Cache
  ↓ (not found)

Step 2: Check OS Cache / hosts file
  ↓ (not found)

Step 3: Query Recursive Resolver (usually your ISP's DNS server)
  ↓ (not in its cache)

Step 4: Recursive Resolver queries Root Name Server
  Root Server: "I don't know google.com, but here's the .com TLD server address"
  ↓

Step 5: Recursive Resolver queries .com TLD Name Server
  TLD Server: "I don't know www.google.com, but here's Google's authoritative server address"
  ↓

Step 6: Recursive Resolver queries Google's Authoritative Name Server
  Google's Server: "www.google.com → 142.250.190.14"
  ↓

Step 7: Recursive Resolver returns IP to your browser
  ↓

Step 8: Browser connects to 142.250.190.14 (Google's server)
  ↓

Step 9: Google's web page loads!
```

---

### Key DNS Components

| Component | What It Does |
|---|---|
| **DNS Resolver** | Your local DNS client — first asks the cache, then queries servers |
| **Root Name Server** | Knows where all TLD servers are (13 root servers worldwide) |
| **TLD Name Server** | Manages top-level domains (`.com`, `.org`, `.pk`) |
| **Authoritative Name Server** | Has the final answer — the actual IP for the domain |
| **DNS Cache** | Stores recent lookups to avoid repeating the full process |

### DNS Record Types

| Record Type | Purpose | Example |
|---|---|---|
| **A Record** | Maps domain to IPv4 address | `google.com → 142.250.190.14` |
| **AAAA Record** | Maps domain to IPv6 address | `google.com → 2607:f8b0::200e` |
| **CNAME** | Alias — points one domain to another | `www.google.com → google.com` |
| **MX Record** | Mail server for the domain | `google.com → mail server` |
| **NS Record** | Name servers for the domain | Which servers answer for this domain |
| **TTL** | Time To Live — how long to cache the record | `3600` = cache for 1 hour |

---

### Why DNS Matters for Software Engineers

- **Debugging:** If your website is unreachable, check DNS first — `nslookup google.com` or `dig google.com`
- **Deployment:** When deploying a new server, you update DNS A records to point to the new IP
- **Load balancing:** DNS can return multiple IPs for the same domain (DNS-based load balancing)
- **CDN:** Content Delivery Networks use DNS to route users to the nearest server
- **API development:** Your API at `api.yourcompany.com` needs a DNS record pointing to your server

---

### Interview Questions — DNS

**Q: What is DNS and why is it needed?**
> DNS (Domain Name System) translates human-readable domain names like `google.com` into IP addresses like `142.250.190.14`. It is needed because computers communicate using IP addresses (numbers), but humans remember names. DNS is the phone book that makes this translation automatic and invisible to users.

**Q: What happens when you type www.google.com in your browser?**
> 1. Browser checks its cache for the IP. 2. OS checks its DNS cache. 3. If not cached, a DNS resolver (usually ISP's) is queried. 4. The resolver asks the root name server, then the TLD server (.com), then Google's authoritative name server. 5. The IP is returned. 6. Browser opens a TCP connection to that IP. 7. HTTP/HTTPS request is sent and the page loads.

---

> **Key Takeaways — Section 6**
> - DNS = phone book of the Internet — translates domain names to IP addresses.
> - Lookup order: browser cache → OS cache → recursive resolver → root → TLD → authoritative server.
> - A record = domain to IPv4. AAAA = domain to IPv6. CNAME = alias.
> - TTL determines how long a DNS record is cached before re-queried.

---

## 7. HTTP vs HTTPS

### What is HTTP?

**HTTP (HyperText Transfer Protocol)** is the protocol used for **communication between a web browser and a web server**.

When you visit a website, your browser sends an HTTP request to the server, and the server sends back an HTTP response containing the web page.

**Key properties of HTTP:**
- **Stateless** — Every request is independent; the server has no memory of previous requests
- **Text-based** — Requests and responses are human-readable text
- **Default port: 80**

---

### HTTP Request-Response Cycle

```
Browser (Client)                    Web Server
        │                               │
        │  1. HTTP Request              │
        │  GET /index.html HTTP/1.1     │
        │  Host: www.example.com        │
        │ ─────────────────────────────►│
        │                               │  2. Server processes request
        │  3. HTTP Response             │
        │  HTTP/1.1 200 OK              │
        │  Content-Type: text/html      │
        │  <html>...</html>             │
        │ ◄─────────────────────────────│
        │                               │
        │  4. Browser renders page      │
```

---

### What is HTTPS?

**HTTPS (HTTP Secure)** is HTTP with **encryption** added. All data sent between the browser and server is encrypted so no one can intercept and read it.

- Uses **SSL (Secure Sockets Layer)** or its modern successor **TLS (Transport Layer Security)** for encryption.
- **Default port: 443**
- The padlock icon in your browser means HTTPS is active.

---

### How HTTPS (TLS) Works — Simplified

```
Browser                              Server
    │                                    │
    │  1. "Hello, I want HTTPS"          │
    │ ──────────────────────────────────►│
    │                                    │
    │  2. Server sends its SSL Certificate│
    │     (contains server's public key) │
    │ ◄──────────────────────────────────│
    │                                    │
    │  3. Browser verifies certificate   │
    │     (signed by trusted CA?)        │
    │                                    │
    │  4. Browser generates session key, │
    │     encrypts with server's         │
    │     public key, sends it           │
    │ ──────────────────────────────────►│
    │                                    │
    │  5. Server decrypts with           │
    │     private key → gets session key │
    │                                    │
    │  6. Both now share a secret key    │
    │     All future data is encrypted   │
    │ ◄═══ Encrypted communication ═════►│
```

**CA (Certificate Authority):** A trusted organization (like DigiCert, Let's Encrypt) that verifies a server's identity and signs its SSL certificate. Your browser trusts certificates signed by known CAs.

---

### HTTP vs HTTPS Comparison

| Feature | HTTP | HTTPS |
|---|---|---|
| Full name | HyperText Transfer Protocol | HTTP Secure |
| Encryption | None — data in plain text | Yes — TLS/SSL encryption |
| Default port | 80 | 443 |
| Security | Vulnerable to eavesdropping | Secure — data is encrypted |
| Data integrity | No — can be modified in transit | Yes — tampering is detected |
| Authentication | No server identity verification | Server identity verified via certificate |
| Use case | Old/internal sites (avoid!) | All modern websites |
| URL starts with | `http://` | `https://` |
| Browser indicator | No padlock | Padlock icon |
| SEO impact | Penalized by Google | Preferred by Google (ranking boost) |

---

### Why HTTPS Matters for Developers

- **User privacy:** Without HTTPS, passwords, credit card numbers, and personal data travel as plain text — easily intercepted.
- **Trust:** Users see the padlock; without it, browsers show "Not Secure" warnings.
- **SEO:** Google ranks HTTPS sites higher.
- **Compliance:** Many regulations (GDPR, PCI-DSS) require HTTPS.
- **Modern APIs:** Many browser features (Service Workers, Geolocation, Camera) only work over HTTPS.

---

### Interview Questions — HTTP vs HTTPS

**Q: What is the difference between HTTP and HTTPS?**
> HTTP transfers data in plain text — anyone who intercepts the traffic can read it. HTTPS adds encryption using TLS/SSL — all data between the browser and server is encrypted, making interception useless. HTTPS also verifies the server's identity via SSL certificates signed by trusted Certificate Authorities.

**Q: What is TLS and how does it relate to SSL?**
> SSL (Secure Sockets Layer) was the original encryption protocol for HTTPS, now deprecated due to security vulnerabilities. TLS (Transport Layer Security) is the modern, secure replacement. When people say "SSL certificate," they technically mean a TLS certificate — the terms are used interchangeably in practice.

**Q: What port does HTTPS use?**
> HTTPS uses port 443. HTTP uses port 80.

---

> **Key Takeaways — Section 7**
> - HTTP = plain text, port 80. HTTPS = encrypted, port 443.
> - HTTPS uses TLS (formerly SSL) for encryption and certificate-based authentication.
> - All modern websites should use HTTPS — browsers warn users on HTTP sites.
> - SSL certificate is issued by a Certificate Authority (CA) to verify server identity.

---

## 8. Common HTTP Methods

HTTP methods define **what action** the client wants to perform on the server's resource. They are the foundation of REST APIs.

---

### GET

- **Purpose:** **Retrieve** data from the server. Should only read data — never modify it.
- **Request body:** None (parameters go in the URL query string)
- **Safe:** Yes — does not change server state
- **Idempotent:** Yes — calling it multiple times gives the same result
- **Example:**

```
GET /api/users/42 HTTP/1.1
Host: api.example.com

Response: { "id": 42, "name": "Ali", "email": "ali@example.com" }
```

**Real-world use:** Loading a user profile, fetching a list of products, searching.

---

### POST

- **Purpose:** **Create** a new resource on the server.
- **Request body:** Yes — contains the data to create
- **Safe:** No — modifies server state (creates new data)
- **Idempotent:** No — calling it twice creates two records
- **Example:**

```
POST /api/users HTTP/1.1
Content-Type: application/json

{ "name": "Sara", "email": "sara@example.com" }

Response: 201 Created
{ "id": 43, "name": "Sara", "email": "sara@example.com" }
```

**Real-world use:** User registration, submitting a form, placing an order.

---

### PUT

- **Purpose:** **Replace** an entire existing resource with new data.
- **Request body:** Yes — the complete new version of the resource
- **Safe:** No — modifies server state
- **Idempotent:** Yes — calling it multiple times with the same data gives the same result
- **Example:**

```
PUT /api/users/42 HTTP/1.1
Content-Type: application/json

{ "name": "Ali Updated", "email": "ali_new@example.com", "age": 25 }

Response: 200 OK (entire resource replaced)
```

**Key point:** PUT replaces the ENTIRE resource. If you omit a field, it gets deleted.

---

### PATCH

- **Purpose:** **Partially update** an existing resource — only the fields you send are changed.
- **Request body:** Yes — only the fields to update
- **Safe:** No — modifies server state
- **Idempotent:** Usually yes (but not guaranteed)
- **Example:**

```
PATCH /api/users/42 HTTP/1.1
Content-Type: application/json

{ "email": "ali_new@example.com" }

Response: 200 OK (only email was updated; name, age remain unchanged)
```

**PUT vs PATCH:** PUT replaces everything. PATCH updates only what you send.

---

### DELETE

- **Purpose:** **Delete** a resource from the server.
- **Request body:** Usually none
- **Safe:** No — modifies server state (removes data)
- **Idempotent:** Yes — deleting an already-deleted resource still results in it being gone
- **Example:**

```
DELETE /api/users/42 HTTP/1.1

Response: 204 No Content (successfully deleted)
```

---

### HTTP Methods Summary Table

| Method | Purpose | Has Body | Safe | Idempotent | Common Status Codes |
|---|---|---|---|---|---|
| GET | Retrieve | No | Yes | Yes | 200 OK, 404 Not Found |
| POST | Create | Yes | No | No | 201 Created, 400 Bad Request |
| PUT | Replace all | Yes | No | Yes | 200 OK, 204 No Content |
| PATCH | Partial update | Yes | No | Usually | 200 OK, 404 Not Found |
| DELETE | Delete | No | No | Yes | 204 No Content, 404 Not Found |

**Safe:** Does not modify server state (read-only).
**Idempotent:** Calling the same operation multiple times produces the same result.

---

### Interview Questions — HTTP Methods

**Q: What is the difference between PUT and PATCH?**
> PUT replaces the entire resource — if you don't include a field, it gets deleted or reset. PATCH partially updates a resource — only the fields you include are changed. Use PUT when replacing a complete resource; use PATCH for small updates.

**Q: What does idempotent mean? Which HTTP methods are idempotent?**
> Idempotent means you can make the same request multiple times and the result is always the same as the first successful call. GET, PUT, DELETE, and HEAD are idempotent. POST is not — calling POST twice creates two resources.

**Q: Why do we use POST to create a resource instead of GET?**
> GET is for reading only — it should not modify server state. Also, GET parameters go in the URL (visible in browser history, logs, and bookmarks), which is insecure for sensitive data like passwords. POST sends data in the request body — hidden from the URL and more secure for creating/submitting data.

---

> **Key Takeaways — Section 8**
> - GET = read only. POST = create. PUT = replace all. PATCH = partial update. DELETE = remove.
> - Safe = read-only (GET). Idempotent = same result every time (GET, PUT, DELETE).
> - POST is NOT idempotent — duplicate submissions create duplicate records.
> - PUT vs PATCH: PUT replaces everything; PATCH changes only what you send.

---

## 9. Cookies, Sessions, and JWT

### Why Are These Needed?

HTTP is **stateless** — every request is completely independent. The server has no memory of who you are between requests.

**Problem:** When you log in to Instagram, how does the server know you are logged in on your next request?

**Solution:** Cookies, Sessions, and JWT are three different mechanisms to maintain **authentication state** between requests.

---

### Cookies

A **cookie** is a **small piece of data** stored by the browser and sent **automatically with every HTTP request** to the same server.

**How cookies work:**
```
1. User logs in → Server sends:
   HTTP Response Header: Set-Cookie: session_id=abc123; HttpOnly; Secure

2. Browser stores the cookie

3. Every future request to that server automatically includes:
   HTTP Request Header: Cookie: session_id=abc123

4. Server reads the cookie and identifies the user
```

**Cookie properties:**
- **HttpOnly** — JavaScript cannot access this cookie (prevents XSS attacks)
- **Secure** — Only sent over HTTPS connections
- **Expires / Max-Age** — When the cookie expires
- **SameSite** — Controls cross-site cookie behaviour (CSRF protection)

**Use cases:** User authentication, shopping cart (e-commerce), user preferences, tracking

---

### Sessions

A **session** stores user data **on the server side**. The client only receives a **session ID** (usually stored in a cookie).

**How sessions work:**
```
1. User logs in →
   Server creates session: { session_id: "xyz789", user_id: 42, role: "admin" }
   Server stores session in memory or database
   Server sends cookie: Set-Cookie: session_id=xyz789

2. User makes a request →
   Browser sends: Cookie: session_id=xyz789

3. Server looks up session_id=xyz789 in its session store →
   Finds: { user_id: 42, role: "admin" }
   Server knows who the user is
```

**Advantages of sessions:**
- Sensitive data (user role, permissions) stored server-side — not exposed to client
- Server can invalidate a session instantly (force logout by deleting session)

**Disadvantages:**
- Server must store sessions in memory or database
- Harder to scale across multiple servers (session stored on one server)

---

### JWT (JSON Web Token)

A **JWT** is a **self-contained token** that the server creates, signs, and sends to the client. The client stores it and sends it with every request. The server **verifies the signature** to confirm authenticity — no session storage needed.

**JWT structure:** Three Base64-encoded parts separated by dots.

```
header.payload.signature

Example:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJ1c2VyX2lkIjo0Miwicm9sZSI6ImFkbWluIiwiZXhwIjoxNzE0MDAwMDAwfQ.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

Decoded:
Header:  { "alg": "HS256", "typ": "JWT" }
Payload: { "user_id": 42, "role": "admin", "exp": 1714000000 }
Signature: HMAC-SHA256(header + payload, secret_key)
```

**How JWT works:**
```
1. User logs in → Server creates JWT with user data, signs it with secret key
2. Server sends JWT to client (stored in localStorage or cookie)
3. Client sends JWT in every request: Authorization: Bearer <token>
4. Server receives JWT → verifies signature → trusts the payload → no DB lookup needed
```

**Advantages of JWT:**
- **Stateless** — server needs no session storage; easy to scale
- **Self-contained** — user data is in the token itself
- Works across **multiple servers** (any server with the secret key can verify)

**Disadvantages:**
- **Cannot invalidate** easily — once issued, valid until expiry (need a blacklist for logout)
- **Larger size** than a session ID cookie
- Sensitive data in payload is **Base64 encoded, not encrypted** — readable if intercepted

---

### Cookie vs Session vs JWT Comparison

| Feature | Cookie | Session | JWT |
|---|---|---|---|
| Data stored at | Client (browser) | Server | Client (browser) |
| What client stores | Actual data or session ID | Session ID only | Full signed token |
| Server storage needed | No | Yes (session store) | No |
| Scalability | Easy | Harder (session sharing needed) | Very easy |
| Invalidation | Set expiry or delete | Delete session from server | Hard (need blacklist) |
| Security risk | XSS (if not HttpOnly), CSRF | Safer (data on server) | XSS if in localStorage |
| Size | Small | Small ID | Larger |
| Best for | Simple state, preferences | Traditional web apps | REST APIs, microservices |

---

### Where to Store JWT?

| Storage | XSS Risk | CSRF Risk | Recommendation |
|---|---|---|---|
| **localStorage** | High (JS can read it) | Low | Avoid for sensitive tokens |
| **Cookie (HttpOnly)** | Low (JS cannot read) | Medium (use SameSite) | Recommended |
| **Memory (variable)** | Low | Low | Safest, lost on page refresh |

---

### Interview Questions — Cookies, Sessions, JWT

**Q: What is the difference between a cookie and a session?**
> A cookie stores data on the client (browser). A session stores data on the server — the client only holds a session ID (typically in a cookie). Sessions are more secure because sensitive data never leaves the server. However, sessions require server-side storage and are harder to scale.

**Q: Why is JWT popular for REST APIs?**
> JWT is stateless — the server does not need to store any session data. The token is self-contained with all user information signed by the server. Any server with the secret key can verify the token, making it perfect for distributed systems and microservices where multiple servers handle requests.

**Q: What is the security risk of storing JWT in localStorage?**
> localStorage is accessible to all JavaScript on the page. If the application has an XSS (Cross-Site Scripting) vulnerability, malicious JavaScript can steal the JWT from localStorage and impersonate the user. Storing JWT in an HttpOnly cookie prevents JavaScript from accessing it.

---

> **Key Takeaways — Section 9**
> - HTTP is stateless — cookies/sessions/JWT maintain identity between requests.
> - Cookie = data in browser. Session = ID in browser, data on server. JWT = signed token in browser.
> - JWT is stateless and scalable — great for APIs. Sessions are easier to invalidate.
> - Store JWT in HttpOnly cookies, not localStorage, to prevent XSS attacks.

---

## 10. Three-Way Handshake and Four-Way Termination

### TCP Three-Way Handshake (Connection Establishment)

Before TCP sends any data, it must first **establish a connection** through a three-step process called the **three-way handshake**.

**Simple analogy:** Meeting someone for the first time:
1. You say "Hello!" (SYN)
2. They say "Hello back! Did you say hello?" (SYN-ACK)
3. You say "Yes I did!" (ACK)
Now you're connected and can start talking.

---

```
Client                              Server
   │                                   │
   │  1. SYN                           │
   │  "I want to connect.              │
   │   My sequence number is 100"      │
   │ ─────────────────────────────────►│
   │                                   │
   │  2. SYN-ACK                       │
   │  "OK! I acknowledge your 100.     │
   │   My sequence number is 200"      │
   │ ◄─────────────────────────────────│
   │                                   │
   │  3. ACK                           │
   │  "I acknowledge your 200.         │
   │   Connection established!"        │
   │ ─────────────────────────────────►│
   │                                   │
   │ ══════ Connection Established ════│
   │  (Data transfer begins now)       │
```

**Step-by-step:**
1. **SYN (Synchronize):** Client sends a packet with a random sequence number (e.g., 100) to say "I want to connect."
2. **SYN-ACK (Synchronize-Acknowledge):** Server acknowledges (ACK = 101, meaning "received up to 100, expecting 101") and sends its own sequence number (e.g., 200).
3. **ACK (Acknowledge):** Client acknowledges the server's sequence number (ACK = 201). Connection is now established.

**Why three steps?** Both sides need to:
- Confirm the other side can **send** and **receive**
- Agree on **sequence numbers** for ordered data delivery

---

### TCP Four-Way Termination (Connection Closing)

Closing a TCP connection takes **four steps** because each side closes its own half of the connection independently.

**Simple analogy:** Ending a phone call:
1. You say "I'm done talking, goodbye." (FIN)
2. They say "OK, I heard you." (ACK)
3. They finish what they were saying, then say "I'm done too, goodbye." (FIN)
4. You say "OK, bye!" (ACK)

---

```
Client                              Server
   │                                   │
   │  1. FIN                           │
   │  "I have no more data to send"    │
   │ ─────────────────────────────────►│
   │                                   │
   │  2. ACK                           │
   │  "Got it. But I might still       │
   │   have data to send you"          │
   │ ◄─────────────────────────────────│
   │                                   │
   │   [Server finishes sending data]  │
   │                                   │
   │  3. FIN                           │
   │  "Now I'm also done sending"      │
   │ ◄─────────────────────────────────│
   │                                   │
   │  4. ACK                           │
   │  "OK, connection closed"          │
   │ ─────────────────────────────────►│
   │                                   │
   │ ══════ Connection Closed ══════════│
```

**Why four steps instead of three?**
TCP is **full-duplex** — both sides can send and receive independently. When the client says FIN, it means "I'm done sending" — but the server may still have data to send. So the server first ACKs the client's FIN, finishes sending its remaining data, then sends its own FIN.

---

### Handshake Summary

| Handshake | Steps | Purpose |
|---|---|---|
| Three-Way Handshake | SYN → SYN-ACK → ACK | Establish TCP connection |
| Four-Way Termination | FIN → ACK → FIN → ACK | Gracefully close TCP connection |

---

### Interview Questions — TCP Handshake

**Q: What is the TCP three-way handshake?**
> The three-way handshake is the process TCP uses to establish a connection. Step 1: Client sends SYN (synchronize) with its sequence number. Step 2: Server responds with SYN-ACK, acknowledging the client's sequence and sending its own. Step 3: Client sends ACK acknowledging the server's sequence. After this, both sides have confirmed they can send and receive data.

**Q: Why does TCP use a three-way handshake instead of two?**
> Two steps would only confirm one direction. After SYN → SYN-ACK, the server knows the client can send. But the client needs to confirm it received the SYN-ACK (ACK step), which also confirms the server can receive. Three steps are the minimum needed to confirm both sides can both send AND receive.

**Q: What is the difference between the three-way handshake and four-way termination?**
> The handshake establishes the connection in three steps because the server's acknowledgment (SYN-ACK) combines two steps. Termination needs four steps because TCP is full-duplex — each direction must be closed independently. The server ACKs the client's FIN immediately but may still have data to send before it sends its own FIN.

---

> **Key Takeaways — Section 10**
> - Three-Way Handshake: SYN → SYN-ACK → ACK (connection established).
> - Four-Way Termination: FIN → ACK → FIN → ACK (connection closed).
> - Four steps needed for termination because TCP is full-duplex (both sides independent).
> - After handshake, sequence numbers are synchronized for ordered data delivery.

---

## 11. Ports and Sockets

### What is a Port?

A **port** is a **logical number** that identifies a specific service or application running on a device.

An IP address identifies the device; a port identifies the **specific application** on that device.

**Analogy:** An IP address is like an **apartment building address**. A port is the **apartment number**. The building (IP) receives the mail (data), and the port ensures it goes to the correct apartment (application).

```
Your Computer: IP = 192.168.1.5
Port 80  → Web Server (Nginx, Apache)
Port 443 → HTTPS Web Server
Port 3306 → MySQL Database
Port 22  → SSH Server
Port 3000 → Your Node.js development server
```

**Port ranges:**

| Range | Name | Description |
|---|---|---|
| 0 – 1023 | Well-known ports | Reserved for standard protocols (HTTP, HTTPS, SSH) |
| 1024 – 49151 | Registered ports | Used by software applications (MySQL: 3306, PostgreSQL: 5432) |
| 49152 – 65535 | Dynamic/Ephemeral | Assigned temporarily to client connections |

---

### Common Port Numbers (Must Memorize)

| Port | Protocol | Use |
|---|---|---|
| 20, 21 | FTP | File Transfer Protocol (20=data, 21=control) |
| 22 | SSH | Secure Shell (remote login) |
| 23 | Telnet | Unencrypted remote login (old, insecure — avoid) |
| 25 | SMTP | Sending email |
| 53 | DNS | Domain Name System lookups |
| 80 | HTTP | Web browsing (unencrypted) |
| 110 | POP3 | Receiving email (download) |
| 143 | IMAP | Receiving email (sync) |
| 443 | HTTPS | Secure web browsing (encrypted) |
| 3306 | MySQL | MySQL database |
| 5432 | PostgreSQL | PostgreSQL database |
| 6379 | Redis | Redis in-memory cache |
| 27017 | MongoDB | MongoDB database |
| 8080 | HTTP alt | Commonly used by developers for local servers |
| 3000 | Node.js | Common default for Node.js/React dev servers |

---

### What is a Socket?

A **socket** is the **combination of an IP address and a port number** that uniquely identifies one endpoint of a network connection.

```
Socket = IP Address + Port Number
Example: 192.168.1.5:3306  (MySQL on a server)
         142.250.190.14:443 (Google's HTTPS server)
```

A **connection** between two devices is identified by a pair of sockets (one on each end):

```
Client Socket                     Server Socket
192.168.1.100:51234  ◄──────────► 142.250.190.14:443
(your browser's      (random port  (Google's IP
 IP)                  assigned      and HTTPS port)
                      by OS)
```

**Types of sockets:**
- **TCP Socket:** Connection-oriented, reliable (like a phone call)
- **UDP Socket:** Connectionless, fast (like sending a letter)
- **Web Socket:** Full-duplex communication over a persistent TCP connection (used for real-time apps like chat)

---

### Interview Questions — Ports and Sockets

**Q: What is the difference between a port and a socket?**
> A port is a number (0–65535) that identifies a specific application or service on a device. A socket is the combination of an IP address and a port number — it uniquely identifies one endpoint of a network connection. A complete network connection is defined by two sockets (one at each end).

**Q: What port does HTTPS use?**
> HTTPS uses port 443. HTTP uses port 80.

**Q: What is a WebSocket?**
> A WebSocket is a protocol that provides full-duplex (two-way) communication over a single, persistent TCP connection. Unlike HTTP (request-response), WebSockets allow the server to push data to the client without the client requesting it. Perfect for real-time applications: chat apps, live notifications, stock price updates, online games.

---

> **Key Takeaways — Section 11**
> - Port = identifies which application on a device. IP address = identifies the device.
> - Socket = IP address + port number (one endpoint of a connection).
> - Must know: HTTP=80, HTTPS=443, SSH=22, DNS=53, MySQL=3306, PostgreSQL=5432.
> - WebSocket = persistent full-duplex connection for real-time communication.

---

## 12. REST API Networking Concepts

### Statelessness

A **stateless** API means the server does **not store any client session information** between requests. Every request must contain all the information the server needs to process it.

**Analogy:** A vending machine — every time you use it, you put in money and make a selection. It doesn't remember your previous purchases or who you are. Each interaction is completely independent.

**Implications:**
- Client must send authentication (JWT, API key) with every request
- Server is simpler and scales easily (any server can handle any request)
- No session state to synchronize across multiple servers

---

### Idempotency

An operation is **idempotent** if calling it multiple times produces the **same result** as calling it once.

**Why it matters:** In distributed systems, requests can fail and be retried. Idempotent operations are safe to retry without causing duplicate data or errors.

| HTTP Method | Idempotent | Example |
|---|---|---|
| GET | Yes | Read the same data multiple times = same result |
| PUT | Yes | Replacing a user with the same data = same result |
| DELETE | Yes | Deleting an already-deleted resource = it's still gone |
| PATCH | Usually | Depends on the operation |
| POST | No | Sending the same order twice = two orders created |

---

### HTTP Status Codes

Status codes tell the client what happened with their request.

#### 2xx — Success

| Code | Name | Meaning |
|---|---|---|
| 200 | OK | Request succeeded; data returned |
| 201 | Created | Resource was successfully created (POST) |
| 204 | No Content | Success but no data to return (DELETE) |

#### 3xx — Redirection

| Code | Name | Meaning |
|---|---|---|
| 301 | Moved Permanently | Resource has moved permanently to a new URL |
| 302 | Found | Temporary redirect |
| 304 | Not Modified | Cached version is still valid (no data sent) |

#### 4xx — Client Errors

| Code | Name | Meaning |
|---|---|---|
| 400 | Bad Request | Invalid request syntax or parameters |
| 401 | Unauthorized | Authentication required (not logged in) |
| 403 | Forbidden | Authenticated but no permission |
| 404 | Not Found | Resource does not exist |
| 405 | Method Not Allowed | HTTP method not supported for this endpoint |
| 409 | Conflict | Conflict with current state (duplicate entry) |
| 422 | Unprocessable Entity | Validation errors |
| 429 | Too Many Requests | Rate limit exceeded |

#### 5xx — Server Errors

| Code | Name | Meaning |
|---|---|---|
| 500 | Internal Server Error | Unexpected server error (bug) |
| 502 | Bad Gateway | Server received invalid response from upstream |
| 503 | Service Unavailable | Server is down or overloaded |
| 504 | Gateway Timeout | Upstream server did not respond in time |

---

### 401 vs 403

**Commonly confused in interviews:**

| | 401 Unauthorized | 403 Forbidden |
|---|---|---|
| Logged in? | No | Yes |
| Has permission? | Unknown (not authenticated) | No (authenticated, no permission) |
| Example | Accessing a page without logging in | A regular user trying to access admin panel |
| Fix | Log in | Request permission or log in as the right user |

---

### Interview Questions — REST API Concepts

**Q: What does stateless mean in REST APIs?**
> Stateless means the server does not store any session information between requests. Every request must be self-contained with all the information needed (authentication token, parameters, etc.). This makes REST APIs easy to scale because any server can handle any request without needing shared session state.

**Q: What is the difference between 401 and 403?**
> 401 Unauthorized means the user is not authenticated — they need to log in. 403 Forbidden means the user is authenticated (logged in) but does not have permission to access the resource. Think of 401 as "who are you?" and 403 as "I know who you are, but you can't come in."

**Q: What is the difference between 200 OK and 201 Created?**
> 200 OK means the request was successful and data is returned (typically for GET). 201 Created means a new resource was successfully created (typically returned for POST). Both are success codes, but 201 specifically indicates that a new resource was created as a result of the request.

---

> **Key Takeaways — Section 12**
> - Stateless = server stores no session; client sends all info with every request.
> - Idempotent = same result no matter how many times called (GET, PUT, DELETE).
> - 2xx = success, 4xx = client error, 5xx = server error.
> - 401 = not logged in. 403 = logged in but no permission.
> - 404 = not found. 500 = server bug.

---

## 13. Frequently Asked Interview Questions

These are the most commonly asked Computer Networking questions in Pakistani software company interviews.

---

**Q1: What is the difference between TCP and UDP? When would you use each?**
> TCP is connection-oriented and reliable — it guarantees complete, ordered delivery with error checking (retransmission). UDP is connectionless and fast — no delivery guarantee. Use TCP for web browsing, file transfers, email (accuracy matters). Use UDP for video streaming, gaming, VoIP, DNS (speed matters more than perfection).

---

**Q2: Explain the OSI model layers.**
> Seven layers: (1) Physical — cables and bits. (2) Data Link — MAC addresses, frames, switches. (3) Network — IP addresses, routing, packets. (4) Transport — TCP/UDP, ports, segments. (5) Session — session management. (6) Presentation — encryption, encoding. (7) Application — HTTP, DNS, FTP. Mnemonic (bottom-up): "Please Do Not Throw Sausage Pizza Away."

---

**Q3: What happens when you type www.google.com in a browser?**
> 1. Browser checks DNS cache. 2. If not cached, queries DNS resolver (ISP). 3. DNS resolver queries root → TLD (.com) → Google's authoritative name server → returns IP. 4. Browser establishes TCP connection (three-way handshake) to that IP on port 443. 5. TLS handshake for HTTPS encryption. 6. Browser sends HTTP GET request. 7. Google server responds with HTML. 8. Browser renders the page.

---

**Q4: What is a three-way handshake?**
> The three-way handshake establishes a TCP connection. Step 1: Client sends SYN with its sequence number. Step 2: Server replies with SYN-ACK (acknowledges client's sequence, sends its own). Step 3: Client sends ACK (acknowledges server's sequence). After these three steps, both sides have confirmed bidirectional communication and data transfer begins.

---

**Q5: What is the difference between HTTP and HTTPS?**
> HTTP sends data in plain text — anyone intercepting the traffic can read it. HTTPS encrypts all communication using TLS (Transport Layer Security), making interception useless. HTTPS also verifies server identity using SSL certificates from trusted Certificate Authorities. HTTP uses port 80; HTTPS uses port 443.

---

**Q6: What is DNS and how does it work?**
> DNS translates domain names to IP addresses. When you visit google.com, your browser asks a DNS resolver for the IP. The resolver queries root → TLD → authoritative name server and returns the IP. Results are cached for efficiency. Without DNS, you would need to remember IP addresses for every website.

---

**Q7: What is the difference between a cookie and a session?**
> A cookie stores data on the client (browser) and is sent with every request. A session stores data on the server; the client only holds a session ID (usually in a cookie). Sessions are more secure because sensitive data never leaves the server. Cookies are simpler but expose data to the client.

---

**Q8: What is JWT and why is it used?**
> JWT (JSON Web Token) is a self-contained signed token that encodes user data (user ID, role, expiry). The server creates it, signs it with a secret key, and sends it to the client. The client stores it and sends it with every request. The server verifies the signature without querying a database — making JWT stateless, scalable, and perfect for REST APIs and microservices.

---

**Q9: What is the difference between GET and POST?**
> GET retrieves data — parameters go in the URL, no request body, safe and idempotent. POST submits data to create a resource — data goes in the request body, not visible in URL, not idempotent. Never use GET for sensitive data (passwords go in the URL and are logged in server logs and browser history).

---

**Q10: What are HTTP status codes? Name some important ones.**
> Status codes indicate the result of an HTTP request. Key ones: 200 OK (success), 201 Created (resource created), 204 No Content (success, no data), 301 Moved Permanently, 400 Bad Request, 401 Unauthorized (not logged in), 403 Forbidden (no permission), 404 Not Found, 500 Internal Server Error, 503 Service Unavailable.

---

**Q11: What is the difference between a router and a switch?**
> A switch connects devices within the same network (LAN) using MAC addresses — it sends data only to the correct device. A router connects different networks together using IP addresses — it forwards packets between networks (e.g., from your home network to the Internet). Your home Wi-Fi box is typically both a router (connects to Internet) and a switch (connects your devices).

---

**Q12: What is a public IP vs a private IP?**
> A public IP is globally unique, assigned by your ISP, and visible on the Internet. A private IP is used only within a local network (192.168.x.x, 10.x.x.x) and is not routable on the Internet. NAT (Network Address Translation) allows all devices on a home network to share one public IP. Private IPs are free to reuse across different networks.

---

**Q13: What is the difference between PUT and PATCH?**
> PUT replaces the entire resource with the data you send — fields you omit are removed or reset. PATCH partially updates a resource — only the fields you include are changed. Use PUT when replacing a complete object; use PATCH for small updates like changing just a user's email.

---

**Q14: What is a WebSocket and when would you use it?**
> A WebSocket is a protocol that provides persistent, full-duplex (two-way) communication over a single TCP connection. Unlike HTTP (request-response), WebSockets allow the server to push data to the client instantly without the client asking. Use it for real-time applications: live chat, online games, stock price dashboards, collaborative editors (like Google Docs).

---

**Q15: What is the difference between 401 and 403 HTTP status codes?**
> 401 Unauthorized: the user is not authenticated — they haven't logged in. The server doesn't know who they are. 403 Forbidden: the user is authenticated (logged in) but lacks permission for this resource. A regular user trying to access an admin-only page gets 403, not 401.

---

> **Key Takeaways — Section 13**
> - Prepare clear 30–60 second answers for each question above.
> - Always use an analogy to explain — it shows deep understanding, not memorization.
> - The most asked questions: TCP vs UDP, OSI layers, three-way handshake, HTTP vs HTTPS, DNS lookup.
> - Know the "what happens when you type a URL" answer thoroughly — it covers many topics at once.

---

## 14. Common Mistakes

These are the most common errors students make in networking interviews. Know them and avoid them.

---

### Mistake 1: Confusing TCP and UDP Use Cases

**Wrong:** "TCP is always better because it is reliable."

**Correct:** TCP is better when accuracy matters (file downloads, web pages, emails). UDP is better when speed matters and some loss is acceptable (video calls, gaming, live streams). Using TCP for video calls would cause buffering and lag every time a packet is retransmitted — UDP's tiny data loss is invisible to the user.

---

### Mistake 2: Saying HTTP and HTTPS Are the Same Except for Speed

**Wrong:** "HTTPS is just slower HTTP."

**Correct:** HTTPS is fundamentally different in security. HTTP sends all data in plain text — interceptable by anyone on the same network. HTTPS encrypts everything with TLS. The server's identity is also verified via SSL certificates. Modern hardware makes the speed difference negligible. Always use HTTPS.

---

### Mistake 3: Confusing Cookie, Session, and JWT

**Wrong:** "Sessions and cookies are the same thing."

**Correct:**
- Cookie = data stored in the browser, sent automatically with requests.
- Session = a cookie holds only an ID; the actual data is on the server.
- JWT = a self-contained signed token; no server storage needed.

The key distinction: in sessions, sensitive data is on the server. In JWT, encoded data is on the client (readable but verified by signature).

---

### Mistake 4: Confusing IPv4 and IPv6 Address Formats

**Wrong:** Describing IPv6 with dot notation or IPv4 with colon notation.

**Correct:**
- IPv4: Four decimal numbers separated by **dots**: `192.168.1.1` (32-bit)
- IPv6: Eight groups of hex digits separated by **colons**: `2001:db8::1` (128-bit)

---

### Mistake 5: Thinking 401 and 403 Are the Same

**Wrong:** "Both mean access denied."

**Correct:**
- **401** = Not authenticated. "I don't know who you are. Please log in."
- **403** = Authenticated, but no permission. "I know who you are, but you're not allowed here."

This distinction matters a lot in API design.

---

### Mistake 6: Saying OSI and TCP/IP Are the Same

**Wrong:** "OSI and TCP/IP are the same model."

**Correct:** OSI has 7 layers — it is a theoretical reference framework. TCP/IP has 4 layers — it is the practical model that actually runs the Internet. OSI is used for understanding and troubleshooting; TCP/IP is what your computer implements.

---

### Mistake 7: Confusing PUT and PATCH

**Wrong:** "PUT and PATCH both update a resource — they're the same."

**Correct:** PUT replaces the entire resource. If you PUT `{ "name": "Ali" }` to a user with `{ "name": "Ali", "email": "ali@x.com", "age": 25 }`, the email and age get deleted. PATCH only modifies the fields you send — everything else stays unchanged.

---

### Mistake 8: Thinking DNS Only Does Web Browsing

**Wrong:** "DNS is just for finding website IP addresses."

**Correct:** DNS is used for all domain-to-IP resolution: websites, email servers (MX records), CDN routing, API endpoints, microservice discovery, load balancing, and more. Anytime a hostname needs resolving, DNS is involved.

---

> **Key Takeaways — Section 14**
> - TCP vs UDP: not about better/worse — it is about reliability vs speed trade-off.
> - 401 = not logged in. 403 = logged in, no permission. These are different problems.
> - Cookie ≠ Session ≠ JWT — different data storage locations and mechanisms.
> - PUT = replace all. PATCH = partial update. They are not interchangeable.

---

## 15. Final Revision Cheat Sheet

Use this section for quick review the morning of your interview.

---

### Core Definitions — One Line Each

| Concept | One-Line Definition |
|---|---|
| Computer Network | Two or more devices connected to share data |
| LAN | Local network (home, office) |
| WAN | Wide area network (Internet is the largest WAN) |
| Router | Connects different networks using IP addresses |
| Switch | Connects devices in the same network using MAC addresses |
| Hub | Broadcasts data to all devices (old, replaced by switches) |
| Modem | Converts digital ↔ analog signals for ISP connection |
| OSI Model | 7-layer framework for understanding network communication |
| TCP/IP Model | 4-layer practical model that runs the Internet |
| TCP | Reliable, connection-oriented, ordered delivery protocol |
| UDP | Fast, connectionless, no delivery guarantee protocol |
| IP Address | Unique numerical label identifying a device on a network |
| IPv4 | 32-bit IP address (e.g., 192.168.1.1) |
| IPv6 | 128-bit IP address (e.g., 2001:db8::1) |
| Public IP | Global, Internet-routable IP (assigned by ISP) |
| Private IP | Local network only (192.168.x.x, 10.x.x.x) |
| DNS | Translates domain names to IP addresses |
| HTTP | Plain-text protocol for web communication (port 80) |
| HTTPS | Encrypted HTTP using TLS (port 443) |
| TLS/SSL | Encryption protocol securing HTTPS connections |
| Cookie | Small data stored in the browser, sent with every request |
| Session | Server-side data; client only holds session ID |
| JWT | Signed self-contained token; stateless authentication |
| Three-Way Handshake | SYN → SYN-ACK → ACK (TCP connection setup) |
| Four-Way Termination | FIN → ACK → FIN → ACK (TCP connection teardown) |
| Port | Number identifying a specific application on a device |
| Socket | IP address + port (one endpoint of a connection) |
| Stateless | Server stores no session state between requests |
| Idempotent | Same result regardless of how many times called |
| Status Code | HTTP response number indicating success or error |
| WebSocket | Persistent full-duplex TCP connection for real-time communication |

---

### OSI Layers — Quick Reference (Bottom to Top)

```
Layer 1: Physical    → Bits, cables, Wi-Fi signals
Layer 2: Data Link   → Frames, MAC addresses, switches
Layer 3: Network     → Packets, IP addresses, routers
Layer 4: Transport   → Segments, TCP/UDP, ports
Layer 5: Session     → Session management
Layer 6: Presentation→ Encryption, encoding
Layer 7: Application → HTTP, DNS, FTP, SMTP
```

**Mnemonic (1→7):** "Please Do Not Throw Sausage Pizza Away"
**Mnemonic (7→1):** "All People Seem To Need Data Processing"

---

### TCP vs UDP — Quick Table

| | TCP | UDP |
|---|---|---|
| Connection | Yes (handshake) | No |
| Reliable | Yes | No |
| Speed | Slower | Faster |
| Use for | HTTP, FTP, SSH, SMTP | DNS, VoIP, Gaming, Streaming |

---

### HTTP Methods — Quick Reference

| Method | Purpose | Idempotent |
|---|---|---|
| GET | Read | Yes |
| POST | Create | No |
| PUT | Replace all | Yes |
| PATCH | Partial update | Usually |
| DELETE | Delete | Yes |

---

### HTTP Status Codes — Must Know

| Code | Meaning |
|---|---|
| 200 | OK — success |
| 201 | Created — resource created |
| 204 | No Content — success, nothing returned |
| 301 | Moved Permanently |
| 400 | Bad Request — invalid input |
| 401 | Unauthorized — not logged in |
| 403 | Forbidden — logged in, no permission |
| 404 | Not Found |
| 409 | Conflict — duplicate |
| 429 | Too Many Requests — rate limited |
| 500 | Internal Server Error — server bug |
| 503 | Service Unavailable |

---

### Common Port Numbers — Must Memorize

| Port | Protocol |
|---|---|
| 22 | SSH |
| 25 | SMTP (email sending) |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 27017 | MongoDB |

---

### Cookie vs Session vs JWT — Quick Table

| | Cookie | Session | JWT |
|---|---|---|---|
| Data stored at | Client | Server | Client |
| Server storage | No | Yes | No |
| Scalable | Yes | Harder | Yes |
| Invalidate easily | Yes | Yes | Hard |
| Best for | Preferences | Web apps | REST APIs |

---

### Analogy Quick Reference

| Concept | Analogy |
|---|---|
| IP Address | Home address |
| Port | Apartment number |
| Socket | Full address (building + apartment) |
| Router | Post office (routes to correct destination) |
| Switch | Office mail delivery (exact desk) |
| Hub | Loudspeaker (everyone hears everything) |
| DNS | Phone book (name → number) |
| TCP | Registered courier (confirmation, tracking) |
| UDP | Postcard (fire and forget) |
| HTTPS/TLS | Sealed envelope (encrypted, tamper-proof) |
| Cookie | ID card you carry everywhere |
| Session | Locker at the server; you carry the key |
| JWT | Self-signed document you carry |
| Three-way handshake | "Hello" → "Hello back, did you say hello?" → "Yes" |

---

### "What Happens When You Type a URL" — Full Flow

```
1. Browser cache → OS cache → DNS lookup
2. DNS: root → TLD → authoritative server → IP address
3. TCP three-way handshake (SYN → SYN-ACK → ACK)
4. TLS handshake (for HTTPS)
5. HTTP GET request sent
6. Server processes and sends HTTP response
7. Browser receives HTML, CSS, JS → renders page
8. TCP four-way termination (or keep-alive for more requests)
```

---

### Final Study Strategy

1. **First pass:** Read all sections once, understand the analogies
2. **Second pass:** Focus on sections 2 (OSI), 4 (TCP/UDP), 6 (DNS), 7 (HTTP/HTTPS), 10 (Handshake)
3. **Third pass:** Go through all interview questions and answer out loud
4. **Morning of interview:** Only read this cheat sheet
5. **In the interview:** Lead with the analogy, then the technical explanation

---

> **Final Tip for Pakistani Software Company Interviews**
>
> The most frequently asked networking topics are:
> **TCP vs UDP → OSI Layers → DNS → HTTP vs HTTPS → Three-Way Handshake → Status Codes → Cookie vs Session vs JWT**
>
> If you can explain these clearly with real-world analogies, you will stand out from most fresh graduates.

---

*End of Computer Networks Interview Study Guide*
