# CYBERSECURITY LAB PORTFOLIO

---

# PROJECT 1: ACTIVE DIRECTORY, PASSWORD-SPRAY & WAZUH INVESTIGATION

### Overview

Simulated a controlled RDP password-guessing attack from Kali Linux against a Windows workstation using Hydra. Investigated the resulting authentication failures in Windows Event Viewer and validated the activity through Wazuh.

### Lab Environment

* **Attacker:** Kali Linux — `192.168.56.109`
* **Target:** Windows Workstation — `192.168.56.106`
* **Protocol:** RDP / TCP 3389
* **Account:** `TargetUser`
* **Tool:** Hydra v9.6
* **SIEM:** Wazuh
* **Virtualization:** VirtualBox

### Network Configuration Troubleshooting

During initial testing, Windows Event ID 4625 incorrectly reported the authentication source as `127.0.0.1`. I traced the issue to the VirtualBox Ethernet Adapter 1 configuration and corrected the network setup.

After the correction, Windows security telemetry accurately identified the remote Kali attacker as `192.168.56.109`.

* **Initial Source:** `127.0.0.1`
* **Corrected Source:** `192.168.56.109`

**Evidence — Initial Source**

<img width="2506" height="1433" alt="image" src="https://github.com/user-attachments/assets/b181d72b-3631-49d3-bca1-73281ca6ae34" />


### Attack Simulation

Executed Hydra v9.6 from Kali Linux against the Windows workstation over RDP using the `TargetUser` account.

**Result:** `0 valid password found`

**Evidence — Hydra Attack**

<img width="1724" height="1394" alt="image" src="https://github.com/user-attachments/assets/2c0f3dcd-86bd-4277-8182-72c46368cfff" />


### Windows Event Investigation

Investigated the resulting authentication failures using Windows Event Viewer. Event ID 4625 confirmed failed authentication attempts and provided the source system and NTLM authentication details.

* **Event ID:** 4625
* **Logon Type:** 3
* **Logon Process:** NtLmSsp
* **Authentication:** NTLM
* **Workstation:** `kali-attacker`
* **Source IP:** `192.168.56.109`
* **Result:** Audit Failure

**Evidence — Event ID 4625**

<img width="1168" height="1178" alt="image" src="https://github.com/user-attachments/assets/d083a5d5-9ed7-4925-99df-5e6dabf2fe97" />


**Evidence — Event 4625 Raw XML**

<img width="1169" height="1113" alt="image" src="https://github.com/user-attachments/assets/b46f58c7-9104-4ed1-85ed-203704875c8e" />


### Wazuh Validation

The Windows security telemetry was collected by the Wazuh agent and processed by the Wazuh manager, providing centralized SIEM visibility into the authentication activity.

**Evidence — Wazuh Alert**

<img width="1169" height="1186" alt="image" src="https://github.com/user-attachments/assets/15659133-c6a4-40bc-a4a0-704258051e96" />


### Skills Demonstrated

* Authentication Attack Simulation
* Windows Event Analysis
* NTLM Analysis
* Network Troubleshooting
* Source IP Attribution
* Wazuh SIEM
* Attack-to-Telemetry Correlation

---

# PROJECT 2: WAZUH BRUTE-FORCE DETECTION USING A CUSTOM XML RULE

### Overview

Developed and tested a custom Wazuh detection rule to identify repeated Windows failed-logon events and flag potential brute-force authentication activity.

### Lab Environment

* **Kali Linux:** Authentication test source — `192.168.56.109`
* **Windows Server:** Monitored endpoint — `192.168.56.106`
* **Ubuntu:** Wazuh Manager/Dashboard — `192.168.56.105`
* **VirtualBox:** Isolated lab environment

### Detection Logic

Created a custom Wazuh rule to correlate five Windows Security Event ID 4625 failures within 60 seconds.

The rule was configured in:

`rules/local_rules.xml`

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

**Evidence — Custom Wazuh XML Rule**

[INSERT SCREENSHOT HERE]

### Detection Testing

Generated controlled failed authentication attempts from Kali Linux against the Windows Server `TargetUser` account.

Five failed-logon events were observed within approximately three seconds, with the events associated with Wazuh Rule `60122`.

**Evidence — Authentication Test / Event Ingestion**

<img width="3024" height="3152" alt="image" src="https://github.com/user-attachments/assets/246cf788-10c9-4e12-8782-1429e542bb14" />


### Wazuh Investigation

Reviewed the Wazuh Threat Hunting data and document details to confirm that the Windows authentication telemetry was successfully collected and processed.

**Evidence — Wazuh Forensic Event Details**

<img width="2882" height="2809" alt="image" src="https://github.com/user-attachments/assets/21bb9aa5-e4cc-47f8-a5ae-a9d6b86f5904" />


### Skills Demonstrated

