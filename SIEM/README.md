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

## 🏗️ Lab Architecture

<p align="center">
  <img src="Architecture.png" alt="SOC Lab Architecture Diagram" width="80%" />
</p>

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

| Tool | Purpose |
| :--- | :--- |
| **Splunk Enterprise** | SIEM — log ingestion, search, alerting |
| **Sysmon v15.15** | Deep Windows process/network monitoring |
| **Kali Linux** | Attacker machine for attack simulation |
| **Hydra** | Brute force attack tool |
| **Windows 10** | Victim/target machine |
| **SwiftOnSecurity Sysmon Config** | Optimized Sysmon detection ruleset |

---
