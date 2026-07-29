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
📌 **Project Overview**
This project demonstrates an end-to-end Security Operations Center (SOC) workflow simulated in a dedicated lab environment. Using **Splunk Enterprise** as the central SIEM, **Sysmon** for fine-grained endpoint visibility, and **Kali Linux** as the attack platform, I executed realistic cyber threat scenarios to analyze host and network telemetry.

Through custom **SPL (Search Processing Language)** queries, I engineered targeted detection rules to capture malicious activity and mapped all identified indicators of compromise (IOCs) directly to the **MITRE ATT&CK Framework**. This project reflects real-world Tier 1 and Tier 2 SOC Analyst operations, focusing on alert triage, forensic log analysis, and threat detection engineering.

---

## 🏗️ Lab Architecture

![Architecture](./SIEM%20Log%20Monitoring%20%26%20Threat%20Detection/01-Lab-Setup/Architecture.png)

> **Telemetry Flow:**  
> **Kali Linux VM** *(Attacker)* ──`Hydra Attack`──> **Windows 10 VM** *(Victim)* ──`Sysmon & Security Logs`──> **Splunk Universal Forwarder** ──`Port 9997`──> **Ubuntu VM** *(Splunk SIEM)*

---

### ⚙️ Component Breakdown:
* **⚔️ Attacker Node (Kali Linux VM):**
  * **Role:** External adversary machine executing brute-force attacks via **Hydra**.
* **🛡️ Target Endpoint (Windows 10 VM):**
  * **Role:** Victim endpoint generating security events.
  * **Telemetry Agents:** **Sysmon** (deep process & network tracking) + **Windows Security Event Logs**.
  * **Log Forwarder:** **Splunk Universal Forwarder (UF)** routing raw telemetry over **Port 9997**.
* **📊 SIEM Server (Ubuntu VM - Splunk Enterprise):**
  * **Role:** Centralized SIEM engine indexing received endpoint telemetry.
  * **Capabilities:** Executes **SPL Queries**, triggers automated **Alerts**, and visualizes threat indicators on custom **Dashboards**.

---

## 🛠️ Tools & Technologies

| Tool                               | Purpose                                         |
|------------------------------------|-------------------------------------------------|
| **Splunk Enterprise**              | SIEM — Log ingestion, search, and alerting     |
| **Sysmon v15.15**                  | Advanced Windows process and network monitoring |
| **Kali Linux**                     | Attack simulation platform                     |
| **Hydra**                          | Password brute-force tool                      |
| **Windows 10**                     | Target / victim machine                        |
| **SwiftOnSecurity Sysmon Config**  | Optimized Sysmon configuration for detection   |

---
## ⚔️ Attacks Simulated

### Attack 1 — SMB Brute Force (T1110)
Credential brute-force attack against SMB service using Hydra to simulate unauthorized login attempts.

### Attack 2 — Malicious PowerShell Execution with Encoded Command (T1059.001)
Simulated suspicious PowerShell activity using encoded commands to replicate attacker obfuscation and stealth execution techniques.

### Attack 3 — System & Network Enumeration (T1087, T1018)
Performed Windows user and network discovery activities using native commands to simulate internal reconnaissance behavior.

---

### 🚨 Incident Case #01: SMB Authentication Flood (Brute Force)

#### 📝 Executive Summary
During blue-team telemetry analysis, an anomalous volume of authentication failures was flagged originating from host `192.168.56.102`. Further forensic examination confirmed an automated SMB brute-force attack leveraging **Hydra** directed at account `testuser` on host `192.168.3.1`. The event stream was correlated via Splunk and mapped to **MITRE ATT&CK T1110**.

---

#### 🧪 Threat Emulation Phase
* **Offensive Vector:** SMB Service Credential Spraying / Dictionary Attack
* **Execution Utility:** `Hydra`
* **Target Endpoint:** Windows Host (`192.168.3.1`)
* **Target Account:** `testuser`

### ⚔️ Adversary Execution Command

```bash
hydra -l testuser -P /usr/share/wordlists/rockyou.txt 192.168.3.1 smb -t 4 -V
```

---

### 🛠️ **Splunk Detection Engineering (SPL)**

```spl
index=main source="WinEventLog:Security" EventCode=4625
| stats count by Account_Name, Source_Network_Address
| where count > 10
| eval Threat="Brute Force Detected!"
| eval MITRE="T1110 - Brute Force"
| table Account_Name, Source_Network_Address, count, Threat, MITRE
```

#### 📌 Threat Intelligence Mapping

| Framework | Technique ID | Technique Name | Tactic |
| :--- | :--- | :--- | :--- |
| **MITRE ATT&CK** | `T1110` | **Brute Force** | Credential Access |


#### 🔑 Key Findings & Triage Analysis

* **High Volume Telemetry:** Ingested **3107 failed logon events** (`EventCode 4625`) within a short timeframe.
* **Adversary Identification:** Source IP `192.168.56.102` identified as the origin of the attack.
* **Target Focus:** Local account `testuser` was targeted using automated wordlists (`rockyou.txt`).
* **Authentication Failure Reason:** Explicit log status confirmed *"Unknown user name or bad password"*.

  ---

## 📋 Attack 2: Suspicious PowerShell Execution

### What Happened

