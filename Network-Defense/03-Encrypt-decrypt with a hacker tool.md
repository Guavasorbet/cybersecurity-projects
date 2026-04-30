# Encrypting and Decrypting Data Using a Hacker Tool

> **Tools Used:** fcrackzip, Linux Terminal, Security Workstation VM (VirtualBox)

---

## 📌 Objective

Simulate a real-world corporate scenario: a CFO locked out of encrypted ZIP files on a USB drive. Use **fcrackzip** — a brute-force password recovery utility — to recover passwords from encrypted ZIP archives of varying lengths, and measure how password length directly affects recovery time.

---

## 🎯 Purpose & Relevance

This lab demonstrates the offensive side of encryption. The same tools available to a penetration tester or forensic analyst are available to attackers. `fcrackzip` is freely available, runs on any Linux system, and requires no expertise to use. Understanding how quickly short passwords are broken — and experiencing the time cost of longer ones — builds an intuition for why password length minimums exist in every security policy. The lab also demonstrates that encrypted files are only as secure as their keys.

---

## 🧰 Tools & Environment

| Tool | Purpose |
|------|---------|
| Security Workstation VM (VirtualBox) | Isolated Linux environment |
| Linux Terminal | Command execution |
| `zip -e` | Create encrypted ZIP files |
| `fcrackzip` | Brute-force password recovery |
| `unzip` | Test decryption manually |

---

## 🔬 Process / Steps Taken

### Part 1 – Create and Encrypt Files

#### Step 1 – Create Text Files

Launched the Security Workstation VM and opened a terminal. Confirmed current directory was the analyst home, then:

```bash
mkdir Zip-Files
cd Zip-Files
echo This is a sample text file > sample-1.txt
echo This is a sample text file > sample-2.txt
echo This is a sample text file > sample-3.txt
ls -l
```

All three files confirmed: 27 bytes each.

<img width="920" height="428" alt="image" src="https://github.com/user-attachments/assets/ab7b081c-0f1a-4406-a591-06e479bd7236" />

#### Step 2 – Create Encrypted ZIP Files with Varying Password Lengths

Created five encrypted ZIP files, each with a progressively longer password:

```bash
zip -e file-1.zip sample-*   # Password: B        (1 char)
zip -e file-2.zip sample-*   # Password: R2       (2 chars)
zip -e file-3.zip sample-*   # Password: 0B1      (3 chars)
zip -e file-4.zip sample-*   # Password: Y0Da     (4 chars)
zip -e file-5.zip sample-*   # Password: C-3P0    (5 chars)
```

Ran `ls -l f*` to verify all five archives were created.

**Then tested what happens with a wrong password:**

```bash
unzip file-1.zip
```
Entered an incorrect password — received `password incorrect--reenter` repeatedly, then `skipping: sample-1.txt incorrect password`. File was inaccessible.

<img width="946" height="620" alt="image" src="https://github.com/user-attachments/assets/c24edcab-919b-40be-a52e-c18d407918b4" />

---

### Part 2 – Recover Encrypted Zip File Passwords Using fcrackzip

#### Step 1 – Introduction to fcrackzip

Ran `fcrackzip -h` to review available flags. Key flags used:
- `-v` — verbose output
- `-u` — use unzip to verify found passwords
- `-l 1-N` — specify minimum and maximum password length to search

#### Step 2 – Crack Each File

**file-1.zip (1-character password):**
```bash
fcrackzip -vul 1-4 file-1.zip
```
Output: `PASSWORD FOUND!!!!: pw == B`
**Time to crack: Almost instantaneous.**

**file-2.zip (2-character password):**
```bash
fcrackzip -vul 1-4 file-2.zip
```
Output: `PASSWORD FOUND!!!!: pw == R2`
**Time: Fairly quick, but file-1 was faster.**

**file-3.zip (3-character password):**
```bash
fcrackzip -vul 1-4 file-3.zip
```
Output: `PASSWORD FOUND!!!!: pw == 0B1`
**Time: Still fairly quickly.**

<img width="958" height="627" alt="image" src="https://github.com/user-attachments/assets/85bb6da3-f4ad-4492-a7ee-1a1af69dd97b" />

