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
| Tool | Purpose |
|---|---|
| Sublime Text | Viewing and manually parsing raw `.eml` header source — tracing Received chain, Authentication-Results, Reply-To, and Return-Path fields |

---

## 📊 Summary — Emails Analyzed

| # | Subject | Claimed Sender | SPF/DKIM/DMARC | Verdict | Key Red Flag |
|---|---|---|---|---|---|
| 1 | Your Bank Account has been blocked due to unusual activities | alerts@chase.com | SPF: Pass* / DKIM: Timeout / DMARC: Pass* | 🔴 Malicious | Reply-To & Return-Path point to an unrelated ProtonMail address, not Chase |
| 2 | *(pending)* | | | | |
| 3 | *(pending)* | | | | |
| 4 | *(pending)* | | | | |
| 5 | *(pending)* | | | | |

> \*SPF/DMARC "Pass" is misleading here — see finding below. It authenticates `protonmail.com`, not the claimed `chase.com` sender.

---

### Sample #1 — "Your Bank Account has been blocked due to unusual activities"

**Scenario:** Email impersonating Chase Bank, using urgency-based social engineering ("account blocked due to unusual activity") to pressure the recipient into taking action — a classic bank-alert phishing lure.

**Action Taken:**
Opened the raw email header source in Sublime Text and manually traced the `Received` chain, `Authentication-Results`, `Reply-To`, and `Return-Path` fields to determine whether the visible sender identity matched the actual sending infrastructure.

**Screenshot:**
`![Raw header part 1 - Received chain and Authentication-Results](screenshots/01-header-sample1-part1.png)`
`![Raw header part 2 - From, Reply-To, Return-Path](screenshots/01-header-sample1-part2.png)`

**Main Finding (Kaise pata chala ye phishing hai):**
- The visible **`From: alerts@chase.com`** made the email appear to come from Chase Bank.
- However, **`Reply-To: kellyellin426@proton.me`** and **`Return-Path: kellyellin426@proton.me`** both pointed to an unrelated free ProtonMail address — a bank would never route replies through a personal Proton Mail account.
- The `Received` chain traced back to **`mail-40140.protonmail.ch (185.70.40.140)`** — ProtonMail's own mail infrastructure, not any Chase/JPMorgan-owned server.
- The `Authentication-Results` showed **`spf=pass`** and **`dmarc=pass`**, which looks reassuring at first glance — but closer inspection showed `smtp.mailfrom=protonmail.com` and `header.d=protonmail.com`. This means SPF/DMARC only validated that ProtonMail's infrastructure is authorized to send *as ProtonMail* — it never validated `chase.com` at all, since the envelope sender domain (used for SPF/DMARC checks) was `protonmail.com`, not `chase.com`.
- **DKIM returned `timeout (key query timeout)`** — no valid signature was verified either.

This is a **Friendly-From Spoofing** technique: the attacker forges the *visible* `From` display to impersonate a trusted brand, while sending through a legitimately-authenticated but unrelated mail service (ProtonMail). A quick glance at "SPF: Pass" without checking *which domain* was actually validated would have led to a false conclusion of legitimacy.

**Tools Used to Confirm:**
| Tool | What It Confirmed |
|---|---|
| Sublime Text | Revealed the full Received chain, exposing ProtonMail as the true sending infrastructure instead of Chase |
| Sublime Text (Authentication-Results parsing) | Showed SPF/DMARC pass was scoped to `protonmail.com`, not `chase.com` — exposing the authentication mismatch |
| Sublime Text (Reply-To / Return-Path comparison) | Confirmed both fields pointed to the same unrelated `proton.me` address, unrelated to the claimed Chase sender |

**Result / Verdict:** 🔴 **Confirmed Phishing** — Friendly-From spoofing of Chase Bank, sent via ProtonMail infrastructure, with Reply-To/Return-Path both redirecting to an attacker-controlled personal email address.

**Analyst Note:** In a real SOC environment, this would justify blocking the sender domain/IP at the mail gateway, searching the mailbox environment for other recipients of emails from `185.70.40.140` or with the same Reply-To address, and flagging any employee who may have replied or clicked a link in this message for further monitoring.

---

### Sample #2 — *(pending)*

*(Use the same structure: Scenario → Action Taken → Screenshot → Main Finding → Tools Used to Confirm → Result/Verdict)*

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
| T1566.002 | Phishing: Spearphishing Link | Bank-alert lure designed to drive victim to a malicious link/action |
| T1036.005 | Masquerading: Match Legitimate Name or Location | "From" display forged to impersonate Chase Bank |

---

## ✅ Conclusion
*(Update once all 5 samples are complete — summarize the common patterns observed across samples: e.g. how many relied on Friendly-From spoofing vs. compromised legitimate accounts, most common authentication failure type, etc.)*

## 💡 Key Learning
- **"SPF: Pass" does not mean the visible sender is legitimate** — it only validates the envelope sender domain (`smtp.mailfrom`), which can be completely different from the `From` display address a user sees. Always check *which* domain passed, not just that something passed.
- **Reply-To and Return-Path mismatches against the claimed brand** are one of the fastest, most reliable indicators of Friendly-From spoofing — faster to check than running the header through an external tool.
- Manually reading raw headers in a text editor builds a stronger mental model of header structure than relying solely on automated analyzers — useful for spotting anomalies those tools might not flag by default.

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../) &nbsp;|&nbsp; [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
