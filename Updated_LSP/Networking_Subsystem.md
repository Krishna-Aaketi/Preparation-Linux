# 🌐 NETWORKING STACK BASICS

*(Subsystem 8 of 10)*

---

### 1. What is the Linux network stack?

A layered subsystem in the kernel that handles data transmission and reception, implementing protocols like Ethernet, IP, TCP/UDP, and sockets.

---

### 2. What are the main layers of the Linux network stack?

| Layer               | Example Components   | Description                           |
| ------------------- | -------------------- | ------------------------------------- |
| **User Space**      | Sockets API, libpcap | Apps use sockets to send/receive data |
| **Socket Layer**    | BSD socket interface | Converts user requests → protocol ops |
| **Transport Layer** | TCP, UDP, SCTP       | Provides reliable/unreliable delivery |
| **Network Layer**   | IPv4/IPv6            | Routing, fragmentation, addressing    |
| **Link Layer**      | Ethernet, Wi-Fi, PPP | Frame Tx/Rx, MAC addresses            |
| **Driver/Hardware** | NIC drivers          | Interface to physical device          |

---

### 3. What is a socket?

A software endpoint for bidirectional communication.
Each socket is defined by:

```
<IP address, Port number, Protocol (TCP/UDP)>
```

---

### 4. What is a network interface?

A logical representation of a physical or virtual NIC (e.g., `eth0`, `wlan0`, `lo`).
It connects the kernel network stack to the outside network.

---

### 5. What commands list interfaces and status?

`ip addr show`, `ifconfig -a`, `ip link`, `ethtool`

---

### 6. What is the loopback interface?

`lo (127.0.0.1)` — used for local host communication; packets never leave the machine.

---

### 7. What is the difference between TCP and UDP?

| Feature     | TCP        | UDP                  |
| ----------- | ---------- | -------------------- |
| Connection  | Yes        | No                   |
| Reliability | Guaranteed | None                 |
| Ordering    | Preserved  | Not preserved        |
| Overhead    | High       | Low                  |
| Use         | Web, SSH   | DNS, VoIP, Streaming |

---

### 8. What is the difference between IPv4 and IPv6?

| Feature      | IPv4        | IPv6                    |
| ------------ | ----------- | ----------------------- |
| Address Size | 32 bits     | 128 bits                |
| Example      | 192.168.0.1 | fe80::1                 |
| Header       | Simple      | Larger with extensions  |
| NAT          | Common      | Not needed (huge space) |

---

### 9. What is the OSI model vs TCP/IP model?

The OSI model has **7 layers**; TCP/IP has **4 (merged)**.
Linux follows TCP/IP — **Application → Transport → Internet → Link**.

---

### 10. What is the role of the `net_device` structure?

Kernel object representing each NIC; stores name, MAC, MTU, statistics, and function pointers for transmit/receive.

---

### 11. What is a packet socket?

A raw socket (`AF_PACKET`) that allows applications to directly send/receive Ethernet frames (bypassing TCP/IP stack).

---

### 12. What is the difference between raw and datagram sockets?

* **Raw:** gives access to entire packet headers (IP, ICMP, etc.)
* **Datagram:** provides transport layer services (UDP)

---

### 13. What is a MAC address?

A **48-bit hardware identifier** unique to every network interface.

---

### 14. What is MTU (Maximum Transmission Unit)?

The largest frame size (bytes) that can be sent on a link without fragmentation (default **1500** on Ethernet).

---

### 15. What is ARP (Address Resolution Protocol)?

Maps IPv4 addresses to MAC addresses within a local network.

---

### 16. What is NDP (Neighbor Discovery Protocol)?

IPv6 replacement for ARP; handles address resolution, autoconfiguration, and reachability.

---

### 17. What is the routing table?

Kernel data structure defining where to forward packets based on destination IP.
View using:

```
ip route
```

---

### 18. How does Linux forward a packet?

If `ip_forward=1`, the kernel uses the routing table to decide the next hop and forwards the packet via the correct interface.

---

### 19. What is netfilter / iptables / nftables?

Linux packet-filtering framework inside the kernel.
Used for **firewalls, NAT, and packet mangling.**
Controlled by `iptables` (legacy) or `nftables` (modern).

---

### 20. What are the Netfilter hook points?

`PREROUTING`, `INPUT`, `FORWARD`, `OUTPUT`, `POSTROUTING` — where rules can intercept packets within the stack.

---

### 21. What is NAT (Network Address Translation)?

Translates private IP addresses to public ones at the router/firewall.
Implemented via Netfilter’s **nat table**.

---

### 22. What is conntrack (connection tracking)?

Kernel module that keeps state of each connection (used by NAT and firewalls for stateful filtering).

---

### 23. What is `/proc/net` used for?

Exposes kernel network information like connections, routing, protocol stats, and interfaces.

---

