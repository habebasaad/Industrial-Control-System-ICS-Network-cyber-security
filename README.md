# 🏭 ICS Network Security Design
**CSCE 4230 – Introduction to Cybersecurity | Spring 2026**

---

## 📋 Project Overview

This project designs and implements a **secure Industrial Control System (ICS) network** using Cisco Packet Tracer. It applies real-world cybersecurity principles — **Defense in Depth**, **Zero Trust**, and **Least Privilege** — to protect Operational Technology (OT) infrastructure from IT-side threats.

The core design principle: **a compromised IT zone must never mean a compromised OT zone.**

🎬 **[Working Demo (Cisco Packet Tracer)](https://drive.google.com/drive/folders/14N2T0cv0d3z3Ox14SgT3hg8EhvZ87Tb5?usp=sharing)**
---

## 🏗️ Network Architecture

The network is divided into **4 security zones** plus a remote site:

| Zone | Name | VLAN | Subnet | Key Devices |
|------|------|------|--------|-------------|
| A | IT Zone | 10 | 192.168.10.0/24 | HR-PC, Finance-PC, Admin-PC, DHCP Server, AAA Server, Wi-Fi AP |
| B | Engineering Zone | 20 | 192.168.20.0/24 | Engineer1–3, Code Repo, Simulation Server |
| C | OT Zone | 30 | 192.168.30.0/24 | Monitor-PCs, Control-System-PC, Historian Server, Telemetry Server |
| D | DMZ | 40 | 192.168.40.0/24 | Logging Server, Jump Server, Patch/Update Server |
| — | Remote Site | — | 192.168.50.0/24 | PC0, PC1 |

### Core Infrastructure

| Device | Role |
|--------|------|
| Cisco 3560 Multilayer Switch | VLAN routing via SVIs |
| Cisco 2911 Edge Router | Internet gateway + IPsec VPN |
| Cisco ASA 5506-X | Perimeter firewall |
| AAA Server | Centralized TACACS+ authentication |
| Logging Server (192.168.40.30) | Central syslog collection |
| Jump Server (192.168.40.10) | SSH gateway to OT zone |

---

## 🔐 Security Principles

### 1. Zero Trust Between Zones
No zone trusts another by default. All inter-zone traffic is explicitly permitted or denied by ACLs and firewall rules.

### 2. OT Air-Gap by Policy
Zone C has **no internet access** under any circumstance. It cannot initiate connections to IT or ENG zones. The only inbound path is SSH through the Jump Server.

### 3. DMZ as the Controlled Gateway
The DMZ acts as a buffer between internal zones and the outside world. All shared services (logging, updates, Jump Server) live here.

### 4. Centralized Authentication & Logging
All devices authenticate via **TACACS+** through the AAA Server. All logs go to the central Logging Server — every login, command, and dropped packet is recorded.

### 5. Least Privilege
Each zone can only reach what it absolutely needs. ENG can only reach OT via the Jump Server.

---

## 🧪 Testing Summary

| Test | Result |
|------|--------|
| IT → OT (direct) | ❌ BLOCKED |
| IT → ENG | ✅ ALLOWED |
| IT → DMZ | ✅ ALLOWED |
| OT → IT (outbound) | ❌ BLOCKED |
| OT → Internet | ❌ BLOCKED |
| ENG → OT via Jump Server (SSH) | ✅ ALLOWED |
| ENG → OT (direct) | ❌ BLOCKED |
| Remote Site → Internal (VPN) | ✅ ALLOWED |
| Remote Site → Internal (no VPN) | ❌ BLOCKED |
| Wireless Laptop → IT-WiFi (WPA2) | ✅ CONNECTED |
| DHCP (IT Zone only) | ✅ WORKING |

---

## 🛡️ Implemented Security Features

- **VLAN Segmentation** — VLANs 10, 20, 30, 40, 99 (native/mgmt)
- **Inter-VLAN Routing via SVIs** — Controlled by ACLs
- **Extended ACLs** — `OT_RESTRICT`, `ENG_TO_OT`, `DMZ_TO_OT`, `OUTBOUND`, `INBOUND`
- **Cisco ASA 5506-X Firewall** — Zone isolation, NAT, ICMP inspection
- **Zone-Based Firewall (ZBF)** — Additional perimeter control on Edge Router
- **SSH (Jump Server)** — Only path to OT; replaces Telnet
- **TACACS+ AAA** — Centralized auth with fallback to local
- **IPsec Site-to-Site VPN** — AES encryption, ESP-SHA-HMAC, IKE Group 2
- **WPA2-PSK Wi-Fi** — AES encryption, IT zone only (SSID: IT-WiFi)
- **Layer 2 Security** — Port Security, DHCP Snooping, BPDU Guard, Dynamic ARP Inspection (DAI)
- **Centralized Syslog** — All devices log to 192.168.40.30 via UDP 514
- **Spanning Tree Protocol (STP)** — Prevents broadcast storms

---

## ⚠️ Threat & Risk Summary

| # | Threat | Risk Level |
|---|--------|------------|
| 1 | Unauthorized OT Access | HIGH |
| 2 | DHCP Spoofing | HIGH |
| 3 | Syslog Tampering | MEDIUM |
| 4 | Brute Force on AAA | HIGH |
| 5 | Rogue Wireless AP | HIGH |
| 6 | VPN Credential Theft | MEDIUM |
| 7 | Lateral Movement ENG→OT | HIGH |
| 8 | OT Internet Access | MEDIUM |
| 9 | Unencrypted Traffic Interception | HIGH |
| 10 | Jump Server Compromise | MEDIUM |

---

## 🛠️ Tools & Technologies

- **Cisco Packet Tracer** — Network simulation
- **Cisco IOS** — Switch and router configuration
- **Cisco ASA** — Firewall management
- **Wireshark** — Packet capture and analysis
- **TACACS+** — AAA protocol
- **IPsec / IKEv1** — VPN tunneling
- **SSH v2** — Secure remote management

---

## 📄 License

This project was created for academic purposes as part of CSCE 4230.
