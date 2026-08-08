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


<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/70e0ec08-ee4e-49ce-8e08-a6b7bf2ff15d" />
 
<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/a05f2ea6-2e38-4d43-8da1-b2868a8658c2" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ed5155a7-9a47-4987-b563-f4534f299377" />






---

## 💡 Root Cause
The Domain Controller's Host-Only network IP changed unexpectedly, leaving the Windows 10 client configured to query a non-existent, stale DNS server.

| Component | Configuration | Status |
| :--- | :--- | :--- |
| **Windows 10 DNS** | `192.168.56.10` | Stale / Broken |
| **Domain Controller** | `192.168.56.106` | Active |
| **Result** | **DNS Timeout** | **Failed** |

### 📸 Configuration Mismatch Proof

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/24082365-d93a-4329-a972-df1b77f31af1" />


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








