# 🔍 SIEM Log Monitoring & Threat Detection

<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:111111,50:ff6900,100:e20074&height=180&section=header&text=SIEM%20Log%20Monitoring%20and%20Threat%20Detection%20using%20Splunk&fontSize=22&fontColor=ffffff" width="100%" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Category-SIEM%20Security%20%7C%20Log%20Analysis-ff6900?style=for-the-badge&logo=splunk&logoColor=white">
  <img src="https://img.shields.io/badge/Status-Complete-success?style=for-the-badge">
  <img src="https://img.shields.io/badge/Focus-Splunk%20%7C%20Threat%20Detection-111111?style=for-the-badge&logo=target&logoColor=ff6900">
</p>

<p align="center">
  <b>Part of SOC Lab Portfolio → SIEM Analysis</b>
</p>

---
📌 Project Overview

This project presents a fully simulated end-to-end Security Operations Center (SOC) workflow within a controlled lab environment. Leveraging Splunk Enterprise as the centralized SIEM, Sysmon for enhanced endpoint telemetry, and Kali Linux as the adversary platform, I executed multiple real-world attack scenarios to analyze system and network behavior.

The project emphasizes detection engineering through custom SPL (Search Processing Language) queries, enabling the identification of malicious activities. All observed Indicators of Compromise (IOCs) were mapped to the MITRE ATT&CK framework to align detection logic with industry standards. This implementation reflects real-world SOC Tier 1 and Tier 2 operations, including alert triage, log correlation, and threat investigation.

---

🏗️ Lab Architecture

Telemetry Flow:
Kali Linux (Attacker) → Windows 10 (Victim) → Sysmon & Security Logs → Splunk Universal Forwarder → Splunk Enterprise (SIEM)

---

⚙️ Component Breakdown

⚔️ Attacker Node (Kali Linux VM):
Role: Simulates external adversary behavior by launching attacks such as brute-force authentication attempts.

🛡️ Target Endpoint (Windows 10 VM):
Role: Victim system generating telemetry during attack execution.
Monitoring Tools:

* Sysmon (process, network, and event monitoring)
* Windows Security Event Logs

📡 Log Forwarding Layer:
Splunk Universal Forwarder configured to securely transmit logs over port 9997.

📊 SIEM Server (Ubuntu VM – Splunk Enterprise):
Role: Centralized log analysis and detection platform.
Capabilities:

* SPL-based threat detection
* Alert generation
* Dashboard visualization

---

🛠️ Tools & Technologies

* Splunk Enterprise — SIEM for log ingestion, correlation, and alerting
* Sysmon (v15.15) — Advanced endpoint telemetry collection
* Kali Linux — Attack simulation platform
* Hydra — Credential brute-force tool
* Windows 10 — Target system
* SwiftOnSecurity Sysmon Config — Optimized logging configuration

---

🚨 Incident Case #01: SMB Brute Force Attack

📝 Executive Summary

During analysis, a high volume of failed authentication attempts was detected originating from IP 192.168.56.102. Investigation revealed a brute-force attack targeting SMB services using Hydra against the account “testuser”. The activity was identified through correlated log analysis in Splunk and mapped to MITRE ATT&CK technique T1110 (Brute Force).

---

🧪 Threat Emulation Phase

Attack Type: SMB Brute Force
Tool Used: Hydra
Target System: Windows 10 (192.168.3.1)
Target Account: testuser

Adversary Command:
hydra -l testuser -P /usr/share/wordlists/rockyou.txt 192.168.3.1 smb -t 4 -V

---

🔍 Detection Logic (Splunk SPL)

index=main source="WinEventLog:Security" EventCode=4625
| stats count by Account_Name, Source_Network_Address
| where count > 10
| eval Threat="Brute Force Detected"
| eval MITRE="T1110 - Brute Force"
| table Account_Name, Source_Network_Address, count, Threat, MITRE

---

📌 Threat Intelligence Mapping

Framework: MITRE ATT&CK
Technique ID: T1110
Technique Name: Brute Force
Tactic: Credential Access

---

🚀 Additional Attack Simulations

Attack 2 — Encoded PowerShell Execution (T1059.001)
Simulated obfuscated PowerShell commands using encoded payloads to replicate stealthy attacker behavior.

Attack 3 — System & Network Enumeration (T1087, T1018)
Executed native Windows commands such as `whoami`, `net user`, and `ipconfig` to simulate internal reconnaissance activity.

---

🎯 Key Outcomes

* Developed hands-on experience with SIEM-based detection
* Implemented real-world attack simulation scenarios
* Built custom SPL queries for threat detection
* Mapped attack behavior to MITRE ATT&CK framework
* Gained practical SOC analyst workflow exposure

---
