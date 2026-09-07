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

**Evidence — Hydra authentication test**

<img width="3024" height="3152" alt="image" src="https://github.com/user-attachments/assets/6d47bc34-dc6d-40d5-9c7d-d305df3dc5b3" />


### Detection Testing

Generated controlled failed authentication attempts from Kali Linux against the Windows Server `TargetUser` account.

Five failed-logon events were observed within approximately three seconds, with the events associated with Wazuh Rule `60122`.

**Evidence — Event Ingestion**

<img width="2871" height="2686" alt="image" src="https://github.com/user-attachments/assets/399edb35-dd3a-47e6-a699-ecf737eff6af" />



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


**Evidence — Canary File Creation**

<img width="2558" height="2482" alt="image" src="https://github.com/user-attachments/assets/e3e08a38-acf6-4e2f-ade3-bafc0e19ea5c" />


### Real-Time FIM Configuration

Configured the Wazuh agent to monitor the Canary directory in real time.


**Evidence — Wazuh FIM Configuration**

<img width="3024" height="2422" alt="image" src="https://github.com/user-attachments/assets/8f1b3afc-3bf8-43b3-a58d-a328987bd200" />


### Controlled File Modification

Modified the monitored file to simulate a change to a sensitive file and trigger FIM detection.


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

 
# CYBERSECURITY LAB PORTFOLIO

---

# PROJECT 5: WINDOWS DNS TROUBLESHOOTING & NETWORK CONFIGURATION

### Overview

Simulated a Windows DNS troubleshooting scenario in a controlled VirtualBox lab. Investigated network connectivity, multiple network interfaces, routing, and DNS resolution before identifying and correcting an interface priority issue.

### Lab Environment

* **Client:** Windows Workstation — `192.168.56.110`
* **NAT Address:** `10.0.2.15`
* **Default Gateway:** `10.0.2.2`
* **DNS Tested:** Google Public DNS — `8.8.8.8`
* **Virtualization:** VirtualBox

### Network Connectivity Testing

Verified local gateway and Internet connectivity before troubleshooting DNS.

* **Gateway:** `10.0.2.2`
* **Internet:** `8.8.8.8`
* **Result:** Successful connectivity

**Evidence — Gateway Connectivity**

**Evidence — Internet Connectivity**

<img width="2822" height="2724" alt="image" src="https://github.com/user-attachments/assets/d8674f1c-bfca-4d9c-85f2-1252d883435e" />


### Network Configuration

The Windows workstation used two network interfaces: Ethernet 2 for the Host-only lab network and Ethernet for the NAT network.

**Evidence — Network Adapters**

<img width="3024" height="3024" alt="image" src="https://github.com/user-attachments/assets/ab7b7026-e215-4e6c-b837-eb70e5d6b405" />


The IPv4 routing table confirmed separate routes for the NAT and Host-only networks.

**Evidence — IPv4 Routing Configuration**

<img width="3024" height="2599" alt="image" src="https://github.com/user-attachments/assets/073b1cda-0efd-4831-b693-7954b6ed5d78" />


### DNS Resolution Investigation

During initial testing, `nslookup google.com` timed out while selecting `192.168.56.106`.

* **Initial Address:** `192.168.56.106`
* **Result:** DNS request timed out

**Evidence — DNS Resolution Failure**

<img width="2806" height="2506" alt="image" src="https://github.com/user-attachments/assets/a4632ec8-cd01-4c00-8fa3-d485402710c1" />


A direct query to Google Public DNS successfully resolved `google.com`, confirming that external DNS resolution was available.

**Evidence — Direct DNS Test**

<img width="2820" height="3043" alt="image" src="https://github.com/user-attachments/assets/98781ef5-35e7-4d96-a641-7e25b0f83026" />


### Network Interface Configuration

Reviewed IPv4 interface metrics to determine network interface priority.

* **Ethernet 2:** `10`
* **Ethernet:** `25`
* **Loopback:** `75`

**Evidence — IPv4 Interface Metrics**




### Configuration Correction

Changed the Ethernet interface metric from `25` to `5` to give it higher priority.

```powershell
Set-NetIPInterface -InterfaceAlias "Ethernet" -AddressFamily IPv4 -InterfaceMetric 5
```

**Evidence — Interface Metric Correction**

<img width="2730" height="3073" alt="image" src="https://github.com/user-attachments/assets/1a8e1286-7d42-4e43-acd0-ea410113ca58" />


### DNS Verification

After correcting the interface priority, normal `nslookup google.com` successfully used Google Public DNS.

* **DNS Server:** `8.8.8.8`
* **Hostname:** `google.com`
* **Result:** Successful DNS resolution

**Evidence — Successful DNS Resolution**

<img width="3024" height="4032" alt="image" src="https://github.com/user-attachments/assets/cc28ff01-5b09-4596-bcf8-7a48410865d7" />


A final `ping google.com` confirmed successful hostname resolution and connectivity with `0%` packet loss.

**Evidence — Final Connectivity Verification**

[INSERT SCREENSHOT: ping google.com showing 0% packet loss]

### Conclusion

Identified and corrected a network interface priority issue affecting DNS resolution. Final testing confirmed successful DNS resolution and network connectivity.

---


















