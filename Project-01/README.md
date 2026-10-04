
PROJECT 1: ACTIVE DIRECTORY, PASSWORD-SPRAY & WAZUH INVESTIGATION

Overview

Simulated a controlled RDP password-guessing attack from Kali Linux against a Windows workstation using Hydra. Investigated the resulting authentication failures in Windows Event Viewer and validated the activity through Wazuh.

Lab Environment

Attacker: Kali Linux — 192.168.56.109
Target: Windows Workstation — 192.168.56.106
Protocol: RDP / TCP 3389
Account: TargetUser
Tool: Hydra v9.6
SIEM: Wazuh
Virtualization: VirtualBox
Network Configuration Troubleshooting

During initial testing, Windows Event ID 4625 incorrectly reported the authentication source as 127.0.0.1. I traced the issue to the VirtualBox Ethernet Adapter 1 configuration and corrected the network setup.

After the correction, Windows security telemetry accurately identified the remote Kali attacker as 192.168.56.109.

Initial Source: 127.0.0.1
Corrected Source: 192.168.56.109
Evidence — Initial Source

image
Attack Simulation

Executed Hydra v9.6 from Kali Linux against the Windows workstation over RDP using the TargetUser account.

Result: 0 valid password found

Evidence — Hydra Attack

image
Windows Event Investigation

Investigated the resulting authentication failures using Windows Event Viewer. Event ID 4625 confirmed failed authentication attempts and provided the source system and NTLM authentication details.

Event ID: 4625
Logon Type: 3
Logon Process: NtLmSsp
Authentication: NTLM
Workstation: kali-attacker
Source IP: 192.168.56.109
Result: Audit Failure
Evidence — Event ID 4625

image
Evidence — Event 4625 Raw XML

image
Wazuh Validation

The Windows security telemetry was collected by the Wazuh agent and processed by the Wazuh manager, providing centralized SIEM visibility into the authentication activity.

Evidence — Wazuh Alert

image
Skills Demonstrated

Authentication Attack Simulation
Windows Event Analysis
NTLM Analysis
Network Troubleshooting
Source IP Attribution
Wazuh SIEM
Attack-to-Telemetry Correlation
