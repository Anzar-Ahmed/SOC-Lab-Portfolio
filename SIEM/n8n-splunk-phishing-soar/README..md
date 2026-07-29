# SOAR-Lite: Automated Phishing Email Triage Pipeline

An end-to-end Security Orchestration, Automation, and Response (SOAR) workflow that automatically ingests reported phishing emails, extracts indicators of compromise (IOCs), enriches them against multiple threat intelligence sources, renders a verdict, and logs the result to Splunk for SOC visibility.

Built with **n8n** as a lightweight, self-hosted SOAR alternative — demonstrating that core SOC automation concepts (ingestion, enrichment, correlation, decision logic, case logging) can be implemented without an enterprise SOAR license.

---

## Why this project

Manual phishing triage is one of the highest-volume, most repetitive tasks a Tier 1 SOC analyst handles daily. This project automates the first-pass triage — IOC extraction and multi-source enrichment — so an analyst's time is spent on judgment calls, not copy-pasting URLs into VirusTotal one at a time.

This is a companion project to my [Splunk SOC Brute-Force Detection Lab](#), extending my portfolio from **detection** (Splunk SPL, correlation searches) into **automation and response** (SOAR workflow design, API-driven enrichment, orchestrated decision-making).

---

## Architecture

```
               [ Reported Email (IMAP) ]
                        │
                        ▼
         [ Extract IOCs (Code node) ]
   sender, reply-to, URLs, attachment SHA-256,
            SPF / DKIM / DMARC results
                        │
        ┌───────────────┼───────────────┬───────────────┐
        ▼               ▼               ▼               ▼
   AbuseIPDB        VirusTotal       URLScan.io       File Hash
  (IP reputation)   (URL scan)     (URL behavior)    (VT lookup)
        │               │               │               │
        └───────────────┴───────────────┴───────────────┘
                        ▼
             [ Merge + Correlate Results ]
                        ▼
             [ Verdict Logic (Code node) ]
                  isMalicious: true/false
                        │
          ┌─────────────┴─────────────┐
          ▼                           ▼
  [ Log to Splunk HEC ]      [ Log to Splunk HEC ]
   ✅ Implemented              ✅ Implemented
          │
          └──► [ Auto-containment / IP blocklist push ]
                🔜 Planned — see Roadmap
```

**Everything above the dashed line is built and verified against real test emails (see Screenshots below). Containment/response actions are designed but not yet implemented — tracked in the Roadmap section.**

---

## Pipeline Stages

### 1. Email Ingestion
- IMAP trigger node polls a dedicated "report-phishing" mailbox for new (unseen) messages
- Supports attachment download for hash-based analysis

### 2. IOC Extraction
A JavaScript code node parses the raw email into structured fields:
- Sender address, display name, and `Reply-To` (common spoofing signal when it differs from the sender)
- All embedded URLs (deduplicated)
- SHA-256 hash of any attachments
- SPF, DKIM, and DMARC authentication results
- Sender IP (extracted from `Received`/`Received-SPF` headers)

### 3. Multi-Source Enrichment (parallel branches)
| Source | What it checks | Signal used |
|---|---|---|
| **AbuseIPDB** | Sender IP reputation | Abuse confidence score |
| **VirusTotal (URL)** | Submitted URL scan | Malicious engine detection count |
| **URLScan.io** | Live URL behavior/screenshot scan | Overall verdict (malicious/benign) |
| **VirusTotal (File Hash)** | Attachment hash lookup | Known-malware match |

Each API call runs independently and results are combined with a Merge node once all branches complete.

### 4. Verdict Logic
A correlation function applies a simple, tunable threshold model:
```
IF VirusTotal malicious_count > 0
   OR URLScan overall verdict == malicious
   OR AbuseIPDB confidence score > 20
THEN verdict = "Phishing"
ELSE verdict = "Benign"
```

### 5. Case Logging (Splunk)
Final verdict and all threat indicators are pushed to Splunk via the **HTTP Event Collector (HEC)**, making every triaged email searchable, dashboard-able, and available for trend analysis (e.g. top targeted users, most abused sender IPs, detection rate over time).

---

## Tech Stack

- **n8n** — workflow orchestration / SOAR engine
- **IMAP** — email ingestion
- **AbuseIPDB API** — IP reputation
- **VirusTotal API v3** — URL and file hash reputation
- **URLScan.io API** — URL sandbox/behavioral scanning
- **Splunk HEC** — centralized event logging and dashboarding

---

## Sample Output (Splunk event)

Each triaged email lands in Splunk as a single JSON event via HEC, with the full extracted IOC set nested under `emailData` alongside the correlated verdict fields:

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
      { "filename": "invoice.pdf", "mimeType": "application/pdf", "sha256": "e039e3ac0c70a40509c7a2ef739479d182773ffdccdf31ed01258ad26bb65dd2" }
    ],
    "spf_result": "fail",
    "dkim_result": "fail",
    "dmarc_result": "fail",
    "sender_ip": "203.0.113.45",
    "reply_to": "not_set",
    "message_id": "..."
  },
  "isMalicious": true,
  "vtMalicious": 9,
  "urlscanMalicious": false
}
```

Indexed with `host=127.0.0.1:8088`, `source=http:n8n_soar_token`, `sourcetype=httpevent` — every field above (including nested `emailData.*` fields) is automatically extracted by Splunk and searchable, e.g.:
```
sourcetype=httpevent isMalicious=true
| stats count by emailData.sender_ip
```

---

## Screenshots

### n8n — Full Workflow Canvas
![n8n SOAR-Lite workflow](screenshots/n8n-workflow-canvas.png)
End-to-end pipeline: IMAP trigger → IOC extraction → four parallel enrichment branches (AbuseIPDB, VirusTotal URL, URLScan.io, VirusTotal file hash via Crypto + Filter) → merge → verdict logic → Splunk HEC.

### Splunk — Correlated Verdict Event
![Splunk verdict event](screenshots/splunk-verdict-event.png)
A fully enriched and correlated phishing verdict landing in Splunk — `isMalicious`, `vtMalicious`, `abuseScore`, and `urlscanMalicious` all computed by the n8n pipeline and logged in a single event.

### Splunk — Attachment Hash Extraction
![Attachment SHA-256 field](screenshots/splunk-attachment-hash.png)
Attachment SHA-256 automatically extracted and indexed as a searchable field — enables hash-based threat hunting across historical events.

### Splunk — Field-Level Drill-Down
![isMalicious field breakdown](screenshots/splunk-ismalicious-field.png)
Splunk's automatic field extraction surfaces every nested `emailData.*` IOC as its own searchable field, alongside the top-level verdict fields.

> Screenshots are saved under `/screenshots` in this repo.

---

## Key Design Decisions

- **Parallel enrichment over sequential** — reduces total triage time; each API is independent and doesn't block the others
- **Header-format defensive parsing** — n8n's IMAP node can surface headers under different keys depending on version (`headers` object vs `headerLines` array); the extraction code checks both so the pipeline doesn't silently break on header parsing
- **SIEM-agnostic logging via HEC** — using Splunk's HTTP Event Collector keeps the SOAR layer decoupled from the SIEM; the same event payload could be redirected to any HEC-compatible backend

---

## Setup

1. Import `SOAR-Lite-Phase1-3.json` into n8n
2. Configure credentials:
   - IMAP account (mailbox to monitor)
   - AbuseIPDB, VirusTotal, and URLScan.io API keys (stored in n8n Credentials — **never hardcoded in the workflow file**)
   - Splunk HEC token and endpoint
3. Enable the workflow and send a test phishing sample (e.g. from a public phishing corpus) to the monitored mailbox
4. Verify the event lands in Splunk:
   ```
   sourcetype=httpevent source="http:n8n_soar_token"
   ```
   > Tip: assigning a dedicated index (e.g. `phishing_soar`) and custom sourcetype (e.g. `phishing:verdict`) at the HEC token level makes long-term searching and dashboarding cleaner than the shared `httpevent` default — worth doing once the pipeline is stable.

> **Note:** The exported workflow JSON in this repo has all credentials stripped and replaced with placeholders. You must supply your own API keys via n8n's Credentials manager before running it.

---

## MITRE ATT&CK Mapping

| Technique | ID | Relevance |
|---|---|---|
| Phishing | T1566 | Primary threat this pipeline detects |
| Phishing via Spearphishing Link | T1566.002 | URL-based payloads flagged by VT/URLScan |
| Spoofing | T1656 | Detected via Reply-To mismatch and SPF/DKIM/DMARC failures |

---

## Roadmap

- [ ] **Auto-containment on confirmed phishing** — push sender IP to a Splunk lookup table / blocklist and send a SOC alert when `isMalicious == true` (currently the pipeline logs the verdict but takes no containment action)
- [ ] Multi-URL enrichment (currently checks the first URL found per email; batch processing would cover all)
- [ ] Auto-quarantine action for confirmed phishing (Gmail API delete/label)
- [ ] Slack/Teams alert on high-confidence verdicts
- [ ] Retry/poll logic for VT and URLScan when analysis is still queued past the wait window
- [ ] Analyst feedback loop to reduce false positives over time
- [ ] Expand test coverage beyond the two verified scenarios (benign vs. malicious) to include mixed-signal and edge cases

---

## Author

**Anzar Ahmed**
Cybersecurity student, Mehran University of Engineering and Technology (MUET)
Aspiring SOC Analyst | Building hands-on SOC automation & detection projects

[GitHub](#) • [LinkedIn](#)