**file-4.zip (4-character password):**
```bash
fcrackzip -vul 1-4 file-4.zip
```
Output: `PASSWORD FOUND!!!!: pw == Y0Da`
**Time: Still able to crack quickly.**

**file-5.zip (5-character password):**
```bash
fcrackzip -vul 1-5 file-5.zip
```
Output: `PASSWORD FOUND!!!!: pw == C-3P0`
**Time: A bit longer than previous ones.**

**file-6.zip (6-character password — created as a test):**
```bash
zip -e file-6.zip sample-*    # Password: JarJar (6 chars)
fcrackzip -vul 1-6 file-6.zip
```

> **Question answered in lab:** *How long would it take for a 6-character password?*
> My prediction: *"Definitely a few hours."*
>
> Actual result: *"Longer than I had time to wait. I left it be and came back to it hours later."*

<img width="911" height="663" alt="image" src="https://github.com/user-attachments/assets/c725b912-e911-4bd7-a929-f738a228dffa" />

> **Final question:** *How long should a password be to be secure?*
> **Given all the tools available, I think a decent password shouldn't be less than 12 characters.**

---

## 🔍 Key Findings

| File | Password Length | Crack Time |
|------|----------------|------------|
| file-1.zip | 1 char | Instantaneous |
| file-2.zip | 2 chars | Seconds |
| file-3.zip | 3 chars | Seconds |
| file-4.zip | 4 chars | Seconds–minutes |
| file-5.zip | 5 chars | Minutes |
| file-6.zip | 6 chars | Hours+ |

- Short passwords (1–4 characters) were cracked **immediately or within seconds** — completely inadequate for any real security purpose
- The 6-character password ran for hours without completing — **exponential growth** in cracking time with each additional character
- `fcrackzip` uses brute-force by default — it tries every possible combination up to the specified length, which is why the search space explodes with length
- The same `fcrackzip` approach could be used with a **dictionary file** (`-D` flag) for targeted attacks against predictable passwords — even a 12-character password like "password123!" would fall instantly to a dictionary attack

---

## 📖 Concepts Learned

- **Brute-force attack:** Systematically tries every possible combination. Time complexity grows exponentially with password length and character set size. A 1-character password from 95 printable ASCII characters = 95 attempts. A 6-character password = 95⁶ ≈ 735 billion attempts.
- **Dictionary attack:** Uses a wordlist of common passwords rather than exhaustive enumeration. Dramatically faster against predictable passwords regardless of length. Defense: avoid dictionary words even in long passwords.
- **fcrackzip:** A lightweight Linux utility that performs brute-force password recovery on encrypted ZIP files. Freely available, no technical expertise needed.
- **`zip -e`:** Creates AES-encrypted ZIP archives. The encryption itself is strong — the weakness is the password protecting the key, not the algorithm.
- **Password length as the primary defense:** Each additional character multiplies the search space by the size of the character set. 12+ characters with mixed types makes brute-force computationally infeasible for current hardware.
- **Minimum password recommendation:** Based on this lab's observed cracking speed — **12 characters minimum**, using a mix of uppercase, lowercase, numbers, and symbols, avoiding dictionary words.

---

## 🧠 Reflection

Watching file-1 crack instantaneously was sobering. A single character. Done. And the attacker would have needed only a basic laptop and five minutes of googling to run the exact same attack. The 6-character JarJar file running for hours without finishing was equally instructive — it made the exponential growth of search space viscerally real rather than just a number on a slide.

The most important practical takeaway: this lab used AES-encrypted ZIP files, and AES itself was never broken. The attack was entirely against the **password**, not the encryption. This is the fundamental lesson of password security — strong encryption is worthless with a weak password. The math of AES-256 is unbreakable; `password` is not.

This lab directly connects to the Telnet and SSH lab — both are about the same underlying principle: the strength of a security control is only as good as its weakest component.

---

## 📎 References

- [Cisco NetAcad – Network Defense](https://www.netacad.com/)
- [NIST FIPS 197 – Advanced Encryption Standard](https://csrc.nist.gov/publications/detail/fips/197/final)
- [NIST SP 800-63B – Digital Identity Guidelines: Authentication](https://pages.nist.gov/800-63-3/sp800-63b.html)
- [fcrackzip – GitHub](https://github.com/hyc/fcrackzip)
