# Configure Access Control

> **Tools Used:** Cisco Packet Tracer, AAA-RADIUS Server, Email Server, FTP Server

---

## 📌 Objective

Configure and test the full AAA (Authentication, Authorization, and Accounting) framework in a simulated enterprise environment. This included setting up user accounts on a RADIUS server for wireless authentication, configuring email services with per-user credentials, and creating FTP accounts with differentiated privilege levels — then verifying that each access control boundary was enforced correctly.

---

## 🎯 Purpose & Relevance

Access control is the cornerstone of enterprise security. This lab goes beyond router passwords to implement **server-based centralized authentication** (RADIUS), **role-based authorization** (FTP privilege levels), and **authenticated messaging** (email with SMTP/POP3). These three services collectively mirror how real enterprise environments control identity — Active Directory for authentication, role-based access control for authorization, and email for accountable communication. Understanding how to configure and test each is essential for any security or systems administration role.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Cisco Packet Tracer | Simulation |
| AAA-RADIUS Server | Centralized wireless authentication |
| Mail Server | SMTP/POP3 email service |
| FTP Server | File transfer with tiered privileges |
| HQ-Laptop-1, HQ-Laptop-2 | Wireless clients |
| PC 1-1, PC 2-3, Net-Admin | Email/FTP clients |

---

## 🔬 Process / Steps Taken

### Part 1 – Configure AAA Authentication

#### Step 1 – Configure User Accounts on the AAA-RADIUS Server

Navigated to Headquarters → Wiring Closet → right-side Rack → **AAA-RADIUS Server > Services > AAA**. Turned the AAA service **On** and added two user accounts:

| Username | Password |
|----------|----------|
| user1 | PASSuser1! |
| user2 | PASSuser2! |

#### Step 2 – Configure Wireless Authentication on HQ-Laptop-1

Clicked HQ-Laptop-1 > **Config > Wireless0**:
- SSID: `HQ-INT`
- Authentication: **WPA2**
- User ID: `user1`
- Password: `PASSuser1!`
- IP Configuration: **DHCP**

Verified: HQ-Laptop-1 received an address in the `192.168.50.0/24` network — confirming RADIUS authentication succeeded and DHCP assignment was triggered.

Repeated for HQ-Laptop-2 using `user2` / `PASSuser2!` credentials.

---

### Part 2 – Configure Email Services

#### Step 1 – Activate Email and Configure Accounts

On the Mail server: **Services > EMAIL** — turned on both **SMTP** and **POP3**. Set domain to `mail.cyberhq.com`. Added four user accounts:

| Username | Password |
|----------|----------|
| HQuser1 | Cisco123! |
| HQuser2 | Cisco123~ |
| BRuser1 | Cisco123- |
| BRuser2 | Cisco123+ |

#### Step 2 – Configure Email Clients and Send Test Email

Configured email on four devices:

| Device | User | Email |
|--------|------|-------|
| PC 1-1 | Suk-Yi | HQuser1@mail.cyberhq.com |
| PC 2-3 | Ajulo | BRuser1@mail.cyberhq.com |
| HQ-Laptop-1 | Malia | BRuser2@mail.cyberhq.com |
| Net-Admin | Cisco | HQuser2@mail.cyberhq.com |

Composed an email from Suk-Yi (PC 1-1) to Ajulo at `BRuser1@mail.cyberhq.com` — sent successfully. Navigated to PC 2-3 → Receive → confirmed the email arrived with the correct subject and body.

---

### Part 3 – Configure FTP Services

#### Step 1 – Configure FTP User Accounts with Differentiated Privileges

On the FTP server: **Services > FTP** — turned service On. Created three accounts with distinct privilege sets:

| Username | Password | Privileges |
|----------|----------|------------|
| sukyi | cisco123 | RWDNL (Write, Read, Delete, Rename, List) |
| ajulo | cisco321 | RWDNL |
| malia | cisco123 | RWNL (Write, Read, Rename, List — **no Delete**) |

