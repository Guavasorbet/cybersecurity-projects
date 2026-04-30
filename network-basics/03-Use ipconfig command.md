# Use the ipconfig Command

> **Tools Used:** Cisco Packet Tracer, Windows Command Prompt

---

## 📌 Objective

Use the `ipconfig` command and its key switches (`/all`, `/release`, `/renew`) to examine a host's full IP configuration, release a DHCP lease, and renew it. Understand every field in the output and observe the DORA handshake during address renewal.

---

## 🎯 Purpose & Relevance

`ipconfig` is among the most-used commands across networking and security careers. During incident response, `ipconfig /all` in under 10 seconds reveals the host's IP, MAC, DNS servers, DHCP server, and lease timestamps — enough to search firewall logs, locate the device on a switch, and trace it through DHCP records. This lab builds the muscle memory and conceptual understanding that makes that workflow instinctive.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Cisco Packet Tracer | Network simulation |
| Windows Command Prompt (simulated) | ipconfig execution |
| DHCP Server (router) | IP address leasing |

---

## 🔬 Process / Steps Taken

### Step 1 – Run `ipconfig` (Basic)

Opened the simulated PC's **Desktop > Command Prompt** and entered:

```
C:\> ipconfig
```

<img width="1024" height="668" alt="image" src="https://github.com/user-attachments/assets/c65f86e9-42a2-4fbb-af93-6a653e48b7ab" />

The basic output returned three fields per interface: **IPv4 Address**, **Subnet Mask**, and **Default Gateway** — enough to confirm network connectivity and identify the subnet.

---

### Step 2 – Run `ipconfig /all` (Full Details)

```
C:\> ipconfig /all
```

<img width="1149" height="739" alt="image" src="https://github.com/user-attachments/assets/fcf830f7-68a9-468e-a01f-c0dfbb62bcaf" />

The `/all` flag revealed the complete network identity:

| Field | Value | Significance |
|-------|-------|--------------|
| Host Name | PC-name | Computer's configured name |
| Physical Address (MAC) | XX-XX-XX-XX-XX-XX | Layer 2 hardware identifier |
| DHCP Enabled | Yes | Confirms dynamic addressing |
| IPv4 Address | 192.168.x.x | Current IP lease |
| Default Gateway | 192.168.x.1 | Router LAN interface |
| DHCP Server | 192.168.x.1 | Source of the lease |
| DNS Servers | 192.168.x.1 | Resolver address |
| Lease Obtained | timestamp | When the IP was assigned |
| Lease Expires | timestamp | When renewal is required |

---

### Step 3 – Run `ipconfig /release`

```
C:\> ipconfig /release
```

<img width="1170" height="775" alt="image" src="https://github.com/user-attachments/assets/e569d656-8354-43c1-9733-5c5732777df4" />

After releasing, the IPv4 address field showed `0.0.0.0` — the host had no IP address and was temporarily offline. No pings, DNS queries, or web traffic could succeed in this state.

---

### Step 4 – Run `ipconfig /renew`

```
C:\> ipconfig /renew
```

<img width="1228" height="830" alt="image" src="https://github.com/user-attachments/assets/dc42f8d2-6543-434f-a9df-a52b43dbd1a7" />

The host sent a DHCP Discover broadcast, the router responded with an Offer, the host sent a Request, and the router confirmed with an Acknowledge — completing the **DORA process**. A new `192.168.x.x` address was assigned and confirmed.

---

### Additional Verification Steps

Ran further verification commands to confirm the renewed configuration:

<img width="1165" height="749" alt="image" src="https://github.com/user-attachments/assets/3d535791-9377-4aaf-a409-4f415329dda6" />

<img width="1264" height="848" alt="image" src="https://github.com/user-attachments/assets/20a95aeb-cb95-443a-940f-842b5ac9d67d" />

The final screenshot confirmed the full adapter details were intact with updated lease timestamps.

<img width="727" height="367" alt="image" src="https://github.com/user-attachments/assets/8bb56a76-d9d3-4afc-96f8-18e73fe3c7fc" />

---

## 🔍 Key Findings

- `ipconfig` (basic) returns only IP, subnet mask, gateway — fast triage tool
- `ipconfig /all` exposes the full network identity including the **MAC address** (critical for correlating to switch CAM tables and DHCP server logs)
- After `/release`, the host became completely network-isolated — `0.0.0.0` with no gateway
- `/renew` triggered the complete **DORA handshake** — observable as four broadcast/unicast packets in Simulation Mode
- The **DHCP Server field** from `/all` tells you exactly which device to query for historical lease records during an investigation

---

## 📖 Concepts Learned

- **`ipconfig` switches summary:**
  - `/all` — full details including MAC, DHCP server, lease times, DNS
  - `/release` — relinquish current DHCP lease (address becomes 0.0.0.0)
  - `/renew` — request a new DHCP lease (triggers DORA)
  - `/flushdns` — clear the local DNS cache (not in this lab, but closely related)

- **DHCP DORA Process:**
  - **D**iscover — client broadcasts: "Any DHCP servers?"
  - **O**ffer — server responds: "Here's an available IP"
  - **R**equest — client broadcasts: "I'd like that IP"
  - **A**cknowledge — server confirms: "It's yours for the lease duration"

- **Physical Address (MAC):** The 48-bit hardware address of the NIC. Globally unique. Displayed in `ipconfig /all` as `XX-XX-XX-XX-XX-XX`. Used at Layer 2 for Ethernet communication and in DHCP logs for device identification.

- **Lease Obtained / Lease Expires:** Timestamps showing when the current IP address was assigned and when it will expire. Critical for reconstructing a timeline during incident response.

---

## 🧠 Reflection

The MAC address exposed by `ipconfig /all` is the most forensically durable identifier on a Windows host. IP addresses change with DHCP renewals, hostnames can be modified, but the MAC address is burned into the NIC hardware. In an investigation, `ipconfig /all` → note the MAC → query DHCP logs for that MAC → reconstruct every IP address that device has ever used on the network. That's a powerful investigation chain from a single command.

The release/renew exercise also built intuition for a common helpdesk scenario: user calls saying "I have no internet." First check: `ipconfig` — do they have a `169.254.x.x` address (APIPA, meaning DHCP failed)? Fix: `ipconfig /release` then `/renew`. If the address doesn't change back to `192.168.x.x`, the problem is DHCP server connectivity, not the client.

---

## 📎 References

- [Cisco NetAcad – Network Basics](https://www.netacad.com/)
- [RFC 2131 – Dynamic Host Configuration Protocol](https://datatracker.ietf.org/doc/html/rfc2131)
- [Microsoft Docs – ipconfig](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/ipconfig)
