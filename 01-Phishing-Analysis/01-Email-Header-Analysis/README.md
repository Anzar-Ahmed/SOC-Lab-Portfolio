<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-1%2F5-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

## 🎯 Objective
To analyze raw email headers from suspicious/phishing samples using a text editor (Sublime Text) to identify sender spoofing, authentication mismatches (SPF/DKIM/DMARC), and Reply-To/Return-Path inconsistencies — without relying on automated header-analysis tools, in order to build strong manual analysis fundamentals.

## 🧰 Tools Used
* **`Sublime Text`** — Utilized for raw source ingestion, isolation of mail routing headers, and manual parsing of authentication metrics.

---

## 📊 Summary — Emails Analyzed

| # | Subject | Claimed Sender | SPF/DKIM/DMARC | Verdict | Key Red Flag |
|---|---|---|---|---|---|
| 1 | Your Bank Account has been blocked due to unusual activities | `alerts@chase.com` | SPF: Pass* / DKIM: Timeout / DMARC: Pass* | 🔴 Malicious | Reply-To & Return-Path point to an unrelated ProtonMail address, not Chase |
| 2 | *(pending)* | | | | |
| 3 | *(pending)* | | | | |
| 4 | *(pending)* | | | | |
| 5 | *(pending)* | | | | |

> ⚠️ **Critical Alignment Note:** The `SPF/DMARC: Pass` status is highly deceptive. The authentication protocol successfully validated the envelope sender (`protonmail.com`), but failed to evaluate or align with the spoofed `From` domain (`chase.com`) presented to the victim.

---

### 📥 Sample #1 — "Your Bank Account has been blocked due to unusual activities"

#### 🔹 Scenario
Email impersonating **Chase Bank**, leveraging an urgency-based social engineering lure (*"account blocked due to unusual activity"*) designed to induce panic and bypass logical scrutiny—a textbook banking credential harvesting vector.

#### 🔹 Action Taken
The raw `.eml` payload was extracted and opened in **Sublime Text**. Performed manual line-by-line inspection tracking the network infrastructure hops via the `Received` chain, verified cryptographic validation markers within `Authentication-Results`, and cross-referenced routing targets inside `Reply-To` and `Return-Path` variables.

---

### 🔍 Main Findings & Forensic Markers

* **Display vs. Envelope Mismatch:** 
  The mail client displays a legitimate-looking address: `From: alerts@chase.com`. However, the transactional delivery paths are explicitly routed to an attacker-controlled target: `Reply-To: kellyellin426@proton.me` and `Return-Path: kellyellin426@proton.me`.
  
* **Infrastructure Trace Routing:** 
  Tracing the sequential `Received` boundaries verified that the final outbound node was `mail-40140.protonmail.ch (185.70.40.140)`. The message originated straight from ProtonMail's commercial servers, with zero interaction from actual Chase/JPMorgan routing facilities.

* **Deceptive Protocol Spoofing:** 
  While `Authentication-Results` flags an initial `spf=pass` and `dmarc=pass`, closer forensic inspection indicates the scope is explicitly bound to `smtp.mailfrom=protonmail.com` and `header.d=protonmail.com`. The validation passed strictly because the attacker used a legally registered ProtonMail asset. No cryptographic verification was performed for `chase.com`.

* **DKIM Verification Timeout:** 
  The signature validation failed with a `timeout (key query timeout)` status, indicating a complete breakdown of public-key lookup for the sender validation sequence.

> **Analysis Verdict:** 🔴 **Confirmed Phishing** — High-confidence identification of Friendly-From spoofing mimicking Chase Bank infrastructure via legitimate ProtonMail nodes, structured with asymmetric reply targets to intercept inbound victim traffic.

---

#### 🛡️ Analyst Recommendations (SOC Playbook Triage)
1. **Perimeter Mitigation:** Operationalize immediate blocks on the relay IP (`185.70.40.140`) and append the malicious external address (`kellyellin426@proton.me`) to the Secure Email Gateway (SEG) global blocklist.
2. **Impact Assessment:** Execute a tenant-wide sweep and purge script across the mail environment to target identical string patterns or header metadata across other enterprise mailboxes.
3. **Network Telemetry Audit:** Cross-reference internal proxy and DNS logs to verify if any endpoint reached out to external URLs if a hyperlink payload was integrated within the mail body.

---

### 📸 Evidence & Verification

To validate the manual analysis findings, the following structural logs and external OSINT source verifications were compiled as part of the investigation:

#### 🧾 Raw Header Analysis (Sublime Text)
*The unparsed header block ingested into the text workspace prior to component isolation:*

![Raw Header View](screenshots/header-full.png)

---

#### 🔍 Highlighted Key Findings & Discrepancies
*Annotated breakdown showcasing the absolute divergence between the spoofed display domain and the structural mail parameters:*

![Highlighted Header](screenshots/header-highlighted.png)

---

#### 🌐 External OSINT Verification

**MXToolbox (Header Analysis & IP Reputation Check)**
*Header logs validated via MXToolbox to evaluate delivery hops and verify infrastructure reputation. The results definitively prove the sending node is dissociated from actual Chase network ranges:*

![MXToolbox Result](screenshots/mxtoolbox.png)

**WHOIS Lookup (Domain Security Profile)**
*A targeted WHOIS request mapped against `185.70.40.140` to formalize ASN assignment, mapping ownership records directly to Swiss assets managed by ProtonMail:*

![WHOIS Result](screenshots/whois.png)

---

## 🚩 IOC Findings

| Indicator | Type | Description |
|---|---|---|
| `alerts@chase.com` | Spoofed Display Sender | Forged "From" address impersonating Chase Bank |
| `kellyellin426@proton.me` | Reply-To / Return-Path | Actual attacker-controlled mailbox behind the spoofed display name |
| `185.70.40.140` | Sending IP | ProtonMail infrastructure IP (`mail-40140.protonmail.ch`) used to relay the phishing email |
| `protonmail.com` | Authenticated Envelope Domain | Domain actually validated by SPF/DMARC — mismatched against the claimed `chase.com` sender |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique ID | Technique Name | Observed Activity |
|---|---|---|
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/) | Phishing: Spearphishing Link | Bank-alert lure designed to drive victim to a malicious link/action |
| [T1036.005](https://attack.mitre.org/techniques/T1036/005/) | Masquerading: Match Legitimate Name or Location | "From" display forged to impersonate Chase Bank |

---

## ✅ Conclusion
*(Update once all 5 samples are complete — summarize the common patterns observed across samples: e.g. how many relied on Friendly-From spoofing vs. compromised legitimate accounts, most common authentication failure type, etc.)*

## 💡 Key Learning
* **"SPF: Pass" does not guarantee sender legitimacy** — It only validates the envelope sender domain (`smtp.mailfrom`), which can easily be different from the spoofed `From` address a user actually sees in their mail client. Always verify domain alignment.
* **Reply-To and Return-Path verification** are highly reliable indicators for spotting Friendly-From spoofing quickly without over-reliance on external parsing automation.
* Analyzing headers manually builds technical muscle memory for identifying edge-case header anomalies that automated security controls or parsers might occasionally gloss over.

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../) &nbsp;|&nbsp; [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
