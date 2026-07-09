<div align="center">

# 📧 Email Header Analysis

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-In%20Progress-orange?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-2%2F5-yellow?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To analyze suspicious/phishing email samples using manual inspection techniques, focusing on identifying spoofed domains, branding inconsistencies, social engineering tactics, and authentication misalignment (SPF/DKIM/DMARC), without relying entirely on automated tools.

### 🧰 Tools Deployed
* **`Sublime Text`** — Used to open raw email source codes and manually inspect routing paths and header structure.
* **`MXToolbox`** — Used to verify email authentication protocols (SPF, DKIM, DMARC) and analyze header integrity.
* **`WHOIS Lookup`** — Used to investigate domain ownership and validate sender legitimacy.

---

## 📊 Investigation Matrix

| #      | Subject / Threat Lure                                          | Claimed Sender           | Core Authentication                       | Final Verdict    |
| ------ | -------------------------------------------------------------- | ------------------------ | ----------------------------------------- | ---------------- |
| **01** | FWD: All unverified accounts will be suspended on 10/30/2022...| Trustwallet-Support      | Not Verified / Suspicious Domain          | 🔴 **Malicious** |
| **02** | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com` | SPF: Pass / DKIM: Pass / DMARC: Pass      | 🟢 **Legitimate**|

---

## 🔬 Triage Walkthroughs

### 📁 sample-01: Trust Wallet Phishing (Social Engineering + Domain Spoofing)

<details>
<summary><b>Click to expand Phishing Indicators Breakdown</b></summary>
<br>

### 🔍 Phishing Indicators Breakdown

#### 🟥 1. Domain Verification Issue
* **Claimed Sender:** Trustwallet-Support
* **The Catch:** Email claims to be from Trust Wallet but originates from the suspicious routing string: `7wq1vg3kn9woejk4@emails.gorgias.com`.
* **Verification Logic:** Real crypto assets do not use third-party ticket management or multi-tenant subdomains (`gorgias.com`) for foundational account alerts. A WHOIS investigation reveals the infrastructure belongs to a consumer customer-service platform, not the wallet infrastructure provider.
* **Verdict:** ❌ Complete mismatch between the outer brand identity and the actual back-end transmission domain indicates structural spoofing.

#### 🟡 2. Branding & Word Spacing Discrepancy
* **Official Corporate Name:** `"Trust Wallet"` (Separated properly with explicit tracking).
* **Email Artifact Strings:** `"Trustwallet-Support"` and `"Trustwallet Team"`.
* **Verdict:** ❌ Typo-squatting and incorrect word spacing. This minor variance is a strong indicator of an unauthorized external phishing layout designed to fool casual inspection.

#### ⚠️ 3. Improper Greeting & Punctuation
* **Greeting Block Observed:** `"Hi Dear; Customer"`
* **Verdict:** ❌ Highly irregular formatting. The deployment of a semi-colon inside a standard greeting indicates missing syntax review and absolute non-alignment with official, automated financial notification platforms.

#### 🚨 4. Urgency / Fear Tactic
* **The Threat Lure (Subject & Body):** `"FWD: All unverified accounts will be suspended on 10/30/2022..."` and `"All unverified accounts will be suspended on 10/31/2022."`
* **Psychological Trigger:** Imposes artificial constraint windows and dates to invoke immediate fear of asset loss.
* **Verdict:** ❌ Textbook social engineering trigger engineered to enforce rapid response vectors before logical tracking can occur.

#### 🎯 5. Suspicious Call-to-Action (CTA)
* **Target Element:** `"Confirm my wallet"` interactive landing anchor button.
* **Verdict:** ❌ High-risk element. This pointer leads to an unverified external credential harvesting script or seed-phrase compromise module.

---

### 🧠 Final Analysis
This artifact presents clear high-fidelity tactical alerts: multi-tenant domain mapping mismatches (`emails.gorgias.com`), corporate identity formatting defects, syntax errors, and standard fear-inducing constraints tracking malicious URLs.

> ➡️ **Conclusion:** 🔴 **Confirmed Phishing** (Crypto-targeted social engineering attack targeting sensitive wallet access indicators).

</details>

---

### 📁 sample-02: Namecheap Notification (Legitimate Transactional Email)

<details>
<summary><b>Click to expand Validation Details</b></summary>
<br>

### 🔍 Key Findings

#### 🟢 1. Proper Domain Alignment
* **Analysis:** The sender domain `namecheap.com` perfectly matches the visible identity presented to the user.

#### 🟢 2. Valid Authentication
* **Analysis:** Critical security checks (**SPF, DKIM, and DMARC**) all pass cleanly with correct alignment flags.

#### 🟢 3. Trusted Infrastructure
* **Analysis:** The email is properly routed via authorized and designated **SendGrid** bulk service delivery nodes mapped in Namecheap's DNS records.

#### 🟢 4. Consistent Branding
* **Analysis:** Layout, typography, links, and formatting are professional and free from anomalies or syntax errors.

---

### 🧠 Final Analysis
Unlike malicious campaigns abusing shared infrastructure, this sample strictly adheres to security policy parameters and shows genuine domain alignment throughout the raw header code.

> ➡️ **Conclusion:** 🟢 **Legitimate Email**

</details>

---

## 🚩 Compiled Indicators of Compromise (IOCs)

| Indicator Target          | Type             | Contextual Function                                        | Association |
| ------------------------- | ---------------- | ---------------------------------------------------------- | ----------- |
| `emails.gorgias.com`      | Suspicious Domain| Third-party domain used for spoofing.                      | Case 01     |
| `Confirm my wallet`       | Phishing CTA     | Used to redirect user to a malicious page.                 | Case 01     |
| `7wq1vg3kn9woejk4`        | Attacker Box/Hash| Specific threat actor source identifier string.            | Case 01     |

---

## 🗺️ MITRE ATT&CK Tracking

| Identification                                              | Structural Name                     | Application Details                                                             |
| ----------------------------------------------------------- | ----------------------------------- | ------------------------------------------------------------------------------- |
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/) | Phishing: Spearphishing Link        | User is tricked into clicking a malicious verification link.                     |
| [T1036.005](https://attack.mitre.org/techniques/T1036/005/) | Masquerading: Match Legitimate Name | Fake branding used to impersonate Trust Wallet.                                 |

---

## 💡 Key Learning Points
* **Domain Verification is Critical:** Always verify whether the sender domain matches the claimed organization.
* **Branding Mistakes Reveal Attacks:** Small inconsistencies like spacing (`"Trustwallet"`) are strong phishing indicators.
* **Urgency is a Red Flag:** Attackers use fear tactics to force quick decisions.
* **CTA Links are High Risk:** Always analyze links before clicking, especially in financial/crypto emails.

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../)  |  [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
