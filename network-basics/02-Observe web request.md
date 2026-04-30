# Lab – Observe Web Requests

> **Tools Used:** Cisco Packet Tracer – Simulation Mode, Web Browser (simulated)

---

## 📌 Objective

Use Cisco Packet Tracer to observe the full sequence of network events when a client requests a web page — from DNS resolution through TCP connection establishment, HTTP request/response, and the resulting HTML rendered in the browser. Examine both the HTML source on the server side and the rendered output on the client side.

---

## 🎯 Purpose & Relevance

Every web-based attack — phishing delivery, drive-by downloads, C2 callbacks — rides on the same HTTP/HTTPS transaction examined in this lab. Understanding the normal baseline of a web request is what makes abnormal traffic visible. This lab also demonstrates why DNS monitoring is so powerful: the domain query arrives before the connection, making it the earliest possible detection point.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Cisco Packet Tracer | Network simulation |
| Simulation Mode | Step-by-step PDU inspection |
| Web Browser (simulated) | HTTP client |
| Command Prompt (simulated) | Ping and DNS verification |
| ciscolearn.web.com server | HTTP/HTML source inspection |

---

## 🔬 Process / Steps Taken

### Part 1 – Verify Connectivity to the Web Server

Opened **External Client > Desktop > Command Prompt** and issued a ping to the domain name:

```
PC> ping ciscolearn.web.com
```

The ping output revealed the resolved IP address (`172.16.15.200`) returned by the DNS server. This confirmed DNS was functional and the server was reachable.

**Observation:** The first ping timed out — DNS resolution happened, then the first ICMP Echo Request was lost before the ARP resolved. The remaining 3 replies succeeded.

### Part 2 – Connect to the Web Server

Opened the simulated **Web Browser** and navigated to `ciscolearn.web.com`. The Cisco Packet Tracer welcome page loaded, displaying quick links including A small page, Copyrights, Image page, and Image.

### Part 3 – View the HTML Source

Navigated to the `ciscolearn.web.com` server in the topology and opened **Services > HTTP > index.html (edit)**. The raw HTML markup was visible — the same code that the browser rendered for the client. Comparing the server-side source to the browser-rendered output made the HTTP request/response cycle concrete.

### Part 4 – Observe Traffic Between Client and Web Server

Switched to **Simulation Mode** and configured filters to show only **TCP** and **HTTP** protocols (Misc tab in Edit Filters).

Created a **Complex PDU**:
- Source: External Client
- Application: HTTP
- Destination: ciscolearn.web.com server
- Starting Source Port: 1000
- Simulation Settings: Periodic Interval, 120 seconds

Clicked **Play** and observed the simulation. The Event List filled with packets — far more than most users ever imagine for a single page load.

**Buffer Full dialog appeared** — clicked **View Previous Events** to review the full event list.

<img width="1154" height="726" alt="image" src="https://github.com/user-attachments/assets/fb8ffee0-8e49-45f1-abdc-ccd5584d6316" />


**Observation from Event List:** HTTP uses TCP, which requires connection establishment (SYN/SYN-ACK/ACK) and acknowledgment of each segment. A single page load generated significantly more packets than the actual HTML data — all the TCP overhead made the total traffic volume much larger than the content itself.

<img width="717" height="201" alt="image" src="https://github.com/user-attachments/assets/253d487d-9beb-4e9c-b05c-c09391af93ac" />

---

## 🔍 Key Findings

- The ping to `ciscolearn.web.com` resolved the IP via DNS before any ICMP packet was sent — DNS always fires first
- The **first ping packet timed out** — this was due to ARP resolution occurring simultaneously with the first ICMP attempt
- The **Buffer Full** dialog confirmed that a large number of packets were generated for even a simple web page — HTTP/TCP overhead is significant
- Viewing the HTML source on the server demonstrated a direct 1:1 mapping: the markup on the server is exactly what the browser rendered as a webpage
- The Event List showed the **TCP three-way handshake, HTTP GET, HTTP 200 response**, and **TCP FIN sequence** — the complete lifecycle of one connection

---

## 📖 Concepts Learned

- **Complex PDU in Packet Tracer:** Allows custom application-layer traffic to be generated with configurable ports and timing — useful for simulating periodic connections like C2 beaconing
- **HTTP as a TCP protocol:** Unlike UDP-based protocols, HTTP requires connection setup and teardown, plus acknowledgment of every segment — adding substantial overhead
- **DNS precedes everything:** No IP-layer connection to a domain name can occur without DNS resolution first — this is why DNS logs are the earliest available signal
- **HTML rendering:** The browser receives raw HTML text from the server via HTTP and renders it as a formatted webpage — the source and the display are two views of the same data
- **Event List as a forensic timeline:** The simulation event list is conceptually identical to what a SIEM or packet capture timeline shows — events in chronological order with source, destination, and protocol

---

## 🧠 Reflection

The Buffer Full dialog was the most instructive moment — it made viscerally clear how many packets a single HTTP transaction generates. Users think "I loaded one page." The network sees dozens of TCP segments, DNS queries, ARP requests, and acknowledgment packets. This overhead is why CDNs, HTTP/2 multiplexing, and connection keep-alive exist in production environments.

From a security monitoring perspective, the lab reinforced that the DNS query for `ciscolearn.web.com` was visible long before the HTTP connection completed. In a real environment, this means a DNS blocklist can stop a malware callback before the C2 connection is ever established — without needing to inspect the encrypted HTTPS payload at all.

---

## 📎 References

- [Cisco NetAcad – Network Basics](https://www.netacad.com/)
- [RFC 9110 – HTTP Semantics](https://datatracker.ietf.org/doc/html/rfc9110)
- [RFC 793 – Transmission Control Protocol](https://datatracker.ietf.org/doc/html/rfc793)
- [RFC 1035 – Domain Names: Implementation](https://datatracker.ietf.org/doc/html/rfc1035)
