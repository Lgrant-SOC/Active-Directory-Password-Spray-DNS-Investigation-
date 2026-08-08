# 🎯 Active Directory Authentication Investigation

## 📋 Overview
This project simulates an aggressive authentication brute-force spray against a target Windows workstation within an isolated virtualization subnet. The objective was to successfully execute the credential spray from a Kali Linux node, bypass local host profile restrictions, and manually extract high-fidelity forensic indicators hidden within raw system logging artifacts.

---

## 🎯 Objectives
* **Execute** a dictionary-based password spray over port 445 (SMB) using Hydra.
* **Programmatically modify** host network categories from Public to Private using administrative PowerShell.
* **Filter and isolate** host authentication trails out of 13,000+ background operating system events.
* **Extract and verify** raw XML loopback metadata from Windows Event ID 4625.

---

## 🔍 Forensic Analysis & Local Loopback Isolation
Upon inspecting the target workstation's Windows Security Log (eventvwr.msc), over 13,000 background events were filtered down to isolate Event ID 4625 (An account failed to log on).

Deep inspection of the raw EventData fields revealed that the authentication attempt was associated with the process `C:\Windows\System32\svchost.exe`. The event also recorded the source IpAddress as `127.0.0.1` (loopback), indicating the failed authentication originated locally on the Windows system rather than from a remote host. This finding explained why the event did not represent the intended password spraying simulation from the Kali Linux attacker.

`[image]` `[image]` `[image]`

---

# 🎯 Active Directory Password Spraying Investigation with Wazuh

## 📋 Lab Environment
* **Attacker**: Kali Linux (IP: `192.168.56.109`)
* **Target**: Windows Server 2022 (Active Directory Domain Controller / Final IP: `192.168.56.106`)
* **Client**: Windows 10 (IP: `192.168.56.110`)
* **SIEM**: Wazuh

---

## 🎯 Project Objectives
* **Simulate** a password spraying attack against an Active Directory Domain Controller using Hydra.
* **Investigate** failed authentication events in Windows Event Logs and Wazuh.
* **Document** the troubleshooting process required to restore proper Active Directory communication before attack simulation.

---

## 🚨 Initial Problem
During the initial attack simulation, Hydra failed to generate the expected network authentication events. Windows Event ID 4625 incorrectly showed the source IP address as `127.0.0.1` instead of the Kali Linux attacker IP.

`[image]`

---

## 🔍 Investigation Symptoms
* **DNS Resolution Failure**: Windows 10 could not resolve `lab.local` and `nslookup` timed out.
* **Domain Connectivity Failure**: Windows could not locate the Domain Controller.
* **Telemetry Gap**: Hydra attack did not produce the expected remote authentication events.

`[image]`

---

## 💡 Root Cause Analysis
Investigation revealed that the Domain Controller networking had become inconsistent. The Active Directory server was using a different Host-Only IP address than expected, while the Windows 10 client was still configured to query the previous DNS server.

| Component | Configuration | Status |
| :--- | :--- | :--- |
| **Windows 10 DNS** | `192.168.56.10` | Incorrect |
| **Domain Controller** | `192.168.56.106` | Active |
| **Result** | **DNS timeout** | **Failed** |

---

## 🛠️ Corrective Actions
* **Verified** VirtualBox adapters.
* **Confirmed** Host-Only network configuration.
* **Updated** Windows 10 DNS server.
* **Verified** Domain Controller registration.
* **Tested** DNS resolution.
* **Validated** Active Directory communication.

`[image]` `[image]`

---

## ✅ Validation
* **Status**: Success.

`[image]`

* **Conclusion**: Success. This proves Active Directory was functioning again.








