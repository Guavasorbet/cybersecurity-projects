# cybersecurity-projects
cyber projects

# 🛡️ Cybersecurity Projects Portfolio

> Hands-on Cybersecurity labs and projects demonstrating network defense, endpoint security, threat detection, incident response, and vulnerability management using industry-standard tools and frameworks.

---

## 👤 About

This repository documents my hands-on cybersecurity training across four core domains. Each lab entry includes the objective, tools used, methodology, and key findings — organized to reflect real-world SOC and security analyst workflows.

---

## 📂 Repository Structure

```
cybersecurity-projects/
├── network-basics/              # Cisco Packet Tracer – Networking Fundamentals
├── network-defense/             # ACLs, Encryption, Firewalls, VPNs, SIEM
├── it-security-endpoint/        # Windows/Linux Hardening, Malware Analysis, Access Control
└── cysa-plus/                   # CySA+ Labs – Threat Hunting, Vuln Mgmt, Incident Response
```

---

## 🗂️ Domain Overview

### 🌐 [Network Basics](./network-basics/)
Foundational networking labs using Cisco Packet Tracer. Covers wireless configuration, DHCP, NAT, routing, and application-layer protocols (HTTP, FTP, Telnet, SSH).

| # | Lab | Key Concept |
|---|-----|-------------|
| 1 | [Configure Wireless Router & Client](./network-basics/lab-01-wireless-router.md) | Wireless setup, WPA2 |
| 2 | [Connect to a Web Server](./network-basics/lab-02-web-server.md) | HTTP, TCP/IP |
| 3 | [Use ipconfig Command](./network-basics/lab-03-ipconfig.md) | IP addressing |
| 4 | [Configure DHCP on Wireless Router](./network-basics/lab-04-dhcp.md) | DHCP leasing |
| 5 | [Examine NAT on Wireless Router](./network-basics/lab-05-nat.md) | NAT/PAT |
| 6 | [Identify MAC and IP Addresses](./network-basics/lab-06-mac-ip.md) | Layer 2 & 3 addressing |
| 7 | [Observe Traffic Flow in Routed Network](./network-basics/lab-07-traffic-flow.md) | Routing |
| 8 | [Create a LAN](./network-basics/lab-08-create-lan.md) | LAN topology |
| 9 | [The Client Interaction](./network-basics/lab-09-client-interaction.md) | Client-server model |
| 10 | [Observe Web Requests](./network-basics/lab-10-web-requests.md) | DNS, HTTP |
| 11 | [Use Telnet and SSH](./network-basics/lab-11-telnet-ssh.md) | Remote access, encryption |
| 12 | [Use FTP Services](./network-basics/lab-12-ftp.md) | File transfer protocols |
| 13 | [Use the ping Command](./network-basics/lab-13-ping.md) | ICMP, connectivity testing |

---

### 🔒 [Network Defense](./network-defense/)
Security-focused networking labs. Covers ACLs, Zone-Based Firewalls, encryption algorithms, authentication protocols, steganography, and network monitoring.

