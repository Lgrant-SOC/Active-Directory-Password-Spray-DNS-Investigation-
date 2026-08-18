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

# Wazuh Custom Rule README
## Wazuh Brute-Force Detection Using a Custom XML Rule## 📋 Overview
This project demonstrates the creation and testing of a custom Wazuh detection rule designed to identify repeated failed authentication attempts against a Windows Server.
The detection architecture was deployed and tested in an isolated VirtualBox lab using Kali Linux as the source of the authentication attacks, a Windows Server endpoint as the target, and an Ubuntu instance hosting the Wazuh Manager to handle security monitoring and alert analysis.
## 🎯 Detection Objective
The primary objective was to engineer a custom Wazuh XML rule capable of identifying multiple failed authentication attempts occurring within a tight 60-second window.

* The target authentication failures generate standard Windows security logs under Event ID 4625.
* The custom rule correlates these distinct telemetry events to flag active brute-force patterns before generating a high-priority alert.

## 🛠️ Lab Environment & Tools## Core Components

* Authentication Test Source: Kali Linux (FreeRDP)
* Monitored Endpoint Target: Windows Server (Windows Event Viewer)
* SIEM Architecture: Ubuntu Server (Wazuh Manager & Wazuh Dashboard)
* Hypervisor Platform: VirtualBox (Isolated Host-Only Network)

## Network Configuration

| System | IP Address | Role / Function |
|---|---|---|
| Wazuh / Ubuntu | 192.168.56.105 | Wazuh Manager & Central SIEM Dashboard |
| Windows Server | 192.168.56.106 | Monitored Target Endpoint (Wazuh Agent installed) |
| Kali Linux | 192.168.56.109 | Red Team Authentication Test Source |

## ⚙️ Custom Detection Rule
The logical correlation engine is defined within the local rule configuration file:
rules/local_rules.xml
This custom XML block is engineered to parse incoming log payloads from the Windows agent, automatically evaluating frequency thresholds to differentiate baseline administrative mistakes from programmatic brute-force behavior.
## 🧪 Testing & Validation
To validate the logic, a sequence of rapid, intentional failed authentication attempts was executed from the Kali Linux attack machine against the target Windows Server utilizing the user account TargetUser.

   1. Telemetry Generation: The endpoint successfully generated Event ID 4625 (An account failed to log on) for each attempt.
   2. SIEM Ingestion: The active Wazuh Agent on the server safely transmitted the security events to the manager.
   3. Threshold Analysis: During the execution window, five distinct failed-logon events were successfully recorded within an approximate three-second burst:
   * 07:26:32.4
      * 07:26:33.7
      * 07:26:33.7
      * 07:26:33.7
      * 07:26:35.3
   4. Rule Matching: Each individual transaction was initially categorized by the SIEM under default Wazuh Rule 60122 (Logon Failure - Unknown...), successfully satisfying the parent prerequisites for our custom threshold rule.

## 📸 Telemetry Evidence## Windows Event Viewer
Local confirmation on the target workstation proving Event ID 4625 was processed during the password-guessing window.
## Wazuh Event Ingestion
The Wazuh Dashboard validating the real-time collection of the 4625 log stream originating specifically from Agent WIN-E1SKA42GQ7G.
## Custom Alert Triggered
Verification that the threshold rule successfully evaluated the rapid event volume and tripped the custom rule alert.
## 🧠 Key Demonstrations
Reviewing this repository highlights actionable experience across several security operations domains:

* Custom SIEM Engineering: Writing, deploying, and tuning custom XML-based detection mechanisms in Wazuh.
* Windows Forensic Analysis: Deep assessment of security log structures and Event ID 4625 fields.
* Identity Monitoring: Tracking authentication failure attributes to pinpoint targeted local or domain accounts.
* Rule Logic Validation: Designing comprehensive testing strategies to confirm detection triggers while avoiding false negatives.
* Telemetry Pipeline Auditing: Verifying end-to-end log routing consistency from a remote endpoint agent into a centralized SIEM dashboard.
* Threat Behavioral Analysis: Modeling and monitoring explicit adversarial behaviors, such as network-based brute-force attacks.



---


















