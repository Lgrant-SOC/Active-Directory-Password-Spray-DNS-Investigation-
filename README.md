CYBERSECURITY LAB PORTFOLIO 

PROJECT 1: ACTIVE DIRECTORY AND PASSWORD-SPRAY ALONG WITH WAZUH INVESTIGATION

 OVERVIEW
Simulated a controlled RDP password-guessing attack from a Kali Linux attacker (192.168.56.109) against a Windows workstation (192.168.56.106) using Hydra. During validation, the Windows authentication telemetry initially displayed the source as 127.0.0.1 due to the Ethernet Adapter 1 configuration. After correcting the configuration, Event ID 4625 successfully recorded the correct remote authentication source, identifying the Kali attacker as 192.168.56.109.

 OBJECTIVE 
* Execute a controlled Hydra authentication attack against RDP.
* Investigate Windows Event ID 4625 authentication failures.
* Troubleshoot the initial network configuration issue.
* Verify the correct attacker IP and workstation in Windows telemetry.
* Analyze NTLM authentication data.
* Validate the activity through Wazuh.

 Network Configuration Troubleshooting
During the initial validation, Event ID 4625 recorded the authentication source as 127.0.0.1.

The issue was traced to the VirtualBox Ethernet Adapter 1 configuration. After correcting the adapter configuration, the Windows security telemetry accurately reflected the remote authentication source.

Initial Source: 127.0.0.1

Corrected Source: 192.168.56.109

<img width="1170" height="676" alt="image" src="https://github.com/user-attachments/assets/3acb8efe-cfcc-49ab-8ceb-09f256fdf7c5" />



 Hydra Attack
Hydra v9.6 was executed from the Kali Linux attacker machine (192.168.56.109) against the Windows workstation (192.168.56.106) over RDP/TCP 3389 using the TargetUser account.

Attack Result: 0 valid password found

 Attack Details
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

 EVIDENCE  — Hydra Attack
<img width="1169" height="846" alt="image" src="https://github.com/user-attachments/assets/7e9ab8b9-9e01-4030-bb2f-99e0d411b29b" />


 Windows Event Investigation
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

EVIDENCE — Event ID 4625
<img width="1168" height="1178" alt="image" src="https://github.com/user-attachments/assets/62bcc641-2285-49cf-8366-681052c272dc" />

 EVIDENCE — Event 4625 Raw XML
<img width="1169" height="1113" alt="image" src="https://github.com/user-attachments/assets/3f12bbbe-6a04-4336-ba9a-f879b95e0e06" />


 Wazuh Detection
The Windows security telemetry was collected by the Wazuh agent and analyzed by the Wazuh manager.

The Wazuh investigation was used to validate that the Windows authentication activity was successfully collected and processed by the SIEM.

 EVIDENCE  — Wazuh Alert
<img width="1169" height="1186" alt="image" src="https://github.com/user-attachments/assets/4d59d44f-dd8e-4b96-a7b4-eafb73a8ebd9" />


 INVESTIGATION FLOW
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

 TOOLS 
Kali Linux • Hydra v9.6 • Windows • Active Directory • RDP • Windows Event Viewer • Wazuh • VirtualBox

 SKILLS DEMONSTRATED 
Authentication Attack Simulation • Windows Event Analysis • Event ID 4625 Analysis • Network Troubleshooting • VirtualBox Network Configuration • NTLM Analysis • Source IP Attribution • Wazuh SIEM • Attack-to-Telemetry Correlation


 PROJECT 2:WAZUH BRUTE-FORCE DETECTION USING A CUSTOM XML RULE 

 OVERVIEW 
This project demonstrates the creation and testing of a custom Wazuh detection rule designed to identify repeated failed authentication attempts against a Windows Server.

The detection was tested in an isolated VirtualBox lab using Kali Linux as the source of the authentication attempts, Windows Server as the target, and Wazuh for security monitoring and alert analysis.

 LAB ENVIRONMENT 
- Kali Linux — authentication test source
- Windows Server — monitored target
- Ubuntu — Wazuh Manager and Dashboard
- VirtualBox — isolated lab environment

 NETWORK 

| System | IP Address | Role |
|---|---|---|
| Wazuh/Ubuntu | 192.168.56.105 | Wazuh Manager |
| Windows Server | 192.168.56.106 | Monitored endpoint |
| Kali Linux | 192.168.56.109 | Authentication test source |

 DETECTION OBJECTIVE 
