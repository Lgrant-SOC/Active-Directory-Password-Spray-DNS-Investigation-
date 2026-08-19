# Cybersecurity Lab Portfolio

## Project 1: Active Directory Password Spray & Wazuh Investigation

### 📋 Overview
Simulated a controlled RDP password-guessing attack from a Kali Linux attacker (192.168.56.109) against a Windows workstation (192.168.56.106) using Hydra. During validation, the Windows authentication telemetry initially displayed the source as 127.0.0.1 due to the Ethernet Adapter 1 configuration. After correcting the configuration, Event ID 4625 successfully recorded the correct remote authentication source, identifying the Kali attacker as 192.168.56.109.

### 🎯 Objectives
* Execute a controlled Hydra authentication attack against RDP.
* Investigate Windows Event ID 4625 authentication failures.
* Troubleshoot the initial network configuration issue.
* Verify the correct attacker IP and workstation in Windows telemetry.
* Analyze NTLM authentication data.
* Validate the activity through Wazuh.

### 🔧 Network Configuration Troubleshooting
During the initial validation, Event ID 4625 recorded the authentication source as 127.0.0.1.

The issue was traced to the VirtualBox Ethernet Adapter 1 configuration. After correcting the adapter configuration, the Windows security telemetry accurately reflected the remote authentication source.

Initial Source: 127.0.0.1

Corrected Source: 192.168.56.109

<img width="1170" height="676" alt="image" src="https://github.com/user-attachments/assets/3acb8efe-cfcc-49ab-8ceb-09f256fdf7c5" />



### ⚔️ Hydra Attack
Hydra v9.6 was executed from the Kali Linux attacker machine (192.168.56.109) against the Windows workstation (192.168.56.106) over RDP/TCP 3389 using the TargetUser account.

Attack Result: 0 valid password found

#### Attack Details
Item	Details
Attacker	Kali Linux
Attacker IP	192.168.56.109
Target	Windows Workstation
Target IP	192.168.56.106
Protocol	RDP
Port	3389
Account	TargetUser
Tool	Hydra v9.6
Result	0 valid password found

#### Evidence — Hydra Attack
<img width="1169" height="846" alt="image" src="https://github.com/user-attachments/assets/7e9ab8b9-9e01-4030-bb2f-99e0d411b29b" />


### 🔍 Windows Event Investigation
Following the authentication attempts, Windows Event Viewer was used to investigate the resulting authentication failures.

Event ID: 4625 — An account failed to log on

Result: Audit Failure

The corrected Event ID 4625 provided the following authentication telemetry:

Event ID:                4625
Logon Type:              3
Logon Process:           NtLmSsp
Authentication Package:  NTLM
Workstation Name:        kali-attacker
Source IP:               192.168.56.109
Result:                  Audit Failure

The WorkstationName and IpAddress fields provided evidence connecting the failed authentication event to the Kali attacker machine.

#### Evidence — Event ID 4625
<img width="1168" height="1178" alt="image" src="https://github.com/user-attachments/assets/62bcc641-2285-49cf-8366-681052c272dc" />


#### Evidence — Event 4625 Raw XML
<img width="1169" height="1113" alt="image" src="https://github.com/user-attachments/assets/3f12bbbe-6a04-4336-ba9a-f879b95e0e06" />


### 🛡️ Wazuh Detection
The Windows security telemetry was collected by the Wazuh agent and analyzed by the Wazuh manager.

The Wazuh investigation was used to validate that the Windows authentication activity was successfully collected and processed by the SIEM.

#### Evidence — Wazuh Alert
<img width="1169" height="1186" alt="image" src="https://github.com/user-attachments/assets/4d59d44f-dd8e-4b96-a7b4-eafb73a8ebd9" />


### 🔗 Investigation Flow
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

### 🛠️ Tools
Kali Linux • Hydra v9.6 • Windows • Active Directory • RDP • Windows Event Viewer • Wazuh • VirtualBox

### 🧠 Skills Demonstrated
Authentication Attack Simulation • Windows Event Analysis • Event ID 4625 Analysis • Network Troubleshooting • VirtualBox Network Configuration • NTLM Analysis • Source IP Attribution • Wazuh SIEM • Attack-to-Telemetry Correlation


## Project 2: Wazuh Brute-Force Detection Using a Custom XML Rule

### Overview
This project demonstrates the creation and testing of a custom Wazuh detection rule designed to identify repeated failed authentication attempts against a Windows Server.

The detection was tested in an isolated VirtualBox lab using Kali Linux as the source of the authentication attempts, Windows Server as the target, and Wazuh for security monitoring and alert analysis.

### Lab Environment
- Kali Linux — authentication test source
- Windows Server — monitored target
- Ubuntu — Wazuh Manager and Dashboard
- VirtualBox — isolated lab environment

### Network

| System | IP Address | Role |
|---|---|---|
| Wazuh/Ubuntu | 192.168.56.105 | Wazuh Manager |
| Windows Server | 192.168.56.106 | Monitored endpoint |
| Kali Linux | 192.168.56.109 | Authentication test source |

### Detection Objective
The objective was to create a custom Wazuh XML rule capable of identifying multiple failed authentication attempts occurring within a 60-second period.

The authentication failures generate Windows Security Event ID 4625.

### Custom Wazuh Rule
The custom XML rule is located in:

`rules/local_rules.xml`

The rule is designed to correlate repeated Windows failed-logon events and identify potential brute-force authentication activity.
```xml
<group name="windows, security,">
  <rule id="100002" level="10" frequency="5" timeframe="60">
    <if_matched_sid>60122</if_matched_sid>
    <description>Possible Windows RDP Brute Force Attempt</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
</group>
```



### Testing
The test generated failed authentication attempts from the Kali Linux system against the Windows Server account `TargetUser`.

Windows Security Event ID 4625 was generated for the failed authentication attempts.

Wazuh successfully received the Windows security telemetry from the Windows Server agent.

During testing, five failed-logon events were observed within approximately three seconds:

- 07:26:32.4
- 07:26:33.7
- 07:26:33.7
- 07:26:33.7
- 07:26:35.3

Each event was associated with Wazuh Rule 60122, `Logon Failure - Unknown...`.

### Evidence Screenshots
* **Screenshot 1: Hydra Terminal Authentication Attack**
<img width="3024" height="3152" alt="image" src="https://github.com/user-attachments/assets/4e68928a-abc0-4eff-b99d-cac891b33291" />



* **Screenshot 2: Wazuh Threat Hunting Dashboard Event Ingestion**
<img width="2871" height="2686" alt="image" src="https://github.com/user-attachments/assets/e9920814-548a-4929-bd8a-4dc5c4e15616" />



* **Screenshot 3: Wazuh Document Details Forensic Event Data**
<img width="2882" height="2809" alt="image" src="https://github.com/user-attachments/assets/c727742e-2293-4032-80ba-414c73590831" />



### What This Demonstrates
This project demonstrates hands-on experience with:
- Creating custom Wazuh XML detection rules
- Windows Security Event ID 4625 analysis
- Failed authentication monitoring
- Wazuh rule testing and validation
- SIEM event investigation
- Correlating authentication activity between an endpoint and SIEM
- Investigating potential brute-force authentication behavior

### Tools
- Wazuh
- Wazuh Dashboard
- Windows Event Viewer
- Windows Server
- Kali Linux
- FreeRDP
- VirtualBox





---


















