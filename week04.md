# Week 04 – Network Technologies

## Task 1 – Knowledge Test

Completed the Week 4 Knowledge Test within the first 10 minutes of the tutorial.

## Task 2 – Project Initiation

I opened the **Group Formation – Project [30%]** tile on Moodle and selected the group listed under my tutor, **Dr David Ling**, which matches the tutorial class I attend every week. I joined a group of two students together with **Drashti Patel**. Group membership was confirmed on the Moodle group formation page before I continued with the remaining tasks.

## Task 3 – Draw Network Diagrams

Both diagrams were drawn in diagrams.net (draw.io) and include my name.

### a) Switched LAN – one switch, four PCs

![Task 3a – one switch, four PCs](week04/week4-task3-lana.png)

- Image: [`week4-task3-lana.png`](week04/week4-task3-lana.png)
- Source: [`week4-task3-lana.drawio`](week04/week4-task3-lana.drawio)

Switch 1 connects PC1–PC4. All PCs are on the `192.168.1.0/24` network (`192.168.1.1` – `192.168.1.4`), forming a basic switched LAN in a star layout.

### b) Switched LAN – three switches, eight PCs (star topology)

![Task 3b – three switches, eight PCs](week04/week4-task3-lanb.png)

- Image: [`week4-task3-lanb.png`](week04/week4-task3-lanb.png)
- Source: [`week4-task3-lanb.drawio`](week04/week4-task3-lanb.drawio)

Switch 1 connects PC1–PC4 and Switch 2 connects PC5–PC8. Both switches uplink to the central Switch 3, giving a star topology of switches (8 PCs, 3 switches).

## Task 4 – Analyse Ping Packet Capture

I pinged the default gateway (`10.255.57.74`) from my PC while capturing in Wireshark, then opened the capture and inspected the packets.

### a) Inspecting the packets

Many packets look alike (repeated echo requests/replies), so I concentrated on the packets that differ: the **ARP request/reply** pair (address resolution) and the first **ICMP Echo Request / Echo Reply** pair (the ping itself). I filtered the capture on `arp || icmp` to isolate this conversation.

### b) Network diagram

![Task 4b – network diagram with IP and MAC addresses](week04/week4-task4-ping.png)

- Image: [`week4-task4-ping.png`](week04/week4-task4-ping.png)
- Source: [`week4-task4-ping.drawio`](week04/week4-task4-ping.drawio)

| Device | IP address | MAC address |
|---|---|---|
| My PC (laptop) | 10.255.57.245 | e0:d4:e8:f3:5f:07 |
| Default gateway / router | 10.255.57.74 | d6:e6:7a:9c:bb:ea |

### c) Purpose of the ARP packets

The Address Resolution Protocol (ARP) maps a known IPv4 address to the MAC address needed to deliver an Ethernet frame on the local network. IP routing works with IP addresses, but delivery of a frame on a LAN needs the destination's MAC address.

- **Who sent it:** the default gateway, `10.255.57.74` (MAC `d6:e6:7a:9c:bb:ea`).
- **Who it was sent to:** the request was broadcast to the whole LAN (`ff:ff:ff:ff:ff:ff`) asking for my PC, `10.255.57.245` (MAC `e0:d4:e8:f3:5f:07`). My PC answered directly (unicast) with an ARP reply containing its MAC address.
- **Why:** the gateway asked *"who has 10.255.57.245? tell 10.255.57.74"* to (re)confirm my PC's MAC address in its ARP table, so it can correctly address Ethernet frames – including the ICMP echo replies – to my PC.

### d) Packet diagram – 1st ARP packet

![Task 4d – ARP packet encapsulation](week04/week4-task4-arp-packet.png)

- Image: [`week4-task4-arp-packet.png`](week04/week4-task4-arp-packet.png)
- Source: [`week4-task4-arp-packet.drawio`](week04/week4-task4-arp-packet.drawio)

| Part | Size |
|---|---|
| Ethernet II header (dst MAC 6 + src MAC 6 + type 2) | 14 bytes |
| ARP message (data) | 28 bytes |
| **Captured frame (what Wireshark shows)** | **42 bytes** |
| Padding added by the NIC (minimum frame is 60 bytes before FCS) | 18 bytes |
| FCS / footer (added by the NIC, not in the capture) | 4 bytes |
| **On the wire** | **64 bytes** |

### e) First two ICMP packets

They are the first ping request/reply pair between my PC and the default gateway.

- **Packet 1 – Echo Request:** sent from my PC (`10.255.57.245`) to the gateway (`10.255.57.74`). ICMP Type 8, Code 0, Identifier `0x1`, Sequence `0x6`, with a 32-byte data payload. It asks the gateway to respond, which tests reachability.
- **Packet 2 – Echo Reply:** sent from the gateway (`10.255.57.74`) back to my PC (`10.255.57.245`). ICMP Type 0, Code 0, with the same Identifier `0x1` and Sequence `0x6`, echoing the same 32-byte payload. It confirms the gateway is reachable and responding correctly.

### f) Packet diagram – 1st ICMP packet

![Task 4f – ICMP echo request encapsulation](week04/week4-task4-icmp-packet.png)

- Image: [`week4-task4-icmp-packet.png`](week04/week4-task4-icmp-packet.png)
- Source: [`week4-task4-icmp-packet.drawio`](week04/week4-task4-icmp-packet.drawio)

| Part | Size |
|---|---|
| Ethernet II header | 14 bytes |
| IPv4 header | 20 bytes |
| ICMP header (type, code, checksum, identifier, sequence) | 8 bytes |
| ICMP data (payload) | 32 bytes |
| **Captured frame** | **74 bytes** |
| FCS / footer (added by the NIC, not in the capture) | 4 bytes |
| **On the wire** | **78 bytes** |