The objective was to create a custom Wazuh XML rule capable of identifying multiple failed authentication attempts occurring within a 60-second period.

The authentication failures generate Windows Security Event ID 4625.

 Custom Wazuh Rule
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



 TESTING
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

 EVIDENCE
* **Screenshot 1: Hydra Terminal Authentication Attack**
<img width="3024" height="3152" alt="image" src="https://github.com/user-attachments/assets/4e68928a-abc0-4eff-b99d-cac891b33291" />



* **Screenshot 2: Wazuh Threat Hunting Dashboard Event Ingestion**
<img width="2871" height="2686" alt="image" src="https://github.com/user-attachments/assets/e9920814-548a-4929-bd8a-4dc5c4e15616" />



* **Screenshot 3: Wazuh Document Details Forensic Event Data**
<img width="2882" height="2809" alt="image" src="https://github.com/user-attachments/assets/c727742e-2293-4032-80ba-414c73590831" />



 WHAT THIS DEMONSTRATES 
This project demonstrates hands-on experience with:
- Creating custom Wazuh XML detection rules
- Windows Security Event ID 4625 analysis
- Failed authentication monitoring
- Wazuh rule testing and validation
- SIEM event investigation
- Correlating authentication activity between an endpoint and SIEM
- Investigating potential brute-force authentication behavior

 TOOLS 
- Wazuh
- Wazuh Dashboard
- Windows Event Viewer
- Windows Server
- Kali Linux
- FreeRDP
- VirtualBox


 PROJECT 3: SYSMON PROCESS MONITORING & WAZUH DETECTION 

 OVERVIEW 

Configured Microsoft Sysmon on a Windows Server to capture Process Creation events (Event ID 1) and integrated Sysmon telemetry with Wazuh for centralized monitoring and detection.

A controlled `cmd.exe` process was executed to validate the configuration. Sysmon captured the activity, and Wazuh successfully ingested and detected the event.

 OBJECTIVE 

- Configure Sysmon to monitor process creation.
- Integrate Sysmon logs with Wazuh.
- Generate a controlled Process Creation event.
- Validate Event ID 1 in Windows Event Viewer.
- Investigate the event in Wazuh Threat Hunting.

 LAB ENVIRONMENT 

| Component | Configuration |
|---|---|
| Wazuh Manager/Dashboard | Ubuntu |
| Endpoint | Windows Server |
| Sysmon | v15.21 |
| Wazuh Agent | v4.12.0 |
| Windows Server IP | 192.168.56.106 |
| Wazuh Manager IP | 192.168.56.105 |
| Primary Event | Sysmon Event ID 1 |

 Controlled Test

Executed:

```powershell
cmd.exe /c "echo Sysmon Event ID 1 test"
```

 EVIDENCE — Sysmon Event ID 1

<img width="3024" height="2193" alt="image" src="https://github.com/user-attachments/assets/5f79a5a2-bf23-4e78-8362-ac9b8f2d40a6" />



 EVIDENCE — Wazuh Alert Ingestion

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/ef7aa9c4-b4c0-4126-9254-e7f80f9e9d83" />


 EVIDENCE — Wazuh Document Details

<img width="3024" height="1883" alt="image" src="https://github.com/user-attachments/assets/0ebd89cb-c42d-45f0-89ae-1ae45e3598ae" />



PROJECT 4: WAZUH FILE INTEGRITY MONITORING & CANARY FILE DETECTION 

OVERVIEW 

This project demonstrates the use of **Wazuh File Integrity Monitoring (FIM)** to detect unauthorized changes to a monitored Windows file. A canary file was created on the Windows Server and configured for real-time monitoring through the Wazuh agent.

A controlled modification was then made to the canary file to simulate suspicious file activity. Wazuh detected the change and generated an integrity alert containing file and cryptographic hash information.

OBJECTIVE 

* Configure Wazuh FIM to monitor a specific Windows directory.
* Enable real-time monitoring of the monitored directory.
* Create a canary file to serve as a monitored security indicator.
* Perform a controlled modification to the file.
* Verify that Wazuh detects the modification.
* Capture the resulting Wazuh alert as security evidence.

LAB ENVIRONMENT 

