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

---<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/4b9ae45c-5676-4e7b-bddd-c21b84b234c9" />


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

---<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ca4e1fb1-59d7-4385-88d9-49d2811e7de5" />


### 3. Authentication Log Analysis (Windows)

Observed key security events:

* **Event ID 4625** → Failed logon attempts
* **Event ID 4624** → Successful logon
* **Event ID 4634** → Logoff activity
* **Event ID 5379** → Credential access (Credential Manager)

---<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ecbcae93-e021-436b-988a-b540ded9d556" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/9b66e7c2-cb9a-4a29-8f2f-a7d4103e8c12" />






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

Deep inspection of the raw EventData fields revealed that the authentication attempt was associated with the process C:\Windows\System32\svchost.exe. The event also recorded the source IpAddress as 127.0.0.1 (loopback), indicating the failed authentication originated locally on the Windows system rather than from a remote host. This finding explained why the event did not represent the intended password spraying simulation from the Kali Linux attacker.



<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/02543fcd-a2b3-480e-85e2-fb75f37da9a0" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/7f473ec3-d6b8-4172-b0fb-00a890be496b" />

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ed4bbf01-4716-43c1-977d-3f519f8a35ea" />

PROJECT TITLE 
Active Directory Password Spraying Investigation with Wazuh: Troubleshooting DNS Before Detection
1. Lab Environment
Attacker:
- Kali Linux
- IP: 192.168.56.109

Target:
- Windows Server 2022
- Active Directory Domain Controller
- Final IP: 192.168.56.106

Client:
- Windows 10
- IP: 192.168.56.110

SIEM:
- Wazuh

### Lab Network Diagram

```text
               +----------------------------------------+

               |        Virtual Network (Switch)        |
```

---


2. Objective
Simulate a password spraying attack against an Active Directory Domain Controller using Hydra, investigate failed authentication events in Windows Event Logs and Wazuh, and document the troubleshooting process required to restore proper Active Directory communication before attack simulation.

4. Initial Problem
During the initial attack simulation, Hydra failed to generate the expected network authentication events. Windows Event ID 4625 showed the source IP address as 127.0.0.1 instead of the Kali Linux attacker IP.

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/37af4c24-46b8-4a4f-a659-c93faf6cd70e" />

4. Investigation
Symptoms
Windows 10 could not resolve lab.local.
nslookup timed out.
Windows could not locate the Domain Controller.
Hydra attack did not produce expected remote authentication events.

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ef138669-ab80-4f13-b6c5-5441cd9a6a23" />


DNS request timed out.
Root Cause
Investigation revealed that the Domain Controller networking had become inconsistent. The Active Directory server was using a different Host-Only IP address than expected, while the Windows 10 client was still configured to query the previous DNS server.

Component	Before
Windows 10 DNS	192.168.56.10
Domain Controller	192.168.56.106
Result	DNS timeout

5. Corrective Actions
Verified VirtualBox adapters.
Confirmed Host-Only network configuration.
Updated Windows 10 DNS server.
Verified Domain Controller registration.
Tested DNS resolution.
Validated Active Directory communication.

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/e6c2e829-f3f1-4985-99d5-4e3e2d356d01" />


<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/3f0dab75-b751-4603-a903-4da2dc206cb2" />

6. Validation

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/1377e3e9-e8a9-43ea-a87c-3082426ea116" />

Success.

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/082d620f-a903-45ae-9658-d94e3377ba78" />

Success.

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/02f5fe47-c27a-4a12-9d2c-f8c48d19521d" />

Success.
This proves Active Directory was functioning again.









