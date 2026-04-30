# Exploring DNS Traffic

> **Tools Used:** Wireshark, nslookup, Linux Terminal, Web Browser

---

## 📌 Objective

Capture live DNS traffic using Wireshark, then drill into both DNS query and response packets to understand the structure of each field — Transaction ID, flags, record types, TTL, and answers. Compare observations from Wireshark against `nslookup` output to confirm they surface the same information through different interfaces.

---

## 🎯 Purpose & Relevance

DNS is the breadcrumb trail of every network connection. Before any device communicates externally, it asks DNS for an address. This makes DNS logs one of the most valuable and underutilized detection sources available — visible even when all subsequent traffic is HTTPS-encrypted. This lab builds the packet-level literacy needed to recognize normal DNS behavior, which is the prerequisite for spotting abnormal DNS.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Wireshark | Live packet capture and DNS analysis |
| `nslookup` | Manual DNS queries for comparison |
| `ipconfig /flushdns` | Clear DNS cache before capture |
| Web Browser | Generate real DNS traffic |
| `arp -a` / `ifconfig` | Verify local MAC and IP for comparison |

---

## 🔬 Process / Steps Taken

### Part 1 – Capture DNS Traffic

#### Step 1 – Prepare the Environment

Flushed the DNS cache to force fresh queries:
```bash
ipconfig /flushdns      # Windows
# OR
systemd-resolve --flush-caches  # Linux
```

#### Step 2 – Start Capture and Generate DNS Traffic

Started Wireshark capture on the active network interface. Opened `nslookup` in interactive mode and queried `www.cisco.com`. Exited and stopped the capture.

<img width="1261" height="649" alt="image" src="https://github.com/user-attachments/assets/83135a8d-dfaa-40be-ac23-73e0871a1a23" />

---

### Part 2 – Explore DNS Query Traffic

Applied filter: `udp.port == 53` to isolate DNS packets.

<img width="1448" height="593" alt="image" src="https://github.com/user-attachments/assets/80a1fe23-a469-4058-b2c9-a34dcc4411ea" />

Selected the DNS packet with `Standard query` and `A www.cisco.com` in the Info column.

#### Ethernet II Layer

Expanded Ethernet II to examine MAC addresses:

<img width="1164" height="660" alt="image" src="https://github.com/user-attachments/assets/e3da5867-0662-4753-bf88-097114fda2f1" />

> **Question answered:** *What are the source and destination MAC addresses?*
> Source: PC's NIC MAC (`08:00:27:09:14:c4`) → Destination: Gateway/router MAC (`80:37:73:ea:b1:7a`)

#### IPv4 Layer

Expanded Internet Protocol Version 4:

<img width="1030" height="658" alt="image" src="https://github.com/user-attachments/assets/52daf90b-84f7-4ea5-aab2-81a03a174d6d" />

> **Question answered:** *What are the source and destination IP addresses?*
> Source: `192.168.1.16` (PC) → Destination: `192.168.1.1` (DNS resolver / gateway)

#### UDP Layer

Expanded User Datagram Protocol:

<img width="1111" height="609" alt="image" src="https://github.com/user-attachments/assets/fd228bd0-2901-4302-b549-30c3c8e5621b" />

> **Question answered:** *What are the source and destination ports? What is the default DNS port?*
> Source: `57729` (ephemeral) → Destination: `53` (DNS). Default DNS port is **53**.

> **Question answered:** *Do MAC/IP addresses in Wireshark match the PC's actual addresses?*
> They seem to be identical on both the PC and Wireshark.

#### DNS Query Section

Expanded Domain Name System (query) → Flags → Queries:

<img width="1025" height="632" alt="image" src="https://github.com/user-attachments/assets/01402e3e-6c21-4768-b223-a8ff38af750b" />

Key observations:
- **Transaction ID:** `0x0002` — matches the corresponding response
- **Flags:** `0x0100` Standard query, Recursion Desired (`RD=1`) — asks the resolver to perform the full lookup
- **Query:** `www.cisco.com`, Type: **A** (Host Address / IPv4), Class: **IN** (Internet)

---

### Part 3 – Explore DNS Response Traffic

Selected the corresponding **Standard query response** for `A www.cisco.com`.

> **Question answered:** *How do source/destination addresses compare to the query?*
> Everything is swapped — the DNS server is now the source, the PC is the destination. Same ports but reversed.

Expanded Domain Name System (response) → Flags → Queries → Answers:

<img width="1244" height="600" alt="image" src="https://github.com/user-attachments/assets/58b95add-2c4b-435e-b937-85f1fad7da50" />