### 24. What is a socket buffer (`sk_buff`)?

The core data structure representing a packet in kernel network stack.
Contains headers, payload, timestamps, and metadata.

---

### 25. What is the difference between SoftIRQ and NAPI in networking?

* **SoftIRQ:** older interrupt bottom half for packet Rx/Tx.
* **NAPI (New API):** hybrid interrupt + polling model reducing CPU load under high traffic.

---

### 26. What is NAPI polling mode?

When traffic is heavy, NIC interrupts are disabled temporarily and kernel polls for batches of packets to improve efficiency.

---

### 27. What is a network namespace?

An isolated instance of the network stack with its own interfaces, routes, and firewall rules — used in containers.

---

### 28. What is a virtual interface (VIF)?

A software-only interface (e.g., `tun0`, `tap0`, `veth0`) used for VPNs, bridges, or containers.

---

### 29. What is a bridge in Linux networking?

A virtual switch connecting multiple interfaces at **Layer 2 (Ethernet)**.
Created with:

```
brctl addbr br0
```

or

```
ip link add name br0 type bridge
```

---

### 30. What is a bonded interface?

Combines multiple NICs for fault tolerance or higher throughput.
Controlled via **bonding driver**.

---

### 31. What is a VLAN?

A **Virtual LAN** adds logical network segmentation using **802.1Q tags**.
Configured as sub-interfaces (e.g., `eth0.10`).

---

### 32. What is the purpose of `ethtool`?

To query and configure NIC settings (speed, duplex, offload, link status).

---

### 33. What is the purpose of `tcpdump` / `wireshark`?

Packet-capture tools that monitor network traffic using raw packet sockets (`libpcap`).

---

### 34. What is the difference between ping and traceroute?

| Tool           | Purpose                                                    |
| -------------- | ---------------------------------------------------------- |
| **ping**       | Uses ICMP Echo to check reachability                       |
| **traceroute** | Uses incremental TTL to trace route path and delay per hop |

---

### 35. What is the role of TCP states?

Represent connection phases:
`LISTEN`, `SYN_SENT`, `ESTABLISHED`, `FIN_WAIT`, `TIME_WAIT`, `CLOSED`.

---

### 36. What is the TCP 3-way handshake?

1. Client sends **SYN**
2. Server replies **SYN-ACK**
3. Client sends **ACK** → Connection established

---

### 37. What is TCP congestion control?

Algorithm that prevents network overload by adjusting send rate.
Common algorithms: **CUBIC**, **BBR**, **Reno** (Linux supports all).

---

### 38. What is a socket buffer queue?

Each socket has Rx/Tx queues of `sk_buff` packets.
Full queues cause drops or back-pressure on senders.

---

### 39. What is checksum offloading?

NIC hardware computes checksums for TCP/UDP packets → reduces CPU usage.

---

### 40. What is TSO / GSO / LRO?

Advanced NIC offload features:

| Feature | Meaning                      |
| ------- | ---------------------------- |
| **TSO** | TCP Segmentation Offload     |
| **GSO** | Generic Segmentation Offload |
| **LRO** | Large Receive Offload        |

---

### 41. What is a netlink socket?

Special socket type for kernel ↔ user-space communication (network config, routing, etc.).
Used by the `ip` command.

---

### 42. What is PF_PACKET vs AF_INET socket?

| Socket Type   | Layer         | Description                         |
| ------------- | ------------- | ----------------------------------- |
| **PF_PACKET** | Link layer    | Low-level raw frames                |
| **AF_INET**   | Network layer | Standard IP-level sockets (TCP/UDP) |

---

### 43. What is the purpose of `/proc/net/dev`?

Shows per-interface statistics (packets sent, received, errors).

---

### 44. How does Linux handle incoming packets?

1. NIC receives frame → DMA to RAM
2. Driver creates `sk_buff`
3. Packet passes through Netfilter
4. Delivered to socket or forwarded

---

### 45. How does Linux handle outgoing packets?

1. User writes to socket
2. Protocol adds headers (TCP/IP)
3. Routing chooses interface
4. Driver transmits to NIC

---

### 46. What is QoS (Quality of Service)?

Mechanisms to prioritize traffic classes using traffic control (`qdisc`, `tc` command).

---

### 47. What is a firewall chain in iptables?

A sequence of rules evaluated on packets — **INPUT**, **FORWARD**, **OUTPUT** — to accept, drop, or modify packets.

---

### 48. What is `conntrack -tools`?

User-space utility to view/manage kernel **connection-tracking** entries.

---

### 49. What is bridge-nf call?

Feature that lets Netfilter inspect **Layer 2 bridge traffic** for firewalling in bridged networks.

---

### 50. What are key network performance metrics?

* Throughput (bits/sec)
* Latency (delay)
* Packet loss rate
* Jitter (delay variation)
* CPU utilization per packet rate

---
