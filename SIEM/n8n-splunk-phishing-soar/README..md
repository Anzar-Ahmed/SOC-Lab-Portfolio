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


# 📌 SOAR-Lite: Phishing Email Triage Automation

An end-to-end **Security Orchestration, Automation, and Response (SOAR)** workflow that automatically ingests reported phishing emails, extracts indicators of compromise (IOCs), enriches them using multiple threat intelligence sources, renders a verdict, and logs results to Splunk for SOC visibility.

Built using **n8n** as a lightweight, self-hosted SOAR alternative — demonstrating that core SOC automation concepts (ingestion, enrichment, correlation, decision logic, and case logging) can be implemented without an enterprise SOAR license.

---

## 🎯 Why This Project

Manual phishing triage is one of the most repetitive and high-volume tasks handled by Tier 1 SOC analysts. This project automates the **first-pass triage process** — specifically IOC extraction and multi-source enrichment — allowing analysts to focus on decision-making instead of manual lookups.

This project complements my **Splunk SOC Brute-Force Detection Lab**, extending my portfolio from:

* 🔍 **Detection Engineering** (Splunk SPL, correlation searches)
  to
* ⚙️ **Automation & Response** (SOAR workflows, API integrations, decision logic)

---

## 🏗️ Architecture

![Phishing SOAR Architecture](assets/architecture.png)

> Everything above the dashed line is fully implemented and tested with real phishing samples.
> Containment actions are planned and listed in the roadmap.

---

## ⚙️ Pipeline Stages

### 1. 📥 Email Ingestion

* IMAP trigger monitors a dedicated **"report-phishing" mailbox**
* Fetches new/unseen emails
* Supports attachment downloads for hash analysis

---

### 2. 🔍 IOC Extraction

A JavaScript node parses raw email data into structured fields:

* Sender email and display name
* `Reply-To` mismatch detection (spoofing indicator)
* Extracted URLs (deduplicated)
* Attachment SHA-256 hashes
* SPF, DKIM, DMARC results
* Sender IP from email headers

---

### 3. 🌐 Multi-Source Enrichment (Parallel Execution)

| Source                 | Purpose               | Signal                     |
| ---------------------- | --------------------- | -------------------------- |
| AbuseIPDB              | IP reputation         | Abuse confidence score     |
| VirusTotal (URL)       | URL scan results      | Malicious engine count     |
| URLScan.io             | URL behavior analysis | Verdict (malicious/benign) |
| VirusTotal (File Hash) | Attachment lookup     | Known malware match        |

* All APIs run **in parallel**
* Results are merged after completion

---

### 4. 🧠 Verdict Logic

A simple, tunable correlation model:

```
IF VirusTotal malicious_count > 0
   OR URLScan verdict == malicious
   OR AbuseIPDB score > 20
THEN verdict = "Phishing"
ELSE verdict = "Benign"
```

---

### 5. 📊 Case Logging (Splunk)

* Final verdict and all IOCs are sent to **Splunk via HTTP Event Collector (HEC)**
* Enables:

  * Searchable events
  * Dashboards
  * Trend analysis

Examples:

* Top targeted users
* Most abused IPs
* Detection rates over time

---

## 🧰 Tech Stack

* **n8n** — SOAR orchestration
* **IMAP** — Email ingestion
* **AbuseIPDB API** — IP intelligence
* **VirusTotal API v3** — URL & file reputation
* **URLScan.io API** — Behavioral scanning
* **Splunk HEC** — Logging & visualization

---

## 📄 Sample Splunk Event

```json
{
  "abuseScore": 0,
  "emailData": {
    "email_from_address": "support@paypa1-secure.com",
    "email_subject": "Your account has been suspended",
    "urls_found": ["http://paypa1-secure.com/verify"],
    "url_count": 1,
    "attachment_count": 1,
    "attachments": [
      {
        "filename": "invoice.pdf",
        "mimeType": "application/pdf",
        "sha256": "e039e3ac0c70a40509c7a2ef739479d182773ffdccdf31ed01258ad26bb65dd2"
      }
    ],
    "spf_result": "fail",
    "dkim_result": "fail",
    "dmarc_result": "fail",
    "sender_ip": "203.0.113.45",
    "reply_to": "not_set"
  },
  "isMalicious": true,
  "vtMalicious": 9,
  "urlscanMalicious": false
}
```

### Example Splunk Query

```
sourcetype=httpevent isMalicious=true
| stats count by emailData.sender_ip
```

---

## 📸 Screenshots

### n8n Workflow

![Workflow](screenshots/n8n-workflow-canvas.png)

### Splunk Verdict Event

![Splunk Event](screenshots/splunk-verdict-event.png)

### Attachment Hash Field

![Hash Field](screenshots/splunk-attachment-hash.png)

### Field-Level Breakdown

![Field Breakdown](screenshots/splunk-ismalicious-field.png)

---

## 🧠 Key Design Decisions

* **Parallel enrichment** → faster triage
* **Defensive header parsing** → avoids pipeline breakage
* **SIEM-agnostic logging** → flexible integration

---

## 🚀 Setup Guide

1. Import workflow into n8n

2. Configure credentials:

   * IMAP mailbox
   * API keys (AbuseIPDB, VirusTotal, URLScan)
   * Splunk HEC token

3. Enable workflow

4. Send test phishing email

5. Verify in Splunk:

```
sourcetype=httpevent source="http:n8n_soar_token"
```

💡 Tip: Use a custom index like `phishing_soar` for cleaner dashboards.

---

## 🧬 MITRE ATT&CK Mapping

| Technique          | ID        | Description                   |
| ------------------ | --------- | ----------------------------- |
| Phishing           | T1566     | Primary attack vector         |
| Spearphishing Link | T1566.002 | Malicious URLs                |
| Spoofing           | T1656     | Header manipulation detection |

---

## 🛣️ Roadmap

* [ ] Auto-containment (block IPs / alerts)
* [ ] Multi-URL support
* [ ] Email quarantine (Gmail API)
* [ ] Slack/Teams alerts
* [ ] API retry logic
* [ ] Analyst feedback loop
* [ ] Expanded test scenarios

---

## 👨‍💻 Author

**Anzar Ahmed**
Cybersecurity Student — MUET Jamshoro
Aspiring SOC Analyst

🔗 GitHub: #
🔗 LinkedIn: #
