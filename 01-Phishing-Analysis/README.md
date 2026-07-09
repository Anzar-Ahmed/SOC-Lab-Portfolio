<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-2%2F5-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

## 🎯 Objective
To analyze raw email headers from suspicious/phishing samples using a text editor (Sublime Text) to identify sender spoofing, authentication mismatches (SPF/DKIM/DMARC), and Reply-To/Return-Path inconsistencies — without relying on automated header-analysis tools, in order to build strong manual analysis fundamentals.

## 🧰 Tools Used
* **`Sublime Text`** — For raw source ingestion, network hop isolation, and authentication string triage.

---

## 📊 Summary — Emails Analyzed

| # | Subject | Claimed Sender | SPF/DKIM/DMARC | Verdict | Key Red Flag / Alignment Signal |
|---|---|---|---|---|---|
| 1 | Your Bank Account has been blocked due to unusual activities | `alerts@chase.com` | SPF: Pass* / DKIM: Timeout / DMARC: Pass* | 🔴 Malicious | Friendly-From spoofing; routing addresses lead to a personal ProtonMail box. |
| 2 | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com` | SPF: Pass / DKIM: Pass / DMARC: Pass | 🟢 Legitimate | Full protocol alignment to `namecheap.com` domain via authorized SendGrid relays. |


> ⚠️ **Critical Alignment Note:** In Sample 1, `SPF/DMARC: Pass` is a deceptive indicator. The protocol verified the envelope sender domain (`protonmail.com`), but completely lacked **alignment** with the visible header domain (`chase.com`).

---

### 📥 Sample #1 — "Your Bank Account has been blocked due to unusual activities"

#### 📋 Case Profile
* **Scenario:** Urgency-driven banking lure impersonating Chase Bank.
* **Objective:** Drive the recipient to bypass logical security checks via panic and credential harvesting.
* **Triage Process:** Loaded raw `.eml` source into Sublime Text; extracted network hops (`Received`), security controls (`Authentication-Results`), and hidden feedback parameters (`Reply-To`, `Return-Path`).

#### 🔍 Main Forensic Findings
* **Friendly-From Spoofing:** The user sees `From: alerts@chase.com`, but the actual data return targets are asymmetric, routing directly to `Reply-To: kellyellin426@proton.me` and `Return-Path: kellyellin426@proton.me`.
* **ProtonMail Origin:** Traced the sequential tracking boundaries back to outbound node `mail-40140.protonmail.ch (185.70.40.140)`. Chase Bank infrastructure had zero interaction with this delivery.
* **Deceptive Protocol Passing:** `Authentication-Results` noted `spf=pass` and `dmarc=pass`. However, granular parsing localized this check to `smtp.mailfrom=protonmail.com`. The adversary authenticated their *own* ProtonMail asset, exploiting a lack of client-side domain alignment.
* **DKIM Timeout:** Cryptographic signature lookup resulted in a `timeout (key query timeout)`, verifying a lack of keys generated for the spoofed domain.

> **Analysis Verdict:** 🔴 **Confirmed Phishing** — Friendly-From identity spoofing designed to redirect credential recovery traffic to an attacker-controlled ProtonMail inbox.

---

#### 🛡️ Defensive Action Plan (SOC Playbook)
1. **Perimeter Drop:** Block IP `185.70.40.140` and malicious mail target `kellyellin426@proton.me` at the Secure Email Gateway (SEG).
2. **Tenant Purge:** Run a tenant-wide lookup query across the mail environment to catch and delete identical delivery metrics.
3. **Telemetry Sweep:** Audit network proxy/DNS infrastructure logs to confirm if any user clicked or loaded external links from the email.

---

### 📥 Sample #2 — "Rachel, cosmicfusiontech.com will expire in 7 days - renew now"

#### 📋 Case Profile
* **Scenario:** Urgent automated domain renewal notice sent to `rachel.jones@cosmicfusiontech.com`.
* **Objective:** Prompt legitimate account access via a prominent "Renew Now" call-to-action button.
* **Triage Process:** Parsed mail headers in Sublime Text to run comparative validation against Sample #1's deceptive alignment metrics.

#### 🔍 Main Forensic Findings
* **Strict DMARC Alignment:** Unlike Sample 1, `Authentication-Results` confirms a complete cryptographical pass: `dmarc=pass header.from=namecheap.com`. The backend authenticated sender strictly matches the visible branding.
* **Infrastructure Integrity:** The tracking paths resolve to `Return-Path: <bounces+...-rachel.jones=cosmicfusiontech.com@mailserviceemailout1.namecheap.com>`. This routes directly to Namecheap's infrastructure subdomains, without generic proxy accounts.
* **Authorized Third-Party Relay:** Tracing verified standard transactional routing metrics via **SendGrid** (`sendgrid.info`), a trusted ESP deployed by Namecheap. The sending IP `149.72.142.11` safely resolves to Namecheap's transactional records.
* **Active Policy Protection:** The domain enforces strict defensive parameter policies: `p=REJECT sp=REJECT`. Any illegitimate spoofing attempt would be immediately dropped at the boundary gate rather than recording a pass.

> **Analysis Verdict:** 🟢 **Legitimate Transactional Mail** — Fully verified, aligned, authenticated domain notification matching internal enterprise delivery baselines.

---

#### 🛡️ Defensive Action Plan (SOC Playbook)
1. **Zero Action Required:** Mark baseline profile as safe; whitelist routing remains intact.
2. **Analyst Notice:** Tone and marketing urgency do not equal high risk; structural cryptographic variables dictate the final security score.
3. **Log Baseline:** Retain data profile as a clean baseline for Namecheap's official communication blocks.

---

### 📸 Evidence & Verification

#### 🧾 Raw Header Logs & Visual Triage
* **Raw Header View:** Complete unparsed text block prior to triage extraction.
  ![Raw Header View](screenshots/header-full.png)
* **Annotated Identification:** Visual mapping of the explicit domain misalignment found in Sample #1.
  ![Highlighted Header](screenshots/header-highlighted.png)

#### 🌐 Reputation & OSINT Validation
* **MXToolbox Scan:** Verification tracking proving the delivery IP does not belong to authorized corporate blocks.
  ![MXToolbox Result](screenshots/mxtoolbox.png)
* **WHOIS Verification:** Direct mapping of infrastructure indicators back to Swiss-based assets managed by ProtonMail.
  ![WHOIS Result](screenshots/whois.png)
* **Sample #2 Layout:** Rendered visual look of the legitimate warning notification and authentic validation block.
  ![Namecheap Renewal Email](screenshots/sample2-email-view.png)
  ![Sample 2 Header Authentication Block](screenshots/sample2-header-auth.png)
  ![Sample 2 Full Header Trace](screenshots/sample2-header-trace.png)

---

## 🚩 IOC Findings

| Indicator | Type | Description | Linked Focus |
|---|---|---|---|
| `alerts@chase.com` | Spoofed Sender | Forged display name masquerading as corporate banking teams. | Sample #1 |
| `kellyellin426@proton.me` | Intercept Target | Attacker inbox configured to capture victim feedback traffic. | Sample #1 |
| `185.70.40.140` | Malicious Source IP | Relaying network IP belonging to external commercial infrastructures. | Sample #1 |
| `protonmail.com` | Envelope Domain | Domain verified by SPF, confirming absolute lack of Chase alignment. | Sample #1 |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique ID | Technique Name | Applied Context |
|---|---|---|
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/) | Phishing: Spearphishing Link | Urgency lures deployed to drive execution of a malicious URL callback. |
| [T1036.005](https://attack.mitre.org/techniques/T1036/005/) | Masquerading: Match Legitimate Name | Display strings forged to match authentic brand identities. |

---

## ✅ Conclusion
*(Update once all 5 samples are complete — tracking core attack vector patterns across analyzed data sets).*

## 💡 Key Learning Points
* **Authentication Alignment is Critical:** A protocol pass means nothing if the verified domain (`smtp.mailfrom`) fails to align with the visible interface domain (`header.from`).
* **Header Integrity Rules Over Tone:** Phishing tactics and legitimate operational warnings frequently look identical regarding urgency. Analysts must evaluate structural engineering signatures over user interface presentation.
* **Asymmetric Routing Identifies Spoofing:** Rapid analysis of `Reply-To` and `Return-Path` metrics remains the fastest indicator for identifying Friendly-From attacks without deep tooling requirements.

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../) &nbsp;|&nbsp; [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
