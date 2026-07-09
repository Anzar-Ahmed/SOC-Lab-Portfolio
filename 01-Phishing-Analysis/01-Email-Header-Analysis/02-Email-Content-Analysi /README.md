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
| **01** | All unverified accounts will be suspended                      | Trust Wallet Support     | Not Verified / Suspicious Domain          | 🔴 **Malicious** |
| **02** | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com` | SPF: Pass / DKIM: Pass / DMARC: Pass      | 🟢 **Legitimate**|

---

## 🔬 Triage Walkthroughs

### 📁 sample-01: Trust Wallet Phishing (Social Engineering + Domain Spoofing)

<details>
<summary><b>Click to expand Phishing Indicators Breakdown</b></summary>
<br>

### 🔍 Phishing Indicators Breakdown

#### 🟥 1. Domain Verification Issue
* **Claimed Sender:** Trust Wallet Support
* **The Catch:** Email claims to be from Trust Wallet but actually originates from `emails.gorgias.com`.
* **Verification Needed:** Domain existence check (WHOIS) is required to confirm infrastructure legitimacy.
* **Verdict:** ❌ Mismatch between the claimed identity and the actual routing domain indicates spoofing.

#### 🟡 2. Branding & Word Spacing Discrepancy
* **Official Branding:** `"Trust Wallet"`
* **Email Uses:** `"Trustwallet"`
* **Verdict:** ❌ Incorrect word spacing indicates a lack of authenticity and is a common sign of phishing templates.

#### ⚠️ 3. Improper Greeting & Punctuation
* **Greeting Used:** `"Hi Dear; Customer"`
* **Verdict:** ❌ Contains awkward punctuation and formatting, which is not aligned with professional corporate communication standards.

#### 🚨 4. Urgency / Fear Tactic
* **The Threat Lure:** `"All unverified accounts will be suspended"`
* **Psychological Trigger:** Creates panic and pressure to force the user to act immediately.
* **Verdict:** ❌ Classic social engineering technique used in phishing attacks to bypass logical thinking.

#### 🎯 5. Suspicious Call-to-Action
* **Target Element:** `"Confirm my wallet"` button.
* **Verdict:** ❌ Likely redirects to a malicious or fake login page, posing a high risk of credential or wallet compromise.

---

### 🧠 Final Analysis
This email demonstrates multiple phishing indicators including domain spoofing, branding inconsistency (`Trust Wallet` vs `Trustwallet`), improper formatting, and the use of fear tactics to manipulate behavior.

> ➡️ **Conclusion:** 🔴 **Confirmed Phishing** (Crypto-targeted social engineering attack designed to trick users into revealing sensitive data).

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
| `kellyellin426@proton.me` | Attacker Inbox   | Used for capturing victim responses.                       | Case 01     |

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