| # | Lab | Key Concept |
|---|-----|-------------|
| 1 | [Document Enterprise Cybersecurity Issues](./network-defense/lab-01-enterprise-cybersecurity.md) | Risk documentation |
| 2 | [Investigating OWASP](./network-defense/lab-02-owasp.md) | OWASP Top 10 |
| 3 | [Wireless Router Hardening](./network-defense/lab-03-wireless-hardening.md) | Wireless security |
| 4 | [Physical Security with IoT](./network-defense/lab-04-iot-physical-security.md) | IoT threats |
| 5 | [Configure Access Control](./network-defense/lab-05-access-control.md) | AAA model |
| 6 | [TACACS+ and RADIUS Authentication](./network-defense/lab-06-tacacs-radius.md) | Server-based AAA |
| 7 | [Named Standard IPv4 ACLs](./network-defense/lab-07-named-acls.md) | ACL filtering |
| 8 | [Numbered Standard IPv4 ACLs](./network-defense/lab-08-numbered-acls.md) | Standard ACLs |
| 9 | [Extended ACLs Scenario 1](./network-defense/lab-09-extended-acls-1.md) | Extended ACLs |
| 10 | [Extended ACLs Scenario 2](./network-defense/lab-10-extended-acls-2.md) | Extended ACLs |
| 11 | [Configure IPv6 ACLs](./network-defense/lab-11-ipv6-acls.md) | IPv6 security |
| 12 | [Configure a ZPF](./network-defense/lab-12-zpf.md) | Zone-Based Firewall |
| 13 | [NetFlow Implementation](./network-defense/lab-13-netflow.md) | Traffic analysis |
| 14 | [Logging from Multiple Sources](./network-defense/lab-14-logging.md) | SIEM/log aggregation |
| 15 | [Classic & Modern Encryption](./network-defense/lab-15-encryption-algorithms.md) | DES, AES, RSA |
| 16 | [Encrypt & Decrypt with OpenSSL](./network-defense/lab-16-openssl.md) | OpenSSL CLI |
| 17 | [Encrypt & Decrypt with Hacker Tool](./network-defense/lab-17-hacker-encryption.md) | Red team perspective |
| 18 | [Telnet vs SSH in Wireshark](./network-defense/lab-18-wireshark-ssh.md) | Packet capture |
| 19 | [Determine Encryption Algorithm](./network-defense/lab-19-encryption-selection.md) | Cryptographic decision-making |
| 20 | [Steganography to Hide Data](./network-defense/lab-20-steganography.md) | Data concealment |
| 21 | [Hashing Things Out](./network-defense/lab-21-hashing.md) | MD5, SHA |
| 22 | [Generate & Use Digital Signature](./network-defense/lab-22-digital-signature.md) | PKI |
| 23 | [Certificate Authority Stores](./network-defense/lab-23-ca-stores.md) | PKI, trust chains |

---

### 💻 [IT Security & Endpoint Protection](./it-security-endpoint/)
Windows and Linux security labs. Covers malware analysis, access control, registry inspection, PowerShell, port scanning, log analysis, and system hardening.

| # | Lab | Key Concept |
|---|-----|-------------|
| 1 | [Logical and Physical Mode Exploration](./it-security-endpoint/lab-01-logical-physical.md) | Network topology |
| 2 | [Social Engineering Techniques](./it-security-endpoint/lab-02-social-engineering.md) | Human factor attacks |
| 3 | [Investigate a Threat Landscape](./it-security-endpoint/lab-03-threat-landscape.md) | Threat modeling |
| 4 | [Exploring DNS Traffic](./it-security-endpoint/lab-04-dns-traffic.md) | DNS analysis |
| 5 | [Install a Virtual Machine](./it-security-endpoint/lab-05-virtual-machine.md) | VM setup, sandboxing |
| 6 | [Attacking a MySQL Database](./it-security-endpoint/lab-06-mysql-attack.md) | SQL injection basics |
| 7 | [Reading Server Logs](./it-security-endpoint/lab-07-server-logs.md) | Log analysis |
| 8 | [Recommend Threat Mitigation](./it-security-endpoint/lab-08-threat-mitigation.md) | Defense strategies |
| 9 | [Configure Basic Wireless Security](./it-security-endpoint/lab-09-wireless-security.md) | WPA2, WEP |
| 10 | [Troubleshoot Wireless Connection](./it-security-endpoint/lab-10-wireless-troubleshoot.md) | Wireless diagnostics |
| 11 | [ACL Demonstration](./it-security-endpoint/lab-11-acl-demo.md) | Access control |
| 12 | [Processes, Threads, Handles & Registry](./it-security-endpoint/lab-12-windows-internals.md) | Windows internals |
| 13 | [Create User Accounts](./it-security-endpoint/lab-13-user-accounts.md) | Identity management |
| 14 | [Using Windows PowerShell](./it-security-endpoint/lab-14-powershell.md) | PowerShell scripting |
| 15 | [Windows Task Manager](./it-security-endpoint/lab-15-task-manager.md) | Process monitoring |
| 16 | [Monitor System Resources in Windows](./it-security-endpoint/lab-16-system-resources.md) | Performance monitoring |
| 17 | [Text Files in the CLI](./it-security-endpoint/lab-17-cli-text.md) | Linux CLI basics |
| 18 | [Getting Familiar with Linux Shell](./it-security-endpoint/lab-18-linux-shell.md) | Bash fundamentals |
| 19 | [Port Scanner to Detect Open Ports](./it-security-endpoint/lab-19-port-scanner.md) | Nmap, reconnaissance |
| 20 | [Linux Servers](./it-security-endpoint/lab-20-linux-servers.md) | Linux services |
| 21 | [Locating Log Files](./it-security-endpoint/lab-21-log-files.md) | Linux logging |
| 22 | [Linux Filesystem & Permission Settings](./it-security-endpoint/lab-22-linux-permissions.md) | File permissions, chmod |
| 23 | [Security Features in Windows & Linux](./it-security-endpoint/lab-23-security-features.md) | OS hardening |
| 24 | [Harden a Linux System](./it-security-endpoint/lab-24-linux-hardening.md) | Linux hardening |
| 25 | [Recover Passwords](./it-security-endpoint/lab-25-password-recovery.md) | Password cracking, defense |
| 26 | [Recommend Endpoint Security Measures](./it-security-endpoint/lab-26-endpoint-security.md) | Endpoint defense |
| 27 | [Online Malware Investigation Tools](./it-security-endpoint/lab-27-malware-tools.md) | VirusTotal, sandboxing |
| 28 | [File and Data Integrity Checks](./it-security-endpoint/lab-28-integrity-checks.md) | Hashing, file integrity |
| 29 | [Explore File and Data Encryption](./it-security-endpoint/lab-29-file-encryption.md) | Encryption at rest |
| 30 | [Cybersecurity Sorcery Cube Quizlet](./it-security-endpoint/lab-30-sorcery-cube.md) | Review |

