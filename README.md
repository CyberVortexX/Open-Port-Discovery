# 🔍 Open Port Discovery — Network Reconnaissance Report

## Overview

This report documents the results of an active host and open port discovery scan performed on the `180.180.3.0/24` subnet using **Nmap**. The goal was to identify live hosts, enumerate exposed services, and assess the potential attack surface of the network.

---

## Scope

| Detail | Value |
|---|---|
| Target Subnet | `180.180.3.0/24` |
| Total Hosts Scanned | 256 |
| Active Hosts Found | 9 |
| Tools Used | Nmap |

---

## Commands Used

### Host Discovery (Ping Sweep)
```bash
nmap -sn 180.180.3.0/24
```
Performs a ping sweep to identify live hosts without port scanning.

### Port Scan
```bash
nmap 180.180.3.0/24
```
Scans the subnet for open ports on all discovered live hosts.

---

## Results

### Active Hosts & Open Ports

| Host | Open Ports | Likely Services |
|---|---|---|
| `180.180.3.4` | 135, 139, 445, 3306 | RPC, NetBIOS, SMB, MySQL |
| `180.180.3.14` | 80, 554, 8000, 8443 | HTTP, RTSP, HTTP-Alt, HTTPS-Alt |
| `180.180.3.84` | 135, 139, 445 | RPC, NetBIOS, SMB |
| `180.180.3.167` | 80, 443 | HTTP, HTTPS |
| `180.180.3.226` | 80, 443 | HTTP, HTTPS |

> **Note:** 4 additional active hosts were identified but had no open ports detected under the default Nmap scan.

---

## Security Analysis

### ⚠️ SMB Services (Ports 135, 139, 445)
- Detected on: `180.180.3.4`, `180.180.3.84`
- SMB (Server Message Block) is a common target for lateral movement and exploitation.
- Notable vulnerabilities include **EternalBlue (MS17-010)** and related ransomware delivery methods.
- **Recommendation:** Restrict SMB access to internal trusted hosts only. Ensure patches are applied and SMB v1 is disabled.

### ⚠️ Exposed MySQL (Port 3306)
- Detected on: `180.180.3.4`
- A database port exposed on the network level is a significant risk — it should never be publicly accessible.
- **Recommendation:** Bind MySQL to `localhost` or restrict access via firewall rules to authorised IPs only.

### ℹ️ Web Management Interfaces (Ports 80, 443, 8000, 8443)
- Detected on: `180.180.3.14`, `180.180.3.167`, `180.180.3.226`
- Web interfaces may expose admin panels, login portals, or device dashboards.
- **Recommendation:** Enforce HTTPS, disable HTTP (port 80), and restrict access to management interfaces.

### ℹ️ RTSP Service (Port 554)
- Detected on: `180.180.3.14`
- RTSP (Real Time Streaming Protocol) indicates a possible IP camera or surveillance system.
- **Recommendation:** Ensure the device uses authentication, is on a segregated VLAN, and is not exposed externally.

---

## Risk Summary

| Severity | Finding |
|---|---|
| 🔴 High | MySQL (3306) exposed on the network |
| 🔴 High | SMB services open on multiple Windows hosts |
| 🟡 Medium | Web management interfaces accessible over HTTP |
| 🟡 Medium | RTSP stream potentially unauthenticated |
| 🟢 Low | Standard HTTPS web services on 167 and 226 |

---

## Conclusion

The scan successfully identified **9 active hosts** and **5 hosts with open ports** across the `180.180.3.0/24` subnet. Key concerns include exposed SMB services vulnerable to known exploits, a publicly accessible MySQL port, and potential unauthenticated access to a surveillance stream. Addressing these findings will significantly reduce the network's attack surface.

---

## Disclaimer

> This scan was conducted for authorised security assessment purposes only. Unauthorised port scanning or network reconnaissance may violate applicable laws and regulations.
