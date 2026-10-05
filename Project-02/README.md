# Project 2: Wazuh Brute-Force Detection Using a Custom XML Rule

## Overview

Developed and tested a custom Wazuh detection rule to identify repeated Windows failed-logon events and flag potential brute-force activity.

The project demonstrates how Windows authentication telemetry can be collected by Wazuh, correlated through an existing Wazuh rule, and used to validate custom detection logic in an isolated lab environment.

---

## Lab Environment

| Component                  | Configuration    |
| -------------------------- | ---------------- |
| Authentication Test Source | Kali Linux       |
| Kali IP                    | `192.168.56.109` |
| Monitored Endpoint         | Windows Server   |
| Windows Server IP          | `192.168.56.106` |
| Wazuh Manager/Dashboard    | Ubuntu           |
| Wazuh Manager IP           | `192.168.56.105` |
| Virtualization             | VirtualBox       |

---

## 1. Authentication Test

A controlled authentication test was performed from Kali Linux against the Windows Server using RDP.

The test generated Windows failed-logon events that could be monitored through Wazuh.

### Evidence

**Hydra Authentication Test**

The test targeted the Windows Server RDP service on TCP port 3389.

---

## 2. Windows Failed-Logon Detection

The Windows authentication failures were recorded as **Event ID 4625**.

Wazuh received the Windows security telemetry through the Wazuh agent and associated the failed-logon events with **Rule 60122 — Logon Failure - Unknown user or bad password**.

The Threat Hunting view showed multiple Event ID 4625 events occurring within the test period.

### Evidence

**Wazuh Threat Hunting — Event ID 4625**

The investigation returned multiple Event ID 4625 events and showed Rule ID `60122` associated with the failed authentication activity.

---

## 3. Custom Wazuh Detection Rule

A custom Wazuh rule was developed to identify repeated failed authentication events within a defined time period.

The rule was configured in:

`/var/ossec/etc/rules/local_rules.xml`

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

### Detection Logic

The rule:

* Monitors events associated with Wazuh Rule `60122`
* Requires **5 matching events**
* Uses a **60-second timeframe**
* Raises the alert to **Level 10**
* Maps the activity to MITRE ATT&CK technique **T1110 — Brute Force**

This provides a detection layer above the individual Windows failed-logon events.

---

## 4. Wazuh Forensic Investigation

The Wazuh document view was used to examine the underlying authentication event and verify the Windows security telemetry collected by the Wazuh agent.

The event included:

* Agent: `WIN-E1SKA42GQ7G`
* Agent IP: `192.168.56.106`
* Authentication Package: `NTLM`
* Logon Process: `NtLmSsp`
* Logon Type: `3`
* Status: `0xc000006d`
* Substatus: `0xc000006a`

These fields provided additional context for validating the failed authentication activity.

### Evidence

**Wazuh Forensic Event Details**

---

## Detection Result

The controlled authentication test successfully generated multiple Windows Event ID 4625 failures.

Wazuh collected the events and associated them with Rule `60122`. The custom rule was designed to identify repeated occurrences within a defined timeframe and classify the activity as a potential brute-force attempt.

The investigation demonstrated the workflow from:

**Authentication Test → Windows Event ID 4625 → Wazuh Rule 60122 → Custom Detection Logic → SIEM Investigation**

---

## Skills Demonstrated

* Custom Wazuh Rule Development
* Windows Event ID 4625 Analysis
* Brute-Force Detection
* SIEM Investigation
* Authentication Monitoring
* Event Correlation
* Wazuh Rule Validation
* MITRE ATT&CK Mapping
* Windows Security Telemetry
* Security Investigation Documentation