---

### 🔬 [CySA+ Labs](./cysa-plus/)
CompTIA CySA+ aligned labs across three course modules (180, 181, 182). Covers threat intelligence, vulnerability scanning, incident response, forensic analysis, and root cause analysis.

| # | Lab | Key Concept |
|---|-----|-------------|
| 1 | [Exploring the Lab Environment](./cysa-plus/lab-01-lab-environment.md) | Tool familiarity |
| 2 | [Configuring Controls](./cysa-plus/lab-02-controls.md) | Security controls |
| 3 | [IoC and Threat Intelligence Sources](./cysa-plus/lab-03-ioc-threat-intel.md) | Threat feeds, IOCs |
| 4 | [Performing Threat Hunting](./cysa-plus/lab-04-threat-hunting.md) | Proactive detection |
| 5 | [System Hardening](./cysa-plus/lab-05-system-hardening.md) | CIS benchmarks |
| 6 | [Centralized Logging](./cysa-plus/lab-06-centralized-logging.md) | SIEM, syslog |
| 7 | [Assess Time Sync Errors](./cysa-plus/lab-07-time-sync.md) | NTP, log correlation |
| 8 | [Configuring Automation](./cysa-plus/lab-08-automation.md) | SOAR, scripting |
| 9 | [Performing Asset Discovery](./cysa-plus/lab-09-asset-discovery.md) | Network mapping |
| 10 | [Performing Passive Scanning](./cysa-plus/lab-10-passive-scanning.md) | Passive recon |
| 11 | [Performing Vulnerability Scanning](./cysa-plus/lab-11-vuln-scanning.md) | Nessus, OpenVAS |
| 12 | [Establishing Context Awareness](./cysa-plus/lab-12-context-awareness.md) | Asset criticality |
| 13 | [Analyzing Vulnerability Reports](./cysa-plus/lab-13-vuln-reports.md) | CVSS scoring |
| 14 | [Detecting Legacy Systems](./cysa-plus/lab-14-legacy-systems.md) | EOL risk management |
| 15 | [Performing Playbook Incident Response](./cysa-plus/lab-15-playbook-ir.md) | IR playbooks |
| 16 | [IoC Detection and Analysis](./cysa-plus/lab-16-ioc-detection.md) | IOC analysis |
| 17 | [Post-Incident Forensic Analysis](./cysa-plus/lab-17-forensics.md) | Digital forensics |
| 18 | [Performing Root Cause Analysis](./cysa-plus/lab-18-root-cause.md) | RCA methodology |

---




