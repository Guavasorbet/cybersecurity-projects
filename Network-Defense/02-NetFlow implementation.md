# Explore a NetFlow Implementation

> **Tools Used:** Cisco Packet Tracer, NetFlow Collector (simulated)

---

## 📌 Objective

Generate various types of network traffic and observe the corresponding **NetFlow flow records** as they appear in a simulated NetFlow collector. Understand what data each field captures, predict how multiple traffic sources will appear in the collector, and interpret TCP flag differences between request and response flows.

---

## 🎯 Purpose & Relevance

NetFlow is the foundation of network-level threat detection. Where full packet capture is storage-intensive and impractical at scale, NetFlow records the metadata of every conversation — who talked to whom, for how long, how much data — at a fraction of the cost. SIEM platforms ingest NetFlow to detect data exfiltration, lateral movement, port scanning, and C2 beaconing. This lab builds direct hands-on familiarity with the data structure that powers those detections.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Cisco Packet Tracer | Network simulation |
| NetFlow Collector (PC at 10.0.0.100) | Receives and displays flow records |
| Edge Router | NetFlow exporter |
| PC-1 through PC-4 | Traffic generators |
| Web Server (www.example.com / 192.0.2.100) | HTTP traffic target |

---

## 🔬 Process / Steps Taken

### Part 1 – Observe NetFlow Flow Records (One Direction)

#### Step 1 – Open the NetFlow Collector
Opened **NetFlow Collector > Desktop > Netflow Collector icon**. Clicked **On** to activate. Positioned the window alongside the topology.

#### Step 2 – Ping from PC-1 to Default Gateway

On PC-1, ran:
```
C:\> ping 10.0.0.1
```

After a brief delay, the NetFlow Collector displayed a **pie chart** with a single color slice representing the ICMP flow.

<img width="721" height="506" alt="image" src="https://github.com/user-attachments/assets/63ecd7a3-fa50-46e8-867d-6d3a910068df" />

Clicking the pie chart revealed the full flow record:

| Field | Value | Explanation |
|-------|-------|-------------|
| Traffic contribution | 100% (1/1) | Only flow in the collector |
| IPV4 SOURCE ADDRESS | 10.0.0.10 | PC-1's IP address |
| IPV4 DESTINATION ADDRESS | 10.0.0.1 | Default gateway |
| TRNS SOURCE PORT | 0 | ICMP has no ports |
| TRNS DESTINATION PORT | 0 | ICMP has no ports |
| IP PROTOCOL | 1 | Protocol 1 = ICMP |
| timestamp first | 01:38:23.022 | Flow start |
| timestamp last | 01:38:26.042 | Flow end |
| tcp flags | 0x00 | No TCP session (ICMP flow) |
| counter bytes | 512 | 4 packets × 128 bytes |
| counter packets | 4 | Four ping packets |
| interface input | Gig0/0 | LAN-facing router interface |
| interface output | Null | Ping to input interface — no outbound |

#### Step 3 – Create Additional Traffic from Multiple PCs

Pinged the default gateway from PC-2, PC-3, and PC-4 in sequence.

**Predicted and observed:** Each new source PC created a **new distinct flow record** (new color slice in the pie chart). Repeating a ping from PC-1 updated the byte/packet counters of the existing PC-1 flow rather than creating a new one — because source IP, destination IP, and protocol matched the existing 5-tuple.

<img width="962" height="593" alt="image" src="https://github.com/user-attachments/assets/b61dd8e8-6ac6-4804-9395-099354a0b59d" />

> **Question answered in lab:** *What happens when PC-2 pings?*
> A new flow record appears — new color-coded portion of the pie chart. Previous records remain.
>
> *What happens when PC-1 pings again?*
> The original PC-1 flow record's byte and packet counters update. Proportion shifts but no new flow.
>
> *What happens with PC-3 and PC-4?*
> Four total records display — one per unique source IP.

---

### Part 2 – NetFlow Records for Bidirectional HTTP Session

#### Step 1 – Access Web Server by IP Address

After power-cycling the collector to clear flows, opened PC-1's **Web Browser** and navigated to `192.0.2.100`. The Example Website page loaded.

<img width="906" height="328" alt="image" src="https://github.com/user-attachments/assets/3ed73d0a-7da6-42c1-aee4-c5748ad25bd9" />

Two flows appeared — one for the HTTP request leaving the LAN and one for the response entering from the internet. Clicked each slice to verify the fields:

