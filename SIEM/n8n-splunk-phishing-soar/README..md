#  SOAR-Lite: Automated Phishing Email Triage Pipeline


<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:1e5f8a,50:0e8388,100:2e8b7b&height=220&section=header&text=Automated%20Phishing%20Email%20Triage%20Pipeline&fontSize=32&fontColor=ffffff"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/CATEGORY-SOAR%20AUTOMATION%20%7C%20PHISHING%20EMAIL%20TRIAGE-008080?style=for-the-badge&labelColor=4b5563"/>
  <img src="https://img.shields.io/badge/STATUS-COMPLETE-008a4b?style=for-the-badge&labelColor=4b5563"/>
  <img src="https://img.shields.io/badge/FOCUS-IMAP%20EXTRACTION%20%7C%20IOC%20ENRICHMENT-0f172a?style=for-the-badge&labelColor=4b5563"/>
</p>

<p align="center">
  Part of SOC Automation Lab Portfolio → Email Security Analysis
</p>


# 📌 Project Overview

This project demonstrates an end-to-end **Security Orchestration, Automation, and Response (SOAR)** workflow designed to automate phishing email triage within a SOC environment.

The pipeline automatically ingests reported phishing emails, extracts key indicators of compromise (IOCs), enriches them using multiple threat intelligence sources, applies correlation logic to generate a verdict, and logs the results into Splunk for centralized visibility.

Built using **n8n as a lightweight, self-hosted SOAR platform**, this project showcases how core SOC automation capabilities — including ingestion, enrichment, correlation, decision-making, and case logging — can be implemented without requiring enterprise-grade SOAR solutions.

This project reflects real-world **Tier 1 SOC automation workflows**, focusing on reducing manual effort, improving triage speed, and enabling scalable threat analysis.

---

# 🎯 Why This Project

Manual phishing triage is one of the most repetitive and time-consuming tasks for a Tier 1 SOC analyst. Analysts often spend hours extracting URLs, checking reputations, and correlating signals across multiple tools.

This project automates the **first-pass triage process**, allowing analysts to focus on decision-making rather than repetitive enrichment tasks.

It also serves as a companion to my **Splunk SOC Brute-Force Detection Lab**, extending my portfolio from:

* 🔍 Detection Engineering (SPL, correlation searches)
  ➡️ to
* ⚙️ Automation & Response (SOAR workflows, API enrichment, decision logic)

---

# 🏗️ Architecture

## 🔄 Phishing SOAR Pipeline Flow

Email Inbox (IMAP)
──▶ IOC Extraction (n8n Code Node)
──▶ Parallel Enrichment APIs
──▶ Correlation & Verdict Logic
──▶ Splunk HEC (Logging & Visualization)

---

# ⚙️ Workflow Breakdown

## 📥 1. Email Ingestion

* IMAP trigger monitors a dedicated **"report-phishing" mailbox**
* Automatically processes new (unseen) emails
* Supports attachment download for further analysis

---

## 🔍 2. IOC Extraction

A custom JavaScript node parses raw email data into structured intelligence:

* Sender address & display name
* Reply-To (spoofing indicator)
* Extracted URLs (deduplicated)
* Attachment metadata + SHA-256 hash
* SPF, DKIM, DMARC authentication results
* Sender IP (from email headers)

---

## 🌐 3. Multi-Source Enrichment (Parallel Processing)

| Source     | Function              | Signal Used            |
| ---------- | --------------------- | ---------------------- |
| AbuseIPDB  | IP reputation         | Abuse score            |
| VirusTotal | URL scanning          | Malicious engine count |
| URLScan.io | URL behavior analysis | Verdict                |
| VirusTotal | File hash reputation  | Malware match          |

All enrichment processes run in parallel and are merged after completion to optimize performance.

---

## 🧠 4. Verdict Logic

A correlation function applies threshold-based detection logic:

```
IF VirusTotal malicious_count > 0
   OR URLScan verdict == malicious
   OR AbuseIPDB score > 20
THEN verdict = "Phishing"
ELSE verdict = "Benign"
```

