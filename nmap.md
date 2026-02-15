
# 🔴 Advanced Red Team Nmap Cheat Sheet

**Tool:** Nmap
**Use Case:** Advanced reconnaissance, stealth scanning, firewall evasion, deep enumeration
⚠️ Use only in authorized environments.

---

# 📌 1️⃣ Advanced Recon Workflow

```bash
# 1. Host Discovery
nmap -sn 10.10.10.0/24

# 2. Stealth Scan
nmap -sS -T4 target

# 3. Full Port Scan
nmap -p- -T4 target

# 4. Service & Script Enumeration
nmap -sV -sC target

# 5. Deep Aggressive Scan
nmap -A -p- target
```

---

# 📌 2️⃣ Stealth & Evasion Techniques

## 🔹 SYN Stealth Scan (Default for root)

```bash
nmap -sS target
```

## 🔹 Fragment Packets (Bypass Simple Firewalls)

```bash
nmap -f target
```

## 🔹 MTU Manipulation

```bash
nmap --mtu 24 target
```

## 🔹 Decoy Scan (Hide Real IP)

```bash
nmap -D RND:10 target
```

## 🔹 Spoof Source Port (DNS Evasion)

```bash
nmap --source-port 53 target
```

## 🔹 Idle/Zombie Scan (Highly Stealthy)

```bash
nmap -sI zombie_ip target
```

---

# 📌 3️⃣ Timing Control (Avoid Detection)

| Option | Mode       |
| ------ | ---------- |
| `-T0`  | Paranoid   |
| `-T1`  | Sneaky     |
| `-T2`  | Polite     |
| `-T3`  | Normal     |
| `-T4`  | Aggressive |
| `-T5`  | Insane     |

Example:

```bash
nmap -sS -T2 target
```

---

# 📌 4️⃣ Advanced Port Scanning

## Scan All Ports

```bash
nmap -p- target
```

## Fast Scan (Top 100 Ports)

```bash
nmap -F target
```

## UDP Scan (Slow but Important)

```bash
nmap -sU target
```

## TCP + UDP Together

```bash
nmap -sS -sU target
```

---

# 📌 5️⃣ Advanced Service Enumeration

## Version Detection

```bash
nmap -sV target
```

## OS Detection

```bash
nmap -O target
```

## Aggressive Mode

```bash
nmap -A target
```

Includes:

* OS detection
* Version detection
* Script scanning
* Traceroute

---

# 📌 6️⃣ NSE (Nmap Scripting Engine) – Red Team Use

## Run Vulnerability Scripts

```bash
nmap --script vuln target
```

## SMB Enumeration

```bash
nmap --script smb-enum-shares target
```

## FTP Anonymous Login Check

```bash
nmap --script ftp-anon target
```

## HTTP Title Grab

```bash
nmap --script http-title target
```

## Brute Force Scripts

```bash
nmap --script brute target
```

---

# 📌 7️⃣ Firewall & IDS Evasion Tricks

```bash
# Randomize hosts
nmap --randomize-hosts target

# Slow scan to avoid IDS
nmap -T1 target

# Send bad checksums
nmap --badsum target

# Append random data
nmap --data-length 50 target
```

---

# 📌 8️⃣ Output for Reporting

```bash
# Normal output
nmap -oN scan.txt target

# XML output
nmap -oX scan.xml target

# All formats
nmap -oA fullscan target
```

---

# 📌 9️⃣ Red Team Quick Attack Patterns

## External Recon

```bash
nmap -sS -T3 -Pn target
```

## Internal Network Sweep

```bash
nmap -sn 10.0.0.0/16
```

## Full Enumeration After Foothold

```bash
nmap -A -p- 10.0.0.5
```

---

# 📌 🔟 Port State Meanings

| State         | Meaning                      |
| ------------- | ---------------------------- |
| Open          | Service listening            |
| Closed        | Reachable but no service     |
| Filtered      | Firewall blocking            |
| Open|Filtered | Cannot determine             |
| Unfiltered    | Accessible but state unclear |

---

# 🧠 Red Team Pro Tips

* Start stealthy → escalate later
* Avoid `-T5` in real engagements
* Use decoys carefully (can trigger alerts)
* Save every scan with `-oA`
* Combine Nmap results with manual enumeration

---

---

# 🔵 Blue Team Detection & Logging Cheat Sheet

**Goal:** Detect, log, and defend against Nmap reconnaissance.

---

# 📌 1️⃣ Detecting Nmap Scans

## 🔍 SYN Scan Detection

Look for:

* Multiple SYN packets
* No completed handshake
* High volume to multiple ports

### Indicators:

* Short burst of connection attempts
* Incomplete TCP handshakes

---

## 🔍 UDP Scan Detection

Look for:

* ICMP Port Unreachable responses
* Multiple UDP probes

---

## 🔍 OS Detection Fingerprinting

Nmap sends:

* Unusual TCP flags
* Crafted packets
* Abnormal window sizes

Monitor for:

* TCP packets with rare flag combinations

---

# 📌 2️⃣ Logs to Monitor

### Linux Systems

* `/var/log/auth.log`
* `/var/log/syslog`
* `/var/log/messages`

### Web Servers

* `/var/log/apache2/access.log`
* `/var/log/nginx/access.log`

### Firewall Logs

* Dropped packets
* Repeated connection attempts

---

# 📌 3️⃣ Common Nmap Fingerprints

| Scan Type    | Detection Clue          |
| ------------ | ----------------------- |
| SYN Scan     | SYN flood pattern       |
| NULL Scan    | No TCP flags set        |
| FIN Scan     | Only FIN flag           |
| Xmas Scan    | FIN, PSH, URG flags     |
| Version Scan | Service banner grabbing |

---

# 📌 4️⃣ Defensive Controls

## Firewall Hardening

* Block unused ports
* Use rate limiting
* Geo-IP filtering
* Default deny policy

---

## IDS/IPS Tools

* Snort
* Suricata
* Zeek

---

## Example Snort Rule (Detect SYN Scan)

```
alert tcp any any -> any any (flags:S; msg:"Possible SYN Scan"; threshold:type threshold, track by_src, count 20, seconds 5;)
```

---

# 📌 5️⃣ Mitigation Strategies

| Threat              | Mitigation         |
| ------------------- | ------------------ |
| Port Scanning       | Rate limiting      |
| Brute Force         | Fail2Ban           |
| Service Enumeration | Disable banners    |
| OS Detection        | Firewall filtering |

---

# 📌 6️⃣ Hardening Checklist

* Disable unused services
* Close unnecessary ports
* Hide version banners
* Enable logging
* Monitor unusual traffic patterns
* Use VPN for admin panels
* Implement MFA

---

# 📌 7️⃣ Blue Team Quick Detection Guide

| Behavior               | Likely Scan |
| ---------------------- | ----------- |
| Rapid SYN packets      | -sS         |
| No flags               | NULL        |
| FIN only               | FIN scan    |
| Mixed flags            | Xmas scan   |
| ICMP unreachable flood | UDP scan    |

---

# 🏁 Final Summary

## 🔴 Red Team Focus:

* Stealth
* Enumeration depth
* Evasion
* Full attack surface mapping

## 🔵 Blue Team Focus:

* Logging
* Rate limiting
* IDS/IPS monitoring
* Minimizing exposed services

