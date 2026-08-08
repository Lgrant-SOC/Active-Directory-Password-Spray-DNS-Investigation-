# 🎯 Active Directory Password Spray & DNS Investigation

## 📋 Overview
Simulated an aggressive authentication brute-force spray using Hydra against a Windows workstation. Diagnosed and resolved severe Active Directory communication issues caused by an out-of-sync DNS configuration, restoring full network telemetry.

---

## 🎯 Objectives
* **Execute** a dictionary-based password spray over port 445 (SMB) using Hydra.
* **Isolate** authentications out of 13,000+ background system events using Event Viewer.
* **Verify** raw XML loopback metadata from Windows Event ID 4625.

---

## 🔍 Forensic Analysis & Local Loopback Isolation
Upon inspecting the target workstation's Windows Security Log (eventvwr.msc), background events were filtered down to isolate Event ID 4625 (An account failed to log on).

Deep inspection of the raw EventData fields revealed that the authentication attempt was associated with the process `C:\Windows\System32\svchost.exe`. The event recorded the source IpAddress as `127.0.0.1` (loopback), indicating the failed authentication originated locally on the Windows system rather than from a remote host. 

<img width="918" height="979" alt="image" src="https://github.com/user-attachments/assets/f9feb8d5-de7b-4231-a8ae-d0fc884a4600" />


<img width="2506" height="1433" alt="image" src="https://github.com/user-attachments/assets/bfc063d0-6ad6-4b83-8e3a-8024fa42a2c3" />




---

## 🚨 The Problem & Symptoms
During attack simulation, Hydra failed to generate remote network authentication events. Windows Event ID 4625 incorrectly flagged the source IP as `127.0.0.1` (local loopback) because the target host was isolated from the domain structure.
* **DNS Failure**: `nslookup lab.local` timed out completely against the configured server.
* **Tool Connection Error**: Hydra returned `[ERROR] freerdp: The Connection failed` due to this routing mismatch.

<img width="3024" height="2098" alt="image" src="https://github.com/user-attachments/assets/f5e62989-1872-4e46-ab08-7e1b5569bc31" />


<img width="1899" height="1359" alt="image" src="https://github.com/user-attachments/assets/9ed7b7c6-f9ec-486b-802e-78a9b86c69d7" />

 
<img width="2620" height="1635" alt="image" src="https://github.com/user-attachments/assets/e5df4d93-b637-45fb-b19d-a69e5c27ac2f" />


<img width="1111" height="937" alt="image" src="https://github.com/user-attachments/assets/825e172f-aeaa-4410-9e4e-bf44a298ef83" />







---

## 💡 Root Cause
The Domain Controller's Host-Only network IP changed unexpectedly, leaving the Windows 10 client configured to query a non-existent, stale DNS server.

| Component | Configuration | Status |
| :--- | :--- | :--- |
| **Windows 10 DNS** | `192.168.56.10` | Stale / Broken |
| **Domain Controller** | `192.168.56.106` | Active |
| **Result** | **DNS Timeout** | **Failed** |

### 📸 Configuration Mismatch Proof

<img width="3024" height="926" alt="image" src="https://github.com/user-attachments/assets/61dde95b-5ce2-4bee-bcc1-845ca0a2904d" />



---

## 🛠️ Corrective Actions & Resolution
1. **Updated** Windows 10 client DNS server to match the active Domain Controller IP (`192.168.56.106`).
2. **Verified** Active Directory Domain Controller registration and network adapters.
3. **Validated** DNS resolution and restored proper lab communication.

---

## ✅ Validation & Verification Metrics
* **Status**: Success. Active Directory domain communication has been fully restored.
* **Verification Method**: Conducted differential network connectivity testing. The stale configuration target (`192.168.56.10`) remains safely offline, while the newly updated Domain Controller target (`192.168.56.106`) instantly responds with 0% packet loss and active MAC address cache mapping.

### 📸 Post-Fix Routing & Connectivity Evidence
<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/f2297b77-5ff6-4274-8c18-f3379d868211" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/155f7ca0-4d69-4fef-b906-104aeac32644" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/4227d542-b5fe-43ea-b07f-8b94e14490a6" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ef3e6678-3fa2-4888-951b-b9c85c06c84a" />