---

## 📊 5. Case Logging (Splunk SIEM)

* Final verdict and IOC data are sent to **Splunk via HTTP Event Collector (HEC)**
* Enables:

  * Threat hunting
  * Dashboard visualization
  * Historical analysis

---

# 🛠️ Tech Stack

| Tool           | Purpose                     |
| -------------- | --------------------------- |
| n8n            | SOAR workflow orchestration |
| IMAP           | Email ingestion             |
| AbuseIPDB API  | IP reputation analysis      |
| VirusTotal API | URL & file hash scanning    |
| URLScan.io API | Behavioral URL analysis     |
| Splunk HEC     | Centralized logging & SIEM  |

---

# 🚨 Sample Splunk Event

Each processed email is logged as a structured JSON event:

```json
{
  "abuseScore": 0,
  "isMalicious": true,
  "vtMalicious": 9,
  "urlscanMalicious": false,
  "emailData": {
    "email_from_address": "support@paypa1-secure.com",
    "urls_found": ["http://paypa1-secure.com/verify"],
    "spf_result": "fail",
    "dkim_result": "fail",
    "dmarc_result": "fail",
    "sender_ip": "203.0.113.45"
  }
}
```

### 🔎 Example Query

```
sourcetype=httpevent isMalicious=true
| stats count by emailData.sender_ip
```

---

# 🖥️ Screenshots

* n8n Workflow Canvas (End-to-End Pipeline)
* Splunk Correlated Verdict Event
* Attachment Hash Extraction
* Field-Level IOC Drill-Down

📁 Stored under `/screenshots`

---

# 🧠 Key Design Decisions

* **Parallel Enrichment**
  Improves performance by reducing total triage time

* **Resilient Header Parsing**
  Handles variations in IMAP header formats

* **SIEM-Agnostic Logging**
  Uses HEC for flexible integration with other SIEMs

---

# 📊 Detection & Coverage

| Metric                    | Value             |
| ------------------------- | ----------------- |
| Enrichment Sources Used   | 4                 |
| Workflow Stages           | 5                 |
| IOC Types Extracted       | 6+                |
| Verdict Accuracy (Tested) | High              |
| Automation Coverage       | First-pass triage |

---

# 🧬 MITRE ATT&CK Mapping

| Technique          | ID        | Relevance                       |
| ------------------ | --------- | ------------------------------- |
| Phishing           | T1566     | Primary attack vector           |
| Spearphishing Link | T1566.002 | URL-based payload detection     |
| Spoofing           | T1656     | Header mismatch & auth failures |

---

# 🚀 Setup Guide

### 1. Import Workflow

* Import `SOAR-Lite-Phase1-3.json` into n8n

### 2. Configure Credentials

* IMAP mailbox
* AbuseIPDB API key
* VirusTotal API key
* URLScan API key
* Splunk HEC token

### 3. Run Test

* Send a phishing sample email
* Verify logs in Splunk:

```
sourcetype=httpevent source="http:n8n_soar_token"
```

---

# ⚠️ Notes

* All credentials are removed from the exported workflow
* Must be configured manually via n8n Credentials
* Recommended: create a dedicated Splunk index (e.g. `phishing_soar`)

---

# 🛣️ Roadmap

* Auto-containment for malicious emails
* Multi-URL enrichment support
* Email auto-quarantine integration
* Slack/Teams alerting
* API retry logic for delayed scans
* Analyst feedback loop for tuning
* Expanded test scenarios

---

# 🧠 Skills Demonstrated

* SOAR workflow design (n8n)
* API integration & automation
* IOC extraction & parsing
* Threat intelligence correlation
* SIEM integration (Splunk HEC)
* Detection logic & decision engineering
* Phishing analysis & triage

---

# 👨‍💻 Author

**Anzar Ahmed**
Cybersecurity Student — MUET Jamshoro
Aspiring SOC Analyst

🔗 GitHub • LinkedIn