After gaining initial access, the attacker executed suspicious PowerShell commands using bypass flags commonly used by malware and threat actors.

### 💻 Attack Commands (Simulated Post-Exploitation)

**Test Case 1: Execution Policy Bypass & Inline Execution**

```powershell
powershell -nop -exec bypass -c "Write-Host 'Simulated Attack'"
```

**Purpose:**
- Simulates execution of PowerShell with policy bypass.
- Used to test detection of suspicious PowerShell execution behavior.

**Test Case 2: Stealth In-Memory Execution (Fileless Malware Simulation)**

```powershell
powershell -nop -exec bypass -w hidden -c "IEX 'Write-Host Malware Simulation'"
```

**Purpose:**
- Simulates hidden PowerShell execution.
- Tests detection of stealthy in-memory execution techniques.

### 🚩 Suspicious PowerShell Flags Breakdown

| Suspicious Flag | Full Parameter Name | Purpose / Security Concern |
|---|---|---|
| `-nop` | `-NoProfile` | Prevents PowerShell profile loading. Attackers use this to avoid user-specific configurations and security controls. |
| `-exec bypass` | `-ExecutionPolicy Bypass` | Allows execution of scripts without enforcing PowerShell execution policies. Commonly abused by malware. |
| `-w hidden` | `-WindowStyle Hidden` | Runs PowerShell without displaying a visible window to the user. |
| `-c` | `-Command` | Executes commands directly through PowerShell. |
| `IEX` | `Invoke-Expression` | Executes dynamically generated PowerShell code in memory and is commonly abused for fileless attacks. |

---

## 📋 Attack 3: Reconnaissance & Discovery

### What Happened

After gaining access, the attacker ran system discovery commands to enumerate users, groups, and network connections — standard post-exploitation recon behavior.

### 💻 Attack Commands (Simulating Attacker Recon)

```cmd
net user                        # List all user accounts
net localgroup administrators   # List admin group members
whoami /all                     # Current user privileges
netstat -ano                    # Active network connections
```

### What Was Generated

- 26 recon-related process creation events flagged out of 287 total `EventCode 4688` events captured.
- Multiple suspicious processes logged (`net.exe`, `whoami.exe`, `netstat.exe`).
- User and group enumeration activity recorded on target endpoint `192.168.1.7`.

### 🛠️ Detection Query (SPL)

```spl
index=main source="WinEventLog:Security" EventCode=4688
| eval Threat="Suspicious Reconnaissance!"
| eval MITRE="T1087 - Account Discovery"
| table _time, User, New_Process_Name, Threat, MITRE
| head 10
```

### 📌 MITRE ATT&CK Mapping

| Technique | ID | Tactic |
|---|---|---|
| Account Discovery | T1087 | Discovery |
| System Network Connections Discovery | T1049 | Discovery |

---

## 🔗 Attack Chain Overview

```
Brute Force (T1110) ──▶ PowerShell Execution (T1059.001) ──▶ Account Discovery (T1087)
                                                        └──▶ Network Discovery (T1049)
```

---

## 🕵️ Detection Logic

| Rule Name | Event Source | Detection Logic | MITRE ID |
|---|---|---|---|
| Brute Force Detection | EventCode 4625 | Failed logins > 10 from same source IP | T1110 |
| PowerShell Execution | Sysmon | `powershell.exe` invoked with bypass flags | T1059.001 |
| Recon Activity | EventCode 4688 | Execution of `net.exe`, `whoami.exe`, `netstat.exe` | T1087, T1049 |

---

## 📊 Environment Stats

| Metric | Value |
|---|---|
| Security Events Ingested (EventCode 4625) | 29,223+ |
| Process Creation Events (EventCode 4688) | 287 |
| Sysmon Events Ingested (PowerShell-related) | 50+ |
| Brute Force Attempts Flagged | 9,434 |
| Custom Detection Rules Built | 3 |
| MITRE Techniques Mapped | 4 |

---

## 🧠 Skills Applied

- SIEM setup and tuning (Splunk)
- Multi-source Windows log ingestion
- SPL query development
- Custom correlation/detection rule creation
- Alert configuration and false-positive tuning
- Controlled attack simulation and investigation
- MITRE ATT&CK technique mapping
- Incident documentation and triage

---

## 🚀 Lab Setup Guide

### Requirements

- Windows 10/11 host machine
- VirtualBox with Kali Linux VM
- Splunk Enterprise (Free tier — 500MB/day)
- Sysmon with SwiftOnSecurity configuration

### 1. Install Sysmon

```powershell
# Pull SwiftOnSecurity config
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml" -OutFile "sysmonconfig-export.xml"

# Install Sysmon with the config applied
Sysmon64.exe -accepteula -i sysmonconfig-export.xml
```

### 2. Configure Splunk Inputs

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
index = main
disabled = false
renderXml = false

[WinEventLog://Security]
index = main
disabled = false
```

### 3. Simulate the Attack

```bash
# Run from Kali Linux
hydra -l testuser -P /usr/share/wordlists/rockyou.txt 192.168.1.7 smb -t 4 -V
```

---

## 📚 References

- [Splunk Documentation](https://docs.splunk.com/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [SwiftOnSecurity Sysmon Config](https://github.com/SwiftOnSecurity/sysmon-config)
- [Sysinternals Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)

---

**Author:** Anzar Ahmed