| Component           | Configuration                              |
| ------------------- | ------------------------------------------ |
| Wazuh Manager       | Ubuntu                                     |
| Wazuh Dashboard     | Ubuntu                                     |
| Windows Server      | Wazuh Agent                                |
| Windows Server IP   | `192.168.56.106`                           |
| Wazuh Manager IP    | `192.168.56.105`                           |
| Monitored Directory | `C:\Canary`                                |
| Monitored File      | `C:\Canary\Important-Financial-Record.txt` |

1. Create the Canary Directory and File

A dedicated `C:\Canary` directory was created on the Windows Server. A file named `Important-Financial-Record.txt` was then created inside the directory.

PowerShell Commands

powershell
New-Item -Path "C:\Canary" -ItemType Directory -Force

New-Item -Path "C:\Canary\Important-Financial-Record.txt" -ItemType File -Force


EVIDENCE 1 — Canary File Creation

<img width="2558" height="2482" alt="image" src="https://github.com/user-attachments/assets/918580f0-c31d-43f9-8163-2fefb117a13a" />



This confirms that the monitored directory and canary file were successfully created on the Windows Server.

2. Configure Wazuh Real-Time File Integrity Monitoring

The Windows Wazuh agent configuration was updated to monitor the Canary directory using real-time File Integrity Monitoring.

The following configuration was added inside the `<syscheck>` section of `ossec.conf`:

xml
<directories realtime="yes">C:\Canary</directories>


The Wazuh agent was then restarted so the updated configuration could take effect.

EVIDENCE 2 — Real-Time FIM Configuration

<img width="3024" height="2422" alt="image" src="https://github.com/user-attachments/assets/b8ed87da-b87e-49c4-b0d8-23877a686afc" />


This configuration instructs the Wazuh agent to monitor the Canary directory for file integrity changes in real time.

3. Perform a Controlled File Modification

After real-time monitoring was enabled, the canary file was intentionally modified using PowerShell.

PowerShell Command

powershell
Add-Content -Path 'C:\Canary\Important-Financial-Record.txt' -Value 'Canary realtime test'


The command modified the contents of the monitored file, creating a controlled file-integrity event.

EVIDENCE 3 — Canary File Modification

 <img width="3024" height="2237" alt="image" src="https://github.com/user-attachments/assets/aab33ba0-be1d-44a6-8766-9455fe2c82af" />


4. Verify Wazuh Detection

After the file was modified, the Wazuh Dashboard was used to verify that the event was detected.

The Threat Hunting view identified **Rule ID 550**, with the description:

`Integrity checksum changed.`

The detailed event identified the monitored file and showed that multiple file attributes changed.

EVIDENCE 4 — Wazuh FIM Alert

<img width="3024" height="3027" alt="image" src="https://github.com/user-attachments/assets/f01102e4-6aea-4e19-acff-27142abf4ba7" />


The alert identified:

* **File:** `C:\Canary\Important-Financial-Record.txt`
* **Mode:** `realtime`
* **Decoder:** `syscheck_integrity_changed`
* **Rule:** `550`
* **Agent:** `WIN-E1SKA42GQ7G`
* **Agent IP:** `192.168.56.106`
* **Manager:** `brai-VirtualBox`
* **Changed attributes:** size, modification time, MD5, SHA1, and SHA256
* **File size:** changed from 43 bytes to 65 bytes

 RESULTS

The test successfully demonstrated end-to-end file integrity monitoring using Wazuh.

The workflow was:

```text
Create Canary File
        ↓
Configure Wazuh Real-Time FIM
        ↓
Modify Canary File
        ↓
Wazuh Detects Change
        ↓
Rule 550: Integrity Checksum Changed
```

The Wazuh alert confirmed that the agent detected the file modification in real time and recorded changes to the file's metadata and cryptographic hashes.

SECURITY SIGNIFICANCE

File Integrity Monitoring can help identify suspicious or unauthorized changes to files on monitored systems. Canary files can also serve as early indicators of potentially malicious activity, such as unauthorized modification or ransomware-related file activity.

In this lab, the modification was intentionally performed and controlled. The purpose was to demonstrate how Wazuh can detect and document file integrity changes.

KEY SKILLS

* Wazuh File Integrity Monitoring (FIM)
* Wazuh Agent configuration
* Real-time file monitoring
* Windows PowerShell
* Windows Server administration
* Security event investigation
* Hash-based file integrity detection
* Wazuh Threat Hunting
* Security evidence collection
* SIEM-based event analysis

 

---


















