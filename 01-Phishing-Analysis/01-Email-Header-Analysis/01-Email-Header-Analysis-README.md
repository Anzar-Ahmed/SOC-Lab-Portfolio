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
* **Sublime Text:** For viewing, manual parsing, and tracing raw `.eml` header sources (Received chain, Authentication-Results, Reply-To, and Return-Path fields).

---

## 📊 Summary — Emails Analyzed

| # | Subject | Claimed Sender | SPF/DKIM/DMARC | Verdict | Key Red Flag |
|---|---|---|---|---|---|
| 1 | Your Bank Account has been blocked due to unusual activities | alerts@chase.com | SPF: Pass* / DKIM: Timeout / DMARC: Pass* | 🔴 Malicious | Reply-To & Return-Path point to an unrelated ProtonMail address, not Chase |
| 2 | *(pending)* | | | | |
| 3 | *(pending)* | | | | |
| 4 | *(pending)* | | | | |
| 5 | *(pending)* | | | | |

> \* **Note on SPF/DMARC:** The "Pass" status is deceptive. It authenticates `protonmail.com` (the envelope sender), not the claimed `chase.com` domain visible to the user.

---

### Sample #1 — "Your Bank Account has been blocked due to unusual activities"

**Scenario:** Email impersonating Chase Bank, using urgency-based social engineering ("account blocked due to unusual activity") to pressure the recipient into taking action — a classic bank-alert phishing lure.

**Action Taken:**
Opened the raw email header source in Sublime Text and manually analyzed the `Received` chain, `Authentication-Results`, `Reply-To`, and `Return-Path` fields to verify if the visible sender identity aligned with the actual sending infrastructure.

**Screenshots:**
```markdown
![Raw header part 1 - Received chain and Authentication-Results](screenshots/01-header-sample1-part1.png)
![Raw header part 2 - From, Reply-To, Return-Path](screenshots/01-header-sample1-part2.png)