* Custom Wazuh Rule Development
* Windows Event ID 4625 Analysis
* Brute-Force Detection
* SIEM Investigation
* Authentication Monitoring
* Event Correlation
* Wazuh Rule Validation

---

# PROJECT 3: SYSMON PROCESS MONITORING & WAZUH DETECTION

### Overview

Configured Microsoft Sysmon to capture Windows Process Creation events and integrated the telemetry with Wazuh for centralized security monitoring and investigation.

### Lab Environment

* **Wazuh Manager/Dashboard:** Ubuntu — `192.168.56.105`
* **Endpoint:** Windows Server — `192.168.56.106`
* **Sysmon:** v15.21
* **Wazuh Agent:** v4.12.0
* **Primary Event:** Sysmon Event ID 1

### Controlled Process Test

Executed a controlled `cmd.exe` process to validate Sysmon process monitoring.

```cmd
cmd.exe /c "echo Sysmon Event ID 1 test"
```

Sysmon captured the process creation activity as Event ID 1.

**Evidence — Sysmon Event ID 1**

<img width="3024" height="2193" alt="image" src="https://github.com/user-attachments/assets/fe31578c-576c-4bd5-8849-f0826d1ce0e6" />


### Wazuh Detection

The Sysmon telemetry was forwarded through the Wazuh agent and successfully ingested into the Wazuh Dashboard for centralized investigation.

**Evidence — Wazuh Alert 

<img width="3024" height="3373" alt="image" src="https://github.com/user-attachments/assets/721f3cd9-ce65-49f5-b384-4331f09ccee0" />



**Evidence — Wazuh Document Details**

<img width="3024" height="1883" alt="image" src="https://github.com/user-attachments/assets/3ea1cac1-7159-4688-84ea-cd957dd84b7a" />



### Skills Demonstrated

* Sysmon
* Process Monitoring
* Windows Event Analysis
* Wazuh SIEM
* Endpoint Telemetry
* Security Event Investigation
* SIEM Log Correlation

---

# PROJECT 4: WAZUH FILE INTEGRITY MONITORING & CANARY FILE DETECTION

### Overview

Configured Wazuh File Integrity Monitoring (FIM) to detect unauthorized or unexpected changes to a monitored Windows canary file in real time.

### Lab Environment

* **Wazuh Manager:** Ubuntu — `192.168.56.105`
* **Target Endpoint:** Windows Server — `192.168.56.106`
* **Wazuh Agent:** Windows
* **Monitored Directory:** `C:\Canary`
* **Canary File:** `Important-Financial-Record.txt`

### Canary File Creation

Created a dedicated Canary directory and financial-record test file using PowerShell.

```powershell
New-Item -Path "C:\Canary" -ItemType Directory -Force
New-Item -Path "C:\Canary\Important-Financial-Record.txt" -ItemType File -Force
```

**Evidence — Canary File Creation**

<img width="2558" height="2482" alt="image" src="https://github.com/user-attachments/assets/e3e08a38-acf6-4e2f-ade3-bafc0e19ea5c" />


### Real-Time FIM Configuration

Configured the Wazuh agent to monitor the Canary directory in real time.

```xml
<directories realtime="yes">C:\Canary</directories>
```

**Evidence — Wazuh FIM Configuration**

<img width="3024" height="2422" alt="image" src="https://github.com/user-attachments/assets/8f1b3afc-3bf8-43b3-a58d-a328987bd200" />


### Controlled File Modification

Modified the monitored file to simulate a change to a sensitive file and trigger FIM detection.

```powershell
Add-Content -Path 'C:\Canary\Important-Financial-Record.txt' -Value 'Canary realtime test'
```

**Evidence — Controlled File Modification**

<img width="3024" height="2237" alt="image" src="https://github.com/user-attachments/assets/ae47b5fa-c881-4f74-9b34-88fecea13071" />


### Wazuh Detection

Wazuh detected the modification through **Rule 550 — Integrity checksum changed** and reported changes to the file's size, modification time, and cryptographic hashes.

* **File:** `C:\Canary\Important-Financial-Record.txt`
* **Mode:** `realtime`
* **Agent:** `WIN-E1SKA42GQ7G`
* **Rule ID:** `550`
* **Changed Attributes:** Size, mtime, MD5, SHA1, SHA256

**Evidence — Wazuh FIM Detection**

<img width="3024" height="3027" alt="image" src="https://github.com/user-attachments/assets/b1117fd0-f1e6-43f6-9966-1a25e6eb23e5" />


### Result

Successfully demonstrated real-time file integrity monitoring by creating a canary file, modifying it in a controlled test, and validating the resulting Wazuh detection.

### Skills Demonstrated

* Wazuh File Integrity Monitoring
* Windows Server
* PowerShell
* SIEM Monitoring
* File Integrity Analysis
* Security Event Investigation
* Real-Time Endpoint Monitoring

 

---


