**Predicted values for the web request (LAN → internet):**

| Field | Predicted | Observed |
|-------|-----------|----------|
| Source IP | 10.0.0.10 | ✓ |
| Destination IP | 192.0.2.100 | ✓ |
| Source Port | 1025–5000 (ephemeral) | ✓ |
| Destination Port | 80 | ✓ |
| Input Interface | Gig0/0 | ✓ |
| Output Interface | Ser0/0/1 | ✓ |

**Predicted values for the web reply (internet → LAN):**

| Field | Predicted | Observed |
|-------|-----------|----------|
| Source IP | 192.0.2.100 | ✓ |
| Destination IP | 10.0.0.10 | ✓ |
| Source Port | 80 | ✓ |
| Destination Port | ephemeral | ✓ |
| Input Interface | Ser0/0/1 | ✓ |
| Output Interface | Gig0/0 | ✓ |

> **Question answered in lab:** *What is different between request and response flows beyond the obvious?*
> The **TCP flags differ**: request flows carry `0x02` (SYN), while response flows carry `0x12` (SYN-ACK). This reveals the direction and state of the TCP handshake.

#### Step 2 – Access Web Server by URL

Cleared the collector, then navigated to `www.example.com` in the browser.

> **Question answered in lab:** *What do you expect to see?*
> Four flows: two for the DNS query and response (UDP protocol 17), and two for the HTTP request and response (TCP protocol 6) — because accessing via URL requires a DNS lookup first.

> **Question answered in lab:** *What IP protocol values appear?*
> Protocol **6** for TCP (HTTP traffic on port 80) and protocol **17** for UDP (DNS query/response).

The Copyrights page link generated two additional flows — because clicking a new link opened a **new source port** on the client for the new HTTP request.

---

## 🔍 Key Findings

- Each unique **5-tuple** (src IP, dst IP, src port, dst port, protocol) generates one distinct flow record
- Repeated traffic from the same source to the same destination **updates** existing flow counters rather than creating new records
- A URL-based web request generates **4 flows** minimum: DNS query, DNS response, HTTP request, HTTP response — NetFlow captures all of them
- **TCP flags in flow records** reveal session state: `0x02` = SYN (connection initiation), `0x12` = SYN-ACK (server response), `0x10` = ACK (acknowledgment)
- The NetFlow collector's pie chart provides an immediate visual of **traffic distribution by source** — a primary tool for identifying top talkers and anomalies

---

## 📖 Concepts Learned

- **5-tuple:** Source IP, Destination IP, Source Port, Destination Port, IP Protocol. The unique identifier for a unidirectional flow. Any change to any field creates a new flow record.
- **IP Protocol Numbers:** 1 = ICMP, 6 = TCP, 17 = UDP. These appear in the flow record's IP PROTOCOL field and allow filtering by transport type.
- **Unidirectional flows:** NetFlow records flows in one direction. A full conversation (request + response) generates two separate flow records — critical to understand when correlating inbound and outbound traffic.
- **Power cycling the collector:** Used to clear all existing flow records before a new test — equivalent to starting a fresh Wireshark capture with no prior history.
- **Security use case — DNS then HTTP:** In a malware scenario, the DNS query to a C2 domain appears in flow records *before* the actual C2 HTTP connection. Monitoring DNS flows enables earlier detection than waiting for the TCP connection to establish.

---

## 🧠 Reflection

The most valuable insight from this lab was the TCP flags field. Most NetFlow analysis tools display source IP and bytes — but the flag values tell the story of the session state. A flow showing only `0x02` (SYN) with no matching `0x12` from the server means the connection was refused or filtered. Hundreds of SYN flows to different destination ports from one source IP is a port scan. Knowing how to read TCP flags in flow records turns raw metadata into behavioral intelligence.

The four-flow DNS + HTTP pattern also reinforced why DNS monitoring is indispensable. Every external connection generates a DNS query first — and DNS is still plaintext on most networks, making it readable even when the subsequent HTTP/HTTPS traffic is encrypted.

---

## 📎 References

- [Cisco NetAcad – Network Defense](https://www.netacad.com/)
- [Cisco – Introduction to NetFlow](https://www.cisco.com/c/en/us/products/ios-nx-os-software/ios-netflow/index.html)
- [RFC 7011 – IPFIX Protocol Specification](https://datatracker.ietf.org/doc/html/rfc7011)
