# Week 05 – Internetworking

## Task 1 – Knowledge Test

Completed the Week 5 Knowledge Test within the first 10 minutes of the tutorial.

## Task 2 – View Routing Table

Command used in PowerShell:

```powershell
route print
```

![Windows IPv4 routing table](week05/week5-task2-route-print.png)

The active interface is my Wi-Fi adapter (Intel Wireless-AC 9560) with IP `10.255.57.245` on `10.255.57.0/24`.

| Destination | Netmask | Gateway | Interface | Metric | How to read the row |
|---|---|---|---|---|---|
| 0.0.0.0 | 0.0.0.0 | 10.255.57.74 | 10.255.57.245 | 35 | **Default route** – anything with no more specific match is sent to the router 10.255.57.74. |
| 10.255.57.0 | 255.255.255.0 | On-link | 10.255.57.245 | 291 | **Local subnet route** – hosts in 10.255.57.0/24 are reached directly (using ARP), no router needed. |
| 10.255.57.245 | 255.255.255.255 | On-link | 10.255.57.245 | 291 | **Host route** for this machine's own IP address. |
| 10.255.57.255 | 255.255.255.255 | On-link | 10.255.57.245 | 291 | **Subnet broadcast** address of the local network. |
| 127.0.0.0 | 255.0.0.0 | On-link | 127.0.0.1 | 331 | **Loopback network** – traffic stays inside this computer. |
| 127.0.0.1 | 255.255.255.255 | On-link | 127.0.0.1 | 331 | The **loopback address** itself. |
| 127.255.255.255 | 255.255.255.255 | On-link | 127.0.0.1 | 331 | Loopback broadcast address. |
| 224.0.0.0 | 240.0.0.0 | On-link | 127.0.0.1 | 331 | **Multicast** range via the loopback interface (local group membership). |
| 224.0.0.0 | 240.0.0.0 | On-link | 10.255.57.245 | 291 | Same multicast range, sent out of the Wi-Fi interface. |
| 255.255.255.255 | 255.255.255.255 | On-link | 127.0.0.1 | 331 | **Limited broadcast** via loopback. |
| 255.255.255.255 | 255.255.255.255 | On-link | 10.255.57.245 | 291 | Limited broadcast via Wi-Fi (e.g. DHCP discovery). |

**IPv6 routes (same screenshot)**

| Network destination | Gateway | Interface / metric | How to read the row |
|---|---|---|---|
| ::/0 | fe80::d4e6:7aff:fe9c:bbea | 9 / 51 | **IPv6 default route** – unmatched traffic goes to the router's link-local address (the router MAC `d6:e6:7a:9c:bb:ea` is embedded in this address). |
| ::1/128 | On-link | 1 / 331 | IPv6 **loopback** address. |
| 2402:3a80:4679:c767::/64 | On-link | 9 / 51 | The **local IPv6 subnet** (global prefix) reached directly. |
| 2402:3a80:4679:c767:… /128 (two rows) | On-link | 9 / 291 | **Host routes** for this PC's own global IPv6 addresses. |
| fe80::/64 | On-link | 9 / 291 | **Link-local** subnet – neighbours on the same physical link. |
| fe80::c299:…/128 | On-link | 9 / 291 | Host route for this PC's own **link-local** address. |
| ff00::/8 (two rows) | On-link | 1 / 331 and 9 / 291 | IPv6 **multicast** range (via loopback and via Wi-Fi). |

**Notes:** the default gateway is `10.255.57.74`, reached through the Wi-Fi adapter. The multicast and broadcast rows appear twice because Windows keeps a loopback path (metric 331) and a real-network path (metric 291); the lower metric wins for outbound traffic. No static IPv6 default gateway is shown – the `::/0` route uses a link-local next hop, which is consistent with SLAAC.

## Task 3 – IP Network Design

**Team members:** Zeel Pareshbhai Jani (12331098) and Drashti Patel (12329332).

**IP addressing basis:** LAN 1 uses the last four digits of my student ID (…**1098** → `10.98.0.0/24`). LAN 2 uses my partner's last four digits (…**9332** → `93.32.0.0/24`). The point-to-point WAN link between the routers uses `172.16.100.0/24`. All networks are IPv4 with a /24 mask.

### a) Table of devices and addresses

