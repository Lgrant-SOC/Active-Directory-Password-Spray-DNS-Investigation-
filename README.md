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
* Sysmon 
* Wazuh SIEM 

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



## 🎯 Active Directory Authentication Investigation

### 📋 Overview
This project simulates an aggressive authentication brute-force spray against a target Windows workstation within an isolated virtualization subnet. The objective was to successfully execute the credential spray from a Kali Linux node, bypass local host profile restrictions, and manually extract high-fidelity forensic indicators hidden within raw system logging artifacts.

### 🎯 Objectives
* Execute a dictionary-based password spray over port 445 (SMB) using Hydra.
* Programmatically modify host network categories from Public to Private using administrative PowerShell.
* Filter and isolate host authentication trails out of 13,000+ background operating system events.
* Extract and verify raw XML loopback metadata from Windows Event ID 4625.

---

## 🔍 Forensic Analysis & Local Loopback Isolation

Upon inspecting the target workstation's Windows Security Log (`eventvwr.msc`), over 13,000 background events were filtered down to isolate **Event ID 4625 (An account failed to log on)**. 

Deep inspection of the raw Event data under the `Details > EventData` block revealed a `ProcessName` attribution to `C:\Windows\System32\svchost.exe` and a loopback `IpAddress` of `127.0.0.1`. In a non-domain joined testing environment, this specific loopback signature confirms that network authentication requests over port 445 successfully reached the host and were routed internally to the local Security Accounts Manager (SAM) database for validation.



<img width="3024" height="4032" alt="IMG_4530" src="https://github.com/user-attachments/assets/0c3fe0fa-3aee-4baa-8e0e-c948d9e648e6" />




<img width="3024" height="4032" alt="IMG_4545" src="https://github.com/user-attachments/assets/cf7a3470-cf63-477c-b8c9-9e298191059a" />

