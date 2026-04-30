# Observe Traffic Flow in a Routed Network

> **Tools Used:** Cisco Packet Tracer – Simulation Mode

---

## 📌 Objective

Use Cisco Packet Tracer's Simulation Mode to observe how traffic flows through both an **unrouted** flat LAN and a **routed** multi-network topology. Compare ARP broadcast behavior before and after introducing routing, then answer questions that demonstrate understanding of how routing improves network efficiency and security.

---

## 🎯 Purpose & Relevance

XYZ LLC was running all 150 devices on a single flat IP network — every broadcast hit every device on the LAN. This lab demonstrates exactly why that design fails at scale, and how segmenting traffic with routers dramatically improves efficiency and creates the foundation for access control policies. Understanding broadcast domains is prerequisite knowledge for every firewall, VLAN, and ACL lab that follows.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Cisco Packet Tracer | Network simulation |
| Simulation Mode | Step-by-step PDU capture |
| Command Prompt (simulated) | ARP cache inspection, ping, ipconfig /renew |

---

## 🔬 Process / Steps Taken

### Part 1 – Observe Traffic Flow in an Unrouted LAN

#### Step 1 – Clear the ARP Cache on Sales 1
Opened **Sales 1 > Desktop > Command Prompt** and ran `arp -a` to confirm the cache was empty. Sales 1's IP address was noted as `192.168.1.4`.

#### Step 2 – Observe Traffic in Simulation Mode
Switched to Simulation Mode and initiated a ping from Sales 2 to Sales 1. Advanced the simulation one step at a time using Capture/Forward.

**Observation:** Before any ICMP packet could be sent, Sales 2 had to resolve Sales 1's MAC address. This triggered an **ARP broadcast** — a frame destined for `FF:FF:FF:FF:FF:FF` that every single device on the network had to process, including the router interface.

> **Question answered in lab:** *Why is the destination MAC address the broadcast address?*
> Because the host's ARP cache was empty — it needed to discover the MAC address before it could send the actual packet.

> **Question answered in lab:** *Which hosts and devices processed the ARP request?*
> **All hosts and the router interface** — every device on the single flat network had to process it.

![Packet Tracer topology showing XYZ LLC unrouted network with Accounting, Finance, and Sales departments on a single segment](screenshots/traffic/img-001.png)

---

### Part 2 – Reconfigure the Network to Route Between LANs

#### Step 1 – Re-cable the Switches to the Router
Moved the cable connecting the Accounting and Finance switches directly to `GigabitEthernet 1/0` on the Edge router. Repeated for Finance-to-Sales, connecting to the remaining available GigabitEthernet port.

#### Step 2 – Configure Hosts with New IP Addresses
On each Finance and Sales host, opened **Command Prompt** and ran:
```
ipconfig /renew
```

The Edge router's DHCP server assigned each department its own subnet:

- **Finance network:** `192.168.2.0`
- **Sales network:** `192.168.3.0`

> **Question answered in lab:** *What IPv4 network is assigned to Finance?* `192.168.2.0`
> *What IPv4 network is assigned to Sales?* `192.168.3.0`

---

### Part 3 – Observe Traffic Flow in the Routed Network

Returned to Simulation Mode and repeated the ping from Sales 2 to Sales 1.

**Critical difference observed:** This time, the ARP broadcast only reached **Sales 1 and the router interface on the Sales network** — it did not propagate to Accounting or Finance hosts at all. The router acts as a **broadcast domain boundary**.

> **Question answered in lab:** *Which devices receive the ARP broadcasts this time?*
> Both Sales 1 and the router interface connected to the Sales network — no other departments.

> **Question answered in lab:** *What is the benefit of multiple IPv4 subnetworks in an enterprise?*
> Boosts performance and reduces congestion; also enhances security by isolating segments to contain threats and allowing better control over access policies.

![Assessment results showing Finance 2 IP address flagged as needing renewal, with other connections marked correct](screenshots/traffic/img-002.png)

---

## 🔍 Key Findings

- In a flat (unrouted) network, every ARP request broadcasts to **all 150+ devices** — consuming CPU and bandwidth on every host with every cache expiry
- After routing was introduced, ARP broadcasts were **contained within their own subnet** — Finance hosts never saw Sales ARP traffic
- The `ipconfig /renew` command forced immediate DHCP address acquisition on the new subnets without waiting for lease expiry
- The **router acts as the broadcast domain boundary** — this is the fundamental reason for network segmentation

---

## 📖 Concepts Learned

- **Broadcast domain:** All devices that receive a broadcast frame. Routers separate broadcast domains; switches do not.
- **ARP (Address Resolution Protocol):** Maps an IP address to a MAC address. Sends a broadcast to the entire local subnet. Cached after resolution to avoid repeat broadcasts.
- **`arp -d`:** Clears the ARP cache, forcing fresh resolution on next communication — used in the lab to simulate a cold-start scenario.
- **`ipconfig /renew`:** Forces a DHCP client to re-request an IP address from the DHCP server — essential after a subnet change.
- **Subnetting benefit:** Segmenting a flat /24 into multiple smaller subnets reduces broadcast traffic, improves performance, and allows targeted security policies per segment.

---

## 🧠 Reflection

The visual contrast between the two simulation runs was striking. In the flat network, the ARP envelope touched every device on the topology. After routing, it stayed in its own lane. At the scale of 150 real devices — with ARP caches expiring constantly — that difference translates directly to reduced CPU load on every host and reduced bandwidth consumption.

The security angle is equally important. A threat actor on the Finance network in a flat topology can see and respond to broadcasts from Sales and Accounting. After segmentation, they are blind to other departments unless explicitly routed there. This is the conceptual foundation for VLANs, firewall zones, and access control lists in later labs.

---

