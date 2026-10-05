# Project 4: Wazuh File Integrity Monitoring & Canary File Detection

## Overview

Built a Windows endpoint monitoring lab using **Wazuh File Integrity Monitoring (FIM)** to detect changes to files and investigate potentially suspicious activity.

A controlled canary-file test was used to generate file modification telemetry and validate that Wazuh could detect and report changes to a monitored file.

---

## Lab Environment

| Component               | Configuration    |
| ----------------------- | ---------------- |
| Monitored Endpoint      | Windows 10/11    |
| Wazuh Manager/Dashboard | Ubuntu           |
| Wazuh Manager IP        | `192.168.56.105` |
| Monitoring Tool         | Wazuh FIM        |
| Virtualization          | VirtualBox       |

---

## 1. File Integrity Monitoring Configuration

Wazuh File Integrity Monitoring was configured on the Windows endpoint to monitor a designated directory for file changes.

FIM provides visibility into file activity such as:

* File creation
* File modification
* File deletion
* File attribute changes

This allows changes to monitored files to be identified and investigated.

### Evidence

**Wazuh File Integrity Monitoring Configuration**

The Wazuh configuration was reviewed to verify that the designated Windows directory was being monitored.

---

## 2. Canary File Test

A controlled canary file was created inside the monitored directory.

The file was then modified to generate file integrity telemetry.

This simulated a basic endpoint activity scenario where a monitored file is changed and the security monitoring system must identify the modification.

### Evidence

**Canary File Activity**

The controlled file modification generated Wazuh telemetry showing activity involving the monitored file.

---

## 3. Wazuh FIM Detection

Wazuh collected the file integrity event from the Windows endpoint and made the activity available for investigation through the Wazuh dashboard.

The event was reviewed to identify the affected file and determine the type of file activity detected.

### Evidence

**Wazuh File Integrity Alert**

The Wazuh event provided details about the monitored file and the detected change.

---

## 4. Investigation

The file integrity event was analyzed to determine what occurred and which file was affected.

File monitoring can provide valuable security context when investigating unauthorized modifications, malware activity, ransomware behavior, or unexpected changes to important files.

In a production environment, unexpected changes to sensitive files could be investigated further by reviewing the responsible user, process activity, timestamps, and related endpoint telemetry.

---

## Detection Workflow

The investigation demonstrated the following workflow:

**Canary File Creation/Modification → Wazuh FIM Monitoring → File Integrity Event → Wazuh Alert → Security Investigation**

---

## Investigation Result

The controlled canary-file test successfully generated file integrity telemetry from the Windows endpoint.

Wazuh detected the monitored file activity and provided event information that could be used to investigate the change.

The project demonstrated how file integrity monitoring can provide an additional layer of endpoint visibility and support investigations involving suspicious or unauthorized file modifications.

---

## Skills Demonstrated

* Wazuh File Integrity Monitoring
* Windows Endpoint Monitoring
* Canary File Testing
* File Change Detection
* Security Event Investigation
* Endpoint Telemetry
* Wazuh Dashboard Analysis
* Malware Detection Concepts
* Ransomware Detection Concepts
* Security Monitoring
* Incident Investigation
* Security Documentation

