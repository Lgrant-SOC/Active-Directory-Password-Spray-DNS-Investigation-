
# Project 3: Sysmon Process Monitoring & Wazuh Detection

## Overview

Built a Windows endpoint monitoring lab using **Sysmon** and **Wazuh** to capture and investigate process execution activity.

The project demonstrates how endpoint telemetry can be collected with Sysmon, forwarded to Wazuh, and analyzed to identify potentially suspicious process activity.

---

## Lab Environment

| Component               | Configuration    |
| ----------------------- | ---------------- |
| Monitored Endpoint      | Windows 10/11    |
| Wazuh Manager/Dashboard | Ubuntu           |
| Wazuh Manager IP        | `192.168.56.105` |
| Windows Endpoint        | Windows 10/11 VM |
| Monitoring Tools        | Sysmon + Wazuh   |
| Virtualization          | VirtualBox       |

---

## 1. Sysmon Installation & Configuration

Sysmon was installed on the Windows endpoint to provide detailed process and system activity telemetry.

The Sysmon configuration enabled monitoring of process creation events and related endpoint activity.

Sysmon provides additional visibility beyond standard Windows security logs by recording details about processes executed on the endpoint.

### Evidence

**Sysmon Process Monitoring**

The Windows endpoint generated Sysmon process creation telemetry during normal and controlled testing activity.

---

## 2. Process Creation Monitoring

Sysmon **Event ID 1 — Process Create** was used to investigate process execution.

The event provides information such as:

* Process name
* Process ID
* Parent process
* Command line
* User account
* Image path
* Process creation timestamp

This information helps establish what process executed, when it executed, and which parent process initiated it.

### Evidence

**Sysmon Event ID 1**

The captured event was reviewed to examine process execution details and establish the relationship between the parent and child processes.

---

## 3. Wazuh Endpoint Detection

The Windows endpoint forwarded Sysmon telemetry to the Wazuh manager.

Wazuh was used to centralize the endpoint events and provide a searchable view of the collected Sysmon activity.

The investigation focused on identifying process creation events and reviewing the available endpoint details.

### Evidence

**Wazuh Sysmon Process Event**

The Wazuh event view was used to confirm that Sysmon process telemetry was successfully collected from the Windows endpoint.

---

## 4. Process Investigation

The collected telemetry was analyzed by reviewing the process execution chain.

Parent-child process relationships were examined to determine which processes initiated other processes on the Windows endpoint.

This provides useful context when investigating unexpected or potentially suspicious process execution.

### Evidence

**Process Execution Analysis**

The captured Sysmon telemetry was reviewed to identify the process, parent process, user context, and execution details associated with the activity.

---

## Detection Workflow

The investigation demonstrated the following workflow:

**Windows Process Execution → Sysmon Event ID 1 → Wazuh Collection → Event Investigation → Process Analysis**

---

## Investigation Result

Sysmon successfully provided detailed process execution telemetry from the Windows endpoint, while Wazuh centralized the resulting events for investigation.

The project demonstrated how endpoint telemetry can improve visibility into process activity and provide additional context for security investigations.

---

## Skills Demonstrated

* Sysmon Deployment
* Sysmon Event ID 1 Analysis
* Windows Endpoint Monitoring
* Wazuh SIEM
* Process Execution Analysis
* Parent-Child Process Analysis
* Endpoint Telemetry
* Security Event Investigation
* Windows Security Monitoring
* Alert Investigation
* Security Documentation


---

##
