# Examining Telnet and SSH in Wireshark

> **Tools Used:** Wireshark, Security Workstation VM, Linux Terminal

---

## 📌 Objective

Capture and compare live network traffic from Telnet and SSH sessions using Wireshark on the loopback interface. Demonstrate — through direct packet inspection — why Telnet exposes credentials in plaintext and why SSH is required for all remote administration.

---

## 🎯 Purpose & Relevance

This is not a theoretical lab. The password appears on screen. In plaintext. In Wireshark. Without any decryption. This lab makes the risk of Telnet impossible to ignore, and provides the direct visual comparison that justifies every SSH requirement in every security policy ever written. In a real SOC or network security role, detecting Telnet traffic in packet captures or flow logs is an automatic critical finding.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Wireshark | Packet capture and stream analysis |
| Linux Terminal | Telnet and SSH client |
| Security Workstation VM | Host system (Ubuntu, loopback capture) |
| Loopback interface (lo) | Capture interface for local sessions |

---

## 🔬 Process / Steps Taken

### Part 1 – Examine a Telnet Session

#### Step 1 – Start Capture and Initiate Telnet

Launched Wireshark from the terminal:
```bash
[analyst@secOps ~]$ wireshark &
```

Started a packet capture on the **Loopback: lo** interface. Opened a second terminal and connected via Telnet to localhost:

```bash
[analyst@secOps ~]$ telnet localhost
```

Entered credentials when prompted:
- Login: `analyst`
- Password: `cyberops`

Stopped the capture after authentication.

<img width="1789" height="690" alt="image" src="https://github.com/user-attachments/assets/cf78c51a-cdb3-43ba-bf53-c7989f11fb4c" />

#### Step 2 – Examine the Telnet Session in Wireshark

Applied display filter: `telnet`

Right-clicked a Telnet packet → **Follow > TCP Stream**

<img width="1127" height="742" alt="image" src="https://github.com/user-attachments/assets/e500e0dd-72c1-45aa-bb89-afe664aac0e4" />

The Follow TCP Stream window revealed the complete session:

<img width="1784" height="734" alt="image" src="https://github.com/user-attachments/assets/b339aa5c-9515-43df-8655-b64ee8ebf2cb" />

**Critical observation:** The entire session was visible in plaintext:
- Login prompt visible
- Username appeared as `aannaallyysstt` — doubled characters due to Telnet's echo setting
- **Password `cyberops` was fully readable** — no decryption required
- Every command typed after login was equally visible

> **Note on doubled characters:** Telnet's echo setting causes each character to appear twice in the stream capture — once sent by the client, once echoed back by the server.

Typed `exit` in the terminal to close the Telnet session.

---

### Part 2 – Examine an SSH Session

#### Step 1 – Start New Capture and Initiate SSH

Started a fresh Wireshark capture on **Loopback: lo**. In the terminal:

```bash
[analyst@secOps ~]$ ssh localhost
```

When prompted about the host key authenticity, entered `yes`. Entered password `cyberops` when prompted. Session established — ran a few commands — stopped the capture.

#### Step 2 – Examine the SSH Session in Wireshark

Applied display filter: `ssh`

Right-clicked an SSHv2 packet → **Follow > TCP Stream**

<img width="715" height="644" alt="image" src="https://github.com/user-attachments/assets/5771361b-9610-439e-821f-2853ca0cee30" />

**Observation:** The stream showed:
- SSH version banner: `SSH-2.0-OpenSSH 8.2` (plaintext — this is expected protocol behavior)
- The cipher negotiation exchange listing supported algorithms
- After the handshake: **completely unreadable encrypted data** — random binary with no recognizable strings

The same username and password that were immediately visible in the Telnet stream were completely inaccessible in the SSH stream.

<img width="671" height="684" alt="image" src="https://github.com/user-attachments/assets/fcb57df5-42df-47e7-bc6d-9fa8bbb9da77" />

The full-screen Wireshark view of the SSH capture:

<img width="1671" height="730" alt="image" src="https://github.com/user-attachments/assets/df90d040-0672-4e7b-b985-74c7c9d5819b" />

---

## Reflection Question

> **Why is SSH preferred over Telnet for remote connections?**

*"SSH is preferred because it encrypts all data and passwords, ensuring that your connection remains private and secure from hackers. Telnet sends everything in plain text, including credentials, meaning anyone monitoring the network can easily read your login credentials and sensitive commands."*

---

## 🔍 Key Findings

- **Telnet (Port 23):** The password `cyberops` was directly readable in the Wireshark TCP stream with zero effort — no tools, no decryption, no expertise required beyond right-click → Follow TCP Stream
- **SSH (Port 22):** After the version banner exchange, the entire session was encrypted binary — credentials and commands were completely inaccessible to a passive listener
- The **SSH version banner** (`SSH-2.0-OpenSSH_8.2`) is intentionally plaintext — this is how clients and servers negotiate capabilities, not a vulnerability
- Wireshark's **Follow TCP Stream** is the definitive tool for reading application-layer protocol conversations from raw packet captures
- The **doubled characters** in the Telnet stream (`aannaallyysstt`) are caused by Telnet's echo mechanism — one character from the client, one echoed back by the server — both visible in the stream

---

## 📖 Concepts Learned

- **Telnet (TCP Port 23):** 1969-era remote access protocol. No encryption, no integrity verification. Everything transmitted in ASCII plaintext. Should be disabled on all production devices and blocked at the firewall.
- **SSH (TCP Port 22):** Secure Shell. Full session encryption using AES (after negotiation). Supports password and key-based authentication. The industry standard for all remote CLI access.
- **Cipher negotiation in SSH:** The version banner exchange lists both sides' supported encryption, MAC, and key exchange algorithms. Client and server agree on the strongest common option. This negotiation is plaintext — but only reveals which ciphers are available, not any session content.
- **Loopback interface (lo):** The local loopback (`127.0.0.1`/`::1`) allows capturing traffic between processes on the same machine — critical for this lab since both Telnet/SSH client and server ran on the same VM.
- **Detection implication:** Any Telnet traffic detected in a production environment is an immediate critical finding — it means either a misconfigured device or an attacker using the protocol deliberately to blend into legacy traffic patterns.

---

## 🧠 Reflection

Seeing `cyberops` in plaintext in the Wireshark stream permanently changes how you think about unencrypted protocols. It takes literally 15 seconds: open Wireshark, start capture, filter for telnet, right-click, Follow TCP Stream. Done. Credential captured. No specialized hardware, no expertise, no backdoor — just the protocol working exactly as designed.

The SSH comparison was equally important. The cipher negotiation banner lists AES-256, ChaCha20, ECDSA — strong modern cryptography. And then the stream becomes binary noise. That's what encryption looks like working correctly: an observer can see that a conversation is happening but learns nothing about its content.

In a real SOC environment, any internal Telnet traffic should trigger a high-severity alert. Even if your own devices use SSH, a legacy printer, industrial controller, or old network appliance using Telnet puts every credential it handles at risk to anyone on that network segment.

---

## 📎 References

- [Cisco NetAcad – Network Defense](https://www.netacad.com/)
- [RFC 4253 – The SSH Transport Layer Protocol](https://datatracker.ietf.org/doc/html/rfc4253)
- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [MITRE ATT&CK – T1021.004: SSH](https://attack.mitre.org/techniques/T1021/004/)
