Active Directory Password Spray & Wazuh Investigation
📋 Overview
Simulated a controlled RDP password-guessing attack from a Kali Linux attacker (192.168.56.109) against a Windows workstation (192.168.56.106) using Hydra. During validation, the Windows authentication telemetry initially displayed the source as 127.0.0.1 due to the Ethernet Adapter 1 configuration. After correcting the configuration, Event ID 4625 successfully recorded the correct remote authentication source, identifying the Kali attacker as 192.168.56.109.
🎯 Objectives
Execute a controlled Hydra authentication attack against RDP.
Investigate Windows Event ID 4625 authentication failures.
Troubleshoot the initial network configuration issue.
Verify the correct attacker IP and workstation in Windows telemetry.
Analyze NTLM authentication data.
Validate the activity through Wazuh.
🔧 Network Configuration Troubleshooting
During the initial validation, Event ID 4625 recorded the authentication source as 127.0.0.1.
The issue was traced to the VirtualBox Ethernet Adapter 1 configuration. After correcting the adapter configuration, the Windows security telemetry accurately reflected the remote authentication source.
Initial Source:
127.0.0.1
Corrected Source:
192.168.56.109
Evidence — Initial Event 4625
➡️ Insert screenshot here
⚔️ Hydra Attack
Hydra v9.6 was executed from Kali Linux (192.168.56.109) against the Windows workstation (192.168.56.106) over RDP/TCP 3389 using the TargetUser account.
Result: 0 valid password found
Evidence — Hydra Attack
➡️ Insert Hydra screenshot here
🔍 Windows Event Investigation
Windows Event Viewer was used to investigate the authentication failures generated during the attack.
Event ID: 4625 — An account failed to log on


















