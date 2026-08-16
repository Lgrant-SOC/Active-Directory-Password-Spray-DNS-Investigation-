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

### Evidence — Initial Event 4625

<!-- INSERT INITIAL EVENT 4625 SCREENSHOT HERE -->

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

<!-- INSERT HYDRA SCREENSHOT HERE -->

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

<!-- INSERT EVENT 4625 SCREENSHOT HERE -->

### Evidence — Corrected Event 4625 Raw XML

<!-- INSERT CORRECTED XML SCREENSHOT HERE -->

---

## 🛡️ Wazuh Detection

The Windows security telemetry was collected by the Wazuh agent and analyzed by the Wazuh manager.

The Wazuh investigation was used to validate that the Windows authentication activity was successfully collected and processed by the SIEM.

### Evidence — Wazuh Alert

<!-- INSERT WAZUH SCREENSHOT HERE -->

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


---


















