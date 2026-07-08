# 🎣 Phishing Analysis Lab

<div align="center">

![Category](https://img.shields.io/badge/Category-Blue%20Team%20%2F%20SOC-blue?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=for-the-badge)
![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red?style=for-the-badge)

</div>

---

## 📖 Overview
This lab covers end-to-end phishing investigations using real-world SOC triage techniques. The primary objective is to break down suspicious emails layer-by-layer to uncover malicious infrastructure, evaluate social engineering tactics, and safely extract actionable intelligence without compromising the host environment.

The investigation transitions from basic structural inspection (headers) down to advanced behavior analysis (dynamic malware execution).

---

## 🎯 Core Objectives
* **Header Triage:** Parse raw mail source to validate SPF, DKIM, and DMARC alignment and map delivery routing.
* **Content Deconstruction:** Identify psychological triggers, urgency-based lures, and obfuscated call-to-actions.
* **Infrastructure Tracking:** Investigate suspicious domains and network reputation using open-source intelligence (OSINT).
* **Defensive Threat Hunting:** Safely detonate, analyze, and extract Indicators of Compromise (IOCs) from malicious artifacts.

---

## 📂 Lab Structure & Roadmap

| Module | Section | Description | Status |
| :--- | :--- | :--- | :--- |
| **01** | [📁 Email Header Analysis](./01-Email-Header-Analysis/) | Manual header parsing, routing tracing, and authentication verification. | 🟢 Completed (Sample 1) |
| **02** | [📁 Email Content Analysis](./02-Email-Content-Analysis/) | Body text analysis, body language triggers, and hidden HTML artifact tracking. | 🟡 Pending |
| **03** | [📁 URL Analysis](./03-URL-Analysis/) | Reputation checking, redirection mapping, and scanning malicious links safely. | 🟡 Pending |
| **04** | [📁 Email Attachment Analysis](./04-Email-Attachment-Analysis/) | Static inspection of suspicious extensions and extraction of raw file metadata. | 🟡 Pending |
| **05** | [📁 Maldoc Analysis](./05-Maldoc-Analysis/) | Macros inspection, document obfuscation triage, and dynamic analysis in sandboxes. | 🟡 Pending |

---

## 🧰 Threat Intelligence & Investigative Stack

The following toolset is utilized throughout the progression of this lab:

| Category | Tools Deployed | Purpose |
| :--- | :--- | :--- |
| **Mail Triage** | Sublime Text / VS Code | Manual raw source parsing |
| **OSINT & IP Rep** | VirusTotal, MXToolbox | Domain authentication & reputation checking |
| **URL Diagnostics** | URLScan.io | Safe link rendering and redirection tracking |
| **Dynamic Sandbox** | Any.Run, Hybrid Analysis | Malware detonation & behavioral sequence logging |

---

## 🛡️ Applied Threat Framework
Investigations within this lab are aligned with the **MITRE ATT&CK** matrix, focusing specifically on the **Initial Access (TA0001)** tactic via **Phishing (T1566)** sub-techniques.

---

<div align="center">

### 📂 Navigation

[⬅️ Back to Main SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)

</div>
