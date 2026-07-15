<div align="center">

# 📧 Email Header Analysis
<div align="center">

---

![Category](https://img.shields.io/badge/Category-Phishing%20Analysis-blue?style=for-the-badge&logo=gmail&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-green?style=for-the-badge)
![Emails Analyzed](https://img.shields.io/badge/Emails%20Analyzed-2%2F2-success?style=for-the-badge)

*Part of the [Phishing Analysis](../) section — [SOC Lab Portfolio](https://github.com/Anzar-Ahmed/SOC-Lab-Portfolio)*

</div>

---

### 🎯 Objective
To analyze raw email headers from suspicious/phishing samples using a text editor (Sublime Text) to identify sender spoofing, authentication mismatches (SPF/DKIM/DMARC), and Reply-To/Return-Path inconsistencies — without relying on automated header-analysis tools, in order to build strong manual analysis fundamentals.

### 🧰 Tools Deployed
* **`Sublime Text`** — Used to open raw email source codes and manually inspect routing paths and header structure.
* **`MXToolbox`** — Used to verify email authentication protocols (SPF, DKIM, DMARC) and analyze header integrity.
* **`WHOIS Lookup`** — Used to investigate domain ownership, registration details, and verify the legitimacy of the sender domain.

---

## 📊 Investigation Matrix

| #      | Subject / Threat Lure                                          | Claimed Sender           | Core Authentication                       | Final Verdict    |
| ------ | -------------------------------------------------------------- | ------------------------ | ----------------------------------------- | ---------------- |
| **01** | Your Bank Account has been blocked due to unusual activities   | `alerts@chase.com`       | SPF: Pass* / DMARC: Pass* / DKIM: Timeout | 🔴 **Malicious** |
| **02** | Rachel, cosmicfusiontech.com will expire in 7 days - renew now | `renewals@namecheap.com` | SPF: Pass / DMARC: Fail* / DKIM: Fail* | 🔴 **Malicious** |


---

## 🔬 Triage Walkthroughs

### 📁 sample-01: Chase Bank Impersonation (Friendly-From Spoofing)
<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage
* **Scenario:** A fake bank email pretending to be Chase Bank, using fake urgency ("account blocked") to scare the user into giving up credentials.
* **Methodology:** Opened the raw email source in `Sublime Text` to look at the hidden routing paths behind the user interface.

#### 🔍 Forensic Analysis (How I caught it)
* **Fake Sender Address:** The user sees `From: alerts@chase.com`, but the hidden headers show that replies are actually going to `Reply-To: kellyellin426@proton.me` and `Return-Path: kellyellin426@proton.me`.
* **Real Source Tracked:** Traced the delivery trail back to a ProtonMail server IP: `185.70.40.140`. Real Chase Bank servers had nothing to do with this email.
* **Deceptive SPF/DMARC Pass:** The security status shows `spf=pass`. However, checking deeper shows it only passed because the attacker authenticated their *own* personal ProtonMail account (`smtp.mailfrom=protonmail.com`). It never verified or matched the visible `chase.com` domain.

> **Verdict:** 🔴 **Confirmed Phishing**

#### 🛡️ Threat Mitigation (SOC Action Plan)
1. Blocked the sender IP `185.70.40.140` and the malicious email address `kellyellin426@proton.me` at the email gateway.
2. Ran an organization-wide search to find and delete this exact same email from all other user mailboxes.
</details>

---

### 📁 sample-02: Namecheap Lure (Advanced ESP Cloud Abuse)
<details>
<summary><b>Click to expand Case Investigation Details</b></summary>
<br>

#### 📋 Profile & Triage
* **Scenario:** A domain expiration warning pretending to be Namecheap, urging the user to click a "Renew Now" link.
* **Methodology:** Checked the raw header layout in Sublime Text and validated the security keys using `MXToolbox`.

#### 🔍 Forensic Analysis (How I caught it)
* **DKIM Signature Mismatch:** The email has a valid signature, but it belongs to `d=sendgrid.info` instead of matching the visible sender domain `namecheap.com`. This is a major domain misalignment flag.
* **Bulk Mailer Abuse:** The attacker registered a real account on SendGrid (a bulk emailing service) and used high-reputation servers to send phishing emails that easily bypass basic security checks.
* **DMARC Failure:** Because the background sender domain (SendGrid) does not align with the visible brand (Namecheap), the overall security logic triggers a hard DMARC failure.

> **Verdict:** 🔴 **Confirmed Phishing**

#### 🛡️ Threat Mitigation (SOC Action Plan)
1. Documented the unique SendGrid header tracking keys and reported the compromised account to SendGrid's abuse team to shut it down.
2. Checked network logs to ensure no local users clicked on the malicious "Renew Now" link.
</details>

---


---

## 🚩 Compiled Indicators of Compromise (IOCs)

| Indicator Target          | Type             | Contextual Function                                        | Association |
| ------------------------- | ---------------- | ---------------------------------------------------------- | ----------- |
| `alerts@chase.com`        | Spoofed String   | Fake display email impersonating a bank.                   | Case 01     |
| `kellyellin426@proton.me` | Exfiltration Box | Attacker's inbox catching victim replies.                  | Case 01     |
| `185.70.40.140`           | Threat Relay IP  | ProtonMail server IP used to launch the attack.            | Case 01     |
| `renewals@namecheap.com`  | Spoofed Identity | Fake identity used to target domain administrators.        | Case 02     |
| `sendgrid.info`           | Hijacked Domain  | The legitimate email relay service abused by the attacker. | Case 02     |

---

## 🗺️ MITRE ATT&CK Tracking

| Identification                                              | Structural Name                     | Application Details                                                             |
| ----------------------------------------------------------- | ----------------------------------- | ------------------------------------------------------------------------------- |
| [T1566.002](https://attack.mitre.org/techniques/T1566/002/) | Phishing: Spearphishing Link        | Using urgency context to trick a user into clicking a malicious link.           |
| [T1036.005](https://attack.mitre.org/techniques/T1036/005/) | Masquerading: Match Legitimate Name | Forging the visible sender name to look exactly like a trusted corporate brand. |

---

## 💡 Key Learning Points
* **Alignment is Everything:** A green "SPF: Pass" means nothing if the backend domain doesn't match the actual `From` address the user sees on their screen.
* **Attackers Love Legitimate Cloud Platforms:** Attackers heavily abuse services like SendGrid and ProtonMail because security filters trust their IP addresses by default. Blue teams must look at *alignment*, not just baseline trust.
* **Check Reply-To Fields First:** Looking closely at the `Reply-To` and `Return-Path` fields is one of the fastest ways to catch identity spoofing without needing heavy external tools.

---

<div align="center">

### 📂 Navigate

[⬅️ Back to Phishing Analysis Overview](../)  |  [Email Content Analysis ➡️](../02-Email-Content-Analysis)

</div>