The key test case: `malia` was explicitly denied **Delete** privileges.

#### Step 2 – Test File Operations

Connected from **Net-Admin** as `sukyi`:
```
ftp 192.168.75.2
```
Downloaded `aMessage.txt` with `get`. The file contained: *"Greetings. You have successfully accessed the FTP server."*

Created a new message file and uploaded it as `aMessage_new.txt` using `put`.

<img width="903" height="192" alt="image" src="https://github.com/user-attachments/assets/379114e5-7173-4cac-a651-771fc2e9ad72" />

#### Step 3 – Verify Privilege Enforcement

Connected from **HQ-Laptop-1** as `malia` and attempted to delete the uploaded file:

```
ftp> delete aMessage_new.txt
```

<img width="990" height="493" alt="image" src="https://github.com/user-attachments/assets/856db2a6-3c1d-4d97-8954-ea5541c0649d" />

**Result:** `550-Requested action not taken, permission denied` — the delete was blocked exactly as configured.

Then attempted to rename the file:
```
ftp> rename aMessage_new.txt aMessage_rename.txt
```

**Result:** `[OK Renamed file successfully]` — rename succeeded because `malia` had the Rename (`N`) privilege.

<img width="959" height="579" alt="image" src="https://github.com/user-attachments/assets/3045b1e7-337a-4440-8ec5-44336cc4227a" />

The final FTP session showing both the failed delete and successful rename in sequence:

<img width="895" height="577" alt="image" src="https://github.com/user-attachments/assets/98946004-0197-4d0e-885b-f567e34dc8da" />

---

## 🔍 Key Findings

- **RADIUS centralized authentication** worked as designed — both laptops received IP addresses only after authenticating with their respective user1/user2 credentials
- The **FTP privilege model** enforced the delete restriction on `malia` precisely — `550 Permission denied` appeared on the delete attempt, while rename succeeded immediately
- The **AAA framework** in practice means: RADIUS server authenticates the user (who are you?), privilege levels authorize actions (what can you do?), and server logs record what happened (what did you do?)
- Default credentials on any server are a critical vulnerability — this lab demonstrated why AAA must be configured before deployment

---

## 📖 Concepts Learned

- **RADIUS (Remote Authentication Dial-In User Service):** A networking protocol providing centralized AAA for users who connect to a network. Enterprises use RADIUS (or its successor DIAMETER) to avoid storing credentials on every individual device.
- **WPA2 Enterprise vs. WPA2 Personal:** Personal uses a pre-shared key everyone knows. Enterprise uses RADIUS — each user authenticates with their own credentials, enabling individual accountability and easy revocation.
- **FTP Privilege Levels (RWDNL):** Read, Write, Delete, reNaming, List. Granular per-user control of exactly what file operations are permitted — this is the principle of least privilege applied to file services.
- **SMTP/POP3:** SMTP (port 25) sends mail. POP3 (port 110) retrieves it. Together they form the basic email delivery and retrieval system.

---

## 🧠 Reflection

The most instructive part of this lab was the privilege verification. It's easy to *say* a user doesn't have delete rights — it's more convincing to watch the `550 Permission denied` error appear live in the terminal. That gap between "configured" and "verified working" is exactly what security testing exists to close.

The RADIUS setup also highlighted a real-world pain point: wireless authentication with individual credentials (WPA2 Enterprise) requires a RADIUS server, which adds infrastructure complexity. But the payoff is enormous — when an employee leaves, you disable their RADIUS account and they're locked out of every wireless network instantly, rather than requiring a PSK change that affects all users.

---

## 📎 References

- [Cisco NetAcad – Network Defense](https://www.netacad.com/)
- [RFC 2865 – Remote Authentication Dial In User Service (RADIUS)](https://datatracker.ietf.org/doc/html/rfc2865)
- [Wi-Fi Alliance – WPA2 Enterprise](https://www.wi-fi.org/discover-wi-fi/security)