| Device | Role | Interface | IP address | Mask | MAC address |
|---|---|---|---|---|---|
| PC1 | Host, LAN 1 | NIC | 10.98.0.11 | 255.255.255.0 | 00:1A:2B:10:98:11 |
| PC2 | Host, LAN 1 | NIC | 10.98.0.12 | 255.255.255.0 | 00:1A:2B:10:98:12 |
| PC3 | Host, LAN 1 | NIC | 10.98.0.13 | 255.255.255.0 | 00:1A:2B:10:98:13 |
| Switch 1 | L2 switch, LAN 1 | – | (no IP) | – | – |
| Router 1 | Gateway LAN 1 ↔ WAN | Gi0/0 (LAN) | 10.98.0.1 | 255.255.255.0 | 00:1A:2B:10:98:01 |
| Router 1 | Gateway LAN 1 ↔ WAN | Gi0/1 (WAN) | 172.16.100.1 | 255.255.255.0 | 00:1A:2B:10:98:02 |
| Router 2 | Gateway WAN ↔ LAN 2 | Gi0/1 (WAN) | 172.16.100.2 | 255.255.255.0 | 00:1A:2B:93:32:02 |
| Router 2 | Gateway WAN ↔ LAN 2 | Gi0/0 (LAN) | 93.32.0.1 | 255.255.255.0 | 00:1A:2B:93:32:01 |
| Switch 2 | L2 switch, LAN 2 | – | (no IP) | – | – |
| PC4 | Host, LAN 2 | NIC | 93.32.0.11 | 255.255.255.0 | 00:1A:2B:93:32:11 |
| PC5 | Host, LAN 2 | NIC | 93.32.0.12 | 255.255.255.0 | 00:1A:2B:93:32:12 |

Default gateway for PC1–PC3 is `10.98.0.1`; for PC4–PC5 it is `93.32.0.1`. (MAC addresses are chosen for the design; every interface has its own MAC.)

### b) Network diagram

![Test network diagram](week05/week5-task3-network.png)

- Image: [`week5-task3-network.png`](week05/week5-task3-network.png)
- Source: [`week5-task3-network.drawio`](week05/week5-task3-network.drawio)

### c) Routing tables

**Router 1**

| Destination | Netmask | Gateway | Interface | Metric |
|---|---|---|---|---|
| 10.98.0.0 | 255.255.255.0 | On-link | Gi0/0 (10.98.0.1) | 0 |
| 172.16.100.0 | 255.255.255.0 | On-link | Gi0/1 (172.16.100.1) | 0 |
| 93.32.0.0 | 255.255.255.0 | 172.16.100.2 | Gi0/1 (172.16.100.1) | 1 |

**Router 2**

| Destination | Netmask | Gateway | Interface | Metric |
|---|---|---|---|---|
| 93.32.0.0 | 255.255.255.0 | On-link | Gi0/0 (93.32.0.1) | 0 |
| 172.16.100.0 | 255.255.255.0 | On-link | Gi0/1 (172.16.100.2) | 0 |
| 10.98.0.0 | 255.255.255.0 | 172.16.100.1 | Gi0/1 (172.16.100.2) | 1 |

**PC1 (PC2 and PC3 are identical apart from their own IP address)**

| Destination | Netmask | Gateway | Interface |
|---|---|---|---|
| 10.98.0.0 | 255.255.255.0 | On-link | 10.98.0.11 |
| 0.0.0.0 | 0.0.0.0 | 10.98.0.1 | 10.98.0.11 |

**PC4 (PC5 is identical apart from its own IP address)**

| Destination | Netmask | Gateway | Interface |
|---|---|---|---|
| 93.32.0.0 | 255.255.255.0 | On-link | 93.32.0.11 |
| 0.0.0.0 | 0.0.0.0 | 93.32.0.1 | 93.32.0.11 |

The switches are Layer 2 devices and have no IP routing table.

### d) Packet diagram – ICMP echo request captured at a router

![ICMP/IP packet captured at Router 1](week05/week5-task3-packet.png)

- Image: [`week5-task3-packet.png`](week05/week5-task3-packet.png)
- Source: [`week5-task3-packet.drawio`](week05/week5-task3-packet.drawio)

Captured at Router 1's WAN interface (Gi0/1), carrying an ICMP Echo Request from PC1 to PC4:

- **IP addresses stay the same end-to-end:** source `10.98.0.11`, destination `93.32.0.11`.
- **MAC addresses change at every hop.** In the Ethernet frame captured on the WAN link, the source MAC is **Router 1's Gi0/1 interface** and the destination MAC is **Router 2's Gi0/1 interface** – not the PCs. On LAN 1 the frame is PC1 → Router 1 Gi0/0 (the default gateway); on LAN 2 it is Router 2 Gi0/0 → PC4.
- **TTL decrements by 1 at each router** (64 at PC1, 63 on the WAN link).

## Task 4 – Academic Integrity Outcomes

**Scenario: Copying from the Internet**

The student committed plagiarism by copying information from the Internet without proper referencing. They could have used their own words and correctly referenced the sources.

The breach level depends on the student's intention and circumstances. CQU may give a warning or reduce marks for a minor breach, while deliberate plagiarism may lead to more serious penalties.

I think the policy is fair because it considers the seriousness of the breach. Even if the student is not caught immediately, the misconduct may be discovered later and affect their academic progress.

**Recommendations:**

1. Always reference sources correctly.
2. Ask the lecturer for help when unsure about academic integrity.


## Task 5 – IP Address Lookup

> **TODO:** complete with results from two different networks (see notes below).
