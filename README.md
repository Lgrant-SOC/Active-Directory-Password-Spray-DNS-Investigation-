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


2. Objective

Simulate a password spraying attack against an Active Directory Domain Controller using Hydra, investigate failed authentication events in Windows Event Logs and Wazuh, and document the troubleshooting process required to restore proper Active Directory communication before attack simulation.

4. Initial Problem
   
During the initial attack simulation, Hydra failed to generate the expected network authentication events. Windows Event ID 4625 showed the source IP address as 127.0.0.1 instead of the Kali Linux attacker IP.

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/37af4c24-46b8-4a4f-a659-c93faf6cd70e" />

4. Investigation Symptoms 

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