The **Answers section** revealed a CNAME chain:
- `www.cisco.com` → CNAME → `www.cisco.com.akadns.net`
- `www.cisco.com.akadns.net` → CNAME → `wwwds.cisco.com.edgekey.net`
- Several more CNAME hops → final **A record: `23.52.234.158`**

> **Question answered:** *Can the DNS server do recursive queries?*
> Yes — the Recursion Available (`RA=1`) bit was set in the response flags.

> **Question answered:** *How do Wireshark results compare to nslookup?*
> They display the same results — nslookup reads the same DNS response that Wireshark captured at the packet level.

<img width="1039" height="757" alt="image" src="https://github.com/user-attachments/assets/1ec9d253-af5d-4709-a581-e83ebeb3fbf9" />

Additional packet views from the capture showing the full DNS query/response cycle:

<img width="1027" height="573" alt="image" src="https://github.com/user-attachments/assets/0fcd4ca1-a0ae-4da5-bfd9-6ca32e773cdc" />

<img width="944" height="581" alt="image" src="https://github.com/user-attachments/assets/e2d227f5-d58b-4fed-a5ac-6753c0af705b" />

---

## Reflection Questions

> **1. What else can you learn when the filter is removed?**
> *"Other packets, such as DHCP and ARP, can be found on the LAN. DHCP packets assign IP addresses to devices, while ARP packets map IP addresses to their corresponding MAC addresses."*

> **2. How can an attacker use Wireshark to compromise network security?**
> *"It can obtain sensitive information pertaining to network traffic if security parameters are not in place."*

<img width="837" height="747" alt="image" src="https://github.com/user-attachments/assets/d52ef153-5aac-4376-83e4-01791e5baabb" />

---

## 🔍 Key Findings

- DNS queries use **UDP port 53** — fast, connectionless, with no handshake overhead
- The **Transaction ID** links each query to its response — used by the resolver to match answers back to requests
- `www.cisco.com` resolved through a **5-hop CNAME chain** to a CDN edge node — this is normal for major websites using Akamai and similar CDN infrastructure
- **Recursion Desired (RD=1)** in queries tells the resolver to do the full lookup. **Recursion Available (RA=1)** in responses confirms the server supports it
- Wireshark and `nslookup` surface identical DNS data — one at the packet level, one as a human-readable query tool
- When the DNS filter was removed, DHCP, ARP, and NTP traffic also appeared — a passive observer on the LAN sees all of this without any active probing

---

## 📖 Concepts Learned

- **DNS Record Types:**
  - **A:** Maps domain → IPv4 address
  - **AAAA:** Maps domain → IPv6 address
  - **CNAME:** Alias — points one domain to another domain name
  - The CNAME chain for `www.cisco.com` is normal CDN behavior — the final A record points to an edge node, not Cisco's origin server

- **TTL in DNS Responses:** Each record includes a Time to Live — how many seconds resolvers can cache it. Low TTLs (60 seconds or less) are a **fast-flux indicator** used by malware infrastructure to rapidly change IP addresses.

- **`ipconfig /flushdns`:** Clears the local DNS resolver cache, forcing the OS to query the configured DNS server for every new lookup — essential before capture to ensure all queries are visible.

- **Security use of DNS monitoring:**
  - Queries to newly registered domains → suspicious
  - Long base64-encoded subdomains → DNS tunneling indicator
  - High NXDOMAIN volume → possible DGA malware
  - Regular periodic queries to same domain → C2 beaconing pattern

---

## 🧠 Reflection

The CNAME chain was the most educationally dense part of this lab. Five hops from `www.cisco.com` to a CDN IP — all visible in the Wireshark Answer section. This is completely normal for major websites. But it also demonstrates why domain-based blocklists need to block the CNAME chain, not just the original domain — a blocklist that only blacklists `www.cisco.com` would miss the intermediate `akadns.net` and `edgekey.net` names.

From an attacker's perspective, the Wireshark observation about what's visible without a filter is striking. DHCP traffic reveals every device joining the network. ARP reveals MAC-to-IP mappings. DNS reveals every domain being queried. A passive listener on the LAN — no active attacks, no malware — collects a comprehensive picture of the network just by watching broadcast traffic.

---

## 📎 References

- [Cisco NetAcad – IT Security & Endpoint Protection](https://www.netacad.com/)
- [RFC 1035 – Domain Names: Implementation and Specification](https://datatracker.ietf.org/doc/html/rfc1035)
- [MITRE ATT&CK – T1071.004: Application Layer Protocol: DNS](https://attack.mitre.org/techniques/T1071/004/)
- [Wireshark DNS Display Filter Reference](https://www.wireshark.org/docs/dfref/d/dns.html)
