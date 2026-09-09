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

Simulated a Help Desk ticket involving a Windows workstation that had Internet connectivity but was unable to resolve websites by hostname. Used a structured troubleshooting process to identify the DNS problem, test a known-good DNS server, correct the DNS configuration, and verify the fix.

### Lab Environment

* **Client:** Windows Workstation
* **Virtualization:** VirtualBox
* **Host-only IP:** `192.168.56.110`
* **NAT IP:** `10.0.2.15`
* **Default Gateway:** `10.0.2.2`
* **DNS Server Tested:** Google Public DNS — `8.8.8.8`

### Help Desk Troubleshooting

#### 1. Verify Network Connectivity

Started by testing the default gateway to confirm the workstation had local network connectivity.

**Evidence — Gateway Connectivity**

The gateway responded successfully.

Next, tested Internet connectivity using Google's public IP address.

**Evidence — Internet Connectivity**

<img width="2822" height="2724" alt="image" src="https://github.com/user-attachments/assets/9266086e-7c64-4c82-85d9-ab192877e702" />


The test was successful, confirming that Internet connectivity was available.

#### 2. Review Network Configuration

Used `ipconfig` to review the workstation's network adapters, IP addresses, and default gateway.

**Evidence — Network Configuration**

<img width="3024" height="3024" alt="image" src="https://github.com/user-attachments/assets/4d2ae0f1-2563-4451-95eb-1112933f2222" />


The workstation had separate Host-only and NAT network connections.

#### 3. Test DNS Resolution

Tested hostname resolution with:

```cmd
nslookup google.com
```

The request timed out and selected `192.168.56.106` as the DNS server.

**Evidence — DNS Resolution Failure**

<img width="2806" height="2506" alt="image" src="https://github.com/user-attachments/assets/2a29f93c-c45d-4a05-9696-5a8284af931f" />


This showed that the workstation had network connectivity, but normal DNS resolution was failing.

#### 4. Test a Known-Good DNS Server

Queried Google Public DNS directly:

```cmd
nslookup google.com 8.8.8.8
```

The query successfully returned Google's addresses.

**Evidence — Direct DNS Test**

<img width="2820" height="1735" alt="image" src="https://github.com/user-attachments/assets/75142cd8-f362-4458-960c-26dcdd091e6d" />


This confirmed that external DNS resolution was working and helped isolate the problem to the workstation's DNS configuration.

#### 5. Correct the DNS Configuration

Configured the workstation to use Google Public DNS (`8.8.8.8`) for DNS resolution.

<img width="3024" height="1626" alt="image" src="https://github.com/user-attachments/assets/eba734d8-cdbe-402e-92d5-8fa7c88682d5" />


The goal was to correct the DNS configuration without changing the workstation's IP addressing or network connectivity.

#### 6. Verify the Resolution

Ran the original DNS test again:

```cmd
nslookup google.com
```

The workstation successfully resolved `google.com` using:

`8.8.8.8`

**Evidence — Successful DNS Resolution**

<img width="2820" height="1735" alt="image" src="https://github.com/user-attachments/assets/b5c79de3-33a4-4ad3-81a0-f11ecfd13250" />


Finally, tested the hostname directly:

```cmd
ping google.com
```

The hostname resolved successfully with `0%` packet loss.

**Evidence — Final Connectivity Verification**

<img width="3024" height="2624" alt="image" src="https://github.com/user-attachments/assets/f104799a-237f-4d98-baeb-c36c48252c05" />


### Troubleshooting Outcome

The issue was isolated to DNS resolution rather than general network connectivity. After correcting the DNS configuration, hostname resolution was restored and verified using `nslookup` and `ping`.

### Help Desk Skills Demonstrated

* Structured troubleshooting
* Windows network configuration
* DNS troubleshooting
* `ipconfig`
* `nslookup`
* `ping`
* Problem isolation
* Configuration correction
* Troubleshooting verification
* Technical documentation


---


















