# SMB Reconnaissance & Authentication Investigation

## 🧾 Overview

This project simulates SMB reconnaissance and authentication attempts within a controlled lab environment. The objective was to detect attacker behavior and correlate endpoint logs with observed network activity.

---

## 🎯 Objectives

* Identify SMB reconnaissance activity
* Analyze authentication attempts using Windows Event Logs
* Correlate attacker actions with endpoint telemetry
* Map activity to MITRE ATT&CK techniques

---

## 🛠️ Tools Used

* Kali Linux (Attacker)
* Nmap
* smbclient
* Windows 10 (Target)
* Windows Event Viewer
* Sysmon (optional if used)
* Wazuh SIEM (if applicable)

---

## 🧪 Lab Setup

* Attacker: Kali Linux VM
* Target: Windows VM
* Network: VirtualBox internal network

---

## 🔍 Investigation

### 1. SMB Reconnaissance (Nmap Scan)

Attacker performed port scanning targeting SMB (port 445):

* Initial state: **filtered**
* Later state: **open**

This indicates potential changes in firewall behavior or service exposure.

---

### 2. SMB Enumeration Attempt

Command used:

```
smbclient -L //192.168.56.102 -N
```

Result:

```
NT_STATUS_ACCESS_DENIED
```

This indicates unauthorized access attempt against SMB shares.

---

### 3. Authentication Log Analysis (Windows)

Observed key security events:

* **Event ID 4625** → Failed logon attempts
* **Event ID 4624** → Successful logon
* **Event ID 4634** → Logoff activity
* **Event ID 5379** → Credential access (Credential Manager)

---

### 4. Log Correlation

* Multiple failed logons (4625) suggest brute-force behavior
* Successful logon (4624) following failures may indicate credential compromise
* Credential access (5379) suggests possible post-authentication activity

---

## 🚨 Findings

* SMB reconnaissance detected via Nmap scan
* Unauthorized SMB enumeration attempt identified
* Repeated failed authentication attempts observed
* Credential-related activity detected post-authentication

---

## 🧬 MITRE ATT&CK Mapping

* **T1046** – Network Service Discovery
* **T1078** – Valid Accounts
* **T1110** – Brute Force
* **T1555** – Credentials from Password Stores

---

## 📌 Indicators of Compromise (IOCs)

* Source IP: 192.168.56.X (attacker machine)
* Multiple failed logon attempts (Event ID 4625)
* SMB access attempts with denied authentication
* Credential Manager access event (5379)

---

## 📸 Screenshots

### Nmap Scan Results
[Technical Evidence](evidence1.png)
<img width="3024" height="4032" alt="IMG_4135" src="https://github.com/user-attachments/assets/0136d285-29fa-4d84-9174-3ac8558e0896" />




### SMB Access Attempt
[Technical Evidence](evidence2.png)
<img width="3024" height="4032" alt="IMG_4136" src="https://github.com/user-attachments/assets/50203b81-2c59-4bd7-b779-d2cb787be7e4" />



### Windows Event Logs (4625, 4624, 5379)
[Technical Evidence](evidence3.png)
<img width="3024" height="4032" alt="IMG_4138" src="https://github.com/user-attachments/assets/6c25f4d6-b81b-46f7-a282-092abdc401ed" />
<img width="3024" height="4032" alt="IMG_4137" src="https://github.com/user-attachments/assets/def09d40-6efc-4c79-b2bd-3148bd3235d7" />
## 🎯 Phase 2: Active Directory Authentication Investigation

### 📋 Overview
This phase simulates an active authentication brute-force spray against a target endpoint within a segregated virtualization subnet. The objective was to successfully execute the credential spray and manually isolate forensic artifacts hidden within raw host system logging.

### 🎯 Objectives
* Execute a dictionary-based password spray over port 445 (SMB) using Hydra
* Bypass native host firewalls programmatically using administrative PowerShell tools
* Extract and verify raw XML loopback metadata from Windows Event ID 4625

### 🔍 Forensic Analysis (Deep Dive)

---

## 🎯 Phase 2: Active Directory Authentication Investigation

### 📋 Overview
This phase simulates an active authentication brute-force spray against a target endpoint within a segregated virtualization subnet. The objective was to successfully execute the credential spray and manually isolate forensic artifacts hidden within raw host system logging.

### 🎯 Objectives
* Execute a dictionary-based password spray over port 445 (SMB) using Hydra
* Bypass native host firewalls programmatically using administrative PowerShell tools
* Extract and verify raw XML loopback metadata from Windows Event ID 4625
* Attribute network attack signatures to the local Security Accounts Manager (SAM) process

### 🔍 Forensic Analysis (Deep Dive)
Upon inspecting the target workstation's Windows Security Log (`eventvwr.msc`), over 13,000 background events were filtered down to isolate **Event ID 4625 (An account failed to log on)**. 

Deep inspection of the raw Event metadata under the `Details > EventData` block revealed a `ProcessName` attribution to `C:\Windows\System32\svchost.exe` and a loopback `IpAddress` of `127.0.0.1`. In a non-domain joined testing environment, this specific loopback signature confirms that network authentication requests over port 445 successfully reached the host and were routed internally to the local Security Accounts Manager (SAM) database for validation.


Upon inspecting the target workstation's Windows Security Log (`eventvwr.msc`), over 13,000 background events were filtered down to isolate **Event ID 4625 (An account failed to log on)**. 

Deep inspection of the raw Event metadata under the `Details > EventData` block revealed a `ProcessName` attribution to `C:\Windows\System32\svchost.exe` and a loopback `IpAddress` of `127.0.0.1`. In a non-domain joined testing environment, this specific loopback signature confirms that network authentication requests over port 445 successfully reached the host and were routed internally to the local Security Accounts Manager (SAM) database for validation.



---

## 📊 Outcome

Successfully identified and correlated SMB reconnaissance, authentication attempts, and credential access activity across attacker and endpoint logs.

---

## 🧠 Lessons Learned

* SMB (port 445) is a high-value target for attackers
* Failed logons (4625) are critical indicators of brute-force attempts
* Credential events (5379) can indicate post-exploitation behavior
* Correlation between network activity and logs is essential for detection


### Phase 1: Local Loopback Extraction & Verification

## 🎯 Phase 2: Active Directory Authentication Investigation

### 📋 Overview
This phase simulates an active authentication brute-force spray against a target endpoint within a segregated virtualization subnet. The objective was to successfully execute the credential spray and manually isolate forensic artifacts hidden within raw host system logging.

### 🎯 Objectives
* Execute a dictionary-based password spray over port 445 (SMB) using Hydra
* Bypass native host firewalls programmatically using administrative PowerShell tools
* Extract and verify raw XML loopback metadata from Windows Event ID 4625

### 🔍 Forensic Analysis (Deep Dive)

Upon inspecting the target workstation's Windows Security Log (`eventvwr.msc`), over 13,000 background events were filtered down to isolate **Event ID 4625 (An account failed to log on)**. 

Deep inspection of the raw Event metadata under the `Details > EventData` block revealed a `ProcessName` attribution to `C:\Windows\System32\svchost.exe` and a loopback `IpAddress` of `127.0.0.1`. In a non-domain joined testing environment, this specific loopback signature confirms that network authentication requests over port 445 successfully reached the host and were routed internally to the local Security Accounts Manager (SAM) database for validation.

<img width="585" height="1266" alt="IMG_4551" src="https://github.com/user-attachments/assets/2fdb1eb8-5e32-41e5-9c98-fb31486ca96a" />
