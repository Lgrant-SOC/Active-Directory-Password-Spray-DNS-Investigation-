Active Directory Password Spray & Wazuh Investigation
📋 Overview
Simulated a controlled RDP password-guessing attack using Hydra from Kali Linux (192.168.56.109) against a Windows workstation (192.168.56.106). Investigated the resulting Windows Event ID 4625 authentication failures and validated the activity through Wazuh.
🎯 Objectives
Execute a controlled Hydra authentication attack.
Investigate Windows Event ID 4625 authentication failures.
Identify the originating attacker IP and workstation.
Analyze NTLM authentication telemetry.
Validate the activity in Wazuh.
⚔️ Hydra Attack
Hydra v9.6 was executed from the Kali Linux attacker machine (192.168.56.109) against the Windows workstation (192.168.56.106) over RDP/TCP 3389 using the TargetUser account.
The attack completed with 0 valid passwords found, demonstrating unsuccessful authentication attempts.
Evidence — Hydra 
<img width="1724" height="1394" alt="image" src="https://github.com/user-attachments/assets/7cbe2f1b-1144-4a3f-aa69-39ae44da81d2" />

🔍 Windows Event Investigation
Following the authentication attempts, Windows Event Viewer was filtered for Event ID 4625 — An account failed to log on.
Evidence — Event ID 4625
<img width="2874" height="2144" alt="image" src="https://github.com/user-attachments/assets/24f466b3-cbdd-4616-957a-2760f54bafa7" />

















