# 🎯 Active Directory Password Spray & DNS Investigation

## 📋 Overview
Simulated an aggressive authentication brute-force spray using Hydra against a Windows workstation. Diagnosed and resolved severe Active Directory communication issues caused by an out-of-sync DNS configuration, restoring full network telemetry.

---

## 🎯 Objectives
* **Execute** a dictionary-based password spray over port 445 (SMB) using Hydra.
* **Isolate** authentications out of 13,000+ background system events using Event Viewer.
* **Verify** raw XML loopback metadata from Windows Event ID 4625.

---

## 🚨 The Problem & Symptoms
During attack simulation, Hydra failed to generate remote network authentication events. Windows Event ID 4625 incorrectly flagged the source IP as `127.0.0.1` (local loopback).
* **DNS Failure**: `nslookup lab.local` timed out.
* **Connectivity Failure**: Windows 10 could not locate the Domain Controller.

`[Insert nslookup Screenshot Here]`

---

## 💡 Root Cause
The Domain Controller's Host-Only network IP changed unexpectedly, leaving the Windows 10 client configured to query a non-existent, stale DNS server.

| Component | Configuration | Status |
| :--- | :--- | :--- |
| **Windows 10 DNS** | `192.168.56.10` | Stale / Broken |
| **Domain Controller** | `192.168.56.106` | Active |
| **Result** | **DNS Timeout** | **Failed** |

---

## 🛠️ Corrective Actions & Resolution
1. **Updated** Windows 10 client DNS server to match the active Domain Controller IP (`192.168.56.106`).
2. **Verified** Active Directory Domain Controller registration and network adapters.
3. **Validated** DNS resolution and restored proper lab communication.

---<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/7c1eb16e-3a2a-4b57-8061-f2d7686211df" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/575355d4-5ea5-4a29-9b09-655de8b45739" />



## ✅ Validation
* **Status**: Success. Active Directory communication fully restored.

`<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/0aeeefe2-2953-40cc-aa08-f236176b85ad" />
[Insert Success/Validation 








