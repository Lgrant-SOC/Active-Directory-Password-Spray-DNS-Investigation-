# Active Directory Password Spray & Wazuh Investigation

## 📋 Overview

Simulated a controlled RDP password-guessing attack from a Kali Linux attacker (`192.168.56.109`) against a Windows workstation (`192.168.56.106`) using Hydra. During validation, the Windows authentication telemetry initially displayed the source as `127.0.0.1` due to the Ethernet Adapter 1 configuration. After correcting the configuration, Event ID 4625 successfully recorded the correct remote authentication source, identifying the Kali attacker as `192.168.56.109`.

---

## 🎯 Objectives

* Execute a controlled Hydra authentication attack against RDP.
* Investigate Windows Event ID 4625 authentication failures.
* Troubleshoot the initial network configuration issue.
* Verify the correct attacker IP and workstation in Windows telemetry.
* Analyze NTLM authentication data.
* Validate the activity through Wazuh.

---

## 🔧 Network Configuration Troubleshooting

During the initial validation, Event ID 4625 recorded the authentication source as `127.0.0.1`.

The issue was traced to the VirtualBox Ethernet Adapter 1 configuration. After correcting the adapter configuration, the Windows security telemetry accurately reflected the remote authentication source.

**Initial Source:** `127.0.0.1`

**Corrected Source:** `192.168.56.109`

<img width="2506" height="1433" alt="image" src="https://github.com/user-attachments/assets/409a73b7-e1b2-4f56-80be-81858ff573ce" />


---

## ⚔️ Hydra Attack

Hydra v9.6 was executed from the Kali Linux attacker machine (`192.168.56.109`) against the Windows workstation (`192.168.56.106`) over RDP/TCP 3389 using the `TargetUser` account.

**Attack Result:** `0 valid password found`

### Attack Details

| Item        | Details                  |
| ----------- | ------------------------ |
| Attacker    | Kali Linux               |
| Attacker IP | `192.168.56.109`         |
| Target      | Windows Workstation      |
| Target IP   | `192.168.56.106`         |
| Protocol    | RDP                      |
| Port        | `3389`                   |
| Account     | `TargetUser`             |
| Tool        | Hydra v9.6               |
| Result      | `0 valid password found` |

### Evidence — Hydra Attack

<img width="1724" height="1394" alt="image" src="https://github.com/user-attachments/assets/efe821a0-d97d-4bcb-9b00-ebca5abae989" />


---

## 🔍 Windows Event Investigation

Following the authentication attempts, Windows Event Viewer was used to investigate the resulting authentication failures.

**Event ID:** `4625 — An account failed to log on`

**Result:** `Audit Failure`

The corrected Event ID 4625 provided the following authentication telemetry:

```text
Event ID:                4625
Logon Type:              3
Logon Process:           NtLmSsp
Authentication Package:  NTLM
Workstation Name:        kali-attacker
Source IP:               192.168.56.109
Result:                  Audit Failure
```

The `WorkstationName` and `IpAddress` fields provided evidence connecting the failed authentication event to the Kali attacker machine.

### Evidence — Event ID 4625

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/de6cb9e6-f974-42fd-a4d9-b0e3f811d7b2" />


### Evidence — Event 4625 Raw XML

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/98bc2719-f0cc-4d99-9cfa-b6112f737990" />


---

## 🛡️ Wazuh Detection

The Windows security telemetry was collected by the Wazuh agent and analyzed by the Wazuh manager.

The Wazuh investigation was used to validate that the Windows authentication activity was successfully collected and processed by the SIEM.

### Evidence — Wazuh Alert

<img width="2863" height="3345" alt="image" src="https://github.com/user-attachments/assets/1bace96d-95cc-4417-aefe-2057d5f19a76" />


---

## 🔗 Investigation Flow

```text
Kali Linux
192.168.56.109
      ↓
Hydra v9.6
      ↓
RDP / TCP 3389
      ↓
Windows Workstation
192.168.56.106
      ↓
Event ID 4625
      ↓
Source: 192.168.56.109
      ↓
Wazuh
```

---

## 🛠️ Tools

**Kali Linux • Hydra v9.6 • Windows • Active Directory • RDP • Windows Event Viewer • Wazuh • VirtualBox**

---

## 🧠 Skills Demonstrated

**Authentication Attack Simulation • Windows Event Analysis • Event ID 4625 Analysis • Network Troubleshooting • VirtualBox Network Configuration • NTLM Analysis • Source IP Attribution • Wazuh SIEM • Attack-to-Telemetry Correlation**




# Wazuh Custom Rule Detection README
## Wazuh Brute-Force Detection Using a Custom XML Rule## Overview
This project demonstrates the creation and testing of a custom Wazuh detection rule designed to identify repeated failed authentication attempts against a Windows Server.
The detection was tested in an isolated VirtualBox lab using Kali Linux as the source of the authentication attempts, Windows Server as the target, and Wazuh for security monitoring and alert analysis.
## Lab Environment

* Kali Linux — authentication test source
* Windows Server — monitored target
* Ubuntu — Wazuh Manager and Dashboard
* VirtualBox — isolated lab environment

## Network

| System | IP Address | Role |
|---|---|---|
| Wazuh/Ubuntu | 192.168.56.105 | Wazuh Manager |
| Windows Server | 192.168.56.106 | Monitored endpoint |
| Kali Linux | 192.168.56.109 | Authentication test source |

## Detection Objective
The objective was to create a custom Wazuh XML rule capable of identifying multiple failed authentication attempts occurring within a 60-second period.
The authentication failures generate Windows Security Event ID 4625.
## Custom Wazuh Rule
The custom XML rule is located in:
rules/local_rules.xml
The rule is designed to correlate repeated Windows failed-logon events and identify potential brute-force authentication activity.
## Testing
The test generated failed authentication attempts from the Kali Linux system against the Windows Server account TargetUser.
Windows Security Event ID 4625 was generated for the failed authentication attempts.
Wazuh successfully received the Windows security telemetry from the Windows Server agent.
During testing, five failed-logon events were observed within approximately three seconds:

* 07:26:32.4
* 07:26:33.7
* 07:26:33.7
* 07:26:33.7
* 07:26:35.3

Each event was associated with Wazuh Rule 60122, Logon Failure - Unknown....
## Evidence Screenshots
Below are the three explicit markdown commands where your screenshot files will link. Each line starting with an exclamation mark ! is an independent slot for an image:

* Screenshot 1: Windows Event Viewer (Event ID 4625 failed authentication)
* Screenshot 2: Wazuh Dashboard (Five failed logon events received)
* Screenshot 3: Custom Alert Trigger (Custom XML rule alert confirmation)

## What This Demonstrates
This project demonstrates hands-on experience with:

* Creating custom Wazuh XML detection rules
* Windows Security Event ID 4625 analysis
* Failed authentication monitoring
* Wazuh rule testing and validation
* SIEM event investigation
* Correlating authentication activity between an endpoint and SIEM
* Investigating potential brute-force authentication behavior

## Tools

* Wazuh
* Wazuh Dashboard
* Windows Event Viewer
* Windows Server
* Kali Linux
* FreeRDP
* VirtualBox



---


















