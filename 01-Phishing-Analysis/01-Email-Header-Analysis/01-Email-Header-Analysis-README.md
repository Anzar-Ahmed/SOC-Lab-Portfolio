<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-1%2F5-orange?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

# 📧 Email Header Analysis

> Part of the Phishing Analysis — SOC Lab Portfolio

---

## 🎯 Objective

To manually analyze raw email headers from phishing samples using a text editor (Sublime Text) in order to identify:

- Sender spoofing  
- SPF / DKIM / DMARC mismatches  
- Reply-To / Return-Path inconsistencies  

This approach avoids automated tools and strengthens real SOC investigation skills.

---

## 🧰 Tools Used

| Tool | Purpose |
|------|--------|
| Sublime Text | Manual parsing of raw email headers (Received chain, Authentication-Results, Reply-To, Return-Path) |

---

## 📊 Summary — Emails Analyzed

| # | Subject | Claimed Sender | SPF/DKIM/DMARC | Verdict | Key Red Flag |
|---|--------|----------------|----------------|--------|--------------|
| 1 | Your Bank Account has been blocked due to unusual activities | alerts@chase.com | SPF: Pass* / DKIM: Timeout / DMARC: Pass* | 🔴 Malicious | Reply-To & Return-Path mismatch |
| 2 | (pending) | | | | |
| 3 | (pending) | | | | |
| 4 | (pending) | | | | |
| 5 | (pending) | | | | |

> *SPF/DMARC "Pass" validated protonmail.com — not chase.com*

---

# 🧪 Sample #1 — "Your Bank Account has been blocked due to unusual activities"

### 📌 Scenario
Email impersonates **Chase Bank**, using urgency-based social engineering ("account blocked due to unusual activity") to pressure the victim into taking action.

---

### ⚙️ Action Taken

- Opened raw email header in **Sublime Text**
- Manually analyzed:
  - Received chain  
  - Authentication-Results  
  - Reply-To  
  - Return-Path  

---

### 📸 Screenshots

![Header Analysis Part 1](screenshots/01-header-sample1-part1.png)

![Header Analysis Part 2](screenshots/01-header-sample1-part2.png)

---

### 🔍 Main Findings

- Displayed sender: **alerts@chase.com**
- Actual sender: **kellyellin426@proton.me**
- Reply-To & Return-Path both point to attacker-controlled mailbox
- Sending infrastructure traced to **ProtonMail**, not Chase
- SPF & DMARC passed for **protonmail.com**, not **chase.com**
- DKIM verification failed (timeout)

➡️ **Technique Identified:** Friendly-From Spoofing

---

### 🧰 Tools Used to Confirm

| Tool | What It Confirmed |
|------|------------------|
| Sublime Text | Revealed full Received chain (ProtonMail infrastructure) |
| Sublime Text | Showed SPF/DMARC mismatch (protonmail.com vs chase.com) |
| Sublime Text | Confirmed Reply-To & Return-Path inconsistency |

---

### 🚨 Result / Verdict

🔴 **Confirmed Phishing**

- Friendly-From spoofing of Chase Bank  
- Sent via ProtonMail infrastructure  
- Reply-To & Return-Path redirect to attacker-controlled email  

---

### 🧠 Analyst Note

In a real SOC environment, this would trigger:

- Blocking sender IP/domain at mail gateway  
- Searching for similar emails across users  
- Investigating users who interacted with the email  

---

## 🚩 IOC Findings

| Indicator | Type | Description |
|----------|------|------------|
| alerts@chase.com | Spoofed Sender | Fake display identity |
| kellyellin426@proton.me | Reply-To / Return-Path | Attacker-controlled mailbox |
| 185.70.40.140 | Sending IP | ProtonMail infrastructure |
| protonmail.com | Domain | Authenticated domain mismatch |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique ID | Technique | Description |
|-------------|----------|------------|
| T1566.002 | Phishing: Spearphishing Link | Bank alert lure |
| T1036.005 | Masquerading | Fake sender identity |

---

## 💡 Key Learning

- "SPF: Pass" does NOT mean the visible sender is legitimate  
- Always check **which domain passed authentication**  
- Reply-To & Return-Path mismatches are strong phishing indicators  
- Manual header analysis builds deeper investigation skills than automated tools  

---

## 📂 Navigate

⬅️ Back to Phishing Analysis Overview  
➡️ Email Content Analysis

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../) &nbsp;|&nbsp; [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
