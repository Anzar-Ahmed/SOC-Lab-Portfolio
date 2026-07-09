<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge\&logo=gmail\&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-2%2F2-success?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective

To analyze raw email headers from suspicious/phishing samples using a text editor (Sublime Text) to identify sender spoofing, authentication mismatches (SPF/DKIM/DMARC), and Reply-To/Return-Path inconsistencies — without relying on automated header-analysis tools, in order to build strong manual analysis fundamentals.

---

### 🧰 Tools Deployed

* **`Sublime Text`** — Used to open raw email source codes and manually check routing paths.
* **`MXToolbox`** — Used to verify if email security protocols (SPF/DKIM/DMARC) are aligned with the visible sender.

---

## 📊 Investigation Matrix

| #      | Subject / Threat Lure                                          | Claimed Sender           | Core Authentication                       | Final Verdict    |
| ------ | -------------------------------------------------------------- | ------------------------ | ----------------------------------------- | ---------------- |
| **01** | Your Bank Account has been blocked due to unusual activities   | `alerts@chase.com`       | SPF: Pass* / DMARC: Pass* / DKIM: Timeout | 🔴 **Malicious** |
| **02** | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com` | SPF: Pass / DMARC: Fail* / DKIM: Fail*    | 🔴 **Malicious** |

> ⚠️ **Important Security Note:** In both cases, basic gateway filters failed to block these emails because the attackers used trusted platforms (ProtonMail & SendGrid). The phishing attempts were only caught by performing a manual **Domain Alignment Check**.

---

## 🔬 Triage Walkthroughs

### 📁 Case 01: Chase Bank Impersonation (Friendly-From Spoofing)

<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage

* **Scenario:** A fake bank email pretending to be Chase Bank, using fake urgency ("account blocked") to scare the user into giving up credentials.
* **Methodology:** Opened the raw email source in `Sublime Text` to look at the hidden routing paths behind the user interface.

#### 🔍 Forensic Analysis (How I caught it)

* **Fake Sender Address:** The user sees `From: alerts@chase.com`, but the hidden headers show that replies are actually going to `Reply-To: kellyellin426@proton.me` and `Return-Path: kellyellin426@proton.me`.
* **Real Source Tracked:** Traced the delivery trail back to a ProtonMail server IP: `185.70.40.140`. Real Chase Bank servers had nothing to do with this email.
* **Deceptive SPF/DMARC Pass:** The security status shows `spf=pass`. However, it passed only for ProtonMail (`smtp.mailfrom=protonmail.com`), not for `chase.com`, indicating domain misalignment.

> **Verdict:** 🔴 **Confirmed Phishing**

#### 🛡️ Threat Mitigation (SOC Action Plan)

1. Blocked the sender IP `185.70.40.140` and the malicious email address `kellyellin426@proton.me`.
2. Removed the email from all user mailboxes.

</details>

---

### 📁 Case 02: Namecheap Lure (Advanced ESP Cloud Abuse)

<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage

* **Scenario:** A domain expiration warning pretending to be Namecheap, urging the user to click a "Renew Now" link.
* **Methodology:** Analyzed raw headers and validated SPF/DKIM/DMARC using `MXToolbox`.

#### 🔍 Forensic Analysis (How I caught it)

* **DKIM Signature Mismatch:** DKIM signed by `sendgrid.info`, not `namecheap.com`.
* **Bulk Mailer Abuse:** Attacker used SendGrid (legitimate service) to bypass filters.
* **DMARC Failure:** Domain misalignment caused authentication failure.

> **Verdict:** 🔴 **Confirmed Phishing**

#### 🛡️ Threat Mitigation (SOC Action Plan)

1. Reported the abuse to SendGrid.
2. Verified that no users interacted with the phishing link.

</details>

---

## 📸 Artifacts & Validation Logs

#### 🧾 Structural Logging (Case 01)

* **Raw Header View:**
  ![Raw Header](./sample-01/header-full.png)

* **Highlighted Analysis:**
  ![Highlighted Header](./sample-01/header-highlighted.png)

---

#### 🌐 Reputation & Diagnostic Checks

* **WHOIS Lookup:**
  ![WHOIS](./sample-01/Whois.png)

* **MXToolbox Analysis:**
  ![MXToolbox](./sample-01/mxtoolbox.png)

* **Alignment Failure (Case 02):**
  ![Sample 2](./sample-01/sample2-header-auth.png)

---

## 🚩 Indicators of Compromise (IOCs)

| Indicator                                                 | Type           | Description        |
| --------------------------------------------------------- | -------------- | ------------------ |
| [alerts@chase.com](mailto:alerts@chase.com)               | Spoofed Email  | Fake bank sender   |
| [kellyellin426@proton.me](mailto:kellyellin426@proton.me) | Attacker Email | Reply-To address   |
| 185.70.40.140                                             | IP Address     | ProtonMail source  |
| [renewals@namecheap.com](mailto:renewals@namecheap.com)   | Spoofed Email  | Fake renewal alert |
| sendgrid.info                                             | Service Abuse  | DKIM domain        |

---

## 🗺️ MITRE ATT&CK Mapping

| Technique | Description        |
| --------- | ------------------ |
| T1566.002 | Spearphishing Link |
| T1036.005 | Masquerading       |

---

## 💡 Key Learning Points

* SPF pass does **not guarantee legitimacy**
* Domain alignment is critical
* Reply-To mismatch is a strong indicator
* Trusted services can be abused for phishing

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis](../) | [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
