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

### 📸 Security Log Forensic Artifacts
`[Insert Event Viewer 13,419 Unfiltered Baseline Screenshot Here]`
`[Insert Filtered Event ID 4625 Loopback 127.0.0.1 Detail Screenshot Here]`

---

## 🚨 The Problem & Symptoms
During attack simulation, Hydra failed to generate remote network authentication events. Windows Event ID 4625 incorrectly flagged the source IP as `127.0.0.1` (local loopback) because the target host was isolated from the domain structure.
* **DNS Failure**: `nslookup lab.local` timed out completely against the configured server.
* **Tool Connection Error**: Hydra returned `[ERROR] freerdp: The Connection failed` due to this routing mismatch.

```bash
# Initial attack command executed on Kali Linux
hydra -l TargetUser -P /usr/share/wordlists/fasttrack.txt rdp://192.168.56.106
```

### 📸 Network & Tool Triage Evidence
`[Insert Command Prompt nslookup Timeout Screenshot Here]`
`[Insert Kali Linux Hydra Connection Failure Screenshot Here]`

---

## 💡 Root Cause
The Domain Controller's Host-Only network IP changed unexpectedly, leaving the Windows 10 client configured to query a non-existent, stale DNS server.

| Component | Configuration | Status |
| :--- | :--- | :--- |
| **Windows 10 DNS** | `192.168.56.10` | Stale / Broken |
| **Domain Controller** | `192.168.56.106` | Active |
| **Result** | **DNS Timeout** | **Failed** |

### 📸 Configuration Mismatch Proof
`[Insert Side-by-Side Split Screen Server Config vs Client DNS Screenshot Here]`

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
`[Insert IPv4 Route Table Screenshot Here]`
`[Insert IPv6 Route Table Screenshot Here]`
`[Insert Dual-Ping Success Screenshot Here]`
`[Insert ARP Cache Table - Resolved .106 MAC Entry Screenshot Here]`







