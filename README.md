# **Cybersecurity Internship — Task 1**

### **Network Reconnaissance, Port Scanning, Packet Capture & Security Analysis**

This repository documents **Task 1** of my Cybersecurity Internship at **Elevate Labs / Gudisoft Technologies**.

In this task, I built a complete cybersecurity testing environment using:

* **Ubuntu Linux (Virtual Machine)**
* **VirtualBox**
* **Bridged Networking**
* **Nmap**
* **Wireshark**
* **Local & Home Network Scanning**

This README explains the *full process, findings, risks, and learning outcomes*.

---

# 🔧 **Tools Used**

| Tool           | Purpose                             |
| -------------- | ----------------------------------- |
| Ubuntu 22.04   | OS for cybersecurity tasks          |
| VirtualBox     | Virtualization                      |
| Nmap           | Port scanning and network discovery |
| Wireshark      | Packet capturing (SYN/RST analysis) |
| ip addr        | Network identification              |
| Linux Terminal | Running scans & commands            |

---

# 🛰️ **1. Identifying Network Information**

## **NAT Network**

Using:

```bash
ip addr
```

I discovered that VirtualBox NAT assigned my VM to:

```
10.0.2.15/24
```

This scan helped me understand subnet masks, private IP ranges, and CIDR.

**Screenshot included in repo.**

---

## **Bridged Adapter — Real Home Network**

After switching to Bridged Mode:

```bash
ip addr
```

My VM received a REAL LAN IP:

```
192.168.0.7/24
```

This allowed me to scan my **actual home network** and discover real devices.

**Screenshot included in repo.**

---

# 🔍 **2. Nmap Scan — NAT Network (VirtualBox)**

Command:

```bash
sudo nmap -sS 10.0.2.0/24
```

### Findings:

| IP        | Open Ports     | Meaning                      |
| --------- | -------------- | ---------------------------- |
| 10.0.2.2  | 135, 445, 5432 | VirtualBox NAT “router”      |
| 10.0.2.3  | 53             | VirtualBox DNS proxy         |
| 10.0.2.15 | None           | VM secure (all ports closed) |

---

# 🌐 **3. Nmap Scan — Home Network (Real LAN)**

Command:

```bash
sudo nmap -sS 192.168.0.0/24
```

### **Detected 5 hosts**

Below is a professional analysis:

---

## 🟦 **192.168.0.1 — Router**

**Open ports:**

* 22 SSH
* 23 Telnet
* 53 DNS
* 80 HTTP
* 1900 UPnP

**Security Risks:**

* Telnet (port 23): ❌ Insecure, plaintext login
* UPnP: ❌ Auto port forwarding can be abused
* HTTP admin panel: Should use HTTPS

**Recommendations:**
✔ Disable Telnet
✔ Disable UPnP
✔ Enable HTTPS admin
✔ Strong password

---

## 🟩 **192.168.0.3 — Secure Device**

All 1000 ports closed → firewall ON
Likely a phone or secured laptop.

---

## 🟧 **192.168.0.6 — Windows System**

**Open ports:**

* 135 (RPC)
* 139 (NetBIOS)
* 445 (SMB)

**Threats:**

* EternalBlue
* WannaCry
* SMBGhost

**Recommendations:**
✔ Disable SMBv1
✔ Enable firewall
✔ Keep Windows updated

---

## 🟨 **192.168.0.8 — IoT Device**

**Open port:**

* 2869 (UPnP / DLNA)

Likely a smart TV / IoT device → normal.

---

## 🟩 **192.168.0.7 — My VM**

All ports closed → fully secure.

---

# 📁 **4. Saving the Scan Output**

Command:

```bash
sudo nmap -sS 192.168.0.0/24 -oN home_scan.txt
```

**File included in repo.**

---

# 🔬 **5. Wireshark Packet Capture Analysis**

I captured packets during an Nmap SYN scan.

### Observations:

### ✔ **Open Port Pattern**

```
SYN → SYN/ACK → RST
```

### ✔ **Closed Port Pattern**

```
SYN → RST
```

### ✔ **Filtered Port Pattern**

```
SYN → (no response)
```

This confirms exactly how a **TCP SYN scan (half-open scan)** works.

---

# 🔍 **6. Researching Common Services Found**

| Port | Service   | Description       | Risk                    |
| ---- | --------- | ----------------- | ----------------------- |
| 22   | SSH       | Secure login      | Safe if password strong |
| 23   | Telnet    | Remote login      | ❌ Insecure              |
| 53   | DNS       | Domain resolution | Normal                  |
| 80   | HTTP      | Router panel      | Use HTTPS               |
| 135  | RPC       | Windows service   | Exploited by malware    |
| 139  | NetBIOS   | Windows sharing   | Should be disabled      |
| 445  | SMB       | File sharing      | Target of WannaCry      |
| 1900 | UPnP      | Auto port opening | Risky                   |
| 2869 | UPNP/DLNA | IoT services      | Normal                  |

---

# 📘 **7. Theory Answers**

### **1. What is an open port?**

A port that is actively listening for connections.
Represents a communication channel and potential attack surface.

---

### **2. How does Nmap perform a TCP SYN scan?**

* Sends SYN
* If SYN/ACK: port open
* Sends RST instead of ACK (does not complete handshake)
* If RST: port closed
* If no response: filtered

---

### **3. Risks associated with open ports:**

* Unauthorized access
* Malware propagation
* Remote exploits
* Data exposure
* Reconnaissance foothold

---

### **4. TCP vs UDP Scan**

| Feature        | TCP           | UDP             |
| -------------- | ------------- | --------------- |
| Uses handshake | Yes           | No              |
| Accuracy       | High          | Low             |
| Speed          | Fast          | Slow            |
| Used for       | Web, SSH, SMB | DNS, SNMP, DHCP |

---

### **5. How to secure open ports?**

✔ Close unused ports
✔ Enable firewall
✔ Disable legacy protocols
✔ Patch systems
✔ Use encryption (TLS, SSH)
✔ Restrict access

---

### **6. Firewall's role regarding ports**

Controls:

* Which ports are open/closed
* Which IPs can connect
* Whether packets are allowed/blocked/dropped

---

### **7. What is a port scan?**

A method to discover open ports & services on a device.

Attackers use it to:

* Find vulnerabilities
* Map network structure

Defenders use it to:

* Assess security posture

---

### **8. How does Wireshark complement port scanning?**

Nmap tells you **WHAT is happening**.
Wireshark shows you **HOW it is happening at the packet level**.

Together:
✔ Full visibility
✔ Proof of behavior
✔ Better analysis

---

# 🎯 **8. Key Learnings**

Through this task I learned:

* Subnetting & CIDR
* Private vs Public IPs
* Network scanning using Nmap
* Human-level port/service analysis
* Wireshark packet interpretation
* Identifying vulnerabilities
* Hardening recommendations
* Documenting findings professionally

This task strengthened my fundamentals in:

### **Networking, Cybersecurity, Recon, Network Analysis & Documentation**

---

# 📸 **9. Screenshots Included**

* ip addr (NAT & Bridged)
* Nmap scans (NAT & Home network)
* Output files

*(MAC addresses blurred for privacy.)*

---

This repository showcases my hands-on cybersecurity skills and real-world analysis capabilities.
